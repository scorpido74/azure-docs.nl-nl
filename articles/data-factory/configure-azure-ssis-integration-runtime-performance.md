---
title: Prestaties configureren voor de runtime azure-SSIS-integratie
description: Meer informatie over het configureren van de eigenschappen van de runtime Azure-SSIS-integratie voor hoge prestaties
services: data-factory
ms.date: 01/10/2018
ms.topic: conceptual
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
ms.reviewer: ''
manager: anandsub
ms.openlocfilehash: ca88e42438c7cb48b062aa67d82053afbb9244bf
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81418283"
---
# <a name="configure-the-azure-ssis-integration-runtime-for-high-performance"></a>De runtime azure-SSIS-integratie configureren voor hoge prestaties

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]


In dit artikel wordt beschreven hoe u een Azure-SSIS Integration Runtime (IR) configureert voor hoge prestaties. Met Azure-SSIS IR u SQL Server Integration Services-pakketten (SSIS) implementeren en uitvoeren in Azure. Zie [Artikel Runtime-runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime) van Integratie voor meer informatie over Azure-SSIS IR. Zie [Sql Server Integration Services-workloads naar de cloud tillen en verschuiven voor](/sql/integration-services/lift-shift/ssis-azure-lift-shift-ssis-packages-overview)informatie over het implementeren en uitvoeren van SSIS-pakketten op Azure.

> [!IMPORTANT]
> Dit artikel bevat prestatieresultaten en observaties van interne tests uitgevoerd door leden van het SSIS-ontwikkelingsteam. Uw resultaten kunnen variëren. Doe uw eigen tests voordat u uw configuratie-instellingen afrondt, die zowel de kosten als de prestaties beïnvloeden.

## <a name="properties-to-configure"></a>Eigenschappen om te configureren

In het volgende gedeelte van een configuratiescript worden de eigenschappen weergegeven die u configureren wanneer u een runtime voor Azure-SSIS-integratie maakt. Zie [SQL Server Integration Services-pakketten implementeren naar Azure voor](tutorial-deploy-ssis-packages-azure-powershell.md)het volledige PowerShell-script en de volledige Beschrijving van PowerShell.

