---
title: De metrische gegevens van het Azure Monitor-platform exporteerbaar via Diagnostische instellingen
description: Een lijst met metrische gegevens die beschikbaar zijn voor elk resource type met Azure Monitor.
services: azure-monitor
ms.topic: reference
ms.date: 03/30/2020
ms.subservice: metrics
ms.openlocfilehash: f0a8fd186862cf95ebdbb2d5bd92d8ff860b3ba1
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/20/2020
ms.locfileid: "86515475"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>De metrische gegevens van het Azure Monitor-platform exporteerbaar via Diagnostische instellingen

Azure Monitor biedt standaard [platform metrieken](data-platform-metrics.md) zonder configuratie. Het biedt verschillende manieren om te communiceren met platform metrieken, zoals het maken van grafieken in de portal, het openen ervan via de REST API of het opvragen van query's via Power shell of CLI. Bekijk [metrische gegevens: ondersteund](metrics-supported.md) voor een volledige lijst met platform metrieken die momenteel beschikbaar zijn met de geconsolideerde metrische pijp lijn van Azure monitor. Als u deze metrische gegevens wilt opvragen en openen, gebruikt u de [2018-01-01-API-versie](/rest/api/monitor/metricdefinitions). Andere metrische gegevens zijn mogelijk beschikbaar in de portal of met behulp van verouderde Api's.

U kunt de platform metrieken van de Azure monitor-pijp lijn naar andere locaties op een van de twee manieren exporteren.
1. [Diagnostische instellingen](diagnostic-settings.md) gebruiken om te verzenden naar Log Analytics, Event Hubs of Azure Storage.
2. De [metrische gegevens rest API](/rest/api/monitor/metrics/list) gebruiken

Vanwege complexiteit in de Azure Monitor back-end kunnen niet alle metrische gegevens exporteerbaar zijn met behulp van diagnostische instellingen. De volgende tabel bevat een lijst die kan en kan niet worden geëxporteerd met Diagnostische instellingen.

## <a name="change-to-behavior-for-nulls-and-zero-values"></a>Wijzigen in gedrag voor NULL-waarden en nulwaarden 
 
Voor de platform metrieken die kunnen worden geëxporteerd via Diagnostische instellingen, zijn er enkele metrische gegevens waarvoor Azure Monitor ' 0s ' als ' Nulls ' interpreteert. Dit heeft een zekere Verwar ring veroorzaakt tussen Real ' 0s ' (verzonden door resource) en ' 0s ' (null-waarden) geïnterpreteerd. Binnenkort wordt een wijziging doorgevoerd en de platform metrieken die worden geëxporteerd via Diagnostische instellingen, zullen niet langer 0s worden geëxporteerd, tenzij ze daad werkelijk door de onderliggende resource zijn verzonden. 

> [!CAUTION]
> Het gewijzigde gedrag dat hierboven wordt beschreven, is gepland om te worden uitgevoerd op 1 juni 2020.

Opmerking:

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
- Metrisch gegeven 
- MetricDisplayName
- Eenheid 
- AggregationType


> [!NOTE]
> De onderstaande tabel bevat mogelijk een horizontale schuif balk aan de onderkant. Als u van mening bent dat u de informatie ontbreekt, controleert u of de schuif balk helemaal naar links wordt weer gegeven.  


