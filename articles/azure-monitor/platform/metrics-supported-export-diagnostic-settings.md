---
title: Azure Monitor-platformstatistieken die kunnen worden geëxporteerd via diagnostische instellingen
description: Lijst met statistieken die beschikbaar zijn voor elk resourcetype met Azure Monitor.
services: azure-monitor
ms.topic: reference
ms.date: 02/10/2020
ms.subservice: metrics
ms.openlocfilehash: 7a75655d1707dd2491065974ed8addc4c2da1a6a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77661359"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Azure Monitor-platformstatistieken die kunnen worden geëxporteerd via diagnostische instellingen

Azure Monitor biedt [standaard platformstatistieken](data-platform-metrics.md) zonder configuratie. Het biedt verschillende manieren om te communiceren met platformstatistieken, waaronder het in kaart brengen ervan in de portal, toegang tot deze gegevens via de REST API of ze opvragen met PowerShell of CLI. Zie [statistieken die worden ondersteund](metrics-supported.md) voor een volledige lijst met platformstatistieken die momenteel beschikbaar zijn met de geconsolideerde metrische pijplijn van Azure Monitor. Gebruik de [api-versie 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)voor en toegang tot deze statistieken om deze statistieken te zoeken en te openen. Andere statistieken kunnen beschikbaar zijn in de portal of met behulp van oudere API's.

