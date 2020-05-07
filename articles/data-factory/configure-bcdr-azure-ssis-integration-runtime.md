---
title: Azure-SSIS Integration Runtime voor SQL Database failover configureren
description: In dit artikel wordt beschreven hoe u de Azure-SSIS Integration Runtime configureert met Azure SQL Database geo-replicatie en failover voor de SSISDB-data base
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.devlang: powershell
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/09/2020
ms.openlocfilehash: b4b679b15092531ff9553d221f23d7f030fc1def
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82592086"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>De Azure-SSIS Integration Runtime configureren met Azure SQL Database geo-replicatie en failover

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt beschreven hoe u de Azure-SSIS Integration Runtime configureert met Azure SQL Database geo-replicatie voor de SSISDB-data base. Wanneer een failover optreedt, kunt u ervoor zorgen dat de Azure-SSIS IR met de secundaire data base werkt.

Zie [overzicht: actieve geo-replicatie en groepen voor automatische failover](../sql-database/sql-database-geo-replication-overview.md)voor meer informatie over geo-replicatie en failover voor SQL database.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-ssis-ir-failover-with-azure-sql-database-managed-instance"></a>Azure-SSIS IR failover met Azure SQL Database beheerd exemplaar

### <a name="prerequisites"></a>Vereisten

Azure SQL Database beheerde instantie gebruikt **database hoofd sleutel (DMK)** om gegevens, referenties en verbindings gegevens die zijn opgeslagen in de data base te beveiligen. Als u de automatische ontsleuteling van DMK wilt inschakelen, wordt een kopie van de sleutel versleuteld met behulp van de **Server hoofd sleutel (SMK)**. SMK wordt echter niet gerepliceerd in een failovergroep. u moet dus een extra wacht woord voor de primaire en secundaire instanties voor DMK-ontsleuteling toevoegen na een failover.

1. Voer de volgende opdracht uit op de SSISDB op het primaire exemplaar. Met deze stap voegt u een nieuw versleutelings wachtwoord toe.

    ```sql
    ALTER MASTER KEY ADD ENCRYPTION BY PASSWORD = 'password'
    ```

2. Maak een failovergroep op Azure SQL Database beheerde instantie.

3. Voer **sp_control_dbmasterkey_password** uit op het secundaire exemplaar met behulp van het nieuwe versleutelings wachtwoord.

    ```sql
    EXEC sp_control_dbmasterkey_password @db_name = N'SSISDB',   
        @password = N'<password>', @action = N'add';  
    GO
    ```

### <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scenario 1-Azure-SSIS IR verwijst naar het listener-eind punt voor lezen/schrijven

Als u het listener-eind punt voor lezen-schrijven wilt Azure-SSIS IR, moet u eerst het eind punt van de primaire server aanwijzen. Nadat u SSISDB naar een failovergroep hebt geplaatst, kunt u overschakelen naar het listener-eind punt voor lezen en schrijven en opnieuw starten Azure-SSIS IR.

#### <a name="solution"></a>Oplossing

Als er een failover optreedt, moet u het volgende doen:

1. Stop Azure-SSIS IR op de primaire regio.

2. Bewerk Azure-SSIS IR met een nieuwe regio, VNET en aangepaste SAS URI-gegevens van het secundaire exemplaar. Als Azure-SSIS IR wordt verwezen naar de listener voor lezen/schrijven en het eind punt transparant is voor Azure-SSIS IR, hoeft u het eind punt niet te bewerken.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Start Azure-SSIS IR opnieuw.

### <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scenario 2-Azure-SSIS IR wijst het eind punt van de primaire server aan

Het scenario is geschikt als Azure-SSIS IR verwijst naar het eind punt van de primaire server.

#### <a name="solution"></a>Oplossing

Als er een failover optreedt, moet u het volgende doen:

1. Stop Azure-SSIS IR op de primaire regio.

2. Azure-SSIS IR bewerken met nieuwe regio's, informatie over het eind punt en VNET van het secundaire exemplaar.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -VNetId "new VNet" `
                -Subnet "new subnet" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Start Azure-SSIS IR opnieuw.

### <a name="scenario-3---azure-ssis-ir-is-pointing-to-public-endpoint-of-azure-sql-database-managed-instance"></a>Scenario 3-Azure-SSIS IR verwijst naar het open bare eind punt van Azure SQL Database beheerde instantie

Het scenario is geschikt als de Azure-SSIS IR verwijst naar het open bare eind punt van Azure SQL Database beheerde instantie en er geen verbinding wordt gemaakt met VNET. Het enige verschil tussen scenario 2 en deze scenario's is dat u de VNET-gegevens van Azure-SSIS IR na een failover niet hoeft te bewerken.

#### <a name="solution"></a>Oplossing

Als er een failover optreedt, moet u het volgende doen:

1. Stop Azure-SSIS IR op de primaire regio.

2. Bewerk Azure-SSIS IR met nieuwe regio-en eindpunt gegevens van een secundair exemplaar.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Start Azure-SSIS IR opnieuw.

### <a name="scenario-4---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scenario 4: een bestaande SSISDB (SSIS-catalogus) koppelen aan een nieuwe Azure-SSIS IR

Dit scenario is geschikt als u een nieuwe Azure-SSIS IR wilt inrichten voor de secundaire regio of als u wilt dat uw SSISDB werkt met een nieuwe Azure-SSIS IR in een nieuwe regio wanneer een ADF of Azure-SSIS IR ramp in de huidige regio optreedt.

#### <a name="solution"></a>Oplossing

Als er een failover optreedt, moet u het volgende doen:

> [!NOTE]
> Stap 4 (maken van IR) moet worden uitgevoerd via Power shell. Azure Portal rapporteert een fout waarin wordt vermeld dat SSISDB al bestaat.