```powershell
# If your input contains a PSH special character, e.g. "$", precede it with the escape character "`" like "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime information - This is a Data Factory compute resource for running SSIS packages
$AzureSSISName = "[specify a name for your Azure-SSIS IR]"
$AzureSSISDescription = "[specify a description for your Azure-SSIS IR]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, while Enterprise lets you use advanced/premium features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, while BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit (AHB) option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported, but for other nodes, up to max(2 x number of cores, 8) are currently supported
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info
$SetupScriptContainerSasUri = "" # OPTIONAL to provide SAS URI of blob container where your custom setup script and its associated files are stored
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use Azure SQL Database with virtual network service endpoints/Managed Instance/on-premises data, Azure Resource Manager virtual network is recommended, Classic virtual network will be deprecated soon
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Please use the same subnet as the one used with your Azure SQL Database with virtual network service endpoints or a different subnet than the one used for your Managed Instance

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name or Managed Instance name.DNS prefix].database.windows.net" # WARNING: Please ensure that there is no existing SSISDB, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure Active Directory (AAD)
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for AAD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for AAD authentication]"
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database or leave it empty for Managed Instance]"
```

## <a name="azuressislocation"></a>AzureSSIS-locatie
**AzureSSISLocatie** is de locatie voor het werkknooppunt voor de inwerkingsruntime van de integratie. Het werkknooppunt onderhoudt een constante verbinding met de SSIS-catalogusdatabase (SSISDB) in een Azure SQL-database. Stel de **AzureSSISLocation** in op dezelfde locatie als de SQL Database-server die SSISDB host, waardoor de runtime van de integratie zo efficiënt mogelijk kan werken.

## <a name="azuressisnodesize"></a>AzureSSISNodeSize
Data Factory, inclusief azure-ssis IR, ondersteunt de volgende opties:
-   Standaard\_A4\_v2
-   Standaard\_A8\_v2
-   Standaard\_D1\_v2
-   Standaard\_D2\_v2
-   Standaard\_D3\_v2
-   Standaard\_D4\_v2
-   Standaard\_D2\_v3
-   Standaard\_D4\_v3
-   Standaard\_D8\_v3
-   Standaard\_D16\_v3
-   Standaard\_D32\_v3
-   Standaard\_D64\_v3
-   Standaard\_E2\_v3
-   Standaard\_E4\_v3
-   Standaard\_E8\_v3
-   Standaard\_E16\_v3
-   Standaard\_E32\_v3
-   Standaard\_E64\_v3

In de officieuze interne tests door het SSIS engineering team blijkt de D-serie meer geschikt voor SSIS-pakketuitvoering dan de A-serie.

-   De prestatie-/prijsverhouding van de D-serie is hoger dan de A-serie en de prestatie-/prijsverhouding van de v3-serie is hoger dan de v2-serie.
-   De doorvoer voor de D-serie is hoger dan de A-serie tegen dezelfde prijs en de doorvoer voor de v3-serie is hoger dan de v2-serie tegen dezelfde prijs.
-   De v2-serie knooppunten van Azure-SSIS IR zijn niet geschikt voor aangepaste installatie, dus gebruik in plaats daarvan de v3-serie knooppunten. Als u de knooppunten uit de V2-serie al gebruikt, schakelt u zo snel mogelijk over naar de nodes uit de V3-serie.
-   De E-serie is geheugengeoptimaliseerde VM-formaten die een hogere geheugen-cpu-verhouding bieden dan andere machines. Als uw pakket veel geheugen vereist, u overwegen om VM uit de E-serie te kiezen.

### <a name="configure-for-execution-speed"></a>Configureren voor uitvoeringssnelheid
Als u niet veel pakketten hebt om uit te voeren en u wilt dat pakketten snel worden uitgevoerd, gebruikt u de informatie in de volgende grafiek om een virtueel machinetype te kiezen dat geschikt is voor uw scenario.

Deze gegevens vertegenwoordigen één pakketuitvoering op één werknemersknooppunt. Het pakket laadt 3 miljoen records met voor- en achternaamkolommen uit Azure Blob Storage, genereert een volledige naamkolom en schrijft de records met de volledige naam langer dan 20 tekens naar Azure Blob Storage.

![Uitvoeringssnelheid runtime-pakket voor SSIS-integratie](media/configure-azure-ssis-integration-runtime-performance/ssisir-execution-speedV2.png)

### <a name="configure-for-overall-throughput"></a>Configureren voor algehele doorvoer

Als u veel pakketten hebt om uit te voeren en u het meest om de totale doorvoer geeft, gebruikt u de informatie in de volgende grafiek om een virtueel machinetype te kiezen dat geschikt is voor uw scenario.

![Runtime van SSIS-integratie maximale totale doorvoer](media/configure-azure-ssis-integration-runtime-performance/ssisir-overall-throughputV2.png)

## <a name="azuressisnodenumber"></a>AzureSSISNodeNumber

**AzureSSISNodeNumber** past de schaalbaarheid van de runtime van de integratie aan. De doorvoer van de runtime van de integratie is evenredig met het **AzureSSISNodeNumber.** Stel het **AzureSSISNodeNumber** eerst in op een kleine waarde, controleer eerst de doorvoer van de runtime van de integratie en pas vervolgens de waarde voor uw scenario aan. Zie Een [runtime voor Azure-SSIS-integratie beheren](manage-azure-ssis-integration-runtime.md)als u het aantal werknemersknooppunten opnieuw wilt configureren.

## <a name="azuressismaxparallelexecutionspernode"></a>AzureSSISMaxParallelExecutionsPerNode

Wanneer u al een krachtig werknemersknooppunt gebruikt om pakketten uit te voeren, kan het verhogen van **AzureSSISMaxParallelExecutionsPerNode** de algehele doorvoer van de uitvoeringstijd van de integratie verhogen. Voor Standard_D1_v2 knooppunten worden 1-4 parallelle uitvoeringen per knooppunt ondersteund. Voor alle andere typen knooppunten worden 1-max(2 x aantal cores, 8) parallelle uitvoeringen per knooppunt ondersteund. Als u **AzureSSISMaxParallelExecutionsPerNode** wilt buiten de maximale waarde die we hebben ondersteund, u een ondersteuningsticket openen en kunnen we de maximale waarde voor u verhogen en daarna moet u Azure Powershell gebruiken om **AzureSSISMaxParallelExecutionsPerNode**bij te werken.
U de juiste waarde schatten op basis van de kosten van uw pakket en de volgende configuraties voor de werknemersknooppunten. Zie voor meer informatie [de grootte van virtuele machineformaten voor algemene doeleinden](../virtual-machines/windows/sizes-general.md).

| Grootte             | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximale tijdelijke opslagdoorvoer: IOPS / MBps lezen / MBps schrijven | Maximumaantal gegevensschijven / doorvoer: IOPS | Maximum aantal NIC's/verwachte netwerkprestaties (Mbps) |
|------------------|------|-------------|------------------------|------------------------------------------------------------|-----------------------------------|------------------------------------------------|
| Standaard\_D1\_v2 | 1    | 3,5         | 50                     | 3000 / 46 / 23                                             | 2 / 2 x 500                         | 2 / 750                                        |
| Standaard\_D2\_v2 | 2    | 7           | 100                    | 6000 / 93 / 46                                             | 4 / 4 x 500                         | 2 / 1500                                       |
| Standaard\_D3\_v2 | 4    | 14          | 200                    | 12.000 / 187 / 93                                           | 8 / 8 x 500                         | 4 / 3000                                       |
| Standaard\_D4\_v2 | 8    | 28          | 400                    | 24.000 / 375 / 187                                          | 16 / 16 x 500                       | 8 / 6000                                       |
| Standaard\_A4\_v2 | 4    | 8           | 40                     | 4000 / 80 / 40                                             | 8 / 8 x 500                         | 4 / 1000                                       |
| Standaard\_A8\_v2 | 8    | 16          | 80                     | 8000 / 160 / 80                                            | 16 / 16 x 500                       | 8 / 2000                                       |
| Standaard\_D2\_v3 | 2    | 8           | 50                     | 3000 / 46 / 23                                             | 4 / 6x500                         | 2 / 1000                                       |
| Standaard\_D4\_v3 | 4    | 16          | 100                    | 6000 / 93 / 46                                             | 8 / 12x500                        | 2 / 2000                                       |
| Standaard\_D8\_v3 | 8    | 32          | 200                    | 12.000 / 187 / 93                                           | 16 / 24x500                       | 4 / 4000                                       |
| Standaard\_D16\_v3| 16   | 64          | 400                    | 24.000 / 375 / 187                                          | 32/ 48x500                        | 8 / 8000                                       |
| Standaard\_D32\_v3| 32   | 128         | 800                    | 48.000 / 750 / 375                                          | 32 / 96x500                       | 8 / 16000                                      |
| Standaard\_D64\_v3| 64   | 256         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192x500                      | 8 / 30000                                      |
| Standaard\_E2\_v3 | 2    | 16          | 50                     | 3000 / 46 / 23                                             | 4 / 6x500                         | 2 / 1000                                       |
| Standaard\_E4\_v3 | 4    | 32          | 100                    | 6000 / 93 / 46                                             | 8 / 12x500                        | 2 / 2000                                       |
| Standaard\_E8\_v3 | 8    | 64          | 200                    | 12.000 / 187 / 93                                           | 16 / 24x500                       | 4 / 4000                                       |
| Standaard\_E16\_v3| 16   | 128         | 400                    | 24.000 / 375 / 187                                          | 32 / 48x500                       | 8 / 8000                                       |
| Standaard\_E32\_v3| 32   | 256         | 800                    | 48.000 / 750 / 375                                          | 32 / 96x500                       | 8 / 16000                                      |
| Standaard\_E64\_v3| 64   | 432         | 1600                   | 96000 / 1000 / 500                                         | 32 / 192x500                      | 8 / 30000                                      |

Hier volgen de richtlijnen voor het instellen van de juiste waarde voor de eigenschap **AzureSSSISMaxParallelExecutionsPerNode:** 

1. Stel het in eerste instantie in op een kleine waarde.
2. Verhoog het met een kleine hoeveelheid om te controleren of de totale doorvoer is verbeterd.
3. Stop met het verhogen van de waarde wanneer de totale doorvoer de maximale waarde bereikt.

## <a name="ssisdbpricingtier"></a>SSISDBPricingTier

**SSISDBPricingTier** is de prijscategorie voor de SSIS Catalog-database (SSISDB) in een Azure SQL-database. Deze instelling is van invloed op het maximum aantal werknemers in de IR-instantie, de snelheid om een pakketuitvoering in de wachtrij te plaatsen en de snelheid om het uitvoeringslogboek te laden.

-   Als u niet de snelheid geeft om de uitvoering van het pakket in de wachtrij te plaatsen en het uitvoeringslogboek te laden, u de laagste databaseprijslaag kiezen. Azure SQL Database with Basic pricing ondersteunt 8 workers in een instantie voor runtime voor integratie.

-   Kies een krachtigere database dan Basic als het aantal werknemers meer dan 8 is of als het aantal kernen meer dan 50 is. Anders wordt de database het knelpunt van de instantie voor de uitvoering van de integratie en wordt de algehele prestaties negatief beïnvloed.

-   Kies een krachtigere database zoals s3 als het logboekniveau is ingesteld op verbose. Volgens onze onofficiële interne tests kan de S3-prijscategorie de uitvoering van SSIS-pakketten ondersteunen met 2 knooppunten, 128 parallelle tellingen en een verbose-registratieniveau.

U ook de databaseprijslaag aanpassen op basis van dtu-gebruiksgegevens [(databasetransactie-eenheid)](../sql-database/sql-database-what-is-a-dtu.md) die beschikbaar zijn op de Azure-portal.

## <a name="design-for-high-performance"></a>Ontwerp voor hoge prestaties
Het ontwerpen van een SSIS-pakket dat op Azure wordt uitgevoerd, is anders dan het ontwerpen van een pakket voor on-premises uitvoering. In plaats van meerdere onafhankelijke taken in hetzelfde pakket te combineren, scheidt u deze in verschillende pakketten voor een efficiëntere uitvoering in de Azure-SSIS IR. Maak een pakketuitvoering voor elk pakket, zodat ze niet hoeven te wachten tot ze klaar zijn. Deze aanpak profiteert van de schaalbaarheid van de runtime van Azure-SSIS-integratie en verbetert de algehele doorvoer.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de runtime azure-SSIS-integratie. Zie [Runtime Azure-SSIS-integratie](concepts-integration-runtime.md#azure-ssis-integration-runtime).
