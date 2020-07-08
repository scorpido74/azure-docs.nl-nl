---
title: Integration runtime in Azure Data Factory bewaken
description: Meer informatie over het bewaken van verschillende typen Integration runtime in Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/25/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: cfb40375fe841dd363681aea3d2cf6355046cd51
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84113692"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Een IR bewaken in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]
  
**Integration runtime** is de reken infrastructuur die wordt gebruikt door Azure Data Factory om verschillende mogelijkheden voor gegevens integratie in verschillende netwerk omgevingen te bieden. Er zijn drie typen Integration runtimes die worden aangeboden door Data Factory:

- Azure Integration Runtime
- Zelf-hostende Integration Runtime
- Azure-SSIS-integratie-runtime

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u de status van een exemplaar van Integration runtime (IR) wilt ophalen, voert u de volgende Power shell-opdracht uit: 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

De cmdlet retourneert verschillende informatie voor verschillende typen Integration runtime. In dit artikel worden de eigenschappen en statussen van elk type Integration runtime beschreven.  

## <a name="azure-integration-runtime"></a>Azure Integration Runtime
De reken resource voor een Azure Integration runtime wordt volledig beheerd in Azure. De volgende tabel bevat beschrijvingen voor eigenschappen die worden geretourneerd door de opdracht **Get-AzDataFactoryV2IntegrationRuntime** :

### <a name="properties"></a>Eigenschappen
De volgende tabel bevat beschrijvingen van de eigenschappen die worden geretourneerd door de cmdlet voor een Azure Integration runtime:

| Eigenschap | Beschrijving |
-------- | ------------- | 
| Naam | De naam van de Azure Integration runtime. |  
| Status | Status van de Azure Integration runtime. | 
| Locatie | Locatie van de Azure Integration runtime. Zie [Inleiding tot Integration runtime](concepts-integration-runtime.md)voor meer informatie over de locatie van een Azure Integration runtime. |
| DataFactoryName | De naam van de data factory waarvan de Azure Integration runtime deel uitmaakt. | 
| ResourceGroupName | De naam van de resource groep waartoe de data factory behoort.  |
| Description | Beschrijving van de Integration runtime.  |

### <a name="status"></a>Status
De volgende tabel bevat mogelijke statussen van een Azure Integration runtime:

| Status | Opmerkingen/Scenario's | 
| ------ | ------------------ |
| Online | Azure Integration runtime is online en klaar om te worden gebruikt. | 
| Offline | De Azure Integration runtime is offline vanwege een interne fout. |

## <a name="self-hosted-integration-runtime"></a>Zelf-hostende Integration Runtime
Deze sectie bevat beschrijvingen voor eigenschappen die door de cmdlet Get-AzDataFactoryV2IntegrationRuntime worden geretourneerd. 

> [!NOTE] 
> De geretourneerde eigenschappen en status bevatten informatie over de algehele zelf-hostende Integration runtime en elk knoop punt in de runtime.  

### <a name="properties"></a>Eigenschappen

De volgende tabel bevat beschrijvingen van de bewakings eigenschappen voor **elk knoop punt**:

