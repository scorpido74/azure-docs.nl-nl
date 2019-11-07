---
title: Azure-SSIS Integration Runtime voor SQL Database failover configureren
description: In dit artikel wordt beschreven hoe u de Azure-SSIS Integration Runtime configureert met Azure SQL Database geo-replicatie en failover voor de SSISDB-data base
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: ''
ms.devlang: powershell
ms.topic: conceptual
ms.date: 08/14/2018
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: craigg
ms.openlocfilehash: 6e709a25c6c33a1fc80a110435035b1473d92681
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681396"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>De Azure-SSIS Integration Runtime configureren met Azure SQL Database geo-replicatie en failover

In dit artikel wordt beschreven hoe u de Azure-SSIS Integration Runtime configureert met Azure SQL Database geo-replicatie voor de SSISDB-data base. Wanneer een failover optreedt, kunt u ervoor zorgen dat de Azure-SSIS IR met de secundaire data base werkt.

Zie [overzicht: actieve geo-replicatie en groepen voor automatische failover](../sql-database/sql-database-geo-replication-overview.md)voor meer informatie over geo-replicatie en failover voor SQL database.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scenario 1-Azure-SSIS IR verwijst naar het listener-eind punt voor lezen/schrijven

### <a name="conditions"></a>Voorwaarden

Deze sectie is van toepassing wanneer aan de volgende voor waarden wordt voldaan:

- De Azure-SSIS IR verwijst naar het Lees-en luister eindpunt van de failovergroep.

  EN

- De SQL Database-Server is *niet* geconfigureerd met de eindpunt regel van de virtuele netwerk service.

### <a name="solution"></a>Oplossing

Wanneer een failover optreedt, is het transparant voor de Azure-SSIS IR. De Azure-SSIS IR maakt automatisch verbinding met de nieuwe primaire groep van de failovergroep.

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scenario 2-Azure-SSIS IR wijst het eind punt van de primaire server aan

### <a name="conditions"></a>Voorwaarden

Deze sectie is van toepassing wanneer een van de volgende voor waarden waar is:

- De Azure-SSIS IR verwijst naar het primaire server eindpunt van de failovergroep. Dit eind punt wordt gewijzigd wanneer failover plaatsvindt.

  OF

- De Azure SQL Database-Server is geconfigureerd met de eindpunt regel van de virtuele netwerk service.

  OF

- De database server is een SQL Database beheerd exemplaar dat is geconfigureerd met een virtueel netwerk.

### <a name="solution"></a>Oplossing

Als er een failover optreedt, moet u het volgende doen:

1. Stop de Azure-SSIS IR.

2. Configureer de IR zo dat deze verwijst naar het nieuwe primaire eind punt en naar een virtueel netwerk in de nieuwe regio.

3. Start de IR opnieuw op.

In de volgende secties worden deze stappen uitvoeriger beschreven.

### <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat u herstel na nood geval hebt ingeschakeld voor uw Azure SQL Database Server als de server op hetzelfde moment een onderbreking heeft. Zie [overzicht van bedrijfs continuïteit met Azure SQL database](../sql-database/sql-database-business-continuity.md)voor meer informatie.

- Als u een virtueel netwerk in de huidige regio gebruikt, moet u een ander virtueel netwerk in de nieuwe regio gebruiken om verbinding te maken met uw Azure SSIS Integration runtime. Zie [een Azure-SSIS-integratie-runtime toevoegen aan een virtueel netwerk](join-azure-ssis-integration-runtime-virtual-network.md)voor meer informatie.

- Als u een aangepaste installatie gebruikt, moet u mogelijk een andere SAS-URI voorbereiden voor de BLOB-container waarin uw aangepaste installatie script en de bijbehorende bestanden worden opgeslagen, zodat deze beschikbaar blijven tijdens een storing. Zie [een aangepaste installatie configureren voor een Azure-SSIS Integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md)voor meer informatie.

### <a name="steps"></a>Stappen

Volg deze stappen om uw Azure-SSIS IR te stoppen, de IR naar een nieuwe regio over te scha kelen en opnieuw te starten.

1. Stop de IR in de oorspronkelijke regio.

2. Roep de volgende opdracht aan in Power shell om de IR bij te werken met de nieuwe instellingen.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    Zie [de Azure-SSIS Integration runtime maken in azure Data Factory](create-azure-ssis-integration-runtime.md) voor meer informatie over deze Power shell-opdracht.

3. Start de IR opnieuw.

## <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scenario 3: een bestaande SSISDB (SSIS-catalogus) koppelen aan een nieuwe Azure-SSIS IR

Wanneer een ADF of Azure-SSIS IR nood geval in de huidige regio optreedt, kunt u ervoor zorgen dat uw SSISDB met een nieuwe Azure-SSIS IR in een nieuwe regio werkt.

### <a name="prerequisites"></a>Vereisten

- Als u een virtueel netwerk in de huidige regio gebruikt, moet u een ander virtueel netwerk in de nieuwe regio gebruiken om verbinding te maken met uw Azure SSIS Integration runtime. Zie [een Azure-SSIS-integratie-runtime toevoegen aan een virtueel netwerk](join-azure-ssis-integration-runtime-virtual-network.md)voor meer informatie.

- Als u een aangepaste installatie gebruikt, moet u mogelijk een andere SAS-URI voorbereiden voor de BLOB-container waarin uw aangepaste installatie script en de bijbehorende bestanden worden opgeslagen, zodat deze beschikbaar blijven tijdens een storing. Zie [een aangepaste installatie configureren voor een Azure-SSIS Integration runtime](how-to-configure-azure-ssis-ir-custom-setup.md)voor meer informatie.

### <a name="steps"></a>Stappen

Volg deze stappen om uw Azure-SSIS IR te stoppen, de IR naar een nieuwe regio over te scha kelen en opnieuw te starten.

1. Voer een opgeslagen procedure uit om SSISDB toe te voegen aan **\<new_data_factory_name\>** of **\<new_integration_runtime_name\>** .
   
  ```SQL
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

2. Maak een nieuwe data factory met de naam **\<new_data_factory_name\>** in de nieuwe regio. Zie een data factory maken voor meer informatie.

     ```powershell
     Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
     ```
    Zie [een Azure-Data Factory maken met Power shell](quickstart-create-data-factory-powershell.md) voor meer informatie over deze Power shell-opdracht.

3. Maak met behulp van Azure PowerShell een nieuwe Azure-SSIS IR met de naam **\<new_integration_runtime_name\>** in de nieuwe regio.

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

4. Start de IR opnieuw.

## <a name="next-steps"></a>Volgende stappen

Houd rekening met de volgende andere configuratie opties voor de Azure-SSIS IR:

- [De Azure-SSIS Integration Runtime configureren voor hoge prestaties](configure-azure-ssis-integration-runtime-performance.md)

- [Instelling voor Azure-SSIS Integration Runtime aanpassen](how-to-configure-azure-ssis-ir-custom-setup.md)

- [De Enter prise-editie voor de Azure-SSIS Integration Runtime inrichten](how-to-configure-azure-ssis-ir-enterprise-edition.md)