| Exporteerbaar via Diagnostische instellingen?  | NULL-waarden al verzenden |  ResourceType  |  Metrisch gegeven  |  MetricDisplayName  |  Eenheid  |  AggregationType | 
|---|---| ---- | ----- | ------ | ---- | ---- | 
| Ja * * * *  | Nee |  Micro soft. AnalysisServices/servers  |  CleanerCurrentPrice  |  Geheugen: huidige prijs opschonen  |  Aantal  |  Average | 
| Ja * * * *  | Nee |  Micro soft. AnalysisServices/servers  |  CleanerMemoryNonshrinkable  |  Geheugen: Removal-geheugen kan niet worden verkleind  |  Bytes  |  Average | 
| Ja * * * *  | Nee |  Micro soft. AnalysisServices/servers  |  CleanerMemoryShrinkable  |  Geheugen: verkleinbaar geheugen  |  Bytes  |  Average | 
| Ja * * * *  | Nee |  Micro soft. AnalysisServices/servers  |  CommandPoolBusyThreads  |  Threads: actieve threads van opdracht pool  |  Aantal  |  Average | 
| Ja * * * *  | Nee |  Micro soft. AnalysisServices/servers  |  CommandPoolIdleThreads  |  Threads: niet-actieve threads van opdracht pool  |  Aantal  |  Average | 
| Ja * * * *  | Nee |  Micro soft. AnalysisServices/servers  |  CommandPoolJobQueueLength  |  Wachtrij lengte van de opdracht pool taak  |  Aantal  |  Average | 
| Ja * * * *  | Nee |  Micro soft. AnalysisServices/servers  |  CurrentConnections  |  Verbinding: huidige verbindingen  |  Aantal  |  Average | 
| Ja * * * *  | Nee |  Micro soft. AnalysisServices/servers  |  CurrentUserSessions  |  Huidige gebruikers sessies  |  Aantal  |  Average | 
| Ja * * * *  | Nee |  Micro soft. AnalysisServices/servers  |  LongParsingBusyThreads  |  Threads: bezette threads voor lang parseren  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  LongParsingIdleThreads  |  Threads: niet-actieve threads voor lang parseren  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  LongParsingJobQueueLength  |  Threads: lengte van taak wachtrij voor lang parseren  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  mashup_engine_memory_metric  |  M-engine geheugen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  mashup_engine_private_bytes_metric  |  M-engine-eigen bytes  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  mashup_engine_qpu_metric  |  M-engine QPU  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  mashup_engine_virtual_bytes_metric  |  M-engine virtuele bytes  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  memory_metric  |  Geheugen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  memory_thrashing_metric  |  Geheugenthrashing  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  MemoryLimitHard  |  Geheugen: vaste geheugen limiet  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  MemoryLimitHigh  |  Geheugen: hoge geheugen limiet  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  MemoryLimitLow  |  Geheugen: lage geheugen limiet  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  MemoryLimitVertiPaq  |  Geheugen: VertiPaq-geheugen limiet  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  MemoryUsage  |  Geheugen: geheugen gebruik  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  private_bytes_metric  |  Privé-bytes  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  ProcessingPoolBusyIOJobThreads  |  Threads: bezig met verwerken van I/O-taak threads van pool  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  ProcessingPoolBusyNonIOThreads  |  Threads: bezig met het verwerken van niet-I/O-threads van de groep  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  ProcessingPoolIdleIOJobThreads  |  Threads: niet-actieve I/O-taak threads van de groep verwerken  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  ProcessingPoolIdleNonIOThreads  |  Threads: niet-I/O-threads van de groep worden verwerkt  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  ProcessingPoolIOJobQueueLength  |  Threads: lengte van I/O-taak wachtrij voor verwerking van groep  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  ProcessingPoolJobQueueLength  |  Wachtrij lengte van de pool taak wordt verwerkt  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  qpu_metric  |  QPU  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  QueryPoolBusyThreads  |  Query's pool bezette threads  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  QueryPoolIdleThreads  |  Threads: niet-actieve threads van query pool  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  QueryPoolJobQueueLength  |  Threads: lengte van taak wachtrij voor query pool  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  Quota  |  Geheugen: quotum  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  QuotaBlocked  |  Geheugen: quotum geblokkeerd  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  RowsConvertedPerSec  |  Verwerken: geconverteerde rijen per seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  RowsReadPerSec  |  Verwerken: gelezen rijen per seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  RowsWrittenPerSec  |  Verwerken: geschreven rijen per seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  ShortParsingBusyThreads  |  Threads: bezette threads voor kort parseren  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  ShortParsingIdleThreads  |  Threads: niet-actieve threads voor kort parseren  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  ShortParsingJobQueueLength  |  Threads: lengte van taak wachtrij voor kort parseren  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  SuccessfullConnectionsPerSec  |  Geslaagde verbindingen per seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  TotalConnectionFailures  |  Totaal aantal verbindings fouten  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  TotalConnectionRequests  |  Totaal aantal verbindings aanvragen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  VertiPaqNonpaged  |  Geheugen: VertiPaq niet-wisselbaar  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  VertiPaqPaged  |  Geheugen: VertiPaq-pagina  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. AnalysisServices/servers  |  virtual_bytes_metric  |  Virtuele bytes  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  BackendDuration  |  Duur van back-end-aanvragen  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.ApiManagement/service  |  Capaciteit  |  Capaciteit  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  Duur  |  Totale duur van gateway aanvragen  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubDroppedEvents  |  Verwijderde EventHub-gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubRejectedEvents  |  Geweigerde EventHub-gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubSuccessfulEvents  |  Geslaagde EventHub-gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubThrottledEvents  |  Vertraagde EventHub-gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubTimedoutEvents  |  Time-out EventHub-gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubTotalBytesSent  |  Grootte van EventHub-gebeurtenissen  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubTotalEvents  |  Totaal aantal EventHub-gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  EventHubTotalFailedEvents  |  Mislukte EventHub-gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  FailedRequests  |  Mislukte gateway aanvragen (afgeschaft)  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  OtherRequests  |  Andere gateway aanvragen (afgeschaft)  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  Aanvragen  |  Aanvragen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  SuccessfulRequests  |  Geslaagde gateway aanvragen (afgeschaft)  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  TotalRequests  |  Totaal aantal gateway aanvragen (afgeschaft)  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ApiManagement/service  |  UnauthorizedRequests  |  Niet-geautoriseerde gateway aanvragen (afgeschaft)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  AppCpuUsagePercentage  |  CPU-gebruiks percentage van app  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  AppMemoryCommitted  |  Toegewezen app-geheugen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  AppMemoryMax  |  Maxi maal app-geheugen  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  AppMemoryUsed  |  Gebruikt app-geheugen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  GCPauseTotalCount  |  Aantal GC-onderbrekingen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  GCPauseTotalTime  |  Totale tijd van de GC-onderbreking  |  Milliseconden  |  Totaal | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  MaxOldGenMemoryPoolBytes  |  Maxi maal beschik bare oude generatie gegevens grootte  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  OldGenMemoryPoolBytes  |  Gegevens grootte van oude generatie  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  OldGenPromotedBytes  |  Promo veren tot oude generatie gegevens grootte  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  SystemCpuUsagePercentage  |  Percentage van het CPU-gebruik van het systeem  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  TomcatErrorCount  |  Tomcat Global-fout  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  TomcatReceivedBytes  |  Totaal aantal bytes ontvangen Tomcat  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  TomcatRequestMaxTime  |  Maximale tijd voor tomcat-aanvraag  |  Milliseconden  |  Maximum | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  TomcatRequestTotalCount  |  Totaal aantal Tomcat-aanvragen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  TomcatRequestTotalTime  |  Totaal aantal keer Tomcat-aanvragen  |  Milliseconden  |  Totaal | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  TomcatResponseAvgTime  |  Gemiddelde tijd Tomcat-aanvraag  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  TomcatSentBytes  |  Totaal aantal verzonden bytes in Tomcat  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  TomcatSessionActiveCurrentCount  |  Aantal Tomcat-sessies  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  TomcatSessionActiveMaxCount  |  Aantal actieve Tomcat-sessies  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  TomcatSessionAliveMaxTime  |  Time-outperiode van Tomcat-sessie  |  Milliseconden  |  Maximum | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  TomcatSessionCreatedCount  |  Aantal gemaakte Tomcat-sessies  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  TomcatSessionExpiredCount  |  Aantal verlopen Tomcat-sessies  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  TomcatSessionRejectedCount  |  Aantal geweigerde Tomcat-sessies  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. AppPlatform/lente  |  YoungGenPromotedBytes  |  Promo veren tot jonge generatie gegevens grootte  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. Automation/automationAccounts  |  TotalJob  |  Totaal aantal taken  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Automation/automationAccounts  |  TotalUpdateDeploymentMachineRuns  |  Totaal aantal machine-uitvoeringen van update-implementaties  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Automation/automationAccounts  |  TotalUpdateDeploymentRuns  |  Totaal aantal uitvoeringen van update-implementaties  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.Bat-CH/batchAccounts  |  CoreCount  |  Aantal toegewezen kernen  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.Bat-CH/batchAccounts  |  CreatingNodeCount  |  Aantal knoop punten maken  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.Bat-CH/batchAccounts  |  IdleNodeCount  |  Aantal niet-actieve knoop punten  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Bat-CH/batchAccounts  |  JobDeleteCompleteEvent  |  Voltooide gebeurtenissen van taak verwijderen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Bat-CH/batchAccounts  |  JobDeleteStartEvent  |  Taak begin gebeurtenissen verwijderen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Bat-CH/batchAccounts  |  JobDisableCompleteEvent  |  Voltooide gebeurtenissen voor taak uitschakelen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Bat-CH/batchAccounts  |  JobDisableStartEvent  |  Taak start gebeurtenissen uitschakelen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Bat-CH/batchAccounts  |  JobStartEvent  |  Taak begin gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Bat-CH/batchAccounts  |  JobTerminateCompleteEvent  |  Voltooide gebeurtenissen voor taak beëindigen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Bat-CH/batchAccounts  |  JobTerminateStartEvent  |  Taak start gebeurtenissen beëindigen  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.Bat-CH/batchAccounts  |  LeavingPoolNodeCount  |  Aantal groeps knooppunten verlaten  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.Bat-CH/batchAccounts  |  LowPriorityCoreCount  |  Aantal LowPriority kernen  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.Bat-CH/batchAccounts  |  OfflineNodeCount  |  Aantal offline knooppunten  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Bat-CH/batchAccounts  |  PoolCreateEvent  |  Groeps gebeurtenissen maken  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Bat-CH/batchAccounts  |  PoolDeleteCompleteEvent  |  Voltooide gebeurtenissen van groep verwijderen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Bat-CH/batchAccounts  |  PoolDeleteStartEvent  |  Begin gebeurtenissen groep verwijderen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Bat-CH/batchAccounts  |  PoolResizeCompleteEvent  |  Volledige gebeurtenissen voor het wijzigen van de pool  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Bat-CH/batchAccounts  |  PoolResizeStartEvent  |  Begin gebeurtenissen van groeps grootte wijzigen  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.Bat-CH/batchAccounts  |  PreemptedNodeCount  |  Aantal knoop punten in herhaling  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.Bat-CH/batchAccounts  |  RebootingNodeCount  |  Aantal knoop punten opnieuw opstarten  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.Bat-CH/batchAccounts  |  ReimagingNodeCount  |  Telling van het aantal knoop punten  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.Bat-CH/batchAccounts  |  RunningNodeCount  |  Aantal actieve knoop punten  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.Bat-CH/batchAccounts  |  StartingNodeCount  |  Begin aantal knoop punten  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.Bat-CH/batchAccounts  |  StartTaskFailedNodeCount  |  Aantal mislukte knoop punten van begin taak  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Bat-CH/batchAccounts  |  TaskCompleteEvent  |  Taak voltooid gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Bat-CH/batchAccounts  |  TaskFailEvent  |  Taak fout gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Bat-CH/batchAccounts  |  TaskStartEvent  |  Taak begin gebeurtenissen  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.Bat-CH/batchAccounts  |  TotalLowPriorityNodeCount  |  Aantal knoop punten met een lage prioriteit  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.Bat-CH/batchAccounts  |  TotalNodeCount  |  Aantal toegewezen knoop punten  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.Bat-CH/batchAccounts  |  UnusableNodeCount  |  Aantal niet-bruikbare knoop punten  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.Bat-CH/batchAccounts  |  WaitingForStartTaskNodeCount  |  Wachten op aantal begin taak knooppunten  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.BatchAI/werk ruimten  |  Actieve kernen  |  Actieve kernen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.BatchAI/werk ruimten  |  Actieve knoop punten  |  Actieve knoop punten  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.BatchAI/werk ruimten  |  Niet-actieve kernen  |  Niet-actieve kernen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.BatchAI/werk ruimten  |  Niet-actieve knoop punten  |  Niet-actieve knoop punten  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.BatchAI/werk ruimten  |  Taak is voltooid  |  Taak is voltooid  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.BatchAI/werk ruimten  |  Taak verzonden  |  Taak verzonden  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.BatchAI/werk ruimten  |  Kernen verlaten  |  Kernen verlaten  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.BatchAI/werk ruimten  |  Knoop punten verlaten  |  Knoop punten verlaten  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.BatchAI/werk ruimten  |  Afgebroken kernen  |  Afgebroken kernen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.BatchAI/werk ruimten  |  Knoop punten die zijn afgebroken  |  Knoop punten die zijn afgebroken  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.BatchAI/werk ruimten  |  Percentage quotum gebruik  |  Percentage quotum gebruik  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.BatchAI/werk ruimten  |  Totaal aantal kernen  |  Totaal aantal kernen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.BatchAI/werk ruimten  |  Totaal aantal knoop punten  |  Totaal aantal knoop punten  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.BatchAI/werk ruimten  |  Onbruikbaar aantal kern geheugens  |  Onbruikbaar aantal kern geheugens  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.BatchAI/werk ruimten  |  Niet-bruikbare knoop punten  |  Niet-bruikbare knoop punten  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  ConnectionAccepted  |  Geaccepteerde verbindingen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  ConnectionActive  |  Actieve verbindingen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  ConnectionHandled  |  Afgehandelde verbindingen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  CpuUsagePercentageInDouble  |  Percentage CPU-gebruik  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  IOReadBytes  |  I/o gelezen bytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  IOWriteBytes  |  I/o-schrijf bytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  Memory limit  |  Geheugen limiet  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  MemoryUsage  |  Geheugengebruik  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  MemoryUsagePercentageInDouble  |  Percentage geheugen gebruik  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  PendingTransactions  |  Trans acties in behandeling  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  ProcessedBlocks  |  Verwerkte blokken  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  ProcessedTransactions  |  Verwerkte trans acties  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  QueuedTransactions  |  Trans acties in de wachtrij  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  RequestHandled  |  Verwerkte aanvragen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Blockchain/blockchainMembers  |  StorageUsage  |  Opslag gebruik  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cachehits  |  Cachetreffers  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cachehits0  |  Cache treffers (Shard 0)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cachehits1  |  Cache treffers (Shard 1)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cachehits2  |  Cache treffers (Shard 2)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cachehits3  |  Cache treffers (Shard 3)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cachehits4  |  Cache treffers (Shard 4)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cachehits5  |  Cache treffers (Shard 5)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cachehits6  |  Cache treffers (Shard 6)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cachehits7  |  Cache treffers (Shard 7)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cachehits8  |  Cache treffers (Shard 8)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cachehits9  |  Cache treffers (Shard 9)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cacheLatency  |  Cache latentie micro seconden (preview-versie)  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cachemisses  |  Cachemissers  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cachemisses0  |  Cache missers (Shard 0)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cachemisses1  |  Cache missers (Shard 1)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cachemisses2  |  Cache missers (Shard 2)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cachemisses3  |  Cache missers (Shard 3)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cachemisses4  |  Cache missers (Shard 4)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cachemisses5  |  Cache missers (Shard 5)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cachemisses6  |  Cache missers (Shard 6)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cachemisses7  |  Cache missers (Shard 7)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cachemisses8  |  Cache missers (Shard 8)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cachemisses9  |  Cache missers (Shard 9)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cacheRead  |  Gelezen uit cache  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cacheRead0  |  Cache gelezen (Shard 0)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cacheRead1  |  Lees bewerking in cache (Shard 1)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cacheRead2  |  Lees bewerking in cache (Shard 2)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cacheRead3  |  Lees bewerking in cache (Shard 3)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cacheRead4  |  Lees bewerking in cache (Shard 4)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cacheRead5  |  Lees bewerking in cache (Shard 5)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cacheRead6  |  Lees bewerking in cache (Shard 6)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cacheRead7  |  Lees bewerking in cache (Shard 7)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cacheRead8  |  Lees bewerking in cache (Shard 8)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cacheRead9  |  Lees bewerking in cache (Shard 9)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cacheWrite  |  Geschreven naar cache  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cacheWrite0  |  Cache schrijven (Shard 0)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cacheWrite1  |  Cache schrijven (Shard 1)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cacheWrite2  |  Cache schrijven (Shard 2)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cacheWrite3  |  Cache schrijven (Shard 3)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cacheWrite4  |  Cache schrijven (Shard 4)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cacheWrite5  |  Cache schrijven (Shard 5)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cacheWrite6  |  Cache schrijven (Shard 6)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cacheWrite7  |  Cache schrijven (Shard 7)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cacheWrite8  |  Cache schrijven (Shard 8)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  cacheWrite9  |  Cache schrijven (Shard 9)  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  connectedclients  |  Verbonden clients  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  connectedclients0  |  Verbonden clients (Shard 0)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  connectedclients1  |  Verbonden clients (Shard 1)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  connectedclients2  |  Verbonden clients (Shard 2)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  connectedclients3  |  Verbonden clients (Shard 3)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  connectedclients4  |  Verbonden clients (Shard 4)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  connectedclients5  |  Verbonden clients (Shard 5)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  connectedclients6  |  Verbonden clients (Shard 6)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  connectedclients7  |  Verbonden clients (Shard 7)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  connectedclients8  |  Verbonden clients (Shard 8)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  connectedclients9  |  Verbonden clients (Shard 9)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  fouten  |  Errors  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  evictedkeys  |  Verwijderde sleutels  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  evictedkeys0  |  Verwijderde sleutels (Shard 0)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  evictedkeys1  |  Verwijderde sleutels (Shard 1)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  evictedkeys2  |  Verwijderde sleutels (Shard 2)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  evictedkeys3  |  Verwijderde sleutels (Shard 3)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  evictedkeys4  |  Verwijderde sleutels (Shard 4)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  evictedkeys5  |  Verwijderde sleutels (Shard 5)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  evictedkeys6  |  Verwijderde sleutels (Shard 6)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  evictedkeys7  |  Verwijderde sleutels (Shard 7)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  evictedkeys8  |  Verwijderde sleutels (Shard 8)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  evictedkeys9  |  Verwijderde sleutels (Shard 9)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  expiredkeys  |  Verlopen sleutels  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  expiredkeys0  |  Verlopen sleutels (Shard 0)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  expiredkeys1  |  Verlopen sleutels (Shard 1)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  expiredkeys2  |  Verlopen sleutels (Shard 2)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  expiredkeys3  |  Verlopen sleutels (Shard 3)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  expiredkeys4  |  Verlopen sleutels (Shard 4)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  expiredkeys5  |  Verlopen sleutels (Shard 5)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  expiredkeys6  |  Verlopen sleutels (Shard 6)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  expiredkeys7  |  Verlopen sleutels (Shard 7)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  expiredkeys8  |  Verlopen sleutels (Shard 8)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  expiredkeys9  |  Verlopen sleutels (Shard 9)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  getcommands  |  Ophalingen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  getcommands0  |  Hiermee wordt opgehaald (Shard 0)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  getcommands1  |  Hiermee wordt opgehaald (Shard 1)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  getcommands2  |  Hiermee wordt opgehaald (Shard 2)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  getcommands3  |  Hiermee wordt opgehaald (Shard 3)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  getcommands4  |  Hiermee wordt opgehaald (Shard 4)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  getcommands5  |  Hiermee wordt opgehaald (Shard 5)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  getcommands6  |  Hiermee wordt opgehaald (Shard 6)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  getcommands7  |  Hiermee wordt opgehaald (Shard 7)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  getcommands8  |  Hiermee wordt opgehaald (Shard 8)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  getcommands9  |  Hiermee wordt opgehaald (Shard 9)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  operationsPerSecond  |  Bewerkingen per seconde  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  operationsPerSecond0  |  Bewerkingen per seconde (Shard 0)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  operationsPerSecond1  |  Bewerkingen per seconde (Shard 1)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  operationsPerSecond2  |  Bewerkingen per seconde (Shard 2)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  operationsPerSecond3  |  Bewerkingen per seconde (Shard 3)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  operationsPerSecond4  |  Bewerkingen per seconde (Shard 4)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  operationsPerSecond5  |  Bewerkingen per seconde (Shard 5)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  operationsPerSecond6  |  Bewerkingen per seconde (Shard 6)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  operationsPerSecond7  |  Bewerkingen per seconde (Shard 7)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  operationsPerSecond8  |  Bewerkingen per seconde (Shard 8)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  operationsPerSecond9  |  Bewerkingen per seconde (Shard 9)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  percentProcessorTime  |  CPU  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  percentProcessorTime0  |  CPU (Shard 0)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  percentProcessorTime1  |  CPU (Shard 1)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  percentProcessorTime2  |  CPU (Shard 2)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  percentProcessorTime3  |  CPU (Shard 3)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  percentProcessorTime4  |  CPU (Shard 4)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  percentProcessorTime5  |  CPU (Shard 5)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  percentProcessorTime6  |  CPU (Shard 6)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  percentProcessorTime7  |  CPU (Shard 7)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  percentProcessorTime8  |  CPU (Shard 8)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  percentProcessorTime9  |  CPU (Shard 9)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  serverLoad  |  Serverbelasting  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  serverLoad0  |  Server belasting (Shard 0)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  serverLoad1  |  Server belasting (Shard 1)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  serverLoad2  |  Server belasting (Shard 2)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  serverLoad3  |  Server belasting (Shard 3)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  serverLoad4  |  Server belasting (Shard 4)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  serverLoad5  |  Server belasting (Shard 5)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  serverLoad6  |  Server belasting (Shard 6)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  serverLoad7  |  Server belasting (Shard 7)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  serverLoad8  |  Server belasting (Shard 8)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  serverLoad9  |  Server belasting (Shard 9)  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  setcommands  |  Instellingen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  setcommands0  |  Sets (Shard 0)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  setcommands1  |  Sets (Shard 1)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  setcommands2  |  Sets (Shard 2)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  setcommands3  |  Sets (Shard 3)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  setcommands4  |  Sets (Shard 4)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  setcommands5  |  Sets (Shard 5)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  setcommands6  |  Sets (Shard 6)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  setcommands7  |  Sets (Shard 7)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  setcommands8  |  Sets (Shard 8)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  setcommands9  |  Sets (Shard 9)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  totalcommandsprocessed  |  Totaalaantal bewerkingen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  totalcommandsprocessed0  |  Totaal aantal bewerkingen (Shard 0)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  totalcommandsprocessed1  |  Totaal aantal bewerkingen (Shard 1)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  totalcommandsprocessed2  |  Totaal aantal bewerkingen (Shard 2)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  totalcommandsprocessed3  |  Totaal aantal bewerkingen (Shard 3)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  totalcommandsprocessed4  |  Totaal aantal bewerkingen (Shard 4)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  totalcommandsprocessed5  |  Totaal aantal bewerkingen (Shard 5)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  totalcommandsprocessed6  |  Totaal aantal bewerkingen (Shard 6)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  totalcommandsprocessed7  |  Totaal aantal bewerkingen (Shard 7)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  totalcommandsprocessed8  |  Totaal aantal bewerkingen (Shard 8)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  totalcommandsprocessed9  |  Totaal aantal bewerkingen (Shard 9)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  totalkeys  |  Totaal aantal sleutels  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  totalkeys0  |  Totaal aantal sleutels (Shard 0)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  totalkeys1  |  Totaal aantal sleutels (Shard 1)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  totalkeys2  |  Totaal aantal sleutels (Shard 2)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  totalkeys3  |  Totaal aantal sleutels (Shard 3)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  totalkeys4  |  Totaal aantal sleutels (Shard 4)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  totalkeys5  |  Totaal aantal sleutels (Shard 5)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  totalkeys6  |  Totaal aantal sleutels (Shard 6)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  totalkeys7  |  Totaal aantal sleutels (Shard 7)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  totalkeys8  |  Totaal aantal sleutels (Shard 8)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  totalkeys9  |  Totaal aantal sleutels (Shard 9)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  usedmemory  |  Gebruikt geheugen  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  usedmemory0  |  Gebruikt geheugen (Shard 0)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  usedmemory1  |  Gebruikt geheugen (Shard 1)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  usedmemory2  |  Gebruikt geheugen (Shard 2)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  usedmemory3  |  Gebruikt geheugen (Shard 3)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  usedmemory4  |  Gebruikt geheugen (Shard 4)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  usedmemory5  |  Gebruikt geheugen (Shard 5)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  usedmemory6  |  Gebruikt geheugen (Shard 6)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  usedmemory7  |  Gebruikt geheugen (Shard 7)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  usedmemory8  |  Gebruikt geheugen (Shard 8)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  usedmemory9  |  Gebruikt geheugen (Shard 9)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  usedmemorypercentage  |  Percentage gebruikt geheugen  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  usedmemoryRss  |  Gebruikte geheugen-RSS  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  usedmemoryRss0  |  Gebruikt geheugen RSS (Shard 0)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  usedmemoryRss1  |  Gebruikte geheugen RSS (Shard 1)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  usedmemoryRss2  |  Gebruikte geheugen RSS (Shard 2)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  usedmemoryRss3  |  Gebruikte geheugen RSS (Shard 3)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  usedmemoryRss4  |  Gebruikte geheugen RSS (Shard 4)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  usedmemoryRss5  |  Gebruikte geheugen RSS (Shard 5)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  usedmemoryRss6  |  Gebruikte geheugen RSS (Shard 6)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  usedmemoryRss7  |  Gebruikte geheugen RSS (Shard 7)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  usedmemoryRss8  |  Gebruikt geheugen RSS (Shard 8)  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. cache/redis  |  usedmemoryRss9  |  Gebruikte geheugen RSS (Shard 9)  |  Bytes  |  Maximum | 
| Nee  | Nee |  Micro soft. ClassicCompute/domein naam/sleuven/rollen  |  Gelezen bytes per seconde  |  Schijf lezen  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicCompute/domein naam/sleuven/rollen  |  Leesbewerkingen op de schijf/seconde  |  Leesbewerkingen op de schijf/seconde  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Micro soft. ClassicCompute/domein naam/sleuven/rollen  |  Geschreven bytes per seconde  |  Schijf schrijven  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicCompute/domein naam/sleuven/rollen  |  Schrijfbewerkingen op de schijf/seconde  |  Schrijfbewerkingen op de schijf/seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicCompute/domein naam/sleuven/rollen  |  Netwerk in  |  Netwerk in  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ClassicCompute/domein naam/sleuven/rollen  |  Netwerk uit  |  Netwerk uit  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ClassicCompute/domein naam/sleuven/rollen  |  CPU-percentage  |  CPU-percentage  |  Percentage  |  Average | 
| Nee  | Nee |  Micro soft. ClassicCompute/informatie  |  Gelezen bytes per seconde  |  Schijf lezen  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicCompute/informatie  |  Leesbewerkingen op de schijf/seconde  |  Leesbewerkingen op de schijf/seconde  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Micro soft. ClassicCompute/informatie  |  Geschreven bytes per seconde  |  Schijf schrijven  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicCompute/informatie  |  Schrijfbewerkingen op de schijf/seconde  |  Schrijfbewerkingen op de schijf/seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicCompute/informatie  |  Netwerk in  |  Netwerk in  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ClassicCompute/informatie  |  Netwerk uit  |  Netwerk uit  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ClassicCompute/informatie  |  CPU-percentage  |  CPU-percentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts  |  Beschikbaarheid  |  Beschikbaarheid  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts  |  SuccessE2ELatency  |  Success E2E Latency  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts  |  SuccessServerLatency  |  Geslaagde server latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts  |  Transacties  |  Transacties  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. ClassicStorage/Storage accounts  |  UsedCapacity  |  Gebruikte capaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/blobServices  |  Beschikbaarheid  |  Beschikbaarheid  |  Percentage  |  Average | 
| Nee  | Nee |  Micro soft. ClassicStorage/Storage accounts/blobServices  |  BlobCapacity  |  BLOB-capaciteit  |  Bytes  |  Average | 
| Nee  | Nee |  Micro soft. ClassicStorage/Storage accounts/blobServices  |  BlobCount  |  Aantal blobs  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/blobServices  |  ContainerCount  |  Aantal BLOB-containers  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/blobServices  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| Nee  | Nee |  Micro soft. ClassicStorage/Storage accounts/blobServices  |  IndexCapacity  |  Index capaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/blobServices  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/blobServices  |  SuccessE2ELatency  |  Success E2E Latency  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/blobServices  |  SuccessServerLatency  |  Geslaagde server latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/blobServices  |  Transacties  |  Transacties  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/fileServices  |  Beschikbaarheid  |  Beschikbaarheid  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/fileServices  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| Nee  | Nee |  Micro soft. ClassicStorage/Storage accounts/fileServices  |  FileCapacity  |  Bestands capaciteit  |  Bytes  |  Average | 
| Nee  | Nee |  Micro soft. ClassicStorage/Storage accounts/fileServices  |  FileCount  |  Aantal bestanden  |  Aantal  |  Average | 
| Nee  | Nee |  Micro soft. ClassicStorage/Storage accounts/fileServices  |  FileShareCount  |  Aantal bestands shares  |  Aantal  |  Average | 
| Nee  | Nee |  Micro soft. ClassicStorage/Storage accounts/fileServices  |  FileShareQuota  |  Quota grootte van bestands share  |  Bytes  |  Average | 
| Nee  | Nee |  Micro soft. ClassicStorage/Storage accounts/fileServices  |  FileShareSnapshotCount  |  Aantal moment opnamen van bestands shares  |  Aantal  |  Average | 
| Nee  | Nee |  Micro soft. ClassicStorage/Storage accounts/fileServices  |  FileShareSnapshotSize  |  Grootte van moment opname van bestands share  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/fileServices  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/fileServices  |  SuccessE2ELatency  |  Success E2E Latency  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/fileServices  |  SuccessServerLatency  |  Geslaagde server latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/fileServices  |  Transacties  |  Transacties  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/queueServices  |  Beschikbaarheid  |  Beschikbaarheid  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/queueServices  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/queueServices  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/queueServices  |  QueueCapacity  |  Wachtrij capaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/queueServices  |  QueueCount  |  Aantal wachtrijen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/queueServices  |  QueueMessageCount  |  Aantal wachtrij berichten  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/queueServices  |  SuccessE2ELatency  |  Success E2E Latency  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/queueServices  |  SuccessServerLatency  |  Geslaagde server latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/queueServices  |  Transacties  |  Transacties  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/tableServices  |  Beschikbaarheid  |  Beschikbaarheid  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/tableServices  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/tableServices  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/tableServices  |  SuccessE2ELatency  |  Success E2E Latency  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/tableServices  |  SuccessServerLatency  |  Geslaagde server latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/tableServices  |  TableCapacity  |  Tabel capaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/tableServices  |  TableCount  |  Aantal tabellen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/tableServices  |  TableEntityCount  |  Aantal tabel entiteiten  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. ClassicStorage/Storage accounts/tableServices  |  Transacties  |  Transacties  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. CognitiveServices/accounts  |  BlockedCalls  |  Geblokkeerde aanroepen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. CognitiveServices/accounts  |  CharactersTrained  |  Getrainde tekens  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. CognitiveServices/accounts  |  CharactersTranslated  |  Geconverteerde tekens  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. CognitiveServices/accounts  |  ClientErrors  |  Client fouten  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. CognitiveServices/accounts  |  DataIn  |  Gegevens in  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. CognitiveServices/accounts  |  DataOut  |  Gegevens uit  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. CognitiveServices/accounts  |  Latentie  |  Latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. CognitiveServices/accounts  |  ServerErrors  |  Server fouten  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. CognitiveServices/accounts  |  SpeechSessionDuration  |  Spraak sessie duur  |  Seconden  |  Totaal | 
| **Ja**  | Nee |  Micro soft. CognitiveServices/accounts  |  SuccessfulCalls  |  Geslaagde aanroepen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. CognitiveServices/accounts  |  TotalCalls  |  Totaal aantal aanroepen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. CognitiveServices/accounts  |  TotalErrors  |  Totaalaantal fouten  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. CognitiveServices/accounts  |  TotalTokenCalls  |  Totaal aantal token aanroepen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. CognitiveServices/accounts  |  TotalTransactions  |  Totaal aantal trans acties  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Verbruikt CPU-tegoed  |  Verbruikt CPU-tegoed  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Resterend CPU-tegoed  |  Resterend CPU-tegoed  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Wachtrijlengte van gegevensschijf  |  Wachtrij diepte van gegevens schijf (preview-versie)  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Gegevens schijf gelezen bytes per seconde  |  Gegevens schijf gelezen bytes per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Lees bewerkingen op de gegevens schijf per seconde  |  Lees bewerkingen op de gegevens schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Geschreven bytes per seconde gegevens schijf  |  Geschreven bytes per seconde (preview) gegevens schijf  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Schrijf bewerkingen op de gegevens schijf per seconde  |  Schrijf bewerkingen op de gegevens schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Gelezen bytes op de schijf  |  Gelezen bytes op de schijf  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Leesbewerkingen op de schijf/seconde  |  Leesbewerkingen op de schijf/seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Geschreven bytes op de schijf  |  Geschreven bytes op de schijf  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Schrijfbewerkingen op de schijf/seconde  |  Schrijfbewerkingen op de schijf/seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Binnenkomende stromen  |  Binnenkomende stromen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Maximum aanmaak frequentie inkomende stromen  |  Maximum aantal aanmaak snelheden inkomende stromen (preview-versie)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Netwerk in  |  Netwerk in Factureerbaar (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Totaal netwerk  |  Totaal netwerk  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Netwerk uit  |  Gefactureerd netwerk (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Totaal aantal netwerk  |  Totaal aantal netwerk  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Wachtrijlengte van besturingssysteemschijf  |  Wachtrij diepte van de besturingssysteem schijf (preview-versie)  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  BESTURINGSSYSTEEM schijf gelezen bytes per seconde  |  BESTURINGSSYSTEEM schijf gelezen bytes per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Lees bewerkingen van de besturingssysteem schijf per seconde  |  Lees bewerkingen op de besturingssysteem schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  WACHTRIJ diepte voor het besturings systeem per schijf  |  WACHTRIJ diepte van de besturingssysteem schijf (afgeschaft)  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  BESTURINGSSYSTEEM per schijf gelezen bytes per seconde  |  BESTURINGSSYSTEEM schijf gelezen bytes per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Lees bewerkingen per schijf voor het besturings systeem/sec.  |  Lees bewerkingen op de besturingssysteem schijf per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Per schijf geschreven bytes/sec.  |  BESTURINGSSYSTEEM schijf schrijf bewerkingen in bytes per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Schrijf bewerkingen per schijf van het besturings systeem/SEC  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Uitgaande stromen  |  Uitgaande stromen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Maximum aanmaak frequentie van uitgaande stromen  |  Maximum aantal uitgaande stromen voor maken (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  WACHTRIJ diepte per schijf  |  WACHTRIJ diepte van de gegevens schijf (afgeschaft)  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Per schijf gelezen bytes/sec.  |  Gegevens schijf gelezen bytes per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Lees bewerkingen per schijf/sec.  |  Lees bewerkingen op de gegevens schijf per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Schrijf bewerkingen per schijf/sec.  |  Gegevens schijf schrijf bewerkingen in bytes per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Schrijf bewerkingen per schijf/SEC  |  Schrijf bewerkingen op de gegevens schijf per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  CPU-percentage  |  CPU-percentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Treffer voor Premium data-schijf cache lezen  |  Cache geheugen voor lezen van Premium-gegevens schijf (preview-versie)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Lees missers cache Premium-gegevens schijf  |  Cache voor lezen van Premium-gegevens schijf (preview)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Treffer voor Premium-besturingssysteem schijf cache lezen  |  Treffer voor het lezen van een Premium-besturingssysteem schijf cache (preview-versie)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachines  |  Leesmij voor Premium-besturingssysteem schijf cache lezen  |  Schijf cache voor Premium-Lees-missers (preview-versie)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Verbruikt CPU-tegoed  |  Verbruikt CPU-tegoed  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Resterend CPU-tegoed  |  Resterend CPU-tegoed  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Wachtrijlengte van gegevensschijf  |  Wachtrij diepte van gegevens schijf (preview-versie)  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Gegevens schijf gelezen bytes per seconde  |  Gegevens schijf gelezen bytes per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Lees bewerkingen op de gegevens schijf per seconde  |  Lees bewerkingen op de gegevens schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Geschreven bytes per seconde gegevens schijf  |  Geschreven bytes per seconde (preview) gegevens schijf  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Schrijf bewerkingen op de gegevens schijf per seconde  |  Schrijf bewerkingen op de gegevens schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Gelezen bytes op de schijf  |  Gelezen bytes op de schijf  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Leesbewerkingen op de schijf/seconde  |  Leesbewerkingen op de schijf/seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Geschreven bytes op de schijf  |  Geschreven bytes op de schijf  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Schrijfbewerkingen op de schijf/seconde  |  Schrijfbewerkingen op de schijf/seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Binnenkomende stromen  |  Binnenkomende stromen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Maximum aanmaak frequentie inkomende stromen  |  Maximum aantal aanmaak snelheden inkomende stromen (preview-versie)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Netwerk in  |  Netwerk in Factureerbaar (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Totaal netwerk  |  Totaal netwerk  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Netwerk uit  |  Gefactureerd netwerk (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Totaal aantal netwerk  |  Totaal aantal netwerk  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Wachtrijlengte van besturingssysteemschijf  |  Wachtrij diepte van de besturingssysteem schijf (preview-versie)  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  BESTURINGSSYSTEEM schijf gelezen bytes per seconde  |  BESTURINGSSYSTEEM schijf gelezen bytes per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Lees bewerkingen van de besturingssysteem schijf per seconde  |  Lees bewerkingen op de besturingssysteem schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  WACHTRIJ diepte voor het besturings systeem per schijf  |  WACHTRIJ diepte van de besturingssysteem schijf (afgeschaft)  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  BESTURINGSSYSTEEM per schijf gelezen bytes per seconde  |  BESTURINGSSYSTEEM schijf gelezen bytes per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Lees bewerkingen per schijf voor het besturings systeem/sec.  |  Lees bewerkingen op de besturingssysteem schijf per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Per schijf geschreven bytes/sec.  |  BESTURINGSSYSTEEM schijf schrijf bewerkingen in bytes per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Schrijf bewerkingen per schijf van het besturings systeem/SEC  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Uitgaande stromen  |  Uitgaande stromen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Maximum aanmaak frequentie van uitgaande stromen  |  Maximum aantal uitgaande stromen voor maken (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  WACHTRIJ diepte per schijf  |  WACHTRIJ diepte van de gegevens schijf (afgeschaft)  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Per schijf gelezen bytes/sec.  |  Gegevens schijf gelezen bytes per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Lees bewerkingen per schijf/sec.  |  Lees bewerkingen op de gegevens schijf per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Schrijf bewerkingen per schijf/sec.  |  Gegevens schijf schrijf bewerkingen in bytes per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Schrijf bewerkingen per schijf/SEC  |  Schrijf bewerkingen op de gegevens schijf per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  CPU-percentage  |  CPU-percentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Treffer voor Premium data-schijf cache lezen  |  Cache geheugen voor lezen van Premium-gegevens schijf (preview-versie)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Lees missers cache Premium-gegevens schijf  |  Cache voor lezen van Premium-gegevens schijf (preview)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Treffer voor Premium-besturingssysteem schijf cache lezen  |  Treffer voor het lezen van een Premium-besturingssysteem schijf cache (preview-versie)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Compute/virtualMachineScaleSets  |  Leesmij voor Premium-besturingssysteem schijf cache lezen  |  Schijf cache voor Premium-Lees-missers (preview-versie)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Verbruikt CPU-tegoed  |  Verbruikt CPU-tegoed  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Resterend CPU-tegoed  |  Resterend CPU-tegoed  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Wachtrijlengte van gegevensschijf  |  Wachtrij diepte van gegevens schijf (preview-versie)  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Gegevens schijf gelezen bytes per seconde  |  Gegevens schijf gelezen bytes per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Lees bewerkingen op de gegevens schijf per seconde  |  Lees bewerkingen op de gegevens schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Geschreven bytes per seconde gegevens schijf  |  Geschreven bytes per seconde (preview) gegevens schijf  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Schrijf bewerkingen op de gegevens schijf per seconde  |  Schrijf bewerkingen op de gegevens schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Gelezen bytes op de schijf  |  Gelezen bytes op de schijf  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Leesbewerkingen op de schijf/seconde  |  Leesbewerkingen op de schijf/seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Geschreven bytes op de schijf  |  Geschreven bytes op de schijf  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Schrijfbewerkingen op de schijf/seconde  |  Schrijfbewerkingen op de schijf/seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Binnenkomende stromen  |  Binnenkomende stromen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Maximum aanmaak frequentie inkomende stromen  |  Maximum aantal aanmaak snelheden inkomende stromen (preview-versie)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Netwerk in  |  Netwerk in Factureerbaar (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Totaal netwerk  |  Totaal netwerk  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Netwerk uit  |  Gefactureerd netwerk (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Totaal aantal netwerk  |  Totaal aantal netwerk  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Wachtrijlengte van besturingssysteemschijf  |  Wachtrij diepte van de besturingssysteem schijf (preview-versie)  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  BESTURINGSSYSTEEM schijf gelezen bytes per seconde  |  BESTURINGSSYSTEEM schijf gelezen bytes per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Lees bewerkingen van de besturingssysteem schijf per seconde  |  Lees bewerkingen op de besturingssysteem schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  WACHTRIJ diepte voor het besturings systeem per schijf  |  WACHTRIJ diepte van de besturingssysteem schijf (afgeschaft)  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  BESTURINGSSYSTEEM per schijf gelezen bytes per seconde  |  BESTURINGSSYSTEEM schijf gelezen bytes per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Lees bewerkingen per schijf voor het besturings systeem/sec.  |  Lees bewerkingen op de besturingssysteem schijf per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Per schijf geschreven bytes/sec.  |  BESTURINGSSYSTEEM schijf schrijf bewerkingen in bytes per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Schrijf bewerkingen per schijf van het besturings systeem/SEC  |  Schrijf bewerkingen op de besturingssysteem schijf per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Uitgaande stromen  |  Uitgaande stromen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Maximum aanmaak frequentie van uitgaande stromen  |  Maximum aantal uitgaande stromen voor maken (preview)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  WACHTRIJ diepte per schijf  |  WACHTRIJ diepte van de gegevens schijf (afgeschaft)  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Per schijf gelezen bytes/sec.  |  Gegevens schijf gelezen bytes per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Lees bewerkingen per schijf/sec.  |  Lees bewerkingen op de gegevens schijf per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Schrijf bewerkingen per schijf/sec.  |  Gegevens schijf schrijf bewerkingen in bytes per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Schrijf bewerkingen per schijf/SEC  |  Schrijf bewerkingen op de gegevens schijf per seconde (afgeschaft)  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  CPU-percentage  |  CPU-percentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Treffer voor Premium data-schijf cache lezen  |  Cache geheugen voor lezen van Premium-gegevens schijf (preview-versie)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Lees missers cache Premium-gegevens schijf  |  Cache voor lezen van Premium-gegevens schijf (preview)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Treffer voor Premium-besturingssysteem schijf cache lezen  |  Treffer voor het lezen van een Premium-besturingssysteem schijf cache (preview-versie)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. Compute/virtualMachineScaleSets/informatie  |  Leesmij voor Premium-besturingssysteem schijf cache lezen  |  Schijf cache voor Premium-Lees-missers (preview-versie)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. ContainerInstance/containerGroups  |  CpuUsage  |  CPU-gebruik  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. ContainerInstance/containerGroups  |  MemoryUsage  |  Geheugengebruik  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. ContainerInstance/containerGroups  |  NetworkBytesReceivedPerSecond  |  Ontvangen netwerk bytes per seconde  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. ContainerInstance/containerGroups  |  NetworkBytesTransmittedPerSecond  |  Verzonden netwerk bytes per seconde  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. ContainerRegistry/registers  |  RunDuration  |  Uitvoerings duur  |  Milliseconden  |  Totaal | 
| **Ja**  | Nee |  Micro soft. ContainerRegistry/registers  |  SuccessfulPullCount  |  Aantal geslaagde pull-bewerkingen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. ContainerRegistry/registers  |  SuccessfulPushCount  |  Aantal geslaagde push berichten  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. ContainerRegistry/registers  |  TotalPullCount  |  Totaal aantal pull-bewerkingen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. ContainerRegistry/registers  |  TotalPushCount  |  Totaal aantal push berichten  |  Aantal  |  Average | 
| Nee  | Nee |  Micro soft. container service/managedClusters  |  kube_node_status_allocatable_cpu_cores  |  Totaal aantal beschik bare CPU-kernen in een beheerd cluster  |  Aantal  |  Average | 
| Nee  | Nee |  Micro soft. container service/managedClusters  |  kube_node_status_allocatable_memory_bytes  |  Totale hoeveelheid beschikbaar geheugen in een beheerd cluster  |  Bytes  |  Average | 
| Nee  | Nee |  Micro soft. container service/managedClusters  |  kube_node_status_condition  |  Statussen voor de verschillende knooppunt voorwaarden  |  Aantal  |  Average | 
| Nee  | Nee |  Micro soft. container service/managedClusters  |  kube_pod_status_phase  |  Aantal per fase  |  Aantal  |  Average | 
| Nee  | Nee |  Micro soft. container service/managedClusters  |  kube_pod_status_ready  |  Aantal in de status gereed  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. DataBoxEdge/dataBoxEdgeDevices  |  Availablecapacity;)  |  Beschik bare capaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloud  |  Geüploade Cloud bytes (apparaat)  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. DataBoxEdge/dataBoxEdgeDevices  |  BytesUploadedToCloudPerShare  |  Geüploade Cloud bytes (delen)  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughput  |  Door Voer van Cloud downloaden  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. DataBoxEdge/dataBoxEdgeDevices  |  CloudReadThroughputPerShare  |  Door Voer van Cloud downloaden (delen)  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughput  |  Upload doorvoer van Cloud  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. DataBoxEdge/dataBoxEdgeDevices  |  CloudUploadThroughputPerShare  |  Upload doorvoer van Cloud (delen)  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. DataBoxEdge/dataBoxEdgeDevices  |  HyperVMemoryUtilization  |  Edge Compute-geheugen gebruik  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. DataBoxEdge/dataBoxEdgeDevices  |  HyperVVirtualProcessorUtilization  |  Edge Compute-percentage CPU  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. DataBoxEdge/dataBoxEdgeDevices  |  NICReadThroughput  |  Lees doorvoer (netwerk)  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. DataBoxEdge/dataBoxEdgeDevices  |  NICWriteThroughput  |  Schrijf doorvoer (netwerk)  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. DataBoxEdge/dataBoxEdgeDevices  |  TotalCapacity  |  Totale capaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. DataFactory/datafactories  |  FailedRuns  |  Mislukte uitvoeringen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. DataFactory/datafactories  |  SuccessfulRuns  |  Geslaagde uitvoeringen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. DataFactory/fabrieken  |  ActivityCancelledRuns  |  Metrische gegevens voor geannuleerde activiteit uitgevoerd  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. DataFactory/fabrieken  |  ActivityFailedRuns  |  Metrische gegevens mislukte uitvoering van activiteit  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. DataFactory/fabrieken  |  ActivitySucceededRuns  |  Metrische gegevens uitvoeringen uitgevoerde activiteit  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. DataFactory/fabrieken  |  FactorySizeInGbUnits  |  Totale grootte van de fabriek (GB-eenheid)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. DataFactory/fabrieken  |  IntegrationRuntimeAvailableMemory  |  Beschik bare geheugen voor Integration runtime  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. DataFactory/fabrieken  |  IntegrationRuntimeAverageTaskPickupDelay  |  Duur van de wachtrij voor Integration runtime  |  Seconden  |  Average | 
| **Ja**  | Nee |  Micro soft. DataFactory/fabrieken  |  IntegrationRuntimeCpuPercentage  |  CPU-gebruik van Integration runtime  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. DataFactory/fabrieken  |  IntegrationRuntimeQueueLength  |  Lengte van de wachtrij voor Integration runtime  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. DataFactory/fabrieken  |  MaxAllowedFactorySizeInGbUnits  |  Maxi maal toegestane grootte van de fabriek (GB-eenheid)  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. DataFactory/fabrieken  |  MaxAllowedResourceCount  |  Maximum aantal toegestane entiteiten  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. DataFactory/fabrieken  |  PipelineCancelledRuns  |  Metrische gegevens van de pijplijn uitvoeringen geannuleerd  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. DataFactory/fabrieken  |  PipelineFailedRuns  |  Metrische gegevens van mislukte pijplijn uitvoeringen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. DataFactory/fabrieken  |  PipelineSucceededRuns  |  Metrische uitvoerings metingen geslaagde pijp lijnen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. DataFactory/fabrieken  |  ResourceCount  |  Totaal aantal entiteiten  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. DataFactory/fabrieken  |  TriggerCancelledRuns  |  Metrische gegevens over de trigger is geannuleerd  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. DataFactory/fabrieken  |  TriggerFailedRuns  |  Meet waarden voor uitvoering van mislukte triggers  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. DataFactory/fabrieken  |  TriggerSucceededRuns  |  Meet waarden voor uitvoering van geslaagde triggers  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. DataLakeAnalytics/accounts  |  JobAUEndedCancelled  |  Geannuleerde AU-tijd  |  Seconden  |  Totaal | 
| **Ja**  | Nee |  Micro soft. DataLakeAnalytics/accounts  |  JobAUEndedFailure  |  Mislukte AU-tijd  |  Seconden  |  Totaal | 
| **Ja**  | Nee |  Micro soft. DataLakeAnalytics/accounts  |  JobAUEndedSuccess  |  Geslaagde AU-tijd  |  Seconden  |  Totaal | 
| **Ja**  | Nee |  Micro soft. DataLakeAnalytics/accounts  |  JobEndedCancelled  |  Geannuleerde taken  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. DataLakeAnalytics/accounts  |  JobEndedFailure  |  Mislukte taken  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. DataLakeAnalytics/accounts  |  JobEndedSuccess  |  Geslaagde taken  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. data Lake Store/accounts  |  DataRead  |  Gegevens lezen  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. data Lake Store/accounts  |  DataWritten  |  Gegevens geschreven  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. data Lake Store/accounts  |  ReadRequests  |  Aanvragen lezen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. data Lake Store/accounts  |  TotalStorage  |  Totale opslagruimte  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. data Lake Store/accounts  |  WriteRequests  |  Aanvragen schrijven  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  active_connections  |  Actieve verbindingen  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  backup_storage_used  |  Gebruikte back-upopslag  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  connections_failed  |  Mislukte verbindingen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  cpu_percent  |  CPU-percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  io_consumption_percent  |  IO-percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  memory_percent  |  Geheugen percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  network_bytes_egress  |  Netwerk uit  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  network_bytes_ingress  |  Netwerk in  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  seconds_behind_master  |  Replicatie vertraging in seconden  |  Aantal  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_limit  |  Opslag limiet voor server logboek  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_percent  |  Percentage server logboek opslag  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  serverlog_storage_usage  |  Gebruikte server logboek opslag  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  storage_limit  |  Opslag limiet  |  Bytes  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  storage_percent  |  Opslag percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMariaDB/servers  |  storage_used  |  Gebruikte opslag  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  active_connections  |  Actieve verbindingen  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  backup_storage_used  |  Gebruikte back-upopslag  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  connections_failed  |  Mislukte verbindingen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  cpu_percent  |  CPU-percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  io_consumption_percent  |  IO-percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  memory_percent  |  Geheugen percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  network_bytes_egress  |  Netwerk uit  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  network_bytes_ingress  |  Netwerk in  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  seconds_behind_master  |  Replicatie vertraging in seconden  |  Aantal  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_limit  |  Opslag limiet voor server logboek  |  Bytes  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_percent  |  Percentage server logboek opslag  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  serverlog_storage_usage  |  Gebruikte server logboek opslag  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  storage_limit  |  Opslag limiet  |  Bytes  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  storage_percent  |  Opslag percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DBforMySQL/servers  |  storage_used  |  Gebruikte opslag  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  active_connections  |  Actieve verbindingen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  backup_storage_used  |  Gebruikte back-upopslag  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  connections_failed  |  Mislukte verbindingen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  cpu_percent  |  CPU-percentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  io_consumption_percent  |  IO-percentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  memory_percent  |  Geheugen percentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_egress  |  Netwerk uit  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  network_bytes_ingress  |  Netwerk in  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_bytes  |  Maximale vertraging in Replica's  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  pg_replica_log_delay_in_seconds  |  Replica vertraging  |  Seconden  |  Maximum | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_limit  |  Opslag limiet voor server logboek  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_percent  |  Percentage server logboek opslag  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  serverlog_storage_usage  |  Gebruikte server logboek opslag  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  storage_limit  |  Opslag limiet  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  storage_percent  |  Opslag percentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.DBforPostgreSQL/servers  |  storage_used  |  Gebruikte opslag  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. DBforPostgreSQL/serversv2  |  active_connections  |  Actieve verbindingen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. DBforPostgreSQL/serversv2  |  cpu_percent  |  CPU-percentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. DBforPostgreSQL/serversv2  |  IOPS  |  IOPS  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. DBforPostgreSQL/serversv2  |  memory_percent  |  Geheugen percentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. DBforPostgreSQL/serversv2  |  network_bytes_egress  |  Netwerk uit  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. DBforPostgreSQL/serversv2  |  network_bytes_ingress  |  Netwerk in  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. DBforPostgreSQL/serversv2  |  storage_percent  |  Opslag percentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. DBforPostgreSQL/serversv2  |  storage_used  |  Gebruikte opslag  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Micro soft. apparaten/account  |  digitaltwins. telemetrie. nodes  |  Tijdelijke aanduiding voor telemetrie van Digital Apparaatdubbels-knoop punt  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  C2D. commands. uitgangs. Abandon. geslaagd  |  C2D-berichten zijn afgebroken  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  C2D. commands. OUTuitgang. complete. geslaagd  |  C2D-bericht leveringen voltooid  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  C2D. commands. uitgangs. reject. geslaagd  |  Geweigerde C2D-berichten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  C2D. methods. failure  |  Mislukte directe aanroepen van methode  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  C2D. methods. requestSize  |  Aanvraag grootte van directe-methode aanroepen  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  C2D. methods. responseSize  |  Antwoord grootte van directe methode aanroepen  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  C2D. methods. geslaagd  |  Geslaagde directe aanroepen van de methode  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  C2D. dubbele. Read. failure  |  Mislukte dubbele Lees bewerkingen van back-end  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  C2D. dubbele. Lees. grootte  |  Reactie grootte van dubbele Lees bewerkingen van de back-end  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  C2D. dubbele. lezen. geslaagd  |  Geslaagde dubbele Lees bewerkingen van back-end  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  C2D. dubbele. update. failure  |  Mislukte dubbele updates van back-end  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  C2D. dubbele. update. grootte  |  Grootte van dubbele updates van back-end  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  C2D. dubbele. update. geslaagd  |  Geslaagde dubbele updates van back-end  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  C2DMessagesExpired  |  C2D-berichten verlopen (preview-versie)  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  configuraties  |  Metrische configuratie gegevens  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. devices/IotHubs  |  connectedDeviceCount  |  Verbonden apparaten (preview-versie)  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  D2C. endpoints. uitgangs punt. builtIn. Events  |  Route ring: berichten worden bezorgd bij berichten/gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  D2C. endpoints. uitgangs. Event hubs  |  Route ring: berichten worden bezorgd bij Event hub  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  D2C. endpoints. uitgangs. serviceBusQueues  |  Route ring: berichten worden bezorgd bij Service Bus wachtrij  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  D2C. endpoints. uitgangs. serviceBusTopics  |  Route ring: berichten die worden bezorgd bij Service Bus onderwerp  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  D2C. endpoints. outwaarde. Storage  |  Route ring: berichten worden bezorgd bij de opslag  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  D2C. endpoints. outwaar. storage. blobs  |  Route ring: blobs die aan de opslag worden geleverd  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  D2C. endpoints. out. storage. bytes  |  Route ring: gegevens worden geleverd aan de opslag  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  D2C. endpoints. latentie. builtIn. Events  |  Route ring: bericht latentie voor berichten/gebeurtenissen  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  D2C. endpoints. latentie. Event hubs  |  Route ring: bericht latentie voor Event hub  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  D2C. endpoints. latentie. serviceBusQueues  |  Route ring: bericht latentie voor Service Bus wachtrij  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  D2C. endpoints. latentie. serviceBusTopics  |  Route ring: bericht latentie voor Service Bus onderwerp  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  D2C. endpoints. latentie. opslag  |  Route ring: bericht latentie voor opslag  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  D2C. telemetrie. uitgangs. verwijderd  |  Route ring: telemetrie-berichten verwijderd   |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  D2C. telemetrie.. fallback  |  Route ring: berichten worden bezorgd bij terugval  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  D2C. telemetrie. uitgangs. ongeldig  |  Route ring: telemetrie-berichten incompatibel  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  D2C. telemetrie.. zwevend  |  Route ring: telemetriegegevens van zwevende berichten   |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  D2C. telemetrie. uitgangs. geslaagd  |  Route ring: telemetrie-berichten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  D2C. telemetrie. ingress. allProtocol  |  Verzend pogingen voor telemetrie-berichten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  D2C. telemetrie. ingress. sendThrottle  |  Aantal beperkings fouten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  D2C. telemetrie. ingress. geslaagd  |  Verzonden telemetriegegevens  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  D2C. dubbele. Read. failure  |  Mislukte dubbele Lees bewerkingen van apparaten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  D2C. dubbele. Lees. grootte  |  Reactie grootte van dubbele Lees bewerkingen van apparaten  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  D2C. dubbele. lezen. geslaagd  |  Geslaagde dubbele Lees bewerkingen van apparaten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  D2C. dubbele. update. failure  |  Mislukte dubbele updates van apparaten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  D2C. dubbele. update. grootte  |  Grootte van dubbele updates van apparaten  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  D2C. dubbele. update. geslaagd  |  Geslaagde dubbele updates van apparaten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  dailyMessageQuotaUsed  |  Totaal aantal gebruikte berichten  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  deviceDataUsage  |  Totale hoeveelheid gegevens gebruik van apparaat  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  deviceDataUsageV2  |  Totaal gebruik van apparaatgegevens (preview-versie)  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  apparaten. connectedDevices. allProtocol  |  Verbonden apparaten (afgeschaft)   |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  apparaten. totalDevices  |  Totaal aantal apparaten (afgeschaft)  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  EventGridDeliveries  |  Event Grid leveringen (preview-versie)  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  EventGridLatency  |  Event Grid latentie (preview-versie)  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  Jobs. cancelJob. failure  |  Mislukte taak annuleringen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  Jobs. cancelJob. geslaagd  |  Voltooide taak annuleringen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  Jobs. voltooid  |  Voltooide taken  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  Jobs. createDirectMethodJob. failure  |  Kan geen aanroepen van methode aanroep taken uitvoeren  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  Jobs. createDirectMethodJob. geslaagd  |  Geslaagde creatie van methode aanroep taken  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  Jobs. createTwinUpdateJob. failure  |  Kan geen dubbele update taken uitvoeren  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  Jobs. createTwinUpdateJob. geslaagd  |  Geslaagde creatie van dubbele update taken  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  Jobs. mislukt  |  Mislukte taken  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  Jobs. listJobs. failure  |  Mislukte aanroepen naar lijst taken  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  Jobs. listJobs. geslaagd  |  Geslaagde aanroepen naar lijst taken  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  Jobs. queryJobs. failure  |  Mislukte taak query's  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  Jobs. queryJobs. geslaagd  |  Geslaagde taak query's  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. devices/IotHubs  |  totalDeviceCount  |  Totaal aantal apparaten (preview-versie)  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  twinQueries. failure  |  Mislukte dubbele query's  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  twinQueries.resultSize  |  Resultaat grootte van dubbele query's  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Micro soft. devices/IotHubs  |  twinQueries. geslaagd  |  Geslaagde dubbele query's  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/provisioningServices  |  AttestationAttempts  |  Attestation-pogingen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/provisioningServices  |  DeviceAssignments  |  Apparaten toegewezen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. devices/provisioningServices  |  RegistrationAttempts  |  Registratie pogingen  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  AvailableStorage  |  Beschikbare opslag  |  Bytes  |  Totaal | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  CassandraConnectionClosures  |  Cassandra-verbinding sluiten  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequestCharges  |  Kosten voor Cassandra-aanvragen  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  CassandraRequests  |  Cassandra aanvragen  |  Aantal  |  Aantal | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  DataUsage  |  Gegevensgebruik  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  DeleteVirtualNetwork  |  DeleteVirtualNetwork  |  Aantal  |  Aantal | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  DocumentCount  |  Aantal documenten  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  DocumentQuota  |  Document quotum  |  Bytes  |  Totaal | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  IndexUsage  |  Indexgebruik  |  Bytes  |  Totaal | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  MetadataRequests  |  Meta gegevens aanvragen  |  Aantal  |  Aantal | 
| **Ja**  | **Ja** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestCharge  |  Kosten voor Mongo-aanvragen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequests  |  Mongo aanvragen  |  Aantal  |  Aantal | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsCount  |  Frequentie van Mongo-aanvragen  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsDelete  |  Aantal Mongo-aanvragen voor verwijderen  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsInsert  |  Aantal Mongo invoegen  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsQuery  |  Frequentie van Mongo-query aanvragen  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  MongoRequestsUpdate  |  Frequentie van Mongo-update aanvragen  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  ProvisionedThroughput  |  Ingerichte doorvoer  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  ReplicationLatency  |  P99-replicatie latentie  |  Milliseconden  |  Average | 
| Nee  | Nee |  Microsoft.DocumentDB/databaseAccounts  |  ServiceAvailability  |  Service beschikbaarheid  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequests  |  Totaal aantal aanvragen  |  Aantal  |  Aantal | 
| **Ja**  | **Ja** |  Microsoft.DocumentDB/databaseAccounts  |  TotalRequestUnits  |  Totaal aantal aanvraag eenheden  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. EnterpriseKnowledgeGraph/Services  |  FailureCount  |  Aantal mislukte pogingen  |  Aantal  |  Aantal | 
| Nee  | Nee |  Micro soft. EnterpriseKnowledgeGraph/Services  |  SuccessCount  |  Aantal geslaagde pogingen  |  Aantal  |  Aantal | 
| Nee  | Nee |  Micro soft. EnterpriseKnowledgeGraph/Services  |  SuccessLatency  |  Geslaagde latentie  |  Milliseconden  |  Average | 
| Nee  | Nee |  Micro soft. EnterpriseKnowledgeGraph/Services  |  TransactionCount  |  Aantal trans acties  |  Aantal  |  Aantal | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/domeinen  |  DeadLetteredCount  |  Gebeurtenissen met onbestelbare berichten  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. EventGrid/domeinen  |  DeliveryAttemptFailCount  |  Mislukte leverings gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/domeinen  |  DeliverySuccessCount  |  Geleverde gebeurtenissen  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. EventGrid/domeinen  |  DestinationProcessingDurationInMs  |  Doel verwerkings duur  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/domeinen  |  DroppedEventCount  |  Verwijderde gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/domeinen  |  MatchedEventCount  |  Overeenkomende gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/domeinen  |  PublishFailCount  |  Mislukte gebeurtenissen publiceren  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/domeinen  |  PublishSuccessCount  |  Gepubliceerde gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/domeinen  |  PublishSuccessLatencyInMs  |  Latentie van publicatie geslaagd  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/eventSubscriptions  |  DeadLetteredCount  |  Gebeurtenissen met onbestelbare berichten  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. EventGrid/eventSubscriptions  |  DeliveryAttemptFailCount  |  Mislukte leverings gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/eventSubscriptions  |  DeliverySuccessCount  |  Geleverde gebeurtenissen  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. EventGrid/eventSubscriptions  |  DestinationProcessingDurationInMs  |  Doel verwerkings duur  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/eventSubscriptions  |  DroppedEventCount  |  Verwijderde gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/eventSubscriptions  |  MatchedEventCount  |  Overeenkomende gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/extensionTopics  |  PublishFailCount  |  Mislukte gebeurtenissen publiceren  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/extensionTopics  |  PublishSuccessCount  |  Gepubliceerde gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/extensionTopics  |  PublishSuccessLatencyInMs  |  Latentie van publicatie geslaagd  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/extensionTopics  |  UnmatchedEventCount  |  Niet-overeenkomende gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/topics  |  PublishFailCount  |  Mislukte gebeurtenissen publiceren  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/topics  |  PublishSuccessCount  |  Gepubliceerde gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/topics  |  PublishSuccessLatencyInMs  |  Latentie van publicatie geslaagd  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventGrid/topics  |  UnmatchedEventCount  |  Niet-overeenkomende gebeurtenissen  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. EventHub/clusters  |  ActiveConnections  |  ActiveConnections  |  Aantal  |  Average | 
| Nee  | Nee |  Micro soft. EventHub/clusters  |  AvailableMemory  |  Beschikbaar geheugen  |  Percentage  |  Maximum | 
| Nee  | Nee |  Micro soft. EventHub/clusters  |  CaptureBacklog  |  Achterstand vastleggen.  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. EventHub/clusters  |  CapturedBytes  |  Vastgelegde bytes.  |  Bytes  |  Totaal | 
| Nee  | Nee |  Micro soft. EventHub/clusters  |  CapturedMessages  |  Vastgelegde berichten.  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. EventHub/clusters  |  ConnectionsClosed  |  Gesloten verbindingen.  |  Aantal  |  Average | 
| Nee  | Nee |  Micro soft. EventHub/clusters  |  ConnectionsOpened  |  Geopende verbindingen.  |  Aantal  |  Average | 
| Nee  | Nee |  Micro soft. EventHub/clusters  |  CPU  |  CPU  |  Percentage  |  Maximum | 
| **Ja**  | **Ja** |  Micro soft. EventHub/clusters  |  IncomingBytes  |  Binnenkomende bytes.  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventHub/clusters  |  IncomingMessages  |  Binnenkomende berichten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventHub/clusters  |  IncomingRequests  |  Binnenkomende aanvragen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventHub/clusters  |  OutgoingBytes  |  Uitgaande bytes.  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. EventHub/clusters  |  OutgoingMessages  |  Uitgaande berichten  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. EventHub/clusters  |  QuotaExceededErrors  |  Fouten met overschreden quota.  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. EventHub/clusters  |  ServerErrors  |  Serverfouten.  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. EventHub/clusters  |  SuccessfulRequests  |  Geslaagde aanvragen  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. EventHub/clusters  |  ThrottledRequests  |  Beperkte aanvragen.  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. EventHub/clusters  |  UserErrors  |  Gebruikersfouten.  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/namespaces  |  ActiveConnections  |  ActiveConnections  |  Aantal  |  Average | 
| Nee  | Nee |  Microsoft.EventHub/namespaces  |  CaptureBacklog  |  Achterstand vastleggen.  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/namespaces  |  CapturedBytes  |  Vastgelegde bytes.  |  Bytes  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/namespaces  |  CapturedMessages  |  Vastgelegde berichten.  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/namespaces  |  ConnectionsClosed  |  Gesloten verbindingen.  |  Aantal  |  Average | 
| Nee  | Nee |  Microsoft.EventHub/namespaces  |  ConnectionsOpened  |  Geopende verbindingen.  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  EHABL  |  Achterstallige berichten archiveren (afgeschaft)  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  EHAMBS  |  Berichten doorvoer archiveren (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  EHAMSGS  |  Berichten archiveren (afgeschaft)  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  EHINBYTES  |  Binnenkomende bytes (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  EHINMBS  |  Binnenkomende bytes (verouderd) (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  EHINMSGS  |  Inkomende berichten (afgeschaft)  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  EHOUTBYTES  |  Uitgaande bytes (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  EHOUTMBS  |  Uitgaande bytes (verouderd) (afgeschaft)  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  EHOUTMSGS  |  Uitgaande berichten (afgeschaft)  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  FAILREQ  |  Mislukte aanvragen (afgeschaft)  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  IncomingBytes  |  Binnenkomende bytes.  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  IncomingMessages  |  Binnenkomende berichten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  IncomingRequests  |  Binnenkomende aanvragen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  INMSGS  |  Binnenkomende berichten (verouderd) (afgeschaft)  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  INREQS  |  Binnenkomende aanvragen (afgeschaft)  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  INTERer  |  Interne server fouten (afgeschaft)  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  MISCERR  |  Andere fouten (afgeschaft)  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  OutgoingBytes  |  Uitgaande bytes.  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  OutgoingMessages  |  Uitgaande berichten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  OUTMSGS  |  Uitgaande berichten (verouderd) (afgeschaft)  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/namespaces  |  QuotaExceededErrors  |  Fouten met overschreden quota.  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/namespaces  |  ServerErrors  |  Serverfouten.  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/namespaces  |  Grootte  |  Grootte  |  Bytes  |  Average | 
| Nee  | Nee |  Microsoft.EventHub/namespaces  |  SuccessfulRequests  |  Geslaagde aanvragen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  SUCCREQ  |  Geslaagde aanvragen (afgeschaft)  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.EventHub/namespaces  |  SVRBSY  |  Fouten bij server bezet (afgeschaft)  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/namespaces  |  ThrottledRequests  |  Beperkte aanvragen.  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.EventHub/namespaces  |  UserErrors  |  Gebruikersfouten.  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. HDInsight/clusters  |  CategorizedGatewayRequests  |  Gecategoriseerde gateway aanvragen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. HDInsight/clusters  |  GatewayRequests  |  Gateway aanvragen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. HDInsight/clusters  |  NumActiveWorkers  |  Aantal actieve werk rollen  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. HDInsight/clusters  |  ScalingRequests  |  Aanvragen schalen  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. Insights/AutoscaleSettings  |  MetricThreshold  |  Drempel waarde voor metrische gegevens  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Insights/AutoscaleSettings  |  ObservedCapacity  |  Waargenomen capaciteit  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Insights/AutoscaleSettings  |  ObservedMetricValue  |  Waargenomen metrische waarde  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Insights/AutoscaleSettings  |  ScaleActionsInitiated  |  Schaal acties gestart  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Insights/onderdelen  |  availabilityResults/availabilityPercentage  |  Beschikbaarheid  |  Percentage  |  Average | 
| Nee  | Nee |  Micro soft. Insights/onderdelen  |  availabilityResults/aantal  |  Beschikbaarheidstests  |  Aantal  |  Aantal | 
| **Ja**  | Nee |  Micro soft. Insights/onderdelen  |  availabilityResults/duur  |  Duur beschikbaarheids test  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. Insights/onderdelen  |  browserTimings/networkDuration  |  Netwerk verbindings tijd voor laden van pagina  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. Insights/onderdelen  |  browserTimings/processingDuration  |  Verwerkings tijd van client  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. Insights/onderdelen  |  browserTimings/receiveDuration  |  Reactie tijd van ontvangst  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. Insights/onderdelen  |  browserTimings/sendDuration  |  Aanvraag tijd verzenden  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. Insights/onderdelen  |  browserTimings/totalDuration  |  Laad tijd van browser pagina  |  Milliseconden  |  Average | 
| Nee  | Nee |  Micro soft. Insights/onderdelen  |  afhankelijkheden/aantal  |  Afhankelijkheids aanroepen  |  Aantal  |  Aantal | 
| **Ja**  | Nee |  Micro soft. Insights/onderdelen  |  afhankelijkheden/duur  |  Duur van afhankelijkheid  |  Milliseconden  |  Average | 
| Nee  | Nee |  Micro soft. Insights/onderdelen  |  afhankelijkheden/mislukt  |  Mislukte afhankelijkheids aanroepen  |  Aantal  |  Aantal | 
| Nee  | Nee |  Micro soft. Insights/onderdelen  |  uitzonde ringen/browser  |  Browseruitzonderingen  |  Aantal  |  Aantal | 
| **Ja**  | **Ja** |  Micro soft. Insights/onderdelen  |  uitzonde ringen/aantal  |  Uitzonderingen  |  Aantal  |  Aantal | 
| Nee  | Nee |  Micro soft. Insights/onderdelen  |  uitzonde ringen/server  |  Server uitzonderingen  |  Aantal  |  Aantal | 
| **Ja**  | **Ja** |  Micro soft. Insights/onderdelen  |  Page views/aantal  |  Pagina weergaven  |  Aantal  |  Aantal | 
| **Ja**  | Nee |  Micro soft. Insights/onderdelen  |  Page views/duur  |  Laad tijd pagina weergave  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Insights/onderdelen  |  Performance Counters/exceptionsPerSecond  |  Uitzonderings frequentie  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Insights/onderdelen  |  Performance Counters/memoryAvailableBytes  |  Beschikbaar geheugen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. Insights/onderdelen  |  Performance Counters/processCpuPercentage  |  CPU verwerken  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. Insights/onderdelen  |  Performance Counters/processIOBytesPerSecond  |  I/o-frequentie van processen  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Insights/onderdelen  |  Performance Counters/processorCpuPercentage  |  Processor tijd  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. Insights/onderdelen  |  Performance Counters/processPrivateBytes  |  Privé-bytes verwerken  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. Insights/onderdelen  |  Performance Counters/requestExecutionTime  |  Uitvoerings tijd van de HTTP-aanvraag  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Insights/onderdelen  |  Performance Counters/requestsInQueue  |  HTTP-aanvragen in de toepassings wachtrij  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Insights/onderdelen  |  Performance Counters/requestsPerSecond  |  Frequentie van HTTP-aanvragen  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Micro soft. Insights/onderdelen  |  aanvragen/aantal  |  Server aanvragen  |  Aantal  |  Aantal | 
| **Ja**  | Nee |  Micro soft. Insights/onderdelen  |  aanvragen/duur  |  Server reactietijd  |  Milliseconden  |  Average | 
| Nee  | Nee |  Micro soft. Insights/onderdelen  |  aanvragen/mislukt  |  Mislukte aanvragen  |  Aantal  |  Aantal | 
| Nee  | Nee |  Micro soft. Insights/onderdelen  |  aanvragen/frequentie  |  Aantal server aanvragen  |  CountPerSecond  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Insights/onderdelen  |  traceringen/aantal  |  Traceringen  |  Aantal  |  Aantal | 
| **Ja**  | Nee |  Micro soft.-sleutel kluis/-kluizen  |  ServiceApiHit  |  Totaal aantal treffers in de service-API  |  Aantal  |  Aantal | 
| **Ja**  | Nee |  Micro soft.-sleutel kluis/-kluizen  |  ServiceApiLatency  |  Algehele latentie van Service-API  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft.-sleutel kluis/-kluizen  |  ServiceApiResult  |  Totale resultaten van Service-API  |  Aantal  |  Aantal | 
| **Ja**  | **Ja** |  Micro soft. Kusto/clusters  |  CacheUtilization  |  Cache gebruik  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Kusto/clusters  |  ContinuousExportMaxLatenessMinutes  |  Maximale achterstand voor continue export  |  Aantal  |  Maximum | 
| **Ja**  | **Ja** |  Micro soft. Kusto/clusters  |  ContinuousExportNumOfRecordsExported  |  Doorlopend exporteren: aantal geëxporteerde records  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Kusto/clusters  |  ContinuousExportPendingCount  |  Aantal doorlopend exporteren in behandeling  |  Aantal  |  Maximum | 
| **Ja**  | **Ja** |  Micro soft. Kusto/clusters  |  ContinuousExportResult  |  Resultaat doorlopend exporteren  |  Aantal  |  Aantal | 
| **Ja**  | **Ja** |  Micro soft. Kusto/clusters  |  CPU  |  CPU  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Kusto/clusters  |  EventsProcessedForEventHubs  |  Verwerkte gebeurtenissen (voor gebeurtenis/IoT-hubs)  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Kusto/clusters  |  ExportUtilization  |  Exportgebruik  |  Percentage  |  Maximum | 
| **Ja**  | **Ja** |  Micro soft. Kusto/clusters  |  IngestionLatencyInSeconds  |  Opname latentie (in seconden)  |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Kusto/clusters  |  IngestionResult  |  Opname resultaat  |  Aantal  |  Aantal | 
| **Ja**  | **Ja** |  Micro soft. Kusto/clusters  |  IngestionUtilization  |  Opname gebruik  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Kusto/clusters  |  IngestionVolumeInMB  |  Opname volume (in MB)  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Kusto/clusters  |  KeepAlive  |  Actief houden  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Kusto/clusters  |  QueryDuration  |  Query duur  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Kusto/clusters  |  SteamingIngestRequestRate  |  Aanvraag frequentie voor streaming-opname  |  Aantal  |  RateRequestsPerSecond | 
| **Ja**  | **Ja** |  Micro soft. Kusto/clusters  |  StreamingIngestDataRate  |  Gegevens frequentie van streaming opname  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Kusto/clusters  |  StreamingIngestDuration  |  Opname duur van streaming  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Kusto/clusters  |  StreamingIngestResults  |  Resultaat van streaming-opname  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Logic/integrationServiceEnvironments  |  ActionLatency  |  Actie latentie   |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Logic/integrationServiceEnvironments  |  ActionsCompleted  |  Acties voltooid   |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/integrationServiceEnvironments  |  ActionsFailed  |  Mislukte acties   |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/integrationServiceEnvironments  |  ActionsSkipped  |  Overgeslagen acties   |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/integrationServiceEnvironments  |  ActionsStarted  |  Gestarte acties   |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/integrationServiceEnvironments  |  ActionsSucceeded  |  Acties geslaagd   |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Logic/integrationServiceEnvironments  |  ActionSuccessLatency  |  Latentie geslaagde acties   |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Logic/integrationServiceEnvironments  |  ActionThrottledEvents  |  Door actie vertraagde gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorMemoryUsage  |  Geheugen gebruik van connector voor Integratieserviceomgeving  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentConnectorProcessorUsage  |  Processor gebruik van connector voor Integratieserviceomgeving  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentWorkflowMemoryUsage  |  Geheugen gebruik van werk stroom voor Integratieserviceomgeving  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. Logic/integrationServiceEnvironments  |  IntegrationServiceEnvironmentWorkflowProcessorUsage  |  Gebruik van werk stroom processor voor Integratieserviceomgeving  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Logic/integrationServiceEnvironments  |  RunFailurePercentage  |  Percentage mislukte uitvoeringen  |  Percentage  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Logic/integrationServiceEnvironments  |  RunLatency  |  Uitvoerings latentie  |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Logic/integrationServiceEnvironments  |  RunsCancelled  |  Uitvoeringen geannuleerd  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/integrationServiceEnvironments  |  RunsCompleted  |  Uitvoeringen voltooid  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/integrationServiceEnvironments  |  RunsFailed  |  Uitvoeringen mislukt  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/integrationServiceEnvironments  |  RunsStarted  |  Uitvoeringen gestart  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/integrationServiceEnvironments  |  RunsSucceeded  |  Geslaagde uitvoeringen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/integrationServiceEnvironments  |  RunStartThrottledEvents  |  Vertraagde gebeurtenissen uitvoeren  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Logic/integrationServiceEnvironments  |  RunSuccessLatency  |  Latentie van geslaagde uitvoering  |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Logic/integrationServiceEnvironments  |  RunThrottledEvents  |  Vertraagde gebeurtenissen uitvoeren  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Logic/integrationServiceEnvironments  |  TriggerFireLatency  |  Brand latentie activeren   |  Seconden  |  Average | 
| **Ja**  | Nee |  Micro soft. Logic/integrationServiceEnvironments  |  TriggerLatency  |  Latentie van trigger   |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Logic/integrationServiceEnvironments  |  TriggersCompleted  |  Triggers voltooid   |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/integrationServiceEnvironments  |  TriggersFailed  |  Mislukte triggers   |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/integrationServiceEnvironments  |  TriggersFired  |  Geactiveerde triggers   |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/integrationServiceEnvironments  |  TriggersSkipped  |  Triggers overgeslagen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/integrationServiceEnvironments  |  TriggersStarted  |  Triggers gestart   |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/integrationServiceEnvironments  |  TriggersSucceeded  |  Geslaagde triggers   |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Logic/integrationServiceEnvironments  |  TriggerSuccessLatency  |  Latentie van trigger geslaagd   |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Logic/integrationServiceEnvironments  |  TriggerThrottledEvents  |  Trigger beperkings gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Logic/werk stromen  |  ActionLatency  |  Actie latentie   |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  ActionsCompleted  |  Acties voltooid   |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  ActionsFailed  |  Mislukte acties   |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  ActionsSkipped  |  Overgeslagen acties   |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  ActionsStarted  |  Gestarte acties   |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  ActionsSucceeded  |  Acties geslaagd   |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Logic/werk stromen  |  ActionSuccessLatency  |  Latentie geslaagde acties   |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  ActionThrottledEvents  |  Door actie vertraagde gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  BillableActionExecutions  |  Factureer bare actie-uitvoeringen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  BillableTriggerExecutions  |  Factureer bare trigger uitvoeringen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  BillingUsageNativeOperation  |  Facturerings gebruik voor uitvoering van systeem eigen bewerkingen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  BillingUsageNativeOperation  |  Facturerings gebruik voor uitvoering van systeem eigen bewerkingen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  BillingUsageStandardConnector  |  Facturerings gebruik voor het uitvoeren van standaard-connectors  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  BillingUsageStandardConnector  |  Facturerings gebruik voor het uitvoeren van standaard-connectors  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  BillingUsageStorageConsumption  |  Facturerings gebruik voor uitvoeringen van opslag verbruik  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  BillingUsageStorageConsumption  |  Facturerings gebruik voor uitvoeringen van opslag verbruik  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  RunFailurePercentage  |  Percentage mislukte uitvoeringen  |  Percentage  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Logic/werk stromen  |  RunLatency  |  Uitvoerings latentie  |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  RunsCancelled  |  Uitvoeringen geannuleerd  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  RunsCompleted  |  Uitvoeringen voltooid  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  RunsFailed  |  Uitvoeringen mislukt  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  RunsStarted  |  Uitvoeringen gestart  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  RunsSucceeded  |  Geslaagde uitvoeringen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  RunStartThrottledEvents  |  Vertraagde gebeurtenissen uitvoeren  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Logic/werk stromen  |  RunSuccessLatency  |  Latentie van geslaagde uitvoering  |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  RunThrottledEvents  |  Vertraagde gebeurtenissen uitvoeren  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  TotalBillableExecutions  |  Totaal aantal factureer bare uitvoeringen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Logic/werk stromen  |  TriggerFireLatency  |  Brand latentie activeren   |  Seconden  |  Average | 
| **Ja**  | Nee |  Micro soft. Logic/werk stromen  |  TriggerLatency  |  Latentie van trigger   |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  TriggersCompleted  |  Triggers voltooid   |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  TriggersFailed  |  Mislukte triggers   |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  TriggersFired  |  Geactiveerde triggers   |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  TriggersSkipped  |  Triggers overgeslagen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  TriggersStarted  |  Triggers gestart   |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  TriggersSucceeded  |  Geslaagde triggers   |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Logic/werk stromen  |  TriggerSuccessLatency  |  Latentie van trigger geslaagd   |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Logic/werk stromen  |  TriggerThrottledEvents  |  Trigger beperkings gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. MachineLearningServices/werk ruimten  |  Actieve kernen  |  Actieve kernen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. MachineLearningServices/werk ruimten  |  Actieve knoop punten  |  Actieve knoop punten  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. MachineLearningServices/werk ruimten  |  Voltooide uitvoeringen  |  Voltooide uitvoeringen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. MachineLearningServices/werk ruimten  |  Mislukte uitvoeringen  |  Mislukte uitvoeringen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. MachineLearningServices/werk ruimten  |  Niet-actieve kernen  |  Niet-actieve kernen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. MachineLearningServices/werk ruimten  |  Niet-actieve knoop punten  |  Niet-actieve knoop punten  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. MachineLearningServices/werk ruimten  |  Kernen verlaten  |  Kernen verlaten  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. MachineLearningServices/werk ruimten  |  Knoop punten verlaten  |  Knoop punten verlaten  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. MachineLearningServices/werk ruimten  |  Modelimplementatie is mislukt  |  Modelimplementatie is mislukt  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. MachineLearningServices/werk ruimten  |  Modelimplementatie gestart  |  Modelimplementatie gestart  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. MachineLearningServices/werk ruimten  |  Modelimplementatie geslaagd  |  Modelimplementatie geslaagd  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. MachineLearningServices/werk ruimten  |  Model register mislukt  |  Model register mislukt  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. MachineLearningServices/werk ruimten  |  Model registratie is voltooid  |  Model registratie is voltooid  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. MachineLearningServices/werk ruimten  |  Afgebroken kernen  |  Afgebroken kernen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. MachineLearningServices/werk ruimten  |  Knoop punten die zijn afgebroken  |  Knoop punten die zijn afgebroken  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. MachineLearningServices/werk ruimten  |  Percentage quotum gebruik  |  Percentage quotum gebruik  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. MachineLearningServices/werk ruimten  |  Gestart uitvoeringen  |  Gestart uitvoeringen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. MachineLearningServices/werk ruimten  |  Totaal aantal kernen  |  Totaal aantal kernen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. MachineLearningServices/werk ruimten  |  Totaal aantal knoop punten  |  Totaal aantal knoop punten  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. MachineLearningServices/werk ruimten  |  Onbruikbaar aantal kern geheugens  |  Onbruikbaar aantal kern geheugens  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. MachineLearningServices/werk ruimten  |  Niet-bruikbare knoop punten  |  Niet-bruikbare knoop punten  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Maps/accounts  |  Beschikbaarheid  |  Beschikbaarheid  |  Percentage  |  Average | 
| Nee  | Nee |  Micro soft. Maps/accounts  |  Gebruik  |  Gebruik  |  Aantal  |  Aantal | 
| **Ja**  | Nee |  Micro soft. Media/Media Services  |  AssetCount  |  Aantal assets  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Media/Media Services  |  AssetQuota  |  Activa quotum  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Media/Media Services  |  AssetQuotaUsedPercentage  |  Percentage gebruikt voor het activa quotum  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. Media/Media Services  |  ContentKeyPolicyCount  |  Aantal beleids regels voor inhouds sleutels  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Media/Media Services  |  ContentKeyPolicyQuota  |  Quotum voor inhouds sleutel beleid  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Media/Media Services  |  ContentKeyPolicyQuotaUsedPercentage  |  Percentage gebruikt quotum voor inhouds sleutel beleid  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. Media/Media Services  |  StreamingPolicyCount  |  Aantal stroomsgewijze beleids regels  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Media/Media Services  |  StreamingPolicyQuota  |  Quota voor streaming-beleid  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Media/Media Services  |  StreamingPolicyQuotaUsedPercentage  |  Percentage gebruikt quotum voor het streaming-beleid  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. Media/Media Services/streamingEndpoints  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Media/Media Services/streamingEndpoints  |  Aanvragen  |  Aanvragen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Media/Media Services/streamingEndpoints  |  SuccessE2ELatency  |  Geslaagde end-to-end-latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  GCPauseTotalCount  |  Aantal GC-onderbrekingen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  GCPauseTotalTime  |  Totale tijd van de GC-onderbreking  |  Milliseconden  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  MaxOldGenMemoryPoolBytes  |  Maxi maal beschik bare oude generatie gegevens grootte  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  OldGenMemoryPoolBytes  |  Gegevens grootte van oude generatie  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  OldGenPromotedBytes  |  Promo veren tot oude generatie gegevens grootte  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  ServiceCpuUsagePercentage  |  CPU-gebruiks percentage van service  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  ServiceMemoryCommitted  |  Toegewezen service geheugen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  ServiceMemoryMax  |  Maxi maal service geheugen  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  ServiceMemoryUsed  |  Gebruikt service geheugen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  SystemCpuUsagePercentage  |  Percentage van het CPU-gebruik van het systeem  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  TomcatErrorCount  |  Tomcat Global-fout  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  TomcatReceivedBytes  |  Totaal aantal bytes ontvangen Tomcat  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  TomcatRequestMaxTime  |  Maximale tijd voor tomcat-aanvraag  |  Milliseconden  |  Maximum | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  TomcatRequestTotalCount  |  Totaal aantal Tomcat-aanvragen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  TomcatRequestTotalTime  |  Totaal aantal keer Tomcat-aanvragen  |  Milliseconden  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  TomcatResponseAvgTime  |  Gemiddelde tijd Tomcat-aanvraag  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  TomcatSentBytes  |  Totaal aantal verzonden bytes in Tomcat  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  TomcatSessionActiveCurrentCount  |  Aantal Tomcat-sessies  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  TomcatSessionActiveMaxCount  |  Aantal actieve Tomcat-sessies  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  TomcatSessionAliveMaxTime  |  Time-outperiode van Tomcat-sessie  |  Milliseconden  |  Maximum | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  TomcatSessionCreatedCount  |  Aantal gemaakte Tomcat-sessies  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  TomcatSessionExpiredCount  |  Aantal verlopen Tomcat-sessies  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  TomcatSessionRejectedCount  |  Aantal geweigerde Tomcat-sessies  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Microservices4Spring/appClusters  |  YoungGenPromotedBytes  |  Promo veren tot jonge generatie gegevens grootte  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Micro soft. NetApp/netAppAccounts/capacityPools  |  VolumePoolAllocatedUsed  |  Gebruikte volume groep  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. NetApp/netAppAccounts/capacityPools  |  VolumePoolTotalLogicalSize  |  Totale logische grootte van volume groep  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. NetApp/netAppAccounts/capacityPools/volumes  |  AverageReadLatency  |  Gemiddelde lees latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. NetApp/netAppAccounts/capacityPools/volumes  |  AverageWriteLatency  |  Gemiddelde schrijf latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. NetApp/netAppAccounts/capacityPools/volumes  |  ReadIops  |  IOPS lezen  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. NetApp/netAppAccounts/capacityPools/volumes  |  VolumeLogicalSize  |  Logische volume grootte  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. NetApp/netAppAccounts/capacityPools/volumes  |  VolumeSnapshotSize  |  Grootte van moment opname van volume  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. NetApp/netAppAccounts/capacityPools/volumes  |  WriteIops  |  IOPS schrijven  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Micro soft. Network/applicationGateways  |  ApplicationGatewayTotalTime  |  Totale tijd van Application Gateway  |  Milliseconden  |  Average | 
| Nee  | Nee |  Micro soft. Network/applicationGateways  |  AvgRequestCountPerHealthyHost  |  Aanvragen per minuut per gegezonde host  |  Aantal  |  Average | 
| Nee  | Nee |  Micro soft. Network/applicationGateways  |  BackendConnectTime  |  Moment back-end verbinding  |  Milliseconden  |  Average | 
| Nee  | Nee |  Micro soft. Network/applicationGateways  |  BackendFirstByteResponseTime  |  Reactie tijd eerste byte van back-end  |  Milliseconden  |  Average | 
| Nee  | Nee |  Micro soft. Network/applicationGateways  |  BackendLastByteResponseTime  |  Reactie tijd laatste byte van back-end  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/applicationGateways  |  BackendResponseStatus  |  Reactie status van back-end  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Network/applicationGateways  |  BlockedCount  |  Regel distributie voor door Web Application firewall geblokkeerde aanvragen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Network/applicationGateways  |  BlockedReqCount  |  Aantal geblokkeerde aanvragen voor Web Application firewall  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Network/applicationGateways  |  BytesReceived  |  Ontvangen bytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Network/applicationGateways  |  Bytes sent  |  Verzonden bytes  |  Bytes  |  Totaal | 
| Nee  | Nee |  Micro soft. Network/applicationGateways  |  CapacityUnits  |  Huidige capaciteits eenheden  |  Aantal  |  Average | 
| Nee  | Nee |  Micro soft. Network/applicationGateways  |  ClientRtt  |  Client RTT  |  Milliseconden  |  Average | 
| Nee  | Nee |  Micro soft. Network/applicationGateways  |  ComputeUnits  |  Huidige reken eenheden  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/applicationGateways  |  CurrentConnections  |  Huidige verbindingen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Network/applicationGateways  |  FailedRequests  |  Mislukte aanvragen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Network/applicationGateways  |  HealthyHostCount  |  Aantal goede hosts  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/applicationGateways  |  MatchedCount  |  Totale regel distributie Web Application firewall  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Network/applicationGateways  |  ResponseStatus  |  Reactie status  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. Network/applicationGateways  |  Doorvoer  |  Doorvoer  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/applicationGateways  |  TlsProtocol  |  TLS-protocol van client  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Network/applicationGateways  |  TotalRequests  |  Totaal aantal aanvragen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Network/applicationGateways  |  UnhealthyHostCount  |  Aantal hosts met slechte status  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/azurefirewalls  |  ApplicationRuleHit  |  Aantal treffers toepassings regels  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Network/azurefirewalls  |  DataProcessed  |  Verwerkte gegevens  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Network/azurefirewalls  |  FirewallHealth  |  Status van Firewall  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/azurefirewalls  |  NetworkRuleHit  |  Aantal treffers in netwerk regels  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Network/azurefirewalls  |  SNATPortUtilization  |  Gebruik van SNAT-poort  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/Connections  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/Connections  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/dnszones  |  QueryVolume  |  Query volume  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. Network/dnszones  |  RecordSetCapacityUtilization  |  Capaciteits gebruik van record sets  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Micro soft. Network/dnszones  |  RecordSetCount  |  Aantal record sets  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. Network/expressRouteCircuits  |  ArpAvailability  |  ARP-Beschik baarheid  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/expressRouteCircuits  |  BgpAvailability  |  BGP-Beschik baarheid  |  Percentage  |  Average | 
| Nee  | Nee |  Micro soft. Network/expressRouteCircuits  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Micro soft. Network/expressRouteCircuits  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Micro soft. Network/expressRouteCircuits  |  GlobalReachBitsInPerSecond  |  GlobalReachBitsInPerSecond  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Micro soft. Network/expressRouteCircuits  |  GlobalReachBitsOutPerSecond  |  GlobalReachBitsOutPerSecond  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Micro soft. Network/expressRouteCircuits  |  QosDropBitsInPerSecond  |  DroppedInBitsPerSecond  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Micro soft. Network/expressRouteCircuits  |  QosDropBitsOutPerSecond  |  DroppedOutBitsPerSecond  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/expressRouteCircuits/peerings  |  BitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/expressRouteCircuits/peerings  |  BitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Micro soft. Network/expressRouteGateways  |  ErGatewayConnectionBitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Average | 
| Nee  | Nee |  Micro soft. Network/expressRouteGateways  |  ErGatewayConnectionBitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/expressRoutePorts  |  AdminState  |  AdminState  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/expressRoutePorts  |  LineProtocol  |  LineProtocol  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/expressRoutePorts  |  PortBitsInPerSecond  |  BitsInPerSecond  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/expressRoutePorts  |  PortBitsOutPerSecond  |  BitsOutPerSecond  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/expressRoutePorts  |  RxLightLevel  |  RxLightLevel  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/expressRoutePorts  |  TxLightLevel  |  TxLightLevel  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Network/frontdoors  |  BackendHealthPercentage  |  Back-status percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Network/frontdoors  |  BackendRequestCount  |  Aantal back-aanvragen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Network/frontdoors  |  BackendRequestLatency  |  Latentie van back-upaanvraag  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Network/frontdoors  |  BillableResponseSize  |  Grootte van factureer bare antwoorden  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Network/frontdoors  |  RequestCount  |  Aantal aanvragen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Network/frontdoors  |  RequestSize  |  Aanvraag grootte  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Network/frontdoors  |  ResponseSize  |  Grootte van antwoord  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Network/frontdoors  |  TotalLatency  |  Totale latentie  |  Milliseconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Network/frontdoors  |  WebApplicationFirewallRequestCount  |  Aantal aanvragen voor Web Application firewall  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.Network/loadBalancers  |  AllocatedSnatPorts  |  Toegewezen SNAT-poorten (preview-versie)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/loadBalancers  |  ByteCount  |  Aantal bytes  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/loadBalancers  |  DipAvailability  |  Status van de statustest  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/loadBalancers  |  PacketCount  |  Aantal pakketten  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/loadBalancers  |  SnatConnectionCount  |  Aantal SNAT-verbindingen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/loadBalancers  |  SYNCount  |  SYN-aantal  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.Network/loadBalancers  |  UsedSnatPorts  |  Gebruikte SNAT-poorten (preview-versie)  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/loadBalancers  |  VipAvailability  |  Beschikbaarheid van gegevenspad  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/networkInterfaces  |  BytesReceivedRate  |  Ontvangen bytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/networkInterfaces  |  BytesSentRate  |  Verzonden bytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/networkInterfaces  |  PacketsReceivedRate  |  Ontvangen pakketten  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/networkInterfaces  |  PacketsSentRate  |  Verzonden pakketten  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Network/networkWatchers/connectionMonitors  |  AverageRoundtripMs  |  Gem. retour tijd (MS)  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/networkWatchers/connectionMonitors  |  ChecksFailedPercent  |  Percentage mislukte controles (preview-versie)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/networkWatchers/connectionMonitors  |  ProbesFailedPercent  |  % Tests mislukt  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/networkWatchers/connectionMonitors  |  RoundTripTimeMs  |  Retour tijd (MS) (preview-versie)  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  ByteCount  |  Aantal bytes  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  BytesDroppedDDoS  |  Binnenkomende bytes verloren DDoS  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  BytesForwardedDDoS  |  Doorgestuurde binnenkomende bytes DDoS  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  BytesInDDoS  |  Binnenkomende bytes DDoS  |  BytesPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerSYNPackets  |  Inkomende SYN-pakketten om DDoS-beperking te activeren  |  CountPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerTCPPackets  |  Binnenkomende TCP-pakketten om DDoS-beperking te activeren  |  CountPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  DDoSTriggerUDPPackets  |  Binnenkomende UDP-pakketten om DDoS-beperking te activeren  |  CountPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  IfUnderDDoSAttack  |  Onder DDoS-aanval of niet  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  PacketCount  |  Aantal pakketten  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  PacketsDroppedDDoS  |  DDoS inkomende pakketten verwijderd  |  CountPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  PacketsForwardedDDoS  |  DDoS inkomende pakketten doorgestuurd  |  CountPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  PacketsInDDoS  |  DDoS inkomende pakketten  |  CountPerSecond  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  SynCount  |  SYN-aantal  |  Aantal  |  Totaal | 
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
| **Ja**  | Nee |  Microsoft.Network/publicIPAddresses  |  VipAvailability  |  Beschikbaarheid van gegevenspad  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Network/trafficManagerProfiles  |  ProbeAgentCurrentEndpointStateByProfileResourceId  |  Eindpunt status op eind punt  |  Aantal  |  Maximum | 
| **Ja**  | **Ja** |  Micro soft. Network/trafficManagerProfiles  |  QpsByEndpoint  |  Query's op eind punt geretourneerd  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  AverageBandwidth  |  Gateway-S2S-band breedte  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  P2SBandwidth  |  Gateway P2S-band breedte  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  P2SConnectionCount  |  Aantal P2S-verbindingen  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  TunnelAverageBandwidth  |  Tunnelbandbreedte  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressBytes  |  Uitgaande bytes in tunnel  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPacketDropTSMismatch  |  Uitgaande niet-overeenkomende TS-pakketten door tunnel  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  TunnelEgressPackets  |  Uitgaande pakketten in tunnel  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressBytes  |  Totaal aantal inkomende bytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPacketDropTSMismatch  |  Inkomende niet-overeenkomende TS-pakketten door tunnel  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworkGateways  |  TunnelIngressPackets  |  Tunnel ingangs pakketten  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworks  |  PingMeshAverageRoundtripMs  |  Retour tijd voor pings naar een virtuele machine  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Network/virtualNetworks  |  PingMeshProbesFailedPercent  |  Pingen naar een virtuele machine is mislukt  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  e-mail  |  Binnenkomende berichten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  inkomend. alle. failedrequests  |  Alle binnenkomende mislukte aanvragen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  binnenkomende. alle. aanvragen  |  Alle inkomende aanvragen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  inkomend. gepland  |  Geplande push meldingen verzonden  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  Binnenkomend. gepland. annuleren  |  Geplande push meldingen geannuleerd  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  installatie. alle  |  Bewerkingen voor installatie beheer  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  installatie. Delete  |  Installatie bewerkingen verwijderen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  installatie. Get  |  Installatie bewerkingen ophalen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  installatie. patch  |  Patch-installatie bewerkingen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  installatie. upsert  |  Installatie bewerkingen maken of bijwerken  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  notificationhub. pushes  |  Alle uitgaande meldingen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. allpns. badorexpiredchannel  |  Ongeldige of verlopen kanaal fouten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. allpns. channelerror  |  Kanaal fouten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. allpns. invalidpayload  |  Payload-fouten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. allpns. pnserror  |  Externe meldingen systeem fouten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaand. allpns. geslaagd  |  Geslaagde meldingen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaand. apns. badchannel  |  Fout met ongeldige APNS-kanaal  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaand. apns. expiredchannel  |  Fout bij verlopen van APNS-kanaal  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaand. apns. invalidcredentials  |  APNS-autorisatie fouten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaand. apns. invalidnotificationsize  |  Fout door ongeldige grootte van APNS-melding  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaand. apns. pnserror  |  APNS-fouten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaand. apns. geslaagd  |  Geslaagde meldingen van APNS  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. GCM. authenticationerror  |  GCM-verificatie fouten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. GCM. badchannel  |  GCM ongeldige kanaal fout  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. GCM. expiredchannel  |  GCM-fout met verlopen kanaal  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. GCM. invalidcredentials  |  GCM-verificatie fouten (ongeldige referenties)  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. GCM. invalidnotificationformat  |  Ongeldige indeling van GCM-melding  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. GCM. invalidnotificationsize  |  Fout met ongeldige grootte van GCM-melding  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. GCM. pnserror  |  GCM-fouten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaand. GCM. geslaagd  |  Geslaagde meldingen GCM  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. GCM. throttled  |  GCM beperkte meldingen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. GCM. wrongchannel  |  GCM onjuiste kanaal fout  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. mpns. authenticationerror  |  MPNS-verificatie fouten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. mpns. badchannel  |  MPNS ongeldige kanaal fout  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. mpns. channeldisconnected  |  Verbinding met MPNS-kanaal verbroken  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. mpns. dropd  |  MPNS-verwijderde meldingen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. mpns. invalidcredentials  |  Ongeldige referenties MPNS  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. mpns. invalidnotificationformat  |  Ongeldige indeling van MPNS-melding  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. mpns. pnserror  |  MPNS-fouten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaand. mpns. geslaagd  |  Geslaagde meldingen MPNS  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. mpns. throttled  |  MPNS beperkte meldingen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. wns. authenticationerror  |  WNS-verificatie fouten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. wns. badchannel  |  WNS ongeldige kanaal fout  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. wns. channeldisconnected  |  Verbinding met WNS-kanaal verbroken  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. wns. channelthrottled  |  WNS-kanaal beperkt  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. wns. dropd  |  WNS-verwijderde meldingen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. wns. expiredchannel  |  WNS-fout met verlopen kanaal  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. wns. invalidcredentials  |  WNS-verificatie fouten (ongeldige referenties)  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. wns. invalidnotificationformat  |  Ongeldige indeling van WNS-melding  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. wns. invalidnotificationsize  |  Fout met ongeldige grootte van WNS-melding  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. wns. invalidtoken  |  WNS-verificatie fouten (ongeldig token)  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. wns. pnserror  |  WNS-fouten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaand. wns. geslaagd  |  Geslaagde meldingen WNS  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. wns. throttled  |  WNS beperkte meldingen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. wns. tokenproviderunreachable  |  WNS-verificatie fouten (onbereikbaar)  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  uitgaande. wns. wrongtoken  |  WNS-autorisatie fouten (onjuist token)  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  registratie. alle  |  Registratie bewerkingen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  registratie. Create  |  Bewerkingen voor het maken van registratie  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  registratie. Delete  |  Verwijderings bewerkingen voor registratie  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  registratie. ophalen  |  Lees bewerkingen voor registratie  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  registratie. update  |  Registratie-update bewerkingen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. notification hubs/naam ruimten/notification hubs  |  gepland. in behandeling  |  Geplande meldingen in behandeling  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Percentage beschik bare geheugen Average_  |  Percentage beschikbaar geheugen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_ percentage beschik bare wissel ruimte  |  Percentage beschik bare wissel ruimte  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_% toegewezen bytes in gebruik  |  % Toegewezen bytes in gebruik  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_ percentage DPC-tijd  |  Percentage DPC-tijd  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Percentage vrije inodes Average_  |  % Vrije inodes  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  % Beschik bare ruimte Average_  |  Percentage beschik bare ruimte  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  % Beschik bare ruimte Average_  |  Percentage beschik bare ruimte  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  % Niet-actieve tijd Average_  |  Percentage niet-actieve tijd  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Percentage interrupt-tijd van Average_  |  Percentage interrupt-tijd  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_% i/o-wacht tijd  |  % I/o-wacht tijd  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Tijd van Average_% leuk  |  Percentage tijd in Nice  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Percentage tijd in beschermde modus Average_  |  Percentage tijd in beschermde modus  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Percentage processor tijd van Average_  |  Percentage processortijd  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Percentage processor tijd van Average_  |  Percentage processortijd  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_% gebruikte inodes  |  % Gebruikte inodes  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_ percentage gebruikt geheugen  |  Percentage gebruikt geheugen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Percentage gebruikte ruimte Average_  |  Percentage gebruikte ruimte  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_ percentage gebruikte wissel ruimte  |  Percentage gebruikte wissel ruimte  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Percentage gebruikers tijd van Average_  |  Percentage gebruikers tijd  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Available MB  |  Beschik bare Mbytes  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Available MB geheugen  |  Beschikbaar geheugen in mega bytes  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Available MB wisselen  |  Beschik bare mega bytes wisselen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. Gelezen bytes per seconde  |  Gemiddelde Lees tijd schijf  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. Gelezen bytes per seconde  |  Gemiddelde Lees tijd schijf  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. Seconde/overdracht schijf  |  Gemiddelde tijd schijf overdracht  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. voor de fysieke schijf  |  Gemiddelde schrijf tijd schijf  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Avg. voor de fysieke schijf  |  Gemiddelde schrijf tijd schijf  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Ontvangen Average_Bytes per seconde  |  Ontvangen bytes per seconde  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Verzonden Average_Bytes per seconde  |  Verzonden bytes per seconde  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Bytes totaal per seconde  |  Totaal aantal bytes per seconde  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Wachtrij lengte van Average_Current schijf  |  Huidige wachtrij lengte voor de schijf  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Lees bewerkingen in bytes per seconde  |  Gelezen bytes per seconde  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Lees bewerkingen per seconde  |  Lees bewerkingen per seconde  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Disk Lees bewerkingen per seconde  |  Lees bewerkingen per seconde  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Disk overdrachten per seconde  |  Schijf overdrachten per seconde  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Disk overdrachten per seconde  |  Schijf overdrachten per seconde  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Disk geschreven bytes per seconde  |  Geschreven bytes per seconde  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Disk schrijf bewerkingen per seconde  |  Schrijf bewerkingen per seconde  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Disk schrijf bewerkingen per seconde  |  Schrijf bewerkingen per seconde  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Free MB  |  Beschik bare mega bytes  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Free MB  |  Beschik bare mega bytes  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Free fysiek geheugen  |  Vrij fysiek geheugen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Free ruimte in wissel geheugen bestanden  |  Vrije ruimte in wissel geheugen bestanden  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Virtueel geheugen Average_Free  |  Vrij virtueel geheugen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Logical schijf bytes per seconde  |  Bytes van logische schijf per seconde  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Page Lees bewerkingen per seconde  |  Gelezen pagina's per seconde  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Page schrijf bewerkingen per seconde  |  Geschreven pagina's per seconde  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Pages/sec.  |  Pagina's per seconde  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Beschermde tijd van Average_Pct  |  Pct-geprivilegieerde tijd  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Gebruikers tijd van Average_Pct  |  Pct-gebruikers tijd  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Physical schijf bytes per seconde  |  Bytes van fysieke schijf per seconde  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Processes  |  Processen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Lengte van Average_Processor wachtrij  |  Lengte van de processor wachtrij  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Size opgeslagen in Wissel bestanden  |  Grootte opgeslagen in Wissel bestanden  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Total bytes  |  Totaal aantal bytes  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Ontvangen Average_Total bytes  |  Totaal aantal ontvangen bytes  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Verzonden Average_Total bytes  |  Totaal aantal verzonden bytes  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Total conflicten  |  Totaal aantal conflicten  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Ontvangen Average_Total pakketten  |  Totaal aantal ontvangen pakketten  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Verzonden Average_Total pakketten  |  Totaal aantal verzonden pakketten  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Total RX-fouten  |  Totaal aantal RX-fouten  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Total TX-fouten  |  Totaal aantal TX-fouten  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Uptime  |  Systeem  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Used MB wissel ruimte  |  Gebruikte MB wissel ruimte  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Used geheugen KB  |  Gebruikte geheugen-kBytes  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Used geheugen MB  |  Gebruikt geheugen Mbytes  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Users  |  Gebruikers  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Average_Virtual gedeeld geheugen  |  Virtueel gedeeld geheugen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Gebeurtenis  |  Gebeurtenis  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Microsoft.OperationalInsights/workspaces  |  Hartslag  |  Hartslag  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.OperationalInsights/workspaces  |  Bijwerken  |  Bijwerken  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. PowerBIDedicated/capaciteiten  |  memory_metric  |  Geheugen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. PowerBIDedicated/capaciteiten  |  memory_thrashing_metric  |  Geheugen overbelasting (gegevens sets)  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. PowerBIDedicated/capaciteiten  |  qpu_high_utilization_metric  |  Hoog QPU-gebruik  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. PowerBIDedicated/capaciteiten  |  QueryDuration  |  Query duur (gegevens sets)  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. PowerBIDedicated/capaciteiten  |  QueryPoolJobQueueLength  |  Wachtrij lengte van de taak pool voor query's (gegevens sets)  |  Aantal  |  Average | 
| Nee  | Nee |  Micro soft. relay/naam ruimten  |  ActiveConnections  |  ActiveConnections  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. relay/naam ruimten  |  ActiveListeners  |  ActiveListeners  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. relay/naam ruimten  |  BytesTransferred  |  BytesTransferred  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. relay/naam ruimten  |  ListenerConnections-client error  |  ListenerConnections-client error  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. relay/naam ruimten  |  ListenerConnections-server error  |  ListenerConnections-server error  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. relay/naam ruimten  |  ListenerConnections-geslaagd  |  ListenerConnections-geslaagd  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. relay/naam ruimten  |  ListenerConnections-TotalRequests  |  ListenerConnections-TotalRequests  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. relay/naam ruimten  |  ListenerDisconnects  |  ListenerDisconnects  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. relay/naam ruimten  |  SenderConnections-client error  |  SenderConnections-client error  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. relay/naam ruimten  |  SenderConnections-server error  |  SenderConnections-server error  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. relay/naam ruimten  |  SenderConnections-geslaagd  |  SenderConnections-geslaagd  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. relay/naam ruimten  |  SenderConnections-TotalRequests  |  SenderConnections-TotalRequests  |  Aantal  |  Totaal | 
| Nee  | Nee |  Micro soft. relay/naam ruimten  |  SenderDisconnects  |  SenderDisconnects  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Search/searchServices  |  SearchLatency  |  Zoek latentie  |  Seconden  |  Average | 
| **Ja**  | Nee |  Micro soft. Search/searchServices  |  SearchQueriesPerSecond  |  Zoek query's per seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. Search/searchServices  |  ThrottledSearchQueriesPercentage  |  Percentage vertraagde Zoek query's  |  Percentage  |  Average | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  ActiveConnections  |  ActiveConnections  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  ActiveMessages  |  Aantal actieve berichten in een wachtrij/onderwerp.  |  Aantal  |  Average | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  ConnectionsClosed  |  Gesloten verbindingen.  |  Aantal  |  Average | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  ConnectionsOpened  |  Geopende verbindingen.  |  Aantal  |  Average | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  CPUXNS  |  CPU (afgeschaft)  |  Percentage  |  Maximum | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  DeadletteredMessages  |  Aantal onbestelbare berichten in een wachtrij/onderwerp.  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Microsoft.ServiceBus/namespaces  |  IncomingMessages  |  Binnenkomende berichten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.ServiceBus/namespaces  |  IncomingRequests  |  Binnenkomende aanvragen  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  Berichten  |  Aantal berichten in een wachtrij/onderwerp.  |  Aantal  |  Average | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  NamespaceCpuUsage  |  CPU  |  Percentage  |  Maximum | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  NamespaceMemoryUsage  |  Geheugengebruik  |  Percentage  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.ServiceBus/namespaces  |  OutgoingMessages  |  Uitgaande berichten  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  ScheduledMessages  |  Aantal geplande berichten in een wachtrij/onderwerp.  |  Aantal  |  Average | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  ServerErrors  |  Serverfouten.  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  Grootte  |  Grootte  |  Bytes  |  Average | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  SuccessfulRequests  |  Geslaagde aanvragen  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  ThrottledRequests  |  Beperkte aanvragen.  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  UserErrors  |  Gebruikersfouten.  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.ServiceBus/namespaces  |  WSXNS  |  Geheugen gebruik (afgeschaft)  |  Percentage  |  Maximum | 
| Nee  | Nee |  Micro soft. ServiceFabricMesh/toepassingen  |  ActualCpu  |  ActualCpu  |  Aantal  |  Average | 
| Nee  | Nee |  Micro soft. ServiceFabricMesh/toepassingen  |  ActualMemory  |  ActualMemory  |  Bytes  |  Average | 
| Nee  | Nee |  Micro soft. ServiceFabricMesh/toepassingen  |  AllocatedCpu  |  AllocatedCpu  |  Aantal  |  Average | 
| Nee  | Nee |  Micro soft. ServiceFabricMesh/toepassingen  |  AllocatedMemory  |  AllocatedMemory  |  Bytes  |  Average | 
| Nee  | Nee |  Micro soft. ServiceFabricMesh/toepassingen  |  ApplicationStatus  |  ApplicationStatus  |  Aantal  |  Average | 
| Nee  | Nee |  Micro soft. ServiceFabricMesh/toepassingen  |  Container status  |  Container status  |  Aantal  |  Average | 
| Nee  | Nee |  Micro soft. ServiceFabricMesh/toepassingen  |  CpuUtilization  |  CpuUtilization  |  Percentage  |  Average | 
| Nee  | Nee |  Micro soft. ServiceFabricMesh/toepassingen  |  MemoryUtilization  |  MemoryUtilization  |  Percentage  |  Average | 
| Nee  | Nee |  Micro soft. ServiceFabricMesh/toepassingen  |  RestartCount  |  RestartCount  |  Aantal  |  Average | 
| Nee  | Nee |  Micro soft. ServiceFabricMesh/toepassingen  |  ServiceReplicaStatus  |  ServiceReplicaStatus  |  Aantal  |  Average | 
| Nee  | Nee |  Micro soft. ServiceFabricMesh/toepassingen  |  ServiceStatus  |  ServiceStatus  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. SignalRService/Signa lering  |  ConnectionCount  |  Aantal verbindingen  |  Aantal  |  Maximum | 
| **Ja**  | **Ja** |  Micro soft. SignalRService/Signa lering  |  InboundTraffic  |  Binnenkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. SignalRService/Signa lering  |  MessageCount  |  Aantal berichten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. SignalRService/Signa lering  |  OutboundTraffic  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. SignalRService/Signa lering  |  SystemErrors  |  Systeem fouten  |  Percentage  |  Maximum | 
| **Ja**  | **Ja** |  Micro soft. SignalRService/Signa lering  |  UserErrors  |  Gebruikers fouten  |  Percentage  |  Maximum | 
| **Ja**  | **Ja** |  Micro soft. SQL/managedInstances  |  avg_cpu_percent  |  Gemiddeld CPU-percentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. SQL/managedInstances  |  io_bytes_read  |  Gelezen IO-bytes  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. SQL/managedInstances  |  io_bytes_written  |  Geschreven IO-bytes  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. SQL/managedInstances  |  io_requests  |  Aantal i/o-aanvragen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. SQL/managedInstances  |  reserved_storage_mb  |  Gereserveerde opslag ruimte  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. SQL/managedInstances  |  storage_space_used_mb  |  Gebruikte opslag ruimte  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. SQL/managedInstances  |  virtual_core_count  |  Aantal virtuele kernen  |  Aantal  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers  |  database_dtu_consumption_percent  |  DTU-percentage  |  Percentage  |  Average | 
| Nee  | Nee |  Microsoft.Sql/servers  |  database_storage_used  |  Gebruikte gegevens ruimte  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers  |  dtu_consumption_percent  |  DTU-percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers  |  dtu_used  |  DTU gebruikt  |  Aantal  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers  |  storage_used  |  Gebruikte gegevens ruimte  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  allocated_data_storage  |  Toegewezen gegevens ruimte  |  Bytes  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  app_cpu_billed  |  App CPU gefactureerd  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  app_cpu_percent  |  CPU-percentage van app  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  app_memory_percent  |  Percentage app-geheugen  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  blocked_by_firewall  |  Geblokkeerd door de firewall  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  cache_hit_percent  |  Percentage cache treffers  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  cache_used_percent  |  Percentage gebruikt cache  |  Percentage  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  connection_failed  |  Mislukte verbindingen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  connection_successful  |  Geslaagde verbindingen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  cpu_limit  |  CPU-limiet  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  cpu_percent  |  CPU-percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  cpu_used  |  CPU gebruikt  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  constateer  |  Impassen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  dtu_consumption_percent  |  DTU-percentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  dtu_limit  |  DTU-limiet  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  dtu_used  |  DTU gebruikt  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  dwu_consumption_percent  |  Percentage DWU  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  dwu_limit  |  Limiet voor DWU  |  Aantal  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  dwu_used  |  DWU gebruikt  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  local_tempdb_usage_percent  |  Lokaal TempDB-percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  log_write_percent  |  Logboek-IO-percentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  memory_usage_percent  |  Geheugen percentage  |  Percentage  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  physical_data_read_percent  |  Gegevens-I/O-percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  sessions_percent  |  Percentage sessies  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  opslag  |  Gebruikte gegevens ruimte  |  Bytes  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  storage_percent  |  Percentage gebruikte gegevens ruimte  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  tempdb_data_size  |  Data File grootte van tempdb  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  tempdb_log_size  |  Grootte van logboek bestanden tempdb  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Microsoft.Sql/servers/databases  |  tempdb_log_used_percent  |  Percentage gebruikt TempDB-logboek  |  Percentage  |  Maximum | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  workers_percent  |  Percentage werk nemers  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Microsoft.Sql/servers/databases  |  xtp_storage_percent  |  Percentage OLTP-opslag in het geheugen  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. SQL/servers/elasticPools  |  allocated_data_storage  |  Toegewezen gegevens ruimte  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. SQL/servers/elasticPools  |  allocated_data_storage_percent  |  Percentage toegewezen gegevens ruimte  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Micro soft. SQL/servers/elasticPools  |  cpu_limit  |  CPU-limiet  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. SQL/servers/elasticPools  |  cpu_percent  |  CPU-percentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. SQL/servers/elasticPools  |  cpu_used  |  CPU gebruikt  |  Aantal  |  Average | 
| Nee  | Nee |  Micro soft. SQL/servers/elasticPools  |  database_allocated_data_storage  |  Toegewezen gegevens ruimte  |  Bytes  |  Average | 
| Nee  | Nee |  Micro soft. SQL/servers/elasticPools  |  database_cpu_limit  |  CPU-limiet  |  Aantal  |  Average | 
| Nee  | Nee |  Micro soft. SQL/servers/elasticPools  |  database_cpu_percent  |  CPU-percentage  |  Percentage  |  Average | 
| Nee  | Nee |  Micro soft. SQL/servers/elasticPools  |  database_cpu_used  |  CPU gebruikt  |  Aantal  |  Average | 
| Nee  | Nee |  Micro soft. SQL/servers/elasticPools  |  database_dtu_consumption_percent  |  DTU-percentage  |  Percentage  |  Average | 
| Nee  | Nee |  Micro soft. SQL/servers/elasticPools  |  database_eDTU_used  |  eDTU gebruikt  |  Aantal  |  Average | 
| Nee  | Nee |  Micro soft. SQL/servers/elasticPools  |  database_log_write_percent  |  Logboek-IO-percentage  |  Percentage  |  Average | 
| Nee  | Nee |  Micro soft. SQL/servers/elasticPools  |  database_physical_data_read_percent  |  Gegevens-I/O-percentage  |  Percentage  |  Average | 
| Nee  | Nee |  Micro soft. SQL/servers/elasticPools  |  database_sessions_percent  |  Percentage sessies  |  Percentage  |  Average | 
| Nee  | Nee |  Micro soft. SQL/servers/elasticPools  |  database_storage_used  |  Gebruikte gegevens ruimte  |  Bytes  |  Average | 
| Nee  | Nee |  Micro soft. SQL/servers/elasticPools  |  database_workers_percent  |  Percentage werk nemers  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Micro soft. SQL/servers/elasticPools  |  dtu_consumption_percent  |  DTU-percentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. SQL/servers/elasticPools  |  eDTU_limit  |  eDTU-limiet  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. SQL/servers/elasticPools  |  eDTU_used  |  eDTU gebruikt  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. SQL/servers/elasticPools  |  log_write_percent  |  Logboek-IO-percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Micro soft. SQL/servers/elasticPools  |  physical_data_read_percent  |  Gegevens-I/O-percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Micro soft. SQL/servers/elasticPools  |  sessions_percent  |  Percentage sessies  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. SQL/servers/elasticPools  |  storage_limit  |  Maximale grootte van gegevens  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. SQL/servers/elasticPools  |  storage_percent  |  Percentage gebruikte gegevens ruimte  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. SQL/servers/elasticPools  |  storage_used  |  Gebruikte gegevens ruimte  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. SQL/servers/elasticPools  |  tempdb_data_size  |  Data File grootte van tempdb  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. SQL/servers/elasticPools  |  tempdb_log_size  |  Grootte van logboek bestanden tempdb  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. SQL/servers/elasticPools  |  tempdb_log_used_percent  |  Percentage gebruikt TempDB-logboek  |  Percentage  |  Maximum | 
| **Ja**  | **Ja** |  Micro soft. SQL/servers/elasticPools  |  workers_percent  |  Percentage werk nemers  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Micro soft. SQL/servers/elasticPools  |  xtp_storage_percent  |  Percentage OLTP-opslag in het geheugen  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts  |  Beschikbaarheid  |  Beschikbaarheid  |  Percentage  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts  |  SuccessE2ELatency  |  Success E2E Latency  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts  |  SuccessServerLatency  |  Geslaagde server latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Microsoft.Storage/storageAccounts  |  Transacties  |  Transacties  |  Aantal  |  Totaal | 
| Nee  | Nee |  Microsoft.Storage/storageAccounts  |  UsedCapacity  |  Gebruikte capaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/blobServices  |  Beschikbaarheid  |  Beschikbaarheid  |  Percentage  |  Average | 
| Nee  | Nee |  Micro soft. Storage/Storage accounts/blobServices  |  BlobCapacity  |  BLOB-capaciteit  |  Bytes  |  Average | 
| Nee  | Nee |  Micro soft. Storage/Storage accounts/blobServices  |  BlobCount  |  Aantal blobs  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/blobServices  |  ContainerCount  |  Aantal BLOB-containers  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/blobServices  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| Nee  | Nee |  Micro soft. Storage/Storage accounts/blobServices  |  IndexCapacity  |  Index capaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/blobServices  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/blobServices  |  SuccessE2ELatency  |  Success E2E Latency  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/blobServices  |  SuccessServerLatency  |  Geslaagde server latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/blobServices  |  Transacties  |  Transacties  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/fileServices  |  Beschikbaarheid  |  Beschikbaarheid  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/fileServices  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| Nee  | Nee |  Micro soft. Storage/Storage accounts/fileServices  |  FileCapacity  |  Bestands capaciteit  |  Bytes  |  Average | 
| Nee  | Nee |  Micro soft. Storage/Storage accounts/fileServices  |  FileCount  |  Aantal bestanden  |  Aantal  |  Average | 
| Nee  | Nee |  Micro soft. Storage/Storage accounts/fileServices  |  FileShareCount  |  Aantal bestands shares  |  Aantal  |  Average | 
| Nee  | Nee |  Micro soft. Storage/Storage accounts/fileServices  |  FileShareQuota  |  Quota grootte van bestands share  |  Bytes  |  Average | 
| Nee  | Nee |  Micro soft. Storage/Storage accounts/fileServices  |  FileShareSnapshotCount  |  Aantal moment opnamen van bestands shares  |  Aantal  |  Average | 
| Nee  | Nee |  Micro soft. Storage/Storage accounts/fileServices  |  FileShareSnapshotSize  |  Grootte van moment opname van bestands share  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/fileServices  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/fileServices  |  SuccessE2ELatency  |  Success E2E Latency  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/fileServices  |  SuccessServerLatency  |  Geslaagde server latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/fileServices  |  Transacties  |  Transacties  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/queueServices  |  Beschikbaarheid  |  Beschikbaarheid  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/queueServices  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/queueServices  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/queueServices  |  QueueCapacity  |  Wachtrij capaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/queueServices  |  QueueCount  |  Aantal wachtrijen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/queueServices  |  QueueMessageCount  |  Aantal wachtrij berichten  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/queueServices  |  SuccessE2ELatency  |  Success E2E Latency  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/queueServices  |  SuccessServerLatency  |  Geslaagde server latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/queueServices  |  Transacties  |  Transacties  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/tableServices  |  Beschikbaarheid  |  Beschikbaarheid  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/tableServices  |  Uitgaand verkeer  |  Uitgaand verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/tableServices  |  Inkomend verkeer  |  Inkomend verkeer  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/tableServices  |  SuccessE2ELatency  |  Success E2E Latency  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/tableServices  |  SuccessServerLatency  |  Geslaagde server latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/tableServices  |  TableCapacity  |  Tabel capaciteit  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/tableServices  |  TableCount  |  Aantal tabellen  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/tableServices  |  TableEntityCount  |  Aantal tabel entiteiten  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. Storage/Storage accounts/tableServices  |  Transacties  |  Transacties  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  ClientIOPS  |  Totaal aantal IOPS client  |  Aantal  |  Average | 
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
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  StorageTargetHealth  |  Status van opslag doel  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  StorageTargetIOPS  |  Totale aantal StorageTarget IOPS  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  StorageTargetLatency  |  StorageTarget-latentie  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  StorageTargetMetadataReadIOPS  |  StorageTarget voor lezen van meta gegevens  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  StorageTargetMetadataWriteIOPS  |  StorageTarget-schrijf-IOPS voor meta gegevens  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  StorageTargetReadAheadThroughput  |  StorageTarget door Voer lezen  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  StorageTargetReadIOPS  |  StorageTarget lezen IOPS  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  StorageTargetSyncWriteThroughput  |  StorageTarget synchrone schrijf doorvoer  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  StorageTargetTotalReadThroughput  |  Totale Lees doorvoer StorageTarget  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  StorageTargetTotalWriteThroughput  |  Totale schrijf doorvoer StorageTarget  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  StorageTargetWriteIOPS  |  StorageTarget write IOPS  |  Aantal  |  Average | 
| **Ja**  | Nee |  Micro soft. StorageCache/caches  |  Systeem  |  Systeem  |  Aantal  |  Average | 
| **Ja**  | Nee |  micro soft. storagesync/storageSyncServices  |  ServerSyncSessionResult  |  Resultaat van synchronisatie sessie  |  Aantal  |  Average | 
| **Ja**  | Nee |  micro soft. storagesync/storageSyncServices  |  StorageSyncBatchTransferredFileBytes  |  Gesynchroniseerde bytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  micro soft. storagesync/storageSyncServices  |  StorageSyncRecalledNetworkBytesByApplication  |  Grootte van intrekken van Cloud lagen op toepassing  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  micro soft. storagesync/storageSyncServices  |  StorageSyncRecalledTotalNetworkBytes  |  Grootte van intrekken Cloud lagen  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  micro soft. storagesync/storageSyncServices  |  StorageSyncRecallIOTotalSizeBytes  |  Cloud lagen intrekken  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  micro soft. storagesync/storageSyncServices  |  StorageSyncRecallThroughputBytesPerSecond  |  Door Voer van Cloud lagen intrekken  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  micro soft. storagesync/storageSyncServices  |  StorageSyncServerHeartbeat  |  Online status van de server  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  micro soft. storagesync/storageSyncServices  |  StorageSyncSyncSessionAppliedFilesCount  |  Gesynchroniseerde bestanden  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  micro soft. storagesync/storageSyncServices  |  StorageSyncSyncSessionPerItemErrorsCount  |  Bestanden die niet worden gesynchroniseerd  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  micro soft. storagesync/storageSyncServices/registeredServer  |  ServerHeartbeat  |  Online status van de server  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  micro soft. storagesync/storageSyncServices/registeredServer  |  ServerRecallIOTotalSizeBytes  |  Cloud lagen intrekken  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  micro soft. storagesync/storageSyncServices/syncGroups  |  SyncGroupBatchTransferredFileBytes  |  Gesynchroniseerde bytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  micro soft. storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionAppliedFilesCount  |  Gesynchroniseerde bestanden  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  micro soft. storagesync/storageSyncServices/syncGroups  |  SyncGroupSyncSessionPerItemErrorsCount  |  Bestanden die niet worden gesynchroniseerd  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  micro soft. storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointBatchTransferredFileBytes  |  Gesynchroniseerde bytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  micro soft. storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionAppliedFilesCount  |  Gesynchroniseerde bestanden  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  micro soft. storagesync/storageSyncServices/syncGroups/serverEndpoints  |  ServerEndpointSyncSessionPerItemErrorsCount  |  Bestanden die niet worden gesynchroniseerd  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. StreamAnalytics/streamingjobs  |  AMLCalloutFailedRequests  |  Mislukte functie aanvragen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. StreamAnalytics/streamingjobs  |  AMLCalloutInputEvents  |  Functie gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. StreamAnalytics/streamingjobs  |  AMLCalloutRequests  |  Functie aanvragen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. StreamAnalytics/streamingjobs  |  ConversionErrors  |  Gegevens conversie fouten  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. StreamAnalytics/streamingjobs  |  DeserializationError  |  Fouten bij het deserialiseren van de invoer  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. StreamAnalytics/streamingjobs  |  DroppedOrAdjustedEvents  |  Gebeurtenissen met een andere volg orde  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. StreamAnalytics/streamingjobs  |  EarlyInputEvents  |  Vroege invoer gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. StreamAnalytics/streamingjobs  |  Errors  |  Runtime-fouten  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. StreamAnalytics/streamingjobs  |  InputEventBytes  |  Invoer gebeurtenis bytes  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. StreamAnalytics/streamingjobs  |  InputEvents  |  Invoer gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. StreamAnalytics/streamingjobs  |  InputEventsSourcesBacklogged  |  Inachterstand, invoer gebeurtenissen  |  Aantal  |  Maximum | 
| **Ja**  | Nee |  Micro soft. StreamAnalytics/streamingjobs  |  InputEventsSourcesPerSecond  |  Invoer bronnen ontvangen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. StreamAnalytics/streamingjobs  |  LateInputEvents  |  Late invoer gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. StreamAnalytics/streamingjobs  |  OutputEvents  |  Uitvoer gebeurtenissen  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. StreamAnalytics/streamingjobs  |  OutputWatermarkDelaySeconds  |  Watermerk vertraging  |  Seconden  |  Maximum | 
| **Ja**  | Nee |  Micro soft. StreamAnalytics/streamingjobs  |  ResourceUtilization  |  % Gebruik  |  Percentage  |  Maximum | 
| **Ja**  | Nee |  Micro soft. VMwareCloudSimple/informatie  |  Gelezen bytes op de schijf  |  Gelezen bytes op de schijf  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. VMwareCloudSimple/informatie  |  Leesbewerkingen op de schijf/seconde  |  Leesbewerkingen op de schijf/seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. VMwareCloudSimple/informatie  |  Geschreven bytes op de schijf  |  Geschreven bytes op de schijf  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. VMwareCloudSimple/informatie  |  Schrijfbewerkingen op de schijf/seconde  |  Schrijfbewerkingen op de schijf/seconde  |  CountPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. VMwareCloudSimple/informatie  |  DiskReadBytesPerSecond  |  Gelezen bytes per seconde  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. VMwareCloudSimple/informatie  |  DiskReadLatency  |  Lees latentie van schijf  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. VMwareCloudSimple/informatie  |  DiskReadOperations  |  Lees bewerkingen op de schijf  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. VMwareCloudSimple/informatie  |  DiskWriteBytesPerSecond  |  Geschreven bytes per seconde  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. VMwareCloudSimple/informatie  |  DiskWriteLatency  |  Schrijf latentie schijf  |  Milliseconden  |  Average | 
| **Ja**  | Nee |  Micro soft. VMwareCloudSimple/informatie  |  DiskWriteOperations  |  Schrijf bewerkingen op de schijf  |  Aantal  |  Totaal | 
| **Ja**  | Nee |  Micro soft. VMwareCloudSimple/informatie  |  MemoryActive  |  Actief geheugen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. VMwareCloudSimple/informatie  |  MemoryGranted  |  Toegewezen geheugen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. VMwareCloudSimple/informatie  |  MemoryUsed  |  Gebruikt geheugen  |  Bytes  |  Average | 
| **Ja**  | Nee |  Micro soft. VMwareCloudSimple/informatie  |  Netwerk in  |  Netwerk in  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. VMwareCloudSimple/informatie  |  Netwerk uit  |  Netwerk uit  |  Bytes  |  Totaal | 
| **Ja**  | Nee |  Micro soft. VMwareCloudSimple/informatie  |  NetworkInBytesPerSecond  |  Netwerk in bytes per seconde  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. VMwareCloudSimple/informatie  |  NetworkOutBytesPerSecond  |  Netwerk uitgaande bytes per seconde  |  BytesPerSecond  |  Average | 
| **Ja**  | Nee |  Micro soft. VMwareCloudSimple/informatie  |  CPU-percentage  |  CPU-percentage  |  Percentage  |  Average | 
| **Ja**  | Nee |  Micro soft. VMwareCloudSimple/informatie  |  PercentageCpuReady  |  Percentage CPU gereed  |  Milliseconden  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Web/hostingEnvironments/multiRolePools  |  ActiveRequests  |  Actieve aanvragen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Web/hostingEnvironments/multiRolePools  |  AverageResponseTime  |  Gemiddelde reactie tijd  |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Web/hostingEnvironments/multiRolePools  |  BytesReceived  |  Gegevens in  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Web/hostingEnvironments/multiRolePools  |  Bytes sent  |  Gegevens uit  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Web/hostingEnvironments/multiRolePools  |  CpuPercentage  |  CPU-percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Web/hostingEnvironments/multiRolePools  |  DiskQueueLength  |  Wachtrij lengte voor schijf  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Web/hostingEnvironments/multiRolePools  |  Http101  |  Http 101  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Web/hostingEnvironments/multiRolePools  |  Http2xx  |  Http-2xx  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Web/hostingEnvironments/multiRolePools  |  Http3xx  |  HTTP-3xx  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Web/hostingEnvironments/multiRolePools  |  Http401  |  HTTP 401  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Web/hostingEnvironments/multiRolePools  |  Http403  |  HTTP 403  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Web/hostingEnvironments/multiRolePools  |  Http404  |  Http 404  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Web/hostingEnvironments/multiRolePools  |  Http406  |  Http 406  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Web/hostingEnvironments/multiRolePools  |  Http4xx  |  Http-4xx  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Web/hostingEnvironments/multiRolePools  |  Http5xx  |  Http-server fouten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Web/hostingEnvironments/multiRolePools  |  HttpQueueLength  |  Lengte van http-wachtrij  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Web/hostingEnvironments/multiRolePools  |  LargeAppServicePlanInstances  |  Werk rollen voor grote App Service plannen  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Web/hostingEnvironments/multiRolePools  |  MediumAppServicePlanInstances  |  Werk nemers met gemiddeld App Service plannen  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Web/hostingEnvironments/multiRolePools  |  MemoryPercentage  |  Geheugen percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Web/hostingEnvironments/multiRolePools  |  Aanvragen  |  Aanvragen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. Web/hostingEnvironments/multiRolePools  |  SmallAppServicePlanInstances  |  Werk rollen voor kleine App Service plannen  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Web/hostingEnvironments/multiRolePools  |  TotalFrontEnds  |  Totale front-ends  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Web/hostingEnvironments/workerPools  |  CpuPercentage  |  CPU-percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Web/hostingEnvironments/workerPools  |  MemoryPercentage  |  Geheugen percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Web/hostingEnvironments/workerPools  |  WorkersAvailable  |  Beschik bare werk nemers  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Web/hostingEnvironments/workerPools  |  WorkersTotal  |  Totaal aantal werk rollen  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. Web/hostingEnvironments/workerPools  |  WorkersUsed  |  Gebruikte werk rollen  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/server farms  |  BytesReceived  |  Gegevens in  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/server farms  |  Bytes sent  |  Gegevens uit  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/server farms  |  CpuPercentage  |  CPU-percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/server farms  |  DiskQueueLength  |  Wachtrij lengte voor schijf  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/server farms  |  HttpQueueLength  |  Lengte van http-wachtrij  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/server farms  |  MemoryPercentage  |  Geheugen percentage  |  Percentage  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/server farms  |  TcpCloseWait  |  TCP-wacht tijd voor sluiten  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/server farms  |  TcpClosing  |  TCP sluiten  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/server farms  |  TcpEstablished  |  TCP-verbinding  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/server farms  |  TcpFinWait1  |  TCP-FIN-wacht 1  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/server farms  |  TcpFinWait2  |  TCP FIN WAIT 2  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/server farms  |  TcpLastAck  |  TCP laatste ACK  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/server farms  |  TcpSynReceived  |  TCP SYN ontvangen  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/server farms  |  TcpSynSent  |  TCP SYN verzonden  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/server farms  |  TcpTimeWait  |  Wacht tijd voor TCP-bewerking  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  AppConnections  |  Verbindingen  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  AverageMemoryWorkingSet  |  Gemiddelde werkset geheugen  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  AverageResponseTime  |  Gemiddelde reactie tijd  |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  BytesReceived  |  Gegevens in  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  Bytes sent  |  Gegevens uit  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  CpuTime  |  CPU-tijd  |  Seconden  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  CurrentAssemblies  |  Huidige Assembly's  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  FunctionExecutionCount  |  Aantal functie-uitvoeringen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  FunctionExecutionUnits  |  Eenheden voor functie-uitvoering  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  Gen0Collections  |  Schone verzamelingen van 0 gen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  Gen1Collections  |  1 garbagecollection-verzamelingen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  Gen2Collections  |  Opschoon verzamelingen van generatie 2  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  Formuleer  |  Aantal ingangen  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  HealthCheckStatus  |  Status van de status controle  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  Http101  |  Http 101  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  Http2xx  |  Http-2xx  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  Http3xx  |  HTTP-3xx  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  Http401  |  HTTP 401  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  Http403  |  HTTP 403  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  Http404  |  Http 404  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  Http406  |  Http 406  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  Http4xx  |  Http-4xx  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  Http5xx  |  Http-server fouten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  HttpResponseTime  |  Reactie tijd  |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  IoOtherBytesPerSecond  |  Andere i/o-bytes per seconde  |  BytesPerSecond  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  IoOtherOperationsPerSecond  |  Andere i/o-bewerkingen per seconde  |  BytesPerSecond  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  IoReadBytesPerSecond  |  I/o gelezen bytes per seconde  |  BytesPerSecond  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  IoReadOperationsPerSecond  |  I/o-Lees bewerkingen per seconde  |  BytesPerSecond  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  IoWriteBytesPerSecond  |  I/o-schrijf bewerkingen in bytes per seconde  |  BytesPerSecond  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  IoWriteOperationsPerSecond  |  I/o-schrijf bewerkingen per seconde  |  BytesPerSecond  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  MemoryWorkingSet  |  Werkset geheugen  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  PrivateBytes  |  Privé-bytes  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  Aanvragen  |  Aanvragen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  RequestsInApplicationQueue  |  Aanvragen in de wachtrij van de toepassing  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  Lijnen  |  Aantal threads  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  TotalAppDomains  |  Totaal aantal app-domeinen  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/sites  |  TotalAppDomainsUnloaded  |  Totaal aantal verwijderde app-domeinen  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  AppConnections  |  Verbindingen  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  AverageMemoryWorkingSet  |  Gemiddelde werkset geheugen  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  AverageResponseTime  |  Gemiddelde reactie tijd  |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  BytesReceived  |  Gegevens in  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  Bytes sent  |  Gegevens uit  |  Bytes  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  CpuTime  |  CPU-tijd  |  Seconden  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  CurrentAssemblies  |  Huidige Assembly's  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  FunctionExecutionCount  |  Aantal functie-uitvoeringen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  FunctionExecutionUnits  |  Eenheden voor functie-uitvoering  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  Gen0Collections  |  Schone verzamelingen van 0 gen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  Gen1Collections  |  1 garbagecollection-verzamelingen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  Gen2Collections  |  Opschoon verzamelingen van generatie 2  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  Formuleer  |  Aantal ingangen  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  HealthCheckStatus  |  Status van de status controle  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  Http101  |  Http 101  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  Http2xx  |  Http-2xx  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  Http3xx  |  HTTP-3xx  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  Http401  |  HTTP 401  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  Http403  |  HTTP 403  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  Http404  |  Http 404  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  Http406  |  Http 406  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  Http4xx  |  Http-4xx  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  Http5xx  |  Http-server fouten  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  HttpResponseTime  |  Reactie tijd  |  Seconden  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  IoOtherBytesPerSecond  |  Andere i/o-bytes per seconde  |  BytesPerSecond  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  IoOtherOperationsPerSecond  |  Andere i/o-bewerkingen per seconde  |  BytesPerSecond  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  IoReadBytesPerSecond  |  I/o gelezen bytes per seconde  |  BytesPerSecond  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  IoReadOperationsPerSecond  |  I/o-Lees bewerkingen per seconde  |  BytesPerSecond  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  IoWriteBytesPerSecond  |  I/o-schrijf bewerkingen in bytes per seconde  |  BytesPerSecond  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  IoWriteOperationsPerSecond  |  I/o-schrijf bewerkingen per seconde  |  BytesPerSecond  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  MemoryWorkingSet  |  Werkset geheugen  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  PrivateBytes  |  Privé-bytes  |  Bytes  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  Aanvragen  |  Aanvragen  |  Aantal  |  Totaal | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  RequestsInApplicationQueue  |  Aanvragen in de wachtrij van de toepassing  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  Lijnen  |  Aantal threads  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  TotalAppDomains  |  Totaal aantal app-domeinen  |  Aantal  |  Average | 
| **Ja**  | **Ja** |  Micro soft. web/sites/sleuven  |  TotalAppDomainsUnloaded  |  Totaal aantal verwijderde app-domeinen  |  Aantal  |  Average | 