| Eigenschap | Beschrijving | 
| -------- | ----------- | 
| Naam | De naam van de zelf-hostende Integration runtime en knoop punten die eraan zijn gekoppeld. Knoop punt is een on-premises Windows-computer waarop de zelf-hostende Integration runtime is geïnstalleerd. |  
| Status | De status van de algemene zelf-hostende Integration runtime en elk knoop punt. Voor beeld: online/offline/beperkt/etc. Zie de volgende sectie voor meer informatie over deze statussen. | 
| Versie | De versie van de zelf-hostende Integration runtime en elk knoop punt. De versie van de zelf-hostende Integration runtime wordt bepaald op basis van de versie van de knoop punten in de groep. Als er knoop punten met verschillende versies in de zelf-hostende Integration runtime-installatie zijn, worden alleen de knoop punten met hetzelfde versie nummer als de logische zelf-hostende Integration runtime-functie correct uitgevoerd. Andere bevinden zich in de beperkte modus en moeten hand matig worden bijgewerkt (alleen als de automatische update mislukt). | 
| Beschikbaar geheugen | Beschikbaar geheugen op een zelf-hostend Integration runtime-knoop punt. Deze waarde is een bijna realtime moment opname. | 
| CPU-gebruik | CPU-gebruik van een zelf-hostend Integration runtime-knoop punt. Deze waarde is een bijna realtime moment opname. |
| Netwerken (in/uit) | Netwerk gebruik van een zelf-hostend Integration runtime-knoop punt. Deze waarde is een bijna realtime moment opname. | 
| Gelijktijdige taken (uitvoeren/beperken) | **Wordt uitgevoerd**. Aantal taken of taken dat op elk knoop punt wordt uitgevoerd. Deze waarde is een bijna realtime moment opname. <br/><br/>**Limiet**. De limiet is het maximale aantal gelijktijdige taken voor elk knoop punt. Deze waarde wordt gedefinieerd op basis van de grootte van de machine. U kunt de limiet verhogen voor het opschalen van gelijktijdige taak uitvoering in geavanceerde scenario's, wanneer activiteiten een time-out hebben, zelfs wanneer de CPU, het geheugen of het netwerk wordt gebruikt. Deze mogelijkheid is ook beschikbaar met een zelf-hostende Integration runtime met één knoop punt. |
| Rol | Er zijn twee soorten rollen in een zelf-hostende Integration runtime met meerdere knoop punten: dispatcher en worker. Alle knoop punten zijn werk nemers, wat betekent dat ze allemaal kunnen worden gebruikt om taken uit te voeren. Er is slechts één dispatcher-knoop punt, dat wordt gebruikt om taken/taken uit te geven vanuit Cloud Services en deze te verzenden naar verschillende worker-knoop punten. Het dispatcher-knoop punt is ook een worker-knoop punt. |

Sommige instellingen van de eigenschappen maken meer indruk wanneer er twee of meer knoop punten aanwezig zijn in de zelf-hostende Integration runtime (dat wil zeggen, in een scenario voor schalen).

#### <a name="concurrent-jobs-limit"></a>Limiet voor gelijktijdige taken

De standaard waarde van de limiet voor gelijktijdige taken wordt ingesteld op basis van de grootte van de machine. De factoren die worden gebruikt om deze waarde te berekenen, zijn afhankelijk van de hoeveelheid RAM-geheugen en het aantal CPU-kernen van de machine. Hoe meer kernen en meer geheugen, des te hoger de standaard limiet van gelijktijdige taken.

U kunt uitschalen door het aantal knoop punten te verhogen. Wanneer u het aantal knoop punten verhoogt, is de limiet voor gelijktijdige taken de som van de gelijktijdige taak limiet waarden van alle beschik bare knoop punten.  Als u bijvoorbeeld met één knoop punt Maxi maal twaalf gelijktijdige taken uitvoert, kunt u met drie meer soort gelijke knoop punten Maxi maal 48 gelijktijdige taken uitvoeren (dat wil zeggen 4 x 12). Het is raadzaam om de limiet voor gelijktijdige taken alleen te verhogen wanneer u weinig resource gebruik ziet met de standaard waarden voor elk knoop punt.

