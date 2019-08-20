---
title: Bewaken van integratieruntime in Azure Data Factory | Microsoft Docs
description: Informatie over het bewaken van verschillende typen integratieruntime in Azure Data Factory.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 07/25/2018
author: gauravmalhot
ms.author: gamal
manager: craigg
ms.openlocfilehash: b62cbe75730da8c5764839d41887deb7e6cd0e90
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/13/2019
ms.locfileid: "66122624"
---
# <a name="monitor-an-integration-runtime-in-azure-data-factory"></a>Een integratieruntime in Azure Data Factory controleren  
**Integratieruntime** is de rekeninfrastructuur die door Azure Data Factory wordt gebruikt voor verschillende mogelijkheden voor gegevensintegratie in verschillende netwerkomgevingen. Er zijn drie typen integration runtime die worden aangeboden door Data Factory:

- Azure Integration Runtime
- Zelf-hostende Integration Runtime
- Azure-SSIS-integratie-runtime

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Als u de status van een instantie van integratieruntime (IR), moet u de volgende PowerShell-opdracht uitvoeren: 

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName MyDataFactory -ResourceGroupName MyResourceGroup -Name MyAzureIR -Status
``` 

De cmdlet retourneert verschillende gegevens voor verschillende typen integratieruntime. In dit artikel wordt uitgelegd voor de eigenschappen en -statussen voor elk type van integratieruntime.  

## <a name="azure-integration-runtime"></a>Azure Integration Runtime
De compute-resource voor een Azure integratieruntime is volledig beheerde, Elastisch in Azure. De volgende tabel bevat beschrijvingen van eigenschappen die zijn geretourneerd door de **Get-AzDataFactoryV2IntegrationRuntime** opdracht:

### <a name="properties"></a>Properties
De volgende tabel bevat beschrijvingen van de eigenschappen die worden geretourneerd door de cmdlet voor een Azure integratieruntime:

| Eigenschap | Description |
-------- | ------------- | 
| Name | De naam van de Azure integratieruntime. |  
| State | De status van de Azure integratieruntime. | 
| Location | Locatie van de Azure integratieruntime. Zie voor meer informatie over de locatie van een Azure integratieruntime [Inleiding tot integratieruntime](concepts-integration-runtime.md). |
| DataFactoryName | De naam van de data factory die deel uitmaakt van de Azure integratieruntime op. | 
| ResourceGroupName | De naam van de resourcegroep die de data factory behoort.  |
| Description | Beschrijving van de integratieruntime.  |

### <a name="status"></a>State
De volgende tabel bevat de mogelijke statussen van een Azure integratieruntime:

| State | Opmerkingen /-scenario 's | 
| ------ | ------------------ |
| Online | De Azure integratieruntime is online en klaar om te worden gebruikt. | 
| Offline | De Azure integratieruntime is offline vanwege een interne fout. |

## <a name="self-hosted-integration-runtime"></a>Zelf-hostende Integration Runtime
In deze sectie bevat beschrijvingen van eigenschappen die door de cmdlet Get-AzDataFactoryV2IntegrationRuntime geretourneerd. 

> [!NOTE] 
> De geretourneerde eigenschappen en de status bevatten informatie over de algehele zelf-hostende integratieruntime en op elk knooppunt in de runtime.  

### <a name="properties"></a>Properties

De volgende tabel bevat beschrijvingen van de eigenschappen voor bewaking **elk knooppunt**:

| Eigenschap | Description | 
| -------- | ----------- | 
| Name | De naam van de zelf-hostende integratieruntime en de knooppunten die zijn gekoppeld. Knooppunt is een on-premises Windows-computer met de zelf-hostende integratieruntime is geïnstalleerd. |  
| State | De status van de algehele zelf-hostende integratieruntime en elk knooppunt. Voorbeeld: Online/Offline/Limited/etc. Zie voor meer informatie over deze statussen van de volgende sectie. | 
| Versie | De versie van de zelf-hostende integratieruntime en elk knooppunt. De versie van de zelf-hostende integratieruntime wordt bepaald op basis van de versie van het merendeel van de knooppunten in de groep. Als er knooppunten met verschillende versies in de installatie van de zelf-hostende integration runtime zijn, alleen de knooppunten met het versienummer hetzelfde als de logische zelf-hostende integratie-runtime-functie goed. Anderen in de beperkte modus zijn en moeten handmatig worden bijgewerkt (alleen als automatisch bijwerken is mislukt). | 
| Beschikbaar geheugen | Beschikbaar geheugen op een zelf-hostende integration runtime-knooppunt. Deze waarde is een momentopname van een bijna realtime. | 
| CPU-gebruik | CPU-gebruik van een zelf-hostende integration runtime-knooppunt. Deze waarde is een momentopname van een bijna realtime. |
| Netwerken (In/uit) | Het netwerkgebruik van een zelf-hostende integration runtime-knooppunt. Deze waarde is een momentopname van een bijna realtime. | 
| Gelijktijdige taken (actief / beperken) | **Met**. Het aantal taken of taken die worden uitgevoerd op elk knooppunt. Deze waarde is een momentopname van een bijna realtime. <br/><br/>**Limiet**. Limiet geeft aan dat het maximum aantal gelijktijdige taken voor elk knooppunt. Deze waarde is gedefinieerd op basis van de machinegrootte. U kunt de limiet voor het opschalen van gelijktijdige taakuitvoering in geavanceerde scenario's wanneer activiteiten zijn time-outs, zelfs wanneer CPU, geheugen of netwerk benut wordt verhogen. Deze mogelijkheid is ook beschikbaar met een zelf-hostende integratieruntime met één knooppunt. |
| Rol | Er zijn twee soorten rollen in een meerdere knooppunten zelf-hostende integratieruntime-functie voor berichtverzending en -werkrollen. Alle knooppunten zijn werknemers, wat betekent dat ze kunnen allemaal worden gebruikt voor het uitvoeren van taken. Er is slechts één functie voor berichtverzending knooppunt, die wordt gebruikt voor het ophalen van taken/taken uit cloudservices en ze verzenden naar verschillende worker-knooppunten. Het knooppunt verzender is ook een worker-knooppunt. |

Sommige instellingen van de eigenschappen ook beter wanneer er twee of meer knooppunten in de zelf-hostende integratieruntime (dat wil zeggen, in een scale-out scenario).

#### <a name="concurrent-jobs-limit"></a>Limiet voor gelijktijdige taken

De standaardwaarde van de gelijktijdige taken limiet is ingesteld, is afhankelijk van de grootte van de machine. De factoren die wordt gebruikt om deze waarde te berekenen, is afhankelijk van de hoeveelheid RAM-geheugen en het aantal CPU-kernen van de machine. Dus meer kernen en meer geheugen, de hogere beperken de standaardwaarde van gelijktijdige taken.

U schalen opwaarts door het aantal knooppunten te verhogen. Als u het aantal knooppunten verhoogt, is de limiet voor gelijktijdige taken de som van de waarden van de limiet voor gelijktijdige taak van alle beschikbare knooppunten.  Bijvoorbeeld, als één knooppunt u maximaal twaalf gelijktijdige taken uitvoeren kunt, kunt vervolgens drie meer lijken knooppunten toe te voegen u maximaal 48 gelijktijdige taken (dat wil zeggen, 4 x 12) worden uitgevoerd. Het is raadzaam om de limiet voor gelijktijdige taken te verhogen, alleen wanneer er brongebruik laag met de standaardwaarden op elk knooppunt.

U kunt de waarde in de Azure-portal berekende standaard overschrijven. Selecteer Auteur > verbindingen > Integratieruntimes > Bewerken > knooppunten > gelijktijdige taak waarde per knooppunt wijzigen. U kunt ook de PowerShell [update Azdatafactoryv2integrationruntimenode](https://docs.microsoft.com/powershell/module/az.datafactory/update-Azdatafactoryv2integrationruntimenode#examples) opdracht.
  
### <a name="status-per-node"></a>Status (per knooppunt)
De volgende tabel bevat de mogelijke statussen van een zelf-hostende integration runtime-knooppunt:

| State | Description |
| ------ | ------------------ | 
| Online | Knooppunt is verbonden met de Data Factory-service. |
| Offline | Knooppunt is offline. |
| Upgraden | Het knooppunt wordt automatisch bijgewerkt. |
| Beperkt | Vanwege een probleem met de netwerkverbinding. Kan worden veroorzaakt door HTTP-poort 8050 probleem, service bus-connectiviteitsprobleem of een synchronisatieprobleem in de referentie. |
| Inactief | Er is een knooppunt in een van de configuratie van andere knooppunten van de meeste andere configuratie. |

Een knooppunt mag inactief zijn als er geen verbinding met andere knooppunten.

### <a name="status-overall-self-hosted-integration-runtime"></a>Status (algemene zelf-hostende integratieruntime)
De volgende tabel bevat de mogelijke statussen van een zelf-hostende integratieruntime. Deze status is afhankelijk van de status van alle knooppunten die deel uitmaken van de runtime. 

| State | Description |
| ------ | ----------- | 
| Registratie moet | Kan geen knooppunt voor is geregistreerd bij deze zelf-hostende integratieruntime nog. |
| Online | Alle knooppunten zijn online. |
| Offline | Kan geen knooppunt is online. |
| Beperkt | Niet alle knooppunten in deze zelf-hostende integratieruntime zijn in een foutloze toestand bevindt. Deze status is een waarschuwing weergegeven dat sommige knooppunten is mogelijk niet beschikbaar. Deze status kan worden veroorzaakt door een synchronisatieprobleem in de referentie op knooppunt verzender/werknemer. |

Gebruik de **Get-AzDataFactoryV2IntegrationRuntimeMetric** cmdlet voor het ophalen van de JSON-nettolading met de gedetailleerde zelf-hostende integration runtime-eigenschappen en hun momentopname waarden tijdens het ophalen van de uitvoering van de cmdlet.

```powershell
Get-AzDataFactoryV2IntegrationRuntimeMetric -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName  | | ConvertTo-Json 
```

Voorbeeld van uitvoer (wordt ervan uitgegaan dat er twee knooppunten die zijn gekoppeld aan deze zelf-hostende integratieruntime):

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
Azure-SSIS integratieruntime is een volledig beheerd cluster van Azure virtuele machines (of knooppunten) toegewezen voor het uitvoeren van uw SSIS-pakketten. Alle andere activiteiten van Azure Data Factory kan niet worden uitgevoerd. Eenmaal ingericht, kunt u query uitvoeren op de eigenschappen en de algehele/knooppunt-specifieke statussen bewaken.

### <a name="properties"></a>Properties

| De eigenschap/Status | Description |
| --------------- | ----------- |
| CreateTime | De UTC-tijd waarop uw Azure-SSIS integratieruntime is gemaakt. |
| Knooppunten | De toegewezen/beschikbare knooppunten van uw Azure-SSIS integratieruntime met knooppunt-specifieke statussen (begin/beschikbaar/recycling/niet beschikbaar) en bruikbare fouten. |
| OtherErrors | De niet-knooppunt-specifieke bruikbare fouten in uw Azure-SSIS integratieruntime. |
| LastOperation | Het resultaat van de laatste starten/stoppen-bewerking op uw Azure-SSIS integratieruntime met bruikbare fouten als deze is mislukt. |
| State | De algemene status (eerste/starten/gestart/stoppen/gestopt) van uw Azure-SSIS integratieruntime. |
| Location | De locatie van uw Azure-SSIS integratieruntime. |
| NodeSize | De grootte van elk knooppunt van uw Azure-SSIS integratieruntime. |
| NodeCount | Het aantal knooppunten in uw Azure-SSIS integratieruntime. |
| MaxParallelExecutionsPerNode | Het aantal parallelle uitvoeringen per knooppunt in uw Azure-SSIS integratieruntime. |
| CatalogServerEndpoint | Het eindpunt van uw bestaande Azure SQL Database/MI-server host SSISDB. |
| CatalogAdminUserName | De beheerdersgebruikersnaam van uw bestaande Azure SQL Database/MI-server. Data Factory-service gebruikt deze informatie voor het voorbereiden en SSISDB beheren namens. |
| CatalogAdminPassword | Het beheerderswachtwoord van uw bestaande Azure SQL Database/MI-server. |
| CatalogPricingTier | De prijscategorie voor de SSISDB wordt gehost door uw bestaande Azure SQL Database-server.  Niet van toepassing op Azure SQL Database Managed Instance SSISDB wordt gehost. |
| VNetId | Het virtuele netwerk resource-ID voor uw Azure-SSIS integratieruntime om toe te voegen. |
| Subnet | De naam van het subnet voor uw Azure-SSIS integratieruntime om toe te voegen. |
| Id | De resource-ID van uw Azure-SSIS integratieruntime. |
| Type | Het type (beheerde/zelf-Hosted) van uw Azure-SSIS integratieruntime. |
| ResourceGroupName | De naam van uw Azure-resourcegroep, waarin uw data factory en Azure-SSIS integratieruntime zijn gemaakt. |
| DataFactoryName | De naam van uw Azure-gegevensfactory. |
| Name | De naam van uw Azure-SSIS integratieruntime. |
| Description | De beschrijving van uw Azure-SSIS integratieruntime. |

  
### <a name="status-per-node"></a>Status (per knooppunt)

| State | Description |
| ------ | ----------- | 
| Starten | Dit knooppunt wordt voorbereid. |
| Beschikbaar | Dit knooppunt is gereed voor u SSIS-pakketten implementeren/uitvoeren. |
| Recyclen | Dit knooppunt is wordt hersteld/opnieuw te starten. |
| Niet beschikbaar | Dit knooppunt is niet gereed voor u SSIS-pakketten implementeren/uitvoeren en heeft praktische fouten/problemen die kunnen worden opgelost. |

### <a name="status-overall-azure-ssis-integration-runtime"></a>Status (algemene Azure-SSIS integratieruntime)

| Algemene status | Description | 
| -------------- | ----------- | 
| Oorspronkelijk | De knooppunten van uw Azure-SSIS integratieruntime zijn niet toegewezen/voorbereid. | 
| Starten | De knooppunten van uw Azure-SSIS integratieruntime worden toegewezen/voorbereid en facturering is gestart. |
| Gestart | De knooppunten van uw Azure-SSIS integratieruntime zijn toegewezen/voorbereid en ze gereed zijn voor u SSIS-pakketten implementeren/uitvoeren. |
| Stoppen  | De knooppunten van uw Azure-SSIS integratieruntime zijn gepubliceerd. |
| Gestopt | De knooppunten van uw Azure-SSIS integratieruntime zijn vrijgegeven en facturering is gestopt. |

### <a name="monitor-the-azure-ssis-integration-runtime-in-the-azure-portal"></a>Monitor voor de Azure-SSIS integratieruntime in Azure portal

De volgende schermafbeeldingen laten zien hoe om te selecteren van de Azure-SSIS IR om te controleren, en geef een voorbeeld van de informatie die wordt weergegeven.

![Selecteer de Azure-SSIS integratieruntime bewaken](media/monitor-integration-runtime/monitor-azure-ssis-ir-image1.png)

![Informatie weergeven over de Azure-SSIS integratieruntime](media/monitor-integration-runtime/monitor-azure-ssis-ir-image2.png)

### <a name="monitor-the-azure-ssis-integration-runtime-with-powershell"></a>Monitor voor de Azure-SSIS integratieruntime met PowerShell

Gebruik van een script, zoals in het volgende voorbeeld om te controleren of de status van de Azure-SSIS-IR.

```powershell
Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -Name $AzureSSISName -ResourceGroupName $ResourceGroupName -Status
```

### <a name="more-info-about-the-azure-ssis-integration-runtime"></a>Meer informatie over de Azure-SSIS integratieruntime

Zie de volgende artikelen voor meer informatie over Azure-SSIS integratieruntime:

- [Azure-SSIS Integratieruntime](concepts-integration-runtime.md#azure-ssis-integration-runtime). In dit artikel bevat algemene informatie over integratieruntimes in het algemeen met inbegrip van de Azure-SSIS-IR. 
- [Zelfstudie: SSIS-pakketten implementeren in Azure](tutorial-create-azure-ssis-runtime-portal.md). Dit artikel biedt stapsgewijze instructies voor het maken van een Azure-SSIS IR en maakt gebruik van een Azure SQL-database voor het hosten van de SSIS-catalogus. 
- [Procedure: Een Azure-SSIS integratieruntime maken](create-azure-ssis-integration-runtime.md). In dit artikel gaat verder in op de zelfstudie en bevat instructies over het gebruik van Azure SQL Database Managed Instance en toevoegen van de IR aan een virtueel netwerk. 
- [Een Azure-SSIS IR beheren](manage-azure-ssis-integration-runtime.md). In dit artikel leest u hoe u een Azure-SSIS IR stopt, start of verwijdert. Er wordt ook uitgelegd hoe u een Azure-SSIS IR kunt uitschalen door meer knooppunten toe te voegen aan de IR. 
- [Een Azure-SSIS-integratieruntime toevoegen aan een virtueel netwerk](join-azure-ssis-integration-runtime-virtual-network.md). Dit artikel bevat algemene informatie over het toevoegen van een Azure-SSIS IR aan een virtueel netwerk van Azure. Het bevat ook stappen voor het gebruik van Azure portal het virtuele netwerk zodanig configureren dat de Azure-SSIS IR kunt deelnemen aan het virtuele netwerk. 

## <a name="next-steps"></a>Volgende stappen
Zie de volgende artikelen voor het bewaken van pijplijnen op verschillende manieren: 

- [Snelstartgids: een data factory maken](quickstart-create-data-factory-dot-net.md).
- [Azure Monitor gebruiken om Data Factory-pijplijnen te bewaken](monitor-using-azure-monitor.md)