U de platformstatistieken vanuit de Azure-monitorpijplijn op twee manieren exporteren naar andere locaties.
1. [Diagnostische instellingen](diagnostic-settings.md) gebruiken om te verzenden naar Log Analytics, Event Hubs of Azure Storage.
2. De [REST API met statistieken gebruiken](https://docs.microsoft.com/rest/api/monitor/metrics/list)

Vanwege fijne kneepjes in de Azure Monitor-backend zijn niet alle statistieken exporteerbaar met diagnostische instellingen. De onderstaande tabel geeft een lijst die wel en niet kan worden geëxporteerd met diagnostische instellingen.

## <a name="change-to-behavior-for-nulls-and-zero-values"></a>Wijzigen in gedrag voor NULLs en Nulwaarden 
 
Voor de platformstatistieken die via diagnostische instellingen kunnen worden geëxporteerd, zijn er een paar statistieken waarvoor Azure Monitor '0s' interpreteert als 'Nulls'. Dit heeft enige verwarring veroorzaakt tussen echte '0s' (uitgezonden door resource) en geïnterpreteerd '0s' (Nulls). Vanaf **1 april 2020** worden platformstatistieken die via diagnostische instellingen worden geëxporteerd, niet langer '0s' geëxporteerd, tenzij ze echt zijn uitgestoten door de onderliggende bron. Opmerking:

1.  Als u een resourcegroep of een specifieke resource verwijdert, worden metrische gegevens uit de uitgevoerde resources niet langer verzonden naar exportbestemmingen voor diagnostische instellingen. Dat wil zeggen dat het niet meer wordt weergegeven in gebeurtenishubs, opslagaccounts en logboekanalysewerkruimten.
2.  Deze verbetering zal beschikbaar zijn in alle publieke en private clouds.
3.  Deze wijziging heeft geen invloed op het gedrag van een van de volgende ervaringen: 
   - Platformbronlogboeken geëxporteerd via diagnostische instellingen
   - Statistieken in grafiek en overzicht in Metrics Explorer
   - Waarschuwingen voor platformstatistieken
 
## <a name="metrics-exportable-table"></a>Tabel met uitvoerbare statistieken 

De tabel bevat de volgende kolommen. 
- Exporteerbaar via diagnostische instellingen? 
- Uitgevoerd door NULL / 0 
- ResourceType 
- Gegevens 
- MetricDisplayName
- Eenheid 
- AggregatieType


> [!NOTE]
> De onderstaande tabel kan een horizontale schuifbalk aan de onderkant hebben. Als u denkt dat u informatie mist, controleert u of de schuifbalk helemaal naar links staat.  


| Exporteerbaar via diagnostische instellingen?  | Zendt NULLs uit |  ResourceType  |  Gegevens  |  MetricDisplayName  |  Eenheid  |  AggregatieType | 
|---|---| ---- | ----- | ------ | ---- | ---- | 
| Ja****  | Nee |  Microsoft.AnalysisServices/servers  |  Schonerestroomprijs  |  Geheugen: Schonere huidige prijs  |  Count  |  Average | 
| Ja****  | Nee |  Microsoft.AnalysisServices/servers  |  CleanerMemoryNonshrinkable CleanerMemory  |  Geheugen: Schoner geheugen niet krimpend  |  Bytes  |  Average | 
| Ja****  | Nee |  Microsoft.AnalysisServices/servers  |  CleanerMemoryKrimpbaar  |  Geheugen: Schoner geheugen krimpbaar  |  Bytes  |  Average | 
| Ja****  | Nee |  Microsoft.AnalysisServices/servers  |  CommandPoolBusyThreads  |  Threads: Drukke threads voor het uitvoeren van opdrachten  |  Count  |  Average | 
| Ja****  | Nee |  Microsoft.AnalysisServices/servers  |  CommandPoolIdleThreads  |  Threads: Niet-actieve threads van de opdrachtgroep  |  Count  |  Average | 
| Ja****  | Nee |  Microsoft.AnalysisServices/servers  |  CommandPoolJobQueueLengte  |  Wachtrijlengte van taakgroep  |  Count  |  Average | 
| Ja****  | Nee |  Microsoft.AnalysisServices/servers  |  Huidige verbindingen  |  Verbinding: huidige verbindingen  |  Count  |  Average | 
| Ja****  | Nee |  Microsoft.AnalysisServices/servers  |  CurrentUserSessions  |  Huidige gebruikerssessies  |  Count  |  Average | 
| Ja****  | Nee |  Microsoft.AnalysisServices/servers  |  LongParsingBusyThreads  |  Threads: Lange ontwering drukke threads  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  LongParsingIdleThreads  |  Threads: Lange ontwering idle threads  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  LongParsingJobQueueLength  |  Threads: Lange ontwering taakwachtrijlengte  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  mashup_engine_memory_metric  |  M-enginegeheugen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  mashup_engine_private_bytes_metric  |  M Engine Private Bytes  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  mashup_engine_qpu_metric  |  M Engine QPU  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  mashup_engine_virtual_bytes_metric  |  Virtuele bytes van M Engine  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  memory_metric  |  Geheugen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  memory_thrashing_metric  |  Geheugenthrashing  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  MemoryLimitHard  |  Geheugen: Geheugenlimiet hard  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  MemoryLimitHigh  |  Geheugen: geheugenlimiet hoog  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  MemoryLimitLow  |  Geheugen: geheugenlimiet laag  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  MemoryLimitVertiPaq  |  Geheugen: Geheugenlimiet VertiPaq  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  Geheugengebruik  |  Geheugen: geheugengebruik  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  private_bytes_metric  |  Privébytes  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  ProcessingpoolBusyIOJobThreads  |  Threads: Verwerking van de gedrukke I/O-taakthreads  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  ProcessingpoolBusyNonIOThreads  |  Threads: Verwerking van druk niet-I/O-threads  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  VerwerkingPoolIdleIOJobThreads  |  Threads: I/O-taakthreads verwerken  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  VerwerkingPoolIdleNonIOThreads  |  Threads: Niet-I/O-threads verwerken  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  ProcessingpoolIOJobQueueLength  |  Threads: De wachtrijlengte van de groep I/O verwerken  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  ProcessingpoolJobQueueLength  |  Wachtrijlengte van de groeptaak verwerken  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  qpu_metric  |  QPU (QPU)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  QuerypoolBusyThreads  |  Drukke threads voor querygroep  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  QueryPoolIdleThreads  |  Threads: Niet-actieve threads van de querygroep  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  QuerypoolJobQueueLengte  |  Threads: wachtrijlengte van de querygroeptaak  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  Quota  |  Geheugen: Quotum  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  Quotageblokkeerd  |  Geheugen: Quotum geblokkeerd  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  Rijenomgebouwde persec  |  Verwerking: rijen die per seconde worden geconverteerd  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  RowsReadPersec  |  Verwerking: Rijen per seconde gelezen  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  RijengeschrevenPersec  |  Verwerking: rijen geschreven per seconde  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  ShortParsingBusyThreads  |  Threads: Korte parsing drukke threads  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  ShortParsingIdleThreads  |  Threads: Korte ontwering idle threads  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  ShortParsingJobQueueLength ShortParsingJobQueueLength ShortParsingJobQueueLength ShortPar  |  Threads: Korte afstand van de taakwachtrijlengte  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  Succesvolle verbindingenPerSec  |  Succesvolle verbindingen per seconde  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  TotalConnection-fouten  |  Totale verbindingsfouten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  TotalConnectionRequests  |  Totaal aantal verbindingsaanvragen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  VertiPaqNonpaged  |  Geheugen: VertiPaq Nonpaged  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  VertiPaqPaged  |  Geheugen: VertiPaq Paged  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  virtual_bytes_metric  |  Virtuele bytes  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  Back-endDuur  |  Duur van backendaanvragen  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.ApiManagement/service  |  Capaciteit  |  Capaciteit  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  Duur  |  Totale duur van gatewayaanvragen  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubDroppedGebeurtenissen  |  Gebeurtenisgebeurtenissen van EventHub laten vallen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubRejectedEvents  |  Afgewezen EventHub-gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubSuccessfulEvents  |  Succesvolle EventHub-evenementen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubThrottledEvents  |  Throttled EventHub-gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubTimedoutGebeurtenissen  |  Time-out EventHub-evenementen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubTotalBytesSent  |  Grootte van EventHub-gebeurtenissen  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubTotalEvents  |  Totaal EventHub-gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  GebeurtenisHubTotalFailedgebeurtenissen  |  Mislukte EventHub-gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  Mislukte aanvragen  |  Mislukte gatewayaanvragen (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  Overige verzoeken  |  Andere gatewayaanvragen (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  Aanvragen  |  Aanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  Succesvolle verzoeken  |  Succesvolle gatewayaanvragen (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  TotalRequests  |  Totaalgatewayaanvragen (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  Ongeautoriseerde verzoeken  |  Ongeautoriseerde gatewayverzoeken (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.AppPlatform/Spring  |  AppCpuUsagePercentage  |  Gebruikspercentage app-cpu's  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.AppPlatform/Spring  |  AppMemoryCommitted  |  App-geheugen toegewezen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AppPlatform/Spring  |  AppMemoryMax AppMemoryMax AppMemoryMax  |  App-geheugen max.  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.AppPlatform/Spring  |  AppMemoryGebruikt  |  App-geheugen gebruikt  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AppPlatform/Spring  |  GCPauseTotalCount  |  GC-pauzetelling  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.AppPlatform/Spring  |  GCPauseTotalTime  |  Totale tijd van GC-pauze  |  Milliseconden  |  Totaal | 
| **Ja**  | Nee |  Microsoft.AppPlatform/Spring  |  MaxOldGenMemoryPoolBytes  |  Max beschikbare oude generatie gegevensgrootte  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AppPlatform/Spring  |  OldGenMemoryPoolBytes  |  Gegevensgrootte van oude generatie  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AppPlatform/Spring  |  OldGenPromotedBytes  |  Bevorderen tot gegevensgrootte van oude generatie  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.AppPlatform/Spring  |  SystemCpuUsagePercentage SystemCpuUsagePercentage SystemCpuUsagePercentage SystemCpu  |  Gebruikspercentage systeem-CPU-gebruik  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.AppPlatform/Spring  |  TomcatErrorCount  |  Tomcat-globale fout  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.AppPlatform/Spring  |  TomcatReceivedBytes  |  Tomcat Totaal ontvangen bytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.AppPlatform/Spring  |  TomcatRequestMaxTime  |  Tomcat Request Max Time  |  Milliseconden  |  Maximum | 
| **Ja**  | Nee |  Microsoft.AppPlatform/Spring  |  TomcatRequestTotalCount  |  Totaal aantal aanvragen voor Tomcat-aanvragen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.AppPlatform/Spring  |  TomcatRequestTotalTime  |  Totaalaantal aanvragen tomcat-aanvraag  |  Milliseconden  |  Totaal | 
| **Ja**  | Nee |  Microsoft.AppPlatform/Spring  |  TomcatResponseAvgTime  |  Gemiddelde tijd voor Tomcat-aanvraag  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.AppPlatform/Spring  |  TomcatSentBytes  |  Tomcat Totaal verzonden bytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.AppPlatform/Spring  |  TomcatSessionActiveCurrentCount  |  Tomcat Sessie Alive Count  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.AppPlatform/Spring  |  TomcatSessionActiveMaxCount  |  Tomcat Session Max Active Count  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.AppPlatform/Spring  |  TomcatSessionAliveMaxTijd  |  Tomcat Sessie Max Alive Time  |  Milliseconden  |  Maximum | 
| **Ja**  | Nee |  Microsoft.AppPlatform/Spring  |  TomcatSessionCreatedCount  |  Aantal gemaakt Tomcat-sessie  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.AppPlatform/Spring  |  TomcatSessionExpiredCount  |  Aantal verlopen tomcat-sessies  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.AppPlatform/Spring  |  TomcatSessionRejectedCount  |  Afgewezen aantal Tomcat-sessie  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.AppPlatform/Spring  |  YoungGenPromotedBytes YoungGenPromotedBytes YoungGenPromotedBytes YoungGen  |  Bevorderen tot gegevensgrootte van jonge generaties  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Automation/automationAccounts  |  TotalJob  |  Totaal aantal taken  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Automation/automationAccounts  |  TotalupdateDeploymentmachineRuns TotalupdateDeploymentmachineRuns TotalupdateDeploymentmachineRuns Totalupdate  |  Totaal aantal machine-uitvoeringen van update-implementaties  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Automation/automationAccounts  |  TotalupdateDeploymentRuns  |  Totaal aantal uitvoeringen van update-implementaties  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  Aantal cores  |  Speciale kerntelling  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  CreatingNodeCount  |  Aantal knooppunt's maken  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  IdleNodeCount  |  Aantal niet-actieve knooppunten  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  JobDeleteCompleteEvent  |  Complete gebeurtenissen verwijderen van taak  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  JobdeleteStartEvent  |  Startgebeurtenissen voor taak verwijderen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  JobDisableCompleteEvent  |  Complete gebeurtenissen uitschakelen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  JobDisableStartEvent  |  Startgebeurtenissen uitschakelen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  JobStartEvent  |  Gebeurtenissen voor het starten van vacatures  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  Gebeurtenis TaakterminateComplete  |  Volledige gebeurtenissen beëindigen van taak  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  JobterminateStartEvent  |  Begingebeurtenissen voor het beëindigen van taken beëindigen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  LeavingPoolNodeCount  |  Poolknooppunt tellen verlaten  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  LowPriorityCoreCount LowPriorityCoreCount LowPriorityCoreCount LowPriority  |  LowPriority-kerntelling  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  OfflineNodeCount  |  Aantal offline knooppunten  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  PoolCreateEvent  |  Gebeurtenissen maken  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  PooldeleteCompleteEvent  |  Complete gebeurtenissen verwijderen verwijderen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  PooldeleteStartEvent  |  Startgebeurtenissen verwijderen verwijderen groep  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  PoolResizeCompleteEvent  |  Het formaat complete gebeurtenissen van de groep wijzigen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  PoolResizeStartEvent  |  Begingebeurtenissen voor het aanpassen van het formaat wijzigen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  VoorrangNodeCount  |  Voorrang Node Count  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  RebootingNodeCount  |  Aantal knooppunten opnieuw opstarten  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  ReimagingNodeCount  |  Reimaging Node Count  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  RunningNodeCount  |  Aantal knooppunten uitvoeren  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  StartingNodeCount  |  Aantal knooppunten starten  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  BegintaakMisluktNodeCount  |  Aantal mislukte knooppunttaken starten  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  TaakCompleteEvent  |  Gebeurtenissen voltooien van taken  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  Taakfailgebeurtenis  |  Gebeurtenissen als taakmislukt  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  TaakstartGebeurtenis  |  Gebeurtenissen voor het starten van taken  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  TotalLowPriorityNodeCount TotalLowPriorityNodeCount TotalLowPriorityNodeCount TotalLow  |  Aantal knooppunts met lage prioriteit  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  TotalNodeCount TotalNodeCount  |  Speciale nodetelling  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  UnusableNodeCount  |  Aantal onbruikbare knooppunt  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  WachtenForStartTaskNodeCount  |  Wachten op het aantal takenvan taak  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.BatchAI/werkruimten  |  Actieve kernen  |  Actieve kernen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.BatchAI/werkruimten  |  Actieve knooppunten  |  Actieve knooppunten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.BatchAI/werkruimten  |  Niet-actieve kernen  |  Niet-actieve kernen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.BatchAI/werkruimten  |  Inactieve knooppunten  |  Inactieve knooppunten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.BatchAI/werkruimten  |  Taak voltooid  |  Taak voltooid  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.BatchAI/werkruimten  |  Vacature ingediend  |  Vacature ingediend  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.BatchAI/werkruimten  |  Kernen verlaten  |  Kernen verlaten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.BatchAI/werkruimten  |  Knooppunten verlaten  |  Knooppunten verlaten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.BatchAI/werkruimten  |  Voorrang kernen  |  Voorrang kernen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.BatchAI/werkruimten  |  Voorkoming van knooppunten  |  Voorkoming van knooppunten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.BatchAI/werkruimten  |  Percentage quotumgebruik  |  Percentage quotumgebruik  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.BatchAI/werkruimten  |  Totaal aantal kernen  |  Totaal aantal kernen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.BatchAI/werkruimten  |  Totaal aantal knooppunten  |  Totaal aantal knooppunten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.BatchAI/werkruimten  |  Onbruikbare kernen  |  Onbruikbare kernen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.BatchAI/werkruimten  |  Onbruikbare knooppunten  |  Onbruikbare knooppunten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainLeden  |  Verbindinggeaccepteerd  |  Geaccepteerde verbindingen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainLeden  |  ConnectionActive  |  Actieve verbindingen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainLeden  |  Verbinding verwerkt  |  Verwerkte verbindingen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainLeden  |  CpuUsagePercentageInDouble  |  CPU-gebruikspercentage  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainLeden  |  IOReadBytes  |  IO Lees bytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainLeden  |  IOWriteBytes  |  IO-schrijfbytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainLeden  |  MemoryLimit (MemoryLimit)  |  Geheugenlimiet  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainLeden  |  Geheugengebruik  |  Geheugengebruik  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainLeden  |  Geheugenusagepercentageindouble  |  Percentage geheugengebruik  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainLeden  |  Transacties in behandeling  |  Transacties in behandeling  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainLeden  |  Verwerkte blokken  |  Verwerkte blokken  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainLeden  |  Verwerkte transacties  |  Verwerkte transacties  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainLeden  |  Transacties in de wachtrij  |  Transacties in wachtrijen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainLeden  |  Aanvraag behandeld  |  Afgehandelde aanvragen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainLeden  |  OpslagGebruik  |  Opslaggebruik  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachehits  |  Cache Hits  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachehits0  |  Cache Hits (Shard 0)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachehits1  |  Cache Hits (Shard 1)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachehits2  |  Cache Hits (Shard 2)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachehits3  |  Cache Hits (Shard 3)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachehits4  |  Cache Hits (Shard 4)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachehits5  |  Cache Hits (Shard 5)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachehits6  |  Cache Hits (Shard 6)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachehits7  |  Cache Hits (Shard 7)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachehits8  |  Cache Hits (Shard 8)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachehits9  |  Cache Hits (Shard 9)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheLatentie  |  Cachelatentie Microseconden (Preview)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachemisses  |  Cache-missers  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachemisses0  |  Cache-missers (Shard 0)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachemisses1  |  Cache Missers (Shard 1)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachemisses2  |  Cache Missers (Shard 2)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachemisses3  |  Cache Missers (Shard 3)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachemisses4  |  Cache Missers (Shard 4)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachemisses5  |  Cache Missers (Shard 5)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachemisses6  |  Cache Missers (Shard 6)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachemisses7  |  Cache Missers (Shard 7)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachemisses8  |  Cache Missers (Shard 8)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachemisses9  |  Cache Missers (Shard 9)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheLezen  |  Cache lezen  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheRead0  |  Cache lezen (shard 0)  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheRead1  |  Cache lezen (shard 1)  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheRead2  |  Cache lezen (Shard 2)  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheLees3  |  Cache lezen (Shard 3)  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheLees4  |  Cache lezen (Shard 4)  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheLees5  |  Cache lezen (Shard 5)  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheRead6  |  Cache lezen (Shard 6)  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheLees7  |  Cache lezen (Shard 7)  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheRead8  |  Cache lezen (Shard 8)  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheRead9  |  Cache lezen (shard 9)  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheWrite  |  Cache schrijven  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheWrite0  |  Cache schrijven (shard 0)  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheWrite1  |  Cache schrijven (shard 1)  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheWrite2  |  Cache schrijven (Shard 2)  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheWrite3  |  Cache schrijven (Shard 3)  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheWrite4  |  Cache schrijven (Shard 4)  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheWrite5  |  Cache schrijven (Shard 5)  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheWrite6  |  Cache schrijven (Shard 6)  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheWrite7  |  Cache schrijven (Shard 7)  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheWrite8  |  Cache schrijven (Shard 8)  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheWrite9  |  Cache schrijven (shard 9)  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  verbonden clients  |  Verbonden clients  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  connectedclients0  |  Verbonden clients (Shard 0)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  verbonden clients1  |  Verbonden clients (Shard 1)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  verbonden clients2  |  Verbonden clients (Shard 2)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  verbonden clients3  |  Verbonden clients (Shard 3)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  connectedclients4  |  Verbonden clients (Shard 4)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  verbonden clients5  |  Verbonden clients (Shard 5)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  verbonden clients6  |  Verbonden clients (Shard 6)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  verbondenclients7  |  Verbonden clients (Shard 7)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  verbondenclients8  |  Verbonden clients (Shard 8)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  verbonden clients9  |  Verbonden clients (Shard 9)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  fouten  |  Fouten  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  uitgezette sleutels  |  Uitgezette sleutels  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  uitgezetsleutels0  |  Uitgezette sleutels (Shard 0)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  uitgezette sleutels1  |  Uitgezette sleutels (Shard 1)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  uitgezette sleutels2  |  Uitgezette sleutels (Shard 2)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  uitgezette sleutels3  |  Uitgezette sleutels (Shard 3)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  uitgezette sleutels4  |  Uitgezette sleutels (Shard 4)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  uitgezette sleutels5  |  Uitgezette sleutels (Shard 5)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  uitgezette sleutels6  |  Uitgezette sleutels (Shard 6)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  uitgezette sleutels7  |  Uitgezette sleutels (Shard 7)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  uitgezette sleutels8  |  Uitgezette sleutels (Shard 8)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  uitgezette sleutels9  |  Uitgezette sleutels (Scherf 9)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  verlopen sleutels  |  Verlopen sleutels  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  verlopensleutels00  |  Verlopen sleutels (shard 0)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  verlopen sleutels1  |  Verlopen sleutels (shard 1)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  verlopen sleutels2  |  Verlopen sleutels (Shard 2)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  verlopen sleutels3  |  Verlopen sleutels (Shard 3)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  verlopen sleutels4  |  Verlopen sleutels (Shard 4)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  verlopen sleutels5  |  Verlopen sleutels (Shard 5)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  verlopen sleutels6  |  Verlopen sleutels (Shard 6)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  verlopen sleutels7  |  Verlopen sleutels (Shard 7)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  verlopen sleutels8  |  Verlopen sleutels (Shard 8)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  verlopen sleutels9  |  Verlopen sleutels (Shard 9)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  getcommands  |  Krijgt  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  getcommands0  |  Krijgt (Shard 0)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  getcommands1  |  Gets (Shard 1)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  getcommands2  |  Krijgt (Shard 2)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  getcommands3  |  Krijgt (Shard 3)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  getcommands4  |  Krijgt (Shard 4)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  getcommands5  |  Krijgt (Shard 5)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  getcommands6  |  Krijgt (Shard 6)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  getcommands7  |  Krijgt (Shard 7)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  getcommands8  |  Krijgt (Shard 8)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  getcommands9  |  Krijgt (Shard 9)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  operationsPerSecond  |  Bewerkingen per seconde  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  operationsPerSeconde0  |  Bewerkingen per seconde (shard 0)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  operationsPerSeconde1  |  Bewerkingen per seconde (shard 1)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  operationsPerSeconde2  |  Bewerkingen per seconde (Shard 2)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  operationsPerSeconde3  |  Bewerkingen per seconde (Shard 3)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  operationsPerSeconde4  |  Bewerkingen per seconde (Shard 4)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  operationsPerSeconde5  |  Bewerkingen per seconde (Shard 5)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  operationsPerSeconde6  |  Bewerkingen per seconde (Shard 6)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  operationsPerSeconde7  |  Bewerkingen per seconde (Shard 7)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  operationsPerSeconde8  |  Bewerkingen per seconde (Shard 8)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  operationsPerSeconde9  |  Bewerkingen per seconde (Shard 9)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  percentProcessorTime  |  CPU  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  percentProcessorTime0  |  CPU (Shard 0)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  percentProcessorTime1  |  CPU (Shard 1)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  percentProcessorTime2  |  CPU (Shard 2)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  percentProcessorTime3  |  CPU (Shard 3)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  percentProcessorTime4  |  CPU (Shard 4)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  percentProcessorTime5  |  CPU (Shard 5)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  percentProcessorTime6  |  CPU (Shard 6)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  percentProcessorTime7  |  CPU (Shard 7)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  percentProcessorTime8  |  CPU (Shard 8)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  percentProcessorTime9  |  CPU (Shard 9)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  serverLaden  |  Serverbelasting  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  serverLoad0  |  Serverbelasting (shard 0)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  serverLoad1  |  Serverbelasting (shard 1)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  serverLoad2  |  Serverbelasting (Shard 2)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  serverLoad3  |  Serverbelasting (Shard 3)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  serverLoad4  |  Serverbelasting (Shard 4)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  serverLoad5  |  Serverbelasting (Shard 5)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  serverLoad6  |  Serverbelasting (shard 6)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  serverLoad7  |  Serverbelasting (shard 7)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  serverLoad8  |  Serverbelasting (shard 8)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  serverLoad9  |  Serverbelasting (shard 9)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  setopdrachten instellen  |  Sets  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  setcommands0  |  Sets (Shard 0)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  setcommands1  |  Sets (Shard 1)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  setcommands2  |  Sets (Shard 2)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  setcommands3  |  Sets (Shard 3)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  setcommands4  |  Sets (Shard 4)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  setcommands5  |  Sets (Shard 5)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  setcommands6  |  Sets (Shard 6)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  setcommands7  |  Sets (Shard 7)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  setcommands8  |  Sets (Shard 8)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  setcommands9  |  Sets (Shard 9)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalcommandsprocessed  |  Totaal bewerkingen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalcommandsprocessed0  |  Totale bewerkingen (shard 0)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalcommandsprocessed1  |  Totale bewerkingen (shard 1)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalcommandsprocessed2  |  Totale bewerkingen (shard 2)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalcommandsprocessed3  |  Totale bewerkingen (shard 3)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalcommandsprocessed4  |  Totale bewerkingen (shard 4)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalcommandsprocessed5  |  Totale bewerkingen (shard 5)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalcommandsprocessed6  |  Totale bewerkingen (shard 6)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalcommandsprocessed7  |  Totale bewerkingen (shard 7)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalcommandsprocessed8  |  Totale bewerkingen (shard 8)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalcommandsprocessed9  |  Totale bewerkingen (shard 9)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalkeys  |  Totaal aantal toetsen  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalkeys0  |  Totaal aantal toetsen (shard 0)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalkeys1  |  Totaal aantal toetsen (shard 1)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalkeys2  |  Totaal aantal toetsen (shard 2)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalkeys3  |  Totaal aantal toetsen (shard 3)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalkeys4  |  Totaal aantal toetsen (shard 4)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalkeys5  |  Totaal aantal toetsen (shard 5)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalkeys6  |  Totaal aantal toetsen (shard 6)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalkeys7  |  Totaal aantal toetsen (shard 7)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalkeys8  |  Totaal aantal toetsen (shard 8)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalkeys9  |  Totaal aantal toetsen (shard 9)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  gebruikt geheugen  |  Gebruikt geheugen  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemory0  |  Gebruikt geheugen (shard 0)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  gebruikt geheugen1  |  Gebruikt geheugen (shard 1)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  gebruikt geheugen2  |  Gebruikt geheugen (shard 2)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  gebruikt geheugen3  |  Gebruikt geheugen (shard 3)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  gebruiktgeheugen4  |  Gebruikt geheugen (shard 4)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  gebruikt geheugen5  |  Gebruikt geheugen (shard 5)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  gebruikt geheugen6  |  Gebruikt geheugen (shard 6)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  gebruikt geheugen7  |  Gebruikt geheugen (shard 7)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  gebruikt geheugen8  |  Gebruikt geheugen (shard 8)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemory9  |  Gebruikt geheugen (shard 9)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  gebruiktgeheugenpercentage  |  Percentage gebruikt geheugen  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  gebruikte memoryRss  |  RSS gebruikt geheugen  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemoryRss0  |  Gebruikte geheugen RSS (Shard 0)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  gebruikte memoryRss1  |  Gebruikte geheugen RSS (Shard 1)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemoryRss2  |  Gebruikte geheugen RSS (Shard 2)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemoryRss3  |  Gebruikte geheugen RSS (Shard 3)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemoryRss4  |  Gebruikte geheugen RSS (Shard 4)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemoryRss5  |  Gebruikte geheugen RSS (Shard 5)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemoryRss6  |  Gebruikte geheugen RSS (Shard 6)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemoryRss7  |  Gebruikte geheugen RSS (Shard 7)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemoryRss8  |  Gebruikte geheugen RSS (Shard 8)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemoryRss9  |  Gebruikte geheugen RSS (Shard 9)  |  Bytes  |  Maximum | 
| Nee  | Nee |  Microsoft.ClassicCompute/domainNames/slots/rollen  |  Bytes/Seconde schijfgelezen  |  Schijf lezen  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicCompute/domainNames/slots/rollen  |  Bewerkingen voor schijflezen/sec  |  Bewerkingen voor schijflezen/sec  |  CountPerSeconde  |  Average | 
| Nee  | Nee |  Microsoft.ClassicCompute/domainNames/slots/rollen  |  Bytes voor schijfschrijfbytes per seconde  |  Schijfschrijven  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicCompute/domainNames/slots/rollen  |  Bewerkingen voor schijfschrijfbewerkingen/sec  |  Bewerkingen voor schijfschrijfbewerkingen/sec  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicCompute/domainNames/slots/rollen  |  Netwerk in  |  Netwerk in  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.ClassicCompute/domainNames/slots/rollen  |  Netwerk uit  |  Netwerk uit  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.ClassicCompute/domainNames/slots/rollen  |  Percentage CPU  |  Percentage CPU  |  Percentage  |  Average | 
| Nee  | Nee |  Microsoft.ClassicCompute/virtualMachines  |  Bytes/Seconde schijfgelezen  |  Schijf lezen  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicCompute/virtualMachines  |  Bewerkingen voor schijflezen/sec  |  Bewerkingen voor schijflezen/sec  |  CountPerSeconde  |  Average | 
| Nee  | Nee |  Microsoft.ClassicCompute/virtualMachines  |  Bytes voor schijfschrijfbytes per seconde  |  Schijfschrijven  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicCompute/virtualMachines  |  Bewerkingen voor schijfschrijfbewerkingen/sec  |  Bewerkingen voor schijfschrijfbewerkingen/sec  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicCompute/virtualMachines  |  Netwerk in  |  Netwerk in  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.ClassicCompute/virtualMachines  |  Netwerk uit  |  Netwerk uit  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.ClassicCompute/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts  |  Beschikbaarheid  |  Beschikbaarheid  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts  |  SuccesE2ELatentie  |  Success E2E Latency  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts  |  SuccessServerLatentie  |  Geslaagde serverlatentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts  |  Transacties  |  Transacties  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.ClassicStorage/storageAccounts  |  Gebruikte capaciteit  |  Gebruikte capaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Beschikbaarheid  |  Beschikbaarheid  |  Percentage  |  Average | 
| Nee  | Nee |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  BlobCapaciteit  |  Blob-capaciteit  |  Bytes  |  Average | 
| Nee  | Nee |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  BlobCount  |  Blobtelling  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  ContainerCount ContainerCount  |  Aantal Blob-containers  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| Nee  | Nee |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Indexcapaciteit  |  Indexcapaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  SuccesE2ELatentie  |  Success E2E Latency  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  SuccessServerLatentie  |  Geslaagde serverlatentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/blobServices  |  Transacties  |  Transacties  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Beschikbaarheid  |  Beschikbaarheid  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| Nee  | Nee |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Bestandscapaciteit  |  Bestandscapaciteit  |  Bytes  |  Average | 
| Nee  | Nee |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileCount  |  Aantal bestanden  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareCount  |  Aantal bestandsdelen  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareQuota  |  Quotumgrootte voor bestandsshare  |  Bytes  |  Average | 
| Nee  | Nee |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareSnapshotCount  |  Momentopnameaantal momentopnamen voor bestandsshare  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  FileShareSnapshotSize  |  Momentopnamemomentopname voor bestandsshare  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  SuccesE2ELatentie  |  Success E2E Latency  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  SuccessServerLatentie  |  Geslaagde serverlatentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/fileServices  |  Transacties  |  Transacties  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Beschikbaarheid  |  Beschikbaarheid  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Wachtrijcapaciteit  |  Wachtrijcapaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Aantal wachtrijen  |  Aantal wachtrijen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Aantal queuemessage's  |  Aantal wachtrijberichten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  SuccesE2ELatentie  |  Success E2E Latency  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  SuccessServerLatentie  |  Geslaagde serverlatentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/queueServices  |  Transacties  |  Transacties  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Beschikbaarheid  |  Beschikbaarheid  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  SuccesE2ELatentie  |  Success E2E Latency  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  SuccessServerLatentie  |  Geslaagde serverlatentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Tabelcapaciteit  |  Tabelcapaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Tabeltelling  |  Tabelaantal  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Tabelentiteittelling  |  Aantal tabelentiteiten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts/tableServices  |  Transacties  |  Transacties  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  Geblokkeerde oproepen  |  Geblokkeerde oproepen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  TekensGetraind  |  Personages getraind  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  Tekensvertaald  |  Tekens vertaald  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  ClientFouten  |  Clientfouten  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  DataIn  |  Gegevens in  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  Gegevensout  |  Gegevens uit  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  Latentie  |  Latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  Serverfouten  |  Serverfouten  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  SessiesessieDuur  |  Sessieduur van spraaksessies  |  Seconden  |  Totaal | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  Succesvol bellen  |  Succesvolle gesprekken  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  Totaal Aantal oproepen  |  Totaal aantal oproepen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  Totaalfouten  |  Totaal aantal fouten  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  TotalTokencalls  |  Totaal aantal tokenoproepen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  TotaalTransacties  |  Totaal aantal transacties  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  CPU-credits verbruikt  |  CPU-credits verbruikt  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Resterende CPU-credits  |  Resterende CPU-credits  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Wachtrijlengte van gegevensschijf  |  Gegevensschijfwachtrijdiepte (voorbeeld)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Bytes/seconde gegevensschijf gelezen  |  Bytes/Seconde voor gegevensschijf lezen (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Leesbewerkingen gegevensschijf/sec  |  Gegevensschijfleesbewerkingen/Sec (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Schrijfbytes voor gegevensschijf per seconde  |  Bytes/sec voor gegevensschijf schrijven (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Schrijfbewerkingen voor gegevensschijf/sec  |  Schrijfbewerkingen voor gegevensschijf/seconde (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Bytes voor schijflezen  |  Bytes voor schijflezen  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Bewerkingen voor schijflezen/sec  |  Bewerkingen voor schijflezen/sec  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Schrijfbytes voor schijf  |  Schrijfbytes voor schijf  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Bewerkingen voor schijfschrijfbewerkingen/sec  |  Bewerkingen voor schijfschrijfbewerkingen/sec  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Binnenkomende stromen  |  Binnenkomende stromen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Maximale creatiesnelheid inkomende stromen  |  Maximale creatiesnelheid inkomende stromen (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Netwerk in  |  Netwerk in factureerbaar (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Netwerk in totaal  |  Netwerk in totaal  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Netwerk uit  |  Network Out Billable (Afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Netwerk totaal  |  Netwerk totaal  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Wachtrijlengte van besturingssysteemschijf  |  Diepte van de schijfwachtrij van HET BE (voorbeeld)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Bytes/seconde voor gelezen osschijflezen  |  Bytes/Sec voor gelezen OS-schijf (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Leesbewerkingen voor schijven van BE/Sec  |  Bewerkingen voor lezen van de schijf van BE/Sec (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Bytes voor schrijven van os-schijven per seconde  |  Bytes/sec voor schrijven in de os-schijf (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Schrijfbewerkingen voor osschijfschrijfbewerkingen/sec  |  Be-disk schrijfbewerkingen/sec (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  OS Per schijf QD  |  OS Disk QD (afgeschaft)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Bytes per seconde voor gelezen besturingssysteem per schijf  |  Bytes/Sec voor gelezen SCHIJVEN (afgeschaft)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Leesbewerkingen van het besturingssysteem per schijf per seconde  |  Bewerkingen voor lezen van schijven/sec van het besturingssysteem (afgeschaft)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Schrijven bytes per schijf per seconde per schijf  |  Bytes/Sec voor schrijven in de schijf van HET BE (afgeschaft)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Schrijfbewerkingen per schijf per seconde  |  Bewerkingen voor schrijven van OS-schijven/Sec (afgeschaft)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Uitgaande stromen  |  Uitgaande stromen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Maximale creatiesnelheid voor uitgaande stromen  |  Maximale creatiesnelheid uitgaande stromen (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  QD per schijf  |  Gegevensschijf QD (afgeschaft)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Bytes per seconde per schijf gelezen  |  Bytes/Sec gegevensschijf lezen (afgeschaft)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Leesbewerkingen per schijf per seconde  |  Gegevensschijfleesbewerkingen/Sec (afgeschaft)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Schrijfbytes per schijf per seconde  |  Bytes/Sec voor gegevensschijfschrijven (afgeschaft)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Schrijfbewerkingen per schijf per seconde  |  Gegevensschijfschrijfbewerkingen/Sec (afgeschaft)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Premium Data Disk Cache Read Hit  |  Gelezen hit in de schijfcache van Premium-gegevens (voorbeeld)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Premium Data Disk Cache Lees Miss  |  Leesmisser van de schijfcache van Premium-gegevens (voorbeeld)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Premium OS Disk Cache Read Hit  |  Premium OS Disk Cache Read Hit (Preview)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Premium OS Disk Cache Lees Miss  |  Premium OS Disk Cache Lees Miss (Preview)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  CPU-credits verbruikt  |  CPU-credits verbruikt  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Resterende CPU-credits  |  Resterende CPU-credits  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Wachtrijlengte van gegevensschijf  |  Gegevensschijfwachtrijdiepte (voorbeeld)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Bytes/seconde gegevensschijf gelezen  |  Bytes/Seconde voor gegevensschijf lezen (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Leesbewerkingen gegevensschijf/sec  |  Gegevensschijfleesbewerkingen/Sec (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Schrijfbytes voor gegevensschijf per seconde  |  Bytes/sec voor gegevensschijf schrijven (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Schrijfbewerkingen voor gegevensschijf/sec  |  Schrijfbewerkingen voor gegevensschijf/seconde (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Bytes voor schijflezen  |  Bytes voor schijflezen  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Bewerkingen voor schijflezen/sec  |  Bewerkingen voor schijflezen/sec  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Schrijfbytes voor schijf  |  Schrijfbytes voor schijf  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Bewerkingen voor schijfschrijfbewerkingen/sec  |  Bewerkingen voor schijfschrijfbewerkingen/sec  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Binnenkomende stromen  |  Binnenkomende stromen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Maximale creatiesnelheid inkomende stromen  |  Maximale creatiesnelheid inkomende stromen (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Netwerk in  |  Netwerk in factureerbaar (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Netwerk in totaal  |  Netwerk in totaal  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Netwerk uit  |  Network Out Billable (Afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Netwerk totaal  |  Netwerk totaal  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Wachtrijlengte van besturingssysteemschijf  |  Diepte van de schijfwachtrij van HET BE (voorbeeld)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Bytes/seconde voor gelezen osschijflezen  |  Bytes/Sec voor gelezen OS-schijf (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Leesbewerkingen voor schijven van BE/Sec  |  Bewerkingen voor lezen van de schijf van BE/Sec (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Bytes voor schrijven van os-schijven per seconde  |  Bytes/sec voor schrijven in de os-schijf (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Schrijfbewerkingen voor osschijfschrijfbewerkingen/sec  |  Be-disk schrijfbewerkingen/sec (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  OS Per schijf QD  |  OS Disk QD (afgeschaft)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Bytes per seconde voor gelezen besturingssysteem per schijf  |  Bytes/Sec voor gelezen SCHIJVEN (afgeschaft)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Leesbewerkingen van het besturingssysteem per schijf per seconde  |  Bewerkingen voor lezen van schijven/sec van het besturingssysteem (afgeschaft)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Schrijven bytes per schijf per seconde per schijf  |  Bytes/Sec voor schrijven in de schijf van HET BE (afgeschaft)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Schrijfbewerkingen per schijf per seconde  |  Bewerkingen voor schrijven van OS-schijven/Sec (afgeschaft)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Uitgaande stromen  |  Uitgaande stromen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Maximale creatiesnelheid voor uitgaande stromen  |  Maximale creatiesnelheid uitgaande stromen (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  QD per schijf  |  Gegevensschijf QD (afgeschaft)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Bytes per seconde per schijf gelezen  |  Bytes/Sec gegevensschijf lezen (afgeschaft)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Leesbewerkingen per schijf per seconde  |  Gegevensschijfleesbewerkingen/Sec (afgeschaft)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Schrijfbytes per schijf per seconde  |  Bytes/Sec voor gegevensschijfschrijven (afgeschaft)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Schrijfbewerkingen per schijf per seconde  |  Gegevensschijfschrijfbewerkingen/Sec (afgeschaft)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Percentage CPU  |  Percentage CPU  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Premium Data Disk Cache Read Hit  |  Gelezen hit in de schijfcache van Premium-gegevens (voorbeeld)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Premium Data Disk Cache Lees Miss  |  Leesmisser van de schijfcache van Premium-gegevens (voorbeeld)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Premium OS Disk Cache Read Hit  |  Premium OS Disk Cache Read Hit (Preview)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Premium OS Disk Cache Lees Miss  |  Premium OS Disk Cache Lees Miss (Preview)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  CPU-credits verbruikt  |  CPU-credits verbruikt  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Resterende CPU-credits  |  Resterende CPU-credits  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Wachtrijlengte van gegevensschijf  |  Gegevensschijfwachtrijdiepte (voorbeeld)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bytes/seconde gegevensschijf gelezen  |  Bytes/Seconde voor gegevensschijf lezen (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Leesbewerkingen gegevensschijf/sec  |  Gegevensschijfleesbewerkingen/Sec (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Schrijfbytes voor gegevensschijf per seconde  |  Bytes/sec voor gegevensschijf schrijven (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Schrijfbewerkingen voor gegevensschijf/sec  |  Schrijfbewerkingen voor gegevensschijf/seconde (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bytes voor schijflezen  |  Bytes voor schijflezen  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bewerkingen voor schijflezen/sec  |  Bewerkingen voor schijflezen/sec  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Schrijfbytes voor schijf  |  Schrijfbytes voor schijf  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bewerkingen voor schijfschrijfbewerkingen/sec  |  Bewerkingen voor schijfschrijfbewerkingen/sec  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Binnenkomende stromen  |  Binnenkomende stromen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Maximale creatiesnelheid inkomende stromen  |  Maximale creatiesnelheid inkomende stromen (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Netwerk in  |  Netwerk in factureerbaar (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Netwerk in totaal  |  Netwerk in totaal  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Netwerk uit  |  Network Out Billable (Afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Netwerk totaal  |  Netwerk totaal  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Wachtrijlengte van besturingssysteemschijf  |  Diepte van de schijfwachtrij van HET BE (voorbeeld)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bytes/seconde voor gelezen osschijflezen  |  Bytes/Sec voor gelezen OS-schijf (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Leesbewerkingen voor schijven van BE/Sec  |  Bewerkingen voor lezen van de schijf van BE/Sec (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bytes voor schrijven van os-schijven per seconde  |  Bytes/sec voor schrijven in de os-schijf (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Schrijfbewerkingen voor osschijfschrijfbewerkingen/sec  |  Be-disk schrijfbewerkingen/sec (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  OS Per schijf QD  |  OS Disk QD (afgeschaft)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bytes per seconde voor gelezen besturingssysteem per schijf  |  Bytes/Sec voor gelezen SCHIJVEN (afgeschaft)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Leesbewerkingen van het besturingssysteem per schijf per seconde  |  Bewerkingen voor lezen van schijven/sec van het besturingssysteem (afgeschaft)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Schrijven bytes per schijf per seconde per schijf  |  Bytes/Sec voor schrijven in de schijf van HET BE (afgeschaft)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Schrijfbewerkingen per schijf per seconde  |  Bewerkingen voor schrijven van OS-schijven/Sec (afgeschaft)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Uitgaande stromen  |  Uitgaande stromen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Maximale creatiesnelheid voor uitgaande stromen  |  Maximale creatiesnelheid uitgaande stromen (voorbeeld)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  QD per schijf  |  Gegevensschijf QD (afgeschaft)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Bytes per seconde per schijf gelezen  |  Bytes/Sec gegevensschijf lezen (afgeschaft)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Leesbewerkingen per schijf per seconde  |  Gegevensschijfleesbewerkingen/Sec (afgeschaft)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Schrijfbytes per schijf per seconde  |  Bytes/Sec voor gegevensschijfschrijven (afgeschaft)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Schrijfbewerkingen per schijf per seconde  |  Gegevensschijfschrijfbewerkingen/Sec (afgeschaft)  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Premium Data Disk Cache Read Hit  |  Gelezen hit in de schijfcache van Premium-gegevens (voorbeeld)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Premium Data Disk Cache Lees Miss  |  Leesmisser van de schijfcache van Premium-gegevens (voorbeeld)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Premium OS Disk Cache Read Hit  |  Premium OS Disk Cache Read Hit (Preview)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Premium OS Disk Cache Lees Miss  |  Premium OS Disk Cache Lees Miss (Preview)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.ContainerInstance/containerGroepen  |  CpuUsage CpuUsage  |  CPU-gebruik  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.ContainerInstance/containerGroepen  |  Geheugengebruik  |  Geheugengebruik  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.ContainerInstance/containerGroepen  |  NetworkBytesReceivedPerSeconde  |  Netwerkbytes ontvangen per seconde  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.ContainerInstance/containerGroepen  |  NetworkBytesTransmittedPerSeconde  |  Netwerkbytes per seconde verzonden  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.ContainerRegistry/registers  |  RunDuration  |  Duur uitvoeren  |  Milliseconden  |  Totaal | 
| **Ja**  | Nee |  Microsoft.ContainerRegistry/registers  |  Geslaagdpullcount  |  Geslaagd aantal pullen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.ContainerRegistry/registers  |  Succesvol PushCount  |  Succesvol aantal push's  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.ContainerRegistry/registers  |  Totaal Aantal pullen  |  Totaal aantal pullen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.ContainerRegistry/registers  |  Totaal aantal push-tellingen  |  Totaal aantal push's  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_cpu_cores  |  Totaal aantal beschikbare cpu-cores in een beheerd cluster  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_memory_bytes  |  Totale hoeveelheid beschikbaar geheugen in een beheerd cluster  |  Bytes  |  Average | 
| Nee  | Nee |  Microsoft.ContainerService/managedClusters  |  kube_node_status_condition  |  Statussen voor verschillende knooppuntvoorwaarden  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_phase  |  Aantal peulen per fase  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_ready  |  Aantal pods in de status Gereed  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  Beschikbare capaciteit  |  Beschikbare capaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  BytesGeuploadedToCloud  |  Cloud bytes geüpload (apparaat)  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  BytesGeüploadToCloudPerShare  |  Cloud bytes geüpload (Delen)  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudRead-doorvoer  |  Clouddownloaddoorvoer  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughputPerShare  |  Clouddownloaddoorvoer (delen)  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadDoorvoer  |  Doorvoer van cloudupload  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughputPerShare  |  Doorvoer van cloudupload (delen)  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  HypervMemoryUsage HyperVMemoryUsage HyperVMemoryUsage HyperV  |  Edge Compute - Geheugengebruik  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  HypervVirtualProcessorGebruik  |  Edge Compute - Percentage CPU  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  NICRead-doorvoer  |  Doorvoer lezen (netwerk)  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  NICWrite-doorvoer  |  Doorvoer schrijven (netwerk)  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  TotaalCapaciteit  |  Totale capaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.DataFactory/datafabrieken  |  Mislukte runs  |  Mislukte uitvoeringen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataFactory/datafabrieken  |  Geslaagde runs  |  Succesvolle runs  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataFactory/fabrieken  |  ActivityCancelledRuns  |  Geannuleerde activiteit voert statistieken uit  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataFactory/fabrieken  |  Activiteitmisluktloopt  |  Mislukte activiteit voert statistieken uit  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataFactory/fabrieken  |  ActivitySucceededRuns ActivitySucceededRuns ActivitySucceededRuns ActivitySucceed  |  Op geslaagde activiteit voert statistieken uit  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataFactory/fabrieken  |  Factorysizeingbunits  |  Totale fabrieksgrootte (GB-eenheid)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.DataFactory/fabrieken  |  IntegratieRuntimeBeschikbaarGeheugen  |  Beschikbare geheugen voor integratieruntime  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.DataFactory/fabrieken  |  IntegratieRuntimeAverageTaskPickupDelay  |  Wachtrijduur voor integratie  |  Seconden  |  Average | 
| **Ja**  | Nee |  Microsoft.DataFactory/fabrieken  |  IntegratieRuntimeCpuPercentage  |  Integratie runtime CPU-gebruik  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.DataFactory/fabrieken  |  IntegratieRuntimeQueueLengte  |  Wachtrijlengte van de in-integratie-runtime  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.DataFactory/fabrieken  |  MaxAllowedFactorySizeInGbUnits  |  Maximaal toegestane fabrieksgrootte (GB-eenheid)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.DataFactory/fabrieken  |  MaxAllowedResourceCount  |  Maximaal toegestane entiteiten tellen  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.DataFactory/fabrieken  |  Pijplijncancelledruns  |  Geannuleerde pijplijn voert statistieken uit  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataFactory/fabrieken  |  Pijplijnmisluktloopt  |  Mislukte pijplijn voert statistieken uit  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataFactory/fabrieken  |  PipelineSucceededRuns  |  Opgevolgd pijplijnvoert statistieken uit  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataFactory/fabrieken  |  Resourceaantal  |  Aantal entiteiten in totaal  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.DataFactory/fabrieken  |  TriggerCancelledRuns  |  Geannuleerde trigger voert statistieken uit  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataFactory/fabrieken  |  Triggerfailedruns  |  Mislukte trigger voert statistieken uit  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataFactory/fabrieken  |  TriggerSucceededRuns  |  Geslaagde trigger voert statistieken uit  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataLakeAnalytics/accounts  |  Jobaucancelled  |  Geannuleerde AU-tijd  |  Seconden  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataLakeAnalytics/accounts  |  JobauendedFailure  |  Mislukte AU-tijd  |  Seconden  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataLakeAnalytics/accounts  |  Jobauendedsucces  |  Succesvolle AU-tijd  |  Seconden  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedCancelled  |  Geannuleerde taken  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedFailure  |  Mislukte taken  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedSucces  |  Succesvolle vacatures  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataLakeStore/accounts  |  Gegevenslezen  |  Gegevens lezen  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataLakeStore/accounts  |  DataGeschreven  |  Gegevens geschreven  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataLakeStore/accounts  |  Leesverzoeken  |  Leesverzoeken  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataLakeStore/accounts  |  TotaalOpslag  |  Totale opslagruimte  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.DataLakeStore/accounts  |  Schrijfverzoeken  |  Schrijfverzoeken  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  active_connections  |  Actieve verbindingen  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  backup_storage_used  |  Gebruikte back-upopslag  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  connections_failed  |  Mislukte verbindingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  cpu_percent  |  CPU-percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  io_consumption_percent  |  IO procent  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  memory_percent  |  Geheugenpercentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  network_bytes_egress  |  Netwerk uit  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  network_bytes_ingress  |  Netwerk in  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  seconds_behind_master  |  Replicatievertraging in seconden  |  Count  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_limit  |  Opslaglimiet voor serverlogboeken  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_percent  |  Opslagpercentage serverlogboeken  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_usage  |  Serverlogboekopslag gebruikt  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  storage_limit  |  Opslaglimiet  |  Bytes  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  storage_percent  |  Opslagpercentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  storage_used  |  Gebruikte opslag  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  active_connections  |  Actieve verbindingen  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  backup_storage_used  |  Gebruikte back-upopslag  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  connections_failed  |  Mislukte verbindingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  cpu_percent  |  CPU-percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  io_consumption_percent  |  IO procent  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  memory_percent  |  Geheugenpercentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  network_bytes_egress  |  Netwerk uit  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  network_bytes_ingress  |  Netwerk in  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  seconds_behind_master  |  Replicatievertraging in seconden  |  Count  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_limit  |  Opslaglimiet voor serverlogboeken  |  Bytes  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_percent  |  Opslagpercentage serverlogboeken  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_usage  |  Serverlogboekopslag gebruikt  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  storage_limit  |  Opslaglimiet  |  Bytes  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  storage_percent  |  Opslagpercentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  storage_used  |  Gebruikte opslag  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  active_connections  |  Actieve verbindingen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  backup_storage_used  |  Gebruikte back-upopslag  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  connections_failed  |  Mislukte verbindingen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  cpu_percent  |  CPU-percentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  io_consumption_percent  |  IO procent  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  memory_percent  |  Geheugenpercentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_egress  |  Netwerk uit  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_ingress  |  Netwerk in  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_bytes  |  Max Lag Over Replica's  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_seconds  |  Replica Lag  |  Seconden  |  Maximum | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_limit  |  Opslaglimiet voor serverlogboeken  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_percent  |  Opslagpercentage serverlogboeken  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_usage  |  Serverlogboekopslag gebruikt  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  storage_limit  |  Opslaglimiet  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  storage_percent  |  Opslagpercentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  storage_used  |  Gebruikte opslag  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/serversv2  |  active_connections  |  Actieve verbindingen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/serversv2  |  cpu_percent  |  CPU-percentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/serversv2  |  iops iops  |  IOPS  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/serversv2  |  memory_percent  |  Geheugenpercentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_egress  |  Netwerk uit  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_ingress  |  Netwerk in  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/serversv2  |  storage_percent  |  Opslagpercentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/serversv2  |  storage_used  |  Gebruikte opslag  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/Account  |  digitaltwins.telemetry.nodes  |  Tijdelijke aanduiding Digital Twins Node Telemetrie  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.abandon.success  |  C2D-berichten verlaten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.complete.success  |  C2D-berichtleveringen voltooid  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.reject.success  |  C2D-berichten afgewezen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.methods.failure  |  Mislukte directe aanroepen van methoden  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.methods.requestSize  |  Aanvraaggrootte van directe methode-aanroepingen  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.methods.responseSize  |  Reactiegrootte van directe aanroepingen van methoden  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.methods.success  |  Succesvolle directe methode aanroepingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.failure  |  Mislukte tweeling leest van back-end  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.size  |  Reactiegrootte van tweeling leest van back-end  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.success  |  Succesvolle tweeling leest van back-end  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.failure  |  Mislukte dubbele updates van back-end  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.size  |  Grootte van dubbele updates van back-end  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.success  |  Succesvolle dubbele updates van back-end  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  C2D-berichten verlopen  |  Verlopen C2D-berichten (voorbeeld)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  Configuraties  |  Configuratiestatistieken  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Devices/IotHubs  |  connectedDeviceCount  |  Verbonden apparaten (voorbeeld)  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.builtIn.events  |  Routering: berichten die worden bezorgd aan berichten/gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.eventHubs  |  Routering: berichten geleverd aan Event Hub  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusQueues  |  Routering: berichten die worden bezorgd in de wachtrij voor servicebussen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusTopics  |  Routering: berichten geleverd aan Service Bus Topic  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage  |  Routering: berichten die in de opslag worden bezorgd  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.blobs  |  Routering: blobs geleverd aan opslag  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.bytes  |  Routering: gegevens die aan opslag worden geleverd  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.builtIn.events  |  Routering: berichtlatentie voor berichten/gebeurtenissen  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.eventHubs  |  Routering: berichtlatentie voor gebeurtenishub  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusQueues  |  Routering: berichtlatentie voor servicebuswachtrij  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusTopics  |  Routering: berichtlatentie voor servicebusonderwerp  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.storage  |  Routering: berichtlatentie voor opslag  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.dropped  |  Routering: telemetrieberichten verwijderd   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.fallback  |  Routering: berichten geleverd aan fallback  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.invalid  |  Routering: telemetrieberichten onverenigbaar  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.orphaned  |  Routering: telemetrieberichten verweesd   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.success  |  Routering: telemetrieberichten geleverd  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetrie.ingress.allProtocol  |  Telemetriebericht verzendt pogingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.sendThrottle  |  Aantal beperkingsfouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetrie.ingress.succes  |  Verzonden telemetrieberichten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.failure  |  Mislukte tweeling leest van apparaten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.size  |  Reactiegrootte van dubbele leest van apparaten  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.success  |  Succesvolle tweeling leest van apparaten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.failure  |  Mislukte dubbele updates van apparaten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.size  |  Grootte van dubbele updates van apparaten  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.success  |  Succesvolle dubbele updates van apparaten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  dailyMessageQuotaGebruikt  |  Totaal aantal gebruikte berichten  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  deviceDataUsage deviceDataUsage  |  Totaal gebruik van apparaatgegevens  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  deviceDataUsageV2  |  Totaal apparaatgegevensgebruik (voorbeeld)  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  devices.connectedDevices.allProtocol  |  Verbonden apparaten (afgeschaft)   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  devices.totalDevices  |  Totaal aantal apparaten (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  EventGridDeliveries EventGridDeliveries EventGridDeliveries EventGrid  |  Gebeurtenisgridleveringen(preview)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  EventGridLatentie  |  Latentie van gebeurtenisraster (voorbeeld)  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.failure  |  Mislukte annuleringen van werk  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.success  |  Succesvolle annuleringen van vacatures  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  vacatures.voltooid  |  Voltooide taken  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.failure  |  Mislukte creaties van aanroeptaken voor methoden  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.success  |  Succesvolle creaties van methode aanroepbanen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.failure  |  Mislukte creaties van twin update-taken  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.success  |  Succesvolle creaties van twin update jobs  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.failed  |  Mislukte taken  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.failure  |  Mislukte oproepen om taken weer te geven  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.success  |  Succesvolle oproepen om vacatures op te noemen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.failure  |  Mislukte query's  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.success  |  Succesvolle zoekopdrachten  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Devices/IotHubs  |  totalDeviceCount totalDeviceCount  |  Totaal aantal apparaten (voorbeeld)  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  twinQueries.failure  |  Mislukte dubbele query's  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  twinQueries.resultSize  |  De resultaatgrootte van twee query's  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  twinQueries.success  |  Succesvolle dubbele query's  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/provisioningServices  |  AttestationPogingen  |  Attestpogingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/provisioningServices  |  Apparaattoewijzingen  |  Toegewezen apparaten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/provisioningServices  |  Registratiepogingen  |  Registratiepogingen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  Beschikbare opslagruimte  |  Beschikbare opslagruimte  |  Bytes  |  Totaal | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  CassandraConnectionSluitingen  |  Cassandra Connection Sluitingen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequestCharges  |  Cassandra Verzoek Kosten  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequests  |  Cassandra Verzoeken  |  Count  |  Count | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  DataGebruik  |  Gegevensgebruik  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  DeleteVirtualNetwork  |  DeleteVirtualNetwork  |  Count  |  Count | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  Documenttelling  |  Documentaantal  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  Documentquota  |  Documentquota  |  Bytes  |  Totaal | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  IndexGebruik  |  Indexgebruik  |  Bytes  |  Totaal | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  MetagegevensVerzoeken  |  Metagegevensaanvragen  |  Count  |  Count | 
| **Ja**  | **Ja** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequest  |  Mongo Aanvraag Kosten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequests  |  Mongo-verzoeken  |  Count  |  Count | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  Aantal mongoverzoeken  |  Mongo-aanvraagtarief  |  CountPerSeconde  |  Average | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsDelete  |  Aanvraagtarief voor mongo verwijderen  |  CountPerSeconde  |  Average | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsInsert  |  Aanvraagpercentage voor invoegen van Mongo  |  CountPerSeconde  |  Average | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsQuery  |  Mongo-aanvraagpercentage  |  CountPerSeconde  |  Average | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsUpdate  |  Mongo-updateaanvraagpercentage  |  CountPerSeconde  |  Average | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  Voorzieningen  |  Ingerichte doorvoer  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  Replicatielatentie  |  Laattie voor P99-replicatie  |  Milliseconden  |  Average | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  Beschikbaarheid van service  |  Beschikbaarheid van service  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequests  |  Totaal aantal aanvragen  |  Count  |  Count | 
| **Ja**  | **Ja** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequestUnits  |  Eenheden voor totale aanvragen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EnterpriseKnowledgeGraph/services  |  Aantal fouten  |  Aantal mislukt  |  Count  |  Count | 
| Nee  | Nee |  Microsoft.EnterpriseKnowledgeGraph/services  |  SuccesTelling  |  Aantal geslaagd  |  Count  |  Count | 
| Nee  | Nee |  Microsoft.EnterpriseKnowledgeGraph/services  |  SuccesLatentie  |  Succes latentie  |  Milliseconden  |  Average | 
| Nee  | Nee |  Microsoft.EnterpriseKnowledgeGraph/services  |  Aantal transacties  |  Aantal transacties  |  Count  |  Count | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/domeinen  |  DeadLetteredCount DeadLetteredCount DeadLetteredCount DeadLetter  |  Dode letterde gebeurtenissen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventGrid/domeinen  |  DeliveryAttemptFailCount  |  Mislukte gebeurtenissen voor bezorging  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/domeinen  |  DeliverySuccessCount  |  Geleverde evenementen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventGrid/domeinen  |  DestinationProcessingDurationInMs  |  Duur bestemmingsverwerking  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/domeinen  |  Aantal gevallengebeurtenissen  |  Gebeurtenissen laten vallen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/domeinen  |  MatchedEventCount  |  Overeenkomende gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/domeinen  |  Aantal publishfailfail  |  Mislukte gebeurtenissen publiceren  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/domeinen  |  SuccessCount publiceren  |  Gepubliceerde evenementen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/domeinen  |  PublicerenSuccessLatencyInMs  |  Succeslatentie publiceren  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/eventAbonnementen  |  DeadLetteredCount DeadLetteredCount DeadLetteredCount DeadLetter  |  Dode letterde gebeurtenissen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventGrid/eventAbonnementen  |  DeliveryAttemptFailCount  |  Mislukte gebeurtenissen voor bezorging  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/eventAbonnementen  |  DeliverySuccessCount  |  Geleverde evenementen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventGrid/eventAbonnementen  |  DestinationProcessingDurationInMs  |  Duur bestemmingsverwerking  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/eventAbonnementen  |  Aantal gevallengebeurtenissen  |  Gebeurtenissen laten vallen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/eventAbonnementen  |  MatchedEventCount  |  Overeenkomende gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/extensionOnderwerpen  |  Aantal publishfailfail  |  Mislukte gebeurtenissen publiceren  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/extensionOnderwerpen  |  SuccessCount publiceren  |  Gepubliceerde evenementen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/extensionOnderwerpen  |  PublicerenSuccessLatencyInMs  |  Succeslatentie publiceren  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/extensionOnderwerpen  |  Ongeëvenaardaantal gebeurtenissen  |  Ongeëvenaarde evenementen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/onderwerpen  |  Aantal publishfailfail  |  Mislukte gebeurtenissen publiceren  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/onderwerpen  |  SuccessCount publiceren  |  Gepubliceerde evenementen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/onderwerpen  |  PublicerenSuccessLatencyInMs  |  Succeslatentie publiceren  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/onderwerpen  |  Ongeëvenaardaantal gebeurtenissen  |  Ongeëvenaarde evenementen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/clusters  |  Actieve verbindingen  |  Actieve verbindingen  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.EventHub/clusters  |  BeschikbaarGeheugen  |  Beschikbaar geheugen  |  Percentage  |  Maximum | 
| Nee  | Nee |  Microsoft.EventHub/clusters  |  VastleggenBacklog  |  Leg Backlog vast.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/clusters  |  CapturedBytes  |  Vastgelegde bytes.  |  Bytes  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/clusters  |  CapturedMessages  |  Vastgelegde berichten.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/clusters  |  Verbindingengesloten  |  Verbindingen gesloten.  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.EventHub/clusters  |  Verbindingengeopend  |  Verbindingen geopend.  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.EventHub/clusters  |  CPU  |  CPU  |  Percentage  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.EventHub/clusters  |  Inkomende Bytes  |  Binnenkomende bytes.  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/clusters  |  Binnenkomende berichten  |  Binnenkomende berichten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/clusters  |  Binnenkomende aanvragen  |  Binnenkomende aanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/clusters  |  Uitgaande bytes  |  Uitgaande bytes.  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/clusters  |  Uitgaande Berichten  |  Uitgaande berichten  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/clusters  |  Quotaoverschre's  |  Quotum overschreden fouten.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/clusters  |  Serverfouten  |  Serverfouten.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/clusters  |  Succesvolle verzoeken  |  Succesvolle aanvragen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/clusters  |  ThrottledRequests  |  Throttled Requests.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/clusters  |  Gebruikersfouten  |  Gebruikersfouten.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/naamruimten  |  Actieve verbindingen  |  Actieve verbindingen  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.EventHub/naamruimten  |  VastleggenBacklog  |  Leg Backlog vast.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/naamruimten  |  CapturedBytes  |  Vastgelegde bytes.  |  Bytes  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/naamruimten  |  CapturedMessages  |  Vastgelegde berichten.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/naamruimten  |  Verbindingengesloten  |  Verbindingen gesloten.  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.EventHub/naamruimten  |  Verbindingengeopend  |  Verbindingen geopend.  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.EventHub/naamruimten  |  EHABL EHABL  |  Archiefbacklogberichten (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/naamruimten  |  EHAMBS  |  Archiefberichtdoorvoer (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/naamruimten  |  EHAMSGS  |  Archiefberichten (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/naamruimten  |  EHINBYTES  |  Binnenkomende bytes (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/naamruimten  |  EHINMBS  |  Binnenkomende bytes (verouderd) (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/naamruimten  |  EHINMSGS  |  Binnenkomende berichten (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/naamruimten  |  EHOUTBYTES  |  Uitgaande bytes (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/naamruimten  |  EHOUTMBS  |  Uitgaande bytes (verouderd) (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/naamruimten  |  EHOUTMSGS  |  Uitgaande berichten (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/naamruimten  |  FAILREQ FAILREQ  |  Mislukte aanvragen (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/naamruimten  |  Inkomende Bytes  |  Binnenkomende bytes.  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/naamruimten  |  Binnenkomende berichten  |  Binnenkomende berichten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/naamruimten  |  Binnenkomende aanvragen  |  Binnenkomende aanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/naamruimten  |  INMSGS INMSGS  |  Binnenkomende berichten (verouderd) (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/naamruimten  |  INREQS  |  Binnenkomende aanvragen (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/naamruimten  |  INTERR (INTERR)  |  Interne serverfouten (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/naamruimten  |  MISCERR (MISCERR)  |  Andere fouten (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/naamruimten  |  Uitgaande bytes  |  Uitgaande bytes.  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/naamruimten  |  Uitgaande Berichten  |  Uitgaande berichten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/naamruimten  |  OUTMSGS OUTMSGS  |  Uitgaande berichten (verouderd) (afgeschaft)  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/naamruimten  |  Quotaoverschre's  |  Quotum overschreden fouten.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/naamruimten  |  Serverfouten  |  Serverfouten.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/naamruimten  |  Grootte  |  Grootte  |  Bytes  |  Average | 
| Nee  | Nee |  Microsoft.EventHub/naamruimten  |  Succesvolle verzoeken  |  Succesvolle aanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/naamruimten  |  SUCCREQ SUCCREQ  |  Succesvolle aanvragen (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/naamruimten  |  SVRBSY (SVRBSY)  |  Serverdrukfouten (afgeschaft)  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/naamruimten  |  ThrottledRequests  |  Throttled Requests.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/naamruimten  |  Gebruikersfouten  |  Gebruikersfouten.  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.HDInsight/clusters  |  Gecategoriseerde gatewayaanvragen  |  Gecategoriseerde gatewayaanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.HDInsight/clusters  |  Gatewayrequests  |  Gatewayaanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.HDInsight/clusters  |  NumActiveWorkers  |  Aantal actieve werknemers  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.HDInsight/clusters  |  Aanvragen schalen  |  Aanvragen schalen  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Insights/Instellingen voor automatisch schalen  |  Metrische drempelwaarde  |  Metrische drempelwaarde  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Insights/Instellingen voor automatisch schalen  |  WaargenomenCapaciteit  |  Waargenomen capaciteit  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Insights/Instellingen voor automatisch schalen  |  WaargenomenMetrische Waarde  |  Waargenomen metrische waarde  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Insights/Instellingen voor automatisch schalen  |  ScaleActionsInitiated  |  Schaalacties gestart  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Insights/Componenten  |  beschikbaarheidResultaten/beschikbaarheidPercentage  |  Beschikbaarheid  |  Percentage  |  Average | 
| Nee  | Nee |  Microsoft.Insights/Componenten  |  beschikbaarheidResultaten/aantal  |  Beschikbaarheidstests  |  Count  |  Count | 
| **Ja**  | Nee |  Microsoft.Insights/Componenten  |  beschikbaarheidResultaten/duur  |  Duur beschikbaarheidstest  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Insights/Componenten  |  browserTimings/netwerkDuur  |  Verbindingstijd van het laden van pagina's  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Insights/Componenten  |  browserTimings/verwerkingDuur  |  Verwerkingstijd van de client  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Insights/Componenten  |  browserTimings/receiveDuration  |  Ontvangstvan de reactietijd  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Insights/Componenten  |  browserTimings/sendDuration  |  Aanvraagtijd verzenden  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Insights/Componenten  |  browserTimings/totalDuration  |  Laadtijd van browserpagina's  |  Milliseconden  |  Average | 
| Nee  | Nee |  Microsoft.Insights/Componenten  |  afhankelijkheden/aantal  |  Afhankelijkheidsoproepen  |  Count  |  Count | 
| **Ja**  | Nee |  Microsoft.Insights/Componenten  |  afhankelijkheden/duur  |  Afhankelijkheidsduur  |  Milliseconden  |  Average | 
| Nee  | Nee |  Microsoft.Insights/Componenten  |  afhankelijkheden/mislukt  |  Fouten in afhankelijkheidsoproepen  |  Count  |  Count | 
| Nee  | Nee |  Microsoft.Insights/Componenten  |  uitzonderingen/browser  |  Browseruitzonderingen  |  Count  |  Count | 
| **Ja**  | **Ja** |  Microsoft.Insights/Componenten  |  uitzonderingen/telling  |  Uitzonderingen  |  Count  |  Count | 
| Nee  | Nee |  Microsoft.Insights/Componenten  |  uitzonderingen/server  |  Serveruitzonderingen  |  Count  |  Count | 
| **Ja**  | **Ja** |  Microsoft.Insights/Componenten  |  pageViews/count pageViews/count pageViews/count pageViews  |  Paginaweergaven  |  Count  |  Count | 
| **Ja**  | Nee |  Microsoft.Insights/Componenten  |  pageViews/duur  |  Laadtijd van de paginaweergave  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Insights/Componenten  |  prestatietellers/exceptionsPerSeconde  |  Uitzonderingstarief  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Insights/Componenten  |  performanceCounters/geheugenAvailableBytes  |  Beschikbaar geheugen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Insights/Componenten  |  performanceCounters/processCpuPercentage  |  CPU verwerken  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Insights/Componenten  |  performanceCounters/processIOBytesPerSeconde  |  Io-snelheid verwerken  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Insights/Componenten  |  performanceCounters/processorCpuPercentage  |  Processortijd  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Insights/Componenten  |  performanceCounters/processPrivateBytes  |  Privébytes verwerken  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Insights/Componenten  |  performanceCounters/requestExecutionTime  |  HTTP-aanvraaguitvoeringstijd  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Insights/Componenten  |  performanceCounters/requestsInQueue  |  HTTP-aanvragen in toepassingswachtrij  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Insights/Componenten  |  prestatietellers/aanvragenPerSeconde  |  HTTP-aanvraagpercentage  |  CountPerSeconde  |  Average | 
| Nee  | Nee |  Microsoft.Insights/Componenten  |  aanvragen/tellen  |  Serveraanvragen  |  Count  |  Count | 
| **Ja**  | Nee |  Microsoft.Insights/Componenten  |  verzoeken/duur  |  Serverresponstijd  |  Milliseconden  |  Average | 
| Nee  | Nee |  Microsoft.Insights/Componenten  |  aanvragen/mislukt  |  Mislukte aanvragen  |  Count  |  Count | 
| Nee  | Nee |  Microsoft.Insights/Componenten  |  verzoeken/tarief  |  Serveraanvraagsnelheid  |  CountPerSeconde  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Insights/Componenten  |  sporen/telling  |  Traceringen  |  Count  |  Count | 
| **Ja**  | Nee |  Microsoft.KeyVault/kluizen  |  Serviceapihit (ServiceApiHit)  |  Total Service Api Hits  |  Count  |  Count | 
| **Ja**  | Nee |  Microsoft.KeyVault/kluizen  |  ServiceApiLatentie  |  Algemene latentie van service-api's  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.KeyVault/kluizen  |  ServiceApiResult  |  Resultaten van de Total Service Api  |  Count  |  Count | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  CacheGebruik  |  Cachegebruik  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  ContinuExportMaxLatenessMinutes  |  Continue export Max Lateness Minuten  |  Count  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  ContinuexportNumOfRecordsExported  |  Continue uitvoer - aantal uitgevoerde registers  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  ContinuExportPendingCount  |  Aantal continue export in behandeling  |  Count  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  ContinuExportResultaat  |  Continu exportresultaat  |  Count  |  Count | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  CPU  |  CPU  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  EventsProcessedForEventHubs  |  Gebeurtenissen verwerkt (voor gebeurtenis/IoT-hubs)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  ExportGebruik  |  Gebruik exporteren  |  Percentage  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  IngestionLatencyInSeconden  |  Innamelatentie (in seconden)  |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  OpnameResultaat  |  Innameresultaat  |  Count  |  Count | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  Opnamegebruik  |  Innamegebruik  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  InnameVolumeInMB  |  Innamevolume (in MB)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  Keepalive  |  In leven blijven  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  Queryduur  |  Queryduur  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  SteamingingestRequestRate (SteamingingestRequestRate)  |  Percentage streamingaanvragen  |  Count  |  RateRequestsPerSeconde | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  StreamingIngestDataRate  |  Gegevenssnelheid streamen  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  StreamingIngestDuration  |  Streaming Ingest Duur  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  StreamingIngestResults  |  Streaming Ingest Resultaat  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Logic/integrationServiceEnvironments  |  Actielatentie  |  Actielatentie   |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  ActiesVoltooid  |  Acties voltooid   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  Actiesmislukt  |  Acties zijn mislukt   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  ActiesOvergeslagen  |  Acties overgeslagen   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  ActiesGestart  |  Acties gestart   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  ActiesGeslaagd  |  Acties geslaagd   |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Logic/integrationServiceEnvironments  |  ActionSuccessLatentie  |  Latentie voor actiesucces   |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionThrottledEvents  |  Actie-aan-eengasste gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Logic/integrationServiceEnvironments  |  IntegratieserviceEnvironmentConnectorMemoryUsage  |  Geheugengebruik connector voor integratieserviceomgeving  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Logic/integrationServiceEnvironments  |  IntegratieServiceEnvironmentConnectorProcessorUsage  |  Gebruik van connectorprocessor voor integratieserviceomgeving  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Logic/integrationServiceEnvironments  |  IntegratieServiceEnvironmentWorkflowMemoryUsage  |  Gebruik van werkstroomgeheugen voor integratieserviceomgeving  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Logic/integrationServiceEnvironments  |  IntegratieServiceEnvironmentWorkflowProcessorUsage  |  Gebruik van werkstroomprocessor voor integratieserviceomgeving  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  RunFailurePercentage  |  Percentage mislukte uitvoeren  |  Percentage  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Logic/integrationServiceEnvironments  |  RunLatentie  |  Latentie uitvoeren  |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsCancelled  |  Runs geannuleerd  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsVoltooid  |  Voltooide runs  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsFailed  |  Runs zijn mislukt  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsStarted  |  Runs gestart  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsGeslaagd  |  Runs geslaagd  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  RunStartThrottledEvents  |  Gebeurtenissen met throttled starten uitvoeren  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Logic/integrationServiceEnvironments  |  RunSuccessLatency RunSuccessLatency  |  Succeslatentie uitvoeren  |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  RunThrottledEvents  |  Loopthrottled gebeurtenissen uit  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerFireLatentie  |  Vuurlatentie activeren   |  Seconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerLatentie  |  Triggerlatentie   |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersVoltooid  |  Triggers voltooid   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  Triggers mislukt  |  Triggers zijn mislukt   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersFired  |  Triggers afgevuurd   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersOvergeslagen  |  Triggers overgeslagen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersStarted  |  Triggers gestart   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersGeslaagd  |  Triggers geslaagd   |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerSuccessLatency  |  Latentie van succes activeren   |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerThrottledGebeurtenissen  |  Gebeurtenissen met gaspedaal activeren  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Logic/werkstromen  |  Actielatentie  |  Actielatentie   |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  ActiesVoltooid  |  Acties voltooid   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  Actiesmislukt  |  Acties zijn mislukt   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  ActiesOvergeslagen  |  Acties overgeslagen   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  ActiesGestart  |  Acties gestart   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  ActiesGeslaagd  |  Acties geslaagd   |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Logic/werkstromen  |  ActionSuccessLatentie  |  Latentie voor actiesucces   |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  ActionThrottledEvents  |  Actie-aan-eengasste gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  FactureerbareActie-executies  |  Factureerbare actie-uitvoeringen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  FactureerbareTriggerExecutions  |  Factureerbare triggeruitvoeringen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  BillingUsageNativeOperation  |  Factureringsgebruik voor uitvoeringen van native bewerkingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  BillingUsageNativeOperation  |  Factureringsgebruik voor uitvoeringen van native bewerkingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  FactureringGebruikSstandaardconnector  |  Factureringsgebruik voor standaardconnectoruitvoeringen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  FactureringGebruikSstandaardconnector  |  Factureringsgebruik voor standaardconnectoruitvoeringen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  FactureringUsageStorageVerbruik  |  Factureringsgebruik voor uitvoer van opslagverbruik  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  FactureringUsageStorageVerbruik  |  Factureringsgebruik voor uitvoer van opslagverbruik  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  RunFailurePercentage  |  Percentage mislukte uitvoeren  |  Percentage  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Logic/werkstromen  |  RunLatentie  |  Latentie uitvoeren  |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  RunsCancelled  |  Runs geannuleerd  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  RunsVoltooid  |  Voltooide runs  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  RunsFailed  |  Runs zijn mislukt  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  RunsStarted  |  Runs gestart  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  RunsGeslaagd  |  Runs geslaagd  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  RunStartThrottledEvents  |  Gebeurtenissen met throttled starten uitvoeren  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Logic/werkstromen  |  RunSuccessLatency RunSuccessLatency  |  Succeslatentie uitvoeren  |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  RunThrottledEvents  |  Loopthrottled gebeurtenissen uit  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  TotalBillableExecutions TotalBillableExecutions TotalBillableExecutions TotalBill  |  Totale factureerbare uitvoeringen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Logic/werkstromen  |  TriggerFireLatentie  |  Vuurlatentie activeren   |  Seconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Logic/werkstromen  |  TriggerLatentie  |  Triggerlatentie   |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  TriggersVoltooid  |  Triggers voltooid   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  Triggers mislukt  |  Triggers zijn mislukt   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  TriggersFired  |  Triggers afgevuurd   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  TriggersOvergeslagen  |  Triggers overgeslagen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  TriggersStarted  |  Triggers gestart   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  TriggersGeslaagd  |  Triggers geslaagd   |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Logic/werkstromen  |  TriggerSuccessLatency  |  Latentie van succes activeren   |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Logic/werkstromen  |  TriggerThrottledGebeurtenissen  |  Gebeurtenissen met gaspedaal activeren  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/werkruimten  |  Actieve kernen  |  Actieve kernen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/werkruimten  |  Actieve knooppunten  |  Actieve knooppunten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/werkruimten  |  Voltooide uitvoeringen  |  Voltooide uitvoeringen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/werkruimten  |  Mislukte uitvoeringen  |  Mislukte uitvoeringen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/werkruimten  |  Niet-actieve kernen  |  Niet-actieve kernen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/werkruimten  |  Inactieve knooppunten  |  Inactieve knooppunten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/werkruimten  |  Kernen verlaten  |  Kernen verlaten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/werkruimten  |  Knooppunten verlaten  |  Knooppunten verlaten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/werkruimten  |  Modeldeploy mislukt  |  Modeldeploy mislukt  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/werkruimten  |  Modeldeploy gestart  |  Modeldeploy gestart  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/werkruimten  |  Modeldeploy geslaagd  |  Modeldeploy geslaagd  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/werkruimten  |  Modelregister is mislukt  |  Modelregister is mislukt  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/werkruimten  |  Modelregister geslaagd  |  Modelregister geslaagd  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/werkruimten  |  Voorrang kernen  |  Voorrang kernen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/werkruimten  |  Voorkoming van knooppunten  |  Voorkoming van knooppunten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/werkruimten  |  Percentage quotumgebruik  |  Percentage quotumgebruik  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/werkruimten  |  Gestarte runs  |  Gestarte runs  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/werkruimten  |  Totaal aantal kernen  |  Totaal aantal kernen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/werkruimten  |  Totaal aantal knooppunten  |  Totaal aantal knooppunten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/werkruimten  |  Onbruikbare kernen  |  Onbruikbare kernen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/werkruimten  |  Onbruikbare knooppunten  |  Onbruikbare knooppunten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Maps/accounts  |  Beschikbaarheid  |  Beschikbaarheid  |  Percentage  |  Average | 
| Nee  | Nee |  Microsoft.Maps/accounts  |  Gebruik  |  Gebruik  |  Count  |  Count | 
| **Ja**  | Nee |  Microsoft.Media/mediaservices  |  AssetCount (AssetCount)  |  Aantal activa  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Media/mediaservices  |  AssetQuota  |  Activaquotum  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Media/mediaservices  |  AssetQuotaUsedPercentage  |  Gebruikt actiefquotum  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Media/mediaservices  |  ContentKeyPolicyCount  |  Aantal inhoudssleutelbeleidsfactoren  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Media/mediaservices  |  ContentKeyPolicyQuota  |  Inhoudssleutelbeleidsquotum  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Media/mediaservices  |  ContentKeyPolicyQuotaUsedPercentage  |  Gebruikt gebruikt quotum voor inhoudssleutelbeleid  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Media/mediaservices  |  Aantal streamingbeleid  |  Aantal streamingbeleid  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Media/mediaservices  |  StreamingPolicyQuota  |  Quotum streamingbeleid  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Media/mediaservices  |  StreamingPolicyQuotaUsedPercentage  |  Gebruikt percentage streamingbeleid  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Media/mediaservices/streamingEindpunten  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Media/mediaservices/streamingEindpunten  |  Aanvragen  |  Aanvragen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Media/mediaservices/streamingEindpunten  |  SuccesE2ELatentie  |  Succes end-to-end Latency  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Microservices4Spring/appClusters  |  GCPauseTotalCount  |  GC-pauzetelling  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Microservices4Spring/appClusters  |  GCPauseTotalTime  |  Totale tijd van GC-pauze  |  Milliseconden  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Microservices4Spring/appClusters  |  MaxOldGenMemoryPoolBytes  |  Max beschikbare oude generatie gegevensgrootte  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Microservices4Spring/appClusters  |  OldGenMemoryPoolBytes  |  Gegevensgrootte van oude generatie  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Microservices4Spring/appClusters  |  OldGenPromotedBytes  |  Bevorderen tot gegevensgrootte van oude generatie  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Microservices4Spring/appClusters  |  ServiceCpuUsagePercentage  |  Gebruikspercentage service-CPU's  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Microservices4Spring/appClusters  |  ServiceMemoryVastgelegd  |  Toegewezen servicegeheugen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Microservices4Spring/appClusters  |  ServiceMemoryMax  |  Servicegeheugen Max  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Microservices4Spring/appClusters  |  Servicegeheugengebruikt  |  Servicegeheugen gebruikt  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Microservices4Spring/appClusters  |  SystemCpuUsagePercentage SystemCpuUsagePercentage SystemCpuUsagePercentage SystemCpu  |  Gebruikspercentage systeem-CPU-gebruik  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Microservices4Spring/appClusters  |  TomcatErrorCount  |  Tomcat-globale fout  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Microservices4Spring/appClusters  |  TomcatReceivedBytes  |  Tomcat Totaal ontvangen bytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestMaxTime  |  Tomcat Request Max Time  |  Milliseconden  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestTotalCount  |  Totaal aantal aanvragen voor Tomcat-aanvragen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Microservices4Spring/appClusters  |  TomcatRequestTotalTime  |  Totaalaantal aanvragen tomcat-aanvraag  |  Milliseconden  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Microservices4Spring/appClusters  |  TomcatResponseAvgTime  |  Gemiddelde tijd voor Tomcat-aanvraag  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Microservices4Spring/appClusters  |  TomcatSentBytes  |  Tomcat Totaal verzonden bytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionActiveCurrentCount  |  Tomcat Sessie Alive Count  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionActiveMaxCount  |  Tomcat Session Max Active Count  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionAliveMaxTijd  |  Tomcat Sessie Max Alive Time  |  Milliseconden  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionCreatedCount  |  Aantal gemaakt Tomcat-sessie  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionExpiredCount  |  Aantal verlopen tomcat-sessies  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Microservices4Spring/appClusters  |  TomcatSessionRejectedCount  |  Afgewezen aantal Tomcat-sessie  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Microservices4Spring/appClusters  |  YoungGenPromotedBytes YoungGenPromotedBytes YoungGenPromotedBytes YoungGen  |  Bevorderen tot gegevensgrootte van jonge generaties  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolToegewezengebruikt  |  Toegewezen volumepool gebruikt  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumepooltotallogicalSize  |  Volumepool totale logische grootte  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  Gemiddelde leeslatentie  |  Gemiddelde leeslatentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  Gemiddelde writelatentie  |  Gemiddelde schrijflatentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  ReadIops  |  Iops lezen  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  VolumeLogische grootte  |  Volumelogische grootte  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  VolumeSnapshotSize  |  Grootte van volumemomentopnamen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.NetApp/netAppAccounts/capacityPools/volumes  |  Schrijfiops  |  Iops schrijven  |  CountPerSeconde  |  Average | 
| Nee  | Nee |  Microsoft.Network/applicationGateways  |  ApplicationGatewayTotalTime  |  Totale tijd van toepassingsgateway  |  Milliseconden  |  Average | 
| Nee  | Nee |  Microsoft.Network/applicationGateways  |  AvgrequestCountPerHealthyHost  |  Aanvragen per minuut per gezonde verhuurder  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.Network/applicationGateways  |  Back-endConnectTime  |  Back-end verbindingstijd  |  Milliseconden  |  Average | 
| Nee  | Nee |  Microsoft.Network/applicationGateways  |  BackendFirstByteResponseTime  |  Backend Eerste byte-reactietijd  |  Milliseconden  |  Average | 
| Nee  | Nee |  Microsoft.Network/applicationGateways  |  BackendLastByteResponseTime  |  Laatste reactietijd van back-end  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/applicationGateways  |  BackendResponseStatus  |  Status backend-antwoord  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/applicationGateways  |  Geblokkeerdaantal  |  Regelverdeling van de regels voor geblokkeerde webtoepassingsfirewall  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/applicationGateways  |  GeblokkeerdEReqCount  |  Aantal geblokkeerde aanvragen voor webtoepassingsfirewall  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/applicationGateways  |  Ontvangen bytes  |  Ontvangen bytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/applicationGateways  |  BytesSent  |  Verzonden bytes  |  Bytes  |  Totaal | 
| Nee  | Nee |  Microsoft.Network/applicationGateways  |  Capaciteitseenheden  |  Huidige capaciteitseenheden  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.Network/applicationGateways  |  ClientRtt  |  Client RTT  |  Milliseconden  |  Average | 
| Nee  | Nee |  Microsoft.Network/applicationGateways  |  ComputeUnits  |  Huidige compute-eenheden  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/applicationGateways  |  Huidige verbindingen  |  Huidige verbindingen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/applicationGateways  |  Mislukte aanvragen  |  Mislukte aanvragen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/applicationGateways  |  HealthyHostCount HealthyHostCount HealthyHostCount HealthyHost  |  Aantal gezonde gasten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/applicationGateways  |  MatchedCount  |  Totale regelverdeling van firewallvoor webtoepassing  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/applicationGateways  |  Reactiestatus  |  Reactiestatus  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Network/applicationGateways  |  Doorvoer  |  Doorvoer  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/applicationGateways  |  TlsProtocol TlsProtocol  |  TLS-protocol voor client  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/applicationGateways  |  TotalRequests  |  Totaal aantal aanvragen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/applicationGateways  |  OngezondeHostCount  |  Aantal ongezonde hosten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/azurefirewalls  |  ApplicationRuleHit  |  Toepassingsregels raken het aantal  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/azurefirewalls  |  Gegevensverwerkt  |  Gegevens verwerkt  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/azurefirewalls  |  FirewallHealth  |  Firewallstatus  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/azurefirewalls  |  NetworkRuleHit (NetworkRuleHit)  |  Netwerkregels raken aantal  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/azurefirewalls  |  SNATPortUsage  |  SNAT-poortgebruik  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/verbindingen  |  BitsinperSeconde  |  BitsinperSeconde  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/verbindingen  |  BitsOutperSeconde  |  BitsOutperSeconde  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/dnszones  |  QueryVolume  |  Queryvolume  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Network/dnszones  |  RecordSetCapacityUsage RecordSetCapacityUsage RecordSetCapacityUsage RecordSet  |  Capaciteitsbenutting recordset  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/dnszones  |  RecordSetCount RecordSetCount RecordSetCount RecordSet  |  Recordsetaantal  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/expressRouteCircuits  |  ArpBeschikbaarheid  |  Arp beschikbaarheid  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/expressRouteCircuits  |  Beschikbaarheid van bgp  |  Beschikbaarheid van Bgp  |  Percentage  |  Average | 
| Nee  | Nee |  Microsoft.Network/expressRouteCircuits  |  BitsinperSeconde  |  BitsinperSeconde  |  CountPerSeconde  |  Average | 
| Nee  | Nee |  Microsoft.Network/expressRouteCircuits  |  BitsOutperSeconde  |  BitsOutperSeconde  |  CountPerSeconde  |  Average | 
| Nee  | Nee |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsInPerSeconde  |  GlobalReachBitsInPerSeconde  |  CountPerSeconde  |  Average | 
| Nee  | Nee |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsOutPerSeconde  |  GlobalReachBitsOutPerSeconde  |  CountPerSeconde  |  Average | 
| Nee  | Nee |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsInPerSeconde  |  DroppedinBitsPerSeconde  |  CountPerSeconde  |  Average | 
| Nee  | Nee |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsOutPerSeconde  |  DroppedOutBitsPerSeconde  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsinperSeconde  |  BitsinperSeconde  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsOutperSeconde  |  BitsOutperSeconde  |  CountPerSeconde  |  Average | 
| Nee  | Nee |  Microsoft.Network/expressRouteGateways  |  ErgatewayConnectionBitsInPerSeconde  |  BitsinperSeconde  |  CountPerSeconde  |  Average | 
| Nee  | Nee |  Microsoft.Network/expressRouteGateways  |  ErgatewayConnectionBitsOutSeconde  |  BitsOutperSeconde  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/expressRoutePorts  |  AdminState  |  AdminState  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/expressRoutePorts  |  LineProtocol  |  LineProtocol  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/expressRoutePorts  |  PortBitsInPerSeconde  |  BitsinperSeconde  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/expressRoutePorts  |  PortBitsOutPerSeconde  |  BitsOutperSeconde  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/expressRoutePorts  |  RxLightLevel RxLightLevel  |  RxLightLevel RxLightLevel  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/expressRoutePorts  |  TxLightLevel TxLightLevel  |  TxLightLevel TxLightLevel  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Network/voordeuren  |  Back-endHealthPercentage  |  Backend-statuspercentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Network/voordeuren  |  BackendRequestCount  |  Aantal back-endaanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Network/voordeuren  |  BackendRequestLatentie  |  Latentie voor back-endaanvragen  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Network/voordeuren  |  BillableResponseSize  |  Factuurbare reactiegrootte  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Network/voordeuren  |  Aantal aanvragen  |  Aantal aanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Network/voordeuren  |  RequestSize  |  Aanvraaggrootte  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Network/voordeuren  |  ResponseSize  |  Reactiegrootte  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Network/voordeuren  |  TotalLatency TotalLatency  |  Totale latentie  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Network/voordeuren  |  WebApplicationFirewallRequestCount  |  Aantal firewallaanvragen voor webtoepassingen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Network/loadBalancers  |  ToegewezenSnatPorts  |  Toegewezen SNAT-poorten (voorbeeld)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/loadBalancers  |  ByteCount ByteCount  |  Byte Telling  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/loadBalancers  |  DipBeschikbaarheid  |  Status van status status status van status van status  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/loadBalancers  |  Aantal pakketten  |  Aantal pakketten  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/loadBalancers  |  Aantal snat-verbindingen  |  Aantal SNAT-verbindingen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/loadBalancers  |  SYNCount  |  SYN-telling  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Network/loadBalancers  |  UsedSnatPorts  |  Gebruikte SNAT-poorten (voorbeeld)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/loadBalancers  |  Beschikbaarheid van vips  |  Beschikbaarheid van gegevenspad  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/networkInterfaces  |  BytesReceivedRate  |  Ontvangen bytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/networkInterfaces  |  BytesSentRate  |  Verzonden bytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/networkInterfaces  |  PakkettenReceivedRate  |  Ontvangen pakketten  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/networkInterfaces  |  PakkettenSentRate  |  Verzonden pakketten  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/networkWatchers/connectionMonitors  |  Gemiddelde RoundtripMs  |  Avg. Retourtijd (ms)  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/networkWatchers/connectionMonitors  |  Controlesmisluktprocent  |  Mislukt percentage (voorbeeld)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/networkWatchers/connectionMonitors  |  ProbesFailedPercent  |  % Sondes mislukt  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/networkWatchers/connectionMonitors  |  RoundTripTimeMs  |  Retourtijd (ms) (preview)  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  ByteCount ByteCount  |  Byte Telling  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  BytesDroppedDDoS  |  Inkomende bytes dropd DDoS  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  BytesForwardedDDoS  |  Inkomende bytes doorgestuurdd DDoS  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  BytesInddos  |  DDoS-binnenkomende bytes  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerSYNPackets DDoSTriggerSYNPackets  |  Binnenkomende SYN-pakketten om DDoS-mitigatie te activeren  |  CountPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  DDoStriggerTCPPackets  |  Binnenkomende TCP-pakketten om DDoS-mitigatie te activeren  |  CountPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerUDPPakketten  |  Binnenkomende UDP-pakketten om DDoS-mitigatie te activeren  |  CountPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  IfunderddosAttack IfUnderDDoSAttack  |  Onder DDoS-aanval of niet  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  Aantal pakketten  |  Aantal pakketten  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  PakkettenDroppedDDoS  |  Binnenkomende pakketten dropten DDoS  |  CountPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  PakkettenForwardedDDoS  |  Binnenkomende pakketten doorgestuurde DDoS  |  CountPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  PacketsindDos  |  Binnenkomende pakketten DDoS  |  CountPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  SynCount (SynCount)  |  SYN-telling  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  TCPBytesDroppedDDoS  |  Binnenkomende TCP-bytes ddos-bytes laten vallen  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  TCPBytesForwardedDDoS  |  Inkomende TCP-bytes doorgestuurde DDoS  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  TCPBytesInddos  |  Inkomende TCP-bytes DDoS  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  TCPPacketsDroppedDDoS  |  Binnenkomende TCP-pakketten hebben DDoS-pakketten laten vallen  |  CountPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  TCPPacketsForwardedDDoS  |  Binnenkomende TCP-pakketten doorgestuurdd DDoS  |  CountPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  TCPpacketsinddos  |  Binnenkomende TCP-pakketten DDoS  |  CountPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  UDPBytesDroppedDDoS  |  Binnenkomende UDP-bytes hebben DDoS-bytes laten vallen  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  UDPBytesForwardedDDoS  |  Inkomende UDP-bytes doorgestuurde DDoS  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  UDPBytesIndDoS  |  Binnenkomende UDP-bytes DDoS  |  BytesPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  UDPPacketsDroppedDDoS  |  Binnenkomende UDP-pakketten droppen DDoS  |  CountPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  UDPPacketsForwardedDDoS  |  Binnenkomende UDP-pakketten doorgestuurde DDoS  |  CountPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  UDPPacketsInDDoS  |  Binnenkomende UDP-pakketten DDoS  |  CountPerSeconde  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  Beschikbaarheid van vips  |  Beschikbaarheid van gegevenspad  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/trafficManagerProfiles  |  ProbeAgentCurrentEndpointStateByProfileResourceId  |  Eindpuntstatus op eindpunt  |  Count  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.Network/trafficManagerProfiles  |  QpsByEndpoint  |  Query's op endpoint geretourneerd  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  Gemiddelde bandbreedte  |  Gateway S2S-bandbreedte  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  P2SBandbreedte  |  Gateway P2S-bandbreedte  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  Aantal P2SConnection-verbindingen  |  Aantal P2S-verbindingen  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  TunnelGemiddeldebandbreedte  |  Tunnelbandbreedte  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressBytes  |  Bytes tunnel uitgang  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPacketDropTSMismatch  |  Tunnel Egress TS Mismatch Packet Drop  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPackets  |  Tunnel Egress Pakketten  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressBytes  |  Tunnel Ingress Bytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPacketDropTSMismatch  |  Tunnel Ingress TS Mismatch Packet Drop  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPackets  |  Tunnel Ingress Pakketten  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworks  |  PingmeshAverageRoundtripMs  |  Retourtijd voor pings naar een vm  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworks  |  PingMeshProbesFailedPercent  |  Mislukte pings naar een vm  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  Inkomende  |  Binnenkomende berichten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  inkomende.alle.mislukte aanvragen  |  Alle binnenkomende mislukte aanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  inkomende.all.requests  |  Alle binnenkomende aanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  inkomend.gepland  |  Geplande pushmeldingen verzonden  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  inkomende.gepland.annuleren  |  Geplande pushmeldingen geannuleerd  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.all  |  Installatiebeheerbewerkingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.delete  |  Installatiebewerkingen verwijderen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.get  |  Installatiebewerkingen uitvoeren  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.patch  |  Installatiebewerkingen voor patch  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.upsert  |  Installatiebewerkingen maken of bijwerken  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  notificationhub.pushes  |  Alle uitgaande meldingen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande.allpns.badorexpiredchannel  |  Foute of verlopen kanaalfouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande.allpns.channelerror  |  Kanaalfouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.invalidpayload  |  Payload-fouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande.allpns.pnsfout  |  Fouten in extern meldingssysteem  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande.allpns.succes  |  Succesvolle meldingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande.apns.badchannel  |  Apns Slechte Kanaalfout  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande.apns.verlopenkanaal  |  Apns-verlopen kanaalfout  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande.apns.invalidereferenties  |  Apns-autorisatiefouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidnotificationsize  |  Apns ongeldige meldingsgrootte fout  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande.apns.pnsfout  |  APNS-fouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.success  |  APNS-succesvolle meldingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande.gcm.authenticationfout  |  GCM-verificatiefouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.badchannel  |  GCM Bad Channel-fout  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande.gcm.verlopenkanaal  |  GCM-fout verlopen kanaal  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande.gcm.ongeldige referenties  |  GCM-autorisatiefouten (ongeldige referenties)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande.gcm.invalidenotatie  |  GCM Ongeldige notificaties  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande.gcm.invalidemeldingsgrootte  |  Fout bij gcm-fout ongeldige meldingsgrootte  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande.gcm.pnsfout  |  GCM-fouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.success  |  GCM-succesvolle meldingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.throttled  |  GCM-meldingen voor throttled  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande.gcm.wrongchannel  |  GCM Verkeerde Kanaalfout  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande.mpns.authenticationfout  |  MPNS-verificatiefouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande.mpns.badchannel  |  MPNS Fout slecht kanaal  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.channeldisconnected  |  MPNS-kanaal verbroken  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.dropped  |  MPNS-meldingen laten vallen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande.mpns.invalidcredentials  |  MPNS-ongeldige referenties  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande.mpns.invalidenotatie  |  MPNS Ongeldige notificaties  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande.mpns.pnsfout  |  MPNS-fouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.success  |  MPNS-succesvolle meldingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.throttled  |  MPNS-meldingen voor throttled  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  extravert.wns.authenticationfout  |  WNS-verificatiefouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.badchannel  |  WNS Fout slecht kanaal  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channeldisconnected  |  WNS-kanaal verbroken  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channelthrottled  |  WNS-kanaal beperkt  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.dropped  |  WNS-meldingen laten vallen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.expiredchannel  |  WNS-verlopen kanaalfout  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande.wns.ongeldige referenties  |  WNS-autorisatiefouten (ongeldige referenties)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande.wns.invalidenotatie  |  WNS ongeldige meldingsnotatie  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnotificationsize  |  FOUT WNS Ongeldige meldingsgrootte  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidtoken  |  WNS-autorisatiefouten (ongeldig token)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande.wns.pnsfout  |  WNS-fouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.success  |  WNS-succesvolle meldingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.throttled  |  WNS-meldingen voor throttled  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.tokenprovideronbereikbaar  |  WNS-autorisatiefouten (onbereikbaar)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.wrongtoken  |  WNS-autorisatiefouten (verkeerde token)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registratie.alle  |  Registratiebewerkingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.create  |  Registratie Bewerkingen maken  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registratie.verwijderen  |  Registratiebewerkingen verwijderen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.get  |  Registratie Leesbewerkingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registration.update  |  Registratieupdatebewerkingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  gepland.in afwachting van  |  Geplande meldingen in behandeling  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_% beschikbaar geheugen  |  % beschikbaar geheugen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_% beschikbare swapruimte  |  % beschikbare ruilruimte  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_% vastgelegde bytes in gebruik  |  % vastgelegde bytes in gebruik  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_% DPC-tijd  |  % DPC-tijd  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_% Gratis Inodes  |  % Gratis Inodes  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_% vrije ruimte  |  % vrije ruimte  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_% vrije ruimte  |  % vrije ruimte  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_% idle tijd  |  Percentage niet-actieve tijd  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_% Onderbrekingstijd  |  % Onderbrekingstijd  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_% IO wachttijd  |  % IO wachttijd  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_% Leuke tijd  |  % Mooie tijd  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_% bevoorrechte tijd  |  % bevoorrechte tijd  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_% processortijd  |  Percentage processortijd  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_% processortijd  |  Percentage processortijd  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_% gebruikte inodes  |  % gebruikte inodes  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_% gebruikt geheugen  |  % gebruikt geheugen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_% gebruikte ruimte  |  % gebruikte ruimte  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_% gebruikte swapruimte  |  % gebruikte swapruimte  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_% gebruikerstijd  |  % gebruikerstijd  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Available MBytes  |  Beschikbare mbytes  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Available MBytes-geheugen  |  Beschikbaar MBytes-geheugen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Available MBytes-swap  |  Beschikbare MBytes-swap  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Avg. Gelezen bytes per seconde  |  Avg. Schijf sec/read  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Avg. Gelezen bytes per seconde  |  Avg. Schijf sec/read  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Avg. Schijfsec/overdracht  |  Avg. Schijfsec/Overdracht  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Avg. voor de fysieke schijf  |  Avg. Schijf sec/write  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Avg. voor de fysieke schijf  |  Avg. Schijf sec/write  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Bytes ontvangen/sec  |  Ontvangen bytes per seconde  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Bytes verzonden/sec  |  Verzonden bytes per seconde  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Bytes Totaal/sec  |  Totaal aantal bytes per seconde  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Current-schijfwachtrijlengte  |  Huidige schijfwachtrijlengte  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Disk Bytes per seconde lezen  |  Bytes voor schijflezen per seconde  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Disk leest/sec  |  Schijfleest/sec  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Disk leest/sec  |  Schijfleest/sec  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Disk Transfers/sec  |  Schijfoverdrachten per seconde  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Disk Transfers/sec  |  Schijfoverdrachten per seconde  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Disk Schrijfbytes per seconde  |  Bytes voor schijfschrijfschrijfberichten per seconde  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Disk schrijft/sec  |  Schijfschrijft/sec  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Disk schrijft/sec  |  Schijfschrijft/sec  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Free Megabytes  |  Gratis Megabytes  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Free Megabytes  |  Gratis Megabytes  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Free fysiek geheugen  |  Gratis fysiek geheugen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Free ruimte in pagingbestanden  |  Vrije ruimte in Paging-bestanden  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Free virtueel geheugen  |  Gratis virtueel geheugen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Logical schijfbytes per seconde  |  Logische schijfbytes per seconde  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Page leest/sec  |  Pagina leest/sec  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Page schrijft/sec  |  Paginaschrijft/sec  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Pages/sec  |  Pagina's/sec  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Pct bevoorrechte tijd  |  Pct bevoorrechte tijd  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Pct gebruikerstijd  |  Pct-gebruikerstijd  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  schijfbytes per seconde Average_Physical  |  Bytes van fysieke schijf per seconde  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Processes  |  Processen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Processor wachtrijlengte  |  Processorwachtrijlengte  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Size opgeslagen in pagingbestanden  |  Grootte opgeslagen in paging-bestanden  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Total Bytes  |  Totaal aantal bytes  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  ontvangen Average_Total ontvangen bytes  |  Totaal aantal ontvangen bytes  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Total verzonden bytes  |  Totaal aantal verzonden bytes  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Total botsingen  |  Totale botsingen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Total ontvangen pakketten  |  Totaal ontvangen pakketten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Total verzonden pakketten  |  Totaal verzonden pakketten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Total Rx-fouten  |  Totaal aantal rx-fouten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Total Tx-fouten  |  Totaal aantal Tx-fouten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Uptime  |  Uptime  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Used MBytes-wisselruimte  |  Gebruikte MBytes-wisselruimte  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Used geheugenkbytes  |  KBytes voor gebruikt geheugen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Used-geheugenmbytes  |  Bytes voor gebruikt geheugen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Users  |  Gebruikers  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Average_Virtual gedeeld geheugen  |  Virtueel gedeeld geheugen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Gebeurtenis  |  Gebeurtenis  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.OperationalInsights/werkruimten  |  Hartslag  |  Hartslag  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/werkruimten  |  Update  |  Update  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.PowerBIDedicated/capaciteiten  |  memory_metric  |  Geheugen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.PowerBIDedicated/capaciteiten  |  memory_thrashing_metric  |  Geheugengeseling (gegevenssets)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.PowerBIDedicated/capaciteiten  |  qpu_high_utilization_metric  |  Hoog QPU-gebruik  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.PowerBIDedicated/capaciteiten  |  Queryduur  |  Queryduur (gegevenssets)  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.PowerBIDedicated/capaciteiten  |  QuerypoolJobQueueLengte  |  Wachtrijlengte van querygroeptaak (gegevenssets)  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.Relay/naamruimten  |  Actieve verbindingen  |  Actieve verbindingen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Relay/naamruimten  |  Actieveluisteraars  |  Actieveluisteraars  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Relay/naamruimten  |  Bytes overgedragen  |  Bytes overgedragen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Relay/naamruimten  |  ListenerConnections-ClientError  |  ListenerConnections-ClientError  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Relay/naamruimten  |  ListenerConnections-ServerFout  |  ListenerConnections-ServerFout  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Relay/naamruimten  |  ListenerConnections-Succes  |  ListenerConnections-Succes  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Relay/naamruimten  |  ListenerConnections-TotaalAanvragen  |  ListenerConnections-TotaalAanvragen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Relay/naamruimten  |  ListenerKoppelt de verbinding  |  ListenerKoppelt de verbinding  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Relay/naamruimten  |  SenderConnections-ClientError  |  SenderConnections-ClientError  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Relay/naamruimten  |  SenderConnections-ServerD  |  SenderConnections-ServerD  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Relay/naamruimten  |  SenderConnections-Succes  |  SenderConnections-Succes  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Relay/naamruimten  |  SenderConnections-TotaalAanvragen  |  SenderConnections-TotaalAanvragen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Relay/naamruimten  |  Afzenderkoppelt  |  Afzenderkoppelt  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Search/searchServices  |  Zoeklatentie  |  Zoeklatentie  |  Seconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Search/searchServices  |  ZoekopdrachtenPerSeconde  |  Zoekopdrachten per seconde  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.Search/searchServices  |  ThrottledSearchQueriesPercentage  |  Percentage gewurgde zoekopdrachten  |  Percentage  |  Average | 
| Nee  | Nee |  Microsoft.ServiceBus/naamruimten  |  Actieve verbindingen  |  Actieve verbindingen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.ServiceBus/naamruimten  |  ActiveMessages  |  Aantal actieve berichten in een wachtrij/onderwerp.  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ServiceBus/naamruimten  |  Verbindingengesloten  |  Verbindingen gesloten.  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ServiceBus/naamruimten  |  Verbindingengeopend  |  Verbindingen geopend.  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ServiceBus/naamruimten  |  CPUXNS CPUXNS  |  CPU (afgeschaft)  |  Percentage  |  Maximum | 
| Nee  | Nee |  Microsoft.ServiceBus/naamruimten  |  DeadletteredMessages  |  Aantal dode-geletterde berichten in een wachtrij/onderwerp.  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.ServiceBus/naamruimten  |  Binnenkomende berichten  |  Binnenkomende berichten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ServiceBus/naamruimten  |  Binnenkomende aanvragen  |  Binnenkomende aanvragen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.ServiceBus/naamruimten  |  Berichten  |  Aantal berichten in een wachtrij/onderwerp.  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ServiceBus/naamruimten  |  NamespaceCpuUsage  |  CPU  |  Percentage  |  Maximum | 
| Nee  | Nee |  Microsoft.ServiceBus/naamruimten  |  NamespaceMemoryUsage  |  Geheugengebruik  |  Percentage  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.ServiceBus/naamruimten  |  Uitgaande Berichten  |  Uitgaande berichten  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.ServiceBus/naamruimten  |  Geplande berichten  |  Aantal geplande berichten in een wachtrij/onderwerp.  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ServiceBus/naamruimten  |  Serverfouten  |  Serverfouten.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.ServiceBus/naamruimten  |  Grootte  |  Grootte  |  Bytes  |  Average | 
| Nee  | Nee |  Microsoft.ServiceBus/naamruimten  |  Succesvolle verzoeken  |  Succesvolle aanvragen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.ServiceBus/naamruimten  |  ThrottledRequests  |  Throttled Requests.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.ServiceBus/naamruimten  |  Gebruikersfouten  |  Gebruikersfouten.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.ServiceBus/naamruimten  |  WSXNS  |  Geheugengebruik (afgeschaft)  |  Percentage  |  Maximum | 
| Nee  | Nee |  Microsoft.ServiceFabricMesh/toepassingen  |  Werkelijke Cpu  |  Werkelijke Cpu  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ServiceFabricMesh/toepassingen  |  Werkelijkememory  |  Werkelijkememory  |  Bytes  |  Average | 
| Nee  | Nee |  Microsoft.ServiceFabricMesh/toepassingen  |  ToegewezenCpu  |  ToegewezenCpu  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ServiceFabricMesh/toepassingen  |  ToegewezenGeheugen  |  ToegewezenGeheugen  |  Bytes  |  Average | 
| Nee  | Nee |  Microsoft.ServiceFabricMesh/toepassingen  |  ApplicationStatus  |  ApplicationStatus  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ServiceFabricMesh/toepassingen  |  ContainerStatus  |  ContainerStatus  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ServiceFabricMesh/toepassingen  |  Cpu-gebruik  |  Cpu-gebruik  |  Percentage  |  Average | 
| Nee  | Nee |  Microsoft.ServiceFabricMesh/toepassingen  |  Geheugengebruik  |  Geheugengebruik  |  Percentage  |  Average | 
| Nee  | Nee |  Microsoft.ServiceFabricMesh/toepassingen  |  Aantal opnieuw starten  |  Aantal opnieuw starten  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ServiceFabricMesh/toepassingen  |  ServiceReplicaStatus  |  ServiceReplicaStatus  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ServiceFabricMesh/toepassingen  |  ServiceStatus  |  ServiceStatus  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.SignalRService/SignalR  |  Aantal verbindingen  |  Aantal verbindingen  |  Count  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.SignalRService/SignalR  |  Binnenkomend verkeer  |  Binnenkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.SignalRService/SignalR  |  Aantal berichten  |  Aantal berichten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.SignalRService/SignalR  |  UitgaandVerkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.SignalRService/SignalR  |  Systeemfouten  |  Systeemfouten  |  Percentage  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.SignalRService/SignalR  |  Gebruikersfouten  |  Gebruikersfouten  |  Percentage  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.Sql/managedInstances  |  avg_cpu_percent  |  Gemiddeld CPU-percentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/managedInstances  |  io_bytes_read  |  GELEZEN IO-bytes  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/managedInstances  |  io_bytes_written  |  IO-bytes geschreven  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/managedInstances  |  io_requests  |  Aantal IO-aanvragen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/managedInstances  |  reserved_storage_mb  |  Opslagruimte gereserveerd  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/managedInstances  |  storage_space_used_mb  |  Gebruikte opslagruimte  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/managedInstances  |  virtual_core_count  |  Aantal virtuele kernen  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers  |  database_dtu_consumption_percent  |  DTU-percentage  |  Percentage  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers  |  database_storage_used  |  Gebruikte gegevensruimte  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers  |  dtu_consumption_percent  |  DTU-percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers  |  dtu_used  |  DTU gebruikt  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers  |  storage_used  |  Gebruikte gegevensruimte  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  allocated_data_storage  |  Toegewezen gegevensruimte  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  app_cpu_billed  |  App CPU gefactureerd  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  app_cpu_percent  |  Cpu-percentage voor apps  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  app_memory_percent  |  Percentage app-geheugen  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  blocked_by_firewall  |  Geblokkeerd door firewall  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  cache_hit_percent  |  Percentage cachehit  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  cache_used_percent  |  Door cache gebruikt percentage  |  Percentage  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  connection_failed  |  Mislukte verbindingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  connection_successful  |  Succesvolle verbindingen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  cpu_limit  |  CPU-limiet  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  cpu_percent  |  CPU-percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  cpu_used  |  CPU gebruikt  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  Impasse  |  Deadlocks  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  dtu_consumption_percent  |  DTU-percentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  dtu_limit  |  DTU-limiet  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  dtu_used  |  DTU gebruikt  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  dwu_consumption_percent  |  DWU-percentage  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  dwu_limit  |  DWU-limiet  |  Count  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  dwu_used  |  Gebruikte DWU  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  local_tempdb_usage_percent  |  Lokaal tempdb percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  log_write_percent  |  Io-percentage logboek  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  memory_usage_percent  |  Geheugenpercentage  |  Percentage  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  physical_data_read_percent  |  Gegevens-I/O-percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  sessions_percent  |  Percentage Sessies  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  storage  |  Gebruikte gegevensruimte  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  storage_percent  |  Gebruikte gegevensruimte procenten  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  tempdb_data_size  |  Tempdb-gegevensbestandsgrootte Kilobytes  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  tempdb_log_size  |  Tempdb Log File Size Kilobytes  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  tempdb_log_used_percent  |  Tempdb Procent Log gebruikt  |  Percentage  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  workers_percent  |  Percentage werknemers  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  xtp_storage_percent  |  In-Memory OLTP-opslagpercentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage  |  Toegewezen gegevensruimte  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage_percent  |  Toegewezen gegevensruimte percentage  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Sql/servers/elasticPools  |  cpu_limit  |  CPU-limiet  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/elasticPools  |  cpu_percent  |  CPU-percentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/elasticPools  |  cpu_used  |  CPU gebruikt  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers/elasticPools  |  database_allocated_data_storage  |  Toegewezen gegevensruimte  |  Bytes  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers/elasticPools  |  database_cpu_limit  |  CPU-limiet  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers/elasticPools  |  database_cpu_percent  |  CPU-percentage  |  Percentage  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers/elasticPools  |  database_cpu_used  |  CPU gebruikt  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers/elasticPools  |  database_dtu_consumption_percent  |  DTU-percentage  |  Percentage  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers/elasticPools  |  database_eDTU_used  |  eDTU gebruikt  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers/elasticPools  |  database_log_write_percent  |  Io-percentage logboek  |  Percentage  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers/elasticPools  |  database_physical_data_read_percent  |  Gegevens-I/O-percentage  |  Percentage  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers/elasticPools  |  database_sessions_percent  |  Percentage Sessies  |  Percentage  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers/elasticPools  |  database_storage_used  |  Gebruikte gegevensruimte  |  Bytes  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers/elasticPools  |  database_workers_percent  |  Percentage werknemers  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/elasticPools  |  dtu_consumption_percent  |  DTU-percentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/elasticPools  |  eDTU_limit  |  eDTU-limiet  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/elasticPools  |  eDTU_used  |  eDTU gebruikt  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/elasticPools  |  log_write_percent  |  Io-percentage logboek  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/elasticPools  |  physical_data_read_percent  |  Gegevens-I/O-percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/elasticPools  |  sessions_percent  |  Percentage Sessies  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/elasticPools  |  storage_limit  |  Maximale grootte van gegevens  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/elasticPools  |  storage_percent  |  Gebruikte gegevensruimte procenten  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/elasticPools  |  storage_used  |  Gebruikte gegevensruimte  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/elasticPools  |  tempdb_data_size  |  Tempdb-gegevensbestandsgrootte Kilobytes  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_size  |  Tempdb Log File Size Kilobytes  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_used_percent  |  Tempdb Procent Log gebruikt  |  Percentage  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/elasticPools  |  workers_percent  |  Percentage werknemers  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/elasticPools  |  xtp_storage_percent  |  In-Memory OLTP-opslagpercentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts  |  Beschikbaarheid  |  Beschikbaarheid  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts  |  SuccesE2ELatentie  |  Success E2E Latency  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts  |  SuccessServerLatentie  |  Geslaagde serverlatentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts  |  Transacties  |  Transacties  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Storage/storageAccounts  |  Gebruikte capaciteit  |  Gebruikte capaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/blobServices  |  Beschikbaarheid  |  Beschikbaarheid  |  Percentage  |  Average | 
| Nee  | Nee |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCapaciteit  |  Blob-capaciteit  |  Bytes  |  Average | 
| Nee  | Nee |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCount  |  Blobtelling  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/blobServices  |  ContainerCount ContainerCount  |  Aantal Blob-containers  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/blobServices  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| Nee  | Nee |  Microsoft.Storage/storageAccounts/blobServices  |  Indexcapaciteit  |  Indexcapaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/blobServices  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/blobServices  |  SuccesE2ELatentie  |  Success E2E Latency  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessServerLatentie  |  Geslaagde serverlatentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/blobServices  |  Transacties  |  Transacties  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/fileServices  |  Beschikbaarheid  |  Beschikbaarheid  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/fileServices  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| Nee  | Nee |  Microsoft.Storage/storageAccounts/fileServices  |  Bestandscapaciteit  |  Bestandscapaciteit  |  Bytes  |  Average | 
| Nee  | Nee |  Microsoft.Storage/storageAccounts/fileServices  |  FileCount  |  Aantal bestanden  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareCount  |  Aantal bestandsdelen  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareQuota  |  Quotumgrootte voor bestandsshare  |  Bytes  |  Average | 
| Nee  | Nee |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareSnapshotCount  |  Momentopnameaantal momentopnamen voor bestandsshare  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareSnapshotSize  |  Momentopname van bestandsshare  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/fileServices  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/fileServices  |  SuccesE2ELatentie  |  Success E2E Latency  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessServerLatentie  |  Geslaagde serverlatentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/fileServices  |  Transacties  |  Transacties  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/queueServices  |  Beschikbaarheid  |  Beschikbaarheid  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/queueServices  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/queueServices  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/queueServices  |  Wachtrijcapaciteit  |  Wachtrijcapaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/queueServices  |  Aantal wachtrijen  |  Aantal wachtrijen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/queueServices  |  Aantal queuemessage's  |  Aantal wachtrijberichten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/queueServices  |  SuccesE2ELatentie  |  Success E2E Latency  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessServerLatentie  |  Geslaagde serverlatentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/queueServices  |  Transacties  |  Transacties  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/tableServices  |  Beschikbaarheid  |  Beschikbaarheid  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/tableServices  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/tableServices  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/tableServices  |  SuccesE2ELatentie  |  Success E2E Latency  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessServerLatentie  |  Geslaagde serverlatentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/tableServices  |  Tabelcapaciteit  |  Tabelcapaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/tableServices  |  Tabeltelling  |  Tabelaantal  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/tableServices  |  Tabelentiteittelling  |  Aantal tabelentiteiten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/tableServices  |  Transacties  |  Transacties  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StorageCache/caches  |  ClientIOPS  |  Totaal aantal client-IOPS  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.StorageCache/caches  |  ClientLatentie  |  Gemiddelde klantlatentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.StorageCache/caches  |  ClientlockiOPS  |  Client Lock IOPS  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.StorageCache/caches  |  ClientMetadataReadIOPS  |  Clientmetagegevens lezen IOPS  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.StorageCache/caches  |  ClientMetadataWriteIOPS  |  Clientmetagegevens schrijven IOPS  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.StorageCache/caches  |  ClientReadIOPS  |  Client Lees IOPS  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.StorageCache/caches  |  ClientRead-doorvoer  |  Gemiddelde cacheleesdoorvoer  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.StorageCache/caches  |  ClientWriteIOPS  |  Client Schrijf IOPS  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.StorageCache/caches  |  ClientWrite-doorvoer  |  Gemiddelde cacheschrijfdoorvoer  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.StorageCache/caches  |  StorageTargetAsyncWriteThroughput  |  StorageTarget Asynchrone schrijfdoorvoer  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.StorageCache/caches  |  StorageTargetFillThroughput  |  Opslagdoelvuldoorvoer  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.StorageCache/caches  |  StorageTargetHealth  |  Status van opslagdoel  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.StorageCache/caches  |  StorageTargetIOPS  |  IOPS voor totale opslagdoel  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.StorageCache/caches  |  StorageTargetLatentie  |  Latentie van StorageTarget  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.StorageCache/caches  |  StorageTargetMetadataReadIOPS  |  StorageTarget-metagegevens lezen IOPS  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.StorageCache/caches  |  StorageTargetMetadataWriteIOPS  |  StorageTarget-metagegevens schrijven IOPS  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.StorageCache/caches  |  StorageTargetReadAheadDoorvoer  |  StorageTarget Lees vooruit doorvoer  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.StorageCache/caches  |  StorageTargetReadIOPS  |  StorageTarget lees IOPS  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.StorageCache/caches  |  StorageTargetSyncWriteThroughput  |  StorageTarget synchrone schrijfdoorvoer  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.StorageCache/caches  |  StorageTargetTotalRead-doorvoer  |  Totale leesdoorvoer van StorageTarget  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.StorageCache/caches  |  StorageTargetTotalWrite-doorvoer  |  Totale schrijfdoorvoer van StorageTarget  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.StorageCache/caches  |  StorageTargetWriteIOPS  |  StorageTarget Schrijven IOPS  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.StorageCache/caches  |  Uptime  |  Uptime  |  Count  |  Average | 
| **Ja**  | Nee |  microsoft.storagesync/storageSyncServices  |  ServerSyncSessionResultaat  |  Sessieresultaat synchroniseren  |  Count  |  Average | 
| **Ja**  | Nee |  microsoft.storagesync/storageSyncServices  |  StorageSyncbatchtransferredFileBytes  |  Bytes gesynchroniseerd  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecalledNetworkBytesByApplication  |  Cloud tiering recall grootte per toepassing  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecalledTotalNetworkBytes  |  Cloud tiering recall grootte  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecallIOTotalSizeBytes  |  Terugroepactie voor cloudtiering  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecallThroughputBytesPerSeconde  |  Cloud tiering recall throughput Cloud tiering recall throughput Cloud tiering recall throughput Cloud tier  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  microsoft.storagesync/storageSyncServices  |  StorageSyncServerHeartbeat  |  Server onlinestatus  |  Count  |  Maximum | 
| **Ja**  | Nee |  microsoft.storagesync/storageSyncServices  |  StorageSyncSyncSessionAppliedFilesCount  |  Bestanden gesynchroniseerd  |  Count  |  Totaal | 
| **Ja**  | Nee |  microsoft.storagesync/storageSyncServices  |  StorageSyncSyncSessionPerItemErrorsCount  |  Bestanden die niet worden gesynchroniseerd  |  Count  |  Totaal | 
| **Ja**  | Nee |  microsoft.storagesync/storageSyncServices/registeredServers  |  ServerHeartbeat ServerHeartbeat  |  Server onlinestatus  |  Count  |  Maximum | 
| **Ja**  | Nee |  microsoft.storagesync/storageSyncServices/registeredServers  |  ServerRecallIOTotalSizeBytes  |  Terugroepactie voor cloudtiering  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupBatchTransferFileBytes  |  Bytes gesynchroniseerd  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  microsoft.storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionAppliedFilesCount  |  Bestanden gesynchroniseerd  |  Count  |  Totaal | 
| **Ja**  | Nee |  microsoft.storagesync/storageSyncServices/syncGroups  |  Aantal syncgroupsync-peritemfouten  |  Bestanden die niet worden gesynchroniseerd  |  Count  |  Totaal | 
| **Ja**  | Nee |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointBatchTransferFileBytes  |  Bytes gesynchroniseerd  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionAppliedFilesCount  |  Bestanden gesynchroniseerd  |  Count  |  Totaal | 
| **Ja**  | Nee |  microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints  |  Aantal serverendpointsync-peritemfouten  |  Bestanden die niet worden gesynchroniseerd  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutFailedRequests  |  Mislukte functieaanvragen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutInputEvents  |  Functiegebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutRequests  |  Functieaanvragen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  Conversiefouten  |  Fouten in gegevensconversie  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  Fout bij deserialisatie  |  Fouten in de deserialisatie van invoer  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  DroppedoradjustedEvents  |  Niet op volgorde evenementen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  EarlyInputEvents  |  Gebeurtenissen voor vroege invoer  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  Fouten  |  Runtime-fouten  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventBytes  |  Gebeurtenisbytes voor invoer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  Invoergebeurtenissen  |  Invoergebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventsSourcesBacklogged  |  Backlogged Invoergebeurtenissen  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventsSourcesPerSeconde  |  Ontvangen invoerbronnen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  LateInputGebeurtenissen  |  Gebeurtenissen voor late invoer  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  Uitvoergebeurtenissen  |  Uitvoergebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  UitvoerWatermarkDelaySeconden  |  Watermerk vertraging  |  Seconden  |  Maximum | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  Resourcegebruik  |  SU % Gebruik  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  Bytes voor schijflezen  |  Bytes voor schijflezen  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  Bewerkingen voor schijflezen/sec  |  Bewerkingen voor schijflezen/sec  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  Schrijfbytes voor schijf  |  Schrijfbytes voor schijf  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  Bewerkingen voor schijfschrijfbewerkingen/sec  |  Bewerkingen voor schijfschrijfbewerkingen/sec  |  CountPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadBytesPerSeconde  |  Bytes/Seconde schijfgelezen  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadLatentie  |  Latentie voor schijflezen  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadOperations  |  Schijfleesbewerkingen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskwritebytesPerSeconde  |  Bytes voor schijfschrijfbytes per seconde  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteLatentie  |  Latentie voor schijfschrijfschrijven  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteOperations  |  Schijfschrijfbewerkingen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemoryActive (MemoryActive)  |  Geheugenactief  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  Geheugenverleend  |  Geheugen verleend  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  Geheugengebruikt  |  Geheugen gebruikt  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  Netwerk in  |  Netwerk in  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  Netwerk uit  |  Netwerk uit  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  NetworkinbytesPerSeconde  |  Netwerk in bytes/sec  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  NetworkOutBytesPerSeconde  |  Bytes/Seconde voor netwerkuit  |  BytesPerSeconde  |  Average | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  PercentageCpuReady  |  Percentage CPU-ready  |  Milliseconden  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingOmgevingen/multiRolePools  |  ActiveRequests  |  Actieve aanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingOmgevingen/multiRolePools  |  Gemiddelde responstijd  |  Gemiddelde responstijd  |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingOmgevingen/multiRolePools  |  Ontvangen bytes  |  Gegevens in  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingOmgevingen/multiRolePools  |  BytesSent  |  Gegevens uit  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingOmgevingen/multiRolePools  |  CpuPercentage  |  CPU-percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingOmgevingen/multiRolePools  |  DiskQueueLengte  |  Lengte van de schijfwachtrij  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingOmgevingen/multiRolePools  |  Http101  |  Http 101  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingOmgevingen/multiRolePools  |  Http2xx (Http2xx)  |  Http 2xx  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingOmgevingen/multiRolePools  |  Http3xx (Http3xx)  |  Http 3xx  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingOmgevingen/multiRolePools  |  Http401  |  Http 401  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingOmgevingen/multiRolePools  |  Http403  |  Http 403  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingOmgevingen/multiRolePools  |  Http404  |  Http 404  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingOmgevingen/multiRolePools  |  Http406  |  Http 406  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingOmgevingen/multiRolePools  |  Http4xx (Http4xx)  |  Http 4xx  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingOmgevingen/multiRolePools  |  Http5xx (Http5xx)  |  Http-serverfouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingOmgevingen/multiRolePools  |  HttpQueuelength  |  Http-wachtrijlengte  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingOmgevingen/multiRolePools  |  LargeAppServicePlanInstances  |  Werknemers met een groot app-serviceplan  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingOmgevingen/multiRolePools  |  MediumAppServicePlanInstances  |  Werknemers met een gemiddelde app-serviceplan  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingOmgevingen/multiRolePools  |  GeheugenPercentage  |  Geheugenpercentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingOmgevingen/multiRolePools  |  Aanvragen  |  Aanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingOmgevingen/multiRolePools  |  SmallAppServicePlanInstances  |  Werknemers met een kleine app-serviceplan  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingOmgevingen/multiRolePools  |  TotalFrontEnds TotalFrontEnds  |  Totaal aantal fronteinden  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingOmgevingen/workerPools  |  CpuPercentage  |  CPU-percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingOmgevingen/workerPools  |  GeheugenPercentage  |  Geheugenpercentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingOmgevingen/workerPools  |  WerknemersBeschikbaar  |  Beschikbare werknemers  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingOmgevingen/workerPools  |  WerknemersTotaal  |  Totaal aantal werknemers  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingOmgevingen/workerPools  |  WerknemersGebruikt  |  Gebruikte werknemers  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  Ontvangen bytes  |  Gegevens in  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  BytesSent  |  Gegevens uit  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  CpuPercentage  |  CPU-percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  DiskQueueLengte  |  Lengte van de schijfwachtrij  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  HttpQueuelength  |  Http-wachtrijlengte  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  GeheugenPercentage  |  Geheugenpercentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpCloseWait  |  TCP Sluiten Wachten  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpClosing  |  TCP-afsluiting  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpEstablished  |  TCP opgericht  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpFinWait1  |  TCP Fin Wacht 1  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpFinWait2  |  TCP Fin Wacht 2  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpLastAck  |  TCP Last Ack  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpSynReceived  |  TCP Syn ontvangen  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpSynsent  |  TCP Syn verzonden  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpTimeWait  |  TCP-tijd wachten  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  AppVerbindingen  |  Verbindingen  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Gemiddelde MemoryWorkingSet  |  Gemiddelde geheugenwerkset  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Gemiddelde responstijd  |  Gemiddelde responstijd  |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Ontvangen bytes  |  Gegevens in  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  BytesSent  |  Gegevens uit  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  CpuTime (CpuTime)  |  CPU-tijd  |  Seconden  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Huidige vergaderingen  |  Huidige vergaderingen  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Aantal functies  |  Aantal functieuitvoeringen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  FunctionExecutionUnits  |  Functieuitvoeringseenheden  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Gen0Collecties  |  Gen 0 Garbage Collections  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Gen1Collecties  |  Gen 1 Garbage Collections  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Gen2Collecties  |  Gen 2 Garbage Collections  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Behandelt  |  Aantal ingangen  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Status van HealthCheck  |  Status van status van status van status van status  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Http101  |  Http 101  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Http2xx (Http2xx)  |  Http 2xx  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Http3xx (Http3xx)  |  Http 3xx  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Http401  |  Http 401  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Http403  |  Http 403  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Http404  |  Http 404  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Http406  |  Http 406  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Http4xx (Http4xx)  |  Http 4xx  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Http5xx (Http5xx)  |  Http-serverfouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  HttpResponseTime  |  Reactietijd  |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  IootherbytesPerSeconde  |  IO Andere bytes per seconde  |  BytesPerSeconde  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  IootherOperationsperSeconde  |  IO Andere bewerkingen per seconde  |  BytesPerSeconde  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  IoReadBytesPerSeconde  |  IO Leesbytes per seconde  |  BytesPerSeconde  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  IoReadOperationsPerSeconde  |  IO-leesbewerkingen per seconde  |  BytesPerSeconde  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  IoWriteBytesPerSeconde  |  IO-schrijfbytes per seconde  |  BytesPerSeconde  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  IoWriteOperationsPerSeconde  |  IO-schrijfbewerkingen per seconde  |  BytesPerSeconde  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  MemoryWorkingSet (MemoryWorkingSet)  |  Geheugenwerkset  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  PrivateBytes  |  Privébytes  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Aanvragen  |  Aanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  RequestsinApplicationQueue  |  Aanvragen in toepassingswachtrij  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Threads  |  Aantal draad  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  TotalAppDomains TotalAppDomains  |  Totaal aantal app-domeinen  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  TotalAppDomainsUnloaded TotalAppDomainsUnloaded TotalAppDomainsUnloaded TotalApp  |  Totaal aantal app-domeinen gelost  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  AppVerbindingen  |  Verbindingen  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Gemiddelde MemoryWorkingSet  |  Gemiddelde geheugenwerkset  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Gemiddelde responstijd  |  Gemiddelde responstijd  |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Ontvangen bytes  |  Gegevens in  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  BytesSent  |  Gegevens uit  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  CpuTime (CpuTime)  |  CPU-tijd  |  Seconden  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Huidige vergaderingen  |  Huidige vergaderingen  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Aantal functies  |  Aantal functieuitvoeringen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  FunctionExecutionUnits  |  Functieuitvoeringseenheden  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Gen0Collecties  |  Gen 0 Garbage Collections  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Gen1Collecties  |  Gen 1 Garbage Collections  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Gen2Collecties  |  Gen 2 Garbage Collections  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Behandelt  |  Aantal ingangen  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Status van HealthCheck  |  Status van status van status van status van status  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Http101  |  Http 101  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Http2xx (Http2xx)  |  Http 2xx  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Http3xx (Http3xx)  |  Http 3xx  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Http401  |  Http 401  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Http403  |  Http 403  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Http404  |  Http 404  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Http406  |  Http 406  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Http4xx (Http4xx)  |  Http 4xx  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Http5xx (Http5xx)  |  Http-serverfouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  HttpResponseTime  |  Reactietijd  |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  IootherbytesPerSeconde  |  IO Andere bytes per seconde  |  BytesPerSeconde  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  IootherOperationsperSeconde  |  IO Andere bewerkingen per seconde  |  BytesPerSeconde  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  IoReadBytesPerSeconde  |  IO Leesbytes per seconde  |  BytesPerSeconde  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  IoReadOperationsPerSeconde  |  IO-leesbewerkingen per seconde  |  BytesPerSeconde  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  IoWriteBytesPerSeconde  |  IO-schrijfbytes per seconde  |  BytesPerSeconde  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  IoWriteOperationsPerSeconde  |  IO-schrijfbewerkingen per seconde  |  BytesPerSeconde  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  MemoryWorkingSet (MemoryWorkingSet)  |  Geheugenwerkset  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  PrivateBytes  |  Privébytes  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Aanvragen  |  Aanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  RequestsinApplicationQueue  |  Aanvragen in toepassingswachtrij  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Threads  |  Aantal draad  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  TotalAppDomains TotalAppDomains  |  Totaal aantal app-domeinen  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  TotalAppDomainsUnloaded TotalAppDomainsUnloaded TotalAppDomainsUnloaded TotalApp  |  Totaal aantal app-domeinen gelost  |  Count  |  Average | 
