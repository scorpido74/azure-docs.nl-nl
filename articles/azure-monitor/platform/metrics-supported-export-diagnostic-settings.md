---
title: De metrische gegevens van het Azure Monitor-platform exporteerbaar via Diagnostische instellingen
description: Een lijst met metrische gegevens die beschikbaar zijn voor elk resource type met Azure Monitor.
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
author: rboucher
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 1bab6a990ba38bc32c2b7350bb1d4eac15a7b95e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75479031"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>De metrische gegevens van het Azure Monitor-platform exporteerbaar via Diagnostische instellingen

Azure Monitor biedt standaard [platform metrieken](data-platform-metrics.md) zonder configuratie. Het biedt verschillende manieren om te communiceren met platform metrieken, zoals het maken van grafieken in de portal, het openen ervan via de REST API of het opvragen van query's via Power shell of CLI. Bekijk [metrische gegevens: ondersteund](metrics-supported.md) voor een volledige lijst met platform metrieken die momenteel beschikbaar zijn met de geconsolideerde metrische pijp lijn van Azure monitor. Als u deze metrische gegevens wilt opvragen en openen, gebruikt u de [2018-01-01-API-versie](https://docs.microsoft.com/rest/api/monitor/metricdefinitions). Andere metrische gegevens zijn mogelijk beschikbaar in de portal of met behulp van verouderde Api's.

