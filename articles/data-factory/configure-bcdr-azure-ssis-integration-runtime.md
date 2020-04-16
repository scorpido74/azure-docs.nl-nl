---
title: Runtime azure-SSIS-integratie configureren voor SQL-database-failover
description: In dit artikel wordt beschreven hoe u de runtime azure-SSIS-integratie configureert met georeplicatie en failover van Azure SQL Database voor de SSISDB-database
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
ms.openlocfilehash: 75dd5a917d718f4ccef034e953a415d575d42bd9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418300"
---
# <a name="configure-the-azure-ssis-integration-runtime-with-azure-sql-database-geo-replication-and-failover"></a>De runtime azure-SSIS-integratie configureren met geo-replicatie en failover van Azure SQL Database

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In dit artikel wordt beschreven hoe u de runtime azure-SSIS-integratie configureert met georeplicatie van Azure SQL Database voor de SSISDB-database. Wanneer er een failover optreedt, u ervoor zorgen dat azure-SSIS IR blijft werken met de secundaire database.

Zie [Overzicht: Actieve georeplicatie- en auto-failovergroepen](../sql-database/sql-database-geo-replication-overview.md)voor meer informatie over georeplicatie en failover voor SQL-database.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario-1---azure-ssis-ir-is-pointing-to-read-write-listener-endpoint"></a>Scenario 1 - Azure-SSIS IR wijst naar eindpunt voor leesschrijflistener

### <a name="conditions"></a>Voorwaarden

Deze sectie is van toepassing wanneer de volgende voorwaarden waar zijn:

- De Azure-SSIS IR wijst naar het eindpunt van de leesschrijflistener van de failovergroep.

  EN

- De SQL Database-server is *niet* geconfigureerd met de eindpuntregel voor virtuele netwerkservice.

### <a name="solution"></a>Oplossing

Wanneer er een failover plaatsvindt, is deze transparant voor de Azure-SSIS IR. De Azure-SSIS IR maakt automatisch verbinding met het nieuwe primaire voorwerk van de failovergroep.

## <a name="scenario-2---azure-ssis-ir-is-pointing-to-primary-server-endpoint"></a>Scenario 2 - Azure-SSIS IR wijst naar primair servereindpunt

### <a name="conditions"></a>Voorwaarden

Deze sectie is van toepassing wanneer een van de volgende voorwaarden waar is:

- De Azure-SSIS IR wijst naar het primaire servereindpunt van de failovergroep. Dit eindpunt verandert wanneer er een failover optreedt.

  OF

- De Azure SQL Database-server is geconfigureerd met de eindpuntregel voor de virtuele netwerkservice.

  OF

- De databaseserver is een SQL Database Managed Instance geconfigureerd met een virtueel netwerk.

### <a name="solution"></a>Oplossing

Wanneer failover optreedt, moet u de volgende dingen doen:

1. Stop de Azure-SSIS IR.

2. Configureer de IR om te wijzen op het nieuwe primaire eindpunt en naar een virtueel netwerk in de nieuwe regio.

3. Start de IR opnieuw.

In de volgende secties worden deze stappen nader beschreven.

### <a name="prerequisites"></a>Vereisten

- Zorg ervoor dat u disaster recovery hebt ingeschakeld voor uw Azure SQL Database-server voor het geval de server tegelijkertijd een storing heeft. Zie [Overzicht van bedrijfscontinuïteit met Azure SQL Database](../sql-database/sql-database-business-continuity.md)voor meer informatie.

- Als u een virtueel netwerk in de huidige regio gebruikt, moet u een ander virtueel netwerk in de nieuwe regio gebruiken om de runtime van uw Azure-SSIS-integratie met elkaar te verbinden. Zie [Runtime van Azure-SSIS-integratie deelnemen aan een virtueel netwerk voor](join-azure-ssis-integration-runtime-virtual-network.md)meer informatie.

- Als u een aangepaste installatie gebruikt, moet u mogelijk een andere SAS URI voorbereiden voor de blobcontainer die uw aangepaste installatiescript en bijbehorende bestanden opslaat, zodat deze toegankelijk blijft tijdens een storing. Zie Een aangepaste instelling configureren voor een runtime van [Azure-SSIS-integratie voor](how-to-configure-azure-ssis-ir-custom-setup.md)meer informatie.

### <a name="steps"></a>Stappen

