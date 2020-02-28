---
title: De metrische gegevens van het Azure Monitor-platform exporteerbaar via Diagnostische instellingen
description: Een lijst met metrische gegevens die beschikbaar zijn voor elk resource type met Azure Monitor.
services: azure-monitor
ms.topic: reference
ms.date: 02/10/2020
ms.subservice: metrics
ms.openlocfilehash: 7a75655d1707dd2491065974ed8addc4c2da1a6a
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77661359"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>De metrische gegevens van het Azure Monitor-platform exporteerbaar via Diagnostische instellingen

Azure Monitor biedt standaard [platform metrieken](data-platform-metrics.md) zonder configuratie. Het biedt verschillende manieren om te communiceren met platform metrieken, zoals het maken van grafieken in de portal, het openen ervan via de REST API of het opvragen van query's via Power shell of CLI. Bekijk [metrische gegevens: ondersteund](metrics-supported.md) voor een volledige lijst met platform metrieken die momenteel beschikbaar zijn met de geconsolideerde metrische pijp lijn van Azure monitor. Als u deze metrische gegevens wilt opvragen en openen, gebruikt u de [2018-01-01-API-versie](https://docs.microsoft.com/rest/api/monitor/metricdefinitions). Andere metrische gegevens zijn mogelijk beschikbaar in de portal of met behulp van verouderde Api's.