U kunt de platform metrieken van de Azure monitor-pijp lijn naar andere locaties op een van de twee manieren exporteren.
1. [Diagnostische instellingen](diagnostic-settings.md) gebruiken om te verzenden naar Log Analytics, Event Hubs of Azure Storage.
2. De [metrische gegevens rest API](https://docs.microsoft.com/rest/api/monitor/metrics/list) gebruiken

Vanwege complexiteit in de Azure Monitor back-end kunnen niet alle metrische gegevens exporteerbaar zijn met behulp van diagnostische instellingen. De volgende tabel bevat een lijst die kan en kan niet worden geëxporteerd met Diagnostische instellingen.

Exporteerbaar via Diagnostische instellingen? | ResourceType | Gegevens | MetricDisplayName | Eenheid | AggregationType
|----|-----|------|----|----|-----|
Ja | Microsoft.AnalysisServices/servers | CleanerCurrentPrice | Geheugen: huidige prijs opschonen | Aantal | Average
Ja | Microsoft.AnalysisServices/servers | CleanerMemoryNonshrinkable | Geheugen: Removal-geheugen kan niet worden verkleind | Bytes | Average
Ja | Microsoft.AnalysisServices/servers | CleanerMemoryShrinkable | Geheugen: verkleinbaar geheugen | Bytes | Average
Ja | Microsoft.AnalysisServices/servers | CommandPoolBusyThreads | Threads: actieve threads van opdracht pool | Aantal | Average
Ja | Microsoft.AnalysisServices/servers | CommandPoolIdleThreads | Threads: niet-actieve threads van opdracht pool | Aantal | Average
Ja | Microsoft.AnalysisServices/servers | CommandPoolJobQueueLength | Wachtrij lengte van de opdracht pool taak | Aantal | Average
Ja | Microsoft.AnalysisServices/servers | CurrentConnections | Verbinding: huidige verbindingen | Aantal | Average
Ja | Microsoft.AnalysisServices/servers | CurrentUserSessions | Huidige gebruikers sessies | Aantal | Average
Ja | Microsoft.AnalysisServices/servers | LongParsingBusyThreads | Threads: bezette threads voor lang parseren | Aantal | Average
Ja | Microsoft.AnalysisServices/servers | LongParsingIdleThreads | Threads: niet-actieve threads voor lang parseren | Aantal | Average
Ja | Microsoft.AnalysisServices/servers | LongParsingJobQueueLength | Threads: lengte van taak wachtrij voor lang parseren | Aantal | Average
Ja | Microsoft.AnalysisServices/servers | mashup_engine_memory_metric | M-engine geheugen | Bytes | Average
Ja | Microsoft.AnalysisServices/servers | mashup_engine_private_bytes_metric | M-engine-eigen bytes | Bytes | Average
Ja | Microsoft.AnalysisServices/servers | mashup_engine_qpu_metric | M-engine QPU | Aantal | Average
Ja | Microsoft.AnalysisServices/servers | mashup_engine_virtual_bytes_metric | M-engine virtuele bytes | Bytes | Average
Ja | Microsoft.AnalysisServices/servers | memory_metric | Geheugen | Bytes | Average
Ja | Microsoft.AnalysisServices/servers | memory_thrashing_metric | Geheugenthrashing | Procent | Average
Ja | Microsoft.AnalysisServices/servers | MemoryLimitHard | Geheugen: vaste geheugen limiet | Bytes | Average
Ja | Microsoft.AnalysisServices/servers | MemoryLimitHigh | Geheugen: hoge geheugen limiet | Bytes | Average
Ja | Microsoft.AnalysisServices/servers | MemoryLimitLow | Geheugen: lage geheugen limiet | Bytes | Average
Ja | Microsoft.AnalysisServices/servers | MemoryLimitVertiPaq | Geheugen: VertiPaq-geheugen limiet | Bytes | Average
Ja | Microsoft.AnalysisServices/servers | MemoryUsage | Geheugen: geheugen gebruik | Bytes | Average
Ja | Microsoft.AnalysisServices/servers | private_bytes_metric | Eigen Bytes | Bytes | Average
Ja | Microsoft.AnalysisServices/servers | ProcessingPoolBusyIOJobThreads | Threads: bezig met verwerken van I/O-taak threads van pool | Aantal | Average
Ja | Microsoft.AnalysisServices/servers | ProcessingPoolBusyNonIOThreads | Threads: bezig met het verwerken van niet-I/O-threads van de groep | Aantal | Average
Ja | Microsoft.AnalysisServices/servers | ProcessingPoolIdleIOJobThreads | Threads: niet-actieve I/O-taak threads van de groep verwerken | Aantal | Average
Ja | Microsoft.AnalysisServices/servers | ProcessingPoolIdleNonIOThreads | Threads: niet-I/O-threads van de groep worden verwerkt | Aantal | Average
Ja | Microsoft.AnalysisServices/servers | ProcessingPoolIOJobQueueLength | Threads: lengte van I/O-taak wachtrij voor verwerking van groep | Aantal | Average
Ja | Microsoft.AnalysisServices/servers | ProcessingPoolJobQueueLength | Wachtrij lengte van de pool taak wordt verwerkt | Aantal | Average
Ja | Microsoft.AnalysisServices/servers | qpu_metric | QPU | Aantal | Average
Ja | Microsoft.AnalysisServices/servers | QueryPoolBusyThreads | Query's pool bezette threads | Aantal | Average
Ja | Microsoft.AnalysisServices/servers | QueryPoolIdleThreads | Threads: niet-actieve threads van query pool | Aantal | Average
Ja | Microsoft.AnalysisServices/servers | QueryPoolJobQueueLength | Threads: lengte van taak wachtrij voor query pool | Aantal | Average
Ja | Microsoft.AnalysisServices/servers | Quota | Geheugen: quotum | Bytes | Average
Ja | Microsoft.AnalysisServices/servers | QuotaBlocked | Geheugen: quotum geblokkeerd | Aantal | Average
Ja | Microsoft.AnalysisServices/servers | RowsConvertedPerSec | Verwerken: geconverteerde rijen per seconde | CountPerSecond | Average
Ja | Microsoft.AnalysisServices/servers | RowsReadPerSec | Verwerken: gelezen rijen per seconde | CountPerSecond | Average
Ja | Microsoft.AnalysisServices/servers | RowsWrittenPerSec | Verwerken: geschreven rijen per seconde | CountPerSecond | Average
Ja | Microsoft.AnalysisServices/servers | ShortParsingBusyThreads | Threads: bezette threads voor kort parseren | Aantal | Average
Ja | Microsoft.AnalysisServices/servers | ShortParsingIdleThreads | Threads: niet-actieve threads voor kort parseren | Aantal | Average
Ja | Microsoft.AnalysisServices/servers | ShortParsingJobQueueLength | Threads: lengte van taak wachtrij voor kort parseren | Aantal | Average
Ja | Microsoft.AnalysisServices/servers | SuccessfullConnectionsPerSec | Geslaagde verbindingen per seconde | CountPerSecond | Average
Ja | Microsoft.AnalysisServices/servers | TotalConnectionFailures | Totaal aantal verbindings fouten | Aantal | Average
Ja | Microsoft.AnalysisServices/servers | TotalConnectionRequests | Totaal aantal verbindings aanvragen | Aantal | Average
Ja | Microsoft.AnalysisServices/servers | VertiPaqNonpaged | Geheugen: VertiPaq niet-wisselbaar | Bytes | Average
Ja | Microsoft.AnalysisServices/servers | VertiPaqPaged | Geheugen: VertiPaq-pagina | Bytes | Average
Ja | Microsoft.AnalysisServices/servers | virtual_bytes_metric | Virtuele bytes | Bytes | Average
Ja | Microsoft.ApiManagement/service | BackendDuration | Duur van back-end-aanvragen | Milliseconden | Average
Ja | Microsoft.ApiManagement/service | Capaciteit | Capaciteit | Procent | Average
Ja | Microsoft.ApiManagement/service | Duur | Totale duur van gateway aanvragen | Milliseconden | Average
Ja | Microsoft.ApiManagement/service | EventHubDroppedEvents | Verwijderde EventHub-gebeurtenissen | Aantal | Totaal
Ja | Microsoft.ApiManagement/service | EventHubRejectedEvents | Geweigerde EventHub-gebeurtenissen | Aantal | Totaal
Ja | Microsoft.ApiManagement/service | EventHubSuccessfulEvents | Geslaagde EventHub-gebeurtenissen | Aantal | Totaal
Ja | Microsoft.ApiManagement/service | EventHubThrottledEvents | Vertraagde EventHub-gebeurtenissen | Aantal | Totaal
Ja | Microsoft.ApiManagement/service | EventHubTimedoutEvents | Time-out EventHub-gebeurtenissen | Aantal | Totaal
Ja | Microsoft.ApiManagement/service | EventHubTotalBytesSent | Grootte van EventHub-gebeurtenissen | Bytes | Totaal
Ja | Microsoft.ApiManagement/service | EventHubTotalEvents | Totaal aantal EventHub-gebeurtenissen | Aantal | Totaal
Ja | Microsoft.ApiManagement/service | EventHubTotalFailedEvents | Mislukte EventHub-gebeurtenissen | Aantal | Totaal
Ja | Microsoft.ApiManagement/service | FailedRequests | Mislukte gateway aanvragen (afgeschaft) | Aantal | Totaal
Ja | Microsoft.ApiManagement/service | OtherRequests | Andere gateway aanvragen (afgeschaft) | Aantal | Totaal
Ja | Microsoft.ApiManagement/service | Aanvragen | Aanvragen | Aantal | Totaal
Ja | Microsoft.ApiManagement/service | SuccessfulRequests | Geslaagde gateway aanvragen (afgeschaft) | Aantal | Totaal
Ja | Microsoft.ApiManagement/service | TotalRequests | Totaal aantal gateway aanvragen (afgeschaft) | Aantal | Totaal
Ja | Microsoft.ApiManagement/service | UnauthorizedRequests | Niet-geautoriseerde gateway aanvragen (afgeschaft) | Aantal | Totaal
Ja | Micro soft. AppPlatform/lente | AppCpuUsagePercentage | CPU-gebruiks percentage van app | Procent | Average
Ja | Micro soft. AppPlatform/lente | AppMemoryCommitted | Toegewezen app-geheugen | Bytes | Average
Ja | Micro soft. AppPlatform/lente | AppMemoryMax | Maxi maal app-geheugen | Bytes | Maximum
Ja | Micro soft. AppPlatform/lente | AppMemoryUsed | Gebruikt app-geheugen | Bytes | Average
Ja | Micro soft. AppPlatform/lente | GCPauseTotalCount | Aantal GC-onderbrekingen | Aantal | Totaal
Ja | Micro soft. AppPlatform/lente | GCPauseTotalTime | Totale tijd van de GC-onderbreking | Milliseconden | Totaal
Ja | Micro soft. AppPlatform/lente | MaxOldGenMemoryPoolBytes | Maxi maal beschik bare oude generatie gegevens grootte | Bytes | Average
Ja | Micro soft. AppPlatform/lente | OldGenMemoryPoolBytes | Gegevens grootte van oude generatie | Bytes | Average
Ja | Micro soft. AppPlatform/lente | OldGenPromotedBytes | Promo veren tot oude generatie gegevens grootte | Bytes | Maximum
Ja | Micro soft. AppPlatform/lente | SystemCpuUsagePercentage | Percentage van het CPU-gebruik van het systeem | Procent | Average
Ja | Micro soft. AppPlatform/lente | TomcatErrorCount | Tomcat Global-fout | Aantal | Totaal
Ja | Micro soft. AppPlatform/lente | TomcatReceivedBytes | Totaal aantal bytes ontvangen Tomcat | Bytes | Totaal
Ja | Micro soft. AppPlatform/lente | TomcatRequestMaxTime | Maximale tijd voor tomcat-aanvraag | Milliseconden | Maximum
Ja | Micro soft. AppPlatform/lente | TomcatRequestTotalCount | Totaal aantal Tomcat-aanvragen | Aantal | Totaal
Ja | Micro soft. AppPlatform/lente | TomcatRequestTotalTime | Totaal aantal keer Tomcat-aanvragen | Milliseconden | Totaal
Ja | Micro soft. AppPlatform/lente | TomcatResponseAvgTime | Gemiddelde tijd Tomcat-aanvraag | Milliseconden | Average
Ja | Micro soft. AppPlatform/lente | TomcatSentBytes | Totaal aantal verzonden bytes in Tomcat | Bytes | Totaal
Ja | Micro soft. AppPlatform/lente | TomcatSessionActiveCurrentCount | Aantal Tomcat-sessies | Aantal | Totaal
Ja | Micro soft. AppPlatform/lente | TomcatSessionActiveMaxCount | Aantal actieve Tomcat-sessies | Aantal | Totaal
Ja | Micro soft. AppPlatform/lente | TomcatSessionAliveMaxTime | Time-outperiode van Tomcat-sessie | Milliseconden | Maximum
Ja | Micro soft. AppPlatform/lente | TomcatSessionCreatedCount | Aantal gemaakte Tomcat-sessies | Aantal | Totaal
Ja | Micro soft. AppPlatform/lente | TomcatSessionExpiredCount | Aantal verlopen Tomcat-sessies | Aantal | Totaal
Ja | Micro soft. AppPlatform/lente | TomcatSessionRejectedCount | Aantal geweigerde Tomcat-sessies | Aantal | Totaal
Ja | Micro soft. AppPlatform/lente | YoungGenPromotedBytes | Promo veren tot jonge generatie gegevens grootte | Bytes | Maximum
Ja | Microsoft.Automation/automationAccounts | TotalJob | Totaal aantal taken | Aantal | Totaal
Ja | Microsoft.Automation/automationAccounts | TotalUpdateDeploymentMachineRuns | Totaal aantal uitgevoerde update-implementatie computers | Aantal | Totaal
Ja | Microsoft.Automation/automationAccounts | TotalUpdateDeploymentRuns | Totaal aantal uitgevoerde update-implementaties | Aantal | Totaal
Nee | Microsoft.Batch/batchAccounts | CoreCount | Aantal toegewezen kernen | Aantal | Totaal
Nee | Microsoft.Batch/batchAccounts | CreatingNodeCount | Aantal knoop punten maken | Aantal | Totaal
Nee | Microsoft.Batch/batchAccounts | IdleNodeCount | Aantal niet-actieve knoop punten | Aantal | Totaal
Ja | Microsoft.Batch/batchAccounts | JobDeleteCompleteEvent | Voltooide gebeurtenissen van taak verwijderen | Aantal | Totaal
Ja | Microsoft.Batch/batchAccounts | JobDeleteStartEvent | Taak begin gebeurtenissen verwijderen | Aantal | Totaal
Ja | Microsoft.Batch/batchAccounts | JobDisableCompleteEvent | Voltooide gebeurtenissen voor taak uitschakelen | Aantal | Totaal
Ja | Microsoft.Batch/batchAccounts | JobDisableStartEvent | Taak start gebeurtenissen uitschakelen | Aantal | Totaal
Ja | Microsoft.Batch/batchAccounts | JobStartEvent | Taak begin gebeurtenissen | Aantal | Totaal
Ja | Microsoft.Batch/batchAccounts | JobTerminateCompleteEvent | Voltooide gebeurtenissen voor taak beëindigen | Aantal | Totaal
Ja | Microsoft.Batch/batchAccounts | JobTerminateStartEvent | Taak start gebeurtenissen beëindigen | Aantal | Totaal
Nee | Microsoft.Batch/batchAccounts | LeavingPoolNodeCount | Aantal groeps knooppunten verlaten | Aantal | Totaal
Nee | Microsoft.Batch/batchAccounts | LowPriorityCoreCount | Aantal LowPriority kernen | Aantal | Totaal
Nee | Microsoft.Batch/batchAccounts | OfflineNodeCount | Aantal offline knooppunten | Aantal | Totaal
Ja | Microsoft.Batch/batchAccounts | PoolCreateEvent | Groeps gebeurtenissen maken | Aantal | Totaal
Ja | Microsoft.Batch/batchAccounts | PoolDeleteCompleteEvent | Voltooide gebeurtenissen van groep verwijderen | Aantal | Totaal
Ja | Microsoft.Batch/batchAccounts | PoolDeleteStartEvent | Begin gebeurtenissen groep verwijderen | Aantal | Totaal
Ja | Microsoft.Batch/batchAccounts | PoolResizeCompleteEvent | Volledige gebeurtenissen voor het wijzigen van de pool | Aantal | Totaal
Ja | Microsoft.Batch/batchAccounts | PoolResizeStartEvent | Begin gebeurtenissen van groeps grootte wijzigen | Aantal | Totaal
Nee | Microsoft.Batch/batchAccounts | PreemptedNodeCount | Aantal knoop punten in herhaling | Aantal | Totaal
Nee | Microsoft.Batch/batchAccounts | RebootingNodeCount | Aantal knoop punten opnieuw opstarten | Aantal | Totaal
Nee | Microsoft.Batch/batchAccounts | ReimagingNodeCount | Telling van het aantal knoop punten | Aantal | Totaal
Nee | Microsoft.Batch/batchAccounts | RunningNodeCount | Aantal actieve knoop punten | Aantal | Totaal
Nee | Microsoft.Batch/batchAccounts | StartingNodeCount | Begin aantal knoop punten | Aantal | Totaal
Nee | Microsoft.Batch/batchAccounts | StartTaskFailedNodeCount | Aantal mislukte knoop punten van begin taak | Aantal | Totaal
Ja | Microsoft.Batch/batchAccounts | TaskCompleteEvent | Taak voltooid gebeurtenissen | Aantal | Totaal
Ja | Microsoft.Batch/batchAccounts | TaskFailEvent | Taak fout gebeurtenissen | Aantal | Totaal
Ja | Microsoft.Batch/batchAccounts | TaskStartEvent | Taak begin gebeurtenissen | Aantal | Totaal
Nee | Microsoft.Batch/batchAccounts | TotalLowPriorityNodeCount | Aantal knoop punten met een lage prioriteit | Aantal | Totaal
Nee | Microsoft.Batch/batchAccounts | TotalNodeCount | Aantal toegewezen knoop punten | Aantal | Totaal
Nee | Microsoft.Batch/batchAccounts | UnusableNodeCount | Aantal niet-bruikbare knoop punten | Aantal | Totaal
Nee | Microsoft.Batch/batchAccounts | WaitingForStartTaskNodeCount | Wachten op aantal begin taak knooppunten | Aantal | Totaal
Ja | Micro soft. BatchAI/werk ruimten | Actieve kernen | Actieve kernen | Aantal | Average
Ja | Micro soft. BatchAI/werk ruimten | Actieve knoop punten | Actieve knoop punten | Aantal | Average
Ja | Micro soft. BatchAI/werk ruimten | Niet-actieve kernen | Niet-actieve kernen | Aantal | Average
Ja | Micro soft. BatchAI/werk ruimten | Niet-actieve knoop punten | Niet-actieve knoop punten | Aantal | Average
Ja | Micro soft. BatchAI/werk ruimten | Taak is voltooid | Taak is voltooid | Aantal | Totaal
Ja | Micro soft. BatchAI/werk ruimten | Taak verzonden | Taak verzonden | Aantal | Totaal
Ja | Micro soft. BatchAI/werk ruimten | Kernen verlaten | Kernen verlaten | Aantal | Average
Ja | Micro soft. BatchAI/werk ruimten | Knoop punten verlaten | Knoop punten verlaten | Aantal | Average
Ja | Micro soft. BatchAI/werk ruimten | Afgebroken kernen | Afgebroken kernen | Aantal | Average
Ja | Micro soft. BatchAI/werk ruimten | Knoop punten die zijn afgebroken | Knoop punten die zijn afgebroken | Aantal | Average
Ja | Micro soft. BatchAI/werk ruimten | Percentage quotum gebruik | Percentage quotum gebruik | Aantal | Average
Ja | Micro soft. BatchAI/werk ruimten | Totaal aantal kernen | Totaal aantal kernen | Aantal | Average
Ja | Micro soft. BatchAI/werk ruimten | Totaal aantal knoop punten | Totaal aantal knoop punten | Aantal | Average
Ja | Micro soft. BatchAI/werk ruimten | Onbruikbaar aantal kern geheugens | Onbruikbaar aantal kern geheugens | Aantal | Average
Ja | Micro soft. BatchAI/werk ruimten | Niet-bruikbare knoop punten | Niet-bruikbare knoop punten | Aantal | Average
Ja | Microsoft.Blockchain/blockchainMembers | ConnectionAccepted | Geaccepteerde verbindingen | Aantal | Totaal
Ja | Microsoft.Blockchain/blockchainMembers | ConnectionActive | Actieve verbindingen | Aantal | Average
Ja | Microsoft.Blockchain/blockchainMembers | ConnectionHandled | Afgehandelde verbindingen | Aantal | Totaal
Ja | Microsoft.Blockchain/blockchainMembers | CpuUsagePercentageInDouble | Percentage CPU-gebruik | Procent | Maximum
Ja | Microsoft.Blockchain/blockchainMembers | IOReadBytes | I/o gelezen bytes | Bytes | Totaal
Ja | Microsoft.Blockchain/blockchainMembers | IOWriteBytes | I/o-schrijf bytes | Bytes | Totaal
Ja | Microsoft.Blockchain/blockchainMembers | MemoryLimit | Geheugen limiet | Bytes | Average
Ja | Microsoft.Blockchain/blockchainMembers | MemoryUsage | Geheugengebruik | Bytes | Average
Ja | Microsoft.Blockchain/blockchainMembers | MemoryUsagePercentageInDouble | Percentage geheugen gebruik | Procent | Average
Ja | Microsoft.Blockchain/blockchainMembers | PendingTransactions | Trans acties in behandeling | Aantal | Average
Ja | Microsoft.Blockchain/blockchainMembers | ProcessedBlocks | Verwerkte blokken | Aantal | Totaal
Ja | Microsoft.Blockchain/blockchainMembers | ProcessedTransactions | Verwerkte trans acties | Aantal | Totaal
Ja | Microsoft.Blockchain/blockchainMembers | QueuedTransactions | Trans acties in de wachtrij | Aantal | Average
Ja | Microsoft.Blockchain/blockchainMembers | RequestHandled | Verwerkte aanvragen | Aantal | Totaal
Ja | Microsoft.Blockchain/blockchainMembers | StorageUsage | Opslag gebruik | Bytes | Average
Ja | Microsoft.Cache/redis | cachehits | Cache treffers | Aantal | Totaal
Ja | Microsoft.Cache/redis | cachehits0 | Cache treffers (Shard 0) | Aantal | Totaal
Ja | Microsoft.Cache/redis | cachehits1 | Cache treffers (Shard 1) | Aantal | Totaal
Ja | Microsoft.Cache/redis | cachehits2 | Cache treffers (Shard 2) | Aantal | Totaal
Ja | Microsoft.Cache/redis | cachehits3 | Cache treffers (Shard 3) | Aantal | Totaal
Ja | Microsoft.Cache/redis | cachehits4 | Cache treffers (Shard 4) | Aantal | Totaal
Ja | Microsoft.Cache/redis | cachehits5 | Cache treffers (Shard 5) | Aantal | Totaal
Ja | Microsoft.Cache/redis | cachehits6 | Cache treffers (Shard 6) | Aantal | Totaal
Ja | Microsoft.Cache/redis | cachehits7 | Cache treffers (Shard 7) | Aantal | Totaal
Ja | Microsoft.Cache/redis | cachehits8 | Cache treffers (Shard 8) | Aantal | Totaal
Ja | Microsoft.Cache/redis | cachehits9 | Cache treffers (Shard 9) | Aantal | Totaal
Ja | Microsoft.Cache/redis | cacheLatency | Cache latentie micro seconden (preview-versie) | Aantal | Average
Ja | Microsoft.Cache/redis | cachemisses | Cache missers | Aantal | Totaal
Ja | Microsoft.Cache/redis | cachemisses0 | Cache missers (Shard 0) | Aantal | Totaal
Ja | Microsoft.Cache/redis | cachemisses1 | Cache missers (Shard 1) | Aantal | Totaal
Ja | Microsoft.Cache/redis | cachemisses2 | Cache missers (Shard 2) | Aantal | Totaal
Ja | Microsoft.Cache/redis | cachemisses3 | Cache missers (Shard 3) | Aantal | Totaal
Ja | Microsoft.Cache/redis | cachemisses4 | Cache missers (Shard 4) | Aantal | Totaal
Ja | Microsoft.Cache/redis | cachemisses5 | Cache missers (Shard 5) | Aantal | Totaal
Ja | Microsoft.Cache/redis | cachemisses6 | Cache missers (Shard 6) | Aantal | Totaal
Ja | Microsoft.Cache/redis | cachemisses7 | Cache missers (Shard 7) | Aantal | Totaal
Ja | Microsoft.Cache/redis | cachemisses8 | Cache missers (Shard 8) | Aantal | Totaal
Ja | Microsoft.Cache/redis | cachemisses9 | Cache missers (Shard 9) | Aantal | Totaal
Ja | Microsoft.Cache/redis | cacheRead | Lees bewerking in cache | BytesPerSecond | Maximum
Ja | Microsoft.Cache/redis | cacheRead0 | Cache gelezen (Shard 0) | BytesPerSecond | Maximum
Ja | Microsoft.Cache/redis | cacheRead1 | Lees bewerking in cache (Shard 1) | BytesPerSecond | Maximum
Ja | Microsoft.Cache/redis | cacheRead2 | Lees bewerking in cache (Shard 2) | BytesPerSecond | Maximum
Ja | Microsoft.Cache/redis | cacheRead3 | Lees bewerking in cache (Shard 3) | BytesPerSecond | Maximum
Ja | Microsoft.Cache/redis | cacheRead4 | Lees bewerking in cache (Shard 4) | BytesPerSecond | Maximum
Ja | Microsoft.Cache/redis | cacheRead5 | Lees bewerking in cache (Shard 5) | BytesPerSecond | Maximum
Ja | Microsoft.Cache/redis | cacheRead6 | Lees bewerking in cache (Shard 6) | BytesPerSecond | Maximum
Ja | Microsoft.Cache/redis | cacheRead7 | Lees bewerking in cache (Shard 7) | BytesPerSecond | Maximum
Ja | Microsoft.Cache/redis | cacheRead8 | Lees bewerking in cache (Shard 8) | BytesPerSecond | Maximum
Ja | Microsoft.Cache/redis | cacheRead9 | Lees bewerking in cache (Shard 9) | BytesPerSecond | Maximum
Ja | Microsoft.Cache/redis | cacheWrite | Cache schrijven | BytesPerSecond | Maximum
Ja | Microsoft.Cache/redis | cacheWrite0 | Cache schrijven (Shard 0) | BytesPerSecond | Maximum
Ja | Microsoft.Cache/redis | cacheWrite1 | Cache schrijven (Shard 1) | BytesPerSecond | Maximum
Ja | Microsoft.Cache/redis | cacheWrite2 | Cache schrijven (Shard 2) | BytesPerSecond | Maximum
Ja | Microsoft.Cache/redis | cacheWrite3 | Cache schrijven (Shard 3) | BytesPerSecond | Maximum
Ja | Microsoft.Cache/redis | cacheWrite4 | Cache schrijven (Shard 4) | BytesPerSecond | Maximum
Ja | Microsoft.Cache/redis | cacheWrite5 | Cache schrijven (Shard 5) | BytesPerSecond | Maximum
Ja | Microsoft.Cache/redis | cacheWrite6 | Cache schrijven (Shard 6) | BytesPerSecond | Maximum
Ja | Microsoft.Cache/redis | cacheWrite7 | Cache schrijven (Shard 7) | BytesPerSecond | Maximum
Ja | Microsoft.Cache/redis | cacheWrite8 | Cache schrijven (Shard 8) | BytesPerSecond | Maximum
Ja | Microsoft.Cache/redis | cacheWrite9 | Cache schrijven (Shard 9) | BytesPerSecond | Maximum
Ja | Microsoft.Cache/redis | connectedclients | Verbonden clients | Aantal | Maximum
Ja | Microsoft.Cache/redis | connectedclients0 | Verbonden clients (Shard 0) | Aantal | Maximum
Ja | Microsoft.Cache/redis | connectedclients1 | Verbonden clients (Shard 1) | Aantal | Maximum
Ja | Microsoft.Cache/redis | connectedclients2 | Verbonden clients (Shard 2) | Aantal | Maximum
Ja | Microsoft.Cache/redis | connectedclients3 | Verbonden clients (Shard 3) | Aantal | Maximum
Ja | Microsoft.Cache/redis | connectedclients4 | Verbonden clients (Shard 4) | Aantal | Maximum
Ja | Microsoft.Cache/redis | connectedclients5 | Verbonden clients (Shard 5) | Aantal | Maximum
Ja | Microsoft.Cache/redis | connectedclients6 | Verbonden clients (Shard 6) | Aantal | Maximum
Ja | Microsoft.Cache/redis | connectedclients7 | Verbonden clients (Shard 7) | Aantal | Maximum
Ja | Microsoft.Cache/redis | connectedclients8 | Verbonden clients (Shard 8) | Aantal | Maximum
Ja | Microsoft.Cache/redis | connectedclients9 | Verbonden clients (Shard 9) | Aantal | Maximum
Ja | Microsoft.Cache/redis | fouten | Fouten | Aantal | Maximum
Ja | Microsoft.Cache/redis | evictedkeys | Verwijderde sleutels | Aantal | Totaal
Ja | Microsoft.Cache/redis | evictedkeys0 | Verwijderde sleutels (Shard 0) | Aantal | Totaal
Ja | Microsoft.Cache/redis | evictedkeys1 | Verwijderde sleutels (Shard 1) | Aantal | Totaal
Ja | Microsoft.Cache/redis | evictedkeys2 | Verwijderde sleutels (Shard 2) | Aantal | Totaal
Ja | Microsoft.Cache/redis | evictedkeys3 | Verwijderde sleutels (Shard 3) | Aantal | Totaal
Ja | Microsoft.Cache/redis | evictedkeys4 | Verwijderde sleutels (Shard 4) | Aantal | Totaal
Ja | Microsoft.Cache/redis | evictedkeys5 | Verwijderde sleutels (Shard 5) | Aantal | Totaal
Ja | Microsoft.Cache/redis | evictedkeys6 | Verwijderde sleutels (Shard 6) | Aantal | Totaal
Ja | Microsoft.Cache/redis | evictedkeys7 | Verwijderde sleutels (Shard 7) | Aantal | Totaal
Ja | Microsoft.Cache/redis | evictedkeys8 | Verwijderde sleutels (Shard 8) | Aantal | Totaal
Ja | Microsoft.Cache/redis | evictedkeys9 | Verwijderde sleutels (Shard 9) | Aantal | Totaal
Ja | Microsoft.Cache/redis | expiredkeys | Verlopen sleutels | Aantal | Totaal
Ja | Microsoft.Cache/redis | expiredkeys0 | Verlopen sleutels (Shard 0) | Aantal | Totaal
Ja | Microsoft.Cache/redis | expiredkeys1 | Verlopen sleutels (Shard 1) | Aantal | Totaal
Ja | Microsoft.Cache/redis | expiredkeys2 | Verlopen sleutels (Shard 2) | Aantal | Totaal
Ja | Microsoft.Cache/redis | expiredkeys3 | Verlopen sleutels (Shard 3) | Aantal | Totaal
Ja | Microsoft.Cache/redis | expiredkeys4 | Verlopen sleutels (Shard 4) | Aantal | Totaal
Ja | Microsoft.Cache/redis | expiredkeys5 | Verlopen sleutels (Shard 5) | Aantal | Totaal
Ja | Microsoft.Cache/redis | expiredkeys6 | Verlopen sleutels (Shard 6) | Aantal | Totaal
Ja | Microsoft.Cache/redis | expiredkeys7 | Verlopen sleutels (Shard 7) | Aantal | Totaal
Ja | Microsoft.Cache/redis | expiredkeys8 | Verlopen sleutels (Shard 8) | Aantal | Totaal
Ja | Microsoft.Cache/redis | expiredkeys9 | Verlopen sleutels (Shard 9) | Aantal | Totaal
Ja | Microsoft.Cache/redis | getcommands | Methode | Aantal | Totaal
Ja | Microsoft.Cache/redis | getcommands0 | Hiermee wordt opgehaald (Shard 0) | Aantal | Totaal
Ja | Microsoft.Cache/redis | getcommands1 | Hiermee wordt opgehaald (Shard 1) | Aantal | Totaal
Ja | Microsoft.Cache/redis | getcommands2 | Hiermee wordt opgehaald (Shard 2) | Aantal | Totaal
Ja | Microsoft.Cache/redis | getcommands3 | Hiermee wordt opgehaald (Shard 3) | Aantal | Totaal
Ja | Microsoft.Cache/redis | getcommands4 | Hiermee wordt opgehaald (Shard 4) | Aantal | Totaal
Ja | Microsoft.Cache/redis | getcommands5 | Hiermee wordt opgehaald (Shard 5) | Aantal | Totaal
Ja | Microsoft.Cache/redis | getcommands6 | Hiermee wordt opgehaald (Shard 6) | Aantal | Totaal
Ja | Microsoft.Cache/redis | getcommands7 | Hiermee wordt opgehaald (Shard 7) | Aantal | Totaal
Ja | Microsoft.Cache/redis | getcommands8 | Hiermee wordt opgehaald (Shard 8) | Aantal | Totaal
Ja | Microsoft.Cache/redis | getcommands9 | Hiermee wordt opgehaald (Shard 9) | Aantal | Totaal
Ja | Microsoft.Cache/redis | operationsPerSecond | Bewerkingen per seconde | Aantal | Maximum
Ja | Microsoft.Cache/redis | operationsPerSecond0 | Bewerkingen per seconde (Shard 0) | Aantal | Maximum
Ja | Microsoft.Cache/redis | operationsPerSecond1 | Bewerkingen per seconde (Shard 1) | Aantal | Maximum
Ja | Microsoft.Cache/redis | operationsPerSecond2 | Bewerkingen per seconde (Shard 2) | Aantal | Maximum
Ja | Microsoft.Cache/redis | operationsPerSecond3 | Bewerkingen per seconde (Shard 3) | Aantal | Maximum
Ja | Microsoft.Cache/redis | operationsPerSecond4 | Bewerkingen per seconde (Shard 4) | Aantal | Maximum
Ja | Microsoft.Cache/redis | operationsPerSecond5 | Bewerkingen per seconde (Shard 5) | Aantal | Maximum
Ja | Microsoft.Cache/redis | operationsPerSecond6 | Bewerkingen per seconde (Shard 6) | Aantal | Maximum
Ja | Microsoft.Cache/redis | operationsPerSecond7 | Bewerkingen per seconde (Shard 7) | Aantal | Maximum
Ja | Microsoft.Cache/redis | operationsPerSecond8 | Bewerkingen per seconde (Shard 8) | Aantal | Maximum
Ja | Microsoft.Cache/redis | operationsPerSecond9 | Bewerkingen per seconde (Shard 9) | Aantal | Maximum
Ja | Microsoft.Cache/redis | percentProcessorTime | CPU | Procent | Maximum
Ja | Microsoft.Cache/redis | percentProcessorTime0 | CPU (Shard 0) | Procent | Maximum
Ja | Microsoft.Cache/redis | percentProcessorTime1 | CPU (Shard 1) | Procent | Maximum
Ja | Microsoft.Cache/redis | percentProcessorTime2 | CPU (Shard 2) | Procent | Maximum
Ja | Microsoft.Cache/redis | percentProcessorTime3 | CPU (Shard 3) | Procent | Maximum
Ja | Microsoft.Cache/redis | percentProcessorTime4 | CPU (Shard 4) | Procent | Maximum
Ja | Microsoft.Cache/redis | percentProcessorTime5 | CPU (Shard 5) | Procent | Maximum
Ja | Microsoft.Cache/redis | percentProcessorTime6 | CPU (Shard 6) | Procent | Maximum
Ja | Microsoft.Cache/redis | percentProcessorTime7 | CPU (Shard 7) | Procent | Maximum
Ja | Microsoft.Cache/redis | percentProcessorTime8 | CPU (Shard 8) | Procent | Maximum
Ja | Microsoft.Cache/redis | percentProcessorTime9 | CPU (Shard 9) | Procent | Maximum
Ja | Microsoft.Cache/redis | serverLoad | Server belasting | Procent | Maximum
Ja | Microsoft.Cache/redis | serverLoad0 | Server belasting (Shard 0) | Procent | Maximum
Ja | Microsoft.Cache/redis | serverLoad1 | Server belasting (Shard 1) | Procent | Maximum
Ja | Microsoft.Cache/redis | serverLoad2 | Server belasting (Shard 2) | Procent | Maximum
Ja | Microsoft.Cache/redis | serverLoad3 | Server belasting (Shard 3) | Procent | Maximum
Ja | Microsoft.Cache/redis | serverLoad4 | Server belasting (Shard 4) | Procent | Maximum
Ja | Microsoft.Cache/redis | serverLoad5 | Server belasting (Shard 5) | Procent | Maximum
Ja | Microsoft.Cache/redis | serverLoad6 | Server belasting (Shard 6) | Procent | Maximum
Ja | Microsoft.Cache/redis | serverLoad7 | Server belasting (Shard 7) | Procent | Maximum
Ja | Microsoft.Cache/redis | serverLoad8 | Server belasting (Shard 8) | Procent | Maximum
Ja | Microsoft.Cache/redis | serverLoad9 | Server belasting (Shard 9) | Procent | Maximum
Ja | Microsoft.Cache/redis | setcommands | Sets | Aantal | Totaal
Ja | Microsoft.Cache/redis | setcommands0 | Sets (Shard 0) | Aantal | Totaal
Ja | Microsoft.Cache/redis | setcommands1 | Sets (Shard 1) | Aantal | Totaal
Ja | Microsoft.Cache/redis | setcommands2 | Sets (Shard 2) | Aantal | Totaal
Ja | Microsoft.Cache/redis | setcommands3 | Sets (Shard 3) | Aantal | Totaal
Ja | Microsoft.Cache/redis | setcommands4 | Sets (Shard 4) | Aantal | Totaal
Ja | Microsoft.Cache/redis | setcommands5 | Sets (Shard 5) | Aantal | Totaal
Ja | Microsoft.Cache/redis | setcommands6 | Sets (Shard 6) | Aantal | Totaal
Ja | Microsoft.Cache/redis | setcommands7 | Sets (Shard 7) | Aantal | Totaal
Ja | Microsoft.Cache/redis | setcommands8 | Sets (Shard 8) | Aantal | Totaal
Ja | Microsoft.Cache/redis | setcommands9 | Sets (Shard 9) | Aantal | Totaal
Ja | Microsoft.Cache/redis | totalcommandsprocessed | Totaal aantal bewerkingen | Aantal | Totaal
Ja | Microsoft.Cache/redis | totalcommandsprocessed0 | Totaal aantal bewerkingen (Shard 0) | Aantal | Totaal
Ja | Microsoft.Cache/redis | totalcommandsprocessed1 | Totaal aantal bewerkingen (Shard 1) | Aantal | Totaal
Ja | Microsoft.Cache/redis | totalcommandsprocessed2 | Totaal aantal bewerkingen (Shard 2) | Aantal | Totaal
Ja | Microsoft.Cache/redis | totalcommandsprocessed3 | Totaal aantal bewerkingen (Shard 3) | Aantal | Totaal
Ja | Microsoft.Cache/redis | totalcommandsprocessed4 | Totaal aantal bewerkingen (Shard 4) | Aantal | Totaal
Ja | Microsoft.Cache/redis | totalcommandsprocessed5 | Totaal aantal bewerkingen (Shard 5) | Aantal | Totaal
Ja | Microsoft.Cache/redis | totalcommandsprocessed6 | Totaal aantal bewerkingen (Shard 6) | Aantal | Totaal
Ja | Microsoft.Cache/redis | totalcommandsprocessed7 | Totaal aantal bewerkingen (Shard 7) | Aantal | Totaal
Ja | Microsoft.Cache/redis | totalcommandsprocessed8 | Totaal aantal bewerkingen (Shard 8) | Aantal | Totaal
Ja | Microsoft.Cache/redis | totalcommandsprocessed9 | Totaal aantal bewerkingen (Shard 9) | Aantal | Totaal
Ja | Microsoft.Cache/redis | totalkeys | Totaal aantal sleutels | Aantal | Maximum
Ja | Microsoft.Cache/redis | totalkeys0 | Totaal aantal sleutels (Shard 0) | Aantal | Maximum
Ja | Microsoft.Cache/redis | totalkeys1 | Totaal aantal sleutels (Shard 1) | Aantal | Maximum
Ja | Microsoft.Cache/redis | totalkeys2 | Totaal aantal sleutels (Shard 2) | Aantal | Maximum
Ja | Microsoft.Cache/redis | totalkeys3 | Totaal aantal sleutels (Shard 3) | Aantal | Maximum
Ja | Microsoft.Cache/redis | totalkeys4 | Totaal aantal sleutels (Shard 4) | Aantal | Maximum
Ja | Microsoft.Cache/redis | totalkeys5 | Totaal aantal sleutels (Shard 5) | Aantal | Maximum
Ja | Microsoft.Cache/redis | totalkeys6 | Totaal aantal sleutels (Shard 6) | Aantal | Maximum
Ja | Microsoft.Cache/redis | totalkeys7 | Totaal aantal sleutels (Shard 7) | Aantal | Maximum
Ja | Microsoft.Cache/redis | totalkeys8 | Totaal aantal sleutels (Shard 8) | Aantal | Maximum
Ja | Microsoft.Cache/redis | totalkeys9 | Totaal aantal sleutels (Shard 9) | Aantal | Maximum
Ja | Microsoft.Cache/redis | usedmemory | Gebruikt geheugen | Bytes | Maximum
Ja | Microsoft.Cache/redis | usedmemory0 | Gebruikt geheugen (Shard 0) | Bytes | Maximum
Ja | Microsoft.Cache/redis | usedmemory1 | Gebruikt geheugen (Shard 1) | Bytes | Maximum
Ja | Microsoft.Cache/redis | usedmemory2 | Gebruikt geheugen (Shard 2) | Bytes | Maximum
Ja | Microsoft.Cache/redis | usedmemory3 | Gebruikt geheugen (Shard 3) | Bytes | Maximum
Ja | Microsoft.Cache/redis | usedmemory4 | Gebruikt geheugen (Shard 4) | Bytes | Maximum
Ja | Microsoft.Cache/redis | usedmemory5 | Gebruikt geheugen (Shard 5) | Bytes | Maximum
Ja | Microsoft.Cache/redis | usedmemory6 | Gebruikt geheugen (Shard 6) | Bytes | Maximum
Ja | Microsoft.Cache/redis | usedmemory7 | Gebruikt geheugen (Shard 7) | Bytes | Maximum
Ja | Microsoft.Cache/redis | usedmemory8 | Gebruikt geheugen (Shard 8) | Bytes | Maximum
Ja | Microsoft.Cache/redis | usedmemory9 | Gebruikt geheugen (Shard 9) | Bytes | Maximum
Ja | Microsoft.Cache/redis | usedmemorypercentage | Percentage gebruikt geheugen | Procent | Maximum
Ja | Microsoft.Cache/redis | usedmemoryRss | Gebruikte geheugen-RSS | Bytes | Maximum
Ja | Microsoft.Cache/redis | usedmemoryRss0 | Gebruikt geheugen RSS (Shard 0) | Bytes | Maximum
Ja | Microsoft.Cache/redis | usedmemoryRss1 | Gebruikte geheugen RSS (Shard 1) | Bytes | Maximum
Ja | Microsoft.Cache/redis | usedmemoryRss2 | Gebruikte geheugen RSS (Shard 2) | Bytes | Maximum
Ja | Microsoft.Cache/redis | usedmemoryRss3 | Gebruikte geheugen RSS (Shard 3) | Bytes | Maximum
Ja | Microsoft.Cache/redis | usedmemoryRss4 | Gebruikte geheugen RSS (Shard 4) | Bytes | Maximum
Ja | Microsoft.Cache/redis | usedmemoryRss5 | Gebruikte geheugen RSS (Shard 5) | Bytes | Maximum
Ja | Microsoft.Cache/redis | usedmemoryRss6 | Gebruikte geheugen RSS (Shard 6) | Bytes | Maximum
Ja | Microsoft.Cache/redis | usedmemoryRss7 | Gebruikte geheugen RSS (Shard 7) | Bytes | Maximum
Ja | Microsoft.Cache/redis | usedmemoryRss8 | Gebruikt geheugen RSS (Shard 8) | Bytes | Maximum
Ja | Microsoft.Cache/redis | usedmemoryRss9 | Gebruikte geheugen RSS (Shard 9) | Bytes | Maximum
Nee | Micro soft. ClassicCompute/domein naam/sleuven/rollen | Gelezen bytes per seconde | Schijf lezen | BytesPerSecond | Average
Ja | Micro soft. ClassicCompute/domein naam/sleuven/rollen | Lees bewerkingen op de schijf per seconde | Lees bewerkingen op de schijf per seconde | CountPerSecond | Average
Nee | Micro soft. ClassicCompute/domein naam/sleuven/rollen | Geschreven bytes per seconde | Schijf schrijven | BytesPerSecond | Average
Ja | Micro soft. ClassicCompute/domein naam/sleuven/rollen | Schrijf bewerkingen op de schijf per seconde | Schrijf bewerkingen op de schijf per seconde | CountPerSecond | Average
Ja | Micro soft. ClassicCompute/domein naam/sleuven/rollen | Netwerk in | Netwerk in | Bytes | Totaal
Ja | Micro soft. ClassicCompute/domein naam/sleuven/rollen | Netwerk uit | Netwerk uit | Bytes | Totaal
Ja | Micro soft. ClassicCompute/domein naam/sleuven/rollen | Percentage CPU | Percentage CPU | Procent | Average
Nee | Microsoft.ClassicCompute/virtualMachines | Gelezen bytes per seconde | Schijf lezen | BytesPerSecond | Average
Ja | Microsoft.ClassicCompute/virtualMachines | Lees bewerkingen op de schijf per seconde | Lees bewerkingen op de schijf per seconde | CountPerSecond | Average
Nee | Microsoft.ClassicCompute/virtualMachines | Geschreven bytes per seconde | Schijf schrijven | BytesPerSecond | Average
Ja | Microsoft.ClassicCompute/virtualMachines | Schrijf bewerkingen op de schijf per seconde | Schrijf bewerkingen op de schijf per seconde | CountPerSecond | Average
Ja | Microsoft.ClassicCompute/virtualMachines | Netwerk in | Netwerk in | Bytes | Totaal
Ja | Microsoft.ClassicCompute/virtualMachines | Netwerk uit | Netwerk uit | Bytes | Totaal
Ja | Microsoft.ClassicCompute/virtualMachines | Percentage CPU | Percentage CPU | Procent | Average
Ja | Microsoft.ClassicStorage/storageAccounts | Beschikbaarheid | Beschikbaarheid | Procent | Average
Ja | Microsoft.ClassicStorage/storageAccounts | Uitgaand verkeer | Uitgaand verkeer | Bytes | Totaal
Ja | Microsoft.ClassicStorage/storageAccounts | Binnenkomend | Binnenkomend | Bytes | Totaal
Ja | Microsoft.ClassicStorage/storageAccounts | SuccessE2ELatency | Geslaagde E2E-latentie | Milliseconden | Average
Ja | Microsoft.ClassicStorage/storageAccounts | SuccessServerLatency | Geslaagde serverlatentie | Milliseconden | Average
Ja | Microsoft.ClassicStorage/storageAccounts | Transacties | Transacties | Aantal | Totaal
Nee | Microsoft.ClassicStorage/storageAccounts | UsedCapacity | Gebruikte capaciteit | Bytes | Average
Ja | Micro soft. ClassicStorage/Storage accounts/blobServices | Beschikbaarheid | Beschikbaarheid | Procent | Average
Nee | Micro soft. ClassicStorage/Storage accounts/blobServices | BlobCapacity | Blobcapaciteit | Bytes | Average
Nee | Micro soft. ClassicStorage/Storage accounts/blobServices | BlobCount | Aantal blobs | Aantal | Average
Ja | Micro soft. ClassicStorage/Storage accounts/blobServices | ContainerCount | Aantal blobcontainers | Aantal | Average
Ja | Micro soft. ClassicStorage/Storage accounts/blobServices | Uitgaand verkeer | Uitgaand verkeer | Bytes | Totaal
Nee | Micro soft. ClassicStorage/Storage accounts/blobServices | IndexCapacity | Indexcapaciteit | Bytes | Average
Ja | Micro soft. ClassicStorage/Storage accounts/blobServices | Binnenkomend | Binnenkomend | Bytes | Totaal
Ja | Micro soft. ClassicStorage/Storage accounts/blobServices | SuccessE2ELatency | Geslaagde E2E-latentie | Milliseconden | Average
Ja | Micro soft. ClassicStorage/Storage accounts/blobServices | SuccessServerLatency | Geslaagde serverlatentie | Milliseconden | Average
Ja | Micro soft. ClassicStorage/Storage accounts/blobServices | Transacties | Transacties | Aantal | Totaal
Ja | Micro soft. ClassicStorage/Storage accounts/fileServices | Beschikbaarheid | Beschikbaarheid | Procent | Average
Ja | Micro soft. ClassicStorage/Storage accounts/fileServices | Uitgaand verkeer | Uitgaand verkeer | Bytes | Totaal
Nee | Micro soft. ClassicStorage/Storage accounts/fileServices | FileCapacity | Bestands capaciteit | Bytes | Average
Nee | Micro soft. ClassicStorage/Storage accounts/fileServices | FileCount | Aantal bestanden | Aantal | Average
Nee | Micro soft. ClassicStorage/Storage accounts/fileServices | FileShareCount | Aantal bestands shares | Aantal | Average
Nee | Micro soft. ClassicStorage/Storage accounts/fileServices | FileShareQuota | Quota grootte van bestands share | Bytes | Average
Nee | Micro soft. ClassicStorage/Storage accounts/fileServices | FileShareSnapshotCount | Aantal moment opnamen van bestands shares | Aantal | Average
Nee | Micro soft. ClassicStorage/Storage accounts/fileServices | FileShareSnapshotSize | Grootte van moment opname van bestands share | Bytes | Average
Ja | Micro soft. ClassicStorage/Storage accounts/fileServices | Binnenkomend | Binnenkomend | Bytes | Totaal
Ja | Micro soft. ClassicStorage/Storage accounts/fileServices | SuccessE2ELatency | Geslaagde E2E-latentie | Milliseconden | Average
Ja | Micro soft. ClassicStorage/Storage accounts/fileServices | SuccessServerLatency | Geslaagde serverlatentie | Milliseconden | Average
Ja | Micro soft. ClassicStorage/Storage accounts/fileServices | Transacties | Transacties | Aantal | Totaal
Ja | Micro soft. ClassicStorage/Storage accounts/queueServices | Beschikbaarheid | Beschikbaarheid | Procent | Average
Ja | Micro soft. ClassicStorage/Storage accounts/queueServices | Uitgaand verkeer | Uitgaand verkeer | Bytes | Totaal
Ja | Micro soft. ClassicStorage/Storage accounts/queueServices | Binnenkomend | Binnenkomend | Bytes | Totaal
Ja | Micro soft. ClassicStorage/Storage accounts/queueServices | QueueCapacity | Wachtrijcapaciteit | Bytes | Average
Ja | Micro soft. ClassicStorage/Storage accounts/queueServices | QueueCount | Aantal wachtrijen | Aantal | Average
Ja | Micro soft. ClassicStorage/Storage accounts/queueServices | QueueMessageCount | Aantal wachtrijberichten | Aantal | Average
Ja | Micro soft. ClassicStorage/Storage accounts/queueServices | SuccessE2ELatency | Geslaagde E2E-latentie | Milliseconden | Average
Ja | Micro soft. ClassicStorage/Storage accounts/queueServices | SuccessServerLatency | Geslaagde serverlatentie | Milliseconden | Average
Ja | Micro soft. ClassicStorage/Storage accounts/queueServices | Transacties | Transacties | Aantal | Totaal
Ja | Micro soft. ClassicStorage/Storage accounts/tableServices | Beschikbaarheid | Beschikbaarheid | Procent | Average
Ja | Micro soft. ClassicStorage/Storage accounts/tableServices | Uitgaand verkeer | Uitgaand verkeer | Bytes | Totaal
Ja | Micro soft. ClassicStorage/Storage accounts/tableServices | Binnenkomend | Binnenkomend | Bytes | Totaal
Ja | Micro soft. ClassicStorage/Storage accounts/tableServices | SuccessE2ELatency | Geslaagde E2E-latentie | Milliseconden | Average
Ja | Micro soft. ClassicStorage/Storage accounts/tableServices | SuccessServerLatency | Geslaagde serverlatentie | Milliseconden | Average
Ja | Micro soft. ClassicStorage/Storage accounts/tableServices | TableCapacity | Tabelcapaciteit | Bytes | Average
Ja | Micro soft. ClassicStorage/Storage accounts/tableServices | TableCount | Aantal tabellen | Aantal | Average
Ja | Micro soft. ClassicStorage/Storage accounts/tableServices | TableEntityCount | Aantal tabelentiteiten | Aantal | Average
Ja | Micro soft. ClassicStorage/Storage accounts/tableServices | Transacties | Transacties | Aantal | Totaal
Ja | Microsoft.CognitiveServices/accounts | BlockedCalls | Geblokkeerde aanroepen | Aantal | Totaal
Ja | Microsoft.CognitiveServices/accounts | CharactersTrained | Getrainde tekens | Aantal | Totaal
Ja | Microsoft.CognitiveServices/accounts | CharactersTranslated | Geconverteerde tekens | Aantal | Totaal
Ja | Microsoft.CognitiveServices/accounts | ClientErrors | Client fouten | Aantal | Totaal
Ja | Microsoft.CognitiveServices/accounts | DataIn | Inkomende gegevens | Bytes | Totaal
Ja | Microsoft.CognitiveServices/accounts | DataOut | Uitgaande gegevens | Bytes | Totaal
Ja | Microsoft.CognitiveServices/accounts | Latentie | Latentie | MilliSeconds | Average
Ja | Microsoft.CognitiveServices/accounts | ServerErrors | Server fouten | Aantal | Totaal
Ja | Microsoft.CognitiveServices/accounts | SpeechSessionDuration | Spraak sessie duur | Seconden | Totaal
Ja | Microsoft.CognitiveServices/accounts | SuccessfulCalls | Geslaagde aanroepen | Aantal | Totaal
Ja | Microsoft.CognitiveServices/accounts | TotalCalls | Totaal aantal aanroepen | Aantal | Totaal
Ja | Microsoft.CognitiveServices/accounts | TotalErrors | Totale aantal fouten | Aantal | Totaal
Ja | Microsoft.CognitiveServices/accounts | TotalTokenCalls | Totaal aantal token aanroepen | Aantal | Totaal
Ja | Microsoft.CognitiveServices/accounts | TotalTransactions | Totaal aantal trans acties | Aantal | Totaal
Ja | Microsoft.Compute/virtualMachines | Verbruikte CPU-tegoeden | Verbruikte CPU-tegoeden | Aantal | Average
Ja | Microsoft.Compute/virtualMachines | Resterende CPU-tegoeden | Resterende CPU-tegoeden | Aantal | Average
Ja | Microsoft.Compute/virtualMachines | Wachtrijlengte van gegevensschijf | Wachtrij diepte van gegevens schijf (preview-versie) | Aantal | Average
Ja | Microsoft.Compute/virtualMachines | Gegevens schijf gelezen bytes per seconde | Gegevens schijf gelezen bytes per seconde (preview) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachines | Lees bewerkingen op de gegevens schijf per seconde | Lees bewerkingen op de gegevens schijf per seconde (preview) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachines | Geschreven bytes per seconde gegevens schijf | Geschreven bytes per seconde (preview) gegevens schijf | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachines | Schrijf bewerkingen op de gegevens schijf per seconde | Schrijf bewerkingen op de gegevens schijf per seconde (preview) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachines | Gelezen bytes op de schijf | Gelezen bytes op de schijf | Bytes | Totaal
Ja | Microsoft.Compute/virtualMachines | Lees bewerkingen op de schijf per seconde | Lees bewerkingen op de schijf per seconde | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachines | Geschreven bytes op de schijf | Geschreven bytes op de schijf | Bytes | Totaal
Ja | Microsoft.Compute/virtualMachines | Schrijf bewerkingen op de schijf per seconde | Schrijf bewerkingen op de schijf per seconde | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachines | Binnenkomende stromen | Binnenkomende stromen | Aantal | Average
Ja | Microsoft.Compute/virtualMachines | Maximum aanmaak frequentie inkomende stromen | Maximum aantal aanmaak snelheden inkomende stromen (preview-versie) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachines | Netwerk in | Netwerk in Factureerbaar (afgeschaft) | Bytes | Totaal
Ja | Microsoft.Compute/virtualMachines | Totaal netwerk | Totaal netwerk | Bytes | Totaal
Ja | Microsoft.Compute/virtualMachines | Netwerk uit | Gefactureerd netwerk (afgeschaft) | Bytes | Totaal
Ja | Microsoft.Compute/virtualMachines | Totaal aantal netwerk | Totaal aantal netwerk | Bytes | Totaal
Ja | Microsoft.Compute/virtualMachines | Wachtrijlengte van besturingssysteemschijf | Wachtrij diepte van de besturingssysteem schijf (preview-versie) | Aantal | Average
Ja | Microsoft.Compute/virtualMachines | BESTURINGSSYSTEEM schijf gelezen bytes per seconde | BESTURINGSSYSTEEM schijf gelezen bytes per seconde (preview) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachines | Lees bewerkingen van de besturingssysteem schijf per seconde | Lees bewerkingen op de besturingssysteem schijf per seconde (preview) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachines | Schrijf bewerkingen op de besturingssysteem schijf per seconde | Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachines | Schrijf bewerkingen op de besturingssysteem schijf per seconde | Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachines | WACHTRIJ diepte voor het besturings systeem per schijf | WACHTRIJ diepte van de besturingssysteem schijf (afgeschaft) | Aantal | Average
Ja | Microsoft.Compute/virtualMachines | BESTURINGSSYSTEEM per schijf gelezen bytes per seconde | BESTURINGSSYSTEEM schijf gelezen bytes per seconde (afgeschaft) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachines | Lees bewerkingen per schijf voor het besturings systeem/sec. | Lees bewerkingen op de besturingssysteem schijf per seconde (afgeschaft) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachines | Per schijf geschreven bytes/sec. | BESTURINGSSYSTEEM schijf schrijf bewerkingen in bytes per seconde (afgeschaft) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachines | Schrijf bewerkingen per schijf van het besturings systeem/SEC | Schrijf bewerkingen op de besturingssysteem schijf per seconde (afgeschaft) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachines | Uitgaande stromen | Uitgaande stromen | Aantal | Average
Ja | Microsoft.Compute/virtualMachines | Maximum aanmaak frequentie van uitgaande stromen | Maximum aantal uitgaande stromen voor maken (preview) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachines | WACHTRIJ diepte per schijf | WACHTRIJ diepte van de gegevens schijf (afgeschaft) | Aantal | Average
Ja | Microsoft.Compute/virtualMachines | Per schijf gelezen bytes/sec. | Gegevens schijf gelezen bytes per seconde (afgeschaft) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachines | Lees bewerkingen per schijf/sec. | Lees bewerkingen op de gegevens schijf per seconde (afgeschaft) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachines | Schrijf bewerkingen per schijf/sec. | Gegevens schijf schrijf bewerkingen in bytes per seconde (afgeschaft) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachines | Schrijf bewerkingen per schijf/SEC | Schrijf bewerkingen op de gegevens schijf per seconde (afgeschaft) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachines | Percentage CPU | Percentage CPU | Procent | Average
Ja | Microsoft.Compute/virtualMachines | Treffer voor Premium data-schijf cache lezen | Cache geheugen voor lezen van Premium-gegevens schijf (preview-versie) | Procent | Average
Ja | Microsoft.Compute/virtualMachines | Lees missers cache Premium-gegevens schijf | Cache voor lezen van Premium-gegevens schijf (preview) | Procent | Average
Ja | Microsoft.Compute/virtualMachines | Treffer voor Premium-besturingssysteem schijf cache lezen | Treffer voor het lezen van een Premium-besturingssysteem schijf cache (preview-versie) | Procent | Average
Ja | Microsoft.Compute/virtualMachines | Leesmij voor Premium-besturingssysteem schijf cache lezen | Schijf cache voor Premium-Lees-missers (preview-versie) | Procent | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Verbruikte CPU-tegoeden | Verbruikte CPU-tegoeden | Aantal | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Resterende CPU-tegoeden | Resterende CPU-tegoeden | Aantal | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Wachtrijlengte van gegevensschijf | Wachtrij diepte van gegevens schijf (preview-versie) | Aantal | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Gegevens schijf gelezen bytes per seconde | Gegevens schijf gelezen bytes per seconde (preview) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Lees bewerkingen op de gegevens schijf per seconde | Lees bewerkingen op de gegevens schijf per seconde (preview) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Geschreven bytes per seconde gegevens schijf | Geschreven bytes per seconde (preview) gegevens schijf | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Schrijf bewerkingen op de gegevens schijf per seconde | Schrijf bewerkingen op de gegevens schijf per seconde (preview) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Gelezen bytes op de schijf | Gelezen bytes op de schijf | Bytes | Totaal
Ja | Microsoft.Compute/virtualMachineScaleSets | Lees bewerkingen op de schijf per seconde | Lees bewerkingen op de schijf per seconde | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Geschreven bytes op de schijf | Geschreven bytes op de schijf | Bytes | Totaal
Ja | Microsoft.Compute/virtualMachineScaleSets | Schrijf bewerkingen op de schijf per seconde | Schrijf bewerkingen op de schijf per seconde | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Binnenkomende stromen | Binnenkomende stromen | Aantal | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Maximum aanmaak frequentie inkomende stromen | Maximum aantal aanmaak snelheden inkomende stromen (preview-versie) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Netwerk in | Netwerk in Factureerbaar (afgeschaft) | Bytes | Totaal
Ja | Microsoft.Compute/virtualMachineScaleSets | Totaal netwerk | Totaal netwerk | Bytes | Totaal
Ja | Microsoft.Compute/virtualMachineScaleSets | Netwerk uit | Gefactureerd netwerk (afgeschaft) | Bytes | Totaal
Ja | Microsoft.Compute/virtualMachineScaleSets | Totaal aantal netwerk | Totaal aantal netwerk | Bytes | Totaal
Ja | Microsoft.Compute/virtualMachineScaleSets | Wachtrijlengte van besturingssysteemschijf | Wachtrij diepte van de besturingssysteem schijf (preview-versie) | Aantal | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | BESTURINGSSYSTEEM schijf gelezen bytes per seconde | BESTURINGSSYSTEEM schijf gelezen bytes per seconde (preview) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Lees bewerkingen van de besturingssysteem schijf per seconde | Lees bewerkingen op de besturingssysteem schijf per seconde (preview) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Schrijf bewerkingen op de besturingssysteem schijf per seconde | Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Schrijf bewerkingen op de besturingssysteem schijf per seconde | Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | WACHTRIJ diepte voor het besturings systeem per schijf | WACHTRIJ diepte van de besturingssysteem schijf (afgeschaft) | Aantal | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | BESTURINGSSYSTEEM per schijf gelezen bytes per seconde | BESTURINGSSYSTEEM schijf gelezen bytes per seconde (afgeschaft) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Lees bewerkingen per schijf voor het besturings systeem/sec. | Lees bewerkingen op de besturingssysteem schijf per seconde (afgeschaft) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Per schijf geschreven bytes/sec. | BESTURINGSSYSTEEM schijf schrijf bewerkingen in bytes per seconde (afgeschaft) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Schrijf bewerkingen per schijf van het besturings systeem/SEC | Schrijf bewerkingen op de besturingssysteem schijf per seconde (afgeschaft) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Uitgaande stromen | Uitgaande stromen | Aantal | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Maximum aanmaak frequentie van uitgaande stromen | Maximum aantal uitgaande stromen voor maken (preview) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | WACHTRIJ diepte per schijf | WACHTRIJ diepte van de gegevens schijf (afgeschaft) | Aantal | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Per schijf gelezen bytes/sec. | Gegevens schijf gelezen bytes per seconde (afgeschaft) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Lees bewerkingen per schijf/sec. | Lees bewerkingen op de gegevens schijf per seconde (afgeschaft) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Schrijf bewerkingen per schijf/sec. | Gegevens schijf schrijf bewerkingen in bytes per seconde (afgeschaft) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Schrijf bewerkingen per schijf/SEC | Schrijf bewerkingen op de gegevens schijf per seconde (afgeschaft) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Percentage CPU | Percentage CPU | Procent | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Treffer voor Premium data-schijf cache lezen | Cache geheugen voor lezen van Premium-gegevens schijf (preview-versie) | Procent | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Lees missers cache Premium-gegevens schijf | Cache voor lezen van Premium-gegevens schijf (preview) | Procent | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Treffer voor Premium-besturingssysteem schijf cache lezen | Treffer voor het lezen van een Premium-besturingssysteem schijf cache (preview-versie) | Procent | Average
Ja | Microsoft.Compute/virtualMachineScaleSets | Leesmij voor Premium-besturingssysteem schijf cache lezen | Schijf cache voor Premium-Lees-missers (preview-versie) | Procent | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Verbruikte CPU-tegoeden | Verbruikte CPU-tegoeden | Aantal | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Resterende CPU-tegoeden | Resterende CPU-tegoeden | Aantal | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Wachtrijlengte van gegevensschijf | Wachtrij diepte van gegevens schijf (preview-versie) | Aantal | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Gegevens schijf gelezen bytes per seconde | Gegevens schijf gelezen bytes per seconde (preview) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Lees bewerkingen op de gegevens schijf per seconde | Lees bewerkingen op de gegevens schijf per seconde (preview) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Geschreven bytes per seconde gegevens schijf | Geschreven bytes per seconde (preview) gegevens schijf | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Schrijf bewerkingen op de gegevens schijf per seconde | Schrijf bewerkingen op de gegevens schijf per seconde (preview) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Gelezen bytes op de schijf | Gelezen bytes op de schijf | Bytes | Totaal
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Lees bewerkingen op de schijf per seconde | Lees bewerkingen op de schijf per seconde | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Geschreven bytes op de schijf | Geschreven bytes op de schijf | Bytes | Totaal
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Schrijf bewerkingen op de schijf per seconde | Schrijf bewerkingen op de schijf per seconde | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Binnenkomende stromen | Binnenkomende stromen | Aantal | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Maximum aanmaak frequentie inkomende stromen | Maximum aantal aanmaak snelheden inkomende stromen (preview-versie) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Netwerk in | Netwerk in Factureerbaar (afgeschaft) | Bytes | Totaal
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Totaal netwerk | Totaal netwerk | Bytes | Totaal
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Netwerk uit | Gefactureerd netwerk (afgeschaft) | Bytes | Totaal
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Totaal aantal netwerk | Totaal aantal netwerk | Bytes | Totaal
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Wachtrijlengte van besturingssysteemschijf | Wachtrij diepte van de besturingssysteem schijf (preview-versie) | Aantal | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | BESTURINGSSYSTEEM schijf gelezen bytes per seconde | BESTURINGSSYSTEEM schijf gelezen bytes per seconde (preview) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Lees bewerkingen van de besturingssysteem schijf per seconde | Lees bewerkingen op de besturingssysteem schijf per seconde (preview) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Schrijf bewerkingen op de besturingssysteem schijf per seconde | Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Schrijf bewerkingen op de besturingssysteem schijf per seconde | Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | WACHTRIJ diepte voor het besturings systeem per schijf | WACHTRIJ diepte van de besturingssysteem schijf (afgeschaft) | Aantal | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | BESTURINGSSYSTEEM per schijf gelezen bytes per seconde | BESTURINGSSYSTEEM schijf gelezen bytes per seconde (afgeschaft) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Lees bewerkingen per schijf voor het besturings systeem/sec. | Lees bewerkingen op de besturingssysteem schijf per seconde (afgeschaft) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Per schijf geschreven bytes/sec. | BESTURINGSSYSTEEM schijf schrijf bewerkingen in bytes per seconde (afgeschaft) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Schrijf bewerkingen per schijf van het besturings systeem/SEC | Schrijf bewerkingen op de besturingssysteem schijf per seconde (afgeschaft) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Uitgaande stromen | Uitgaande stromen | Aantal | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Maximum aanmaak frequentie van uitgaande stromen | Maximum aantal uitgaande stromen voor maken (preview) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | WACHTRIJ diepte per schijf | WACHTRIJ diepte van de gegevens schijf (afgeschaft) | Aantal | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Per schijf gelezen bytes/sec. | Gegevens schijf gelezen bytes per seconde (afgeschaft) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Lees bewerkingen per schijf/sec. | Lees bewerkingen op de gegevens schijf per seconde (afgeschaft) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Schrijf bewerkingen per schijf/sec. | Gegevens schijf schrijf bewerkingen in bytes per seconde (afgeschaft) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Schrijf bewerkingen per schijf/SEC | Schrijf bewerkingen op de gegevens schijf per seconde (afgeschaft) | CountPerSecond | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Percentage CPU | Percentage CPU | Procent | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Treffer voor Premium data-schijf cache lezen | Cache geheugen voor lezen van Premium-gegevens schijf (preview-versie) | Procent | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Lees missers cache Premium-gegevens schijf | Cache voor lezen van Premium-gegevens schijf (preview) | Procent | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Treffer voor Premium-besturingssysteem schijf cache lezen | Treffer voor het lezen van een Premium-besturingssysteem schijf cache (preview-versie) | Procent | Average
Ja | Microsoft.Compute/virtualMachineScaleSets/virtualMachines | Leesmij voor Premium-besturingssysteem schijf cache lezen | Schijf cache voor Premium-Lees-missers (preview-versie) | Procent | Average
Ja | Microsoft.ContainerInstance/containerGroups | CpuUsage | CPU-gebruik | Aantal | Average
Ja | Microsoft.ContainerInstance/containerGroups | MemoryUsage | Geheugengebruik | Bytes | Average
Ja | Microsoft.ContainerInstance/containerGroups | NetworkBytesReceivedPerSecond | Ontvangen netwerk bytes per seconde | Bytes | Average
Ja | Microsoft.ContainerInstance/containerGroups | NetworkBytesTransmittedPerSecond | Verzonden netwerk bytes per seconde | Bytes | Average
Ja | Micro soft. ContainerRegistry/registers | RunDuration | Uitvoerings duur | Milliseconden | Totaal
Ja | Micro soft. ContainerRegistry/registers | SuccessfulPullCount | Aantal geslaagde pull-bewerkingen | Aantal | Average
Ja | Micro soft. ContainerRegistry/registers | SuccessfulPushCount | Aantal geslaagde push berichten | Aantal | Average
Ja | Micro soft. ContainerRegistry/registers | TotalPullCount | Totaal aantal pull-bewerkingen | Aantal | Average
Ja | Micro soft. ContainerRegistry/registers | TotalPushCount | Totaal aantal push berichten | Aantal | Average
Nee | Microsoft.ContainerService/managedClusters | kube_node_status_allocatable_cpu_cores | Totaal aantal beschik bare CPU-kernen in een beheerd cluster | Aantal | Average
Nee | Microsoft.ContainerService/managedClusters | kube_node_status_allocatable_memory_bytes | Totale hoeveelheid beschikbaar geheugen in een beheerd cluster | Bytes | Average
Nee | Microsoft.ContainerService/managedClusters | kube_node_status_condition | Statussen voor de verschillende knooppunt voorwaarden | Aantal | Average
Nee | Microsoft.ContainerService/managedClusters | kube_pod_status_phase | Aantal per fase | Aantal | Average
Nee | Microsoft.ContainerService/managedClusters | kube_pod_status_ready | Aantal in de status gereed | Aantal | Average
Ja | Microsoft.DataBoxEdge/dataBoxEdgeDevices | Availablecapacity;) | Beschik bare capaciteit | Bytes | Average
Ja | Microsoft.DataBoxEdge/dataBoxEdgeDevices | BytesUploadedToCloud | Geüploade Cloud bytes (apparaat) | Bytes | Average
Ja | Microsoft.DataBoxEdge/dataBoxEdgeDevices | BytesUploadedToCloudPerShare | Geüploade Cloud bytes (delen) | Bytes | Average
Ja | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudReadThroughput | Door Voer van Cloud downloaden | BytesPerSecond | Average
Ja | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudReadThroughputPerShare | Door Voer van Cloud downloaden (delen) | BytesPerSecond | Average
Ja | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudUploadThroughput | Upload doorvoer van Cloud | BytesPerSecond | Average
Ja | Microsoft.DataBoxEdge/dataBoxEdgeDevices | CloudUploadThroughputPerShare | Upload doorvoer van Cloud (delen) | BytesPerSecond | Average
Ja | Microsoft.DataBoxEdge/dataBoxEdgeDevices | HyperVMemoryUtilization | Edge Compute-geheugen gebruik | Procent | Average
Ja | Microsoft.DataBoxEdge/dataBoxEdgeDevices | HyperVVirtualProcessorUtilization | Edge Compute-percentage CPU | Procent | Average
Ja | Microsoft.DataBoxEdge/dataBoxEdgeDevices | NICReadThroughput | Lees doorvoer (netwerk) | BytesPerSecond | Average
Ja | Microsoft.DataBoxEdge/dataBoxEdgeDevices | NICWriteThroughput | Schrijf doorvoer (netwerk) | BytesPerSecond | Average
Ja | Microsoft.DataBoxEdge/dataBoxEdgeDevices | TotalCapacity | Totale capaciteit | Bytes | Average
Ja | Microsoft.DataFactory/datafactories | FailedRuns | Mislukte verwerkingen | Aantal | Totaal
Ja | Microsoft.DataFactory/datafactories | SuccessfulRuns | Geslaagde uitvoeringen | Aantal | Totaal
Ja | Microsoft.DataFactory/factories | ActivityCancelledRuns | Metrische gegevens voor geannuleerde activiteit uitgevoerd | Aantal | Totaal
Ja | Microsoft.DataFactory/factories | ActivityFailedRuns | Metrische gegevens mislukte uitvoering van activiteit | Aantal | Totaal
Ja | Microsoft.DataFactory/factories | ActivitySucceededRuns | Metrische gegevens uitvoeringen uitgevoerde activiteit | Aantal | Totaal
Ja | Microsoft.DataFactory/factories | FactorySizeInGbUnits | Totale grootte van de fabriek (GB-eenheid) | Aantal | Maximum
Ja | Microsoft.DataFactory/factories | IntegrationRuntimeAvailableMemory | Beschik bare geheugen voor Integration runtime | Bytes | Average
Ja | Microsoft.DataFactory/factories | IntegrationRuntimeAverageTaskPickupDelay | Duur van de wachtrij voor Integration runtime | Seconden | Average
Ja | Microsoft.DataFactory/factories | IntegrationRuntimeCpuPercentage | CPU-gebruik van Integration runtime | Procent | Average
Ja | Microsoft.DataFactory/factories | IntegrationRuntimeQueueLength | Lengte van de wachtrij voor Integration runtime | Aantal | Average
Ja | Microsoft.DataFactory/factories | MaxAllowedFactorySizeInGbUnits | Maxi maal toegestane grootte van de fabriek (GB-eenheid) | Aantal | Maximum
Ja | Microsoft.DataFactory/factories | MaxAllowedResourceCount | Maximum aantal toegestane entiteiten | Aantal | Maximum
Ja | Microsoft.DataFactory/factories | PipelineCancelledRuns | Metrische gegevens van de pijplijn uitvoeringen geannuleerd | Aantal | Totaal
Ja | Microsoft.DataFactory/factories | PipelineFailedRuns | Metrische gegevens van mislukte pijplijn uitvoeringen | Aantal | Totaal
Ja | Microsoft.DataFactory/factories | PipelineSucceededRuns | Metrische uitvoerings metingen geslaagde pijp lijnen | Aantal | Totaal
Ja | Microsoft.DataFactory/factories | ResourceCount | Totaal aantal entiteiten | Aantal | Maximum
Ja | Microsoft.DataFactory/factories | TriggerCancelledRuns | Metrische gegevens over de trigger is geannuleerd | Aantal | Totaal
Ja | Microsoft.DataFactory/factories | TriggerFailedRuns | Meet waarden voor uitvoering van mislukte triggers | Aantal | Totaal
Ja | Microsoft.DataFactory/factories | TriggerSucceededRuns | Meet waarden voor uitvoering van geslaagde triggers | Aantal | Totaal
Ja | Microsoft.DataLakeAnalytics/accounts | JobAUEndedCancelled | Geannuleerde AU-tijd | Seconden | Totaal
Ja | Microsoft.DataLakeAnalytics/accounts | JobAUEndedFailure | Mislukte AU-tijd | Seconden | Totaal
Ja | Microsoft.DataLakeAnalytics/accounts | JobAUEndedSuccess | Geslaagde AU-tijd | Seconden | Totaal
Ja | Microsoft.DataLakeAnalytics/accounts | JobEndedCancelled | Geannuleerde taken | Aantal | Totaal
Ja | Microsoft.DataLakeAnalytics/accounts | JobEndedFailure | Mislukte taken | Aantal | Totaal
Ja | Microsoft.DataLakeAnalytics/accounts | JobEndedSuccess | Geslaagde taken | Aantal | Totaal
Ja | Microsoft.DataLakeStore/accounts | DataRead | Gegevens lezen | Bytes | Totaal
Ja | Microsoft.DataLakeStore/accounts | DataWritten | Gegevens geschreven | Bytes | Totaal
Ja | Microsoft.DataLakeStore/accounts | ReadRequests | Aanvragen lezen | Aantal | Totaal
Ja | Microsoft.DataLakeStore/accounts | TotalStorage | Totale opslag | Bytes | Maximum
Ja | Microsoft.DataLakeStore/accounts | WriteRequests | Aanvragen schrijven | Aantal | Totaal
Ja | Microsoft.DBforMariaDB/servers | active_connections | Actieve verbindingen | Aantal | Average
Ja | Microsoft.DBforMariaDB/servers | backup_storage_used | Gebruikte back-upopslag | Bytes | Average
Ja | Microsoft.DBforMariaDB/servers | connections_failed | Mislukte verbindingen | Aantal | Totaal
Ja | Microsoft.DBforMariaDB/servers | cpu_percent | CPU-percentage | Procent | Average
Ja | Microsoft.DBforMariaDB/servers | io_consumption_percent | IO-percentage | Procent | Average
Ja | Microsoft.DBforMariaDB/servers | memory_percent | Geheugen percentage | Procent | Average
Ja | Microsoft.DBforMariaDB/servers | network_bytes_egress | Netwerk uit | Bytes | Totaal
Ja | Microsoft.DBforMariaDB/servers | network_bytes_ingress | Netwerk in | Bytes | Totaal
Ja | Microsoft.DBforMariaDB/servers | seconds_behind_master | Replicatie vertraging in seconden | Aantal | Maximum
Ja | Microsoft.DBforMariaDB/servers | serverlog_storage_limit | Opslag limiet voor server logboek | Bytes | Average
Ja | Microsoft.DBforMariaDB/servers | serverlog_storage_percent | Percentage server logboek opslag | Procent | Average
Ja | Microsoft.DBforMariaDB/servers | serverlog_storage_usage | Gebruikte server logboek opslag | Bytes | Average
Ja | Microsoft.DBforMariaDB/servers | storage_limit | Opslag limiet | Bytes | Maximum
Ja | Microsoft.DBforMariaDB/servers | storage_percent | Opslag percentage | Procent | Average
Ja | Microsoft.DBforMariaDB/servers | storage_used | Gebruikte opslag | Bytes | Average
Ja | Microsoft.DBforMySQL/servers | active_connections | Actieve verbindingen | Aantal | Average
Ja | Microsoft.DBforMySQL/servers | backup_storage_used | Gebruikte back-upopslag | Bytes | Average
Ja | Microsoft.DBforMySQL/servers | connections_failed | Mislukte verbindingen | Aantal | Totaal
Ja | Microsoft.DBforMySQL/servers | cpu_percent | CPU-percentage | Procent | Average
Ja | Microsoft.DBforMySQL/servers | io_consumption_percent | IO-percentage | Procent | Average
Ja | Microsoft.DBforMySQL/servers | memory_percent | Geheugen percentage | Procent | Average
Ja | Microsoft.DBforMySQL/servers | network_bytes_egress | Netwerk uit | Bytes | Totaal
Ja | Microsoft.DBforMySQL/servers | network_bytes_ingress | Netwerk in | Bytes | Totaal
Ja | Microsoft.DBforMySQL/servers | seconds_behind_master | Replicatie vertraging in seconden | Aantal | Maximum
Ja | Microsoft.DBforMySQL/servers | serverlog_storage_limit | Opslag limiet voor server logboek | Bytes | Maximum
Ja | Microsoft.DBforMySQL/servers | serverlog_storage_percent | Percentage server logboek opslag | Procent | Average
Ja | Microsoft.DBforMySQL/servers | serverlog_storage_usage | Gebruikte server logboek opslag | Bytes | Average
Ja | Microsoft.DBforMySQL/servers | storage_limit | Opslag limiet | Bytes | Maximum
Ja | Microsoft.DBforMySQL/servers | storage_percent | Opslag percentage | Procent | Average
Ja | Microsoft.DBforMySQL/servers | storage_used | Gebruikte opslag | Bytes | Average
Ja | Microsoft.DBforPostgreSQL/servers | active_connections | Actieve verbindingen | Aantal | Average
Ja | Microsoft.DBforPostgreSQL/servers | backup_storage_used | Gebruikte back-upopslag | Bytes | Average
Ja | Microsoft.DBforPostgreSQL/servers | connections_failed | Mislukte verbindingen | Aantal | Totaal
Ja | Microsoft.DBforPostgreSQL/servers | cpu_percent | CPU-percentage | Procent | Average
Ja | Microsoft.DBforPostgreSQL/servers | io_consumption_percent | IO-percentage | Procent | Average
Ja | Microsoft.DBforPostgreSQL/servers | memory_percent | Geheugen percentage | Procent | Average
Ja | Microsoft.DBforPostgreSQL/servers | network_bytes_egress | Netwerk uit | Bytes | Totaal
Ja | Microsoft.DBforPostgreSQL/servers | network_bytes_ingress | Netwerk in | Bytes | Totaal
Ja | Microsoft.DBforPostgreSQL/servers | pg_replica_log_delay_in_bytes | Maximale vertraging in Replica's | Bytes | Maximum
Ja | Microsoft.DBforPostgreSQL/servers | pg_replica_log_delay_in_seconds | Replica vertraging | Seconden | Maximum
Ja | Microsoft.DBforPostgreSQL/servers | serverlog_storage_limit | Opslag limiet voor server logboek | Bytes | Maximum
Ja | Microsoft.DBforPostgreSQL/servers | serverlog_storage_percent | Percentage server logboek opslag | Procent | Average
Ja | Microsoft.DBforPostgreSQL/servers | serverlog_storage_usage | Gebruikte server logboek opslag | Bytes | Average
Ja | Microsoft.DBforPostgreSQL/servers | storage_limit | Opslag limiet | Bytes | Maximum
Ja | Microsoft.DBforPostgreSQL/servers | storage_percent | Opslag percentage | Procent | Average
Ja | Microsoft.DBforPostgreSQL/servers | storage_used | Gebruikte opslag | Bytes | Average
Ja | Microsoft.DBforPostgreSQL/serversv2 | active_connections | Actieve verbindingen | Aantal | Average
Ja | Microsoft.DBforPostgreSQL/serversv2 | cpu_percent | CPU-percentage | Procent | Average
Ja | Microsoft.DBforPostgreSQL/serversv2 | IOPS | IOPS | Aantal | Average
Ja | Microsoft.DBforPostgreSQL/serversv2 | memory_percent | Geheugen percentage | Procent | Average
Ja | Microsoft.DBforPostgreSQL/serversv2 | network_bytes_egress | Netwerk uit | Bytes | Totaal
Ja | Microsoft.DBforPostgreSQL/serversv2 | network_bytes_ingress | Netwerk in | Bytes | Totaal
Ja | Microsoft.DBforPostgreSQL/serversv2 | storage_percent | Opslag percentage | Procent | Average
Ja | Microsoft.DBforPostgreSQL/serversv2 | storage_used | Gebruikte opslag | Bytes | Average
Ja | Micro soft. apparaten/account | digitaltwins. telemetrie. nodes | Tijdelijke aanduiding voor telemetrie van Digital Apparaatdubbels-knoop punt | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | c2d.commands.egress.abandon.success | C2D-berichten zijn afgebroken | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | C2D. commands. OUTuitgang. complete. geslaagd | C2D-bericht leveringen voltooid | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | c2d.commands.egress.reject.success | Geweigerde C2D-berichten | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | C2D. methods. failure | Mislukte aanroepen van de directe methode | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | C2D. methods. requestSize | Aanvraaggrootte van aanroepen van de directe methode | Bytes | Average
Ja | Microsoft.Devices/IotHubs | C2D. methods. responseSize | Antwoordgrootte van aanroepen van de directe methode | Bytes | Average
Ja | Microsoft.Devices/IotHubs | C2D. methods. geslaagd | Geslaagde directe aanroepen van de methode | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | c2d.twin.read.failure | Mislukte dubbele leesbewerkingen van back-end | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | c2d.twin.read.size | Antwoordgrootte van dubbele leesbewerkingen van back-end | Bytes | Average
Ja | Microsoft.Devices/IotHubs | c2d.twin.read.success | Geslaagde dubbele Lees bewerkingen van back-end | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | C2D. dubbele. update. failure | Mislukte dubbele updates van back-end | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | C2D. dubbele. update. grootte | Grootte van dubbele updates van back-end | Bytes | Average
Ja | Microsoft.Devices/IotHubs | c2d.twin.update.success | Geslaagde dubbele updates van back-end | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | C2DMessagesExpired | C2D-berichten verlopen (preview-versie) | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | configuraties | Metrische gegevens van configuratie | Aantal | Totaal
Nee | Microsoft.Devices/IotHubs | connectedDeviceCount | Verbonden apparaten (preview) | Aantal | Average
Ja | Microsoft.Devices/IotHubs | D2C. endpoints. uitgangs punt. builtIn. Events | Route ring: berichten worden bezorgd bij berichten/gebeurtenissen | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | d2c.endpoints.egress.eventHubs | Route ring: berichten worden bezorgd bij Event hub | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusQueues | Route ring: berichten worden bezorgd bij Service Bus wachtrij | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | d2c.endpoints.egress.serviceBusTopics | Route ring: berichten die worden bezorgd bij Service Bus onderwerp | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage | Route ring: berichten worden bezorgd bij de opslag | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | d2c.endpoints.egress.storage.blobs | Routering: aan opslag geleverde blobs | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | D2C. endpoints. out. storage. bytes | Routering: aan opslag geleverde gegevens | Bytes | Totaal
Ja | Microsoft.Devices/IotHubs | d2c.endpoints.latency.builtIn.events | Route ring: bericht latentie voor berichten/gebeurtenissen | Milliseconden | Average
Ja | Microsoft.Devices/IotHubs | d2c.endpoints.latency.eventHubs | Routering: berichtlatentie voor Event Hub | Milliseconden | Average
Ja | Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusQueues | Routering: berichtlatentie voor Service Bus-wachtrij | Milliseconden | Average
Ja | Microsoft.Devices/IotHubs | d2c.endpoints.latency.serviceBusTopics | Route ring: bericht latentie voor Service Bus onderwerp | Milliseconden | Average
Ja | Microsoft.Devices/IotHubs | d2c.endpoints.latency.storage | Route ring: bericht latentie voor opslag | Milliseconden | Average
Ja | Microsoft.Devices/IotHubs | d2c.telemetry.egress.dropped | Route ring: telemetrie-berichten verwijderd | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | d2c.telemetry.egress.fallback | Route ring: berichten worden bezorgd bij terugval | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | d2c.telemetry.egress.invalid | Route ring: telemetrie-berichten incompatibel | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | d2c.telemetry.egress.orphaned | Route ring: telemetriegegevens van zwevende berichten | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | d2c.telemetry.egress.success | Route ring: telemetrie-berichten | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.allProtocol | Verzend pogingen voor telemetrie-berichten | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.sendThrottle | Aantal netwerkbeperkingsfouten | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | d2c.telemetry.ingress.success | Berichten over telemetrie verzonden | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | D2C. dubbele. Read. failure | Mislukte dubbele leesbewerkingen van apparaten | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | d2c.twin.read.size | Antwoordgrootte van dubbele leesbewerkingen van apparaten | Bytes | Average
Ja | Microsoft.Devices/IotHubs | d2c.twin.read.success | Geslaagde dubbele Lees bewerkingen van apparaten | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | D2C. dubbele. update. failure | Mislukte dubbele updates van apparaten | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | D2C. dubbele. update. grootte | Grootte van dubbele updates van apparaten | Bytes | Average
Ja | Microsoft.Devices/IotHubs | d2c.twin.update.success | Geslaagde dubbele updates van apparaten | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | dailyMessageQuotaUsed | Totaal aantal gebruikte berichten | Aantal | Average
Ja | Microsoft.Devices/IotHubs | deviceDataUsage | Totale hoeveelheid gegevens gebruik van apparaat | Bytes | Totaal
Ja | Microsoft.Devices/IotHubs | deviceDataUsageV2 | Totaal gebruik van apparaatgegevens (preview-versie) | Bytes | Totaal
Ja | Microsoft.Devices/IotHubs | devices.connectedDevices.allProtocol | Verbonden apparaten (afgeschaft)  | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | devices.totalDevices | Alle apparaten (afgeschaft) | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | EventGridDeliveries | Event Grid leveringen (preview-versie) | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | EventGridLatency | Event Grid latentie (preview-versie) | Milliseconden | Average
Ja | Microsoft.Devices/IotHubs | jobs.cancelJob.failure | Mislukte taakannuleringen | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | jobs.cancelJob.success | Voltooide taak annuleringen | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | Jobs. voltooid | Voltooide taken | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.failure | Mislukte bewerkingen voor het maken van taken voor het aanroepen van methode | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | jobs.createDirectMethodJob.success | Geslaagde creatie van methode aanroep taken | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.failure | Mislukte bewerkingen voor het maken van dubbele taken voor bijwerken | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | jobs.createTwinUpdateJob.success | Geslaagde creatie van dubbele update taken | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | Jobs. mislukt | Mislukte taken | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | jobs.listJobs.failure | Mislukte aanroepen naar taken op de lijst | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | jobs.listJobs.success | Geslaagde aanroepen naar lijst taken | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | jobs.queryJobs.failure | Mislukte taakquery's | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | jobs.queryJobs.success | Geslaagde taak query's | Aantal | Totaal
Nee | Microsoft.Devices/IotHubs | totalDeviceCount | Totaal aantal apparaten (preview-versie) | Aantal | Average
Ja | Microsoft.Devices/IotHubs | twinQueries.failure | Mislukte dubbele query's | Aantal | Totaal
Ja | Microsoft.Devices/IotHubs | twinQueries.resultSize | Resultaat grootte van dubbele query's | Bytes | Average
Ja | Microsoft.Devices/IotHubs | twinQueries.success | Geslaagde dubbele query's | Aantal | Totaal
Ja | Microsoft.Devices/provisioningServices | AttestationAttempts | Attestation-pogingen | Aantal | Totaal
Ja | Microsoft.Devices/provisioningServices | DeviceAssignments | Apparaten toegewezen | Aantal | Totaal
Ja | Microsoft.Devices/provisioningServices | RegistrationAttempts | Registratie pogingen | Aantal | Totaal
Nee | Microsoft.DocumentDB/databaseAccounts | AvailableStorage | Beschikbare opslag | Bytes | Totaal
Nee | Microsoft.DocumentDB/databaseAccounts | CassandraConnectionClosures | Cassandra-verbinding sluiten | Aantal | Totaal
Nee | Microsoft.DocumentDB/databaseAccounts | CassandraRequestCharges | Kosten voor Cassandra-aanvragen | Aantal | Totaal
Nee | Microsoft.DocumentDB/databaseAccounts | CassandraRequests | Cassandra aanvragen | Aantal | Aantal
Nee | Microsoft.DocumentDB/databaseAccounts | DataUsage | Gegevensgebruik | Bytes | Totaal
Ja | Microsoft.DocumentDB/databaseAccounts | DeleteVirtualNetwork | DeleteVirtualNetwork | Aantal | Aantal
Nee | Microsoft.DocumentDB/databaseAccounts | DocumentCount | Aantal documenten | Aantal | Totaal
Nee | Microsoft.DocumentDB/databaseAccounts | DocumentQuota | Document quotum | Bytes | Totaal
Nee | Microsoft.DocumentDB/databaseAccounts | IndexUsage | Index gebruik | Bytes | Totaal
Nee | Microsoft.DocumentDB/databaseAccounts | MetadataRequests | Meta gegevens aanvragen | Aantal | Aantal
Ja | Microsoft.DocumentDB/databaseAccounts | MongoRequestCharge | Kosten voor Mongo-aanvragen | Aantal | Totaal
Ja | Microsoft.DocumentDB/databaseAccounts | MongoRequests | Mongo aanvragen | Aantal | Aantal
Nee | Microsoft.DocumentDB/databaseAccounts | MongoRequestsCount | Frequentie van Mongo-aanvragen | CountPerSecond | Average
Nee | Microsoft.DocumentDB/databaseAccounts | MongoRequestsDelete | Aantal Mongo-aanvragen voor verwijderen | CountPerSecond | Average
Nee | Microsoft.DocumentDB/databaseAccounts | MongoRequestsInsert | Aantal Mongo invoegen | CountPerSecond | Average
Nee | Microsoft.DocumentDB/databaseAccounts | MongoRequestsQuery | Frequentie van Mongo-query aanvragen | CountPerSecond | Average
Nee | Microsoft.DocumentDB/databaseAccounts | MongoRequestsUpdate | Frequentie van Mongo-update aanvragen | CountPerSecond | Average
Nee | Microsoft.DocumentDB/databaseAccounts | ProvisionedThroughput | Ingerichte doorvoer | Aantal | Maximum
Ja | Microsoft.DocumentDB/databaseAccounts | ReplicationLatency | P99-replicatie latentie | MilliSeconds | Average
Nee | Microsoft.DocumentDB/databaseAccounts | ServiceAvailability | Service beschikbaarheid | Procent | Average
Ja | Microsoft.DocumentDB/databaseAccounts | TotalRequests | Totaal aantal aanvragen | Aantal | Aantal
Ja | Microsoft.DocumentDB/databaseAccounts | TotalRequestUnits | Totaal aantal aanvraag eenheden | Aantal | Totaal
Nee | Micro soft. EnterpriseKnowledgeGraph/Services | FailureCount | Aantal mislukt | Aantal | Aantal
Nee | Micro soft. EnterpriseKnowledgeGraph/Services | SuccessCount | Aantal geslaagd | Aantal | Aantal
Nee | Micro soft. EnterpriseKnowledgeGraph/Services | SuccessLatency | Geslaagde latentie | MilliSeconds | Average
Nee | Micro soft. EnterpriseKnowledgeGraph/Services | TransactionCount | Aantal trans acties | Aantal | Aantal
Ja | Micro soft. EventGrid/domeinen | DeadLetteredCount | Gebeurtenissen met onbestelbare berichten | Aantal | Totaal
Nee | Micro soft. EventGrid/domeinen | DeliveryAttemptFailCount | Mislukte leverings gebeurtenissen | Aantal | Totaal
Ja | Micro soft. EventGrid/domeinen | DeliverySuccessCount | Geleverde gebeurtenissen | Aantal | Totaal
Nee | Micro soft. EventGrid/domeinen | DestinationProcessingDurationInMs | Doel verwerkings duur | Milliseconden | Average
Ja | Micro soft. EventGrid/domeinen | DroppedEventCount | Verwijderde gebeurtenissen | Aantal | Totaal
Ja | Micro soft. EventGrid/domeinen | MatchedEventCount | Overeenkomende gebeurtenissen | Aantal | Totaal
Ja | Micro soft. EventGrid/domeinen | PublishFailCount | Mislukte gebeurtenissen publiceren | Aantal | Totaal
Ja | Micro soft. EventGrid/domeinen | PublishSuccessCount | Gepubliceerde gebeurtenissen | Aantal | Totaal
Ja | Micro soft. EventGrid/domeinen | PublishSuccessLatencyInMs | Latentie van publicatie geslaagd | Aantal | Totaal
Ja | Microsoft.EventGrid/eventSubscriptions | DeadLetteredCount | Gebeurtenissen met onbestelbare berichten | Aantal | Totaal
Nee | Microsoft.EventGrid/eventSubscriptions | DeliveryAttemptFailCount | Mislukte leverings gebeurtenissen | Aantal | Totaal
Ja | Microsoft.EventGrid/eventSubscriptions | DeliverySuccessCount | Geleverde gebeurtenissen | Aantal | Totaal
Nee | Microsoft.EventGrid/eventSubscriptions | DestinationProcessingDurationInMs | Doel verwerkings duur | Milliseconden | Average
Ja | Microsoft.EventGrid/eventSubscriptions | DroppedEventCount | Verwijderde gebeurtenissen | Aantal | Totaal
Ja | Microsoft.EventGrid/eventSubscriptions | MatchedEventCount | Overeenkomende gebeurtenissen | Aantal | Totaal
Ja | Microsoft.EventGrid/extensionTopics | PublishFailCount | Mislukte gebeurtenissen publiceren | Aantal | Totaal
Ja | Microsoft.EventGrid/extensionTopics | PublishSuccessCount | Gepubliceerde gebeurtenissen | Aantal | Totaal
Ja | Microsoft.EventGrid/extensionTopics | PublishSuccessLatencyInMs | Latentie van publicatie geslaagd | Aantal | Totaal
Ja | Microsoft.EventGrid/extensionTopics | UnmatchedEventCount | Niet-overeenkomende gebeurtenissen | Aantal | Totaal
Ja | Micro soft. EventGrid/topics | PublishFailCount | Mislukte gebeurtenissen publiceren | Aantal | Totaal
Ja | Micro soft. EventGrid/topics | PublishSuccessCount | Gepubliceerde gebeurtenissen | Aantal | Totaal
Ja | Micro soft. EventGrid/topics | PublishSuccessLatencyInMs | Latentie van publicatie geslaagd | Aantal | Totaal
Ja | Micro soft. EventGrid/topics | UnmatchedEventCount | Niet-overeenkomende gebeurtenissen | Aantal | Totaal
Nee | Microsoft.EventHub/clusters | ActiveConnections | ActiveConnections | Aantal | Average
Nee | Microsoft.EventHub/clusters | AvailableMemory | Beschikbaar geheugen | Procent | Maximum
Nee | Microsoft.EventHub/clusters | CaptureBacklog | Achterstand vastleggen. | Aantal | Totaal
Nee | Microsoft.EventHub/clusters | CapturedBytes | Vastgelegde bytes. | Bytes | Totaal
Nee | Microsoft.EventHub/clusters | CapturedMessages | Vastgelegde berichten. | Aantal | Totaal
Nee | Microsoft.EventHub/clusters | ConnectionsClosed | Verbindingen gesloten. | Aantal | Average
Nee | Microsoft.EventHub/clusters | ConnectionsOpened | Geopende verbindingen. | Aantal | Average
Nee | Microsoft.EventHub/clusters | CPU | CPU | Procent | Maximum
Ja | Microsoft.EventHub/clusters | IncomingBytes | Binnenkomende bytes. | Bytes | Totaal
Ja | Microsoft.EventHub/clusters | IncomingMessages | Inkomende berichten | Aantal | Totaal
Ja | Microsoft.EventHub/clusters | IncomingRequests | Binnenkomende aanvragen | Aantal | Totaal
Ja | Microsoft.EventHub/clusters | OutgoingBytes | Uitgaande bytes. | Bytes | Totaal
Ja | Microsoft.EventHub/clusters | OutgoingMessages | Uitgaande berichten | Aantal | Totaal
Nee | Microsoft.EventHub/clusters | QuotaExceededErrors | Quota overschreden fouten. | Aantal | Totaal
Nee | Microsoft.EventHub/clusters | ServerErrors | Server fouten. | Aantal | Totaal
Nee | Microsoft.EventHub/clusters | SuccessfulRequests | Geslaagde aanvragen | Aantal | Totaal
Nee | Microsoft.EventHub/clusters | ThrottledRequests | Vertraagde aanvragen. | Aantal | Totaal
Nee | Microsoft.EventHub/clusters | UserErrors | Gebruikers fouten. | Aantal | Totaal
Nee | Microsoft.EventHub/namespaces | ActiveConnections | ActiveConnections | Aantal | Average
Nee | Microsoft.EventHub/namespaces | CaptureBacklog | Achterstand vastleggen. | Aantal | Totaal
Nee | Microsoft.EventHub/namespaces | CapturedBytes | Vastgelegde bytes. | Bytes | Totaal
Nee | Microsoft.EventHub/namespaces | CapturedMessages | Vastgelegde berichten. | Aantal | Totaal
Nee | Microsoft.EventHub/namespaces | ConnectionsClosed | Verbindingen gesloten. | Aantal | Average
Nee | Microsoft.EventHub/namespaces | ConnectionsOpened | Geopende verbindingen. | Aantal | Average
Ja | Microsoft.EventHub/namespaces | EHABL | Achterstallige berichten archiveren (afgeschaft) | Aantal | Totaal
Ja | Microsoft.EventHub/namespaces | EHAMBS | Berichten doorvoer archiveren (afgeschaft) | Bytes | Totaal
Ja | Microsoft.EventHub/namespaces | EHAMSGS | Berichten archiveren (afgeschaft) | Aantal | Totaal
Ja | Microsoft.EventHub/namespaces | EHINBYTES | Binnenkomende bytes (afgeschaft) | Bytes | Totaal
Ja | Microsoft.EventHub/namespaces | EHINMBS | Binnenkomende bytes (verouderd) (afgeschaft) | Bytes | Totaal
Ja | Microsoft.EventHub/namespaces | EHINMSGS | Inkomende berichten (afgeschaft) | Aantal | Totaal
Ja | Microsoft.EventHub/namespaces | EHOUTBYTES | Uitgaande bytes (afgeschaft) | Bytes | Totaal
Ja | Microsoft.EventHub/namespaces | EHOUTMBS | Uitgaande bytes (verouderd) (afgeschaft) | Bytes | Totaal
Ja | Microsoft.EventHub/namespaces | EHOUTMSGS | Uitgaande berichten (afgeschaft) | Aantal | Totaal
Ja | Microsoft.EventHub/namespaces | FAILREQ | Mislukte aanvragen (afgeschaft) | Aantal | Totaal
Ja | Microsoft.EventHub/namespaces | IncomingBytes | Binnenkomende bytes. | Bytes | Totaal
Ja | Microsoft.EventHub/namespaces | IncomingMessages | Inkomende berichten | Aantal | Totaal
Ja | Microsoft.EventHub/namespaces | IncomingRequests | Binnenkomende aanvragen | Aantal | Totaal
Ja | Microsoft.EventHub/namespaces | INMSGS | Binnenkomende berichten (verouderd) (afgeschaft) | Aantal | Totaal
Ja | Microsoft.EventHub/namespaces | INREQS | Binnenkomende aanvragen (afgeschaft) | Aantal | Totaal
Ja | Microsoft.EventHub/namespaces | INTERer | Interne server fouten (afgeschaft) | Aantal | Totaal
Ja | Microsoft.EventHub/namespaces | MISCERR | Andere fouten (afgeschaft) | Aantal | Totaal
Ja | Microsoft.EventHub/namespaces | OutgoingBytes | Uitgaande bytes. | Bytes | Totaal
Ja | Microsoft.EventHub/namespaces | OutgoingMessages | Uitgaande berichten | Aantal | Totaal
Ja | Microsoft.EventHub/namespaces | OUTMSGS | Uitgaande berichten (verouderd) (afgeschaft) | Aantal | Totaal
Nee | Microsoft.EventHub/namespaces | QuotaExceededErrors | Quota overschreden fouten. | Aantal | Totaal
Nee | Microsoft.EventHub/namespaces | ServerErrors | Server fouten. | Aantal | Totaal
Nee | Microsoft.EventHub/namespaces | Grootte | Grootte | Bytes | Average
Nee | Microsoft.EventHub/namespaces | SuccessfulRequests | Geslaagde aanvragen | Aantal | Totaal
Ja | Microsoft.EventHub/namespaces | SUCCREQ | Geslaagde aanvragen (afgeschaft) | Aantal | Totaal
Ja | Microsoft.EventHub/namespaces | SVRBSY | Fouten bij server bezet (afgeschaft) | Aantal | Totaal
Nee | Microsoft.EventHub/namespaces | ThrottledRequests | Vertraagde aanvragen. | Aantal | Totaal
Nee | Microsoft.EventHub/namespaces | UserErrors | Gebruikers fouten. | Aantal | Totaal
Ja | Microsoft.HDInsight/clusters | CategorizedGatewayRequests | Gecategoriseerde gateway aanvragen | Aantal | Totaal
Ja | Microsoft.HDInsight/clusters | GatewayRequests | Gateway aanvragen | Aantal | Totaal
Ja | Microsoft.HDInsight/clusters | NumActiveWorkers | Aantal actieve werk rollen | Aantal | Maximum
Ja | Microsoft.HDInsight/clusters | ScalingRequests | Aanvragen schalen | Aantal | Maximum
Ja | Microsoft.Insights/AutoscaleSettings | MetricThreshold | Drempel waarde voor metrische gegevens | Aantal | Average
Ja | Microsoft.Insights/AutoscaleSettings | ObservedCapacity | Waargenomen capaciteit | Aantal | Average
Ja | Microsoft.Insights/AutoscaleSettings | ObservedMetricValue | Waargenomen metrische waarde | Aantal | Average
Ja | Microsoft.Insights/AutoscaleSettings | ScaleActionsInitiated | Schaal acties gestart | Aantal | Totaal
Ja | Microsoft.Insights/Components | availabilityResults/availabilityPercentage | Beschikbaarheid | Procent | Average
Nee | Microsoft.Insights/Components | availabilityResults/aantal | Beschikbaarheidstests | Aantal | Aantal
Ja | Microsoft.Insights/Components | availabilityResults/duur | Duur beschikbaarheids test | MilliSeconds | Average
Ja | Microsoft.Insights/Components | browserTimings/networkDuration | Netwerk verbindings tijd voor laden van pagina | MilliSeconds | Average
Ja | Microsoft.Insights/Components | browserTimings/processingDuration | Verwerkingstijd client | MilliSeconds | Average
Ja | Microsoft.Insights/Components | browserTimings/receiveDuration | Reactie tijd van ontvangst | MilliSeconds | Average
Ja | Microsoft.Insights/Components | browserTimings/sendDuration | Aanvraag tijd verzenden | MilliSeconds | Average
Ja | Microsoft.Insights/Components | browserTimings/totalDuration | Laad tijd van browser pagina | MilliSeconds | Average
Nee | Microsoft.Insights/Components | afhankelijkheden/aantal | Afhankelijkheids aanroepen | Aantal | Aantal
Ja | Microsoft.Insights/Components | afhankelijkheden/duur | Duur van afhankelijkheid | MilliSeconds | Average
Nee | Microsoft.Insights/Components | afhankelijkheden/mislukt | Mislukte afhankelijkheids aanroepen | Aantal | Aantal
Nee | Microsoft.Insights/Components | uitzonde ringen/browser | Browseruitzonderingen | Aantal | Aantal
Ja | Microsoft.Insights/Components | uitzonde ringen/aantal | Uitzonderingen | Aantal | Aantal
Nee | Microsoft.Insights/Components | uitzonde ringen/server | Server uitzonderingen | Aantal | Aantal
Ja | Microsoft.Insights/Components | Page views/aantal | Pagina weergaven | Aantal | Aantal
Ja | Microsoft.Insights/Components | Page views/duur | Laad tijd pagina weergave | MilliSeconds | Average
Ja | Microsoft.Insights/Components | performanceCounters/exceptionsPerSecond | Uitzonderings frequentie | CountPerSecond | Average
Ja | Microsoft.Insights/Components | performanceCounters/memoryAvailableBytes | Beschikbaar geheugen | Bytes | Average
Ja | Microsoft.Insights/Components | performanceCounters/processCpuPercentage | CPU verwerken | Procent | Average
Ja | Microsoft.Insights/Components | performanceCounters/processIOBytesPerSecond | I/o-frequentie van processen | BytesPerSecond | Average
Ja | Microsoft.Insights/Components | performanceCounters/processorCpuPercentage | Processor tijd | Procent | Average
Ja | Microsoft.Insights/Components | performanceCounters/processPrivateBytes | Privé-bytes verwerken | Bytes | Average
Ja | Microsoft.Insights/Components | performanceCounters/requestExecutionTime | Uitvoerings tijd van de HTTP-aanvraag | MilliSeconds | Average
Ja | Microsoft.Insights/Components | Performance Counters/requestsInQueue | HTTP-aanvragen in de toepassings wachtrij | Aantal | Average
Ja | Microsoft.Insights/Components | performanceCounters/requestsPerSecond | Frequentie van HTTP-aanvragen | CountPerSecond | Average
Nee | Microsoft.Insights/Components | aanvragen/aantal | Server aanvragen | Aantal | Aantal
Ja | Microsoft.Insights/Components | aanvragen/duur | Serverreactietijd | MilliSeconds | Average
Nee | Microsoft.Insights/Components | aanvragen/mislukt | Mislukte aanvragen | Aantal | Aantal
Nee | Microsoft.Insights/Components | aanvragen/frequentie | Aantal server aanvragen | CountPerSecond | Average
Ja | Microsoft.Insights/Components | traceringen/aantal | Traces | Aantal | Aantal
Ja | Microsoft.KeyVault/vaults | ServiceApiHit | Totaal aantal treffers in de service-API | Aantal | Aantal
Ja | Microsoft.KeyVault/vaults | ServiceApiLatency | Algehele latentie van Service-API | Milliseconden | Average
Ja | Microsoft.KeyVault/vaults | ServiceApiResult | Totale resultaten van Service-API | Aantal | Aantal
Ja | Microsoft.Kusto/Clusters | CacheUtilization | Cache gebruik | Procent | Average
Ja | Microsoft.Kusto/Clusters | ContinuousExportMaxLatenessMinutes | Maximale achterstand voor continue export | Aantal | Maximum
Ja | Microsoft.Kusto/Clusters | ContinuousExportNumOfRecordsExported | Doorlopend exporteren: aantal geëxporteerde records | Aantal | Totaal
Ja | Microsoft.Kusto/Clusters | ContinuousExportPendingCount | Aantal doorlopend exporteren in behandeling | Aantal | Maximum
Ja | Microsoft.Kusto/Clusters | ContinuousExportResult | Resultaat doorlopend exporteren | Aantal | Aantal
Ja | Microsoft.Kusto/Clusters | CPU | CPU | Procent | Average
Ja | Microsoft.Kusto/Clusters | EventsProcessedForEventHubs | Verwerkte gebeurtenissen (voor gebeurtenis/IoT-hubs) | Aantal | Totaal
Ja | Microsoft.Kusto/Clusters | ExportUtilization | Gebruik exporteren | Procent | Maximum
Ja | Microsoft.Kusto/Clusters | IngestionLatencyInSeconds | Opname latentie (in seconden) | Seconden | Average
Ja | Microsoft.Kusto/Clusters | IngestionResult | Opname resultaat | Aantal | Aantal
Ja | Microsoft.Kusto/Clusters | IngestionUtilization | Opname gebruik | Procent | Average
Ja | Microsoft.Kusto/Clusters | IngestionVolumeInMB | Opname volume (in MB) | Aantal | Totaal
Ja | Microsoft.Kusto/Clusters | KeepAlive | Actief houden | Aantal | Average
Ja | Microsoft.Kusto/Clusters | QueryDuration | Queryduur | Milliseconden | Average
Ja | Microsoft.Kusto/Clusters | SteamingIngestRequestRate | Aanvraag frequentie voor streaming-opname | Aantal | RateRequestsPerSecond
Ja | Microsoft.Kusto/Clusters | StreamingIngestDataRate | Gegevens frequentie van streaming opname | Aantal | Average
Ja | Microsoft.Kusto/Clusters | StreamingIngestDuration | Opname duur van streaming | Milliseconden | Average
Ja | Microsoft.Kusto/Clusters | StreamingIngestResults | Resultaat van streaming-opname | Aantal | Average
Ja | Microsoft.Logic/integrationServiceEnvironments | ActionLatency | Actie latentie  | Seconden | Average
Ja | Microsoft.Logic/integrationServiceEnvironments | ActionsCompleted | Acties voltooid  | Aantal | Totaal
Ja | Microsoft.Logic/integrationServiceEnvironments | ActionsFailed | Mislukte acties  | Aantal | Totaal
Ja | Microsoft.Logic/integrationServiceEnvironments | ActionsSkipped | Overgeslagen acties  | Aantal | Totaal
Ja | Microsoft.Logic/integrationServiceEnvironments | ActionsStarted | Gestarte acties  | Aantal | Totaal
Ja | Microsoft.Logic/integrationServiceEnvironments | ActionsSucceeded | Acties geslaagd  | Aantal | Totaal
Ja | Microsoft.Logic/integrationServiceEnvironments | ActionSuccessLatency | Latentie geslaagde acties  | Seconden | Average
Ja | Microsoft.Logic/integrationServiceEnvironments | ActionThrottledEvents | Door actie vertraagde gebeurtenissen | Aantal | Totaal
Ja | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentConnectorMemoryUsage | Geheugen gebruik van connector voor Integratieserviceomgeving | Procent | Average
Ja | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentConnectorProcessorUsage | Processor gebruik van connector voor Integratieserviceomgeving | Procent | Average
Ja | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentWorkflowMemoryUsage | Geheugen gebruik van werk stroom voor Integratieserviceomgeving | Procent | Average
Ja | Microsoft.Logic/integrationServiceEnvironments | IntegrationServiceEnvironmentWorkflowProcessorUsage | Gebruik van werk stroom processor voor Integratieserviceomgeving | Procent | Average
Ja | Microsoft.Logic/integrationServiceEnvironments | RunFailurePercentage | Percentage mislukte uitvoeringen | Procent | Totaal
Ja | Microsoft.Logic/integrationServiceEnvironments | RunLatency | Uitvoerings latentie | Seconden | Average
Ja | Microsoft.Logic/integrationServiceEnvironments | RunsCancelled | Uitvoeringen geannuleerd | Aantal | Totaal
Ja | Microsoft.Logic/integrationServiceEnvironments | RunsCompleted | Uitvoeringen voltooid | Aantal | Totaal
Ja | Microsoft.Logic/integrationServiceEnvironments | RunsFailed | Uitvoeringen mislukt | Aantal | Totaal
Ja | Microsoft.Logic/integrationServiceEnvironments | RunsStarted | Uitvoeringen gestart | Aantal | Totaal
Ja | Microsoft.Logic/integrationServiceEnvironments | RunsSucceeded | Geslaagde uitvoeringen | Aantal | Totaal
Ja | Microsoft.Logic/integrationServiceEnvironments | RunStartThrottledEvents | Vertraagde gebeurtenissen uitvoeren | Aantal | Totaal
Ja | Microsoft.Logic/integrationServiceEnvironments | RunSuccessLatency | Latentie van geslaagde uitvoering | Seconden | Average
Ja | Microsoft.Logic/integrationServiceEnvironments | RunThrottledEvents | Vertraagde gebeurtenissen uitvoeren | Aantal | Totaal
Ja | Microsoft.Logic/integrationServiceEnvironments | TriggerFireLatency | Brand latentie activeren  | Seconden | Average
Ja | Microsoft.Logic/integrationServiceEnvironments | TriggerLatency | Latentie van trigger  | Seconden | Average
Ja | Microsoft.Logic/integrationServiceEnvironments | TriggersCompleted | Triggers voltooid  | Aantal | Totaal
Ja | Microsoft.Logic/integrationServiceEnvironments | TriggersFailed | Mislukte triggers  | Aantal | Totaal
Ja | Microsoft.Logic/integrationServiceEnvironments | TriggersFired | Geactiveerde triggers  | Aantal | Totaal
Ja | Microsoft.Logic/integrationServiceEnvironments | TriggersSkipped | Triggers overgeslagen | Aantal | Totaal
Ja | Microsoft.Logic/integrationServiceEnvironments | TriggersStarted | Triggers gestart  | Aantal | Totaal
Ja | Microsoft.Logic/integrationServiceEnvironments | TriggersSucceeded | Geslaagde triggers  | Aantal | Totaal
Ja | Microsoft.Logic/integrationServiceEnvironments | TriggerSuccessLatency | Latentie van trigger geslaagd  | Seconden | Average
Ja | Microsoft.Logic/integrationServiceEnvironments | TriggerThrottledEvents | Trigger beperkings gebeurtenissen | Aantal | Totaal
Ja | Microsoft.Logic/workflows | ActionLatency | Actie latentie  | Seconden | Average
Ja | Microsoft.Logic/workflows | ActionsCompleted | Acties voltooid  | Aantal | Totaal
Ja | Microsoft.Logic/workflows | ActionsFailed | Mislukte acties  | Aantal | Totaal
Ja | Microsoft.Logic/workflows | ActionsSkipped | Overgeslagen acties  | Aantal | Totaal
Ja | Microsoft.Logic/workflows | ActionsStarted | Gestarte acties  | Aantal | Totaal
Ja | Microsoft.Logic/workflows | ActionsSucceeded | Acties geslaagd  | Aantal | Totaal
Ja | Microsoft.Logic/workflows | ActionSuccessLatency | Latentie geslaagde acties  | Seconden | Average
Ja | Microsoft.Logic/workflows | ActionThrottledEvents | Door actie vertraagde gebeurtenissen | Aantal | Totaal
Ja | Microsoft.Logic/workflows | BillableActionExecutions | Factureer bare actie-uitvoeringen | Aantal | Totaal
Ja | Microsoft.Logic/workflows | BillableTriggerExecutions | Factureer bare trigger uitvoeringen | Aantal | Totaal
Ja | Microsoft.Logic/workflows | BillingUsageNativeOperation | Facturerings gebruik voor uitvoering van systeem eigen bewerkingen | Aantal | Totaal
Ja | Microsoft.Logic/workflows | BillingUsageNativeOperation | Facturerings gebruik voor uitvoering van systeem eigen bewerkingen | Aantal | Totaal
Ja | Microsoft.Logic/workflows | BillingUsageStandardConnector | Facturerings gebruik voor het uitvoeren van standaard-connectors | Aantal | Totaal
Ja | Microsoft.Logic/workflows | BillingUsageStandardConnector | Facturerings gebruik voor het uitvoeren van standaard-connectors | Aantal | Totaal
Ja | Microsoft.Logic/workflows | BillingUsageStorageConsumption | Facturerings gebruik voor uitvoeringen van opslag verbruik | Aantal | Totaal
Ja | Microsoft.Logic/workflows | BillingUsageStorageConsumption | Facturerings gebruik voor uitvoeringen van opslag verbruik | Aantal | Totaal
Ja | Microsoft.Logic/workflows | RunFailurePercentage | Percentage mislukte uitvoeringen | Procent | Totaal
Ja | Microsoft.Logic/workflows | RunLatency | Uitvoerings latentie | Seconden | Average
Ja | Microsoft.Logic/workflows | RunsCancelled | Uitvoeringen geannuleerd | Aantal | Totaal
Ja | Microsoft.Logic/workflows | RunsCompleted | Uitvoeringen voltooid | Aantal | Totaal
Ja | Microsoft.Logic/workflows | RunsFailed | Uitvoeringen mislukt | Aantal | Totaal
Ja | Microsoft.Logic/workflows | RunsStarted | Uitvoeringen gestart | Aantal | Totaal
Ja | Microsoft.Logic/workflows | RunsSucceeded | Geslaagde uitvoeringen | Aantal | Totaal
Ja | Microsoft.Logic/workflows | RunStartThrottledEvents | Vertraagde gebeurtenissen uitvoeren | Aantal | Totaal
Ja | Microsoft.Logic/workflows | RunSuccessLatency | Latentie van geslaagde uitvoering | Seconden | Average
Ja | Microsoft.Logic/workflows | RunThrottledEvents | Vertraagde gebeurtenissen uitvoeren | Aantal | Totaal
Ja | Microsoft.Logic/workflows | TotalBillableExecutions | Totaal aantal factureer bare uitvoeringen | Aantal | Totaal
Ja | Microsoft.Logic/workflows | TriggerFireLatency | Brand latentie activeren  | Seconden | Average
Ja | Microsoft.Logic/workflows | TriggerLatency | Latentie van trigger  | Seconden | Average
Ja | Microsoft.Logic/workflows | TriggersCompleted | Triggers voltooid  | Aantal | Totaal
Ja | Microsoft.Logic/workflows | TriggersFailed | Mislukte triggers  | Aantal | Totaal
Ja | Microsoft.Logic/workflows | TriggersFired | Geactiveerde triggers  | Aantal | Totaal
Ja | Microsoft.Logic/workflows | TriggersSkipped | Triggers overgeslagen | Aantal | Totaal
Ja | Microsoft.Logic/workflows | TriggersStarted | Triggers gestart  | Aantal | Totaal
Ja | Microsoft.Logic/workflows | TriggersSucceeded | Geslaagde triggers  | Aantal | Totaal
Ja | Microsoft.Logic/workflows | TriggerSuccessLatency | Latentie van trigger geslaagd  | Seconden | Average
Ja | Microsoft.Logic/workflows | TriggerThrottledEvents | Trigger beperkings gebeurtenissen | Aantal | Totaal
Ja | Microsoft.MachineLearningServices/workspaces | Actieve kernen | Actieve kernen | Aantal | Average
Ja | Microsoft.MachineLearningServices/workspaces | Actieve knoop punten | Actieve knoop punten | Aantal | Average
Ja | Microsoft.MachineLearningServices/workspaces | Voltooide uitvoeringen | Voltooide uitvoeringen | Aantal | Totaal
Ja | Microsoft.MachineLearningServices/workspaces | Mislukte verwerkingen | Mislukte verwerkingen | Aantal | Totaal
Ja | Microsoft.MachineLearningServices/workspaces | Niet-actieve kernen | Niet-actieve kernen | Aantal | Average
Ja | Microsoft.MachineLearningServices/workspaces | Niet-actieve knoop punten | Niet-actieve knoop punten | Aantal | Average
Ja | Microsoft.MachineLearningServices/workspaces | Kernen verlaten | Kernen verlaten | Aantal | Average
Ja | Microsoft.MachineLearningServices/workspaces | Knoop punten verlaten | Knoop punten verlaten | Aantal | Average
Ja | Microsoft.MachineLearningServices/workspaces | Modelimplementatie is mislukt | Modelimplementatie is mislukt | Aantal | Totaal
Ja | Microsoft.MachineLearningServices/workspaces | Modelimplementatie gestart | Modelimplementatie gestart | Aantal | Totaal
Ja | Microsoft.MachineLearningServices/workspaces | Modelimplementatie geslaagd | Modelimplementatie geslaagd | Aantal | Totaal
Ja | Microsoft.MachineLearningServices/workspaces | Model register mislukt | Model register mislukt | Aantal | Totaal
Ja | Microsoft.MachineLearningServices/workspaces | Model registratie is voltooid | Model registratie is voltooid | Aantal | Totaal
Ja | Microsoft.MachineLearningServices/workspaces | Afgebroken kernen | Afgebroken kernen | Aantal | Average
Ja | Microsoft.MachineLearningServices/workspaces | Knoop punten die zijn afgebroken | Knoop punten die zijn afgebroken | Aantal | Average
Ja | Microsoft.MachineLearningServices/workspaces | Percentage quotum gebruik | Percentage quotum gebruik | Aantal | Average
Ja | Microsoft.MachineLearningServices/workspaces | Gestart uitvoeringen | Gestart uitvoeringen | Aantal | Totaal
Ja | Microsoft.MachineLearningServices/workspaces | Totaal aantal kernen | Totaal aantal kernen | Aantal | Average
Ja | Microsoft.MachineLearningServices/workspaces | Totaal aantal knoop punten | Totaal aantal knoop punten | Aantal | Average
Ja | Microsoft.MachineLearningServices/workspaces | Onbruikbaar aantal kern geheugens | Onbruikbaar aantal kern geheugens | Aantal | Average
Ja | Microsoft.MachineLearningServices/workspaces | Niet-bruikbare knoop punten | Niet-bruikbare knoop punten | Aantal | Average
Ja | Microsoft.Maps/accounts | Beschikbaarheid | Beschikbaarheid | Procent | Average
Nee | Microsoft.Maps/accounts | Gebruik | Gebruik | Aantal | Aantal
Ja | Micro soft. Media/Media Services | AssetCount | Aantal assets | Aantal | Average
Ja | Micro soft. Media/Media Services | AssetQuota | Activa quotum | Aantal | Average
Ja | Micro soft. Media/Media Services | AssetQuotaUsedPercentage | Percentage gebruikt voor het activa quotum | Procent | Average
Ja | Micro soft. Media/Media Services | ContentKeyPolicyCount | Aantal beleids regels voor inhouds sleutels | Aantal | Average
Ja | Micro soft. Media/Media Services | ContentKeyPolicyQuota | Quotum voor inhouds sleutel beleid | Aantal | Average
Ja | Micro soft. Media/Media Services | ContentKeyPolicyQuotaUsedPercentage | Percentage gebruikt quotum voor inhouds sleutel beleid | Procent | Average
Ja | Micro soft. Media/Media Services | StreamingPolicyCount | Aantal stroomsgewijze beleids regels | Aantal | Average
Ja | Micro soft. Media/Media Services | StreamingPolicyQuota | Quota voor streaming-beleid | Aantal | Average
Ja | Micro soft. Media/Media Services | StreamingPolicyQuotaUsedPercentage | Percentage gebruikt quotum voor het streaming-beleid | Procent | Average
Ja | Micro soft. Media/Media Services/streamingEndpoints | Uitgaand verkeer | Uitgaand verkeer | Bytes | Totaal
Ja | Micro soft. Media/Media Services/streamingEndpoints | Aanvragen | Aanvragen | Aantal | Totaal
Ja | Micro soft. Media/Media Services/streamingEndpoints | SuccessE2ELatency | Geslaagde end-to-end-latentie | Milliseconden | Average
Ja | Micro soft. Microservices4Spring/appClusters | GCPauseTotalCount | Aantal GC-onderbrekingen | Aantal | Totaal
Ja | Micro soft. Microservices4Spring/appClusters | GCPauseTotalTime | Totale tijd van de GC-onderbreking | Milliseconden | Totaal
Ja | Micro soft. Microservices4Spring/appClusters | MaxOldGenMemoryPoolBytes | Maxi maal beschik bare oude generatie gegevens grootte | Bytes | Average
Ja | Micro soft. Microservices4Spring/appClusters | OldGenMemoryPoolBytes | Gegevens grootte van oude generatie | Bytes | Average
Ja | Micro soft. Microservices4Spring/appClusters | OldGenPromotedBytes | Promo veren tot oude generatie gegevens grootte | Bytes | Maximum
Ja | Micro soft. Microservices4Spring/appClusters | ServiceCpuUsagePercentage | CPU-gebruiks percentage van service | Procent | Average
Ja | Micro soft. Microservices4Spring/appClusters | ServiceMemoryCommitted | Toegewezen service geheugen | Bytes | Average
Ja | Micro soft. Microservices4Spring/appClusters | ServiceMemoryMax | Maxi maal service geheugen | Bytes | Maximum
Ja | Micro soft. Microservices4Spring/appClusters | ServiceMemoryUsed | Gebruikt service geheugen | Bytes | Average
Ja | Micro soft. Microservices4Spring/appClusters | SystemCpuUsagePercentage | Percentage van het CPU-gebruik van het systeem | Procent | Average
Ja | Micro soft. Microservices4Spring/appClusters | TomcatErrorCount | Tomcat Global-fout | Aantal | Totaal
Ja | Micro soft. Microservices4Spring/appClusters | TomcatReceivedBytes | Totaal aantal bytes ontvangen Tomcat | Bytes | Totaal
Ja | Micro soft. Microservices4Spring/appClusters | TomcatRequestMaxTime | Maximale tijd voor tomcat-aanvraag | Milliseconden | Maximum
Ja | Micro soft. Microservices4Spring/appClusters | TomcatRequestTotalCount | Totaal aantal Tomcat-aanvragen | Aantal | Totaal
Ja | Micro soft. Microservices4Spring/appClusters | TomcatRequestTotalTime | Totaal aantal keer Tomcat-aanvragen | Milliseconden | Totaal
Ja | Micro soft. Microservices4Spring/appClusters | TomcatResponseAvgTime | Gemiddelde tijd Tomcat-aanvraag | Milliseconden | Average
Ja | Micro soft. Microservices4Spring/appClusters | TomcatSentBytes | Totaal aantal verzonden bytes in Tomcat | Bytes | Totaal
Ja | Micro soft. Microservices4Spring/appClusters | TomcatSessionActiveCurrentCount | Aantal Tomcat-sessies | Aantal | Totaal
Ja | Micro soft. Microservices4Spring/appClusters | TomcatSessionActiveMaxCount | Aantal actieve Tomcat-sessies | Aantal | Totaal
Ja | Micro soft. Microservices4Spring/appClusters | TomcatSessionAliveMaxTime | Time-outperiode van Tomcat-sessie | Milliseconden | Maximum
Ja | Micro soft. Microservices4Spring/appClusters | TomcatSessionCreatedCount | Aantal gemaakte Tomcat-sessies | Aantal | Totaal
Ja | Micro soft. Microservices4Spring/appClusters | TomcatSessionExpiredCount | Aantal verlopen Tomcat-sessies | Aantal | Totaal
Ja | Micro soft. Microservices4Spring/appClusters | TomcatSessionRejectedCount | Aantal geweigerde Tomcat-sessies | Aantal | Totaal
Ja | Micro soft. Microservices4Spring/appClusters | YoungGenPromotedBytes | Promo veren tot jonge generatie gegevens grootte | Bytes | Maximum
Ja | Microsoft.NetApp/netAppAccounts/capacityPools | VolumePoolAllocatedUsed | Gebruikte volume groep | Bytes | Average
Ja | Microsoft.NetApp/netAppAccounts/capacityPools | VolumePoolTotalLogicalSize | Totale logische grootte van volume groep | Bytes | Average
Ja | Micro soft. NetApp/netAppAccounts/capacityPools/volumes | AverageReadLatency | Gemiddelde lees latentie | MilliSeconds | Average
Ja | Micro soft. NetApp/netAppAccounts/capacityPools/volumes | AverageWriteLatency | Gemiddelde schrijf latentie | MilliSeconds | Average
Ja | Micro soft. NetApp/netAppAccounts/capacityPools/volumes | ReadIops | IOPS lezen | CountPerSecond | Average
Ja | Micro soft. NetApp/netAppAccounts/capacityPools/volumes | VolumeLogicalSize | Logische volume grootte | Bytes | Average
Ja | Micro soft. NetApp/netAppAccounts/capacityPools/volumes | VolumeSnapshotSize | Grootte van moment opname van volume | Bytes | Average
Ja | Micro soft. NetApp/netAppAccounts/capacityPools/volumes | WriteIops | IOPS schrijven | CountPerSecond | Average
Nee | Microsoft.Network/applicationGateways | ApplicationGatewayTotalTime | Totale tijd van Application Gateway | MilliSeconds | Average
Nee | Microsoft.Network/applicationGateways | AvgRequestCountPerHealthyHost | Aanvragen per minuut per gegezonde host | Aantal | Average
Nee | Microsoft.Network/applicationGateways | BackendConnectTime | Moment back-end verbinding | MilliSeconds | Average
Nee | Microsoft.Network/applicationGateways | BackendFirstByteResponseTime | Reactie tijd eerste byte van back-end | MilliSeconds | Average
Nee | Microsoft.Network/applicationGateways | BackendLastByteResponseTime | Reactie tijd laatste byte van back-end | MilliSeconds | Average
Ja | Microsoft.Network/applicationGateways | BackendResponseStatus | Reactie status van back-end | Aantal | Totaal
Ja | Microsoft.Network/applicationGateways | BlockedCount | Regel distributie voor door Web Application firewall geblokkeerde aanvragen | Aantal | Totaal
Ja | Microsoft.Network/applicationGateways | BlockedReqCount | Aantal geblokkeerde aanvragen voor Web Application firewall | Aantal | Totaal
Ja | Microsoft.Network/applicationGateways | BytesReceived | Ontvangen bytes | Bytes | Totaal
Ja | Microsoft.Network/applicationGateways | BytesSent | Verzonden bytes | Bytes | Totaal
Nee | Microsoft.Network/applicationGateways | CapacityUnits | Huidige capaciteits eenheden | Aantal | Average
Nee | Microsoft.Network/applicationGateways | ClientRtt | Client RTT | MilliSeconds | Average
Nee | Microsoft.Network/applicationGateways | ComputeUnits | Huidige reken eenheden | Aantal | Average
Ja | Microsoft.Network/applicationGateways | CurrentConnections | Huidige verbindingen | Aantal | Totaal
Ja | Microsoft.Network/applicationGateways | FailedRequests | Mislukte aanvragen | Aantal | Totaal
Ja | Microsoft.Network/applicationGateways | HealthyHostCount | Aantal goede hosts | Aantal | Average
Ja | Microsoft.Network/applicationGateways | MatchedCount | Totale regel distributie Web Application firewall | Aantal | Totaal
Ja | Microsoft.Network/applicationGateways | ResponseStatus | Reactie status | Aantal | Totaal
Nee | Microsoft.Network/applicationGateways | Doorvoer | Doorvoer | BytesPerSecond | Average
Ja | Microsoft.Network/applicationGateways | TlsProtocol | TLS-protocol van client | Aantal | Totaal
Ja | Microsoft.Network/applicationGateways | TotalRequests | Totaal aantal aanvragen | Aantal | Totaal
Ja | Microsoft.Network/applicationGateways | UnhealthyHostCount | Aantal hosts met slechte status | Aantal | Average
Ja | Microsoft.Network/azurefirewalls | ApplicationRuleHit | Aantal treffers toepassings regels | Aantal | Totaal
Ja | Microsoft.Network/azurefirewalls | DataProcessed | Verwerkte gegevens | Bytes | Totaal
Ja | Microsoft.Network/azurefirewalls | FirewallHealth | Status van Firewall | Procent | Average
Ja | Microsoft.Network/azurefirewalls | NetworkRuleHit | Aantal treffers in netwerk regels | Aantal | Totaal
Ja | Microsoft.Network/azurefirewalls | SNATPortUtilization | Gebruik van SNAT-poort | Procent | Average
Ja | Microsoft.Network/connections | BitsInPerSecond | BitsInPerSecond | CountPerSecond | Average
Ja | Microsoft.Network/connections | BitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Average
Ja | Microsoft.Network/dnszones | QueryVolume | Query volume | Aantal | Totaal
Nee | Microsoft.Network/dnszones | RecordSetCapacityUtilization | Capaciteits gebruik van record sets | Procent | Maximum
Ja | Microsoft.Network/dnszones | RecordSetCount | Aantal record sets | Aantal | Maximum
Ja | Microsoft.Network/expressRouteCircuits | ArpAvailability | ARP-Beschik baarheid | Procent | Average
Ja | Microsoft.Network/expressRouteCircuits | BgpAvailability | BGP-Beschik baarheid | Procent | Average
Nee | Microsoft.Network/expressRouteCircuits | BitsInPerSecond | BitsInPerSecond | CountPerSecond | Average
Nee | Microsoft.Network/expressRouteCircuits | BitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Average
Nee | Microsoft.Network/expressRouteCircuits | GlobalReachBitsInPerSecond | GlobalReachBitsInPerSecond | CountPerSecond | Average
Nee | Microsoft.Network/expressRouteCircuits | GlobalReachBitsOutPerSecond | GlobalReachBitsOutPerSecond | CountPerSecond | Average
Nee | Microsoft.Network/expressRouteCircuits | QosDropBitsInPerSecond | DroppedInBitsPerSecond | CountPerSecond | Average
Nee | Microsoft.Network/expressRouteCircuits | QosDropBitsOutPerSecond | DroppedOutBitsPerSecond | CountPerSecond | Average
Ja | Microsoft.Network/expressRouteCircuits/peerings | BitsInPerSecond | BitsInPerSecond | CountPerSecond | Average
Ja | Microsoft.Network/expressRouteCircuits/peerings | BitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Average
Nee | Micro soft. Network/expressRouteGateways | ErGatewayConnectionBitsInPerSecond | BitsInPerSecond | CountPerSecond | Average
Nee | Micro soft. Network/expressRouteGateways | ErGatewayConnectionBitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Average
Ja | Micro soft. Network/expressRoutePorts | AdminState | AdminState | Aantal | Average
Ja | Micro soft. Network/expressRoutePorts | LineProtocol | LineProtocol | Aantal | Average
Ja | Micro soft. Network/expressRoutePorts | PortBitsInPerSecond | BitsInPerSecond | CountPerSecond | Average
Ja | Micro soft. Network/expressRoutePorts | PortBitsOutPerSecond | BitsOutPerSecond | CountPerSecond | Average
Ja | Micro soft. Network/expressRoutePorts | RxLightLevel | RxLightLevel | Aantal | Average
Ja | Micro soft. Network/expressRoutePorts | TxLightLevel | TxLightLevel | Aantal | Average
Ja | Microsoft.Network/frontdoors | BackendHealthPercentage | Back-status percentage | Procent | Average
Ja | Microsoft.Network/frontdoors | BackendRequestCount | Aantal back-aanvragen | Aantal | Totaal
Ja | Microsoft.Network/frontdoors | BackendRequestLatency | Latentie van back-upaanvraag | MilliSeconds | Average
Ja | Microsoft.Network/frontdoors | BillableResponseSize | Grootte van factureer bare antwoorden | Bytes | Totaal
Ja | Microsoft.Network/frontdoors | RequestCount | Aantal aanvragen | Aantal | Totaal
Ja | Microsoft.Network/frontdoors | RequestSize | Aanvraag grootte | Bytes | Totaal
Ja | Microsoft.Network/frontdoors | ResponseSize | Grootte van antwoord | Bytes | Totaal
Ja | Microsoft.Network/frontdoors | TotalLatency | Totale latentie | MilliSeconds | Average
Ja | Microsoft.Network/frontdoors | WebApplicationFirewallRequestCount | Aantal aanvragen voor Web Application firewall | Aantal | Totaal
Nee | Microsoft.Network/loadBalancers | AllocatedSnatPorts | Toegewezen SNAT-poorten (preview-versie) | Aantal | Totaal
Ja | Microsoft.Network/loadBalancers | ByteCount | Aantal bytes | Aantal | Totaal
Ja | Microsoft.Network/loadBalancers | DipAvailability | Status van Health probe | Aantal | Average
Ja | Microsoft.Network/loadBalancers | PacketCount | Aantal pakketten | Aantal | Totaal
Ja | Microsoft.Network/loadBalancers | SnatConnectionCount | Aantal SNAT-verbindingen | Aantal | Totaal
Ja | Microsoft.Network/loadBalancers | SYNCount | SYN-aantal | Aantal | Totaal
Nee | Microsoft.Network/loadBalancers | UsedSnatPorts | Gebruikte SNAT-poorten (preview-versie) | Aantal | Totaal
Ja | Microsoft.Network/loadBalancers | VipAvailability | Beschik baarheid gegevenspad | Aantal | Average
Ja | Microsoft.Network/networkInterfaces | BytesReceivedRate | Ontvangen bytes | Bytes | Totaal
Ja | Microsoft.Network/networkInterfaces | BytesSentRate | Verzonden bytes | Bytes | Totaal
Ja | Microsoft.Network/networkInterfaces | PacketsReceivedRate | Ontvangen pakketten | Aantal | Totaal
Ja | Microsoft.Network/networkInterfaces | PacketsSentRate | Verzonden pakketten | Aantal | Totaal
Ja | Microsoft.Network/networkWatchers/connectionMonitors | AverageRoundtripMs | Gem. retour tijd (MS) | MilliSeconds | Average
Ja | Microsoft.Network/networkWatchers/connectionMonitors | ChecksFailedPercent | Percentage mislukte controles (preview-versie) | Procent | Average
Ja | Microsoft.Network/networkWatchers/connectionMonitors | ProbesFailedPercent | % Tests mislukt | Procent | Average
Ja | Microsoft.Network/networkWatchers/connectionMonitors | RoundTripTimeMs | Retour tijd (MS) (preview-versie) | MilliSeconds | Average
Ja | Microsoft.Network/publicIPAddresses | ByteCount | Aantal bytes | Aantal | Totaal
Ja | Microsoft.Network/publicIPAddresses | BytesDroppedDDoS | Verwijderde binnenkomende bytes DDoS | BytesPerSecond | Maximum
Ja | Microsoft.Network/publicIPAddresses | BytesForwardedDDoS | Doorgestuurde binnenkomende bytes DDoS | BytesPerSecond | Maximum
Ja | Microsoft.Network/publicIPAddresses | BytesInDDoS | Binnenkomende bytes DDoS | BytesPerSecond | Maximum
Ja | Microsoft.Network/publicIPAddresses | DDoSTriggerSYNPackets | Binnenkomende SYN-pakketten om DDoS-beperking te activeren | CountPerSecond | Maximum
Ja | Microsoft.Network/publicIPAddresses | DDoSTriggerTCPPackets | Binnenkomende TCP-pakketten om DDoS-beperking te activeren | CountPerSecond | Maximum
Ja | Microsoft.Network/publicIPAddresses | DDoSTriggerUDPPackets | Binnenkomende UDP-pakketten om DDoS-beperking te activeren | CountPerSecond | Maximum
Ja | Microsoft.Network/publicIPAddresses | IfUnderDDoSAttack | Onder DDoS-aanval of niet | Aantal | Maximum
Ja | Microsoft.Network/publicIPAddresses | PacketCount | Aantal pakketten | Aantal | Totaal
Ja | Microsoft.Network/publicIPAddresses | PacketsDroppedDDoS | Verwijderde binnenkomende pakketten DDoS | CountPerSecond | Maximum
Ja | Microsoft.Network/publicIPAddresses | PacketsForwardedDDoS | Doorgestuurde binnenkomende pakketten DDoS | CountPerSecond | Maximum
Ja | Microsoft.Network/publicIPAddresses | PacketsInDDoS | Binnenkomende pakketten DDoS | CountPerSecond | Maximum
Ja | Microsoft.Network/publicIPAddresses | SynCount | SYN-aantal | Aantal | Totaal
Ja | Microsoft.Network/publicIPAddresses | TCPBytesDroppedDDoS | DDoS binnenkomende TCP-bytes | BytesPerSecond | Maximum
Ja | Microsoft.Network/publicIPAddresses | TCPBytesForwardedDDoS | DDoS doorgestuurde binnenkomende TCP-bytes | BytesPerSecond | Maximum
Ja | Microsoft.Network/publicIPAddresses | TCPBytesInDDoS | Binnenkomende TCP-bytes DDoS | BytesPerSecond | Maximum
Ja | Microsoft.Network/publicIPAddresses | TCPPacketsDroppedDDoS | DDoS binnenkomende TCP-pakketten | CountPerSecond | Maximum
Ja | Microsoft.Network/publicIPAddresses | TCPPacketsForwardedDDoS | Doorgestuurde binnenkomende TCP-pakketten DDoS | CountPerSecond | Maximum
Ja | Microsoft.Network/publicIPAddresses | TCPPacketsInDDoS | Binnenkomende TCP-pakketten DDoS | CountPerSecond | Maximum
Ja | Microsoft.Network/publicIPAddresses | UDPBytesDroppedDDoS | Binnenkomend UDP-bytes verloren DDoS | BytesPerSecond | Maximum
Ja | Microsoft.Network/publicIPAddresses | UDPBytesForwardedDDoS | Doorgestuurde binnenkomende UDP-bytes DDoS | BytesPerSecond | Maximum
Ja | Microsoft.Network/publicIPAddresses | UDPBytesInDDoS | Binnenkomende UDP-bytes DDoS | BytesPerSecond | Maximum
Ja | Microsoft.Network/publicIPAddresses | UDPPacketsDroppedDDoS | Verwijderde binnenkomende UDP-pakketten DDoS | CountPerSecond | Maximum
Ja | Microsoft.Network/publicIPAddresses | UDPPacketsForwardedDDoS | Door inkomende UDP-pakketten DDoS doorgestuurd | CountPerSecond | Maximum
Ja | Microsoft.Network/publicIPAddresses | UDPPacketsInDDoS | Binnenkomende UDP-pakketten DDoS | CountPerSecond | Maximum
Ja | Microsoft.Network/publicIPAddresses | VipAvailability | Beschik baarheid gegevenspad | Aantal | Average
Ja | Microsoft.Network/trafficManagerProfiles | ProbeAgentCurrentEndpointStateByProfileResourceId | Eindpunt status op eind punt | Aantal | Maximum
Ja | Microsoft.Network/trafficManagerProfiles | QpsByEndpoint | Query's op eind punt geretourneerd | Aantal | Totaal
Ja | Microsoft.Network/virtualNetworkGateways | AverageBandwidth | Gateway-S2S-band breedte | BytesPerSecond | Average
Ja | Microsoft.Network/virtualNetworkGateways | P2SBandwidth | Gateway P2S-band breedte | BytesPerSecond | Average
Ja | Microsoft.Network/virtualNetworkGateways | P2SConnectionCount | Aantal P2S-verbindingen | Aantal | Maximum
Ja | Microsoft.Network/virtualNetworkGateways | TunnelAverageBandwidth | Tunnel bandbreedte | BytesPerSecond | Average
Ja | Microsoft.Network/virtualNetworkGateways | TunnelEgressBytes | Bytes voor uitgaand tunnels | Bytes | Totaal
Ja | Microsoft.Network/virtualNetworkGateways | TunnelEgressPacketDropTSMismatch | Uitschakeling van niet-overeenkomende TS-pakketten door tunnel | Aantal | Totaal
Ja | Microsoft.Network/virtualNetworkGateways | TunnelEgressPackets | Tunnel-uituitgangs pakketten | Aantal | Totaal
Ja | Microsoft.Network/virtualNetworkGateways | TunnelIngressBytes | Bytes van de tunnel ingang | Bytes | Totaal
Ja | Microsoft.Network/virtualNetworkGateways | TunnelIngressPacketDropTSMismatch | Verloren gegane pakketten door de tunnel ingang TS komen niet overeen | Aantal | Totaal
Ja | Microsoft.Network/virtualNetworkGateways | TunnelIngressPackets | Tunnel ingangs pakketten | Aantal | Totaal
Ja | Microsoft.Network/virtualNetworks | PingMeshAverageRoundtripMs | Retour tijd voor pings naar een virtuele machine | MilliSeconds | Average
Ja | Microsoft.Network/virtualNetworks | PingMeshProbesFailedPercent | Pingen naar een virtuele machine is mislukt | Procent | Average
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | e-mail | Inkomende berichten | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | inkomend. alle. failedrequests | Alle binnenkomende mislukte aanvragen | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | binnenkomende. alle. aanvragen | Alle inkomende aanvragen | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | inkomend. gepland | Geplande push meldingen verzonden | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | Binnenkomend. gepland. annuleren | Geplande push meldingen geannuleerd | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installatie. alle | Bewerkingen voor installatie beheer | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.delete | Installatie bewerkingen verwijderen | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installation.get | Installatie bewerkingen ophalen | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installatie. patch | Patch-installatie bewerkingen | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | installatie. upsert | Installatie bewerkingen maken of bijwerken | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | notificationhub. pushes | Alle uitgaande meldingen | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.badorexpiredchannel | Ongeldige of verlopen kanaal fouten | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | uitgaande. allpns. channelerror | Kanaal fouten | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.invalidpayload | Payload-fouten | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | uitgaande. allpns. pnserror | Externe meldingen systeem fouten | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.allpns.success | Geslaagde meldingen | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.badchannel | Fout met ongeldige APNS-kanaal | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.expiredchannel | Fout bij verlopen van APNS-kanaal | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidcredentials | APNS-autorisatie fouten | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.invalidnotificationsize | Fout door ongeldige grootte van APNS-melding | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.pnserror | APNS-fouten | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.apns.success | Geslaagde meldingen van APNS | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | uitgaande. GCM. authenticationerror | GCM-verificatie fouten | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.badchannel | GCM ongeldige kanaal fout | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.expiredchannel | GCM-fout met verlopen kanaal | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidcredentials | GCM-verificatie fouten (ongeldige referenties) | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | uitgaande. GCM. invalidnotificationformat | Ongeldige indeling van GCM-melding | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.invalidnotificationsize | Fout met ongeldige grootte van GCM-melding | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | uitgaande. GCM. pnserror | GCM-fouten | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.success | Geslaagde meldingen GCM | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | uitgaande. GCM. throttled | GCM beperkte meldingen | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.gcm.wrongchannel | GCM onjuiste kanaal fout | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | uitgaande. mpns. authenticationerror | MPNS-verificatie fouten | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.badchannel | MPNS ongeldige kanaal fout | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.channeldisconnected | Verbinding met MPNS-kanaal verbroken | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | uitgaande. mpns. dropd | MPNS-verwijderde meldingen | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.mpns.invalidcredentials | Ongeldige referenties MPNS | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | uitgaande. mpns. invalidnotificationformat | Ongeldige indeling van MPNS-melding | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | uitgaande. mpns. pnserror | MPNS-fouten | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | uitgaand. mpns. geslaagd | Geslaagde meldingen MPNS | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | uitgaande. mpns. throttled | MPNS beperkte meldingen | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | uitgaande. wns. authenticationerror | WNS-verificatie fouten | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.badchannel | WNS ongeldige kanaal fout | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channeldisconnected | Verbinding met WNS-kanaal verbroken | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.channelthrottled | WNS-kanaal beperkt | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | uitgaande. wns. dropd | WNS-verwijderde meldingen | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.expiredchannel | WNS-fout met verlopen kanaal | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidcredentials | WNS-verificatie fouten (ongeldige referenties) | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationformat | Ongeldige indeling van WNS-melding | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.invalidnotificationsize | Fout met ongeldige grootte van WNS-melding | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | uitgaande. wns. invalidtoken | WNS-verificatie fouten (ongeldig token) | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | uitgaande. wns. pnserror | WNS-fouten | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.success | Geslaagde meldingen WNS | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | uitgaande. wns. throttled | WNS beperkte meldingen | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.tokenproviderunreachable | WNS-verificatie fouten (onbereikbaar) | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | outgoing.wns.wrongtoken | WNS-autorisatie fouten (onjuist token) | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registratie. alle | Registratie bewerkingen | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registratie. Create | Bewerkingen voor het maken van registratie | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registratie. Delete | Verwijderings bewerkingen voor registratie | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registratie. ophalen | Lees bewerkingen voor registratie | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | registratie. update | Registratie-update bewerkingen | Aantal | Totaal
Ja | Microsoft.NotificationHubs/Namespaces/NotificationHubs | gepland. in behandeling | Geplande meldingen in behandeling | Aantal | Totaal
Ja | Microsoft.OperationalInsights/workspaces | Percentage beschik bare geheugen Average_ | Percentage beschikbaar geheugen | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_ percentage beschik bare wissel ruimte | Percentage beschik bare wissel ruimte | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_% toegewezen bytes in gebruik | % Toegewezen bytes in gebruik | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_ percentage DPC-tijd | Percentage DPC-tijd | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Percentage vrije inodes Average_ | % Vrije inodes | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | % Beschik bare ruimte Average_ | Percentage vrije ruimte | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | % Beschik bare ruimte Average_ | Percentage vrije ruimte | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | % Niet-actieve tijd Average_ | Percentage niet-actieve tijd | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Percentage interrupt-tijd van Average_ | Percentage interrupt-tijd | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_% i/o-wacht tijd | % I/o-wacht tijd | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Tijd van Average_% leuk | Percentage tijd in Nice | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Percentage tijd in beschermde modus Average_ | Percentage tijd in beschermde modus | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Percentage processor tijd van Average_ | % Processortijd | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Percentage processor tijd van Average_ | % Processortijd | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_% gebruikte inodes | % Gebruikte inodes | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_ percentage gebruikt geheugen | Percentage gebruikt geheugen | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Percentage gebruikte ruimte Average_ | Percentage gebruikte ruimte | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_ percentage gebruikte wissel ruimte | Percentage gebruikte wissel ruimte | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Percentage gebruikers tijd van Average_ | Percentage gebruikers tijd | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Available MB | Beschikbare megabytes (MB) | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Available MB geheugen | Beschikbaar geheugen in megabytes | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Available MB wisselen | Beschik bare mega bytes wisselen | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Avg. Schijf sec/lezen | Gemiddelde Lees tijd schijf | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Avg. Schijf sec/lezen | Gemiddelde Lees tijd schijf | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Avg. Schijfoverdrachten per seconde | Gemiddelde tijd schijf overdracht | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Avg. Schijf sec/schrijven | Gemiddelde schrijf tijd schijf | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Avg. Schijf sec/schrijven | Gemiddelde schrijf tijd schijf | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Ontvangen Average_Bytes per seconde | Ontvangen bytes per seconde | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Verzonden Average_Bytes per seconde | Verzonden bytes per seconde | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Bytes totaal per seconde | Totaal aantal bytes per seconde | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Wachtrij lengte van Average_Current schijf | Huidige wachtrij lengte voor de schijf | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Disk Lees bewerkingen in bytes per seconde | Bytes gelezen op schijf/sec | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Disk Reads/sec | Schijf lezen per seconde | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Disk Reads/sec | Schijf lezen per seconde | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Disk overdrachten per seconde | Schijfoverdrachten per seconde | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Disk overdrachten per seconde | Schijfoverdrachten per seconde | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Disk geschreven bytes per seconde | Bytes geschreven naar schijf/sec | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Disk schrijf bewerkingen per seconde | Schijf schrijven per seconde | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Disk schrijf bewerkingen per seconde | Schijf schrijven per seconde | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Free Megabytes | Beschikbare Megabytes | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Free Megabytes | Beschikbare Megabytes | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Free fysiek geheugen | Vrij fysiek geheugen | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Free ruimte in wissel geheugen bestanden | Vrije ruimte in wissel geheugen bestanden | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Virtueel geheugen Average_Free | Vrij virtueel geheugen | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Logical schijf bytes per seconde | Logische schijf Bytes per seconde | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Page Lees bewerkingen per seconde | paginaleesbewerkingen per seconde | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Page schrijf bewerkingen per seconde | paginaschrijfbewerkingen per seconde | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Pages/sec. | pagina's per seconde | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Beschermde tijd van Average_Pct | Pct-geprivilegieerde tijd | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Gebruikers tijd van Average_Pct | Pct-gebruikers tijd | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Physical schijf bytes per seconde | Bytes van fysieke schijf per seconde | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Processes | Processen | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Lengte van Average_Processor wachtrij | Lengte van de processor wachtrij | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Size opgeslagen in Wissel bestanden | Grootte opgeslagen in Wissel bestanden | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Total bytes | Totaal aantal bytes | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Ontvangen Average_Total bytes | Totaal aantal ontvangen Bytes | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Verzonden Average_Total bytes | Totaal aantal verzonden Bytes | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Total conflicten | Totaal aantal conflicten | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Ontvangen Average_Total pakketten | Totaal aantal ontvangen pakketten | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Verzonden Average_Total pakketten | Totaal aantal verzonden pakketten | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Total RX-fouten | Totaal aantal RX-fouten | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Total TX-fouten | Totaal aantal TX-fouten | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Uptime | Actieve tijdsduur | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Used MB wissel ruimte | Gebruikte MB wissel ruimte | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Used geheugen KB | Gebruikte geheugen-kBytes | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Used geheugen MB | Gebruikt geheugen Mbytes | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Users | Gebruikers | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Average_Virtual gedeeld geheugen | Virtueel gedeeld geheugen | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Gebeurtenis | Gebeurtenis | Aantal | Average
Ja | Microsoft.OperationalInsights/workspaces | Hartslag | Hartslag | Aantal | Totaal
Ja | Microsoft.OperationalInsights/workspaces | Update | Update | Aantal | Average
Ja | Micro soft. PowerBIDedicated/capaciteiten | memory_metric | Geheugen | Bytes | Average
Ja | Micro soft. PowerBIDedicated/capaciteiten | memory_thrashing_metric | Geheugen overbelasting (gegevens sets) | Procent | Average
Ja | Micro soft. PowerBIDedicated/capaciteiten | qpu_high_utilization_metric | Hoog QPU-gebruik | Aantal | Totaal
Ja | Micro soft. PowerBIDedicated/capaciteiten | QueryDuration | Query duur (gegevens sets) | Milliseconden | Average
Ja | Micro soft. PowerBIDedicated/capaciteiten | QueryPoolJobQueueLength | Wachtrij lengte van de taak pool voor query's (gegevens sets) | Aantal | Average
Nee | Micro soft. relay/naam ruimten | ActiveConnections | ActiveConnections | Aantal | Totaal
Nee | Micro soft. relay/naam ruimten | ActiveListeners | ActiveListeners | Aantal | Totaal
Ja | Micro soft. relay/naam ruimten | BytesTransferred | BytesTransferred | Aantal | Totaal
Nee | Micro soft. relay/naam ruimten | ListenerConnections-client error | ListenerConnections-client error | Aantal | Totaal
Nee | Micro soft. relay/naam ruimten | ListenerConnections-server error | ListenerConnections-server error | Aantal | Totaal
Nee | Micro soft. relay/naam ruimten | ListenerConnections-Success | ListenerConnections-Success | Aantal | Totaal
Nee | Micro soft. relay/naam ruimten | ListenerConnections-TotalRequests | ListenerConnections-TotalRequests | Aantal | Totaal
Nee | Micro soft. relay/naam ruimten | ListenerDisconnects | ListenerDisconnects | Aantal | Totaal
Nee | Micro soft. relay/naam ruimten | SenderConnections-client error | SenderConnections-client error | Aantal | Totaal
Nee | Micro soft. relay/naam ruimten | SenderConnections-server error | SenderConnections-server error | Aantal | Totaal
Nee | Micro soft. relay/naam ruimten | SenderConnections-Success | SenderConnections-Success | Aantal | Totaal
Nee | Micro soft. relay/naam ruimten | SenderConnections-TotalRequests | SenderConnections-TotalRequests | Aantal | Totaal
Nee | Micro soft. relay/naam ruimten | SenderDisconnects | SenderDisconnects | Aantal | Totaal
Ja | Microsoft.Search/searchServices | SearchLatency | Zoek latentie | Seconden | Average
Ja | Microsoft.Search/searchServices | SearchQueriesPerSecond | Zoek query's per seconde | CountPerSecond | Average
Ja | Microsoft.Search/searchServices | ThrottledSearchQueriesPercentage | Percentage vertraagde Zoek query's | Procent | Average
Nee | Microsoft.ServiceBus/namespaces | ActiveConnections | ActiveConnections | Aantal | Totaal
Nee | Microsoft.ServiceBus/namespaces | ActiveMessages | Aantal actieve berichten in een wachtrij/onderwerp. | Aantal | Average
Nee | Microsoft.ServiceBus/namespaces | ConnectionsClosed | Verbindingen gesloten. | Aantal | Average
Nee | Microsoft.ServiceBus/namespaces | ConnectionsOpened | Geopende verbindingen. | Aantal | Average
Nee | Microsoft.ServiceBus/namespaces | CPUXNS | CPU (afgeschaft) | Procent | Maximum
Nee | Microsoft.ServiceBus/namespaces | DeadletteredMessages | Aantal onbestelbare berichten in een wachtrij/onderwerp. | Aantal | Average
Ja | Microsoft.ServiceBus/namespaces | IncomingMessages | Inkomende berichten | Aantal | Totaal
Ja | Microsoft.ServiceBus/namespaces | IncomingRequests | Binnenkomende aanvragen | Aantal | Totaal
Nee | Microsoft.ServiceBus/namespaces | Berichten | Aantal berichten in een wachtrij/onderwerp. | Aantal | Average
Nee | Microsoft.ServiceBus/namespaces | NamespaceCpuUsage | CPU | Procent | Maximum
Nee | Microsoft.ServiceBus/namespaces | NamespaceMemoryUsage | Geheugengebruik | Procent | Maximum
Ja | Microsoft.ServiceBus/namespaces | OutgoingMessages | Uitgaande berichten | Aantal | Totaal
Nee | Microsoft.ServiceBus/namespaces | ScheduledMessages | Aantal geplande berichten in een wachtrij/onderwerp. | Aantal | Average
Nee | Microsoft.ServiceBus/namespaces | ServerErrors | Server fouten. | Aantal | Totaal
Nee | Microsoft.ServiceBus/namespaces | Grootte | Grootte | Bytes | Average
Nee | Microsoft.ServiceBus/namespaces | SuccessfulRequests | Geslaagde aanvragen | Aantal | Totaal
Nee | Microsoft.ServiceBus/namespaces | ThrottledRequests | Vertraagde aanvragen. | Aantal | Totaal
Nee | Microsoft.ServiceBus/namespaces | UserErrors | Gebruikers fouten. | Aantal | Totaal
Nee | Microsoft.ServiceBus/namespaces | WSXNS | Geheugen gebruik (afgeschaft) | Procent | Maximum
Nee | Microsoft.ServiceFabricMesh/applications | ActualCpu | ActualCpu | Aantal | Average
Nee | Microsoft.ServiceFabricMesh/applications | ActualMemory | ActualMemory | Bytes | Average
Nee | Microsoft.ServiceFabricMesh/applications | AllocatedCpu | AllocatedCpu | Aantal | Average
Nee | Microsoft.ServiceFabricMesh/applications | AllocatedMemory | AllocatedMemory | Bytes | Average
Nee | Microsoft.ServiceFabricMesh/applications | ApplicationStatus | ApplicationStatus | Aantal | Average
Nee | Microsoft.ServiceFabricMesh/applications | Container status | Container status | Aantal | Average
Nee | Microsoft.ServiceFabricMesh/applications | CpuUtilization | CpuUtilization | Procent | Average
Nee | Microsoft.ServiceFabricMesh/applications | MemoryUtilization | MemoryUtilization | Procent | Average
Nee | Microsoft.ServiceFabricMesh/applications | RestartCount | RestartCount | Aantal | Average
Nee | Microsoft.ServiceFabricMesh/applications | ServiceReplicaStatus | ServiceReplicaStatus | Aantal | Average
Nee | Microsoft.ServiceFabricMesh/applications | ServiceStatus | ServiceStatus | Aantal | Average
Ja | Microsoft.SignalRService/SignalR | ConnectionCount | Aantal verbindingen | Aantal | Maximum
Ja | Microsoft.SignalRService/SignalR | InboundTraffic | Binnenkomend verkeer | Bytes | Totaal
Ja | Microsoft.SignalRService/SignalR | MessageCount | Aantal berichten | Aantal | Totaal
Ja | Microsoft.SignalRService/SignalR | OutboundTraffic | Uitgaand verkeer | Bytes | Totaal
Ja | Microsoft.SignalRService/SignalR | SystemErrors | Systeem fouten | Procent | Maximum
Ja | Microsoft.SignalRService/SignalR | UserErrors | Gebruikers fouten | Procent | Maximum
Ja | Micro soft. SQL/managedInstances | avg_cpu_percent | Gemiddeld CPU-percentage | Procent | Average
Ja | Micro soft. SQL/managedInstances | io_bytes_read | Gelezen IO-bytes | Bytes | Average
Ja | Micro soft. SQL/managedInstances | io_bytes_written | Geschreven IO-bytes | Bytes | Average
Ja | Micro soft. SQL/managedInstances | io_requests | Aantal i/o-aanvragen | Aantal | Average
Ja | Micro soft. SQL/managedInstances | reserved_storage_mb | Gereserveerde opslag ruimte | Aantal | Average
Ja | Micro soft. SQL/managedInstances | storage_space_used_mb | Gebruikte opslag ruimte | Aantal | Average
Ja | Micro soft. SQL/managedInstances | virtual_core_count | Aantal virtuele kernen | Aantal | Average
Nee | Microsoft.Sql/servers | database_dtu_consumption_percent | DTU-percentage | Procent | Average
Nee | Microsoft.Sql/servers | database_storage_used | Gebruikte gegevens ruimte | Bytes | Average
Ja | Microsoft.Sql/servers | dtu_consumption_percent | DTU-percentage | Procent | Average
Ja | Microsoft.Sql/servers | dtu_used | DTU gebruikt | Aantal | Average
Ja | Microsoft.Sql/servers | storage_used | Gebruikte gegevens ruimte | Bytes | Average
Ja | Microsoft.Sql/servers/databases | allocated_data_storage | Toegewezen gegevens ruimte | Bytes | Average
Ja | Microsoft.Sql/servers/databases | app_cpu_billed | App CPU gefactureerd | Aantal | Totaal
Ja | Microsoft.Sql/servers/databases | app_cpu_percent | CPU-percentage van app | Procent | Average
Ja | Microsoft.Sql/servers/databases | app_memory_percent | Percentage app-geheugen | Procent | Average
Ja | Microsoft.Sql/servers/databases | blocked_by_firewall | Geblokkeerd door de firewall | Aantal | Totaal
Ja | Microsoft.Sql/servers/databases | cache_hit_percent | Percentage cache treffers | Procent | Maximum
Ja | Microsoft.Sql/servers/databases | cache_used_percent | Percentage gebruikt cache | Procent | Maximum
Ja | Microsoft.Sql/servers/databases | connection_failed | Mislukte verbindingen | Aantal | Totaal
Ja | Microsoft.Sql/servers/databases | connection_successful | Geslaagde verbindingen | Aantal | Totaal
Ja | Microsoft.Sql/servers/databases | cpu_limit | CPU-limiet | Aantal | Average
Ja | Microsoft.Sql/servers/databases | cpu_percent | CPU-percentage | Procent | Average
Ja | Microsoft.Sql/servers/databases | cpu_used | CPU gebruikt | Aantal | Average
Ja | Microsoft.Sql/servers/databases | constateer | Deadlocks | Aantal | Totaal
Ja | Microsoft.Sql/servers/databases | dtu_consumption_percent | DTU-percentage | Procent | Average
Ja | Microsoft.Sql/servers/databases | dtu_limit | DTU-limiet | Aantal | Average
Ja | Microsoft.Sql/servers/databases | dtu_used | DTU gebruikt | Aantal | Average
Ja | Microsoft.Sql/servers/databases | dwu_consumption_percent | Percentage DWU | Procent | Maximum
Ja | Microsoft.Sql/servers/databases | dwu_limit | Limiet voor DWU | Aantal | Maximum
Ja | Microsoft.Sql/servers/databases | dwu_used | DWU gebruikt | Aantal | Maximum
Ja | Microsoft.Sql/servers/databases | local_tempdb_usage_percent | Lokaal TempDB-percentage | Procent | Average
Ja | Microsoft.Sql/servers/databases | log_write_percent | Logboek-IO-percentage | Procent | Average
Ja | Microsoft.Sql/servers/databases | memory_usage_percent | Geheugen percentage | Procent | Maximum
Ja | Microsoft.Sql/servers/databases | physical_data_read_percent | Gegevens-I/O-percentage | Procent | Average
Ja | Microsoft.Sql/servers/databases | sessions_percent | Percentage sessies | Procent | Average
Ja | Microsoft.Sql/servers/databases | opslag | Gebruikte gegevens ruimte | Bytes | Maximum
Ja | Microsoft.Sql/servers/databases | storage_percent | Percentage gebruikte gegevens ruimte | Procent | Maximum
Ja | Microsoft.Sql/servers/databases | tempdb_data_size | Data File grootte van tempdb | Aantal | Maximum
Ja | Microsoft.Sql/servers/databases | tempdb_log_size | Grootte van logboek bestanden tempdb | Aantal | Maximum
Ja | Microsoft.Sql/servers/databases | tempdb_log_used_percent | Percentage gebruikt TempDB-logboek | Procent | Maximum
Ja | Microsoft.Sql/servers/databases | workers_percent | Percentage werk nemers | Procent | Average
Ja | Microsoft.Sql/servers/databases | xtp_storage_percent | Percentage OLTP-opslag in het geheugen | Procent | Average
Ja | Microsoft.Sql/servers/elasticPools | allocated_data_storage | Toegewezen gegevens ruimte | Bytes | Average
Ja | Microsoft.Sql/servers/elasticPools | allocated_data_storage_percent | Percentage toegewezen gegevens ruimte | Procent | Maximum
Ja | Microsoft.Sql/servers/elasticPools | cpu_limit | CPU-limiet | Aantal | Average
Ja | Microsoft.Sql/servers/elasticPools | cpu_percent | CPU-percentage | Procent | Average
Ja | Microsoft.Sql/servers/elasticPools | cpu_used | CPU gebruikt | Aantal | Average
Nee | Microsoft.Sql/servers/elasticPools | database_allocated_data_storage | Toegewezen gegevens ruimte | Bytes | Average
Nee | Microsoft.Sql/servers/elasticPools | database_cpu_limit | CPU-limiet | Aantal | Average
Nee | Microsoft.Sql/servers/elasticPools | database_cpu_percent | CPU-percentage | Procent | Average
Nee | Microsoft.Sql/servers/elasticPools | database_cpu_used | CPU gebruikt | Aantal | Average
Nee | Microsoft.Sql/servers/elasticPools | database_dtu_consumption_percent | DTU-percentage | Procent | Average
Nee | Microsoft.Sql/servers/elasticPools | database_eDTU_used | eDTU gebruikt | Aantal | Average
Nee | Microsoft.Sql/servers/elasticPools | database_log_write_percent | Logboek-IO-percentage | Procent | Average
Nee | Microsoft.Sql/servers/elasticPools | database_physical_data_read_percent | Gegevens-I/O-percentage | Procent | Average
Nee | Microsoft.Sql/servers/elasticPools | database_sessions_percent | Percentage sessies | Procent | Average
Nee | Microsoft.Sql/servers/elasticPools | database_storage_used | Gebruikte gegevens ruimte | Bytes | Average
Nee | Microsoft.Sql/servers/elasticPools | database_workers_percent | Percentage werk nemers | Procent | Average
Ja | Microsoft.Sql/servers/elasticPools | dtu_consumption_percent | DTU-percentage | Procent | Average
Ja | Microsoft.Sql/servers/elasticPools | eDTU_limit | eDTU-limiet | Aantal | Average
Ja | Microsoft.Sql/servers/elasticPools | eDTU_used | eDTU gebruikt | Aantal | Average
Ja | Microsoft.Sql/servers/elasticPools | log_write_percent | Logboek-IO-percentage | Procent | Average
Ja | Microsoft.Sql/servers/elasticPools | physical_data_read_percent | Gegevens-I/O-percentage | Procent | Average
Ja | Microsoft.Sql/servers/elasticPools | sessions_percent | Percentage sessies | Procent | Average
Ja | Microsoft.Sql/servers/elasticPools | storage_limit | Maximale grootte van gegevens | Bytes | Average
Ja | Microsoft.Sql/servers/elasticPools | storage_percent | Percentage gebruikte gegevens ruimte | Procent | Average
Ja | Microsoft.Sql/servers/elasticPools | storage_used | Gebruikte gegevens ruimte | Bytes | Average
Ja | Microsoft.Sql/servers/elasticPools | tempdb_data_size | Data File grootte van tempdb | Aantal | Maximum
Ja | Microsoft.Sql/servers/elasticPools | tempdb_log_size | Grootte van logboek bestanden tempdb | Aantal | Maximum
Ja | Microsoft.Sql/servers/elasticPools | tempdb_log_used_percent | Percentage gebruikt TempDB-logboek | Procent | Maximum
Ja | Microsoft.Sql/servers/elasticPools | workers_percent | Percentage werk nemers | Procent | Average
Ja | Microsoft.Sql/servers/elasticPools | xtp_storage_percent | Percentage OLTP-opslag in het geheugen | Procent | Average
Ja | Microsoft.Storage/storageAccounts | Beschikbaarheid | Beschikbaarheid | Procent | Average
Ja | Microsoft.Storage/storageAccounts | Uitgaand verkeer | Uitgaand verkeer | Bytes | Totaal
Ja | Microsoft.Storage/storageAccounts | Binnenkomend | Binnenkomend | Bytes | Totaal
Ja | Microsoft.Storage/storageAccounts | SuccessE2ELatency | Geslaagde E2E-latentie | Milliseconden | Average
Ja | Microsoft.Storage/storageAccounts | SuccessServerLatency | Geslaagde serverlatentie | Milliseconden | Average
Ja | Microsoft.Storage/storageAccounts | Transacties | Transacties | Aantal | Totaal
Nee | Microsoft.Storage/storageAccounts | UsedCapacity | Gebruikte capaciteit | Bytes | Average
Ja | Microsoft.Storage/storageAccounts/blobServices | Beschikbaarheid | Beschikbaarheid | Procent | Average
Nee | Microsoft.Storage/storageAccounts/blobServices | BlobCapacity | Blobcapaciteit | Bytes | Average
Nee | Microsoft.Storage/storageAccounts/blobServices | BlobCount | Aantal blobs | Aantal | Average
Ja | Microsoft.Storage/storageAccounts/blobServices | ContainerCount | Aantal blobcontainers | Aantal | Average
Ja | Microsoft.Storage/storageAccounts/blobServices | Uitgaand verkeer | Uitgaand verkeer | Bytes | Totaal
Nee | Microsoft.Storage/storageAccounts/blobServices | IndexCapacity | Indexcapaciteit | Bytes | Average
Ja | Microsoft.Storage/storageAccounts/blobServices | Binnenkomend | Binnenkomend | Bytes | Totaal
Ja | Microsoft.Storage/storageAccounts/blobServices | SuccessE2ELatency | Geslaagde E2E-latentie | Milliseconden | Average
Ja | Microsoft.Storage/storageAccounts/blobServices | SuccessServerLatency | Geslaagde serverlatentie | Milliseconden | Average
Ja | Microsoft.Storage/storageAccounts/blobServices | Transacties | Transacties | Aantal | Totaal
Ja | Microsoft.Storage/storageAccounts/fileServices | Beschikbaarheid | Beschikbaarheid | Procent | Average
Ja | Microsoft.Storage/storageAccounts/fileServices | Uitgaand verkeer | Uitgaand verkeer | Bytes | Totaal
Nee | Microsoft.Storage/storageAccounts/fileServices | FileCapacity | Bestands capaciteit | Bytes | Average
Nee | Microsoft.Storage/storageAccounts/fileServices | FileCount | Aantal bestanden | Aantal | Average
Nee | Microsoft.Storage/storageAccounts/fileServices | FileShareCount | Aantal bestands shares | Aantal | Average
Nee | Microsoft.Storage/storageAccounts/fileServices | FileShareQuota | Quota grootte van bestands share | Bytes | Average
Nee | Microsoft.Storage/storageAccounts/fileServices | FileShareSnapshotCount | Aantal moment opnamen van bestands shares | Aantal | Average
Nee | Microsoft.Storage/storageAccounts/fileServices | FileShareSnapshotSize | Grootte van moment opname van bestands share | Bytes | Average
Ja | Microsoft.Storage/storageAccounts/fileServices | Binnenkomend | Binnenkomend | Bytes | Totaal
Ja | Microsoft.Storage/storageAccounts/fileServices | SuccessE2ELatency | Geslaagde E2E-latentie | Milliseconden | Average
Ja | Microsoft.Storage/storageAccounts/fileServices | SuccessServerLatency | Geslaagde serverlatentie | Milliseconden | Average
Ja | Microsoft.Storage/storageAccounts/fileServices | Transacties | Transacties | Aantal | Totaal
Ja | Microsoft.Storage/storageAccounts/queueServices | Beschikbaarheid | Beschikbaarheid | Procent | Average
Ja | Microsoft.Storage/storageAccounts/queueServices | Uitgaand verkeer | Uitgaand verkeer | Bytes | Totaal
Ja | Microsoft.Storage/storageAccounts/queueServices | Binnenkomend | Binnenkomend | Bytes | Totaal
Ja | Microsoft.Storage/storageAccounts/queueServices | QueueCapacity | Wachtrijcapaciteit | Bytes | Average
Ja | Microsoft.Storage/storageAccounts/queueServices | QueueCount | Aantal wachtrijen | Aantal | Average
Ja | Microsoft.Storage/storageAccounts/queueServices | QueueMessageCount | Aantal wachtrijberichten | Aantal | Average
Ja | Microsoft.Storage/storageAccounts/queueServices | SuccessE2ELatency | Geslaagde E2E-latentie | Milliseconden | Average
Ja | Microsoft.Storage/storageAccounts/queueServices | SuccessServerLatency | Geslaagde serverlatentie | Milliseconden | Average
Ja | Microsoft.Storage/storageAccounts/queueServices | Transacties | Transacties | Aantal | Totaal
Ja | Microsoft.Storage/storageAccounts/tableServices | Beschikbaarheid | Beschikbaarheid | Procent | Average
Ja | Microsoft.Storage/storageAccounts/tableServices | Uitgaand verkeer | Uitgaand verkeer | Bytes | Totaal
Ja | Microsoft.Storage/storageAccounts/tableServices | Binnenkomend | Binnenkomend | Bytes | Totaal
Ja | Microsoft.Storage/storageAccounts/tableServices | SuccessE2ELatency | Geslaagde E2E-latentie | Milliseconden | Average
Ja | Microsoft.Storage/storageAccounts/tableServices | SuccessServerLatency | Geslaagde serverlatentie | Milliseconden | Average
Ja | Microsoft.Storage/storageAccounts/tableServices | TableCapacity | Tabelcapaciteit | Bytes | Average
Ja | Microsoft.Storage/storageAccounts/tableServices | TableCount | Aantal tabellen | Aantal | Average
Ja | Microsoft.Storage/storageAccounts/tableServices | TableEntityCount | Aantal tabelentiteiten | Aantal | Average
Ja | Microsoft.Storage/storageAccounts/tableServices | Transacties | Transacties | Aantal | Totaal
Ja | Micro soft. StorageCache/caches | ClientIOPS | Totaal aantal IOPS client | Aantal | Average
Ja | Micro soft. StorageCache/caches | ClientLatency | Gemiddelde client latentie | Milliseconden | Average
Ja | Micro soft. StorageCache/caches | ClientLockIOPS | IOPS-client vergrendeling | CountPerSecond | Average
Ja | Micro soft. StorageCache/caches | ClientMetadataReadIOPS | IOPS voor lezen van meta gegevens van client | CountPerSecond | Average
Ja | Micro soft. StorageCache/caches | ClientMetadataWriteIOPS | IOPS voor schrijven van meta gegevens van client | CountPerSecond | Average
Ja | Micro soft. StorageCache/caches | ClientReadIOPS | Door client gelezen IOPS | CountPerSecond | Average
Ja | Micro soft. StorageCache/caches | ClientReadThroughput | Gemiddelde doorvoer snelheid van cache Lees bewerking | BytesPerSecond | Average
Ja | Micro soft. StorageCache/caches | ClientWriteIOPS | Client schrijf-IOPS | CountPerSecond | Average
Ja | Micro soft. StorageCache/caches | ClientWriteThroughput | Gemiddelde doorvoer snelheid van cache schrijf bewerkingen | BytesPerSecond | Average
Ja | Micro soft. StorageCache/caches | StorageTargetAsyncWriteThroughput | Asynchrone schrijf doorvoer StorageTarget | BytesPerSecond | Average
Ja | Micro soft. StorageCache/caches | StorageTargetFillThroughput | Door Voer voor StorageTarget-vulling | BytesPerSecond | Average
Ja | Micro soft. StorageCache/caches | StorageTargetHealth | Status van opslag doel | Aantal | Average
Ja | Micro soft. StorageCache/caches | StorageTargetIOPS | Totale aantal StorageTarget IOPS | Aantal | Average
Ja | Micro soft. StorageCache/caches | StorageTargetLatency | StorageTarget-latentie | Milliseconden | Average
Ja | Micro soft. StorageCache/caches | StorageTargetMetadataReadIOPS | StorageTarget voor lezen van meta gegevens | CountPerSecond | Average
Ja | Micro soft. StorageCache/caches | StorageTargetMetadataWriteIOPS | StorageTarget-schrijf-IOPS voor meta gegevens | CountPerSecond | Average
Ja | Micro soft. StorageCache/caches | StorageTargetReadAheadThroughput | StorageTarget door Voer lezen | BytesPerSecond | Average
Ja | Micro soft. StorageCache/caches | StorageTargetReadIOPS | StorageTarget lezen IOPS | CountPerSecond | Average
Ja | Micro soft. StorageCache/caches | StorageTargetSyncWriteThroughput | StorageTarget synchrone schrijf doorvoer | BytesPerSecond | Average
Ja | Micro soft. StorageCache/caches | StorageTargetTotalReadThroughput | Totale Lees doorvoer StorageTarget | BytesPerSecond | Average
Ja | Micro soft. StorageCache/caches | StorageTargetTotalWriteThroughput | Totale schrijf doorvoer StorageTarget | BytesPerSecond | Average
Ja | Micro soft. StorageCache/caches | StorageTargetWriteIOPS | StorageTarget write IOPS | Aantal | Average
Ja | Micro soft. StorageCache/caches | Actieve tijdsduur | Actieve tijdsduur | Aantal | Average
Ja | microsoft.storagesync/storageSyncServices | ServerSyncSessionResult | Resultaat van synchronisatie sessie | Aantal | Average
Ja | microsoft.storagesync/storageSyncServices | StorageSyncBatchTransferredFileBytes | Gesynchroniseerde bytes | Bytes | Totaal
Ja | microsoft.storagesync/storageSyncServices | StorageSyncRecalledNetworkBytesByApplication | Grootte van intrekken van Cloud lagen op toepassing | Bytes | Totaal
Ja | microsoft.storagesync/storageSyncServices | StorageSyncRecalledTotalNetworkBytes | Grootte van intrekken Cloud lagen | Bytes | Totaal
Ja | microsoft.storagesync/storageSyncServices | StorageSyncRecallIOTotalSizeBytes | Cloud lagen intrekken | Bytes | Totaal
Ja | microsoft.storagesync/storageSyncServices | StorageSyncRecallThroughputBytesPerSecond | Door Voer van Cloud lagen intrekken | BytesPerSecond | Average
Ja | microsoft.storagesync/storageSyncServices | StorageSyncServerHeartbeat | Online status van de server | Aantal | Maximum
Ja | microsoft.storagesync/storageSyncServices | StorageSyncSyncSessionAppliedFilesCount | Gesynchroniseerde bestanden | Aantal | Totaal
Ja | microsoft.storagesync/storageSyncServices | StorageSyncSyncSessionPerItemErrorsCount | Bestanden die niet worden gesynchroniseerd | Aantal | Totaal
Ja | micro soft. storagesync/storageSyncServices/registeredServer | ServerHeartbeat | Online status van de server | Aantal | Maximum
Ja | micro soft. storagesync/storageSyncServices/registeredServer | ServerRecallIOTotalSizeBytes | Cloud lagen intrekken | Bytes | Totaal
Ja | micro soft. storagesync/storageSyncServices/syncGroups | SyncGroupBatchTransferredFileBytes | Gesynchroniseerde bytes | Bytes | Totaal
Ja | micro soft. storagesync/storageSyncServices/syncGroups | SyncGroupSyncSessionAppliedFilesCount | Gesynchroniseerde bestanden | Aantal | Totaal
Ja | micro soft. storagesync/storageSyncServices/syncGroups | SyncGroupSyncSessionPerItemErrorsCount | Bestanden die niet worden gesynchroniseerd | Aantal | Totaal
Ja | micro soft. storagesync/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointBatchTransferredFileBytes | Gesynchroniseerde bytes | Bytes | Totaal
Ja | micro soft. storagesync/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointSyncSessionAppliedFilesCount | Gesynchroniseerde bestanden | Aantal | Totaal
Ja | micro soft. storagesync/storageSyncServices/syncGroups/serverEndpoints | ServerEndpointSyncSessionPerItemErrorsCount | Bestanden die niet worden gesynchroniseerd | Aantal | Totaal
Ja | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutFailedRequests | Mislukte functieaanvragen | Aantal | Totaal
Ja | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutInputEvents | Functiegebeurtenissen | Aantal | Totaal
Ja | Microsoft.StreamAnalytics/streamingjobs | AMLCalloutRequests | Functieaanvragen | Aantal | Totaal
Ja | Microsoft.StreamAnalytics/streamingjobs | ConversionErrors | Gegevensconversiefouten | Aantal | Totaal
Ja | Microsoft.StreamAnalytics/streamingjobs | DeserializationError | Fouten in invoerdeserialisatie | Aantal | Totaal
Ja | Microsoft.StreamAnalytics/streamingjobs | DroppedOrAdjustedEvents | Gebeurtenissen met een andere volg orde | Aantal | Totaal
Ja | Microsoft.StreamAnalytics/streamingjobs | EarlyInputEvents | Vroege-invoergebeurtenissen | Aantal | Totaal
Ja | Microsoft.StreamAnalytics/streamingjobs | Fouten | Runtimefouten | Aantal | Totaal
Ja | Microsoft.StreamAnalytics/streamingjobs | InputEventBytes | Invoergebeurtenisbytes | Bytes | Totaal
Ja | Microsoft.StreamAnalytics/streamingjobs | InputEvents | Invoergebeurtenissen | Aantal | Totaal
Ja | Microsoft.StreamAnalytics/streamingjobs | InputEventsSourcesBacklogged | Invoervelden met achterstand | Aantal | Maximum
Ja | Microsoft.StreamAnalytics/streamingjobs | InputEventsSourcesPerSecond | Ontvangen invoerbronnen | Aantal | Totaal
Ja | Microsoft.StreamAnalytics/streamingjobs | LateInputEvents | Late invoergebeurtenissen | Aantal | Totaal
Ja | Microsoft.StreamAnalytics/streamingjobs | OutputEvents | Uitvoergebeurtenis | Aantal | Totaal
Ja | Microsoft.StreamAnalytics/streamingjobs | OutputWatermarkDelaySeconds | Watermerkvertraging | Seconden | Maximum
Ja | Microsoft.StreamAnalytics/streamingjobs | ResourceUtilization | Gebruikspercentage voor Streaming-eenheden | Procent | Maximum
Ja | Microsoft.VMwareCloudSimple/virtualMachines | Gelezen bytes op de schijf | Gelezen bytes op de schijf | Bytes | Totaal
Ja | Microsoft.VMwareCloudSimple/virtualMachines | Lees bewerkingen op de schijf per seconde | Lees bewerkingen op de schijf per seconde | CountPerSecond | Average
Ja | Microsoft.VMwareCloudSimple/virtualMachines | Geschreven bytes op de schijf | Geschreven bytes op de schijf | Bytes | Totaal
Ja | Microsoft.VMwareCloudSimple/virtualMachines | Schrijf bewerkingen op de schijf per seconde | Schrijf bewerkingen op de schijf per seconde | CountPerSecond | Average
Ja | Microsoft.VMwareCloudSimple/virtualMachines | DiskReadBytesPerSecond | Gelezen bytes per seconde | BytesPerSecond | Average
Ja | Microsoft.VMwareCloudSimple/virtualMachines | DiskReadLatency | Lees latentie van schijf | Milliseconden | Average
Ja | Microsoft.VMwareCloudSimple/virtualMachines | DiskReadOperations | Lees bewerkingen op de schijf | Aantal | Totaal
Ja | Microsoft.VMwareCloudSimple/virtualMachines | DiskWriteBytesPerSecond | Geschreven bytes per seconde | BytesPerSecond | Average
Ja | Microsoft.VMwareCloudSimple/virtualMachines | DiskWriteLatency | Schrijf latentie schijf | Milliseconden | Average
Ja | Microsoft.VMwareCloudSimple/virtualMachines | DiskWriteOperations | Schrijf bewerkingen op de schijf | Aantal | Totaal
Ja | Microsoft.VMwareCloudSimple/virtualMachines | MemoryActive | Actief geheugen | Bytes | Average
Ja | Microsoft.VMwareCloudSimple/virtualMachines | MemoryGranted | Toegewezen geheugen | Bytes | Average
Ja | Microsoft.VMwareCloudSimple/virtualMachines | MemoryUsed | Gebruikt geheugen | Bytes | Average
Ja | Microsoft.VMwareCloudSimple/virtualMachines | Netwerk in | Netwerk in | Bytes | Totaal
Ja | Microsoft.VMwareCloudSimple/virtualMachines | Netwerk uit | Netwerk uit | Bytes | Totaal
Ja | Microsoft.VMwareCloudSimple/virtualMachines | NetworkInBytesPerSecond | Netwerk in bytes per seconde | BytesPerSecond | Average
Ja | Microsoft.VMwareCloudSimple/virtualMachines | NetworkOutBytesPerSecond | Netwerk uitgaande bytes per seconde | BytesPerSecond | Average
Ja | Microsoft.VMwareCloudSimple/virtualMachines | Percentage CPU | Percentage CPU | Procent | Average
Ja | Microsoft.VMwareCloudSimple/virtualMachines | PercentageCpuReady | Percentage CPU gereed | Milliseconden | Totaal
Ja | Microsoft.Web/hostingEnvironments/multiRolePools | ActiveRequests | Actieve aanvragen | Aantal | Totaal
Ja | Microsoft.Web/hostingEnvironments/multiRolePools | AverageResponseTime | Gemiddelde reactietijd | Seconden | Average
Ja | Microsoft.Web/hostingEnvironments/multiRolePools | BytesReceived | Inkomende gegevens | Bytes | Totaal
Ja | Microsoft.Web/hostingEnvironments/multiRolePools | BytesSent | Uitgaande gegevens | Bytes | Totaal
Ja | Microsoft.Web/hostingEnvironments/multiRolePools | CpuPercentage | CPU-percentage | Procent | Average
Ja | Microsoft.Web/hostingEnvironments/multiRolePools | DiskQueueLength | Wachtrij lengte voor schijf | Aantal | Average
Ja | Microsoft.Web/hostingEnvironments/multiRolePools | Http101 | Http 101 | Aantal | Totaal
Ja | Microsoft.Web/hostingEnvironments/multiRolePools | Http2xx | Http-2xx | Aantal | Totaal
Ja | Microsoft.Web/hostingEnvironments/multiRolePools | Http3xx | HTTP-3xx | Aantal | Totaal
Ja | Microsoft.Web/hostingEnvironments/multiRolePools | Http401 | HTTP 401 | Aantal | Totaal
Ja | Microsoft.Web/hostingEnvironments/multiRolePools | Http403 | HTTP 403 | Aantal | Totaal
Ja | Microsoft.Web/hostingEnvironments/multiRolePools | Http404 | Http 404 | Aantal | Totaal
Ja | Microsoft.Web/hostingEnvironments/multiRolePools | Http406 | Http 406 | Aantal | Totaal
Ja | Microsoft.Web/hostingEnvironments/multiRolePools | Http4xx | Http 4xx | Aantal | Totaal
Ja | Microsoft.Web/hostingEnvironments/multiRolePools | Http5xx | Http-server fouten | Aantal | Totaal
Ja | Microsoft.Web/hostingEnvironments/multiRolePools | HttpQueueLength | Lengte van http-wachtrij | Aantal | Average
Ja | Microsoft.Web/hostingEnvironments/multiRolePools | LargeAppServicePlanInstances | Werk rollen voor grote App Service plannen | Aantal | Average
Ja | Microsoft.Web/hostingEnvironments/multiRolePools | MediumAppServicePlanInstances | Werk nemers met gemiddeld App Service plannen | Aantal | Average
Ja | Microsoft.Web/hostingEnvironments/multiRolePools | MemoryPercentage | Geheugen percentage | Procent | Average
Ja | Microsoft.Web/hostingEnvironments/multiRolePools | Aanvragen | Aanvragen | Aantal | Totaal
Ja | Microsoft.Web/hostingEnvironments/multiRolePools | SmallAppServicePlanInstances | Werk rollen voor kleine App Service plannen | Aantal | Average
Ja | Microsoft.Web/hostingEnvironments/multiRolePools | TotalFrontEnds | Totale front-ends | Aantal | Average
Ja | Microsoft.Web/hostingEnvironments/workerPools | CpuPercentage | CPU-percentage | Procent | Average
Ja | Microsoft.Web/hostingEnvironments/workerPools | MemoryPercentage | Geheugen percentage | Procent | Average
Ja | Microsoft.Web/hostingEnvironments/workerPools | WorkersAvailable | Beschik bare werk nemers | Aantal | Average
Ja | Microsoft.Web/hostingEnvironments/workerPools | WorkersTotal | Totaal aantal werk rollen | Aantal | Average
Ja | Microsoft.Web/hostingEnvironments/workerPools | WorkersUsed | Gebruikte werk rollen | Aantal | Average
Ja | Microsoft.Web/serverfarms | BytesReceived | Inkomende gegevens | Bytes | Totaal
Ja | Microsoft.Web/serverfarms | BytesSent | Uitgaande gegevens | Bytes | Totaal
Ja | Microsoft.Web/serverfarms | CpuPercentage | CPU-percentage | Procent | Average
Ja | Microsoft.Web/serverfarms | DiskQueueLength | Wachtrij lengte voor schijf | Aantal | Average
Ja | Microsoft.Web/serverfarms | HttpQueueLength | Lengte van http-wachtrij | Aantal | Average
Ja | Microsoft.Web/serverfarms | MemoryPercentage | Geheugen percentage | Procent | Average
Ja | Microsoft.Web/serverfarms | TcpCloseWait | TCP-wacht tijd voor sluiten | Aantal | Average
Ja | Microsoft.Web/serverfarms | TcpClosing | TCP sluiten | Aantal | Average
Ja | Microsoft.Web/serverfarms | TcpEstablished | TCP-verbinding | Aantal | Average
Ja | Microsoft.Web/serverfarms | TcpFinWait1 | TCP-FIN-wacht 1 | Aantal | Average
Ja | Microsoft.Web/serverfarms | TcpFinWait2 | TCP FIN WAIT 2 | Aantal | Average
Ja | Microsoft.Web/serverfarms | TcpLastAck | TCP laatste ACK | Aantal | Average
Ja | Microsoft.Web/serverfarms | TcpSynReceived | TCP SYN ontvangen | Aantal | Average
Ja | Microsoft.Web/serverfarms | TcpSynSent | TCP SYN verzonden | Aantal | Average
Ja | Microsoft.Web/serverfarms | TcpTimeWait | Wacht tijd voor TCP-bewerking | Aantal | Average
Ja | Microsoft.Web/sites | AppConnections | Verbindingen | Aantal | Average
Ja | Microsoft.Web/sites | AverageMemoryWorkingSet | Gemiddelde werkset geheugen | Bytes | Average
Ja | Microsoft.Web/sites | AverageResponseTime | Gemiddelde reactietijd | Seconden | Average
Ja | Microsoft.Web/sites | BytesReceived | Inkomende gegevens | Bytes | Totaal
Ja | Microsoft.Web/sites | BytesSent | Uitgaande gegevens | Bytes | Totaal
Ja | Microsoft.Web/sites | CpuTime | CPU-tijd | Seconden | Totaal
Ja | Microsoft.Web/sites | CurrentAssemblies | Huidige Assembly's | Aantal | Average
Ja | Microsoft.Web/sites | FunctionExecutionCount | Aantal functie-uitvoeringen | Aantal | Totaal
Ja | Microsoft.Web/sites | FunctionExecutionUnits | Eenheden voor functie-uitvoering | Aantal | Totaal
Ja | Microsoft.Web/sites | Gen0Collections | Schone verzamelingen van 0 gen | Aantal | Totaal
Ja | Microsoft.Web/sites | Gen1Collections | 1 garbagecollection-verzamelingen | Aantal | Totaal
Ja | Microsoft.Web/sites | Gen2Collections | Opschoon verzamelingen van generatie 2 | Aantal | Totaal
Ja | Microsoft.Web/sites | Formuleer | Aantal ingangen | Aantal | Average
Ja | Microsoft.Web/sites | HealthCheckStatus | Status van de status controle | Aantal | Average
Ja | Microsoft.Web/sites | Http101 | Http 101 | Aantal | Totaal
Ja | Microsoft.Web/sites | Http2xx | Http-2xx | Aantal | Totaal
Ja | Microsoft.Web/sites | Http3xx | HTTP-3xx | Aantal | Totaal
Ja | Microsoft.Web/sites | Http401 | HTTP 401 | Aantal | Totaal
Ja | Microsoft.Web/sites | Http403 | HTTP 403 | Aantal | Totaal
Ja | Microsoft.Web/sites | Http404 | Http 404 | Aantal | Totaal
Ja | Microsoft.Web/sites | Http406 | Http 406 | Aantal | Totaal
Ja | Microsoft.Web/sites | Http4xx | Http 4xx | Aantal | Totaal
Ja | Microsoft.Web/sites | Http5xx | Http-server fouten | Aantal | Totaal
Ja | Microsoft.Web/sites | HttpResponseTime | Reactie tijd | Seconden | Average
Ja | Microsoft.Web/sites | IoOtherBytesPerSecond | Andere i/o-bytes per seconde | BytesPerSecond | Totaal
Ja | Microsoft.Web/sites | IoOtherOperationsPerSecond | Andere i/o-bewerkingen per seconde | BytesPerSecond | Totaal
Ja | Microsoft.Web/sites | IoReadBytesPerSecond | I/o gelezen bytes per seconde | BytesPerSecond | Totaal
Ja | Microsoft.Web/sites | IoReadOperationsPerSecond | I/o-Lees bewerkingen per seconde | BytesPerSecond | Totaal
Ja | Microsoft.Web/sites | IoWriteBytesPerSecond | I/o-schrijf bewerkingen in bytes per seconde | BytesPerSecond | Totaal
Ja | Microsoft.Web/sites | IoWriteOperationsPerSecond | I/o-schrijf bewerkingen per seconde | BytesPerSecond | Totaal
Ja | Microsoft.Web/sites | MemoryWorkingSet | Geheugenwerkset | Bytes | Average
Ja | Microsoft.Web/sites | PrivateBytes | Eigen Bytes | Bytes | Average
Ja | Microsoft.Web/sites | Aanvragen | Aanvragen | Aantal | Totaal
Ja | Microsoft.Web/sites | RequestsInApplicationQueue | Aanvragen in de wachtrij van de toepassing | Aantal | Average
Ja | Microsoft.Web/sites | Threads | Aantal threads | Aantal | Average
Ja | Microsoft.Web/sites | TotalAppDomains | Totaal aantal app-domeinen | Aantal | Average
Ja | Microsoft.Web/sites | TotalAppDomainsUnloaded | Totaal aantal verwijderde app-domeinen | Aantal | Average
Ja | Microsoft.Web/sites/slots | AppConnections | Verbindingen | Aantal | Average
Ja | Microsoft.Web/sites/slots | AverageMemoryWorkingSet | Gemiddelde werkset geheugen | Bytes | Average
Ja | Microsoft.Web/sites/slots | AverageResponseTime | Gemiddelde reactietijd | Seconden | Average
Ja | Microsoft.Web/sites/slots | BytesReceived | Inkomende gegevens | Bytes | Totaal
Ja | Microsoft.Web/sites/slots | BytesSent | Uitgaande gegevens | Bytes | Totaal
Ja | Microsoft.Web/sites/slots | CpuTime | CPU-tijd | Seconden | Totaal
Ja | Microsoft.Web/sites/slots | CurrentAssemblies | Huidige Assembly's | Aantal | Average
Ja | Microsoft.Web/sites/slots | FunctionExecutionCount | Aantal functie-uitvoeringen | Aantal | Totaal
Ja | Microsoft.Web/sites/slots | FunctionExecutionUnits | Eenheden voor functie-uitvoering | Aantal | Totaal
Ja | Microsoft.Web/sites/slots | Gen0Collections | Schone verzamelingen van 0 gen | Aantal | Totaal
Ja | Microsoft.Web/sites/slots | Gen1Collections | 1 garbagecollection-verzamelingen | Aantal | Totaal
Ja | Microsoft.Web/sites/slots | Gen2Collections | Opschoon verzamelingen van generatie 2 | Aantal | Totaal
Ja | Microsoft.Web/sites/slots | Formuleer | Aantal ingangen | Aantal | Average
Ja | Microsoft.Web/sites/slots | HealthCheckStatus | Status van de status controle | Aantal | Average
Ja | Microsoft.Web/sites/slots | Http101 | Http 101 | Aantal | Totaal
Ja | Microsoft.Web/sites/slots | Http2xx | Http-2xx | Aantal | Totaal
Ja | Microsoft.Web/sites/slots | Http3xx | HTTP-3xx | Aantal | Totaal
Ja | Microsoft.Web/sites/slots | Http401 | HTTP 401 | Aantal | Totaal
Ja | Microsoft.Web/sites/slots | Http403 | HTTP 403 | Aantal | Totaal
Ja | Microsoft.Web/sites/slots | Http404 | Http 404 | Aantal | Totaal
Ja | Microsoft.Web/sites/slots | Http406 | Http 406 | Aantal | Totaal
Ja | Microsoft.Web/sites/slots | Http4xx | Http 4xx | Aantal | Totaal
Ja | Microsoft.Web/sites/slots | Http5xx | Http-server fouten | Aantal | Totaal
Ja | Microsoft.Web/sites/slots | HttpResponseTime | Reactie tijd | Seconden | Average
Ja | Microsoft.Web/sites/slots | IoOtherBytesPerSecond | Andere i/o-bytes per seconde | BytesPerSecond | Totaal
Ja | Microsoft.Web/sites/slots | IoOtherOperationsPerSecond | Andere i/o-bewerkingen per seconde | BytesPerSecond | Totaal
Ja | Microsoft.Web/sites/slots | IoReadBytesPerSecond | I/o gelezen bytes per seconde | BytesPerSecond | Totaal
Ja | Microsoft.Web/sites/slots | IoReadOperationsPerSecond | I/o-Lees bewerkingen per seconde | BytesPerSecond | Totaal
Ja | Microsoft.Web/sites/slots | IoWriteBytesPerSecond | I/o-schrijf bewerkingen in bytes per seconde | BytesPerSecond | Totaal
Ja | Microsoft.Web/sites/slots | IoWriteOperationsPerSecond | I/o-schrijf bewerkingen per seconde | BytesPerSecond | Totaal
Ja | Microsoft.Web/sites/slots | MemoryWorkingSet | Geheugenwerkset | Bytes | Average
Ja | Microsoft.Web/sites/slots | PrivateBytes | Eigen Bytes | Bytes | Average
Ja | Microsoft.Web/sites/slots | Aanvragen | Aanvragen | Aantal | Totaal
Ja | Microsoft.Web/sites/slots | RequestsInApplicationQueue | Aanvragen in de wachtrij van de toepassing | Aantal | Average
Ja | Microsoft.Web/sites/slots | Threads | Aantal threads | Aantal | Average
Ja | Microsoft.Web/sites/slots | TotalAppDomains | Totaal aantal app-domeinen | Aantal | Average
Ja | Microsoft.Web/sites/slots | TotalAppDomainsUnloaded | Totaal aantal verwijderde app-domeinen | Aantal | Average
