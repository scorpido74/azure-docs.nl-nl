---
title: Runtime van integratie controleren in Azure Data Factory
description: Meer informatie over het controleren van verschillende typen integratieruntime in Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/25/2018
author: djpmsft
ms.author: daperlov
manager: anandsub
ms.openlocfilehash: 6d2ea5c0b7354867086fc0cce43732f2d73c53ab
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81398953"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Een IR bewaken in Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]
  
**Integratieruntime** is de rekeninfrastructuur die wordt gebruikt door Azure Data Factory om verschillende mogelijkheden voor gegevensintegratie te bieden in verschillende netwerkomgevingen. Er zijn drie soorten integratie-runtimes aangeboden door Data Factory:

- Azure Integration Runtime
- Zelf-hostende Integration Runtime
- Azure-SSIS-integratie-runtime

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voer de volgende PowerShell-opdracht uit om de status van een instantie van inburgeringsruntime (IR) op te halen: 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

De cmdlet retourneert verschillende informatie voor verschillende typen integratieruntime. In dit artikel worden de eigenschappen en statussen voor elk type nabewerking van integratie uitgelegd.  

## <a name="azure-integration-runtime"></a>Azure Integration Runtime
De rekenbron voor een Azure-integratieruntime wordt volledig elastisch beheerd in Azure. In de volgende tabel vindt u beschrijvingen voor eigenschappen die worden geretourneerd door de opdracht **Get-AzDataFactoryV2IntegrationRuntime:**

### <a name="properties"></a>Eigenschappen
In de volgende tabel vindt u beschrijvingen van eigenschappen die door de cmdlet worden geretourneerd voor een runtime voor Azure-integratie:

| Eigenschap | Beschrijving |
-------- | ------------- | 
| Naam | Naam van de runtime van Azure-integratie. |  
| Status | Status van de runtime van Azure-integratie. | 
| Locatie | Locatie van de runtime van azure-integratie. Zie [Inleiding tot de runtime van integratie voor](concepts-integration-runtime.md)meer informatie over de locatie van een runtime voor Azure-integratie. |
| DataFactoryName | Naam van de gegevensfabriek waartoe de runtime van azure-integratie behoort. | 
| ResourceGroupName | Naam van de resourcegroep waartoe de gegevensfabriek behoort.  |
| Beschrijving | Beschrijving van de inburgeringsruntime.  |

### <a name="status"></a>Status
In de volgende tabel worden mogelijke statussen van een runtime voor Azure-integratie gegeven:

| Status | Opmerkingen/scenario's | 
| ------ | ------------------ |
| Online | De runtime voor Azure-integratie is online en klaar voor gebruik. | 
| Offline | De runtime van azure-integratie is offline vanwege een interne fout. |

## <a name="self-hosted-integration-runtime"></a>Zelf-hostende Integration Runtime
In deze sectie vindt u beschrijvingen voor eigenschappen die worden geretourneerd door de cmdlet Get-AzDataFactoryV2IntegrationRuntime. 

> [!NOTE] 
> De geretourneerde eigenschappen en status bevatten informatie over de algehele zelfgehoste nawerking van de integratie en elk knooppunt in de runtime.  

### <a name="properties"></a>Eigenschappen

In de volgende tabel vindt u beschrijvingen van bewakingseigenschappen voor **elk knooppunt:**