U kunt de berekende standaard waarde in het Azure Portal overschrijven. Selecteer Auteur > verbindingen > Integration Runtimes > > knooppunten te bewerken > gelijktijdige taak waarde per knoop punt te wijzigen. U kunt ook de opdracht Power shell [Update-Azdatafactoryv2integrationruntimenode](https://docs.microsoft.com/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) gebruiken.
  
### <a name="status-per-node"></a>Status (per knoop punt)
De volgende tabel bevat mogelijke statussen van een zelf-hostende Integration runtime-knoop punt:

| Status | Beschrijving |
| ------ | ------------------ | 
| Online | Het knoop punt is verbonden met de Data Factory-service. |
| Offline | Het knoop punt is offline. |
| Abonnement | Het knoop punt wordt automatisch bijgewerkt. |
| Beperkt | Vanwege een probleem met de verbinding. Wordt mogelijk veroorzaakt door een probleem met de HTTP-poort 8050, een probleem met de Service Bus-verbinding of een probleem met de synchronisatie van referenties. |
| Niet-actief | Het knoop punt bevindt zich in een configuratie die verschilt van de configuratie van andere hoofd knooppunten. |

Een knoop punt kan inactief zijn wanneer er geen verbinding kan worden gemaakt met andere knoop punten.

### <a name="status-overall-self-hosted-integration-runtime"></a>Status (algemene zelf-hostende Integration runtime)
De volgende tabel bevat mogelijke statussen van een zelf-hostende Integration runtime. Deze status is afhankelijk van de status van alle knoop punten die deel uitmaken van de runtime. 

| Status | Beschrijving |
| ------ | ----------- | 
| Registratie vereist | Er is nog geen knoop punt geregistreerd bij deze zelf-hostende Integration runtime. |
| Online | Alle knoop punten zijn online. |
| Offline | Er is geen knoop punt online. |
| Beperkt | Niet alle knoop punten in deze zelf-hostende Integration runtime hebben de status in orde. Deze status is een waarschuwing dat bepaalde knoop punten mogelijk niet actief zijn. Deze status kan worden veroorzaakt door een probleem met de synchronisatie van referenties op de verzender/het worker-knoop punt. |

Gebruik de cmdlet **Get-AzDataFactoryV2IntegrationRuntimeMetric** om de JSON-nettolading op te halen met de gedetailleerde runtime-eigenschappen voor zelf-hostende Integration en de bijbehorende momentopname waarden tijdens de uitvoering van de cmdlet.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName | ConvertTo-Json 
```

Voorbeeld uitvoer (er wordt van uitgegaan dat er twee knoop punten zijn gekoppeld aan deze zelf-hostende Integration runtime):

```json
{
    "IntegrationRuntimeName":  "<Name of your integration runtime>",
    "ResourceGroupName":  "<Resource Group Name>",
    "DataFactoryName":  "<Data Factory Name>",
    "Nodes":  [
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        },
        {
            "NodeName":  "<Node Name>",
            "AvailableMemoryInMB":  <Value>,
            "CpuUtilization":  <Value>,
            "ConcurrentJobsLimit":  <Value>,
            "ConcurrentJobsRunning":  <Value>,
            "MaxConcurrentJobs":  <Value>,
            "SentBytes":  <Value>,
            "ReceivedBytes":  <Value>
        }

    ]
} 
```


## <a name="azure-ssis-integration-runtime"></a>Azure-SSIS-integratie-runtime
Azure-SSIS Integration runtime is een volledig beheerd cluster van virtuele Azure-machines (of knoop punten) die zijn toegewezen om uw SSIS-pakketten uit te voeren. Er worden geen andere activiteiten van Azure Data Factory uitgevoerd. Zodra u het hebt ingericht, kunt u de eigenschappen opvragen en de totale/knooppunt specifieke statussen controleren.

### <a name="properties"></a>Eigenschappen

| Eigenschap/status | Description |
| --------------- | ----------- |
| CreateTime | De UTC-tijd waarop uw Azure SSIS Integration runtime is gemaakt. |
| Knooppunten | De toegewezen/beschik bare knoop punten van uw Azure-SSIS-integratie-runtime met knooppunt statussen (gestart/beschikbaar/recycling/niet beschikbaar) en bruikbare fouten. |
| OtherErrors | De niet-knooppunt specifieke actie fouten op uw Azure SSIS Integration runtime. |
| LastOperation | Het resultaat van de laatste start-en stop bewerking voor uw Azure SSIS Integration runtime met een actie bare fout (en) als deze is mislukt. |
| Status | De algehele status van uw Azure SSIS Integration runtime (begin/start/gestart/gestopt). |
| Locatie | De locatie van uw Azure SSIS Integration runtime. |
| NodeSize | De grootte van elk knoop punt van uw Azure SSIS Integration runtime. |
| NodeCount | Het aantal knoop punten in uw Azure SSIS Integration runtime. |
| MaxParallelExecutionsPerNode | Het aantal parallelle uitvoeringen per knoop punt in uw Azure SSIS Integration runtime. |
| CatalogServerEndpoint | Het eind punt van uw bestaande door SQL Database/SQL beheerde instantie voor het hosten van SSISDB. |
| CatalogAdminUserName | De gebruikers naam van de beheerder van uw bestaande door SQL Database/SQL beheerd exemplaar. Data Factory-service gebruikt deze informatie om namens u SSISDB voor te bereiden en te beheren. |
| CatalogAdminPassword | Het beheerders wachtwoord van uw bestaande door SQL Database/SQL beheerd exemplaar. |
| CatalogPricingTier | De prijs categorie voor SSISDB die worden gehost door SQL Database.  Niet van toepassing op een SQL Managed instance die als host fungeert voor SSISDB. |
| VNetId | De resource-ID van het virtuele netwerk voor uw Azure-SSIS Integration runtime om samen te voegen. |
| Subnet | De naam van het subnet voor uw Azure-SSIS Integration runtime om samen te voegen. |
| Id | De resource-ID van uw Azure SSIS Integration runtime. |
| Type | Het type (beheerd/zelf-Hostend) van uw Azure SSIS Integration runtime. |
| ResourceGroupName | De naam van uw Azure-resource groep waarin uw data factory en Azure-SSIS Integration runtime zijn gemaakt. |
| DataFactoryName | De naam van uw Azure-data factory. |
| Name | De naam van uw Azure SSIS Integration runtime. |
| Description | De beschrijving van uw Azure SSIS Integration runtime. |

  
### <a name="status-per-node"></a>Status (per knoop punt)

| Status | Beschrijving |
| ------ | ----------- | 
| Starten | Dit knoop punt wordt voor bereid. |
| Beschikbaar | Met dit knoop punt kunt u SSIS-pakketten implementeren en uitvoeren. |
| Recycl | Dit knoop punt wordt hersteld/opnieuw gestart. |
| Niet beschikbaar | Dit knoop punt is niet gereed voor het implementeren/uitvoeren van SSIS-pakketten en heeft actie fouten/problemen die kunnen worden opgelost. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Status (algehele Azure-SSIS Integration runtime)

| Algemene status | Description | 
| -------------- | ----------- | 
| Eerste | De knoop punten van uw Azure SSIS Integration runtime zijn niet toegewezen/voor bereid. | 
| Starten | De knoop punten van uw Azure SSIS Integration runtime worden toegewezen/voor bereid en de facturering is gestart. |
| Gestart | De knoop punten van uw Azure SSIS Integration runtime zijn toegewezen/voor bereid en ze zijn klaar om SSIS-pakketten te implementeren en uit te voeren. |
| Stoppen  | De knoop punten van uw Azure SSIS Integration runtime worden vrijgegeven. |
| Gestopt | De knoop punten van uw Azure SSIS Integration runtime zijn vrijgegeven en de facturering is gestopt. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-the-azure-portal"></a>De Azure SSIS Integration runtime bewaken in de Azure Portal

In de volgende scherm afbeeldingen ziet u hoe u de Azure-SSIS IR selecteert die u wilt bewaken en een voor beeld krijgt van de informatie die wordt weer gegeven.

![Selecteer de Azure SSIS Integration runtime die u wilt bewaken](media/monitor-integration-runtime/monitor-azure-ssis-ir-image1.png)

![Informatie weer geven over de Azure-SSIS Integration runtime](media/monitor-integration-runtime/monitor-azure-ssis-ir-image2.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-powershell"></a>De Azure SSIS Integration runtime controleren met Power shell

Gebruik een script zoals het volgende voor beeld om de status van de Azure-SSIS IR te controleren.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Meer informatie over de Azure-SSIS Integration runtime

Raadpleeg de volgende artikelen voor meer informatie over Azure-SSIS Integration runtime:

- [Azure-SSIS Integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Dit artikel bevat conceptuele informatie over integratie-Runtimes in het algemeen, met inbegrip van de Azure-SSIS IR. 
- [Zelfstudie: SSIS-pakketten implementeren in Azure](tutorial-create-azure-ssis-runtime-portal.md). In dit artikel vindt u stapsgewijze instructies voor het maken van een Azure-SSIS IR en het gebruik van SQL Database voor het hosten van de SSIS-catalogus. 
- [Procedure: Een Azure SSIS Integration Runtime maken](create-azure-ssis-integration-runtime.md). In dit artikel wordt de zelf studie uitgebreid en vindt u instructies voor het gebruik van SQL Managed instance en het toevoegen van de IR aan een virtueel netwerk. 
- [Een Azure-SSIS IR beheren](manage-azure-ssis-integration-runtime.md). In dit artikel leest u hoe u een Azure-SSIS IR stopt, start of verwijdert. Er wordt ook uitgelegd hoe u een Azure-SSIS IR kunt uitschalen door meer knooppunten toe te voegen aan de IR. 
- [Een Azure-SSIS-integratieruntime toevoegen aan een virtueel netwerk](join-azure-ssis-integration-runtime-virtual-network.md). Dit artikel bevat algemene informatie over het toevoegen van een Azure-SSIS IR aan een virtueel netwerk van Azure. Het bevat ook stappen om Azure Portal te gebruiken voor het configureren van het virtuele netwerk, zodat de Azure-SSIS IR kan worden toegevoegd aan het virtuele netwerk. 

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor het bewaken van pijp lijnen op verschillende manieren: 

- [Snelstartgids: een Data Factory maken](quickstart-create-data-factory-dot-net.md).
- [Azure Monitor gebruiken om Data Factory pijp lijnen te bewaken](monitor-using-azure-monitor.md)