U kunt de platform metrieken van de Azure monitor-pijp lijn naar andere locaties op een van de twee manieren exporteren.
1. [Diagnostische instellingen](diagnostic-settings.md) gebruiken om te verzenden naar Log Analytics, Event Hubs of Azure Storage.
2. De [metrische gegevens rest API](https://docs.microsoft.com/rest/api/monitor/metrics/list) gebruiken

Vanwege complexiteit in de Azure Monitor back-end kunnen niet alle metrische gegevens exporteerbaar zijn met behulp van diagnostische instellingen. De volgende tabel bevat een lijst die kan en kan niet worden geëxporteerd met Diagnostische instellingen.

## <a name="change-to-behavior-for-nulls-and-zero-values"></a>Wijzigen in gedrag voor NULL-waarden en nulwaarden 
 
Voor de platform metrieken die kunnen worden geëxporteerd via Diagnostische instellingen, zijn er enkele metrische gegevens waarvoor Azure Monitor ' 0s ' als ' Nulls ' interpreteert. Dit heeft een zekere Verwar ring veroorzaakt tussen Real ' 0s ' (verzonden door resource) en ' 0s ' (null-waarden) geïnterpreteerd. Vanaf **1 April 2020** worden de metrische gegevens van het platform die zijn geëxporteerd via Diagnostische instellingen, niet meer geëxporteerd, tenzij ze daad werkelijk door de onderliggende resource zijn verzonden. Opmerking:

1.  Als u een resource groep of een specifieke resource verwijdert, worden metrische gegevens van de betrokken resources niet langer verzonden naar de diagnostische instelling export doelen. Dat wil zeggen dat deze niet meer wordt weer gegeven in Event Hubs, opslag accounts en Log Analytics-werk ruimten.
2.  Deze verbetering is beschikbaar in alle open bare en persoonlijke Clouds.
3.  Deze wijziging heeft geen invloed op het gedrag van een van de volgende acties: 
   - Platform bron logboeken geëxporteerd via Diagnostische instellingen
   - Metrische gegevens grafieken in Metrics Explorer
   - Waarschuwingen voor metrische gegevens van platform
 
## <a name="metrics-exportable-table"></a>Exporteer bare tabel met metrische gegevens 

De tabel bevat de volgende kolommen. 
- Exporteerbaar via Diagnostische instellingen? 
- Doorgevoerd door NULL/0 
- ResourceType 
- Gegevens 
- MetricDisplayName
- Eenheid 
- AggregationType


> [!NOTE]
> De onderstaande tabel bevat mogelijk een horizontale schuif balk aan de onderkant. Als u van mening bent dat u de informatie ontbreekt, controleert u of de schuif balk helemaal naar links wordt weer gegeven.  


| Exporteerbaar via Diagnostische instellingen?  | NULL-waarden worden verzonden |  ResourceType  |  Gegevens  |  MetricDisplayName  |  Eenheid  |  AggregationType | 
|---|---| ---- | ----- | ------ | ---- | ---- | 
| Ja * * * *  | Nee |  Microsoft.AnalysisServices/servers  |  CleanerCurrentPrice  |  Geheugen: huidige prijs opschonen  |  Count  |  Average | 
| Ja * * * *  | Nee |  Microsoft.AnalysisServices/servers  |  CleanerMemoryNonshrinkable  |  Geheugen: Removal-geheugen kan niet worden verkleind  |  Bytes  |  Average | 
| Ja * * * *  | Nee |  Microsoft.AnalysisServices/servers  |  CleanerMemoryShrinkable  |  Geheugen: verkleinbaar geheugen  |  Bytes  |  Average | 
| Ja * * * *  | Nee |  Microsoft.AnalysisServices/servers  |  CommandPoolBusyThreads  |  Threads: actieve threads van opdracht pool  |  Count  |  Average | 
| Ja * * * *  | Nee |  Microsoft.AnalysisServices/servers  |  CommandPoolIdleThreads  |  Threads: niet-actieve threads van opdracht pool  |  Count  |  Average | 
| Ja * * * *  | Nee |  Microsoft.AnalysisServices/servers  |  CommandPoolJobQueueLength  |  Wachtrij lengte van de opdracht pool taak  |  Count  |  Average | 
| Ja * * * *  | Nee |  Microsoft.AnalysisServices/servers  |  CurrentConnections  |  Verbinding: huidige verbindingen  |  Count  |  Average | 
| Ja * * * *  | Nee |  Microsoft.AnalysisServices/servers  |  CurrentUserSessions  |  Huidige gebruikers sessies  |  Count  |  Average | 
| Ja * * * *  | Nee |  Microsoft.AnalysisServices/servers  |  LongParsingBusyThreads  |  Threads: bezette threads voor lang parseren  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  LongParsingIdleThreads  |  Threads: niet-actieve threads voor lang parseren  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  LongParsingJobQueueLength  |  Threads: lengte van taak wachtrij voor lang parseren  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  mashup_engine_memory_metric  |  M-engine geheugen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  mashup_engine_private_bytes_metric  |  M-engine-eigen bytes  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  mashup_engine_qpu_metric  |  M-engine QPU  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  mashup_engine_virtual_bytes_metric  |  M-engine virtuele bytes  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  memory_metric  |  Geheugen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  memory_thrashing_metric  |  Geheugen overbelasting  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  MemoryLimitHard  |  Geheugen: vaste geheugen limiet  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  MemoryLimitHigh  |  Geheugen: hoge geheugen limiet  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  MemoryLimitLow  |  Geheugen: lage geheugen limiet  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  MemoryLimitVertiPaq  |  Geheugen: VertiPaq-geheugen limiet  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  MemoryUsage  |  Geheugen: geheugen gebruik  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  private_bytes_metric  |  Eigen Bytes  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  ProcessingPoolBusyIOJobThreads  |  Threads: bezig met verwerken van I/O-taak threads van pool  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  ProcessingPoolBusyNonIOThreads  |  Threads: bezig met het verwerken van niet-I/O-threads van de groep  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIdleIOJobThreads  |  Threads: niet-actieve I/O-taak threads van de groep verwerken  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIdleNonIOThreads  |  Threads: niet-I/O-threads van de groep worden verwerkt  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  ProcessingPoolIOJobQueueLength  |  Threads: lengte van I/O-taak wachtrij voor verwerking van groep  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  ProcessingPoolJobQueueLength  |  Wachtrij lengte van de pool taak wordt verwerkt  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  qpu_metric  |  QPU  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  QueryPoolBusyThreads  |  Query's pool bezette threads  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  QueryPoolIdleThreads  |  Threads: niet-actieve threads van query pool  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  QueryPoolJobQueueLength  |  Threads: lengte van taak wachtrij voor query pool  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  Overschreden  |  Geheugen: quotum  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  QuotaBlocked  |  Geheugen: quotum geblokkeerd  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  RowsConvertedPerSec  |  Verwerken: geconverteerde rijen per seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  RowsReadPerSec  |  Verwerken: gelezen rijen per seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  RowsWrittenPerSec  |  Verwerken: geschreven rijen per seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  ShortParsingBusyThreads  |  Threads: bezette threads voor kort parseren  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  ShortParsingIdleThreads  |  Threads: niet-actieve threads voor kort parseren  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  ShortParsingJobQueueLength  |  Threads: lengte van taak wachtrij voor kort parseren  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  SuccessfullConnectionsPerSec  |  Geslaagde verbindingen per seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  TotalConnectionFailures  |  Totaal aantal verbindings fouten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  TotalConnectionRequests  |  Totaal aantal verbindings aanvragen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  VertiPaqNonpaged  |  Geheugen: VertiPaq niet-wisselbaar  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  VertiPaqPaged  |  Geheugen: VertiPaq-pagina  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.AnalysisServices/servers  |  virtual_bytes_metric  |  Virtuele bytes  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  BackendDuration  |  Duur van back-end-aanvragen  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.ApiManagement/service  |  Capaciteit  |  Capaciteit  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  Duur  |  Totale duur van gateway aanvragen  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubDroppedEvents  |  Verwijderde EventHub-gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubRejectedEvents  |  Geweigerde EventHub-gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubSuccessfulEvents  |  Geslaagde EventHub-gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubThrottledEvents  |  Vertraagde EventHub-gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubTimedoutEvents  |  Time-out EventHub-gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubTotalBytesSent  |  Grootte van EventHub-gebeurtenissen  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubTotalEvents  |  Totaal aantal EventHub-gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubTotalFailedEvents  |  Mislukte EventHub-gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  FailedRequests  |  Mislukte gateway aanvragen (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  OtherRequests  |  Andere gateway aanvragen (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  Aanvragen  |  Aanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  SuccessfulRequests  |  Geslaagde gateway aanvragen (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  TotalRequests  |  Totaal aantal gateway aanvragen (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  UnauthorizedRequests  |  Niet-geautoriseerde gateway aanvragen (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  AppCpuUsagePercentage  |  CPU-gebruiks percentage van app  |  Procent  |  Average | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  AppMemoryCommitted  |  Toegewezen app-geheugen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  AppMemoryMax  |  Maxi maal app-geheugen  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  AppMemoryUsed  |  Gebruikt app-geheugen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  GCPauseTotalCount  |  Aantal GC-onderbrekingen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  GCPauseTotalTime  |  Totale tijd van de GC-onderbreking  |  Milliseconden  |  Totaal | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  MaxOldGenMemoryPoolBytes  |  Maxi maal beschik bare oude generatie gegevens grootte  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  OldGenMemoryPoolBytes  |  Gegevens grootte van oude generatie  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  OldGenPromotedBytes  |  Promo veren tot oude generatie gegevens grootte  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  SystemCpuUsagePercentage  |  Percentage van het CPU-gebruik van het systeem  |  Procent  |  Average | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  TomcatErrorCount  |  Tomcat Global-fout  |  Count  |  Totaal | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  TomcatReceivedBytes  |  Totaal aantal bytes ontvangen Tomcat  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  TomcatRequestMaxTime  |  Maximale tijd voor tomcat-aanvraag  |  Milliseconden  |  Maximum | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  TomcatRequestTotalCount  |  Totaal aantal Tomcat-aanvragen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  TomcatRequestTotalTime  |  Totaal aantal keer Tomcat-aanvragen  |  Milliseconden  |  Totaal | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  TomcatResponseAvgTime  |  Gemiddelde tijd Tomcat-aanvraag  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  TomcatSentBytes  |  Totaal aantal verzonden bytes in Tomcat  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  TomcatSessionActiveCurrentCount  |  Aantal Tomcat-sessies  |  Count  |  Totaal | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  TomcatSessionActiveMaxCount  |  Aantal actieve Tomcat-sessies  |  Count  |  Totaal | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  TomcatSessionAliveMaxTime  |  Time-outperiode van Tomcat-sessie  |  Milliseconden  |  Maximum | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  TomcatSessionCreatedCount  |  Aantal gemaakte Tomcat-sessies  |  Count  |  Totaal | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  TomcatSessionExpiredCount  |  Aantal verlopen Tomcat-sessies  |  Count  |  Totaal | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  TomcatSessionRejectedCount  |  Aantal geweigerde Tomcat-sessies  |  Count  |  Totaal | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  YoungGenPromotedBytes  |  Promo veren tot jonge generatie gegevens grootte  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Automation/automationAccounts  |  TotalJob  |  Totaal aantal taken  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Automation/automationAccounts  |  TotalUpdateDeploymentMachineRuns  |  Totaal aantal uitgevoerde update-implementatie computers  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Automation/automationAccounts  |  TotalUpdateDeploymentRuns  |  Totaal aantal uitgevoerde update-implementaties  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  CoreCount  |  Aantal toegewezen kernen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  CreatingNodeCount  |  Aantal knoop punten maken  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  IdleNodeCount  |  Aantal niet-actieve knoop punten  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  JobDeleteCompleteEvent  |  Voltooide gebeurtenissen van taak verwijderen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  JobDeleteStartEvent  |  Taak begin gebeurtenissen verwijderen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  JobDisableCompleteEvent  |  Voltooide gebeurtenissen voor taak uitschakelen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  JobDisableStartEvent  |  Taak start gebeurtenissen uitschakelen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  JobStartEvent  |  Taak begin gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  JobTerminateCompleteEvent  |  Voltooide gebeurtenissen voor taak beëindigen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  JobTerminateStartEvent  |  Taak start gebeurtenissen beëindigen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  LeavingPoolNodeCount  |  Aantal groeps knooppunten verlaten  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  LowPriorityCoreCount  |  Aantal LowPriority kernen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  OfflineNodeCount  |  Aantal offline knooppunten  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  PoolCreateEvent  |  Groeps gebeurtenissen maken  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  PoolDeleteCompleteEvent  |  Voltooide gebeurtenissen van groep verwijderen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  PoolDeleteStartEvent  |  Begin gebeurtenissen groep verwijderen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  PoolResizeCompleteEvent  |  Volledige gebeurtenissen voor het wijzigen van de pool  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  PoolResizeStartEvent  |  Begin gebeurtenissen van groeps grootte wijzigen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  PreemptedNodeCount  |  Aantal knoop punten in herhaling  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  RebootingNodeCount  |  Aantal knoop punten opnieuw opstarten  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  ReimagingNodeCount  |  Telling van het aantal knoop punten  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  RunningNodeCount  |  Aantal actieve knoop punten  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  StartingNodeCount  |  Begin aantal knoop punten  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  StartTaskFailedNodeCount  |  Aantal mislukte knoop punten van begin taak  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  TaskCompleteEvent  |  Taak voltooid gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  TaskFailEvent  |  Taak fout gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Batch/batchAccounts  |  TaskStartEvent  |  Taak begin gebeurtenissen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  TotalLowPriorityNodeCount  |  Aantal knoop punten met een lage prioriteit  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  TotalNodeCount  |  Aantal toegewezen knoop punten  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  UnusableNodeCount  |  Aantal niet-bruikbare knoop punten  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Batch/batchAccounts  |  WaitingForStartTaskNodeCount  |  Wachten op aantal begin taak knooppunten  |  Count  |  Totaal | 
| **Ja**  | Nee |  Micro soft. BatchAI/werk ruimten  |  Actieve kernen  |  Actieve kernen  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. BatchAI/werk ruimten  |  Actieve knoop punten  |  Actieve knoop punten  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. BatchAI/werk ruimten  |  Niet-actieve kernen  |  Niet-actieve kernen  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. BatchAI/werk ruimten  |  Niet-actieve knoop punten  |  Niet-actieve knoop punten  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. BatchAI/werk ruimten  |  Taak is voltooid  |  Taak is voltooid  |  Count  |  Totaal | 
| **Ja**  | Nee |  Micro soft. BatchAI/werk ruimten  |  Taak verzonden  |  Taak verzonden  |  Count  |  Totaal | 
| **Ja**  | Nee |  Micro soft. BatchAI/werk ruimten  |  Kernen verlaten  |  Kernen verlaten  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. BatchAI/werk ruimten  |  Knoop punten verlaten  |  Knoop punten verlaten  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. BatchAI/werk ruimten  |  Afgebroken kernen  |  Afgebroken kernen  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. BatchAI/werk ruimten  |  Knoop punten die zijn afgebroken  |  Knoop punten die zijn afgebroken  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. BatchAI/werk ruimten  |  Percentage quotum gebruik  |  Percentage quotum gebruik  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. BatchAI/werk ruimten  |  Totaal aantal kernen  |  Totaal aantal kernen  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. BatchAI/werk ruimten  |  Totaal aantal knoop punten  |  Totaal aantal knoop punten  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. BatchAI/werk ruimten  |  Onbruikbaar aantal kern geheugens  |  Onbruikbaar aantal kern geheugens  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. BatchAI/werk ruimten  |  Niet-bruikbare knoop punten  |  Niet-bruikbare knoop punten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  ConnectionAccepted  |  Geaccepteerde verbindingen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  ConnectionActive  |  Actieve verbindingen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  ConnectionHandled  |  Afgehandelde verbindingen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  CpuUsagePercentageInDouble  |  Percentage CPU-gebruik  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  IOReadBytes  |  I/o gelezen bytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  IOWriteBytes  |  I/o-schrijf bytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  MemoryLimit  |  Geheugen limiet  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  MemoryUsage  |  Geheugen gebruik  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  MemoryUsagePercentageInDouble  |  Percentage geheugen gebruik  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  PendingTransactions  |  Trans acties in behandeling  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  ProcessedBlocks  |  Verwerkte blokken  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  ProcessedTransactions  |  Verwerkte trans acties  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  QueuedTransactions  |  Trans acties in de wachtrij  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  RequestHandled  |  Verwerkte aanvragen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  StorageUsage  |  Opslag gebruik  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachehits  |  Cache treffers  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachehits0  |  Cache treffers (Shard 0)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachehits1  |  Cache treffers (Shard 1)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachehits2  |  Cache treffers (Shard 2)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachehits3  |  Cache treffers (Shard 3)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachehits4  |  Cache treffers (Shard 4)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachehits5  |  Cache treffers (Shard 5)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachehits6  |  Cache treffers (Shard 6)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachehits7  |  Cache treffers (Shard 7)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachehits8  |  Cache treffers (Shard 8)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachehits9  |  Cache treffers (Shard 9)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheLatency  |  Cache latentie micro seconden (preview-versie)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachemisses  |  Cache missers  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachemisses0  |  Cache missers (Shard 0)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachemisses1  |  Cache missers (Shard 1)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachemisses2  |  Cache missers (Shard 2)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachemisses3  |  Cache missers (Shard 3)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachemisses4  |  Cache missers (Shard 4)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachemisses5  |  Cache missers (Shard 5)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachemisses6  |  Cache missers (Shard 6)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachemisses7  |  Cache missers (Shard 7)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachemisses8  |  Cache missers (Shard 8)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cachemisses9  |  Cache missers (Shard 9)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheRead  |  Lees bewerking in cache  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheRead0  |  Cache gelezen (Shard 0)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheRead1  |  Lees bewerking in cache (Shard 1)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheRead2  |  Lees bewerking in cache (Shard 2)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheRead3  |  Lees bewerking in cache (Shard 3)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheRead4  |  Lees bewerking in cache (Shard 4)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheRead5  |  Lees bewerking in cache (Shard 5)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheRead6  |  Lees bewerking in cache (Shard 6)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheRead7  |  Lees bewerking in cache (Shard 7)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheRead8  |  Lees bewerking in cache (Shard 8)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheRead9  |  Lees bewerking in cache (Shard 9)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheWrite  |  Cache schrijven  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheWrite0  |  Cache schrijven (Shard 0)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheWrite1  |  Cache schrijven (Shard 1)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheWrite2  |  Cache schrijven (Shard 2)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheWrite3  |  Cache schrijven (Shard 3)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheWrite4  |  Cache schrijven (Shard 4)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheWrite5  |  Cache schrijven (Shard 5)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheWrite6  |  Cache schrijven (Shard 6)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheWrite7  |  Cache schrijven (Shard 7)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheWrite8  |  Cache schrijven (Shard 8)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  cacheWrite9  |  Cache schrijven (Shard 9)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  connectedclients  |  Verbonden clients  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  connectedclients0  |  Verbonden clients (Shard 0)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  connectedclients1  |  Verbonden clients (Shard 1)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  connectedclients2  |  Verbonden clients (Shard 2)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  connectedclients3  |  Verbonden clients (Shard 3)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  connectedclients4  |  Verbonden clients (Shard 4)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  connectedclients5  |  Verbonden clients (Shard 5)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  connectedclients6  |  Verbonden clients (Shard 6)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  connectedclients7  |  Verbonden clients (Shard 7)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  connectedclients8  |  Verbonden clients (Shard 8)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  connectedclients9  |  Verbonden clients (Shard 9)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  fouten  |  Fouten  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  evictedkeys  |  Verwijderde sleutels  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  evictedkeys0  |  Verwijderde sleutels (Shard 0)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  evictedkeys1  |  Verwijderde sleutels (Shard 1)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  evictedkeys2  |  Verwijderde sleutels (Shard 2)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  evictedkeys3  |  Verwijderde sleutels (Shard 3)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  evictedkeys4  |  Verwijderde sleutels (Shard 4)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  evictedkeys5  |  Verwijderde sleutels (Shard 5)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  evictedkeys6  |  Verwijderde sleutels (Shard 6)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  evictedkeys7  |  Verwijderde sleutels (Shard 7)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  evictedkeys8  |  Verwijderde sleutels (Shard 8)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  evictedkeys9  |  Verwijderde sleutels (Shard 9)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  expiredkeys  |  Verlopen sleutels  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  expiredkeys0  |  Verlopen sleutels (Shard 0)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  expiredkeys1  |  Verlopen sleutels (Shard 1)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  expiredkeys2  |  Verlopen sleutels (Shard 2)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  expiredkeys3  |  Verlopen sleutels (Shard 3)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  expiredkeys4  |  Verlopen sleutels (Shard 4)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  expiredkeys5  |  Verlopen sleutels (Shard 5)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  expiredkeys6  |  Verlopen sleutels (Shard 6)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  expiredkeys7  |  Verlopen sleutels (Shard 7)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  expiredkeys8  |  Verlopen sleutels (Shard 8)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  expiredkeys9  |  Verlopen sleutels (Shard 9)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  getcommands  |  Methode  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  getcommands0  |  Hiermee wordt opgehaald (Shard 0)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  getcommands1  |  Hiermee wordt opgehaald (Shard 1)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  getcommands2  |  Hiermee wordt opgehaald (Shard 2)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  getcommands3  |  Hiermee wordt opgehaald (Shard 3)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  getcommands4  |  Hiermee wordt opgehaald (Shard 4)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  getcommands5  |  Hiermee wordt opgehaald (Shard 5)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  getcommands6  |  Hiermee wordt opgehaald (Shard 6)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  getcommands7  |  Hiermee wordt opgehaald (Shard 7)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  getcommands8  |  Hiermee wordt opgehaald (Shard 8)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  getcommands9  |  Hiermee wordt opgehaald (Shard 9)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  operationsPerSecond  |  Bewerkingen per seconde  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  operationsPerSecond0  |  Bewerkingen per seconde (Shard 0)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  operationsPerSecond1  |  Bewerkingen per seconde (Shard 1)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  operationsPerSecond2  |  Bewerkingen per seconde (Shard 2)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  operationsPerSecond3  |  Bewerkingen per seconde (Shard 3)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  operationsPerSecond4  |  Bewerkingen per seconde (Shard 4)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  operationsPerSecond5  |  Bewerkingen per seconde (Shard 5)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  operationsPerSecond6  |  Bewerkingen per seconde (Shard 6)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  operationsPerSecond7  |  Bewerkingen per seconde (Shard 7)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  operationsPerSecond8  |  Bewerkingen per seconde (Shard 8)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  operationsPerSecond9  |  Bewerkingen per seconde (Shard 9)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  percentProcessorTime  |  CPU  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  percentProcessorTime0  |  CPU (Shard 0)  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  percentProcessorTime1  |  CPU (Shard 1)  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  percentProcessorTime2  |  CPU (Shard 2)  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  percentProcessorTime3  |  CPU (Shard 3)  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  percentProcessorTime4  |  CPU (Shard 4)  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  percentProcessorTime5  |  CPU (Shard 5)  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  percentProcessorTime6  |  CPU (Shard 6)  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  percentProcessorTime7  |  CPU (Shard 7)  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  percentProcessorTime8  |  CPU (Shard 8)  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  percentProcessorTime9  |  CPU (Shard 9)  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  serverLoad  |  Server belasting  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  serverLoad0  |  Server belasting (Shard 0)  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  serverLoad1  |  Server belasting (Shard 1)  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  serverLoad2  |  Server belasting (Shard 2)  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  serverLoad3  |  Server belasting (Shard 3)  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  serverLoad4  |  Server belasting (Shard 4)  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  serverLoad5  |  Server belasting (Shard 5)  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  serverLoad6  |  Server belasting (Shard 6)  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  serverLoad7  |  Server belasting (Shard 7)  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  serverLoad8  |  Server belasting (Shard 8)  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  serverLoad9  |  Server belasting (Shard 9)  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  setcommands  |  Groepen  |  Count  |  Totaal | 
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
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalcommandsprocessed  |  Totaal aantal bewerkingen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalcommandsprocessed0  |  Totaal aantal bewerkingen (Shard 0)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalcommandsprocessed1  |  Totaal aantal bewerkingen (Shard 1)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalcommandsprocessed2  |  Totaal aantal bewerkingen (Shard 2)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalcommandsprocessed3  |  Totaal aantal bewerkingen (Shard 3)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalcommandsprocessed4  |  Totaal aantal bewerkingen (Shard 4)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalcommandsprocessed5  |  Totaal aantal bewerkingen (Shard 5)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalcommandsprocessed6  |  Totaal aantal bewerkingen (Shard 6)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalcommandsprocessed7  |  Totaal aantal bewerkingen (Shard 7)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalcommandsprocessed8  |  Totaal aantal bewerkingen (Shard 8)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalcommandsprocessed9  |  Totaal aantal bewerkingen (Shard 9)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalkeys  |  Totaal aantal sleutels  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalkeys0  |  Totaal aantal sleutels (Shard 0)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalkeys1  |  Totaal aantal sleutels (Shard 1)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalkeys2  |  Totaal aantal sleutels (Shard 2)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalkeys3  |  Totaal aantal sleutels (Shard 3)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalkeys4  |  Totaal aantal sleutels (Shard 4)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalkeys5  |  Totaal aantal sleutels (Shard 5)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalkeys6  |  Totaal aantal sleutels (Shard 6)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalkeys7  |  Totaal aantal sleutels (Shard 7)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalkeys8  |  Totaal aantal sleutels (Shard 8)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  totalkeys9  |  Totaal aantal sleutels (Shard 9)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemory  |  Gebruikt geheugen  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemory0  |  Gebruikt geheugen (Shard 0)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemory1  |  Gebruikt geheugen (Shard 1)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemory2  |  Gebruikt geheugen (Shard 2)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemory3  |  Gebruikt geheugen (Shard 3)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemory4  |  Gebruikt geheugen (Shard 4)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemory5  |  Gebruikt geheugen (Shard 5)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemory6  |  Gebruikt geheugen (Shard 6)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemory7  |  Gebruikt geheugen (Shard 7)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemory8  |  Gebruikt geheugen (Shard 8)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemory9  |  Gebruikt geheugen (Shard 9)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemorypercentage  |  Percentage gebruikt geheugen  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemoryRss  |  Gebruikte geheugen-RSS  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemoryRss0  |  Gebruikt geheugen RSS (Shard 0)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemoryRss1  |  Gebruikte geheugen RSS (Shard 1)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemoryRss2  |  Gebruikte geheugen RSS (Shard 2)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemoryRss3  |  Gebruikte geheugen RSS (Shard 3)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemoryRss4  |  Gebruikte geheugen RSS (Shard 4)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemoryRss5  |  Gebruikte geheugen RSS (Shard 5)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemoryRss6  |  Gebruikte geheugen RSS (Shard 6)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemoryRss7  |  Gebruikte geheugen RSS (Shard 7)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemoryRss8  |  Gebruikt geheugen RSS (Shard 8)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Cache/redis  |  usedmemoryRss9  |  Gebruikte geheugen RSS (Shard 9)  |  Bytes  |  Maximum | 
| Nee  | Nee |  Micro soft. ClassicCompute/domein naam/sleuven/rollen  |  Gelezen bytes per seconde  |  Schijf lezen  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicCompute/domein naam/sleuven/rollen  |  Lees bewerkingen op de schijf per seconde  |  Lees bewerkingen op de schijf per seconde  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Micro soft. ClassicCompute/domein naam/sleuven/rollen  |  Geschreven bytes per seconde  |  Schijf schrijven  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicCompute/domein naam/sleuven/rollen  |  Schrijf bewerkingen op de schijf per seconde  |  Schrijf bewerkingen op de schijf per seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicCompute/domein naam/sleuven/rollen  |  Netwerk in  |  Netwerk in  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ClassicCompute/domein naam/sleuven/rollen  |  Netwerk uit  |  Netwerk uit  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ClassicCompute/domein naam/sleuven/rollen  |  Percentage CPU  |  Percentage CPU  |  Procent  |  Average | 
| Nee  | Nee |  Microsoft.ClassicCompute/virtualMachines  |  Gelezen bytes per seconde  |  Schijf lezen  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicCompute/virtualMachines  |  Lees bewerkingen op de schijf per seconde  |  Lees bewerkingen op de schijf per seconde  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Microsoft.ClassicCompute/virtualMachines  |  Geschreven bytes per seconde  |  Schijf schrijven  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicCompute/virtualMachines  |  Schrijf bewerkingen op de schijf per seconde  |  Schrijf bewerkingen op de schijf per seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicCompute/virtualMachines  |  Netwerk in  |  Netwerk in  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.ClassicCompute/virtualMachines  |  Netwerk uit  |  Netwerk uit  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.ClassicCompute/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts  |  Beschikbaarheid  |  Beschikbaarheid  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts  |  SuccessE2ELatency  |  Geslaagde E2E-latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts  |  SuccessServerLatency  |  Geslaagde serverlatentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.ClassicStorage/storageAccounts  |  Transacties  |  Transacties  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.ClassicStorage/storageAccounts  |  UsedCapacity  |  Gebruikte capaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/blobServices  |  Beschikbaarheid  |  Beschikbaarheid  |  Procent  |  Average | 
| Nee  | Nee |  Micro soft. ClassicStorage/Storage accounts/blobServices  |  BlobCapacity  |  Blobcapaciteit  |  Bytes  |  Average | 
| Nee  | Nee |  Micro soft. ClassicStorage/Storage accounts/blobServices  |  BlobCount  |  Aantal blobs  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/blobServices  |  ContainerCount  |  Aantal blobcontainers  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/blobServices  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| Nee  | Nee |  Micro soft. ClassicStorage/Storage accounts/blobServices  |  IndexCapacity  |  Index capaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/blobServices  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/blobServices  |  SuccessE2ELatency  |  Geslaagde E2E-latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/blobServices  |  SuccessServerLatency  |  Geslaagde serverlatentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/blobServices  |  Transacties  |  Transacties  |  Count  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/fileServices  |  Beschikbaarheid  |  Beschikbaarheid  |  Procent  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/fileServices  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| Nee  | Nee |  Micro soft. ClassicStorage/Storage accounts/fileServices  |  FileCapacity  |  Bestands capaciteit  |  Bytes  |  Average | 
| Nee  | Nee |  Micro soft. ClassicStorage/Storage accounts/fileServices  |  FileCount  |  Aantal bestanden  |  Count  |  Average | 
| Nee  | Nee |  Micro soft. ClassicStorage/Storage accounts/fileServices  |  FileShareCount  |  Aantal bestands shares  |  Count  |  Average | 
| Nee  | Nee |  Micro soft. ClassicStorage/Storage accounts/fileServices  |  FileShareQuota  |  Quota grootte van bestands share  |  Bytes  |  Average | 
| Nee  | Nee |  Micro soft. ClassicStorage/Storage accounts/fileServices  |  FileShareSnapshotCount  |  Aantal moment opnamen van bestands shares  |  Count  |  Average | 
| Nee  | Nee |  Micro soft. ClassicStorage/Storage accounts/fileServices  |  FileShareSnapshotSize  |  Grootte van moment opname van bestands share  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/fileServices  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/fileServices  |  SuccessE2ELatency  |  Geslaagde E2E-latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/fileServices  |  SuccessServerLatency  |  Geslaagde serverlatentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/fileServices  |  Transacties  |  Transacties  |  Count  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/queueServices  |  Beschikbaarheid  |  Beschikbaarheid  |  Procent  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/queueServices  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/queueServices  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/queueServices  |  QueueCapacity  |  Wachtrijcapaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/queueServices  |  QueueCount  |  Aantal wachtrijen  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/queueServices  |  QueueMessageCount  |  Aantal wachtrijberichten  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/queueServices  |  SuccessE2ELatency  |  Geslaagde E2E-latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/queueServices  |  SuccessServerLatency  |  Geslaagde serverlatentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/queueServices  |  Transacties  |  Transacties  |  Count  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/tableServices  |  Beschikbaarheid  |  Beschikbaarheid  |  Procent  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/tableServices  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/tableServices  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/tableServices  |  SuccessE2ELatency  |  Geslaagde E2E-latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/tableServices  |  SuccessServerLatency  |  Geslaagde serverlatentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/tableServices  |  TableCapacity  |  Tabelcapaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/tableServices  |  TableCount  |  Aantal tabellen  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/tableServices  |  TableEntityCount  |  Aantal tabelentiteiten  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/tableServices  |  Transacties  |  Transacties  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  BlockedCalls  |  Geblokkeerde aanroepen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  CharactersTrained  |  Getrainde tekens  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  CharactersTranslated  |  Geconverteerde tekens  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  ClientErrors  |  Client fouten  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  DataIn  |  Gegevens in  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  DataOut  |  Gegevens uit  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  Latentie  |  Latentie  |  MilliSeconds  |  Average | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  ServerErrors  |  Server fouten  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  SpeechSessionDuration  |  Spraak sessie duur  |  Waarna  |  Totaal | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  SuccessfulCalls  |  Geslaagde aanroepen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  TotalCalls  |  Totaal aantal aanroepen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  TotalErrors  |  Totale aantal fouten  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  TotalTokenCalls  |  Totaal aantal token aanroepen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.CognitiveServices/accounts  |  TotalTransactions  |  Totaal aantal trans acties  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Verbruikte CPU-tegoeden  |  Verbruikte CPU-tegoeden  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Resterende CPU-tegoeden  |  Resterende CPU-tegoeden  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Wachtrij diepte van gegevens schijf  |  Wachtrij diepte van gegevens schijf (preview-versie)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Gegevens schijf gelezen bytes per seconde  |  Gegevens schijf gelezen bytes per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Lees bewerkingen op de gegevens schijf per seconde  |  Lees bewerkingen op de gegevens schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Geschreven bytes per seconde gegevens schijf  |  Geschreven bytes per seconde (preview) gegevens schijf  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Schrijf bewerkingen op de gegevens schijf per seconde  |  Schrijf bewerkingen op de gegevens schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Gelezen bytes op de schijf  |  Gelezen bytes op de schijf  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Lees bewerkingen op de schijf per seconde  |  Lees bewerkingen op de schijf per seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Geschreven bytes op de schijf  |  Geschreven bytes op de schijf  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Schrijf bewerkingen op de schijf per seconde  |  Schrijf bewerkingen op de schijf per seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Binnenkomende stromen  |  Binnenkomende stromen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Maximum aanmaak frequentie inkomende stromen  |  Maximum aantal aanmaak snelheden inkomende stromen (preview-versie)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Netwerk in  |  Netwerk in Factureerbaar (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Totaal netwerk  |  Totaal netwerk  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Netwerk uit  |  Gefactureerd netwerk (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Totaal aantal netwerk  |  Totaal aantal netwerk  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Wachtrij diepte van besturingssysteem schijf  |  Wachtrij diepte van de besturingssysteem schijf (preview-versie)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  BESTURINGSSYSTEEM schijf gelezen bytes per seconde  |  BESTURINGSSYSTEEM schijf gelezen bytes per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Lees bewerkingen van de besturingssysteem schijf per seconde  |  Lees bewerkingen op de besturingssysteem schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  WACHTRIJ diepte voor het besturings systeem per schijf  |  WACHTRIJ diepte van de besturingssysteem schijf (afgeschaft)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  BESTURINGSSYSTEEM per schijf gelezen bytes per seconde  |  BESTURINGSSYSTEEM schijf gelezen bytes per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Lees bewerkingen per schijf voor het besturings systeem/sec.  |  Lees bewerkingen op de besturingssysteem schijf per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Per schijf geschreven bytes/sec.  |  BESTURINGSSYSTEEM schijf schrijf bewerkingen in bytes per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Schrijf bewerkingen per schijf van het besturings systeem/SEC  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Uitgaande stromen  |  Uitgaande stromen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Maximum aanmaak frequentie van uitgaande stromen  |  Maximum aantal uitgaande stromen voor maken (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  WACHTRIJ diepte per schijf  |  WACHTRIJ diepte van de gegevens schijf (afgeschaft)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Per schijf gelezen bytes/sec.  |  Gegevens schijf gelezen bytes per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Lees bewerkingen per schijf/sec.  |  Lees bewerkingen op de gegevens schijf per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Schrijf bewerkingen per schijf/sec.  |  Gegevens schijf schrijf bewerkingen in bytes per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Schrijf bewerkingen per schijf/SEC  |  Schrijf bewerkingen op de gegevens schijf per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Treffer voor Premium data-schijf cache lezen  |  Cache geheugen voor lezen van Premium-gegevens schijf (preview-versie)  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Lees missers cache Premium-gegevens schijf  |  Cache voor lezen van Premium-gegevens schijf (preview)  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Treffer voor Premium-besturingssysteem schijf cache lezen  |  Treffer voor het lezen van een Premium-besturingssysteem schijf cache (preview-versie)  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Leesmij voor Premium-besturingssysteem schijf cache lezen  |  Schijf cache voor Premium-Lees-missers (preview-versie)  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Verbruikte CPU-tegoeden  |  Verbruikte CPU-tegoeden  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Resterende CPU-tegoeden  |  Resterende CPU-tegoeden  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Wachtrij diepte van gegevens schijf  |  Wachtrij diepte van gegevens schijf (preview-versie)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Gegevens schijf gelezen bytes per seconde  |  Gegevens schijf gelezen bytes per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Lees bewerkingen op de gegevens schijf per seconde  |  Lees bewerkingen op de gegevens schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Geschreven bytes per seconde gegevens schijf  |  Geschreven bytes per seconde (preview) gegevens schijf  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Schrijf bewerkingen op de gegevens schijf per seconde  |  Schrijf bewerkingen op de gegevens schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Gelezen bytes op de schijf  |  Gelezen bytes op de schijf  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Lees bewerkingen op de schijf per seconde  |  Lees bewerkingen op de schijf per seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Geschreven bytes op de schijf  |  Geschreven bytes op de schijf  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Schrijf bewerkingen op de schijf per seconde  |  Schrijf bewerkingen op de schijf per seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Binnenkomende stromen  |  Binnenkomende stromen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Maximum aanmaak frequentie inkomende stromen  |  Maximum aantal aanmaak snelheden inkomende stromen (preview-versie)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Netwerk in  |  Netwerk in Factureerbaar (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Totaal netwerk  |  Totaal netwerk  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Netwerk uit  |  Gefactureerd netwerk (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Totaal aantal netwerk  |  Totaal aantal netwerk  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Wachtrij diepte van besturingssysteem schijf  |  Wachtrij diepte van de besturingssysteem schijf (preview-versie)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  BESTURINGSSYSTEEM schijf gelezen bytes per seconde  |  BESTURINGSSYSTEEM schijf gelezen bytes per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Lees bewerkingen van de besturingssysteem schijf per seconde  |  Lees bewerkingen op de besturingssysteem schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  WACHTRIJ diepte voor het besturings systeem per schijf  |  WACHTRIJ diepte van de besturingssysteem schijf (afgeschaft)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  BESTURINGSSYSTEEM per schijf gelezen bytes per seconde  |  BESTURINGSSYSTEEM schijf gelezen bytes per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Lees bewerkingen per schijf voor het besturings systeem/sec.  |  Lees bewerkingen op de besturingssysteem schijf per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Per schijf geschreven bytes/sec.  |  BESTURINGSSYSTEEM schijf schrijf bewerkingen in bytes per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Schrijf bewerkingen per schijf van het besturings systeem/SEC  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Uitgaande stromen  |  Uitgaande stromen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Maximum aanmaak frequentie van uitgaande stromen  |  Maximum aantal uitgaande stromen voor maken (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  WACHTRIJ diepte per schijf  |  WACHTRIJ diepte van de gegevens schijf (afgeschaft)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Per schijf gelezen bytes/sec.  |  Gegevens schijf gelezen bytes per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Lees bewerkingen per schijf/sec.  |  Lees bewerkingen op de gegevens schijf per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Schrijf bewerkingen per schijf/sec.  |  Gegevens schijf schrijf bewerkingen in bytes per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Schrijf bewerkingen per schijf/SEC  |  Schrijf bewerkingen op de gegevens schijf per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Percentage CPU  |  Percentage CPU  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Treffer voor Premium data-schijf cache lezen  |  Cache geheugen voor lezen van Premium-gegevens schijf (preview-versie)  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Lees missers cache Premium-gegevens schijf  |  Cache voor lezen van Premium-gegevens schijf (preview)  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Treffer voor Premium-besturingssysteem schijf cache lezen  |  Treffer voor het lezen van een Premium-besturingssysteem schijf cache (preview-versie)  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Leesmij voor Premium-besturingssysteem schijf cache lezen  |  Schijf cache voor Premium-Lees-missers (preview-versie)  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Verbruikte CPU-tegoeden  |  Verbruikte CPU-tegoeden  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Resterende CPU-tegoeden  |  Resterende CPU-tegoeden  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Wachtrij diepte van gegevens schijf  |  Wachtrij diepte van gegevens schijf (preview-versie)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Gegevens schijf gelezen bytes per seconde  |  Gegevens schijf gelezen bytes per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Lees bewerkingen op de gegevens schijf per seconde  |  Lees bewerkingen op de gegevens schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Geschreven bytes per seconde gegevens schijf  |  Geschreven bytes per seconde (preview) gegevens schijf  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Schrijf bewerkingen op de gegevens schijf per seconde  |  Schrijf bewerkingen op de gegevens schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Gelezen bytes op de schijf  |  Gelezen bytes op de schijf  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Lees bewerkingen op de schijf per seconde  |  Lees bewerkingen op de schijf per seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Geschreven bytes op de schijf  |  Geschreven bytes op de schijf  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Schrijf bewerkingen op de schijf per seconde  |  Schrijf bewerkingen op de schijf per seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Binnenkomende stromen  |  Binnenkomende stromen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Maximum aanmaak frequentie inkomende stromen  |  Maximum aantal aanmaak snelheden inkomende stromen (preview-versie)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Netwerk in  |  Netwerk in Factureerbaar (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Totaal netwerk  |  Totaal netwerk  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Netwerk uit  |  Gefactureerd netwerk (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Totaal aantal netwerk  |  Totaal aantal netwerk  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Wachtrij diepte van besturingssysteem schijf  |  Wachtrij diepte van de besturingssysteem schijf (preview-versie)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  BESTURINGSSYSTEEM schijf gelezen bytes per seconde  |  BESTURINGSSYSTEEM schijf gelezen bytes per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Lees bewerkingen van de besturingssysteem schijf per seconde  |  Lees bewerkingen op de besturingssysteem schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  WACHTRIJ diepte voor het besturings systeem per schijf  |  WACHTRIJ diepte van de besturingssysteem schijf (afgeschaft)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  BESTURINGSSYSTEEM per schijf gelezen bytes per seconde  |  BESTURINGSSYSTEEM schijf gelezen bytes per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Lees bewerkingen per schijf voor het besturings systeem/sec.  |  Lees bewerkingen op de besturingssysteem schijf per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Per schijf geschreven bytes/sec.  |  BESTURINGSSYSTEEM schijf schrijf bewerkingen in bytes per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Schrijf bewerkingen per schijf van het besturings systeem/SEC  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Uitgaande stromen  |  Uitgaande stromen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Maximum aanmaak frequentie van uitgaande stromen  |  Maximum aantal uitgaande stromen voor maken (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  WACHTRIJ diepte per schijf  |  WACHTRIJ diepte van de gegevens schijf (afgeschaft)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Per schijf gelezen bytes/sec.  |  Gegevens schijf gelezen bytes per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Lees bewerkingen per schijf/sec.  |  Lees bewerkingen op de gegevens schijf per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Schrijf bewerkingen per schijf/sec.  |  Gegevens schijf schrijf bewerkingen in bytes per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Schrijf bewerkingen per schijf/SEC  |  Schrijf bewerkingen op de gegevens schijf per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Treffer voor Premium data-schijf cache lezen  |  Cache geheugen voor lezen van Premium-gegevens schijf (preview-versie)  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Lees missers cache Premium-gegevens schijf  |  Cache voor lezen van Premium-gegevens schijf (preview)  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Treffer voor Premium-besturingssysteem schijf cache lezen  |  Treffer voor het lezen van een Premium-besturingssysteem schijf cache (preview-versie)  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets/virtualMachines  |  Leesmij voor Premium-besturingssysteem schijf cache lezen  |  Schijf cache voor Premium-Lees-missers (preview-versie)  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.ContainerInstance/containerGroups  |  CpuUsage  |  CPU-gebruik  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.ContainerInstance/containerGroups  |  MemoryUsage  |  Geheugen gebruik  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesReceivedPerSecond  |  Ontvangen netwerk bytes per seconde  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.ContainerInstance/containerGroups  |  NetworkBytesTransmittedPerSecond  |  Verzonden netwerk bytes per seconde  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. ContainerRegistry/registers  |  RunDuration  |  Uitvoerings duur  |  Milliseconden  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ContainerRegistry/registers  |  SuccessfulPullCount  |  Aantal geslaagde pull-bewerkingen  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. ContainerRegistry/registers  |  SuccessfulPushCount  |  Aantal geslaagde push berichten  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. ContainerRegistry/registers  |  TotalPullCount  |  Totaal aantal pull-bewerkingen  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. ContainerRegistry/registers  |  TotalPushCount  |  Totaal aantal push berichten  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_cpu_cores  |  Totaal aantal beschik bare CPU-kernen in een beheerd cluster  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ContainerService/managedClusters  |  kube_node_status_allocatable_memory_bytes  |  Totale hoeveelheid beschikbaar geheugen in een beheerd cluster  |  Bytes  |  Average | 
| Nee  | Nee |  Microsoft.ContainerService/managedClusters  |  kube_node_status_condition  |  Statussen voor de verschillende knooppunt voorwaarden  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_phase  |  Aantal per fase  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ContainerService/managedClusters  |  kube_pod_status_ready  |  Aantal in de status gereed  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  Availablecapacity;)  |  Beschik bare capaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloud  |  Geüploade Cloud bytes (apparaat)  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloudPerShare  |  Geüploade Cloud bytes (delen)  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughput  |  Door Voer van Cloud downloaden  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughputPerShare  |  Door Voer van Cloud downloaden (delen)  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughput  |  Upload doorvoer van Cloud  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughputPerShare  |  Upload doorvoer van Cloud (delen)  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  HyperVMemoryUtilization  |  Edge Compute-geheugen gebruik  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  HyperVVirtualProcessorUtilization  |  Edge Compute-percentage CPU  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  NICReadThroughput  |  Lees doorvoer (netwerk)  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  NICWriteThroughput  |  Schrijf doorvoer (netwerk)  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.DataBoxEdge/dataBoxEdgeDevices  |  TotalCapacity  |  Totale capaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.DataFactory/datafactories  |  FailedRuns  |  Mislukte uitvoeringen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataFactory/datafactories  |  SuccessfulRuns  |  Geslaagde uitvoeringen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataFactory/factories  |  ActivityCancelledRuns  |  Metrische gegevens voor geannuleerde activiteit uitgevoerd  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataFactory/factories  |  ActivityFailedRuns  |  Metrische gegevens mislukte uitvoering van activiteit  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataFactory/factories  |  ActivitySucceededRuns  |  Metrische gegevens uitvoeringen uitgevoerde activiteit  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataFactory/factories  |  FactorySizeInGbUnits  |  Totale grootte van de fabriek (GB-eenheid)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.DataFactory/factories  |  IntegrationRuntimeAvailableMemory  |  Beschik bare geheugen voor Integration runtime  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.DataFactory/factories  |  IntegrationRuntimeAverageTaskPickupDelay  |  Duur van de wachtrij voor Integration runtime  |  Waarna  |  Average | 
| **Ja**  | Nee |  Microsoft.DataFactory/factories  |  IntegrationRuntimeCpuPercentage  |  CPU-gebruik van Integration runtime  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.DataFactory/factories  |  IntegrationRuntimeQueueLength  |  Lengte van de wachtrij voor Integration runtime  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.DataFactory/factories  |  MaxAllowedFactorySizeInGbUnits  |  Maxi maal toegestane grootte van de fabriek (GB-eenheid)  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.DataFactory/factories  |  MaxAllowedResourceCount  |  Maximum aantal toegestane entiteiten  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.DataFactory/factories  |  PipelineCancelledRuns  |  Metrische gegevens van de pijplijn uitvoeringen geannuleerd  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataFactory/factories  |  PipelineFailedRuns  |  Metrische gegevens van mislukte pijplijn uitvoeringen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataFactory/factories  |  PipelineSucceededRuns  |  Metrische uitvoerings metingen geslaagde pijp lijnen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataFactory/factories  |  ResourceCount  |  Totaal aantal entiteiten  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.DataFactory/factories  |  TriggerCancelledRuns  |  Metrische gegevens over de trigger is geannuleerd  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataFactory/factories  |  TriggerFailedRuns  |  Meet waarden voor uitvoering van mislukte triggers  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataFactory/factories  |  TriggerSucceededRuns  |  Meet waarden voor uitvoering van geslaagde triggers  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedCancelled  |  Geannuleerde AU-tijd  |  Waarna  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedFailure  |  Mislukte AU-tijd  |  Waarna  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataLakeAnalytics/accounts  |  JobAUEndedSuccess  |  Geslaagde AU-tijd  |  Waarna  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedCancelled  |  Geannuleerde taken  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedFailure  |  Mislukte taken  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataLakeAnalytics/accounts  |  JobEndedSuccess  |  Geslaagde taken  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataLakeStore/accounts  |  DataRead  |  Gegevens lezen  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataLakeStore/accounts  |  DataWritten  |  Gegevens geschreven  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataLakeStore/accounts  |  ReadRequests  |  Aanvragen lezen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DataLakeStore/accounts  |  TotalStorage  |  Totale opslag  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.DataLakeStore/accounts  |  WriteRequests  |  Aanvragen schrijven  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  active_connections  |  Actieve verbindingen  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  backup_storage_used  |  Gebruikte back-upopslag  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  connections_failed  |  Mislukte verbindingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  cpu_percent  |  CPU-percentage  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  io_consumption_percent  |  IO-percentage  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  memory_percent  |  Geheugen percentage  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  network_bytes_egress  |  Netwerk uit  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  network_bytes_ingress  |  Netwerk in  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  seconds_behind_master  |  Replicatie vertraging in seconden  |  Count  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_limit  |  Opslag limiet voor server logboek  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_percent  |  Percentage server logboek opslag  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_usage  |  Gebruikte server logboek opslag  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  storage_limit  |  Opslag limiet  |  Bytes  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  storage_percent  |  Opslag percentage  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  storage_used  |  Gebruikte opslag  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  active_connections  |  Actieve verbindingen  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  backup_storage_used  |  Gebruikte back-upopslag  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  connections_failed  |  Mislukte verbindingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  cpu_percent  |  CPU-percentage  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  io_consumption_percent  |  IO-percentage  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  memory_percent  |  Geheugen percentage  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  network_bytes_egress  |  Netwerk uit  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  network_bytes_ingress  |  Netwerk in  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  seconds_behind_master  |  Replicatie vertraging in seconden  |  Count  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_limit  |  Opslag limiet voor server logboek  |  Bytes  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_percent  |  Percentage server logboek opslag  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_usage  |  Gebruikte server logboek opslag  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  storage_limit  |  Opslag limiet  |  Bytes  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  storage_percent  |  Opslag percentage  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  storage_used  |  Gebruikte opslag  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  active_connections  |  Actieve verbindingen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  backup_storage_used  |  Gebruikte back-upopslag  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  connections_failed  |  Mislukte verbindingen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  cpu_percent  |  CPU-percentage  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  io_consumption_percent  |  IO-percentage  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  memory_percent  |  Geheugen percentage  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_egress  |  Netwerk uit  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_ingress  |  Netwerk in  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_bytes  |  Maximale vertraging in Replica's  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_seconds  |  Replica vertraging  |  Waarna  |  Maximum | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_limit  |  Opslag limiet voor server logboek  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_percent  |  Percentage server logboek opslag  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_usage  |  Gebruikte server logboek opslag  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  storage_limit  |  Opslag limiet  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  storage_percent  |  Opslag percentage  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  storage_used  |  Gebruikte opslag  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/serversv2  |  active_connections  |  Actieve verbindingen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/serversv2  |  cpu_percent  |  CPU-percentage  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/serversv2  |  IOPS  |  IOPS  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/serversv2  |  memory_percent  |  Geheugen percentage  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_egress  |  Netwerk uit  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/serversv2  |  network_bytes_ingress  |  Netwerk in  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/serversv2  |  storage_percent  |  Opslag percentage  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/serversv2  |  storage_used  |  Gebruikte opslag  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Micro soft. apparaten/account  |  digitaltwins. telemetrie. nodes  |  Tijdelijke aanduiding voor telemetrie van Digital Apparaatdubbels-knoop punt  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.abandon.success  |  C2D-berichten zijn afgebroken  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  C2D. commands. OUTuitgang. complete. geslaagd  |  C2D-bericht leveringen voltooid  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.commands.egress.reject.success  |  Geweigerde C2D-berichten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  C2D. methods. failure  |  Mislukte directe aanroepen van methode  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  C2D. methods. requestSize  |  Aanvraag grootte van directe-methode aanroepen  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  C2D. methods. responseSize  |  Antwoord grootte van directe methode aanroepen  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  C2D. methods. geslaagd  |  Geslaagde directe aanroepen van de methode  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.failure  |  Mislukte dubbele Lees bewerkingen van back-end  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.size  |  Reactie grootte van dubbele Lees bewerkingen van de back-end  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.twin.read.success  |  Geslaagde dubbele Lees bewerkingen van back-end  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  C2D. dubbele. update. failure  |  Mislukte dubbele updates van back-end  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  C2D. dubbele. update. grootte  |  Grootte van dubbele updates van back-end  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  c2d.twin.update.success  |  Geslaagde dubbele updates van back-end  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  C2DMessagesExpired  |  C2D-berichten verlopen (preview-versie)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  configuraties  |  Metrische configuratie gegevens  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Devices/IotHubs  |  connectedDeviceCount  |  Verbonden apparaten (preview-versie)  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  D2C. endpoints. uitgangs punt. builtIn. Events  |  Route ring: berichten worden bezorgd bij berichten/gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.eventHubs  |  Route ring: berichten worden bezorgd bij Event hub  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusQueues  |  Route ring: berichten worden bezorgd bij Service Bus wachtrij  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.serviceBusTopics  |  Route ring: berichten die worden bezorgd bij Service Bus onderwerp  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage  |  Route ring: berichten worden bezorgd bij de opslag  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.egress.storage.blobs  |  Route ring: blobs die aan de opslag worden geleverd  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  D2C. endpoints. out. storage. bytes  |  Route ring: gegevens worden geleverd aan de opslag  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.builtIn.events  |  Route ring: bericht latentie voor berichten/gebeurtenissen  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.eventHubs  |  Route ring: bericht latentie voor Event hub  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusQueues  |  Route ring: bericht latentie voor Service Bus wachtrij  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.serviceBusTopics  |  Route ring: bericht latentie voor Service Bus onderwerp  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.endpoints.latency.storage  |  Route ring: bericht latentie voor opslag  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.dropped  |  Route ring: telemetrie-berichten verwijderd   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.fallback  |  Route ring: berichten worden bezorgd bij terugval  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.invalid  |  Route ring: telemetrie-berichten incompatibel  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.orphaned  |  Route ring: telemetriegegevens van zwevende berichten   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.egress.success  |  Route ring: telemetrie-berichten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.allProtocol  |  Verzend pogingen voor telemetrie-berichten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.sendThrottle  |  Aantal beperkings fouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.telemetry.ingress.success  |  Verzonden telemetriegegevens  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  D2C. dubbele. Read. failure  |  Mislukte dubbele Lees bewerkingen van apparaten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.size  |  Reactie grootte van dubbele Lees bewerkingen van apparaten  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.twin.read.success  |  Geslaagde dubbele Lees bewerkingen van apparaten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  D2C. dubbele. update. failure  |  Mislukte dubbele updates van apparaten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  D2C. dubbele. update. grootte  |  Grootte van dubbele updates van apparaten  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  d2c.twin.update.success  |  Geslaagde dubbele updates van apparaten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  dailyMessageQuotaUsed  |  Totaal aantal gebruikte berichten  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  deviceDataUsage  |  Totale hoeveelheid gegevens gebruik van apparaat  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  deviceDataUsageV2  |  Totaal gebruik van apparaatgegevens (preview-versie)  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  devices.connectedDevices.allProtocol  |  Verbonden apparaten (afgeschaft)   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  devices.totalDevices  |  Totaal aantal apparaten (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  EventGridDeliveries  |  Event Grid leveringen (preview-versie)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  EventGridLatency  |  Event Grid latentie (preview-versie)  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.failure  |  Mislukte taak annuleringen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.cancelJob.success  |  Voltooide taak annuleringen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  Jobs. voltooid  |  Voltooide taken  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.failure  |  Kan geen aanroepen van methode aanroep taken uitvoeren  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.createDirectMethodJob.success  |  Geslaagde creatie van methode aanroep taken  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.failure  |  Kan geen dubbele update taken uitvoeren  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.createTwinUpdateJob.success  |  Geslaagde creatie van dubbele update taken  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  Jobs. mislukt  |  Mislukte taken  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.failure  |  Mislukte aanroepen naar lijst taken  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.listJobs.success  |  Geslaagde aanroepen naar lijst taken  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.failure  |  Mislukte taak query's  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  jobs.queryJobs.success  |  Geslaagde taak query's  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Devices/IotHubs  |  totalDeviceCount  |  Totaal aantal apparaten (preview-versie)  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  twinQueries.failure  |  Mislukte dubbele query's  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  twinQueries.resultSize  |  Resultaat grootte van dubbele query's  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Devices/IotHubs  |  twinQueries.success  |  Geslaagde dubbele query's  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/provisioningServices  |  AttestationAttempts  |  Attestation-pogingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/provisioningServices  |  DeviceAssignments  |  Apparaten toegewezen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Devices/provisioningServices  |  RegistrationAttempts  |  Registratie pogingen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  AvailableStorage  |  Beschikbare opslag  |  Bytes  |  Totaal | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  CassandraConnectionClosures  |  Cassandra-verbinding sluiten  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequestCharges  |  Kosten voor Cassandra-aanvragen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequests  |  Cassandra aanvragen  |  Count  |  Count | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  DataUsage  |  Gegevensgebruik  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  DeleteVirtualNetwork  |  DeleteVirtualNetwork  |  Count  |  Count | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  DocumentCount  |  Aantal documenten  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  DocumentQuota  |  Document quotum  |  Bytes  |  Totaal | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  IndexUsage  |  Index gebruik  |  Bytes  |  Totaal | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  MetadataRequests  |  Meta gegevens aanvragen  |  Count  |  Count | 
| **Ja**  | **Ja** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestCharge  |  Kosten voor Mongo-aanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequests  |  Mongo aanvragen  |  Count  |  Count | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsCount  |  Frequentie van Mongo-aanvragen  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsDelete  |  Aantal Mongo-aanvragen voor verwijderen  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsInsert  |  Aantal Mongo invoegen  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsQuery  |  Frequentie van Mongo-query aanvragen  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsUpdate  |  Frequentie van Mongo-update aanvragen  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  ProvisionedThroughput  |  Ingerichte doorvoer  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  ReplicationLatency  |  P99-replicatie latentie  |  MilliSeconds  |  Average | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  ServiceAvailability  |  Service beschikbaarheid  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequests  |  Totaal aantal aanvragen  |  Count  |  Count | 
| **Ja**  | **Ja** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequestUnits  |  Totaal aantal aanvraag eenheden  |  Count  |  Totaal | 
| Nee  | Nee |  Micro soft. EnterpriseKnowledgeGraph/Services  |  FailureCount  |  Aantal fouten  |  Count  |  Count | 
| Nee  | Nee |  Micro soft. EnterpriseKnowledgeGraph/Services  |  SuccessCount  |  Aantal geslaagde pogingen  |  Count  |  Count | 
| Nee  | Nee |  Micro soft. EnterpriseKnowledgeGraph/Services  |  SuccessLatency  |  Geslaagde latentie  |  MilliSeconds  |  Average | 
| Nee  | Nee |  Micro soft. EnterpriseKnowledgeGraph/Services  |  TransactionCount  |  Aantal trans acties  |  Count  |  Count | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/domeinen  |  DeadLetteredCount  |  Gebeurtenissen met onbestelbare berichten  |  Count  |  Totaal | 
| Nee  | Nee |  Micro soft. EventGrid/domeinen  |  DeliveryAttemptFailCount  |  Mislukte leverings gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/domeinen  |  DeliverySuccessCount  |  Geleverde gebeurtenissen  |  Count  |  Totaal | 
| Nee  | Nee |  Micro soft. EventGrid/domeinen  |  DestinationProcessingDurationInMs  |  Doel verwerkings duur  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/domeinen  |  DroppedEventCount  |  Verwijderde gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/domeinen  |  MatchedEventCount  |  Overeenkomende gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/domeinen  |  PublishFailCount  |  Mislukte gebeurtenissen publiceren  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/domeinen  |  PublishSuccessCount  |  Gepubliceerde gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/domeinen  |  PublishSuccessLatencyInMs  |  Latentie van publicatie geslaagd  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/eventSubscriptions  |  DeadLetteredCount  |  Gebeurtenissen met onbestelbare berichten  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventGrid/eventSubscriptions  |  DeliveryAttemptFailCount  |  Mislukte leverings gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/eventSubscriptions  |  DeliverySuccessCount  |  Geleverde gebeurtenissen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventGrid/eventSubscriptions  |  DestinationProcessingDurationInMs  |  Doel verwerkings duur  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/eventSubscriptions  |  DroppedEventCount  |  Verwijderde gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/eventSubscriptions  |  MatchedEventCount  |  Overeenkomende gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/extensionTopics  |  PublishFailCount  |  Mislukte gebeurtenissen publiceren  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/extensionTopics  |  PublishSuccessCount  |  Gepubliceerde gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/extensionTopics  |  PublishSuccessLatencyInMs  |  Latentie van publicatie geslaagd  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventGrid/extensionTopics  |  UnmatchedEventCount  |  Niet-overeenkomende gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/topics  |  PublishFailCount  |  Mislukte gebeurtenissen publiceren  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/topics  |  PublishSuccessCount  |  Gepubliceerde gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/topics  |  PublishSuccessLatencyInMs  |  Latentie van publicatie geslaagd  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/topics  |  UnmatchedEventCount  |  Niet-overeenkomende gebeurtenissen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/clusters  |  ActiveConnections  |  ActiveConnections  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.EventHub/clusters  |  AvailableMemory  |  Beschikbaar geheugen  |  Procent  |  Maximum | 
| Nee  | Nee |  Microsoft.EventHub/clusters  |  CaptureBacklog  |  Achterstand vastleggen.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/clusters  |  CapturedBytes  |  Vastgelegde bytes.  |  Bytes  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/clusters  |  CapturedMessages  |  Vastgelegde berichten.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/clusters  |  ConnectionsClosed  |  Verbindingen gesloten.  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.EventHub/clusters  |  ConnectionsOpened  |  Geopende verbindingen.  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.EventHub/clusters  |  CPU  |  CPU  |  Procent  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.EventHub/clusters  |  IncomingBytes  |  Binnenkomende bytes.  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/clusters  |  IncomingMessages  |  Inkomende berichten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/clusters  |  IncomingRequests  |  Binnenkomende aanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/clusters  |  OutgoingBytes  |  Uitgaande bytes.  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/clusters  |  OutgoingMessages  |  Uitgaande berichten  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/clusters  |  QuotaExceededErrors  |  Quota overschreden fouten.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/clusters  |  ServerErrors  |  Server fouten.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/clusters  |  SuccessfulRequests  |  Geslaagde aanvragen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/clusters  |  ThrottledRequests  |  Vertraagde aanvragen.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/clusters  |  UserErrors  |  Gebruikers fouten.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/namespaces  |  ActiveConnections  |  ActiveConnections  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.EventHub/namespaces  |  CaptureBacklog  |  Achterstand vastleggen.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/namespaces  |  CapturedBytes  |  Vastgelegde bytes.  |  Bytes  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/namespaces  |  CapturedMessages  |  Vastgelegde berichten.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/namespaces  |  ConnectionsClosed  |  Verbindingen gesloten.  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.EventHub/namespaces  |  ConnectionsOpened  |  Geopende verbindingen.  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  EHABL  |  Achterstallige berichten archiveren (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  EHAMBS  |  Berichten doorvoer archiveren (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  EHAMSGS  |  Berichten archiveren (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  EHINBYTES  |  Binnenkomende bytes (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  EHINMBS  |  Binnenkomende bytes (verouderd) (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  EHINMSGS  |  Inkomende berichten (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  EHOUTBYTES  |  Uitgaande bytes (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  EHOUTMBS  |  Uitgaande bytes (verouderd) (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  EHOUTMSGS  |  Uitgaande berichten (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  FAILREQ  |  Mislukte aanvragen (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  IncomingBytes  |  Binnenkomende bytes.  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  IncomingMessages  |  Inkomende berichten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  IncomingRequests  |  Binnenkomende aanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  INMSGS  |  Binnenkomende berichten (verouderd) (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  INREQS  |  Binnenkomende aanvragen (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  INTERer  |  Interne server fouten (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  MISCERR  |  Andere fouten (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  OutgoingBytes  |  Uitgaande bytes.  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  OutgoingMessages  |  Uitgaande berichten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  OUTMSGS  |  Uitgaande berichten (verouderd) (afgeschaft)  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/namespaces  |  QuotaExceededErrors  |  Quota overschreden fouten.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/namespaces  |  ServerErrors  |  Server fouten.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/namespaces  |  Grootte  |  Grootte  |  Bytes  |  Average | 
| Nee  | Nee |  Microsoft.EventHub/namespaces  |  SuccessfulRequests  |  Geslaagde aanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  SUCCREQ  |  Geslaagde aanvragen (afgeschaft)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  SVRBSY  |  Fouten bij server bezet (afgeschaft)  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/namespaces  |  ThrottledRequests  |  Vertraagde aanvragen.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/namespaces  |  UserErrors  |  Gebruikers fouten.  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.HDInsight/clusters  |  CategorizedGatewayRequests  |  Gecategoriseerde gateway aanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.HDInsight/clusters  |  GatewayRequests  |  Gateway aanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.HDInsight/clusters  |  NumActiveWorkers  |  Aantal actieve werk rollen  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.HDInsight/clusters  |  ScalingRequests  |  Aanvragen schalen  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Insights/AutoscaleSettings  |  MetricThreshold  |  Drempel waarde voor metrische gegevens  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Insights/AutoscaleSettings  |  ObservedCapacity  |  Waargenomen capaciteit  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Insights/AutoscaleSettings  |  ObservedMetricValue  |  Waargenomen metrische waarde  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Insights/AutoscaleSettings  |  ScaleActionsInitiated  |  Schaal acties gestart  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Insights/Components  |  availabilityResults/availabilityPercentage  |  Beschikbaarheid  |  Procent  |  Average | 
| Nee  | Nee |  Microsoft.Insights/Components  |  availabilityResults/aantal  |  Beschikbaarheidstests  |  Count  |  Count | 
| **Ja**  | Nee |  Microsoft.Insights/Components  |  availabilityResults/duur  |  Duur beschikbaarheids test  |  MilliSeconds  |  Average | 
| **Ja**  | Nee |  Microsoft.Insights/Components  |  browserTimings/networkDuration  |  Netwerk verbindings tijd voor laden van pagina  |  MilliSeconds  |  Average | 
| **Ja**  | Nee |  Microsoft.Insights/Components  |  browserTimings/processingDuration  |  Verwerkings tijd van client  |  MilliSeconds  |  Average | 
| **Ja**  | Nee |  Microsoft.Insights/Components  |  browserTimings/receiveDuration  |  Reactie tijd van ontvangst  |  MilliSeconds  |  Average | 
| **Ja**  | Nee |  Microsoft.Insights/Components  |  browserTimings/sendDuration  |  Aanvraag tijd verzenden  |  MilliSeconds  |  Average | 
| **Ja**  | Nee |  Microsoft.Insights/Components  |  browserTimings/totalDuration  |  Laad tijd van browser pagina  |  MilliSeconds  |  Average | 
| Nee  | Nee |  Microsoft.Insights/Components  |  afhankelijkheden/aantal  |  Afhankelijkheids aanroepen  |  Count  |  Count | 
| **Ja**  | Nee |  Microsoft.Insights/Components  |  afhankelijkheden/duur  |  Duur van afhankelijkheid  |  MilliSeconds  |  Average | 
| Nee  | Nee |  Microsoft.Insights/Components  |  afhankelijkheden/mislukt  |  Mislukte afhankelijkheids aanroepen  |  Count  |  Count | 
| Nee  | Nee |  Microsoft.Insights/Components  |  uitzonde ringen/browser  |  Browseruitzonderingen  |  Count  |  Count | 
| **Ja**  | **Ja** |  Microsoft.Insights/Components  |  uitzonde ringen/aantal  |  Uitzonderingen  |  Count  |  Count | 
| Nee  | Nee |  Microsoft.Insights/Components  |  uitzonde ringen/server  |  Server uitzonderingen  |  Count  |  Count | 
| **Ja**  | **Ja** |  Microsoft.Insights/Components  |  Page views/aantal  |  Pagina weergaven  |  Count  |  Count | 
| **Ja**  | Nee |  Microsoft.Insights/Components  |  Page views/duur  |  Laad tijd pagina weergave  |  MilliSeconds  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Insights/Components  |  performanceCounters/exceptionsPerSecond  |  Uitzonderings frequentie  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Insights/Components  |  performanceCounters/memoryAvailableBytes  |  Beschikbaar geheugen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Insights/Components  |  performanceCounters/processCpuPercentage  |  CPU verwerken  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Insights/Components  |  performanceCounters/processIOBytesPerSecond  |  I/o-frequentie van processen  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Insights/Components  |  performanceCounters/processorCpuPercentage  |  Processor tijd  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Insights/Components  |  performanceCounters/processPrivateBytes  |  Privé-bytes verwerken  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Insights/Components  |  performanceCounters/requestExecutionTime  |  Uitvoerings tijd van de HTTP-aanvraag  |  MilliSeconds  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Insights/Components  |  Performance Counters/requestsInQueue  |  HTTP-aanvragen in de toepassings wachtrij  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Insights/Components  |  performanceCounters/requestsPerSecond  |  Frequentie van HTTP-aanvragen  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Microsoft.Insights/Components  |  aanvragen/aantal  |  Server aanvragen  |  Count  |  Count | 
| **Ja**  | Nee |  Microsoft.Insights/Components  |  aanvragen/duur  |  Serverreactietijd  |  MilliSeconds  |  Average | 
| Nee  | Nee |  Microsoft.Insights/Components  |  aanvragen/mislukt  |  Mislukte aanvragen  |  Count  |  Count | 
| Nee  | Nee |  Microsoft.Insights/Components  |  aanvragen/frequentie  |  Aantal server aanvragen  |  CountPerSecond  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Insights/Components  |  traceringen/aantal  |  Traceringen  |  Count  |  Count | 
| **Ja**  | Nee |  Microsoft.KeyVault/vaults  |  ServiceApiHit  |  Totaal aantal treffers in de service-API  |  Count  |  Count | 
| **Ja**  | Nee |  Microsoft.KeyVault/vaults  |  ServiceApiLatency  |  Algehele latentie van Service-API  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.KeyVault/vaults  |  ServiceApiResult  |  Totale resultaten van Service-API  |  Count  |  Count | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  CacheUtilization  |  Cache gebruik  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  ContinuousExportMaxLatenessMinutes  |  Maximale achterstand voor continue export  |  Count  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  ContinuousExportNumOfRecordsExported  |  Doorlopend exporteren: aantal geëxporteerde records  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  ContinuousExportPendingCount  |  Aantal doorlopend exporteren in behandeling  |  Count  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  ContinuousExportResult  |  Resultaat doorlopend exporteren  |  Count  |  Count | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  CPU  |  CPU  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  EventsProcessedForEventHubs  |  Verwerkte gebeurtenissen (voor gebeurtenis/IoT-hubs)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  ExportUtilization  |  Gebruik exporteren  |  Procent  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  IngestionLatencyInSeconds  |  Opname latentie (in seconden)  |  Waarna  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  IngestionResult  |  Opname resultaat  |  Count  |  Count | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  IngestionUtilization  |  Opname gebruik  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  IngestionVolumeInMB  |  Opname volume (in MB)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  KeepAlive  |  Actief houden  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  QueryDuration  |  Queryduur  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  SteamingIngestRequestRate  |  Aanvraag frequentie voor streaming-opname  |  Count  |  RateRequestsPerSecond | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  StreamingIngestDataRate  |  Gegevens frequentie van streaming opname  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  StreamingIngestDuration  |  Opname duur van streaming  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Kusto/Clusters  |  StreamingIngestResults  |  Resultaat van streaming-opname  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Logic/integrationServiceEnvironments  |  ActionLatency  |  Actie latentie   |  Waarna  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsCompleted  |  Acties voltooid   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsFailed  |  Mislukte acties   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsSkipped  |  Overgeslagen acties   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsStarted  |  Gestarte acties   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionsSucceeded  |  Acties geslaagd   |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Logic/integrationServiceEnvironments  |  ActionSuccessLatency  |  Latentie geslaagde acties   |  Waarna  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  ActionThrottledEvents  |  Door actie vertraagde gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorMemoryUsage  |  Geheugen gebruik van connector voor Integratieserviceomgeving  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorProcessorUsage  |  Processor gebruik van connector voor Integratieserviceomgeving  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentWorkflowMemoryUsage  |  Geheugen gebruik van werk stroom voor Integratieserviceomgeving  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentWorkflowProcessorUsage  |  Gebruik van werk stroom processor voor Integratieserviceomgeving  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  RunFailurePercentage  |  Percentage mislukte uitvoeringen  |  Procent  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Logic/integrationServiceEnvironments  |  RunLatency  |  Uitvoerings latentie  |  Waarna  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsCancelled  |  Uitvoeringen geannuleerd  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsCompleted  |  Uitvoeringen voltooid  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsFailed  |  Uitvoeringen mislukt  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsStarted  |  Uitvoeringen gestart  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  RunsSucceeded  |  Geslaagde uitvoeringen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  RunStartThrottledEvents  |  Vertraagde gebeurtenissen uitvoeren  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Logic/integrationServiceEnvironments  |  RunSuccessLatency  |  Latentie van geslaagde uitvoering  |  Waarna  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  RunThrottledEvents  |  Vertraagde gebeurtenissen uitvoeren  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerFireLatency  |  Brand latentie activeren   |  Waarna  |  Average | 
| **Ja**  | Nee |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerLatency  |  Latentie van trigger   |  Waarna  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersCompleted  |  Triggers voltooid   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersFailed  |  Mislukte triggers   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersFired  |  Geactiveerde triggers   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersSkipped  |  Triggers overgeslagen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersStarted  |  Triggers gestart   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggersSucceeded  |  Geslaagde triggers   |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerSuccessLatency  |  Latentie van trigger geslaagd   |  Waarna  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Logic/integrationServiceEnvironments  |  TriggerThrottledEvents  |  Trigger beperkings gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Logic/workflows  |  ActionLatency  |  Actie latentie   |  Waarna  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  ActionsCompleted  |  Acties voltooid   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  ActionsFailed  |  Mislukte acties   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  ActionsSkipped  |  Overgeslagen acties   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  ActionsStarted  |  Gestarte acties   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  ActionsSucceeded  |  Acties geslaagd   |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Logic/workflows  |  ActionSuccessLatency  |  Latentie geslaagde acties   |  Waarna  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  ActionThrottledEvents  |  Door actie vertraagde gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  BillableActionExecutions  |  Factureer bare actie-uitvoeringen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  BillableTriggerExecutions  |  Factureer bare trigger uitvoeringen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  BillingUsageNativeOperation  |  Facturerings gebruik voor uitvoering van systeem eigen bewerkingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  BillingUsageNativeOperation  |  Facturerings gebruik voor uitvoering van systeem eigen bewerkingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  BillingUsageStandardConnector  |  Facturerings gebruik voor het uitvoeren van standaard-connectors  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  BillingUsageStandardConnector  |  Facturerings gebruik voor het uitvoeren van standaard-connectors  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  BillingUsageStorageConsumption  |  Facturerings gebruik voor uitvoeringen van opslag verbruik  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  BillingUsageStorageConsumption  |  Facturerings gebruik voor uitvoeringen van opslag verbruik  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  RunFailurePercentage  |  Percentage mislukte uitvoeringen  |  Procent  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Logic/workflows  |  RunLatency  |  Uitvoerings latentie  |  Waarna  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  RunsCancelled  |  Uitvoeringen geannuleerd  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  RunsCompleted  |  Uitvoeringen voltooid  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  RunsFailed  |  Uitvoeringen mislukt  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  RunsStarted  |  Uitvoeringen gestart  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  RunsSucceeded  |  Geslaagde uitvoeringen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  RunStartThrottledEvents  |  Vertraagde gebeurtenissen uitvoeren  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Logic/workflows  |  RunSuccessLatency  |  Latentie van geslaagde uitvoering  |  Waarna  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  RunThrottledEvents  |  Vertraagde gebeurtenissen uitvoeren  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  TotalBillableExecutions  |  Totaal aantal factureer bare uitvoeringen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Logic/workflows  |  TriggerFireLatency  |  Brand latentie activeren   |  Waarna  |  Average | 
| **Ja**  | Nee |  Microsoft.Logic/workflows  |  TriggerLatency  |  Latentie van trigger   |  Waarna  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  TriggersCompleted  |  Triggers voltooid   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  TriggersFailed  |  Mislukte triggers   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  TriggersFired  |  Geactiveerde triggers   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  TriggersSkipped  |  Triggers overgeslagen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  TriggersStarted  |  Triggers gestart   |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  TriggersSucceeded  |  Geslaagde triggers   |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Logic/workflows  |  TriggerSuccessLatency  |  Latentie van trigger geslaagd   |  Waarna  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Logic/workflows  |  TriggerThrottledEvents  |  Trigger beperkings gebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/workspaces  |  Actieve kernen  |  Actieve kernen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/workspaces  |  Actieve knoop punten  |  Actieve knoop punten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/workspaces  |  Voltooide uitvoeringen  |  Voltooide uitvoeringen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/workspaces  |  Mislukte uitvoeringen  |  Mislukte uitvoeringen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/workspaces  |  Niet-actieve kernen  |  Niet-actieve kernen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/workspaces  |  Niet-actieve knoop punten  |  Niet-actieve knoop punten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/workspaces  |  Kernen verlaten  |  Kernen verlaten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/workspaces  |  Knoop punten verlaten  |  Knoop punten verlaten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/workspaces  |  Modelimplementatie is mislukt  |  Modelimplementatie is mislukt  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/workspaces  |  Modelimplementatie gestart  |  Modelimplementatie gestart  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/workspaces  |  Modelimplementatie geslaagd  |  Modelimplementatie geslaagd  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/workspaces  |  Model register mislukt  |  Model register mislukt  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/workspaces  |  Model registratie is voltooid  |  Model registratie is voltooid  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/workspaces  |  Afgebroken kernen  |  Afgebroken kernen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/workspaces  |  Knoop punten die zijn afgebroken  |  Knoop punten die zijn afgebroken  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/workspaces  |  Percentage quotum gebruik  |  Percentage quotum gebruik  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/workspaces  |  Gestart uitvoeringen  |  Gestart uitvoeringen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/workspaces  |  Totaal aantal kernen  |  Totaal aantal kernen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/workspaces  |  Totaal aantal knoop punten  |  Totaal aantal knoop punten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/workspaces  |  Onbruikbaar aantal kern geheugens  |  Onbruikbaar aantal kern geheugens  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.MachineLearningServices/workspaces  |  Niet-bruikbare knoop punten  |  Niet-bruikbare knoop punten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Maps/accounts  |  Beschikbaarheid  |  Beschikbaarheid  |  Procent  |  Average | 
| Nee  | Nee |  Microsoft.Maps/accounts  |  Gebruik  |  Gebruik  |  Count  |  Count | 
| **Ja**  | Nee |  Micro soft. Media/Media Services  |  AssetCount  |  Aantal assets  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. Media/Media Services  |  AssetQuota  |  Activa quotum  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. Media/Media Services  |  AssetQuotaUsedPercentage  |  Percentage gebruikt voor het activa quotum  |  Procent  |  Average | 
| **Ja**  | Nee |  Micro soft. Media/Media Services  |  ContentKeyPolicyCount  |  Aantal beleids regels voor inhouds sleutels  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. Media/Media Services  |  ContentKeyPolicyQuota  |  Quotum voor inhouds sleutel beleid  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. Media/Media Services  |  ContentKeyPolicyQuotaUsedPercentage  |  Percentage gebruikt quotum voor inhouds sleutel beleid  |  Procent  |  Average | 
| **Ja**  | Nee |  Micro soft. Media/Media Services  |  StreamingPolicyCount  |  Aantal stroomsgewijze beleids regels  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. Media/Media Services  |  StreamingPolicyQuota  |  Quota voor streaming-beleid  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. Media/Media Services  |  StreamingPolicyQuotaUsedPercentage  |  Percentage gebruikt quotum voor het streaming-beleid  |  Procent  |  Average | 
| **Ja**  | Nee |  Micro soft. Media/Media Services/streamingEndpoints  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Media/Media Services/streamingEndpoints  |  Aanvragen  |  Aanvragen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Media/Media Services/streamingEndpoints  |  SuccessE2ELatency  |  Geslaagde end-to-end-latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  GCPauseTotalCount  |  Aantal GC-onderbrekingen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  GCPauseTotalTime  |  Totale tijd van de GC-onderbreking  |  Milliseconden  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  MaxOldGenMemoryPoolBytes  |  Maxi maal beschik bare oude generatie gegevens grootte  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  OldGenMemoryPoolBytes  |  Gegevens grootte van oude generatie  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  OldGenPromotedBytes  |  Promo veren tot oude generatie gegevens grootte  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  ServiceCpuUsagePercentage  |  CPU-gebruiks percentage van service  |  Procent  |  Average | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  ServiceMemoryCommitted  |  Toegewezen service geheugen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  ServiceMemoryMax  |  Maxi maal service geheugen  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  ServiceMemoryUsed  |  Gebruikt service geheugen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  SystemCpuUsagePercentage  |  Percentage van het CPU-gebruik van het systeem  |  Procent  |  Average | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  TomcatErrorCount  |  Tomcat Global-fout  |  Count  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  TomcatReceivedBytes  |  Totaal aantal bytes ontvangen Tomcat  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  TomcatRequestMaxTime  |  Maximale tijd voor tomcat-aanvraag  |  Milliseconden  |  Maximum | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  TomcatRequestTotalCount  |  Totaal aantal Tomcat-aanvragen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  TomcatRequestTotalTime  |  Totaal aantal keer Tomcat-aanvragen  |  Milliseconden  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  TomcatResponseAvgTime  |  Gemiddelde tijd Tomcat-aanvraag  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  TomcatSentBytes  |  Totaal aantal verzonden bytes in Tomcat  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  TomcatSessionActiveCurrentCount  |  Aantal Tomcat-sessies  |  Count  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  TomcatSessionActiveMaxCount  |  Aantal actieve Tomcat-sessies  |  Count  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  TomcatSessionAliveMaxTime  |  Time-outperiode van Tomcat-sessie  |  Milliseconden  |  Maximum | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  TomcatSessionCreatedCount  |  Aantal gemaakte Tomcat-sessies  |  Count  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  TomcatSessionExpiredCount  |  Aantal verlopen Tomcat-sessies  |  Count  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  TomcatSessionRejectedCount  |  Aantal geweigerde Tomcat-sessies  |  Count  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  YoungGenPromotedBytes  |  Promo veren tot jonge generatie gegevens grootte  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolAllocatedUsed  |  Gebruikte volume groep  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.NetApp/netAppAccounts/capacityPools  |  VolumePoolTotalLogicalSize  |  Totale logische grootte van volume groep  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. NetApp/netAppAccounts/capacityPools/volumes  |  AverageReadLatency  |  Gemiddelde lees latentie  |  MilliSeconds  |  Average | 
| **Ja**  | Nee |  Micro soft. NetApp/netAppAccounts/capacityPools/volumes  |  AverageWriteLatency  |  Gemiddelde schrijf latentie  |  MilliSeconds  |  Average | 
| **Ja**  | Nee |  Micro soft. NetApp/netAppAccounts/capacityPools/volumes  |  ReadIops  |  IOPS lezen  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. NetApp/netAppAccounts/capacityPools/volumes  |  VolumeLogicalSize  |  Logische volume grootte  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. NetApp/netAppAccounts/capacityPools/volumes  |  VolumeSnapshotSize  |  Grootte van moment opname van volume  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. NetApp/netAppAccounts/capacityPools/volumes  |  WriteIops  |  IOPS schrijven  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Microsoft.Network/applicationGateways  |  ApplicationGatewayTotalTime  |  Totale tijd van Application Gateway  |  MilliSeconds  |  Average | 
| Nee  | Nee |  Microsoft.Network/applicationGateways  |  AvgRequestCountPerHealthyHost  |  Aanvragen per minuut per gegezonde host  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.Network/applicationGateways  |  BackendConnectTime  |  Moment back-end verbinding  |  MilliSeconds  |  Average | 
| Nee  | Nee |  Microsoft.Network/applicationGateways  |  BackendFirstByteResponseTime  |  Reactie tijd eerste byte van back-end  |  MilliSeconds  |  Average | 
| Nee  | Nee |  Microsoft.Network/applicationGateways  |  BackendLastByteResponseTime  |  Reactie tijd laatste byte van back-end  |  MilliSeconds  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/applicationGateways  |  BackendResponseStatus  |  Reactie status van back-end  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/applicationGateways  |  BlockedCount  |  Regel distributie voor door Web Application firewall geblokkeerde aanvragen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/applicationGateways  |  BlockedReqCount  |  Aantal geblokkeerde aanvragen voor Web Application firewall  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/applicationGateways  |  BytesReceived  |  Ontvangen bytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/applicationGateways  |  BytesSent  |  Verzonden bytes  |  Bytes  |  Totaal | 
| Nee  | Nee |  Microsoft.Network/applicationGateways  |  CapacityUnits  |  Huidige capaciteits eenheden  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.Network/applicationGateways  |  ClientRtt  |  Client RTT  |  MilliSeconds  |  Average | 
| Nee  | Nee |  Microsoft.Network/applicationGateways  |  ComputeUnits  |  Huidige reken eenheden  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/applicationGateways  |  CurrentConnections  |  Huidige verbindingen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/applicationGateways  |  FailedRequests  |  Mislukte aanvragen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/applicationGateways  |  HealthyHostCount  |  Aantal goede hosts  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/applicationGateways  |  MatchedCount  |  Totale regel distributie Web Application firewall  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/applicationGateways  |  ResponseStatus  |  Reactie status  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Network/applicationGateways  |  Doorvoer  |  Doorvoer  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/applicationGateways  |  TlsProtocol  |  TLS-protocol van client  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/applicationGateways  |  TotalRequests  |  Totaal aantal aanvragen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/applicationGateways  |  UnhealthyHostCount  |  Aantal hosts met slechte status  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/azurefirewalls  |  ApplicationRuleHit  |  Aantal treffers toepassings regels  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/azurefirewalls  |  DataProcessed  |  Verwerkte gegevens  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/azurefirewalls  |  FirewallHealth  |  Status van Firewall  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/azurefirewalls  |  NetworkRuleHit  |  Aantal treffers in netwerk regels  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/azurefirewalls  |  SNATPortUtilization  |  Gebruik van SNAT-poort  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/connections  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/connections  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/dnszones  |  QueryVolume  |  Query volume  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Network/dnszones  |  RecordSetCapacityUtilization  |  Capaciteits gebruik van record sets  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/dnszones  |  RecordSetCount  |  Aantal record sets  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/expressRouteCircuits  |  ArpAvailability  |  ARP-Beschik baarheid  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/expressRouteCircuits  |  BgpAvailability  |  BGP-Beschik baarheid  |  Procent  |  Average | 
| Nee  | Nee |  Microsoft.Network/expressRouteCircuits  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Microsoft.Network/expressRouteCircuits  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsInPerSecond  |  GlobalReachBitsInPerSecond  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Microsoft.Network/expressRouteCircuits  |  GlobalReachBitsOutPerSecond  |  GlobalReachBitsOutPerSecond  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsInPerSecond  |  DroppedInBitsPerSecond  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Microsoft.Network/expressRouteCircuits  |  QosDropBitsOutPerSecond  |  DroppedOutBitsPerSecond  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/expressRouteCircuits/peerings  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Micro soft. Network/expressRouteGateways  |  ErGatewayConnectionBitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Micro soft. Network/expressRouteGateways  |  ErGatewayConnectionBitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/expressRoutePorts  |  AdminState  |  AdminState  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/expressRoutePorts  |  LineProtocol  |  LineProtocol  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/expressRoutePorts  |  PortBitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/expressRoutePorts  |  PortBitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/expressRoutePorts  |  RxLightLevel  |  RxLightLevel  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/expressRoutePorts  |  TxLightLevel  |  TxLightLevel  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Network/frontdoors  |  BackendHealthPercentage  |  Back-status percentage  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Network/frontdoors  |  BackendRequestCount  |  Aantal back-aanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Network/frontdoors  |  BackendRequestLatency  |  Latentie van back-upaanvraag  |  MilliSeconds  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Network/frontdoors  |  BillableResponseSize  |  Grootte van factureer bare antwoorden  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Network/frontdoors  |  RequestCount  |  Aantal aanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Network/frontdoors  |  RequestSize  |  Aanvraag grootte  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Network/frontdoors  |  ResponseSize  |  Grootte van antwoord  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Network/frontdoors  |  TotalLatency  |  Totale latentie  |  MilliSeconds  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Network/frontdoors  |  WebApplicationFirewallRequestCount  |  Aantal aanvragen voor Web Application firewall  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Network/loadBalancers  |  AllocatedSnatPorts  |  Toegewezen SNAT-poorten (preview-versie)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/loadBalancers  |  ByteCount  |  Aantal bytes  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/loadBalancers  |  DipAvailability  |  Status van Health probe  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/loadBalancers  |  PacketCount  |  Aantal pakketten  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/loadBalancers  |  SnatConnectionCount  |  Aantal SNAT-verbindingen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/loadBalancers  |  SYNCount  |  SYN-aantal  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Network/loadBalancers  |  UsedSnatPorts  |  Gebruikte SNAT-poorten (preview-versie)  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/loadBalancers  |  VipAvailability  |  Beschik baarheid gegevenspad  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/networkInterfaces  |  BytesReceivedRate  |  Ontvangen bytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/networkInterfaces  |  BytesSentRate  |  Verzonden bytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/networkInterfaces  |  PacketsReceivedRate  |  Ontvangen pakketten  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/networkInterfaces  |  PacketsSentRate  |  Verzonden pakketten  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/networkWatchers/connectionMonitors  |  AverageRoundtripMs  |  Gem. retour tijd (MS)  |  MilliSeconds  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/networkWatchers/connectionMonitors  |  ChecksFailedPercent  |  Percentage mislukte controles (preview-versie)  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/networkWatchers/connectionMonitors  |  ProbesFailedPercent  |  % Tests mislukt  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/networkWatchers/connectionMonitors  |  RoundTripTimeMs  |  Retour tijd (MS) (preview-versie)  |  MilliSeconds  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  ByteCount  |  Aantal bytes  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  BytesDroppedDDoS  |  Binnenkomende bytes verloren DDoS  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  BytesForwardedDDoS  |  Doorgestuurde binnenkomende bytes DDoS  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  BytesInDDoS  |  Binnenkomende bytes DDoS  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerSYNPackets  |  Inkomende SYN-pakketten om DDoS-beperking te activeren  |  CountPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerTCPPackets  |  Binnenkomende TCP-pakketten om DDoS-beperking te activeren  |  CountPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerUDPPackets  |  Binnenkomende UDP-pakketten om DDoS-beperking te activeren  |  CountPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  IfUnderDDoSAttack  |  Onder DDoS-aanval of niet  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  PacketCount  |  Aantal pakketten  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  PacketsDroppedDDoS  |  DDoS inkomende pakketten verwijderd  |  CountPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  PacketsForwardedDDoS  |  DDoS inkomende pakketten doorgestuurd  |  CountPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  PacketsInDDoS  |  DDoS inkomende pakketten  |  CountPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  SynCount  |  SYN-aantal  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  TCPBytesDroppedDDoS  |  DDoS binnenkomende TCP-bytes  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  TCPBytesForwardedDDoS  |  DDoS doorgestuurde binnenkomende TCP-bytes  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  TCPBytesInDDoS  |  DDoS binnenkomende TCP-bytes  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  TCPPacketsDroppedDDoS  |  DDoS binnenkomende TCP-pakketten  |  CountPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  TCPPacketsForwardedDDoS  |  Doorgestuurde binnenkomende TCP-pakketten DDoS  |  CountPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  TCPPacketsInDDoS  |  Binnenkomende TCP-pakketten DDoS  |  CountPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  UDPBytesDroppedDDoS  |  Binnenkomend UDP-bytes verloren DDoS  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  UDPBytesForwardedDDoS  |  Doorgestuurde binnenkomende UDP-bytes DDoS  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  UDPBytesInDDoS  |  Binnenkomende UDP-bytes DDoS  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  UDPPacketsDroppedDDoS  |  Verwijderde binnenkomende UDP-pakketten DDoS  |  CountPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  UDPPacketsForwardedDDoS  |  Door inkomende UDP-pakketten DDoS doorgestuurd  |  CountPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  UDPPacketsInDDoS  |  Binnenkomende UDP-pakketten DDoS  |  CountPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  VipAvailability  |  Beschik baarheid gegevenspad  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/trafficManagerProfiles  |  ProbeAgentCurrentEndpointStateByProfileResourceId  |  Eindpunt status op eind punt  |  Count  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.Network/trafficManagerProfiles  |  QpsByEndpoint  |  Query's op eind punt geretourneerd  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  AverageBandwidth  |  Gateway-S2S-band breedte  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  P2SBandwidth  |  Gateway P2S-band breedte  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  P2SConnectionCount  |  Aantal P2S-verbindingen  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  TunnelAverageBandwidth  |  Tunnel bandbreedte  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressBytes  |  Bytes voor uitgaand tunnels  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPacketDropTSMismatch  |  Uitschakeling van niet-overeenkomende TS-pakketten door tunnel  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPackets  |  Tunnel-uituitgangs pakketten  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressBytes  |  Bytes van de tunnel ingang  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPacketDropTSMismatch  |  Verloren gegane pakketten door de tunnel ingang TS komen niet overeen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPackets  |  Tunnel ingangs pakketten  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworks  |  PingMeshAverageRoundtripMs  |  Retour tijd voor pings naar een virtuele machine  |  MilliSeconds  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworks  |  PingMeshProbesFailedPercent  |  Pingen naar een virtuele machine is mislukt  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  e-mail  |  Inkomende berichten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  inkomend. alle. failedrequests  |  Alle binnenkomende mislukte aanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  binnenkomende. alle. aanvragen  |  Alle inkomende aanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  inkomend. gepland  |  Geplande push meldingen verzonden  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  Binnenkomend. gepland. annuleren  |  Geplande push meldingen geannuleerd  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installatie. alle  |  Bewerkingen voor installatie beheer  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.delete  |  Installatie bewerkingen verwijderen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installation.get  |  Installatie bewerkingen ophalen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installatie. patch  |  Patch-installatie bewerkingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  installatie. upsert  |  Installatie bewerkingen maken of bijwerken  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  notificationhub. pushes  |  Alle uitgaande meldingen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.badorexpiredchannel  |  Ongeldige of verlopen kanaal fouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande. allpns. channelerror  |  Kanaal fouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.invalidpayload  |  Payload-fouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande. allpns. pnserror  |  Externe meldingen systeem fouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.allpns.success  |  Geslaagde meldingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.badchannel  |  Fout met ongeldige APNS-kanaal  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.expiredchannel  |  Fout bij verlopen van APNS-kanaal  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidcredentials  |  APNS-autorisatie fouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.invalidnotificationsize  |  Fout door ongeldige grootte van APNS-melding  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.pnserror  |  APNS-fouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.apns.success  |  Geslaagde meldingen van APNS  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande. GCM. authenticationerror  |  GCM-verificatie fouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.badchannel  |  GCM ongeldige kanaal fout  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.expiredchannel  |  GCM-fout met verlopen kanaal  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidcredentials  |  GCM-verificatie fouten (ongeldige referenties)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande. GCM. invalidnotificationformat  |  Ongeldige indeling van GCM-melding  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.invalidnotificationsize  |  Fout met ongeldige grootte van GCM-melding  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande. GCM. pnserror  |  GCM-fouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.success  |  Geslaagde meldingen GCM  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande. GCM. throttled  |  GCM beperkte meldingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.gcm.wrongchannel  |  GCM onjuiste kanaal fout  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande. mpns. authenticationerror  |  MPNS-verificatie fouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.badchannel  |  MPNS ongeldige kanaal fout  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.channeldisconnected  |  Verbinding met MPNS-kanaal verbroken  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande. mpns. dropd  |  MPNS-verwijderde meldingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.mpns.invalidcredentials  |  Ongeldige referenties MPNS  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande. mpns. invalidnotificationformat  |  Ongeldige indeling van MPNS-melding  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande. mpns. pnserror  |  MPNS-fouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaand. mpns. geslaagd  |  Geslaagde meldingen MPNS  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande. mpns. throttled  |  MPNS beperkte meldingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande. wns. authenticationerror  |  WNS-verificatie fouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.badchannel  |  WNS ongeldige kanaal fout  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channeldisconnected  |  Verbinding met WNS-kanaal verbroken  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.channelthrottled  |  WNS-kanaal beperkt  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande. wns. dropd  |  WNS-verwijderde meldingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.expiredchannel  |  WNS-fout met verlopen kanaal  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidcredentials  |  WNS-verificatie fouten (ongeldige referenties)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnotificationformat  |  Ongeldige indeling van WNS-melding  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.invalidnotificationsize  |  Fout met ongeldige grootte van WNS-melding  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande. wns. invalidtoken  |  WNS-verificatie fouten (ongeldig token)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande. wns. pnserror  |  WNS-fouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.success  |  Geslaagde meldingen WNS  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  uitgaande. wns. throttled  |  WNS beperkte meldingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.tokenproviderunreachable  |  WNS-verificatie fouten (onbereikbaar)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  outgoing.wns.wrongtoken  |  WNS-autorisatie fouten (onjuist token)  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registratie. alle  |  Registratie bewerkingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registratie. Create  |  Bewerkingen voor het maken van registratie  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registratie. Delete  |  Verwijderings bewerkingen voor registratie  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registratie. ophalen  |  Lees bewerkingen voor registratie  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  registratie. update  |  Registratie-update bewerkingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.NotificationHubs/Namespaces/NotificationHubs  |  gepland. in behandeling  |  Geplande meldingen in behandeling  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Percentage beschik bare geheugen Average_  |  Percentage beschikbaar geheugen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_ percentage beschik bare wissel ruimte  |  Percentage beschik bare wissel ruimte  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_% toegewezen bytes in gebruik  |  % Toegewezen bytes in gebruik  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_ percentage DPC-tijd  |  Percentage DPC-tijd  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Percentage vrije inodes Average_  |  % Vrije inodes  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  % Beschik bare ruimte Average_  |  % vrije ruimte  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  % Beschik bare ruimte Average_  |  % vrije ruimte  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  % Niet-actieve tijd Average_  |  Percentage niet-actieve tijd  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Percentage interrupt-tijd van Average_  |  Percentage interrupt-tijd  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_% i/o-wacht tijd  |  % I/o-wacht tijd  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Tijd van Average_% leuk  |  Percentage tijd in Nice  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Percentage tijd in beschermde modus Average_  |  Percentage tijd in beschermde modus  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Percentage processor tijd van Average_  |  Percentage processortijd  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Percentage processor tijd van Average_  |  Percentage processortijd  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_% gebruikte inodes  |  % Gebruikte inodes  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_ percentage gebruikt geheugen  |  Percentage gebruikt geheugen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Percentage gebruikte ruimte Average_  |  Percentage gebruikte ruimte  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_ percentage gebruikte wissel ruimte  |  Percentage gebruikte wissel ruimte  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Percentage gebruikers tijd van Average_  |  Percentage gebruikers tijd  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Available MB  |  Beschikbare megabytes (MB)  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Available MB geheugen  |  Beschikbaar geheugen in megabytes  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Available MB wisselen  |  Beschik bare mega bytes wisselen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. Gelezen bytes per seconde  |  Gemiddelde Lees tijd schijf  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. Gelezen bytes per seconde  |  Gemiddelde Lees tijd schijf  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. overdrachttijd  |  Gemiddelde tijd schijf overdracht  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. voor de fysieke schijf  |  Gemiddelde schrijf tijd schijf  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. voor de fysieke schijf  |  Gemiddelde schrijf tijd schijf  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Ontvangen Average_Bytes per seconde  |  Ontvangen bytes per seconde  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Verzonden Average_Bytes per seconde  |  Verzonden bytes per seconde  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes totaal per seconde  |  Totaal aantal bytes per seconde  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Wachtrij lengte van Average_Current schijf  |  Huidige wachtrij lengte voor de schijf  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Lees bewerkingen in bytes per seconde  |  gelezen bytes per seconde  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Reads/sec  |  leesbewerkingen per seconde  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Reads/sec  |  leesbewerkingen per seconde  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Disk overdrachten per seconde  |  Schijfoverdrachten per seconde  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Disk overdrachten per seconde  |  Schijfoverdrachten per seconde  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Disk geschreven bytes per seconde  |  geschreven Bytes per seconde  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Disk schrijf bewerkingen per seconde  |  schrijfbewerkingen per seconde  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Disk schrijf bewerkingen per seconde  |  schrijfbewerkingen per seconde  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Free Megabytes  |  Beschikbare Megabytes  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Free Megabytes  |  Beschikbare Megabytes  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Free fysiek geheugen  |  Vrij fysiek geheugen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Free ruimte in wissel geheugen bestanden  |  Vrije ruimte in wissel geheugen bestanden  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Virtueel geheugen Average_Free  |  Vrij virtueel geheugen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Logical schijf bytes per seconde  |  Logische schijf Bytes per seconde  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Page Lees bewerkingen per seconde  |  paginaleesbewerkingen per seconde  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Page schrijf bewerkingen per seconde  |  paginaschrijfbewerkingen per seconde  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Pages/sec.  |  pagina's per seconde  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Beschermde tijd van Average_Pct  |  Pct-geprivilegieerde tijd  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Gebruikers tijd van Average_Pct  |  Pct-gebruikers tijd  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Physical schijf bytes per seconde  |  Bytes van fysieke schijf per seconde  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Processes  |  Processen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Lengte van Average_Processor wachtrij  |  Lengte van de processor wachtrij  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Size opgeslagen in Wissel bestanden  |  Grootte opgeslagen in Wissel bestanden  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Total bytes  |  Totaal aantal bytes  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Ontvangen Average_Total bytes  |  Totaal aantal ontvangen Bytes  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Verzonden Average_Total bytes  |  Totaal aantal verzonden Bytes  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Total conflicten  |  Totaal aantal conflicten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Ontvangen Average_Total pakketten  |  Totaal aantal ontvangen pakketten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Verzonden Average_Total pakketten  |  Totaal aantal verzonden pakketten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Total RX-fouten  |  Totaal aantal RX-fouten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Total TX-fouten  |  Totaal aantal TX-fouten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Uptime  |  Bedrijfstijd  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Used MB wissel ruimte  |  Gebruikte MB wissel ruimte  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Used geheugen KB  |  Gebruikte geheugen-kBytes  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Used geheugen MB  |  Gebruikt geheugen Mbytes  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Users  |  Gebruikers  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Virtual gedeeld geheugen  |  Virtueel gedeeld geheugen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Gebeurtenis  |  Gebeurtenis  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.OperationalInsights/workspaces  |  Heartbeat  |  Heartbeat  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Bijwerken  |  Bijwerken  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. PowerBIDedicated/capaciteiten  |  memory_metric  |  Geheugen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. PowerBIDedicated/capaciteiten  |  memory_thrashing_metric  |  Geheugen overbelasting (gegevens sets)  |  Procent  |  Average | 
| **Ja**  | Nee |  Micro soft. PowerBIDedicated/capaciteiten  |  qpu_high_utilization_metric  |  Hoog gebruik van QPU  |  Count  |  Totaal | 
| **Ja**  | Nee |  Micro soft. PowerBIDedicated/capaciteiten  |  QueryDuration  |  Query duur (gegevens sets)  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. PowerBIDedicated/capaciteiten  |  QueryPoolJobQueueLength  |  Wachtrij lengte van de taak pool voor query's (gegevens sets)  |  Count  |  Average | 
| Nee  | Nee |  Micro soft. relay/naam ruimten  |  ActiveConnections  |  ActiveConnections  |  Count  |  Totaal | 
| Nee  | Nee |  Micro soft. relay/naam ruimten  |  ActiveListeners  |  ActiveListeners  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. relay/naam ruimten  |  BytesTransferred  |  BytesTransferred  |  Count  |  Totaal | 
| Nee  | Nee |  Micro soft. relay/naam ruimten  |  ListenerConnections-client error  |  ListenerConnections-client error  |  Count  |  Totaal | 
| Nee  | Nee |  Micro soft. relay/naam ruimten  |  ListenerConnections-server error  |  ListenerConnections-server error  |  Count  |  Totaal | 
| Nee  | Nee |  Micro soft. relay/naam ruimten  |  ListenerConnections-Success  |  ListenerConnections-Success  |  Count  |  Totaal | 
| Nee  | Nee |  Micro soft. relay/naam ruimten  |  ListenerConnections-TotalRequests  |  ListenerConnections-TotalRequests  |  Count  |  Totaal | 
| Nee  | Nee |  Micro soft. relay/naam ruimten  |  ListenerDisconnects  |  ListenerDisconnects  |  Count  |  Totaal | 
| Nee  | Nee |  Micro soft. relay/naam ruimten  |  SenderConnections-client error  |  SenderConnections-client error  |  Count  |  Totaal | 
| Nee  | Nee |  Micro soft. relay/naam ruimten  |  SenderConnections-server error  |  SenderConnections-server error  |  Count  |  Totaal | 
| Nee  | Nee |  Micro soft. relay/naam ruimten  |  SenderConnections-Success  |  SenderConnections-Success  |  Count  |  Totaal | 
| Nee  | Nee |  Micro soft. relay/naam ruimten  |  SenderConnections-TotalRequests  |  SenderConnections-TotalRequests  |  Count  |  Totaal | 
| Nee  | Nee |  Micro soft. relay/naam ruimten  |  SenderDisconnects  |  SenderDisconnects  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Search/searchServices  |  SearchLatency  |  Zoek latentie  |  Waarna  |  Average | 
| **Ja**  | Nee |  Microsoft.Search/searchServices  |  SearchQueriesPerSecond  |  Zoek query's per seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Search/searchServices  |  ThrottledSearchQueriesPercentage  |  Percentage vertraagde Zoek query's  |  Procent  |  Average | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  ActiveConnections  |  ActiveConnections  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  ActiveMessages  |  Aantal actieve berichten in een wachtrij/onderwerp.  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  ConnectionsClosed  |  Verbindingen gesloten.  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  ConnectionsOpened  |  Geopende verbindingen.  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  CPUXNS  |  CPU (afgeschaft)  |  Procent  |  Maximum | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  DeadletteredMessages  |  Aantal onbestelbare berichten in een wachtrij/onderwerp.  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.ServiceBus/namespaces  |  IncomingMessages  |  Inkomende berichten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ServiceBus/namespaces  |  IncomingRequests  |  Binnenkomende aanvragen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  Berichten  |  Aantal berichten in een wachtrij/onderwerp.  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  NamespaceCpuUsage  |  CPU  |  Procent  |  Maximum | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  NamespaceMemoryUsage  |  Geheugen gebruik  |  Procent  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.ServiceBus/namespaces  |  OutgoingMessages  |  Uitgaande berichten  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  ScheduledMessages  |  Aantal geplande berichten in een wachtrij/onderwerp.  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  ServerErrors  |  Server fouten.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  Grootte  |  Grootte  |  Bytes  |  Average | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  SuccessfulRequests  |  Geslaagde aanvragen  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  ThrottledRequests  |  Vertraagde aanvragen.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  UserErrors  |  Gebruikers fouten.  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  WSXNS  |  Geheugen gebruik (afgeschaft)  |  Procent  |  Maximum | 
| Nee  | Nee |  Microsoft.ServiceFabricMesh/applications  |  ActualCpu  |  ActualCpu  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ServiceFabricMesh/applications  |  ActualMemory  |  ActualMemory  |  Bytes  |  Average | 
| Nee  | Nee |  Microsoft.ServiceFabricMesh/applications  |  AllocatedCpu  |  AllocatedCpu  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ServiceFabricMesh/applications  |  AllocatedMemory  |  AllocatedMemory  |  Bytes  |  Average | 
| Nee  | Nee |  Microsoft.ServiceFabricMesh/applications  |  ApplicationStatus  |  ApplicationStatus  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ServiceFabricMesh/applications  |  Container status  |  Container status  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ServiceFabricMesh/applications  |  CpuUtilization  |  CpuUtilization  |  Procent  |  Average | 
| Nee  | Nee |  Microsoft.ServiceFabricMesh/applications  |  MemoryUtilization  |  MemoryUtilization  |  Procent  |  Average | 
| Nee  | Nee |  Microsoft.ServiceFabricMesh/applications  |  RestartCount  |  RestartCount  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ServiceFabricMesh/applications  |  ServiceReplicaStatus  |  ServiceReplicaStatus  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.ServiceFabricMesh/applications  |  ServiceStatus  |  ServiceStatus  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.SignalRService/SignalR  |  ConnectionCount  |  Aantal verbindingen  |  Count  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.SignalRService/SignalR  |  InboundTraffic  |  Binnenkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.SignalRService/SignalR  |  MessageCount  |  Aantal berichten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.SignalRService/SignalR  |  OutboundTraffic  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.SignalRService/SignalR  |  SystemErrors  |  Systeem fouten  |  Procent  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.SignalRService/SignalR  |  UserErrors  |  Gebruikers fouten  |  Procent  |  Maximum | 
| **Ja**  | **Ja** |  Micro soft. SQL/managedInstances  |  avg_cpu_percent  |  Gemiddeld CPU-percentage  |  Procent  |  Average | 
| **Ja**  | Nee |  Micro soft. SQL/managedInstances  |  io_bytes_read  |  Gelezen IO-bytes  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. SQL/managedInstances  |  io_bytes_written  |  Geschreven IO-bytes  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. SQL/managedInstances  |  io_requests  |  Aantal i/o-aanvragen  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. SQL/managedInstances  |  reserved_storage_mb  |  Gereserveerde opslag ruimte  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. SQL/managedInstances  |  storage_space_used_mb  |  Gebruikte opslag ruimte  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. SQL/managedInstances  |  virtual_core_count  |  Aantal virtuele kernen  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers  |  database_dtu_consumption_percent  |  DTU-percentage  |  Procent  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers  |  database_storage_used  |  Gebruikte gegevens ruimte  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers  |  dtu_consumption_percent  |  DTU-percentage  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers  |  dtu_used  |  DTU gebruikt  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers  |  storage_used  |  Gebruikte gegevens ruimte  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  allocated_data_storage  |  Toegewezen gegevens ruimte  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  app_cpu_billed  |  App CPU gefactureerd  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  app_cpu_percent  |  CPU-percentage van app  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  app_memory_percent  |  Percentage app-geheugen  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  blocked_by_firewall  |  Geblokkeerd door de firewall  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  cache_hit_percent  |  Percentage cache treffers  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  cache_used_percent  |  Percentage gebruikt cache  |  Procent  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  connection_failed  |  Mislukte verbindingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  connection_successful  |  Geslaagde verbindingen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  cpu_limit  |  CPU-limiet  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  cpu_percent  |  CPU-percentage  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  cpu_used  |  CPU gebruikt  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  constateer  |  Deadlocks  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  dtu_consumption_percent  |  DTU-percentage  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  dtu_limit  |  DTU-limiet  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  dtu_used  |  DTU gebruikt  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  dwu_consumption_percent  |  Percentage DWU  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  dwu_limit  |  Limiet voor DWU  |  Count  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  dwu_used  |  DWU gebruikt  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  local_tempdb_usage_percent  |  Lokaal TempDB-percentage  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  log_write_percent  |  Logboek-IO-percentage  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  memory_usage_percent  |  Geheugen percentage  |  Procent  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  physical_data_read_percent  |  Gegevens-I/O-percentage  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  sessions_percent  |  Percentage sessies  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  opslagpad  |  Gebruikte gegevens ruimte  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  storage_percent  |  Percentage gebruikte gegevens ruimte  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  tempdb_data_size  |  Data File grootte van tempdb  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  tempdb_log_size  |  Grootte van logboek bestanden tempdb  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  tempdb_log_used_percent  |  Percentage gebruikt TempDB-logboek  |  Procent  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  workers_percent  |  Percentage werk nemers  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  xtp_storage_percent  |  Percentage OLTP-opslag in het geheugen  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage  |  Toegewezen gegevens ruimte  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/elasticPools  |  allocated_data_storage_percent  |  Percentage toegewezen gegevens ruimte  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Sql/servers/elasticPools  |  cpu_limit  |  CPU-limiet  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/elasticPools  |  cpu_percent  |  CPU-percentage  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/elasticPools  |  cpu_used  |  CPU gebruikt  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers/elasticPools  |  database_allocated_data_storage  |  Toegewezen gegevens ruimte  |  Bytes  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers/elasticPools  |  database_cpu_limit  |  CPU-limiet  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers/elasticPools  |  database_cpu_percent  |  CPU-percentage  |  Procent  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers/elasticPools  |  database_cpu_used  |  CPU gebruikt  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers/elasticPools  |  database_dtu_consumption_percent  |  DTU-percentage  |  Procent  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers/elasticPools  |  database_eDTU_used  |  eDTU gebruikt  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers/elasticPools  |  database_log_write_percent  |  Logboek-IO-percentage  |  Procent  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers/elasticPools  |  database_physical_data_read_percent  |  Gegevens-I/O-percentage  |  Procent  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers/elasticPools  |  database_sessions_percent  |  Percentage sessies  |  Procent  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers/elasticPools  |  database_storage_used  |  Gebruikte gegevens ruimte  |  Bytes  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers/elasticPools  |  database_workers_percent  |  Percentage werk nemers  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/elasticPools  |  dtu_consumption_percent  |  DTU-percentage  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/elasticPools  |  eDTU_limit  |  eDTU-limiet  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/elasticPools  |  eDTU_used  |  eDTU gebruikt  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/elasticPools  |  log_write_percent  |  Logboek-IO-percentage  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/elasticPools  |  physical_data_read_percent  |  Gegevens-I/O-percentage  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/elasticPools  |  sessions_percent  |  Percentage sessies  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/elasticPools  |  storage_limit  |  Maximale grootte van gegevens  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/elasticPools  |  storage_percent  |  Percentage gebruikte gegevens ruimte  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/elasticPools  |  storage_used  |  Gebruikte gegevens ruimte  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/elasticPools  |  tempdb_data_size  |  Data File grootte van tempdb  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_size  |  Grootte van logboek bestanden tempdb  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Sql/servers/elasticPools  |  tempdb_log_used_percent  |  Percentage gebruikt TempDB-logboek  |  Procent  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/elasticPools  |  workers_percent  |  Percentage werk nemers  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/elasticPools  |  xtp_storage_percent  |  Percentage OLTP-opslag in het geheugen  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts  |  Beschikbaarheid  |  Beschikbaarheid  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts  |  SuccessE2ELatency  |  Geslaagde E2E-latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts  |  SuccessServerLatency  |  Geslaagde serverlatentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts  |  Transacties  |  Transacties  |  Count  |  Totaal | 
| Nee  | Nee |  Microsoft.Storage/storageAccounts  |  UsedCapacity  |  Gebruikte capaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/blobServices  |  Beschikbaarheid  |  Beschikbaarheid  |  Procent  |  Average | 
| Nee  | Nee |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCapacity  |  Blobcapaciteit  |  Bytes  |  Average | 
| Nee  | Nee |  Microsoft.Storage/storageAccounts/blobServices  |  BlobCount  |  Aantal blobs  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/blobServices  |  ContainerCount  |  Aantal blobcontainers  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/blobServices  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| Nee  | Nee |  Microsoft.Storage/storageAccounts/blobServices  |  IndexCapacity  |  Index capaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/blobServices  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessE2ELatency  |  Geslaagde E2E-latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/blobServices  |  SuccessServerLatency  |  Geslaagde serverlatentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/blobServices  |  Transacties  |  Transacties  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/fileServices  |  Beschikbaarheid  |  Beschikbaarheid  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/fileServices  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| Nee  | Nee |  Microsoft.Storage/storageAccounts/fileServices  |  FileCapacity  |  Bestands capaciteit  |  Bytes  |  Average | 
| Nee  | Nee |  Microsoft.Storage/storageAccounts/fileServices  |  FileCount  |  Aantal bestanden  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareCount  |  Aantal bestands shares  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareQuota  |  Quota grootte van bestands share  |  Bytes  |  Average | 
| Nee  | Nee |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareSnapshotCount  |  Aantal moment opnamen van bestands shares  |  Count  |  Average | 
| Nee  | Nee |  Microsoft.Storage/storageAccounts/fileServices  |  FileShareSnapshotSize  |  Grootte van moment opname van bestands share  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/fileServices  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessE2ELatency  |  Geslaagde E2E-latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/fileServices  |  SuccessServerLatency  |  Geslaagde serverlatentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/fileServices  |  Transacties  |  Transacties  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/queueServices  |  Beschikbaarheid  |  Beschikbaarheid  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/queueServices  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/queueServices  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/queueServices  |  QueueCapacity  |  Wachtrijcapaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/queueServices  |  QueueCount  |  Aantal wachtrijen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/queueServices  |  QueueMessageCount  |  Aantal wachtrijberichten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessE2ELatency  |  Geslaagde E2E-latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/queueServices  |  SuccessServerLatency  |  Geslaagde serverlatentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/queueServices  |  Transacties  |  Transacties  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/tableServices  |  Beschikbaarheid  |  Beschikbaarheid  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/tableServices  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/tableServices  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessE2ELatency  |  Geslaagde E2E-latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/tableServices  |  SuccessServerLatency  |  Geslaagde serverlatentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/tableServices  |  TableCapacity  |  Tabelcapaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/tableServices  |  TableCount  |  Aantal tabellen  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/tableServices  |  TableEntityCount  |  Aantal tabelentiteiten  |  Count  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts/tableServices  |  Transacties  |  Transacties  |  Count  |  Totaal | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  ClientIOPS  |  Totaal aantal IOPS client  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  ClientLatency  |  Gemiddelde client latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  ClientLockIOPS  |  IOPS-client vergrendeling  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  ClientMetadataReadIOPS  |  IOPS voor lezen van meta gegevens van client  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  ClientMetadataWriteIOPS  |  IOPS voor schrijven van meta gegevens van client  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  ClientReadIOPS  |  Door client gelezen IOPS  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  ClientReadThroughput  |  Gemiddelde doorvoer snelheid van cache Lees bewerking  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  ClientWriteIOPS  |  Client schrijf-IOPS  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  ClientWriteThroughput  |  Gemiddelde doorvoer snelheid van cache schrijf bewerkingen  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  StorageTargetAsyncWriteThroughput  |  Asynchrone schrijf doorvoer StorageTarget  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  StorageTargetFillThroughput  |  Door Voer voor StorageTarget-vulling  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  StorageTargetHealth  |  Status van opslag doel  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  StorageTargetIOPS  |  Totale aantal StorageTarget IOPS  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  StorageTargetLatency  |  StorageTarget-latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  StorageTargetMetadataReadIOPS  |  StorageTarget voor lezen van meta gegevens  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  StorageTargetMetadataWriteIOPS  |  StorageTarget-schrijf-IOPS voor meta gegevens  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  StorageTargetReadAheadThroughput  |  StorageTarget door Voer lezen  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  StorageTargetReadIOPS  |  StorageTarget lezen IOPS  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  StorageTargetSyncWriteThroughput  |  StorageTarget synchrone schrijf doorvoer  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  StorageTargetTotalReadThroughput  |  Totale Lees doorvoer StorageTarget  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  StorageTargetTotalWriteThroughput  |  Totale schrijf doorvoer StorageTarget  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  StorageTargetWriteIOPS  |  StorageTarget write IOPS  |  Count  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  Bedrijfstijd  |  Bedrijfstijd  |  Count  |  Average | 
| **Ja**  | Nee |  microsoft.storagesync/storageSyncServices  |  ServerSyncSessionResult  |  Resultaat van synchronisatie sessie  |  Count  |  Average | 
| **Ja**  | Nee |  microsoft.storagesync/storageSyncServices  |  StorageSyncBatchTransferredFileBytes  |  Gesynchroniseerde bytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecalledNetworkBytesByApplication  |  Grootte van intrekken van Cloud lagen op toepassing  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecalledTotalNetworkBytes  |  Grootte van intrekken Cloud lagen  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecallIOTotalSizeBytes  |  Cloud lagen intrekken  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  microsoft.storagesync/storageSyncServices  |  StorageSyncRecallThroughputBytesPerSecond  |  Door Voer van Cloud lagen intrekken  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  microsoft.storagesync/storageSyncServices  |  StorageSyncServerHeartbeat  |  Online status van de server  |  Count  |  Maximum | 
| **Ja**  | Nee |  microsoft.storagesync/storageSyncServices  |  StorageSyncSyncSessionAppliedFilesCount  |  Gesynchroniseerde bestanden  |  Count  |  Totaal | 
| **Ja**  | Nee |  microsoft.storagesync/storageSyncServices  |  StorageSyncSyncSessionPerItemErrorsCount  |  Bestanden die niet worden gesynchroniseerd  |  Count  |  Totaal | 
| **Ja**  | Nee |  micro soft. storagesync/storageSyncServices/registeredServer  |  ServerHeartbeat  |  Online status van de server  |  Count  |  Maximum | 
| **Ja**  | Nee |  micro soft. storagesync/storageSyncServices/registeredServer  |  ServerRecallIOTotalSizeBytes  |  Cloud lagen intrekken  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  micro soft. storagesync/storageSyncServices/syncGroups  |  SyncGroupBatchTransferredFileBytes  |  Gesynchroniseerde bytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  micro soft. storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionAppliedFilesCount  |  Gesynchroniseerde bestanden  |  Count  |  Totaal | 
| **Ja**  | Nee |  micro soft. storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionPerItemErrorsCount  |  Bestanden die niet worden gesynchroniseerd  |  Count  |  Totaal | 
| **Ja**  | Nee |  micro soft. storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointBatchTransferredFileBytes  |  Gesynchroniseerde bytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  micro soft. storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionAppliedFilesCount  |  Gesynchroniseerde bestanden  |  Count  |  Totaal | 
| **Ja**  | Nee |  micro soft. storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionPerItemErrorsCount  |  Bestanden die niet worden gesynchroniseerd  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutFailedRequests  |  Mislukte functieaanvragen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutInputEvents  |  Functiegebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  AMLCalloutRequests  |  Functieaanvragen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  ConversionErrors  |  Gegevensconversiefouten  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  DeserializationError  |  Fouten in invoerdeserialisatie  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  DroppedOrAdjustedEvents  |  Gebeurtenissen met een andere volg orde  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  EarlyInputEvents  |  Vroege-invoergebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  Fouten  |  Runtimefouten  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventBytes  |  Invoergebeurtenisbytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  InputEvents  |  Invoergebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventsSourcesBacklogged  |  Invoervelden met achterstand  |  Count  |  Maximum | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  InputEventsSourcesPerSecond  |  Ontvangen invoerbronnen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  LateInputEvents  |  Late invoergebeurtenissen  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  OutputEvents  |  Uitvoergebeurtenis  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  OutputWatermarkDelaySeconds  |  Watermerkvertraging  |  Waarna  |  Maximum | 
| **Ja**  | Nee |  Microsoft.StreamAnalytics/streamingjobs  |  ResourceUtilization  |  Gebruikspercentage voor Streaming-eenheden  |  Procent  |  Maximum | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  Gelezen bytes op de schijf  |  Gelezen bytes op de schijf  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  Lees bewerkingen op de schijf per seconde  |  Lees bewerkingen op de schijf per seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  Geschreven bytes op de schijf  |  Geschreven bytes op de schijf  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  Schrijf bewerkingen op de schijf per seconde  |  Schrijf bewerkingen op de schijf per seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadBytesPerSecond  |  Gelezen bytes per seconde  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadLatency  |  Lees latentie van schijf  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskReadOperations  |  Lees bewerkingen op de schijf  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteBytesPerSecond  |  Geschreven bytes per seconde  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteLatency  |  Schrijf latentie schijf  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  DiskWriteOperations  |  Schrijf bewerkingen op de schijf  |  Count  |  Totaal | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemoryActive  |  Actief geheugen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemoryGranted  |  Toegewezen geheugen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  MemoryUsed  |  Gebruikt geheugen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  Netwerk in  |  Netwerk in  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  Netwerk uit  |  Netwerk uit  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  NetworkInBytesPerSecond  |  Netwerk in bytes per seconde  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  NetworkOutBytesPerSecond  |  Netwerk uitgaande bytes per seconde  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  Percentage CPU  |  Percentage CPU  |  Procent  |  Average | 
| **Ja**  | Nee |  Microsoft.VMwareCloudSimple/virtualMachines  |  PercentageCpuReady  |  Percentage CPU gereed  |  Milliseconden  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  ActiveRequests  |  Actieve aanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  AverageResponseTime  |  Gemiddelde reactietijd  |  Waarna  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  BytesReceived  |  Gegevens in  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  BytesSent  |  Gegevens uit  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  CpuPercentage  |  CPU-percentage  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  DiskQueueLength  |  Wachtrij lengte voor schijf  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http101  |  Http 101  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http2xx  |  Http-2xx  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http3xx  |  HTTP-3xx  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http401  |  HTTP 401  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http403  |  HTTP 403  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http404  |  Http 404  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http406  |  Http 406  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http4xx  |  Http 4xx  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Http5xx  |  Http-server fouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  HttpQueueLength  |  Lengte van http-wachtrij  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  LargeAppServicePlanInstances  |  Werk rollen voor grote App Service plannen  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  MediumAppServicePlanInstances  |  Werk nemers met gemiddeld App Service plannen  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  MemoryPercentage  |  Geheugen percentage  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  Aanvragen  |  Aanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  SmallAppServicePlanInstances  |  Werk rollen voor kleine App Service plannen  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/multiRolePools  |  TotalFrontEnds  |  Totale front-ends  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/workerPools  |  CpuPercentage  |  CPU-percentage  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/workerPools  |  MemoryPercentage  |  Geheugen percentage  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersAvailable  |  Beschik bare werk nemers  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersTotal  |  Totaal aantal werk rollen  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/hostingEnvironments/workerPools  |  WorkersUsed  |  Gebruikte werk rollen  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  BytesReceived  |  Gegevens in  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  BytesSent  |  Gegevens uit  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  CpuPercentage  |  CPU-percentage  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  DiskQueueLength  |  Wachtrij lengte voor schijf  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  HttpQueueLength  |  Lengte van http-wachtrij  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  MemoryPercentage  |  Geheugen percentage  |  Procent  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpCloseWait  |  TCP-wacht tijd voor sluiten  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpClosing  |  TCP sluiten  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpEstablished  |  TCP-verbinding  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpFinWait1  |  TCP-FIN-wacht 1  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpFinWait2  |  TCP FIN WAIT 2  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpLastAck  |  TCP laatste ACK  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpSynReceived  |  TCP SYN ontvangen  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpSynSent  |  TCP SYN verzonden  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/serverfarms  |  TcpTimeWait  |  Wacht tijd voor TCP-bewerking  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  AppConnections  |  Verbindingen  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  AverageMemoryWorkingSet  |  Gemiddelde werkset geheugen  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  AverageResponseTime  |  Gemiddelde reactietijd  |  Waarna  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  BytesReceived  |  Gegevens in  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  BytesSent  |  Gegevens uit  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  CpuTime  |  CPU-tijd  |  Waarna  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  CurrentAssemblies  |  Huidige Assembly's  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  FunctionExecutionCount  |  Aantal functie-uitvoeringen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  FunctionExecutionUnits  |  Eenheden voor functie-uitvoering  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Gen0Collections  |  Schone verzamelingen van 0 gen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Gen1Collections  |  1 garbagecollection-verzamelingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Gen2Collections  |  Opschoon verzamelingen van generatie 2  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Formuleer  |  Aantal ingangen  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  HealthCheckStatus  |  Status van de status controle  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Http101  |  Http 101  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Http2xx  |  Http-2xx  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Http3xx  |  HTTP-3xx  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Http401  |  HTTP 401  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Http403  |  HTTP 403  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Http404  |  Http 404  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Http406  |  Http 406  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Http4xx  |  Http 4xx  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Http5xx  |  Http-server fouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  HttpResponseTime  |  Reactie tijd  |  Waarna  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  IoOtherBytesPerSecond  |  Andere i/o-bytes per seconde  |  BytesPerSecond  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  IoOtherOperationsPerSecond  |  Andere i/o-bewerkingen per seconde  |  BytesPerSecond  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  IoReadBytesPerSecond  |  I/o gelezen bytes per seconde  |  BytesPerSecond  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  IoReadOperationsPerSecond  |  I/o-Lees bewerkingen per seconde  |  BytesPerSecond  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  IoWriteBytesPerSecond  |  I/o-schrijf bewerkingen in bytes per seconde  |  BytesPerSecond  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  IoWriteOperationsPerSecond  |  I/o-schrijf bewerkingen per seconde  |  BytesPerSecond  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  MemoryWorkingSet  |  Werkset geheugen  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  PrivateBytes  |  Eigen Bytes  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Aanvragen  |  Aanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  RequestsInApplicationQueue  |  Aanvragen in de wachtrij van de toepassing  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  Lijnen  |  Aantal threads  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  TotalAppDomains  |  Totaal aantal app-domeinen  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites  |  TotalAppDomainsUnloaded  |  Totaal aantal verwijderde app-domeinen  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  AppConnections  |  Verbindingen  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  AverageMemoryWorkingSet  |  Gemiddelde werkset geheugen  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  AverageResponseTime  |  Gemiddelde reactietijd  |  Waarna  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  BytesReceived  |  Gegevens in  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  BytesSent  |  Gegevens uit  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  CpuTime  |  CPU-tijd  |  Waarna  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  CurrentAssemblies  |  Huidige Assembly's  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  FunctionExecutionCount  |  Aantal functie-uitvoeringen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  FunctionExecutionUnits  |  Eenheden voor functie-uitvoering  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Gen0Collections  |  Schone verzamelingen van 0 gen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Gen1Collections  |  1 garbagecollection-verzamelingen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Gen2Collections  |  Opschoon verzamelingen van generatie 2  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Formuleer  |  Aantal ingangen  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  HealthCheckStatus  |  Status van de status controle  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Http101  |  Http 101  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Http2xx  |  Http-2xx  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Http3xx  |  HTTP-3xx  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Http401  |  HTTP 401  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Http403  |  HTTP 403  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Http404  |  Http 404  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Http406  |  Http 406  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Http4xx  |  Http 4xx  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Http5xx  |  Http-server fouten  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  HttpResponseTime  |  Reactie tijd  |  Waarna  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  IoOtherBytesPerSecond  |  Andere i/o-bytes per seconde  |  BytesPerSecond  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  IoOtherOperationsPerSecond  |  Andere i/o-bewerkingen per seconde  |  BytesPerSecond  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  IoReadBytesPerSecond  |  I/o gelezen bytes per seconde  |  BytesPerSecond  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  IoReadOperationsPerSecond  |  I/o-Lees bewerkingen per seconde  |  BytesPerSecond  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  IoWriteBytesPerSecond  |  I/o-schrijf bewerkingen in bytes per seconde  |  BytesPerSecond  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  IoWriteOperationsPerSecond  |  I/o-schrijf bewerkingen per seconde  |  BytesPerSecond  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  MemoryWorkingSet  |  Werkset geheugen  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  PrivateBytes  |  Eigen Bytes  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Aanvragen  |  Aanvragen  |  Count  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  RequestsInApplicationQueue  |  Aanvragen in de wachtrij van de toepassing  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  Lijnen  |  Aantal threads  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  TotalAppDomains  |  Totaal aantal app-domeinen  |  Count  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Web/sites/slots  |  TotalAppDomainsUnloaded  |  Totaal aantal verwijderde app-domeinen  |  Count  |  Average | 