| Eigenschap | Beschrijving | 
| -------- | ----------- | 
| Naam | Naam van de zelf gehoste runtime voor integratie en knooppunten die eraan zijn gekoppeld. Node is een on-premises Windows-machine die de zelf gehoste integratieruntime heeft geïnstalleerd. |  
| Status | De status van de algehele zelfgehoste runtime voor integratie en elk knooppunt. Voorbeeld: Online/Offline/Limited/etc. Zie de volgende sectie voor informatie over deze statussen. | 
| Versie | De versie van zelf gehoste ingebruikloop van integratie en elk knooppunt. De versie van de zelfgehoste runtime voor integratie wordt bepaald op basis van de versie van de meeste knooppunten in de groep. Als er knooppunten zijn met verschillende versies in de zelfgehoste instelling voor de runtime van de integratie, werken alleen de knooppunten met hetzelfde versienummer als de logische zelfgehoste runtime voor integratie naar behoren. Anderen bevinden zich in de beperkte modus en moeten handmatig worden bijgewerkt (alleen voor het geval de automatische update mislukt). | 
| Beschikbaar geheugen | Beschikbaar geheugen op een zelf gehoste runtime-knooppunt voor integratie. Deze waarde is een bijna realtime momentopname. | 
| CPU-gebruik | CPU-gebruik van een zelf gehoste runtime-knooppunt voor integratie. Deze waarde is een bijna realtime momentopname. |
| Netwerken (In/Uit) | Netwerkgebruik van een zelf gehost e-segmentering voor integratie. Deze waarde is een bijna realtime momentopname. | 
| Gelijktijdige taken (lopend/limiet) | **Hardlopen**. Aantal taken of taken die op elk knooppunt worden uitgevoerd. Deze waarde is een bijna realtime momentopname. <br/><br/>**Limiet**. Limit betekent de maximale gelijktijdige taken voor elk knooppunt. Deze waarde wordt gedefinieerd op basis van de grootte van de machine. U de limiet verhogen om gelijktijdige taakuitvoering op te schalen in geavanceerde scenario's, wanneer activiteiten worden getimed, zelfs wanneer cpu, geheugen of netwerk onderbenut is. Deze mogelijkheid is ook beschikbaar met een self-hosted integratieruntime met één knooppunt. |
| Rol | Er zijn twee soorten rollen in een self-hosted integratieruntime met meerdere nodes : dispatcher en werknemer. Alle knooppunten zijn werknemers, wat betekent dat ze allemaal kunnen worden gebruikt om taken uit te voeren. Er is slechts één dispatcherknooppunt, dat wordt gebruikt om taken/taken uit cloudservices te halen en deze naar verschillende werknemersknooppunten te verzenden. Het verzendknooppunt is ook een werknemersknooppunt. |

Sommige instellingen van de eigenschappen zijn logischer wanneer er twee of meer knooppunten in de zelfgehoste runtime voor integratie zijn (dat wil zeggen in een uitschaingsscenario).

#### <a name="concurrent-jobs-limit"></a>Limiet voor gelijktijdige taken

De standaardwaarde van de limiet voor gelijktijdige taken wordt ingesteld op basis van de grootte van de machine. De factoren die worden gebruikt om deze waarde te berekenen, zijn afhankelijk van de hoeveelheid RAM en het aantal CPU-cores van de machine. Dus hoe meer cores en hoe meer geheugen, hoe hoger de standaardlimiet van gelijktijdige taken.

U schaalt uit door het aantal knooppunten te verhogen. Wanneer u het aantal knooppunten verhoogt, is de limiet voor gelijktijdige taken de som van de gelijktijdige taaklimietwaarden van alle beschikbare knooppunten.  Als u bijvoorbeeld met één knooppunt maximaal twaalf gelijktijdige taken uitvoert, u met drie vergelijkbare knooppunten maximaal 48 gelijktijdige taken uitvoeren (dat wil zeggen 4 x 12). We raden u aan de limiet voor gelijktijdige taken alleen te verhogen wanneer u een laag resourcegebruik ziet met de standaardwaarden op elk knooppunt.