Volg deze stappen om uw Azure-SSIS IR te stoppen, de IR over te schakelen naar een nieuwe regio en opnieuw te starten.

1. Stop de IR in de oorspronkelijke regio.

2. Roep de volgende opdracht in PowerShell om de IR bij te werken met de nieuwe instellingen.

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -Location "new region" `
                    -CatalogServerEndpoint "Azure SQL Database server endpoint" `
                    -CatalogAdminCredential "Azure SQL Database server admin credentials" `
                    -VNetId "new VNet" `
                    -Subnet "new subnet" `
                    -SetupScriptContainerSasUri "new custom setup SAS URI"
    ```

    Zie [Runtime azure-SSIS-integratie maken in Azure Data Factory voor](create-azure-ssis-integration-runtime.md) meer informatie over deze PowerShell-opdracht

3. Start de IR opnieuw.

## <a name="scenario-3---attaching-an-existing-ssisdb-ssis-catalog-to-a-new-azure-ssis-ir"></a>Scenario 3 - Een bestaande SSISDB -catalogus (SSIS-catalogus) koppelen aan een nieuwe Azure-SSIS IR

Wanneer een ADF- of Azure-SSIS IR-ramp zich voordoet in de huidige regio, u ervoor zorgen dat uw SSISDB blijft werken met een nieuwe Azure-SSIS IR in een nieuwe regio.

### <a name="prerequisites"></a>Vereisten

- Als u een virtueel netwerk in de huidige regio gebruikt, moet u een ander virtueel netwerk in de nieuwe regio gebruiken om de runtime van uw Azure-SSIS-integratie met elkaar te verbinden. Zie [Runtime van Azure-SSIS-integratie deelnemen aan een virtueel netwerk voor](join-azure-ssis-integration-runtime-virtual-network.md)meer informatie.

- Als u een aangepaste installatie gebruikt, moet u mogelijk een andere SAS URI voorbereiden voor de blobcontainer die uw aangepaste installatiescript en bijbehorende bestanden opslaat, zodat deze toegankelijk blijft tijdens een storing. Zie Een aangepaste instelling configureren voor een runtime van [Azure-SSIS-integratie voor](how-to-configure-azure-ssis-ir-custom-setup.md)meer informatie.

### <a name="steps"></a>Stappen

Volg deze stappen om uw Azure-SSIS IR naar een nieuwe regio te verplaatsen.
> [!NOTE]
> Stap 3 (creatie van IR) moet worden gedaan via PowerShell. Azure-portal rapporteert een fout waarin staat dat SSISDB al bestaat.

1. De opgeslagen procedure uitvoeren om metagegevens in SSISDB bij te werken om verbindingen van ** \<new_data_factory_name\> ** en ** \<new_integration_runtime_name\>** te accepteren.
   
  ```SQL
    EXEC [catalog].[failover_integration_runtime] @data_factory_name='<new_data_factory_name>', @integration_runtime_name='<new_integration_runtime_name>'
   ```

2. Maak een nieuwe ** \<\> ** gegevensfabriek met de naam new_data_factory_name in de nieuwe regio. Zie Een gegevensfabriek maken voor meer informatie.

     ```powershell
     Set-AzDataFactoryV2 -ResourceGroupName "new resource group name" `
                         -Location "new region"`
                         -Name "<new_data_factory_name>"
     ```
    Zie [Een Azure-gegevensfabriek maken met PowerShell](quickstart-create-data-factory-powershell.md) voor meer informatie over deze PowerShell-opdracht

3. Maak een nieuwe Azure-SSIS IR met ** \<de\> ** naam new_integration_runtime_name in de nieuwe regio met Azure PowerShell.

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

    Zie [Runtime azure-SSIS-integratie maken in Azure Data Factory voor](create-azure-ssis-integration-runtime.md) meer informatie over deze PowerShell-opdracht

4. Start de IR opnieuw.

## <a name="next-steps"></a>Volgende stappen

Houd rekening met deze andere configuratieopties voor Azure-SSIS IR:

- [De runtime azure-SSIS-integratie configureren voor hoge prestaties](configure-azure-ssis-integration-runtime-performance.md)

- [Instelling voor Azure-SSIS Integration Runtime aanpassen](how-to-configure-azure-ssis-ir-custom-setup.md)

- [Enterprise Edition inrichten voor de runtime azure-SSIS-integratie](how-to-configure-azure-ssis-ir-enterprise-edition.md)