1. Stop Azure-SSIS IR op de primaire regio.

2. Voer een opgeslagen procedure voor het bijwerken van meta gegevens in SSISDB uit om verbindingen van ** \<new_data_factory_name\> ** en ** \<new_integration_runtime_name\>** te accepteren.
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Maak een nieuwe Data Factory met de naam ** \<new_data_factory_name\> ** in de nieuwe regio. Zie een data factory maken voor meer informatie.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                      -Location "new region"`
                      -Name "<new_data_factory_name>"
    ```
    
    Zie [een Azure-Data Factory maken met Power shell](quickstart-create-data-factory-powershell.md) voor meer informatie over deze Power shell-opdracht.

4. Maak met behulp van Azure PowerShell een nieuwe Azure-SSIS IR met de naam ** \<new_integration_runtime_name\> ** in de nieuwe regio.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    Zie [de Azure-SSIS Integration runtime maken in azure Data Factory](create-azure-ssis-integration-runtime.md) voor meer informatie over deze Power shell-opdracht.



## <a name="azure-ssis-ir-failover-with-azure-sql-database"></a>Azure-SSIS IR failover met Azure SQL Database

### <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scenario 1-Azure-SSIS IR verwijst naar het listener-eind punt voor lezen/schrijven

Dit scenario is geschikt Azure-SSIS IR verwijst naar het Lees-/schrijftoegangs eindpunt van de failovergroep en de SQL Database-Server is *niet* geconfigureerd met de eindpunt regel van de virtuele netwerk service. Als u het listener-eind punt voor lezen-schrijven wilt Azure-SSIS IR, moet u eerst het eind punt van de primaire server aanwijzen. Nadat u SSISDB naar een failovergroep hebt geplaatst, kunt u overschakelen naar het listener-eind punt voor lezen en schrijven en opnieuw starten Azure-SSIS IR.

#### <a name="solution"></a>Oplossing

Wanneer een failover optreedt, is het transparant voor de Azure-SSIS IR. De Azure-SSIS IR maakt automatisch verbinding met de nieuwe primaire groep van de failovergroep. Als u de regio of andere informatie in Azure-SSIS IR wilt bijwerken, kunt u deze stoppen, bewerken en opnieuw starten.


### <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scenario 2-Azure-SSIS IR wijst het eind punt van de primaire server aan

Het scenario is geschikt als Azure-SSIS IR verwijst naar het eind punt van de primaire server.

#### <a name="solution"></a>Oplossing

Als er een failover optreedt, moet u het volgende doen:

1. Stop Azure-SSIS IR op de primaire regio.

2. Azure-SSIS IR bewerken met nieuwe regio's, eind punten en VNET-gegevens van een secundair exemplaar.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

3. Start Azure-SSIS IR opnieuw.

### <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scenario 3: een bestaande SSISDB (SSIS-catalogus) koppelen aan een nieuwe Azure-SSIS IR

Dit scenario is geschikt als u een nieuwe Azure-SSIS IR wilt inrichten voor de secundaire regio of als u wilt dat uw SSISDB werkt met een nieuwe Azure-SSIS IR in een nieuwe regio wanneer een ADF of Azure-SSIS IR ramp in de huidige regio optreedt.

#### <a name="solution"></a>Oplossing

> [!NOTE]
> Stap 4 (maken van IR) moet worden uitgevoerd via Power shell. Azure Portal rapporteert een fout waarin wordt vermeld dat SSISDB al bestaat.

1. Stop Azure-SSIS IR op de primaire regio.

2. Voer een opgeslagen procedure voor het bijwerken van meta gegevens in SSISDB uit om verbindingen van ** \<new_data_factory_name\> ** en ** \<new_integration_runtime_name\>** te accepteren.
   
    ```sql
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
    ```

3. Maak een nieuwe Data Factory met de naam ** \<new_data_factory_name\> ** in de nieuwe regio. Zie een data factory maken voor meer informatie.

    ```powershell
    Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
    ```
    
    Zie [een Azure-Data Factory maken met Power shell](quickstart-create-data-factory-powershell.md) voor meer informatie over deze Power shell-opdracht.

4. Maak met behulp van Azure PowerShell een nieuwe Azure-SSIS IR met de naam ** \<new_integration_runtime_name\> ** in de nieuwe regio.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "new resource group name" `
                                           -DataFactoryName "new data factory name" `
                                           -Name "<new_integration_runtime_name>" `
                                           -Description $AzureSSISDescription `
                                           -Type Managed `
                                           -Location $AzureSSISLocation `
                                           -NodeSize $AzureSSISNodeSize `
                                           -NodeCount $AzureSSISNodeNumber `
                                           -Edition $AzureSSISEdition `
                                           -LicenseType $AzureSSISLicenseType `
                                           -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
                                           -VnetId "new vnet" `
                                           -Subnet "new subnet" `
                                           -CatalogServerEndpoint $SSISDBServerEndpoint `
                                           -CatalogPricingTier $SSISDBPricingTier
    ```

    Zie [de Azure-SSIS Integration runtime maken in azure Data Factory](create-azure-ssis-integration-runtime.md) voor meer informatie over deze Power shell-opdracht.


## <a name="next-steps"></a>Volgende stappen

Houd rekening met de volgende andere configuratie opties voor de Azure-SSIS IR:

- [De Azure-SSIS Integration Runtime configureren voor hoge prestaties](configure-azure-ssis-integration-runtime-performance.md)

- [Instelling voor Azure-SSIS Integration Runtime aanpassen](how-to-configure-azure-ssis-ir-custom-setup.md)

- [De Enter prise-editie voor de Azure-SSIS Integration Runtime inrichten](how-to-configure-azure-ssis-ir-enterprise-edition.md)