U de berekende standaardwaarde in de Azure-portal overschrijven. Selecteer Runtimes voor > verbindingen > integratie > > knooppunten bewerken > gelijktijdige taakwaarde per knooppunt wijzigen. U ook de opdracht [PowerShell-update-Azdatafactoryv2integrationruntimenode](https://docs.microsoft.com/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) gebruiken.
  
### <a name="status-per-node"></a>Status (per knooppunt)
De volgende tabel biedt mogelijke statussen van een zelf gehoste runtime-knooppunt voor integratie:

| Status | Beschrijving |
| ------ | ------------------ | 
| Online | Node is verbonden met de Data Factory-service. |
| Offline | Knooppunt is offline. |
| Upgraden | Het knooppunt wordt automatisch bijgewerkt. |
| Beperkt | Vanwege een verbindingsprobleem. Mogelijk als gevolg van http-poort 8050 probleem, service bus connectiviteit probleem, of een referentie synchronisatie probleem. |
| Niet-actief | Node is in een andere configuratie dan de configuratie van andere meerderheidsknooppunten. |

Een knooppunt kan inactief zijn wanneer het geen verbinding kan maken met andere knooppunten.

### <a name="status-overall-self-hosted-integration-runtime"></a>Status (algehele self-hosted integratie runtime)
De volgende tabel biedt mogelijke statussen van een zelf gehoste runtime voor integratie. Deze status is afhankelijk van statussen van alle knooppunten die tot de runtime behoren. 

| Status | Beschrijving |
| ------ | ----------- | 
| Registratie nodig | Er is nog geen knooppunt geregistreerd op deze zelfgehoste inloop van integratie. |
| Online | Alle knooppunten zijn online. |
| Offline | Er is geen knooppunt online. |
| Beperkt | Niet alle knooppunten in deze zelf gehoste runtime voor integratie zijn in een gezonde staat. Deze status is een waarschuwing dat sommige knooppunten mogelijk zijn uitgeschakeld. Deze status kan te wijten zijn aan een probleem met het synchroniseren van referenties op het verzender-/werknemersknooppunt. |

Gebruik de **cmdlet Get-AzDataFactoryV2IntegrationRuntimeMetric** om de JSON-payload op te halen met de gedetailleerde zelfgehoste eigenschappen voor de uitvoering van de cmdlet en hun momentopnamewaarden tijdens de uitvoering van de cmdlet.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName | ConvertTo-Json 
```

Voorbeelduitvoer (ervan uitgaat dat er twee knooppunten zijn gekoppeld aan deze zelf gehoste runtime voor integratie):

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
Runtime azure-SSIS-integratie is een volledig beheerde cluster van Virtuele Azure-machines (of knooppunten) die zijn gewijd aan het uitvoeren van uw SSIS-pakketten. Er worden geen andere activiteiten van Azure Data Factory uitgevoerd. Zodra u is ingericht, u de eigenschappen ervan opvragen en de algemene/node-specifieke statussen controleren.

### <a name="properties"></a>Eigenschappen

| Eigenschap/status | Beschrijving |
| --------------- | ----------- |
| Maaktijd | De UTC-tijd waarop de runtime van uw Azure-SSIS-integratie is gemaakt. |
| Knooppunten | De toegewezen/beschikbare knooppunten van de runtime van uw Azure-SSIS-integratie met node-specifieke statussen (start/beschikbaar/recycling/niet beschikbaar) en bruikbare fouten. |
| Overige fouten | De niet-node-specifieke bruikbare fouten op uw Azure-SSIS-integratieruntime. |
| Laatste operatie | Het resultaat van de laatste start/stop-bewerking op de runtime van uw Azure-SSIS-integratie met bruikbare fouten(en) als deze is mislukt. |
| Status | De algehele status (initieel/begin/start/stoppen/gestopt) van uw azure-SSIS-integratieruntime. |
| Locatie | De locatie van de runtime van uw Azure-SSIS-integratie. |
| NodeSize | De grootte van elk knooppunt van de runtime van uw Azure-SSIS-integratie. |
| NodeCount | Het aantal knooppunten in de runtime van uw Azure-SSIS-integratie. |
| MaxParallelExecutionsPerNode | Het aantal parallelle uitvoeringen per knooppunt in de runtime van uw Azure-SSIS-integratie. |
| CatalogServerEndpoint | Het eindpunt van uw bestaande Azure SQL Database/Managed Instance-server om SSISDB te hosten. |
| CatalogusadminGebruikersnaam | De beheerdersgebruikersnaam van uw bestaande Azure SQL Database/Managed Instance-server. Data Factory-service gebruikt deze informatie om SSISDB namens u voor te bereiden en te beheren. |
| CatalogusAdminPassword | Het beheerderswachtwoord van uw bestaande Azure SQL Database/Managed Instance-server. |
| Catalogusprijstier | De prijscategorie voor SSISDB wordt gehost door uw bestaande Azure SQL Database-server.  Niet van toepassing op Azure SQL Database Managed Instance hosting SSISDB. |
| Vnetid (Vnetid) | De virtuele netwerkbron-id voor de runtime van uw Azure-SSIS-integratie om lid te worden. |
| Subnet | De subnetnaam voor de runtime van uw Azure-SSIS-integratie om lid te worden. |
| Id | De resource-id van de runtime van uw Azure-SSIS-integratie. |
| Type | Het type (Managed/Self-Hosted) van de runtime van uw Azure-SSIS-integratie. |
| ResourceGroupName | De naam van uw Azure Resource Group, waarin de runtime van uw gegevensfabriek en Azure-SSIS-integratie zijn gemaakt. |
| DataFactoryName | De naam van uw Azure-gegevensfabriek. |
| Naam | De naam van de runtime van uw Azure-SSIS-integratie. |
| Beschrijving | De beschrijving van de runtime van uw Azure-SSIS-integratie. |

  
### <a name="status-per-node"></a>Status (per knooppunt)

| Status | Beschrijving |
| ------ | ----------- | 
| Starten | Dit knooppunt wordt voorbereid. |
| Beschikbaar | Dit knooppunt is klaar voor u om SSIS-pakketten te implementeren/uitvoeren. |
| Recycling | Dit knooppunt wordt gerepareerd/opnieuw opgestart. |
| Niet beschikbaar | Dit knooppunt is niet klaar voor u om SSIS-pakketten te implementeren/uit te voeren en bevat bruikbare fouten/problemen die u oplossen. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Status (totale runtime azure-SSIS-integratie)

| Algemene status | Beschrijving | 
| -------------- | ----------- | 
| Eerste | De knooppunten van uw azure-SSIS-integratieruntime zijn niet toegewezen/voorbereid. | 
| Starten | De knooppunten van uw Azure-SSIS-integratieruntime worden toegewezen/voorbereid en facturering is gestart. |
| Gestart | De knooppunten van uw Azure-SSIS-integratieruntime zijn toegewezen/voorbereid en ze zijn klaar voor u om SSIS-pakketten te implementeren/uitvoeren. |
| Stoppen  | De knooppunten van uw Azure-SSIS-integratieruntime worden vrijgegeven. |
| Gestopt | De knooppunten van de runtime van uw Azure-SSIS-integratie zijn vrijgegeven en facturering is gestopt. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-the-azure-portal"></a>De runtime van Azure-SSIS-integratie in de Azure-portal bewaken

In de volgende schermafbeeldingen ziet u hoe u de Azure-SSIS IR selecteert die u wilt controleren en een voorbeeld geven van de weergegeven informatie.

![Selecteer de runtime van Azure-SSIS-integratie om te controleren](media/monitor-integration-runtime/monitor-azure-ssis-ir-image1.png)

![Informatie weergeven over de runtime van Azure-SSIS-integratie](media/monitor-integration-runtime/monitor-azure-ssis-ir-image2.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-powershell"></a>De runtime van Azure-SSIS-integratie met PowerShell bewaken

Gebruik een script zoals het volgende voorbeeld om de status van Azure-SSIS IR te controleren.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Meer informatie over de runtime van Azure-SSIS-integratie

Zie de volgende artikelen voor meer informatie over de runtime van Azure-SSIS-integratie:

- [Runtime azure-SSIS-integratie](concepts-integration-runtime.md#azure-ssis-integration-runtime). In dit artikel vindt u conceptuele informatie over de runtimes voor integratie in het algemeen, waaronder azure-SSIS IR. 
- [Zelfstudie: SSIS-pakketten implementeren in Azure](tutorial-create-azure-ssis-runtime-portal.md). Dit artikel biedt stapsgewijze instructies voor het maken van een Azure-SSIS IR en maakt gebruik van een Azure SQL-database voor het hosten van de SSIS-catalogus. 
- [Procedure: Een Azure SSIS Integration Runtime maken](create-azure-ssis-integration-runtime.md). In dit artikel wordt de zelfstudie uitgebreid en worden instructies gegeven over het gebruik van Azure SQL Database Managed Instance en het samenvoegen van de IR bij een virtueel netwerk. 
- [Een Azure-SSIS IR beheren](manage-azure-ssis-integration-runtime.md). In dit artikel leest u hoe u een Azure-SSIS IR stopt, start of verwijdert. Er wordt ook uitgelegd hoe u een Azure-SSIS IR kunt uitschalen door meer knooppunten toe te voegen aan de IR. 
- [Een Azure-SSIS-integratieruntime toevoegen aan een virtueel netwerk](join-azure-ssis-integration-runtime-virtual-network.md). Dit artikel bevat algemene informatie over het toevoegen van een Azure-SSIS IR aan een virtueel netwerk van Azure. Het biedt ook stappen om Azure-portal te gebruiken om het virtuele netwerk te configureren, zodat azure-SSIS IR kan toetreden tot het virtuele netwerk. 

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor het monitoren van pijplijnen op verschillende manieren: 

- [Snelstart: maak een gegevensfabriek](quickstart-create-data-factory-dot-net.md).
- [Azure Monitor gebruiken om de pijplijnen van Gegevensfabriek te controleren](monitor-using-azure-monitor.md)
