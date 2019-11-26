---
title: Azure Monitor supported metrics by resource type
description: List of metrics available for each resource type with Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 30e2b880f32f896098778942deb67d7ced9f5c2d
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2019
ms.locfileid: "74484219"
---
# <a name="supported-metrics-with-azure-monitor"></a>Supported metrics with Azure Monitor

Azure Monitor provides several ways to interact with metrics, including charting them in the portal, accessing them through the REST API, or querying them using PowerShell or CLI. Below is a complete list of all metrics currently available with Azure Monitor's metric pipeline. Other metrics may be available in the portal or using legacy APIs. This list below only includes metrics available using the consolidated Azure Monitor metric pipeline. To query for and access these metrics please use the [2018-01-01 api-version](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> Het verzenden van multidimensionale metrische gegevens via diagnostische instellingen wordt momenteel niet ondersteund. Metrische gegevens met dimensies worden geëxporteerd als platte eendimensionale metrische gegevens, als totaal van alle dimensiewaarden.
>
> *Een voorbeeld*: de meetwaarde 'Binnenkomende berichten' voor een Event Hub kan worden verkend en uitgezet op wachtrijniveau. Wanneer de waarde wordt geëxporteerd via diagnostische instellingen, wordt deze echter voorgesteld als alle binnenkomende berichten voor alle wachtrijen in de Event Hub.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|qpu_metric|QPU|Aantal|Average|QPU. Range 0-100 for S1, 0-200 for S2 and 0-400 for S4|ServerResourceType|
|memory_metric|Geheugen|Bytes|Average|Memory. Range 0-25 GB for S1, 0-50 GB for S2 and 0-100 GB for S4|ServerResourceType|
|private_bytes_metric|Private Bytes |Bytes|Average|The total amount of memory the Analysis Services engine process and Mashup container processes have allocated, not including memory shared with other processes.|ServerResourceType|
|virtual_bytes_metric|Virtual Bytes |Bytes|Average|The current size of the virtual address space that Analysis Services engine process and Mashup container processes are using.|ServerResourceType|
|TotalConnectionRequests|Total Connection Requests|Aantal|Average|Total connection requests. These are arrivals.|ServerResourceType|
|SuccessfullConnectionsPerSec|Successful Connections Per Sec|CountPerSecond|Average|Rate of successful connection completions.|ServerResourceType|
|TotalConnectionFailures|Total Connection Failures|Aantal|Average|Total failed connection attempts.|ServerResourceType|
|CurrentUserSessions|Current User Sessions|Aantal|Average|Current number of user sessions established.|ServerResourceType|
|QueryPoolBusyThreads|Query Pool Busy Threads|Aantal|Average|Number of busy threads in the query thread pool.|ServerResourceType|
|CommandPoolJobQueueLength|Command Pool Job Queue Length|Aantal|Average|Number of jobs in the queue of the command thread pool.|ServerResourceType|
|ProcessingPoolJobQueueLength|Processing Pool Job Queue Length|Aantal|Average|Number of non-I/O jobs in the queue of the processing thread pool.|ServerResourceType|
|CurrentConnections|Connection: Current connections|Aantal|Average|Current number of client connections established.|ServerResourceType|
|CleanerCurrentPrice|Memory: Cleaner Current Price|Aantal|Average|Current price of memory, $/byte/time, normalized to 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Memory: Cleaner Memory shrinkable|Bytes|Average|Amount of memory, in bytes, subject to purging by the background cleaner.|ServerResourceType|
|CleanerMemoryNonshrinkable|Memory: Cleaner Memory nonshrinkable|Bytes|Average|Amount of memory, in bytes, not subject to purging by the background cleaner.|ServerResourceType|
|MemoryUsage|Memory: Memory Usage|Bytes|Average|Memory usage of the server process as used in calculating cleaner memory price. Equal to counter Process\PrivateBytes plus the size of memory-mapped data, ignoring any memory which was mapped or allocated by the xVelocity in-memory analytics engine (VertiPaq) in excess of the xVelocity engine Memory Limit.|ServerResourceType|
|MemoryLimitHard|Memory: Memory Limit Hard|Bytes|Average|Hard memory limit, from configuration file.|ServerResourceType|
|MemoryLimitHigh|Memory: Memory Limit High|Bytes|Average|High memory limit, from configuration file.|ServerResourceType|
|MemoryLimitLow|Memory: Memory Limit Low|Bytes|Average|Low memory limit, from configuration file.|ServerResourceType|
|MemoryLimitVertiPaq|Memory: Memory Limit VertiPaq|Bytes|Average|In-memory limit, from configuration file.|ServerResourceType|
|Quota|Memory: Quota|Bytes|Average|Current memory quota, in bytes. Memory quota is also known as a memory grant or memory reservation.|ServerResourceType|
|QuotaBlocked|Memory: Quota Blocked|Aantal|Average|Current number of quota requests that are blocked until other memory quotas are freed.|ServerResourceType|
|VertiPaqNonpaged|Memory: VertiPaq Nonpaged|Bytes|Average|Bytes of memory locked in the working set for use by the in-memory engine.|ServerResourceType|
|VertiPaqPaged|Memory: VertiPaq Paged|Bytes|Average|Bytes of paged memory in use for in-memory data.|ServerResourceType|
|RowsReadPerSec|Processing: Rows read per sec|CountPerSecond|Average|Rate of rows read from all relational databases.|ServerResourceType|
|RowsConvertedPerSec|Processing: Rows converted per sec|CountPerSecond|Average|Rate of rows converted during processing.|ServerResourceType|
|RowsWrittenPerSec|Processing: Rows written per sec|CountPerSecond|Average|Rate of rows written during processing.|ServerResourceType|
|CommandPoolBusyThreads|Threads: Command pool busy threads|Aantal|Average|Number of busy threads in the command thread pool.|ServerResourceType|
|CommandPoolIdleThreads|Threads: Command pool idle threads|Aantal|Average|Number of idle threads in the command thread pool.|ServerResourceType|
|LongParsingBusyThreads|Threads: Long parsing busy threads|Aantal|Average|Number of busy threads in the long parsing thread pool.|ServerResourceType|
|LongParsingIdleThreads|Threads: Long parsing idle threads|Aantal|Average|Number of idle threads in the long parsing thread pool.|ServerResourceType|
|LongParsingJobQueueLength|Threads: Long parsing job queue length|Aantal|Average|Number of jobs in the queue of the long parsing thread pool.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Threads: Processing pool busy I/O job threads|Aantal|Average|Number of threads running I/O jobs in the processing thread pool.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Threads: Processing pool busy non-I/O threads|Aantal|Average|Number of threads running non-I/O jobs in the processing thread pool.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Threads: Processing pool I/O job queue length|Aantal|Average|Number of I/O jobs in the queue of the processing thread pool.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Threads: Processing pool idle I/O job threads|Aantal|Average|Number of idle threads for I/O jobs in the processing thread pool.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Threads: Processing pool idle non-I/O threads|Aantal|Average|Number of idle threads in the processing thread pool dedicated to non-I/O jobs.|ServerResourceType|
|QueryPoolIdleThreads|Threads: Query pool idle threads|Aantal|Average|Number of idle threads for I/O jobs in the processing thread pool.|ServerResourceType|
|QueryPoolJobQueueLength|Threads: Query pool job queue length|Aantal|Average|Number of jobs in the queue of the query thread pool.|ServerResourceType|
|ShortParsingBusyThreads|Threads: Short parsing busy threads|Aantal|Average|Number of busy threads in the short parsing thread pool.|ServerResourceType|
|ShortParsingIdleThreads|Threads: Short parsing idle threads|Aantal|Average|Number of idle threads in the short parsing thread pool.|ServerResourceType|
|ShortParsingJobQueueLength|Threads: Short parsing job queue length|Aantal|Average|Number of jobs in the queue of the short parsing thread pool.|ServerResourceType|
|memory_thrashing_metric|Memory Thrashing|Procent|Average|Average memory thrashing.|ServerResourceType|
|mashup_engine_qpu_metric|M Engine QPU|Aantal|Average|QPU usage by mashup engine processes|ServerResourceType|
|mashup_engine_memory_metric|M Engine Memory|Bytes|Average|Memory usage by mashup engine processes|ServerResourceType|
|mashup_engine_private_bytes_metric|M Engine Private Bytes |Bytes|Average|The total amount of memory Mashup container processes have allocated, not including memory shared with other processes.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|M Engine Virtual Bytes |Bytes|Average|The current size of the virtual address space Mashup container processes are using.|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Aanvragen|Aanvragen|Aantal|Totaal|The total number of gateway requests in a given period. It can be sliced by various dimensions to help you diagnose issues. |Location, BackendResponseCode, LastErrorReason, GatewayResponseCode|
|TotalRequests|Total Gateway Requests|Aantal|Totaal|The total number of gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric. |Location, Hostname|
|SuccessfulRequests|Successful Gateway Requests|Aantal|Totaal|The total number of successful gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric.|Location, Hostname|
|UnauthorizedRequests|Unauthorized Gateway Requests|Aantal|Totaal| The total number of unauthorized gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric.|Location, Hostname|
|FailedRequests|Failed Gateway Requests|Aantal|Totaal|The total number of failed gateway requests in a given period. This metric has been deprecated, we recommend using the new `Requests` metric.|Location, Hostname|
|OtherRequests|Other Gateway Requests|Aantal|Totaal|The total number of gateway requests in a given period that do not fall into the successful, unauthorized, or failed categories. This metric has been deprecated, we recommend using the new `Requests` metric. |Location, Hostname|
|Duur|Overall Duration of Gateway Requests|Milliseconds|Average|The time between when API Management receives a request from a client and when it returns a response to the client.|Location, Hostname|
|Capaciteit|Capaciteit|Procent|Average|Indicator of load on an API Management instance for making informed decisions whether to scale the instance to accommodate more load.|Locatie|
|EventHubTotalEvents|Total EventHub Events|Aantal|Totaal|The total number of events sent to EventHub from API Management in a given period.|Locatie|
|EventHubSuccessfulEvents|Successful EventHub Events|Aantal|Totaal|The total number of successful EventHub events in a given period.|Locatie|
|EventHubTotalFailedEvents|Failed EventHub Events|Aantal|Totaal|The total number of failed EventHub events in a given period.|Locatie|
|EventHubRejectedEvents|Rejected EventHub Events|Aantal|Totaal|The total number of rejected EventHub events (wrong configuration or unauthorized) in a given period.|Locatie|
|EventHubThrottledEvents|Throttled EventHub Events|Aantal|Totaal|The total number of throttled EventHub events in a given period.|Locatie|
|EventHubTimedoutEvents|Timed Out EventHub Events|Aantal|Totaal|The total number of timed out EventHub events in a given period.|Locatie|
|EventHubDroppedEvents|Dropped EventHub Events|Aantal|Totaal|The total number of events skipped because of queue size limit reached in a given period.|Locatie|
|EventHubTotalBytesSent|Size of EventHub Events|Bytes|Totaal|The total size of EventHub events in bytes in a given period.|Locatie|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TotalJob|Total Jobs|Aantal|Totaal|The total number of jobs|Runbook, Status|
|TotalUpdateDeploymentRuns|Total Update Deployment Runs|Aantal|Totaal|Total software update deployment runs|SoftwareUpdateConfigurationName, Status|
|TotalUpdateDeploymentMachineRuns|Total Update Deployment Machine Runs|Aantal|Totaal|Total software update deployment machine runs in a software update deployment run|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CoreCount|Dedicated Core Count|Aantal|Totaal|Total number of dedicated cores in the batch account|No Dimensions|
|TotalNodeCount|Dedicated Node Count|Aantal|Totaal|Total number of dedicated nodes in the batch account|No Dimensions|
|LowPriorityCoreCount|LowPriority Core Count|Aantal|Totaal|Total number of low-priority cores in the batch account|No Dimensions|
|TotalLowPriorityNodeCount|Low-Priority Node Count|Aantal|Totaal|Total number of low-priority nodes in the batch account|No Dimensions|
|CreatingNodeCount|Creating Node Count|Aantal|Totaal|Number of nodes being created|No Dimensions|
|StartingNodeCount|Starting Node Count|Aantal|Totaal|Number of nodes starting|No Dimensions|
|WaitingForStartTaskNodeCount|Waiting For Start Task Node Count|Aantal|Totaal|Number of nodes waiting for the Start Task to complete|No Dimensions|
|StartTaskFailedNodeCount|Start Task Failed Node Count|Aantal|Totaal|Number of nodes where the Start Task has failed|No Dimensions|
|IdleNodeCount|Idle Node Count|Aantal|Totaal|Number of idle nodes|No Dimensions|
|OfflineNodeCount|Offline Node Count|Aantal|Totaal|Number of offline nodes|No Dimensions|
|RebootingNodeCount|Rebooting Node Count|Aantal|Totaal|Number of rebooting nodes|No Dimensions|
|ReimagingNodeCount|Reimaging Node Count|Aantal|Totaal|Number of reimaging nodes|No Dimensions|
|RunningNodeCount|Running Node Count|Aantal|Totaal|Number of running nodes|No Dimensions|
|LeavingPoolNodeCount|Leaving Pool Node Count|Aantal|Totaal|Number of nodes leaving the Pool|No Dimensions|
|UnusableNodeCount|Unusable Node Count|Aantal|Totaal|Number of unusable nodes|No Dimensions|
|PreemptedNodeCount|Preempted Node Count|Aantal|Totaal|Number of preempted nodes|No Dimensions|
|TaskStartEvent|Task Start Events|Aantal|Totaal|Total number of tasks that have started|No Dimensions|
|TaskCompleteEvent|Task Complete Events|Aantal|Totaal|Total number of tasks that have completed|No Dimensions|
|TaskFailEvent|Task Fail Events|Aantal|Totaal|Total number of tasks that have completed in a failed state|No Dimensions|
|PoolCreateEvent|Pool Create Events|Aantal|Totaal|Total number of pools that have been created|No Dimensions|
|PoolResizeStartEvent|Pool Resize Start Events|Aantal|Totaal|Total number of pool resizes that have started|No Dimensions|
|PoolResizeCompleteEvent|Pool Resize Complete Events|Aantal|Totaal|Total number of pool resizes that have completed|No Dimensions|
|PoolDeleteStartEvent|Pool Delete Start Events|Aantal|Totaal|Total number of pool deletes that have started|No Dimensions|
|PoolDeleteCompleteEvent|Pool Delete Complete Events|Aantal|Totaal|Total number of pool deletes that have completed|No Dimensions|
|JobDeleteCompleteEvent|Job Delete Complete Events|Aantal|Totaal|Total number of jobs that have been successfully deleted.|No Dimensions|
|JobDeleteStartEvent|Job Delete Start Events|Aantal|Totaal|Total number of jobs that have been requested to be deleted.|No Dimensions|
|JobDisableCompleteEvent|Job Disable Complete Events|Aantal|Totaal|Total number of jobs that have been successfully disabled.|No Dimensions|
|JobDisableStartEvent|Job Disable Start Events|Aantal|Totaal|Total number of jobs that have been requested to be disabled.|No Dimensions|
|JobStartEvent|Job Start Events|Aantal|Totaal|Total number of jobs that have been successfully started.|No Dimensions|
|JobTerminateCompleteEvent|Job Terminate Complete Events|Aantal|Totaal|Total number of jobs that have been successfully terminated.|No Dimensions|
|JobTerminateStartEvent|Job Terminate Start Events|Aantal|Totaal|Total number of jobs that have been requested to be terminated.|No Dimensions|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|connectedclients|Connected Clients|Aantal|Maximum||ShardId|
|totalcommandsprocessed|Total Operations|Aantal|Totaal||ShardId|
|cachehits|Cache Hits|Aantal|Totaal||ShardId|
|cachemisses|Cache Misses|Aantal|Totaal||ShardId|
|getcommands|Gets|Aantal|Totaal||ShardId|
|setcommands|Sets|Aantal|Totaal||ShardId|
|operationsPerSecond|Operations Per Second|Aantal|Maximum||ShardId|
|evictedkeys|Evicted Keys|Aantal|Totaal||ShardId|
|totalkeys|Total Keys|Aantal|Maximum||ShardId|
|expiredkeys|Expired Keys|Aantal|Totaal||ShardId|
|usedmemory|Used Memory|Bytes|Maximum||ShardId|
|usedmemorypercentage|Used Memory Percentage|Procent|Maximum||ShardId|
|usedmemoryRss|Used Memory RSS|Bytes|Maximum||ShardId|
|serverLoad|Server Load|Procent|Maximum||ShardId|
|cacheWrite|Cache Write|BytesPerSecond|Maximum||ShardId|
|cacheRead|Cache Read|BytesPerSecond|Maximum||ShardId|
|percentProcessorTime|CPU|Procent|Maximum||ShardId|
|cacheLatency|Cache Latency Microseconds (Preview)|Aantal|Average||ShardId, SampleType|
|fouten|Fouten|Aantal|Maximum||ShardId, ErrorType|
|connectedclients0|Connected Clients (Shard 0)|Aantal|Maximum||No Dimensions|
|totalcommandsprocessed0|Total Operations (Shard 0)|Aantal|Totaal||No Dimensions|
|cachehits0|Cache Hits (Shard 0)|Aantal|Totaal||No Dimensions|
|cachemisses0|Cache Misses (Shard 0)|Aantal|Totaal||No Dimensions|
|getcommands0|Gets (Shard 0)|Aantal|Totaal||No Dimensions|
|setcommands0|Sets (Shard 0)|Aantal|Totaal||No Dimensions|
|operationsPerSecond0|Operations Per Second (Shard 0)|Aantal|Maximum||No Dimensions|
|evictedkeys0|Evicted Keys (Shard 0)|Aantal|Totaal||No Dimensions|
|totalkeys0|Total Keys (Shard 0)|Aantal|Maximum||No Dimensions|
|expiredkeys0|Expired Keys (Shard 0)|Aantal|Totaal||No Dimensions|
|usedmemory0|Used Memory (Shard 0)|Bytes|Maximum||No Dimensions|
|usedmemoryRss0|Used Memory RSS (Shard 0)|Bytes|Maximum||No Dimensions|
|serverLoad0|Server Load (Shard 0)|Procent|Maximum||No Dimensions|
|cacheWrite0|Cache Write (Shard 0)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead0|Cache Read (Shard 0)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime0|CPU (Shard 0)|Procent|Maximum||No Dimensions|
|connectedclients1|Connected Clients (Shard 1)|Aantal|Maximum||No Dimensions|
|totalcommandsprocessed1|Total Operations (Shard 1)|Aantal|Totaal||No Dimensions|
|cachehits1|Cache Hits (Shard 1)|Aantal|Totaal||No Dimensions|
|cachemisses1|Cache Misses (Shard 1)|Aantal|Totaal||No Dimensions|
|getcommands1|Gets (Shard 1)|Aantal|Totaal||No Dimensions|
|setcommands1|Sets (Shard 1)|Aantal|Totaal||No Dimensions|
|operationsPerSecond1|Operations Per Second (Shard 1)|Aantal|Maximum||No Dimensions|
|evictedkeys1|Evicted Keys (Shard 1)|Aantal|Totaal||No Dimensions|
|totalkeys1|Total Keys (Shard 1)|Aantal|Maximum||No Dimensions|
|expiredkeys1|Expired Keys (Shard 1)|Aantal|Totaal||No Dimensions|
|usedmemory1|Used Memory (Shard 1)|Bytes|Maximum||No Dimensions|
|usedmemoryRss1|Used Memory RSS (Shard 1)|Bytes|Maximum||No Dimensions|
|serverLoad1|Server Load (Shard 1)|Procent|Maximum||No Dimensions|
|cacheWrite1|Cache Write (Shard 1)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead1|Cache Read (Shard 1)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime1|CPU (Shard 1)|Procent|Maximum||No Dimensions|
|connectedclients2|Connected Clients (Shard 2)|Aantal|Maximum||No Dimensions|
|totalcommandsprocessed2|Total Operations (Shard 2)|Aantal|Totaal||No Dimensions|
|cachehits2|Cache Hits (Shard 2)|Aantal|Totaal||No Dimensions|
|cachemisses2|Cache Misses (Shard 2)|Aantal|Totaal||No Dimensions|
|getcommands2|Gets (Shard 2)|Aantal|Totaal||No Dimensions|
|setcommands2|Sets (Shard 2)|Aantal|Totaal||No Dimensions|
|operationsPerSecond2|Operations Per Second (Shard 2)|Aantal|Maximum||No Dimensions|
|evictedkeys2|Evicted Keys (Shard 2)|Aantal|Totaal||No Dimensions|
|totalkeys2|Total Keys (Shard 2)|Aantal|Maximum||No Dimensions|
|expiredkeys2|Expired Keys (Shard 2)|Aantal|Totaal||No Dimensions|
|usedmemory2|Used Memory (Shard 2)|Bytes|Maximum||No Dimensions|
|usedmemoryRss2|Used Memory RSS (Shard 2)|Bytes|Maximum||No Dimensions|
|serverLoad2|Server Load (Shard 2)|Procent|Maximum||No Dimensions|
|cacheWrite2|Cache Write (Shard 2)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead2|Cache Read (Shard 2)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime2|CPU (Shard 2)|Procent|Maximum||No Dimensions|
|connectedclients3|Connected Clients (Shard 3)|Aantal|Maximum||No Dimensions|
|totalcommandsprocessed3|Total Operations (Shard 3)|Aantal|Totaal||No Dimensions|
|cachehits3|Cache Hits (Shard 3)|Aantal|Totaal||No Dimensions|
|cachemisses3|Cache Misses (Shard 3)|Aantal|Totaal||No Dimensions|
|getcommands3|Gets (Shard 3)|Aantal|Totaal||No Dimensions|
|setcommands3|Sets (Shard 3)|Aantal|Totaal||No Dimensions|
|operationsPerSecond3|Operations Per Second (Shard 3)|Aantal|Maximum||No Dimensions|
|evictedkeys3|Evicted Keys (Shard 3)|Aantal|Totaal||No Dimensions|
|totalkeys3|Total Keys (Shard 3)|Aantal|Maximum||No Dimensions|
|expiredkeys3|Expired Keys (Shard 3)|Aantal|Totaal||No Dimensions|
|usedmemory3|Used Memory (Shard 3)|Bytes|Maximum||No Dimensions|
|usedmemoryRss3|Used Memory RSS (Shard 3)|Bytes|Maximum||No Dimensions|
|serverLoad3|Server Load (Shard 3)|Procent|Maximum||No Dimensions|
|cacheWrite3|Cache Write (Shard 3)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead3|Cache Read (Shard 3)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime3|CPU (Shard 3)|Procent|Maximum||No Dimensions|
|connectedclients4|Connected Clients (Shard 4)|Aantal|Maximum||No Dimensions|
|totalcommandsprocessed4|Total Operations (Shard 4)|Aantal|Totaal||No Dimensions|
|cachehits4|Cache Hits (Shard 4)|Aantal|Totaal||No Dimensions|
|cachemisses4|Cache Misses (Shard 4)|Aantal|Totaal||No Dimensions|
|getcommands4|Gets (Shard 4)|Aantal|Totaal||No Dimensions|
|setcommands4|Sets (Shard 4)|Aantal|Totaal||No Dimensions|
|operationsPerSecond4|Operations Per Second (Shard 4)|Aantal|Maximum||No Dimensions|
|evictedkeys4|Evicted Keys (Shard 4)|Aantal|Totaal||No Dimensions|
|totalkeys4|Total Keys (Shard 4)|Aantal|Maximum||No Dimensions|
|expiredkeys4|Expired Keys (Shard 4)|Aantal|Totaal||No Dimensions|
|usedmemory4|Used Memory (Shard 4)|Bytes|Maximum||No Dimensions|
|usedmemoryRss4|Used Memory RSS (Shard 4)|Bytes|Maximum||No Dimensions|
|serverLoad4|Server Load (Shard 4)|Procent|Maximum||No Dimensions|
|cacheWrite4|Cache Write (Shard 4)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead4|Cache Read (Shard 4)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime4|CPU (Shard 4)|Procent|Maximum||No Dimensions|
|connectedclients5|Connected Clients (Shard 5)|Aantal|Maximum||No Dimensions|
|totalcommandsprocessed5|Total Operations (Shard 5)|Aantal|Totaal||No Dimensions|
|cachehits5|Cache Hits (Shard 5)|Aantal|Totaal||No Dimensions|
|cachemisses5|Cache Misses (Shard 5)|Aantal|Totaal||No Dimensions|
|getcommands5|Gets (Shard 5)|Aantal|Totaal||No Dimensions|
|setcommands5|Sets (Shard 5)|Aantal|Totaal||No Dimensions|
|operationsPerSecond5|Operations Per Second (Shard 5)|Aantal|Maximum||No Dimensions|
|evictedkeys5|Evicted Keys (Shard 5)|Aantal|Totaal||No Dimensions|
|totalkeys5|Total Keys (Shard 5)|Aantal|Maximum||No Dimensions|
|expiredkeys5|Expired Keys (Shard 5)|Aantal|Totaal||No Dimensions|
|usedmemory5|Used Memory (Shard 5)|Bytes|Maximum||No Dimensions|
|usedmemoryRss5|Used Memory RSS (Shard 5)|Bytes|Maximum||No Dimensions|
|serverLoad5|Server Load (Shard 5)|Procent|Maximum||No Dimensions|
|cacheWrite5|Cache Write (Shard 5)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead5|Cache Read (Shard 5)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime5|CPU (Shard 5)|Procent|Maximum||No Dimensions|
|connectedclients6|Connected Clients (Shard 6)|Aantal|Maximum||No Dimensions|
|totalcommandsprocessed6|Total Operations (Shard 6)|Aantal|Totaal||No Dimensions|
|cachehits6|Cache Hits (Shard 6)|Aantal|Totaal||No Dimensions|
|cachemisses6|Cache Misses (Shard 6)|Aantal|Totaal||No Dimensions|
|getcommands6|Gets (Shard 6)|Aantal|Totaal||No Dimensions|
|setcommands6|Sets (Shard 6)|Aantal|Totaal||No Dimensions|
|operationsPerSecond6|Operations Per Second (Shard 6)|Aantal|Maximum||No Dimensions|
|evictedkeys6|Evicted Keys (Shard 6)|Aantal|Totaal||No Dimensions|
|totalkeys6|Total Keys (Shard 6)|Aantal|Maximum||No Dimensions|
|expiredkeys6|Expired Keys (Shard 6)|Aantal|Totaal||No Dimensions|
|usedmemory6|Used Memory (Shard 6)|Bytes|Maximum||No Dimensions|
|usedmemoryRss6|Used Memory RSS (Shard 6)|Bytes|Maximum||No Dimensions|
|serverLoad6|Server Load (Shard 6)|Procent|Maximum||No Dimensions|
|cacheWrite6|Cache Write (Shard 6)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead6|Cache Read (Shard 6)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime6|CPU (Shard 6)|Procent|Maximum||No Dimensions|
|connectedclients7|Connected Clients (Shard 7)|Aantal|Maximum||No Dimensions|
|totalcommandsprocessed7|Total Operations (Shard 7)|Aantal|Totaal||No Dimensions|
|cachehits7|Cache Hits (Shard 7)|Aantal|Totaal||No Dimensions|
|cachemisses7|Cache Misses (Shard 7)|Aantal|Totaal||No Dimensions|
|getcommands7|Gets (Shard 7)|Aantal|Totaal||No Dimensions|
|setcommands7|Sets (Shard 7)|Aantal|Totaal||No Dimensions|
|operationsPerSecond7|Operations Per Second (Shard 7)|Aantal|Maximum||No Dimensions|
|evictedkeys7|Evicted Keys (Shard 7)|Aantal|Totaal||No Dimensions|
|totalkeys7|Total Keys (Shard 7)|Aantal|Maximum||No Dimensions|
|expiredkeys7|Expired Keys (Shard 7)|Aantal|Totaal||No Dimensions|
|usedmemory7|Used Memory (Shard 7)|Bytes|Maximum||No Dimensions|
|usedmemoryRss7|Used Memory RSS (Shard 7)|Bytes|Maximum||No Dimensions|
|serverLoad7|Server Load (Shard 7)|Procent|Maximum||No Dimensions|
|cacheWrite7|Cache Write (Shard 7)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead7|Cache Read (Shard 7)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime7|CPU (Shard 7)|Procent|Maximum||No Dimensions|
|connectedclients8|Connected Clients (Shard 8)|Aantal|Maximum||No Dimensions|
|totalcommandsprocessed8|Total Operations (Shard 8)|Aantal|Totaal||No Dimensions|
|cachehits8|Cache Hits (Shard 8)|Aantal|Totaal||No Dimensions|
|cachemisses8|Cache Misses (Shard 8)|Aantal|Totaal||No Dimensions|
|getcommands8|Gets (Shard 8)|Aantal|Totaal||No Dimensions|
|setcommands8|Sets (Shard 8)|Aantal|Totaal||No Dimensions|
|operationsPerSecond8|Operations Per Second (Shard 8)|Aantal|Maximum||No Dimensions|
|evictedkeys8|Evicted Keys (Shard 8)|Aantal|Totaal||No Dimensions|
|totalkeys8|Total Keys (Shard 8)|Aantal|Maximum||No Dimensions|
|expiredkeys8|Expired Keys (Shard 8)|Aantal|Totaal||No Dimensions|
|usedmemory8|Used Memory (Shard 8)|Bytes|Maximum||No Dimensions|
|usedmemoryRss8|Used Memory RSS (Shard 8)|Bytes|Maximum||No Dimensions|
|serverLoad8|Server Load (Shard 8)|Procent|Maximum||No Dimensions|
|cacheWrite8|Cache Write (Shard 8)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead8|Cache Read (Shard 8)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime8|CPU (Shard 8)|Procent|Maximum||No Dimensions|
|connectedclients9|Connected Clients (Shard 9)|Aantal|Maximum||No Dimensions|
|totalcommandsprocessed9|Total Operations (Shard 9)|Aantal|Totaal||No Dimensions|
|cachehits9|Cache Hits (Shard 9)|Aantal|Totaal||No Dimensions|
|cachemisses9|Cache Misses (Shard 9)|Aantal|Totaal||No Dimensions|
|getcommands9|Gets (Shard 9)|Aantal|Totaal||No Dimensions|
|setcommands9|Sets (Shard 9)|Aantal|Totaal||No Dimensions|
|operationsPerSecond9|Operations Per Second (Shard 9)|Aantal|Maximum||No Dimensions|
|evictedkeys9|Evicted Keys (Shard 9)|Aantal|Totaal||No Dimensions|
|totalkeys9|Total Keys (Shard 9)|Aantal|Maximum||No Dimensions|
|expiredkeys9|Expired Keys (Shard 9)|Aantal|Totaal||No Dimensions|
|usedmemory9|Used Memory (Shard 9)|Bytes|Maximum||No Dimensions|
|usedmemoryRss9|Used Memory RSS (Shard 9)|Bytes|Maximum||No Dimensions|
|serverLoad9|Server Load (Shard 9)|Procent|Maximum||No Dimensions|
|cacheWrite9|Cache Write (Shard 9)|BytesPerSecond|Maximum||No Dimensions|
|cacheRead9|Cache Read (Shard 9)|BytesPerSecond|Maximum||No Dimensions|
|percentProcessorTime9|CPU (Shard 9)|Procent|Maximum||No Dimensions|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Procent|Average|The percentage of allocated compute units that are currently in use by the Virtual Machine(s).|No Dimensions|
|Netwerk in|Netwerk in|Bytes|Totaal|The number of bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic).|No Dimensions|
|Netwerk uit|Netwerk uit|Bytes|Totaal|The number of bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic).|No Dimensions|
|Disk Read Bytes/Sec|Disk Read|BytesPerSecond|Average|Average bytes read from disk during monitoring period.|No Dimensions|
|Disk Write Bytes/Sec|Disk Write|BytesPerSecond|Average|Average bytes written to disk during monitoring period.|No Dimensions|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Average|Disk Read IOPS.|No Dimensions|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Average|Disk Write IOPS.|No Dimensions|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Procent|Average|The percentage of allocated compute units that are currently in use by the Virtual Machine(s).|RoleInstanceId|
|Netwerk in|Netwerk in|Bytes|Totaal|The number of bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic).|RoleInstanceId|
|Netwerk uit|Netwerk uit|Bytes|Totaal|The number of bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic).|RoleInstanceId|
|Disk Read Bytes/Sec|Disk Read|BytesPerSecond|Average|Average bytes read from disk during monitoring period.|RoleInstanceId|
|Disk Write Bytes/Sec|Disk Write|BytesPerSecond|Average|Average bytes written to disk during monitoring period.|RoleInstanceId|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Average|Disk Read IOPS.|RoleInstanceId|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Average|Disk Write IOPS.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TotalCalls|Total Calls|Aantal|Totaal|Total number of calls.|ApiName, OperationName, Region|
|SuccessfulCalls|Successful Calls|Aantal|Totaal|Number of successful calls.|ApiName, OperationName, Region|
|TotalErrors|Total Errors|Aantal|Totaal|Total number of calls with error response (HTTP response code 4xx or 5xx).|ApiName, OperationName, Region|
|BlockedCalls|Blocked Calls|Aantal|Totaal|Number of calls that exceeded rate or quota limit.|ApiName, OperationName, Region|
|ServerErrors|Server Errors|Aantal|Totaal|Number of calls with service internal error (HTTP response code 5xx).|ApiName, OperationName, Region|
|ClientErrors|Client Errors|Aantal|Totaal|Number of calls with client side error (HTTP response code 4xx).|ApiName, OperationName, Region|
|DataIn|Data In|Bytes|Totaal|Size of incoming data in bytes.|ApiName, OperationName, Region|
|DataOut|Data Out|Bytes|Totaal|Size of outgoing data in bytes.|ApiName, OperationName, Region|
|Latentie|Latentie|MilliSeconds|Average|Latency in milliseconds.|ApiName, OperationName, Region|
|CharactersTranslated|Characters Translated|Aantal|Totaal|Total number of characters in incoming text request.|ApiName, OperationName, Region|
|CharactersTrained|Characters Trained|Aantal|Totaal|Total number of characters trained.|ApiName, OperationName, Region|
|SpeechSessionDuration|Speech Session Duration|Seconden|Totaal|Total duration of speech session in seconds.|ApiName, OperationName, Region|
|TotalTransactions|Total Transactions|Aantal|Totaal|Total number of transactions.|No Dimensions|
|TotalTokenCalls|Total Token Calls|Aantal|Totaal|Total number of token calls.|ApiName, OperationName, Region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Procent|Average|The percentage of allocated compute units that are currently in use by the Virtual Machine(s)|No Dimensions|
|Netwerk in|Network In Billable|Bytes|Totaal|The number of billable bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic)|No Dimensions|
|Netwerk uit|Network Out Billable|Bytes|Totaal|The number of billable bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic)|No Dimensions|
|Disk Read Bytes|Disk Read Bytes|Bytes|Totaal|Bytes read from disk during monitoring period|No Dimensions|
|Disk Write Bytes|Disk Write Bytes|Bytes|Totaal|Bytes written to disk during monitoring period|No Dimensions|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Average|Disk Read IOPS|No Dimensions|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Average|Disk Write IOPS|No Dimensions|
|CPU Credits Remaining|CPU Credits Remaining|Aantal|Average|Total number of credits available to burst|No Dimensions|
|CPU Credits Consumed|CPU Credits Consumed|Aantal|Average|Total number of credits consumed by the Virtual Machine|No Dimensions|
|Per Disk Read Bytes/sec|Data Disk Read Bytes/Sec (Deprecated)|CountPerSecond|Average|Bytes/Sec read from a single disk during monitoring period|SlotId|
|Per Disk Write Bytes/sec|Data Disk Write Bytes/Sec (Deprecated)|CountPerSecond|Average|Bytes/Sec written to a single disk during monitoring period|SlotId|
|Per Disk Read Operations/Sec|Data Disk Read Operations/Sec (Deprecated)|CountPerSecond|Average|Read IOPS from a single disk during monitoring period|SlotId|
|Per Disk Write Operations/Sec|Data Disk Write Operations/Sec (Deprecated)|CountPerSecond|Average|Write IOPS from a single disk during monitoring period|SlotId|
|Per Disk QD|Data Disk QD (Deprecated)|Aantal|Average|Data Disk Queue Depth(or Queue Length)|SlotId|
|OS Per Disk Read Bytes/sec|OS Disk Read Bytes/Sec (Deprecated)|CountPerSecond|Average|Bytes/Sec read from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Write Bytes/sec|OS Disk Write Bytes/Sec (Deprecated)|CountPerSecond|Average|Bytes/Sec written to a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Read Operations/Sec|OS Disk Read Operations/Sec (Deprecated)|CountPerSecond|Average|Read IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Write Operations/Sec|OS Disk Write Operations/Sec (Deprecated)|CountPerSecond|Average|Write IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk QD|OS Disk QD (Deprecated)|Aantal|Average|OS Disk Queue Depth(or Queue Length)|No Dimensions|
|Data Disk Read Bytes/sec|Data Disk Read Bytes/Sec (Preview)|CountPerSecond|Average|Bytes/Sec read from a single disk during monitoring period|LUN|
|Data Disk Write Bytes/sec|Data Disk Write Bytes/Sec (Preview)|CountPerSecond|Average|Bytes/Sec written to a single disk during monitoring period|LUN|
|Data Disk Read Operations/Sec|Data Disk Read Operations/Sec (Preview)|CountPerSecond|Average|Read IOPS from a single disk during monitoring period|LUN|
|Data Disk Write Operations/Sec|Data Disk Write Operations/Sec (Preview)|CountPerSecond|Average|Write IOPS from a single disk during monitoring period|LUN|
|Data Disk Queue Depth|Data Disk Queue Depth (Preview)|Aantal|Average|Data Disk Queue Depth(or Queue Length)|LUN|
|OS Disk Read Bytes/sec|OS Disk Read Bytes/Sec (Preview)|CountPerSecond|Average|Bytes/Sec read from a single disk during monitoring period for OS disk|No Dimensions|
|OS Disk Write Bytes/sec|OS Disk Write Bytes/Sec (Preview)|CountPerSecond|Average|Bytes/Sec written to a single disk during monitoring period for OS disk|No Dimensions|
|OS Disk Read Operations/Sec|OS Disk Read Operations/Sec (Preview)|CountPerSecond|Average|Read IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Disk Write Operations/Sec|OS Disk Write Operations/Sec (Preview)|CountPerSecond|Average|Write IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Disk Queue Depth|OS Disk Queue Depth (Preview)|Aantal|Average|OS Disk Queue Depth(or Queue Length)|No Dimensions|
|Inbound Flows|Inbound Flows (Preview)|Aantal|Average|Inbound Flows are number of current flows in the inbound direction (traffic going into the VM)|No Dimensions|
|Outbound Flows|Outbound Flows (Preview)|Aantal|Average|Outbound Flows are number of current flows in the outbound direction (traffic going out of the VM)|No Dimensions|
|Inbound Flows Maximum Creation Rate|Inbound Flows Maximum Creation Rate (Preview)|CountPerSecond|Average|The maximum creation rate of inbound flows (traffic going into the VM)|No Dimensions|
|Outbound Flows Maximum Creation Rate|Outbound Flows Maximum Creation Rate (Preview)|CountPerSecond|Average|The maximum creation rate of outbound flows (traffic going out of the VM)|No Dimensions|
|Premium Data Disk Cache Read Hit|Premium Data Disk Cache Read Hit (Preview)|Procent|Average|Premium Data Disk Cache Read Hit|LUN|
|Premium Data Disk Cache Read Miss|Premium Data Disk Cache Read Miss (Preview)|Procent|Average|Premium Data Disk Cache Read Miss|LUN|
|Premium OS Disk Cache Read Hit|Premium OS Disk Cache Read Hit (Preview)|Procent|Average|Premium OS Disk Cache Read Hit|No Dimensions|
|Premium OS Disk Cache Read Miss|Premium OS Disk Cache Read Miss (Preview)|Procent|Average|Premium OS Disk Cache Read Miss|No Dimensions|
|Network In Total|Network In Total|Bytes|Totaal|The number of bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic)|No Dimensions|
|Network Out Total|Network Out Total|Bytes|Totaal|The number of bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic)|No Dimensions|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Procent|Average|The percentage of allocated compute units that are currently in use by the Virtual Machine(s)|VMName|
|Netwerk in|Network In Billable|Bytes|Totaal|The number of billable bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic)|VMName|
|Netwerk uit|Network Out Billable|Bytes|Totaal|The number of billable bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic)|VMName|
|Disk Read Bytes|Disk Read Bytes|Bytes|Totaal|Bytes read from disk during monitoring period|VMName|
|Disk Write Bytes|Disk Write Bytes|Bytes|Totaal|Bytes written to disk during monitoring period|VMName|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Average|Disk Read IOPS|VMName|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Average|Disk Write IOPS|VMName|
|CPU Credits Remaining|CPU Credits Remaining|Aantal|Average|Total number of credits available to burst|No Dimensions|
|CPU Credits Consumed|CPU Credits Consumed|Aantal|Average|Total number of credits consumed by the Virtual Machine|No Dimensions|
|Per Disk Read Bytes/sec|Data Disk Read Bytes/Sec (Deprecated)|CountPerSecond|Average|Bytes/Sec read from a single disk during monitoring period|SlotId|
|Per Disk Write Bytes/sec|Data Disk Write Bytes/Sec (Deprecated)|CountPerSecond|Average|Bytes/Sec written to a single disk during monitoring period|SlotId|
|Per Disk Read Operations/Sec|Data Disk Read Operations/Sec (Deprecated)|CountPerSecond|Average|Read IOPS from a single disk during monitoring period|SlotId|
|Per Disk Write Operations/Sec|Data Disk Write Operations/Sec (Deprecated)|CountPerSecond|Average|Write IOPS from a single disk during monitoring period|SlotId|
|Per Disk QD|Data Disk QD (Deprecated)|Aantal|Average|Data Disk Queue Depth(or Queue Length)|SlotId|
|OS Per Disk Read Bytes/sec|OS Disk Read Bytes/Sec (Deprecated)|CountPerSecond|Average|Bytes/Sec read from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Write Bytes/sec|OS Disk Write Bytes/Sec (Deprecated)|CountPerSecond|Average|Bytes/Sec written to a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Read Operations/Sec|OS Disk Read Operations/Sec (Deprecated)|CountPerSecond|Average|Read IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk Write Operations/Sec|OS Disk Write Operations/Sec (Deprecated)|CountPerSecond|Average|Write IOPS from a single disk during monitoring period for OS disk|No Dimensions|
|OS Per Disk QD|OS Disk QD (Deprecated)|Aantal|Average|OS Disk Queue Depth(or Queue Length)|No Dimensions|
|Data Disk Read Bytes/sec|Data Disk Read Bytes/Sec (Preview)|CountPerSecond|Average|Bytes/Sec read from a single disk during monitoring period|LUN, VMName|
|Data Disk Write Bytes/sec|Data Disk Write Bytes/Sec (Preview)|CountPerSecond|Average|Bytes/Sec written to a single disk during monitoring period|LUN, VMName|
|Data Disk Read Operations/Sec|Data Disk Read Operations/Sec (Preview)|CountPerSecond|Average|Read IOPS from a single disk during monitoring period|LUN, VMName|
|Data Disk Write Operations/Sec|Data Disk Write Operations/Sec (Preview)|CountPerSecond|Average|Write IOPS from a single disk during monitoring period|LUN, VMName|
|Data Disk Queue Depth|Data Disk Queue Depth (Preview)|Aantal|Average|Data Disk Queue Depth(or Queue Length)|LUN, VMName|
|OS Disk Read Bytes/sec|OS Disk Read Bytes/Sec (Preview)|CountPerSecond|Average|Bytes/Sec read from a single disk during monitoring period for OS disk|VMName|
|OS Disk Write Bytes/sec|OS Disk Write Bytes/Sec (Preview)|CountPerSecond|Average|Bytes/Sec written to a single disk during monitoring period for OS disk|VMName|
|OS Disk Read Operations/Sec|OS Disk Read Operations/Sec (Preview)|CountPerSecond|Average|Read IOPS from a single disk during monitoring period for OS disk|VMName|
|OS Disk Write Operations/Sec|OS Disk Write Operations/Sec (Preview)|CountPerSecond|Average|Write IOPS from a single disk during monitoring period for OS disk|VMName|
|OS Disk Queue Depth|OS Disk Queue Depth (Preview)|Aantal|Average|OS Disk Queue Depth(or Queue Length)|VMName|
|Inbound Flows|Inbound Flows (Preview)|Aantal|Average|Inbound Flows are number of current flows in the inbound direction (traffic going into the VM)|VMName|
|Outbound Flows|Outbound Flows (Preview)|Aantal|Average|Outbound Flows are number of current flows in the outbound direction (traffic going out of the VM)|VMName|
|Inbound Flows Maximum Creation Rate|Inbound Flows Maximum Creation Rate (Preview)|CountPerSecond|Average|The maximum creation rate of inbound flows (traffic going into the VM)|VMName|
|Outbound Flows Maximum Creation Rate|Outbound Flows Maximum Creation Rate (Preview)|CountPerSecond|Average|The maximum creation rate of outbound flows (traffic going out of the VM)|VMName|
|Premium Data Disk Cache Read Hit|Premium Data Disk Cache Read Hit (Preview)|Procent|Average|Premium Data Disk Cache Read Hit|LUN, VMName|
|Premium Data Disk Cache Read Miss|Premium Data Disk Cache Read Miss (Preview)|Procent|Average|Premium Data Disk Cache Read Miss|LUN, VMName|
|Premium OS Disk Cache Read Hit|Premium OS Disk Cache Read Hit (Preview)|Procent|Average|Premium OS Disk Cache Read Hit|VMName|
|Premium OS Disk Cache Read Miss|Premium OS Disk Cache Read Miss (Preview)|Procent|Average|Premium OS Disk Cache Read Miss|VMName|
|Network In Total|Network In Total|Bytes|Totaal|The number of bytes received on all network interfaces by the Virtual Machine(s) (Incoming Traffic)|VMName|
|Network Out Total|Network Out Total|Bytes|Totaal|The number of bytes out on all network interfaces by the Virtual Machine(s) (Outgoing Traffic)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CpuUsage|CPU Usage|Aantal|Average|CPU usage on all cores in millicores.|containerName|
|MemoryUsage|Memory Usage|Bytes|Average|Total memory usage in byte.|containerName|
|NetworkBytesReceivedPerSecond|Network Bytes Received Per Second|Bytes|Average|The network bytes received per second.|No Dimensions|
|NetworkBytesTransmittedPerSecond|Network Bytes Transmitted Per Second|Bytes|Average|The network bytes transmitted per second.|No Dimensions|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TotalPullCount|Total Pull Count|Aantal|Average|Number of image pulls in total|No Dimensions|
|SuccessfulPullCount|Successful Pull Count|Aantal|Average|Number of successful image pulls|No Dimensions|
|TotalPushCount|Total Push Count|Aantal|Average|Number of image pushes in total|No Dimensions|
|SuccessfulPushCount|Successful Push Count|Aantal|Average|Number of successful image pushes|No Dimensions|
|RunDuration|Run Duration|Milliseconds|Totaal|Run Duration in milliseconds|No Dimensions|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Total number of available cpu cores in a managed cluster|Aantal|Totaal|Total number of available cpu cores in a managed cluster|No Dimensions|
|kube_node_status_allocatable_memory_bytes|Total amount of available memory in a managed cluster|Bytes|Totaal|Total amount of available memory in a managed cluster|No Dimensions|
|kube_pod_status_ready|Number of pods in Ready state|Aantal|Totaal|Number of pods in Ready state|namespace, pod|
|kube_node_status_condition|Statuses for various node conditions|Aantal|Totaal|Statuses for various node conditions|condition, status, status2, node|
|kube_pod_status_phase|Number of pods by phase|Aantal|Totaal|Number of pods by phase|phase, namespace, pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights API Calls|Aantal|Totaal||No Dimensions|
|DCIMappingImportOperationSuccessfulLines|Mapping Import Operation Successful Lines|Aantal|Totaal||No Dimensions|
|DCIMappingImportOperationFailedLines|Mapping Import Operation Failed Lines|Aantal|Totaal||No Dimensions|
|DCIMappingImportOperationTotalLines|Mapping Import Operation Total Lines|Aantal|Totaal||No Dimensions|
|DCIMappingImportOperationRuntimeInSeconds|Mapping Import Operation Runtime In Seconds|Seconden|Totaal||No Dimensions|
|DCIOutboundProfileExportSucceeded|Outbound Profile Export Succeeded|Aantal|Totaal||No Dimensions|
|DCIOutboundProfileExportFailed|Outbound Profile Export Failed|Aantal|Totaal||No Dimensions|
|DCIOutboundProfileExportDuration|Outbound Profile Export Duration|Seconden|Totaal||No Dimensions|
|DCIOutboundKpiExportSucceeded|Outbound Kpi Export Succeeded|Aantal|Totaal||No Dimensions|
|DCIOutboundKpiExportFailed|Outbound Kpi Export Failed|Aantal|Totaal||No Dimensions|
|DCIOutboundKpiExportDuration|Outbound Kpi Export Duration|Seconden|Totaal||No Dimensions|
|DCIOutboundKpiExportStarted|Outbound Kpi Export Started|Seconden|Totaal||No Dimensions|
|DCIOutboundKpiRecordCount|Outbound Kpi Record Count|Seconden|Totaal||No Dimensions|
|DCIOutboundProfileExportCount|Outbound Profile Export Count|Seconden|Totaal||No Dimensions|
|DCIOutboundInitialProfileExportFailed|Outbound Initial Profile Export Failed|Seconden|Totaal||No Dimensions|
|DCIOutboundInitialProfileExportSucceeded|Outbound Initial Profile Export Succeeded|Seconden|Totaal||No Dimensions|
|DCIOutboundInitialKpiExportFailed|Outbound Initial Kpi Export Failed|Seconden|Totaal||No Dimensions|
|DCIOutboundInitialKpiExportSucceeded|Outbound Initial Kpi Export Succeeded|Seconden|Totaal||No Dimensions|
|DCIOutboundInitialProfileExportDurationInSeconds|Outbound Initial Profile Export Duration In Seconds|Seconden|Totaal||No Dimensions|
|AdlaJobForStandardKpiFailed|Adla Job For Standard Kpi Failed In Seconds|Seconden|Totaal||No Dimensions|
|AdlaJobForStandardKpiTimeOut|Adla Job For Standard Kpi TimeOut In Seconds|Seconden|Totaal||No Dimensions|
|AdlaJobForStandardKpiCompleted|Adla Job For Standard Kpi Completed In Seconds|Seconden|Totaal||No Dimensions|
|ImportASAValuesFailed|Import ASA Values Failed Count|Aantal|Totaal||No Dimensions|
|ImportASAValuesSucceeded|Import ASA Values Succeeded Count|Aantal|Totaal||No Dimensions|
|DCIProfilesCount|Profile Instance Count|Aantal|Last||No Dimensions|
|DCIInteractionsPerMonthCount|Interactions per Month Count|Aantal|Last||No Dimensions|
|DCIKpisCount|KPI Count|Aantal|Last||No Dimensions|
|DCISegmentsCount|Segment Count|Aantal|Last||No Dimensions|
|DCIPredictiveMatchPoliciesCount|Predictive Match Count|Aantal|Last||No Dimensions|
|DCIPredictionsCount|Prediction Count|Aantal|Last||No Dimensions|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|NICReadThroughput|Read Throughput (Network)|BytesPerSecond|Average|The read throughput of the network interface on the device in the reporting period for all volumes in the gateway.|InstanceName|
|NICWriteThroughput|Write Throughput (Network)|BytesPerSecond|Average|The write throughput of the network interface on the device in the reporting period for all volumes in the gateway.|InstanceName|
|CloudReadThroughputPerShare|Cloud Download Throughput (Share)|BytesPerSecond|Average|The download throughput to Azure from a share during the reporting period.|Delen|
|CloudUploadThroughputPerShare|Cloud Upload Throughput (Share)|BytesPerSecond|Average|The upload throughput to Azure from a share during the reporting period.|Delen|
|BytesUploadedToCloudPerShare|Cloud Bytes Uploaded (Share)|Bytes|Average|The total number of bytes that is uploaded to Azure from a share during the reporting period.|Delen|
|TotalCapacity|Total Capacity|Bytes|Average|Total Capacity|No Dimensions|
|AvailableCapacity|Available Capacity|Bytes|Average|The available capacity in bytes during the reporting period.|No Dimensions|
|CloudUploadThroughput|Cloud Upload Throughput|BytesPerSecond|Average|The cloud upload throughput  to Azure during the reporting period.|No Dimensions|
|CloudReadThroughput|Cloud Download Throughput|BytesPerSecond|Average|The cloud download throughput to Azure during the reporting period.|No Dimensions|
|BytesUploadedToCloud|Cloud Bytes Uploaded (Device)|Bytes|Average|The total number of bytes that is uploaded to Azure from a device during the reporting period.|No Dimensions|
|HyperVVirtualProcessorUtilization|Edge Compute - Percentage CPU|Procent|Average|Percent CPU Usage|InstanceName|
|HyperVMemoryUtilization|Edge Compute - Memory Usage|Procent|Average|Amount of RAM in Use|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|FailedRuns|Failed Runs|Aantal|Totaal||pipelineName, activityName|
|SuccessfulRuns|Successful Runs|Aantal|Totaal||pipelineName, activityName|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PipelineFailedRuns|Failed pipeline runs metrics|Aantal|Totaal||FailureType, Name|
|PipelineSucceededRuns|Succeeded pipeline runs metrics|Aantal|Totaal||FailureType, Name|
|ActivityFailedRuns|Failed activity runs metrics|Aantal|Totaal||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Succeeded activity runs metrics|Aantal|Totaal||ActivityType, PipelineName, FailureType, Name|
|TriggerFailedRuns|Failed trigger runs metrics|Aantal|Totaal||Name, FailureType|
|TriggerSucceededRuns|Succeeded trigger runs metrics|Aantal|Totaal||Name, FailureType|
|IntegrationRuntimeCpuPercentage|Integration runtime CPU utilization|Procent|Average||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableMemory|Integration runtime available memory|Bytes|Average||IntegrationRuntimeName, NodeName|
|MaxAllowedResourceCount|Maximum allowed entities count|Aantal|Maximum||No Dimensions|
|MaxAllowedFactorySizeInGbUnits|Maximum allowed factory size (GB unit)|Aantal|Maximum||No Dimensions|
|ResourceCount|Total entities count|Aantal|Maximum||No Dimensions|
|FactorySizeInGbUnits|Total factory size (GB unit)|Aantal|Maximum||No Dimensions|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|JobEndedSuccess|Successful Jobs|Aantal|Totaal|Count of successful jobs.|No Dimensions|
|JobEndedFailure|Failed Jobs|Aantal|Totaal|Count of failed jobs.|No Dimensions|
|JobEndedCancelled|Canceled Jobs|Aantal|Totaal|Count of canceled jobs.|No Dimensions|
|JobAUEndedSuccess|Successful AU Time|Seconden|Totaal|Total AU time for successful jobs.|No Dimensions|
|JobAUEndedFailure|Failed AU Time|Seconden|Totaal|Total AU time for failed jobs.|No Dimensions|
|JobAUEndedCancelled|Canceled AU Time|Seconden|Totaal|Total AU time for canceled jobs.|No Dimensions|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TotalStorage|Totale opslag|Bytes|Maximum|Total amount of data stored in the account.|No Dimensions|
|DataWritten|Data Written|Bytes|Totaal|Total amount of data written to the account.|No Dimensions|
|DataRead|Data Read|Bytes|Totaal|Total amount of data read from the account.|No Dimensions|
|WriteRequests|Write Requests|Aantal|Totaal|Count of data write requests to the account.|No Dimensions|
|ReadRequests|Read Requests|Aantal|Totaal|Count of data read requests to the account.|No Dimensions|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU percent|Procent|Average|CPU percent|No Dimensions|
|memory_percent|Memory percent|Procent|Average|Memory percent|No Dimensions|
|io_consumption_percent|IO percent|Procent|Average|IO percent|No Dimensions|
|storage_percent|Storage percent|Procent|Average|Storage percent|No Dimensions|
|storage_used|Gebruikte opslag|Bytes|Average|Gebruikte opslag|No Dimensions|
|storage_limit|Storage limit|Bytes|Average|Storage limit|No Dimensions|
|serverlog_storage_percent|Server Log storage percent|Procent|Average|Server Log storage percent|No Dimensions|
|serverlog_storage_usage|Server Log storage used|Bytes|Average|Server Log storage used|No Dimensions|
|serverlog_storage_limit|Server Log storage limit|Bytes|Average|Server Log storage limit|No Dimensions|
|active_connections|Active Connections|Aantal|Average|Active Connections|No Dimensions|
|connections_failed|Mislukte verbindingen|Aantal|Totaal|Mislukte verbindingen|No Dimensions|
|seconds_behind_master|Replication lag in seconds|Aantal|Average|Replication lag in seconds|No Dimensions|
|backup_storage_used|Backup Storage used|Bytes|Average|Backup Storage used|No Dimensions|
|network_bytes_egress|Netwerk uit|Bytes|Totaal|Network Out across active connections|No Dimensions|
|network_bytes_ingress|Netwerk in|Bytes|Totaal|Network In across active connections|No Dimensions|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU percent|Procent|Average|CPU percent|No Dimensions|
|memory_percent|Memory percent|Procent|Average|Memory percent|No Dimensions|
|io_consumption_percent|IO percent|Procent|Average|IO percent|No Dimensions|
|storage_percent|Storage percent|Procent|Average|Storage percent|No Dimensions|
|storage_used|Gebruikte opslag|Bytes|Average|Gebruikte opslag|No Dimensions|
|storage_limit|Storage limit|Bytes|Average|Storage limit|No Dimensions|
|serverlog_storage_percent|Server Log storage percent|Procent|Average|Server Log storage percent|No Dimensions|
|serverlog_storage_usage|Server Log storage used|Bytes|Average|Server Log storage used|No Dimensions|
|serverlog_storage_limit|Server Log storage limit|Bytes|Average|Server Log storage limit|No Dimensions|
|active_connections|Active Connections|Aantal|Average|Active Connections|No Dimensions|
|connections_failed|Mislukte verbindingen|Aantal|Totaal|Mislukte verbindingen|No Dimensions|
|seconds_behind_master|Replication lag in seconds|Aantal|Average|Replication lag in seconds|No Dimensions|
|backup_storage_used|Backup Storage used|Bytes|Average|Backup Storage used|No Dimensions|
|network_bytes_egress|Netwerk uit|Bytes|Totaal|Network Out across active connections|No Dimensions|
|network_bytes_ingress|Netwerk in|Bytes|Totaal|Network In across active connections|No Dimensions|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU percent|Procent|Average|CPU percent|No Dimensions|
|memory_percent|Memory percent|Procent|Average|Memory percent|No Dimensions|
|io_consumption_percent|IO percent|Procent|Average|IO percent|No Dimensions|
|storage_percent|Storage percent|Procent|Average|Storage percent|No Dimensions|
|storage_used|Gebruikte opslag|Bytes|Average|Gebruikte opslag|No Dimensions|
|storage_limit|Storage limit|Bytes|Average|Storage limit|No Dimensions|
|serverlog_storage_percent|Server Log storage percent|Procent|Average|Server Log storage percent|No Dimensions|
|serverlog_storage_usage|Server Log storage used|Bytes|Average|Server Log storage used|No Dimensions|
|serverlog_storage_limit|Server Log storage limit|Bytes|Average|Server Log storage limit|No Dimensions|
|active_connections|Active Connections|Aantal|Average|Active Connections|No Dimensions|
|connections_failed|Mislukte verbindingen|Aantal|Totaal|Mislukte verbindingen|No Dimensions|
|backup_storage_used|Backup Storage used|Bytes|Average|Backup Storage used|No Dimensions|
|network_bytes_egress|Netwerk uit|Bytes|Totaal|Network Out across active connections|No Dimensions|
|network_bytes_ingress|Netwerk in|Bytes|Totaal|Network In across active connections|No Dimensions|
|pg_replica_log_delay_in_seconds|Replica Lag|Seconden|Maximum|Replica lag in seconds|No Dimensions|
|pg_replica_log_delay_in_bytes|Max Lag Across Replicas|Bytes|Maximum|Lag in bytes of the most lagging replica|No Dimensions|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU percent|Procent|Average|CPU percent|No Dimensions|
|memory_percent|Memory percent|Procent|Average|Memory percent|No Dimensions|
|iops|IOPS|Aantal|Average|IO Operations per second|No Dimensions|
|storage_percent|Storage percent|Procent|Average|Storage percent|No Dimensions|
|storage_used|Gebruikte opslag|Bytes|Average|Gebruikte opslag|No Dimensions|
|active_connections|Active Connections|Aantal|Average|Active Connections|No Dimensions|
|network_bytes_egress|Netwerk uit|Bytes|Totaal|Network Out across active connections|No Dimensions|
|network_bytes_ingress|Netwerk in|Bytes|Totaal|Network In across active connections|No Dimensions|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Telemetry message send attempts|Aantal|Totaal|Number of device-to-cloud telemetry messages attempted to be sent to your IoT hub|No Dimensions|
|d2c.telemetry.ingress.success|Telemetry messages sent|Aantal|Totaal|Number of device-to-cloud telemetry messages sent successfully to your IoT hub|No Dimensions|
|c2d.commands.egress.complete.success|Commands completed|Aantal|Totaal|Number of cloud-to-device commands completed successfully by the device|No Dimensions|
|c2d.commands.egress.abandon.success|Commands abandoned|Aantal|Totaal|Number of cloud-to-device commands abandoned by the device|No Dimensions|
|c2d.commands.egress.reject.success|Commands rejected|Aantal|Totaal|Number of cloud-to-device commands rejected by the device|No Dimensions|
|devices.totalDevices|Total devices (deprecated)|Aantal|Totaal|Number of devices registered to your IoT hub|No Dimensions|
|devices.connectedDevices.allProtocol|Connected devices (deprecated) |Aantal|Totaal|Number of devices connected to your IoT hub|No Dimensions|
|d2c.telemetry.egress.success|Routing: telemetry messages delivered|Aantal|Totaal|The number of times messages were successfully delivered to all endpoints using IoT Hub routing. If a message is routed to multiple endpoints, this value increases by one for each successful delivery. If a message is delivered to the same endpoint multiple times, this value increases by one for each successful delivery.|No Dimensions|
|d2c.telemetry.egress.dropped|Routing: telemetry messages dropped |Aantal|Totaal|The number of times messages were dropped by IoT Hub routing due to dead endpoints. This value does not count messages delivered to fallback route as dropped messages are not delivered there.|No Dimensions|
|d2c.telemetry.egress.orphaned|Routing: telemetry messages orphaned |Aantal|Totaal|The number of times messages were orphaned by IoT Hub routing because they didn't match any routing rules (including the fallback rule). |No Dimensions|
|d2c.telemetry.egress.invalid|Routing: telemetry messages incompatible|Aantal|Totaal|The number of times IoT Hub routing failed to deliver messages due to an incompatibility with the endpoint. This value does not include retries.|No Dimensions|
|d2c.telemetry.egress.fallback|Routing: messages delivered to fallback|Aantal|Totaal|The number of times IoT Hub routing delivered messages to the endpoint associated with the fallback route.|No Dimensions|
|d2c.endpoints.egress.eventHubs|Routing: messages delivered to Event Hub|Aantal|Totaal|The number of times IoT Hub routing successfully delivered messages to Event Hub endpoints.|No Dimensions|
|d2c.endpoints.latency.eventHubs|Routing: message latency for Event Hub|Milliseconds|Average|The average latency (milliseconds) between message ingress to IoT Hub and message ingress into an Event Hub endpoint.|No Dimensions|
|d2c.endpoints.egress.serviceBusQueues|Routing: messages delivered to Service Bus Queue|Aantal|Totaal|The number of times IoT Hub routing successfully delivered messages to Service Bus queue endpoints.|No Dimensions|
|d2c.endpoints.latency.serviceBusQueues|Routing: message latency for Service Bus Queue|Milliseconds|Average|The average latency (milliseconds) between message ingress to IoT Hub and telemetry message ingress into a Service Bus queue endpoint.|No Dimensions|
|d2c.endpoints.egress.serviceBusTopics|Routing: messages delivered to Service Bus Topic|Aantal|Totaal|The number of times IoT Hub routing successfully delivered messages to Service Bus topic endpoints.|No Dimensions|
|d2c.endpoints.latency.serviceBusTopics|Routing: message latency for Service Bus Topic|Milliseconds|Average|The average latency (milliseconds) between message ingress to IoT Hub and telemetry message ingress into a Service Bus topic endpoint.|No Dimensions|
|d2c.endpoints.egress.builtIn.events|Routing: messages delivered to messages/events|Aantal|Totaal|The number of times IoT Hub routing successfully delivered messages to the built-in endpoint (messages/events). This metric only starts working when routing is enabled (https://aka.ms/iotrouting) for the IoT hub.|No Dimensions|
|d2c.endpoints.latency.builtIn.events|Routing: message latency for messages/events|Milliseconds|Average|The average latency (milliseconds) between message ingress to IoT Hub and telemetry message ingress into the built-in endpoint (messages/events). This metric only starts working when routing is enabled (https://aka.ms/iotrouting) for the IoT hub.|No Dimensions|
|d2c.endpoints.egress.storage|Routing: messages delivered to storage|Aantal|Totaal|The number of times IoT Hub routing successfully delivered messages to storage endpoints.|No Dimensions|
|d2c.endpoints.latency.storage|Routing: message latency for storage|Milliseconds|Average|The average latency (milliseconds) between message ingress to IoT Hub and telemetry message ingress into a storage endpoint.|No Dimensions|
|d2c.endpoints.egress.storage.bytes|Routing: data delivered to storage|Bytes|Totaal|The amount of data (bytes) IoT Hub routing delivered to storage endpoints.|No Dimensions|
|d2c.endpoints.egress.storage.blobs|Routing: blobs delivered to storage|Aantal|Totaal|The number of times IoT Hub routing delivered blobs to storage endpoints.|No Dimensions|
|EventGridDeliveries|Event Grid deliveries (preview)|Aantal|Totaal|The number of IoT Hub events published to Event Grid. Use the Result dimension for the number of successful and failed requests. EventType dimension shows the type of event (https://aka.ms/ioteventgrid).|Result, EventType|
|EventGridLatency|The average latency (milliseconds) from when the Iot Hub event was generated to when the event was published to Event Grid. This number is an average between all event types. Use the EventType dimension to see latency of a specific type of event.|EventType|
|d2c.twin.read.success|Successful twin reads from devices|Aantal|Totaal|The count of all successful device-initiated twin reads.|No Dimensions|
|d2c.twin.read.failure|Failed twin reads from devices|Aantal|Totaal|The count of all failed device-initiated twin reads.|No Dimensions|
|d2c.twin.read.size|Response size of twin reads from devices|Bytes|Average|The average, min, and max of all successful device-initiated twin reads.|No Dimensions|
|d2c.twin.update.success|Successful twin updates from devices|Aantal|Totaal|The count of all successful device-initiated twin updates.|No Dimensions|
|d2c.twin.update.failure|Failed twin updates from devices|Aantal|Totaal|The count of all failed device-initiated twin updates.|No Dimensions|
|d2c.twin.update.size|Size of twin updates from devices|Bytes|Average|The average, min, and max size of all successful device-initiated twin updates.|No Dimensions|
|c2d.methods.success|Successful direct method invocations|Aantal|Totaal|The count of all successful direct method calls.|No Dimensions|
|c2d.methods.failure|Failed direct method invocations|Aantal|Totaal|The count of all failed direct method calls.|No Dimensions|
|c2d.methods.requestSize|Request size of direct method invocations|Bytes|Average|The average, min, and max of all successful direct method requests.|No Dimensions|
|c2d.methods.responseSize|Response size of direct method invocations|Bytes|Average|The average, min, and max of all successful direct method responses.|No Dimensions|
|c2d.twin.read.success|Successful twin reads from back end|Aantal|Totaal|The count of all successful back-end-initiated twin reads.|No Dimensions|
|c2d.twin.read.failure|Failed twin reads from back end|Aantal|Totaal|The count of all failed back-end-initiated twin reads.|No Dimensions|
|c2d.twin.read.size|Response size of twin reads from back end|Bytes|Average|The average, min, and max of all successful back-end-initiated twin reads.|No Dimensions|
|c2d.twin.update.success|Successful twin updates from back end|Aantal|Totaal|The count of all successful back-end-initiated twin updates.|No Dimensions|
|c2d.twin.update.failure|Failed twin updates from back end|Aantal|Totaal|The count of all failed back-end-initiated twin updates.|No Dimensions|
|c2d.twin.update.size|Size of twin updates from back end|Bytes|Average|The average, min, and max size of all successful back-end-initiated twin updates.|No Dimensions|
|twinQueries.success|Successful twin queries|Aantal|Totaal|The count of all successful twin queries.|No Dimensions|
|twinQueries.failure|Failed twin queries|Aantal|Totaal|The count of all failed twin queries.|No Dimensions|
|twinQueries.resultSize|Twin queries result size|Bytes|Average|The average, min, and max of the result size of all successful twin queries.|No Dimensions|
|jobs.createTwinUpdateJob.success|Successful creations of twin update jobs|Aantal|Totaal|The count of all successful creation of twin update jobs.|No Dimensions|
|jobs.createTwinUpdateJob.failure|Failed creations of twin update jobs|Aantal|Totaal|The count of all failed creation of twin update jobs.|No Dimensions|
|jobs.createDirectMethodJob.success|Successful creations of method invocation jobs|Aantal|Totaal|The count of all successful creation of direct method invocation jobs.|No Dimensions|
|jobs.createDirectMethodJob.failure|Failed creations of method invocation jobs|Aantal|Totaal|The count of all failed creation of direct method invocation jobs.|No Dimensions|
|jobs.listJobs.success|Successful calls to list jobs|Aantal|Totaal|The count of all successful calls to list jobs.|No Dimensions|
|jobs.listJobs.failure|Failed calls to list jobs|Aantal|Totaal|The count of all failed calls to list jobs.|No Dimensions|
|jobs.cancelJob.success|Successful job cancellations|Aantal|Totaal|The count of all successful calls to cancel a job.|No Dimensions|
|jobs.cancelJob.failure|Failed job cancellations|Aantal|Totaal|The count of all failed calls to cancel a job.|No Dimensions|
|jobs.queryJobs.success|Successful job queries|Aantal|Totaal|The count of all successful calls to query jobs.|No Dimensions|
|jobs.queryJobs.failure|Failed job queries|Aantal|Totaal|The count of all failed calls to query jobs.|No Dimensions|
|jobs.completed|Completed jobs|Aantal|Totaal|The count of all completed jobs.|No Dimensions|
|jobs.failed|Failed jobs|Aantal|Totaal|The count of all failed jobs.|No Dimensions|
|d2c.telemetry.ingress.sendThrottle|Number of throttling errors|Aantal|Totaal|Number of throttling errors due to device throughput throttles|No Dimensions|
|dailyMessageQuotaUsed|Total number of messages used|Aantal|Average|Number of total messages used today. This is a cumulative value that is reset to zero at 00:00 UTC every day.|No Dimensions|
|deviceDataUsage|Total device data usage|Bytes|Totaal|Bytes transferred to and from any devices connected to IotHub|No Dimensions|
|totalDeviceCount|Total devices (preview)|Aantal|Average|Number of devices registered to your IoT hub|No Dimensions|
|connectedDeviceCount|Connected devices (preview)|Aantal|Average|Number of devices connected to your IoT hub|No Dimensions|
|configurations|Configuration Metrics|Aantal|Totaal|Metrics for Configuration Operations|No Dimensions|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|RegistrationAttempts|Registration attempts|Aantal|Totaal|Number of device registrations attempted|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Devices assigned|Aantal|Totaal|Number of devices assigned to an IoT hub|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Attestation attempts|Aantal|Totaal|Number of device attestations attempted|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AvailableStorage|Available Storage|Bytes|Totaal|Total available storage reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|CassandraConnectionClosures|Cassandra Connection Closures|Aantal|Totaal|Number of Cassandra connections that were closed, reported at a 1 minute granularity|Region, ClosureReason|
|CassandraRequestCharges|Cassandra Request Charges|Aantal|Totaal|RUs consumed for Cassandra requests made|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|Cassandra Requests|Aantal|Aantal|Number of Cassandra requests made|DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|DataUsage|Gegevensgebruik|Bytes|Totaal|Total data usage reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|DocumentCount|Document Count|Aantal|Totaal|Total document count reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|DocumentQuota|Document Quota|Bytes|Totaal|Total storage quota reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|IndexUsage|Index Usage|Bytes|Totaal|Total index usage reported at 5 minutes granularity|CollectionName, DatabaseName, Region|
|MetadataRequests|Metadata Requests|Aantal|Aantal|Count of metadata requests. Cosmos DB maintains system metadata collection for each account, that allows you to enumerate collections, databases, etc., and their configurations, free of charge.|DatabaseName, CollectionName, Region, StatusCode, |
|MongoRequestCharge|Mongo Request Charge|Aantal|Totaal|Mongo Request Units Consumed|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|MongoRequests|Mongo Requests|Aantal|Aantal|Number of Mongo Requests Made|DatabaseName, CollectionName, Region, CommandName, ErrorCode|
|ProvisionedThroughput|Ingerichte doorvoer|Aantal|Maximum|Ingerichte doorvoer|DatabaseName, CollectionName|
|ReplicationLatency|P99 Replication Latency|MilliSeconds|Average|P99 Replication Latency across source and target regions for geo-enabled account|SourceRegion, TargetRegion|
|ServiceAvailability|Service Availability|Procent|Average|Account requests availability at one hour, day or month granularity|No Dimensions|
|TotalRequestUnits|Total Request Units|Aantal|Totaal|Request Units consumed|DatabaseName, CollectionName, Region, StatusCode, OperationType|
|TotalRequests|Totaal aantal aanvragen|Aantal|Aantal|Number of requests made|DatabaseName, CollectionName, Region, StatusCode, OperationType|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events|Aantal|Totaal|Total events published to this topic|No Dimensions|
|PublishFailCount|Publish Failed Events|Aantal|Totaal|Total events failed to publish to this topic|ErrorType, Error|
|UnmatchedEventCount|Unmatched Events|Aantal|Totaal|Total events not matching any of the event subscriptions for this topic|No Dimensions|
|PublishSuccessLatencyInMs|Publish Success Latency|Aantal|Totaal|Publish success latency in milliseconds|No Dimensions|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|MatchedEventCount|Matched Events|Aantal|Totaal|Total events matched to this event subscription|No Dimensions|
|DeliveryAttemptFailCount|Delivery Failed Events|Aantal|Totaal|Total events failed to deliver to this event subscription|Error, ErrorType|
|DeliverySuccessCount|Delivered Events|Aantal|Totaal|Total events delivered to this event subscription|No Dimensions|
|DestinationProcessingDurationInMs|Destination Processing Duration|Milliseconds|Average|Destination processing duration in milliseconds|No Dimensions|
|DroppedEventCount|Dropped Events|Aantal|Totaal|Total dropped events matching to this event subscription|DropReason|
|DeadLetteredCount|Dead Lettered Events|Aantal|Totaal|Total dead lettered events matching to this event subscription|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PublishSuccessCount|Published Events|Aantal|Totaal|Total events published to this topic|No Dimensions|
|PublishFailCount|Failed Events|Aantal|Totaal|Total events failed to publish to this topic|ErrorType, Error|
|UnmatchedEventCount|Unmatched Events|Aantal|Totaal|Total events not matching any of the event subscriptions for this topic|No Dimensions|
|PublishSuccessLatencyInMs|Publish Success Latency|Aantal|Totaal|Publish success latency in milliseconds|No Dimensions|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SuccessfulRequests|Successful Requests|Aantal|Totaal|Successful Requests for Microsoft.EventHub.|EntityName, |
|ServerErrors|Server Errors.|Aantal|Totaal|Server Errors for Microsoft.EventHub.|EntityName, |
|UserErrors|User Errors.|Aantal|Totaal|User Errors for Microsoft.EventHub.|EntityName, |
|QuotaExceededErrors|Quota Exceeded Errors.|Aantal|Totaal|Quota Exceeded Errors for Microsoft.EventHub.|EntityName, |
|ThrottledRequests|Throttled Requests.|Aantal|Totaal|Throttled Requests for Microsoft.EventHub.|EntityName, |
|IncomingRequests|Incoming Requests|Aantal|Totaal|Incoming Requests for Microsoft.EventHub.|EntityName|
|IncomingMessages|Incoming Messages|Aantal|Totaal|Incoming Messages for Microsoft.EventHub.|EntityName|
|OutgoingMessages|Outgoing Messages|Aantal|Totaal|Outgoing Messages for Microsoft.EventHub.|EntityName|
|IncomingBytes|Incoming Bytes.|Bytes|Totaal|Incoming Bytes for Microsoft.EventHub.|EntityName|
|OutgoingBytes|Outgoing Bytes.|Bytes|Totaal|Outgoing Bytes for Microsoft.EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Aantal|Average|Total Active Connections for Microsoft.EventHub.|No Dimensions|
|ConnectionsOpened|Connections Opened.|Aantal|Average|Connections Opened for Microsoft.EventHub.|EntityName|
|ConnectionsClosed|Connections Closed.|Aantal|Average|Connections Closed for Microsoft.EventHub.|EntityName|
|CaptureBacklog|Capture Backlog.|Aantal|Totaal|Capture Backlog for Microsoft.EventHub.|EntityName|
|CapturedMessages|Captured Messages.|Aantal|Totaal|Captured Messages for Microsoft.EventHub.|EntityName|
|CapturedBytes|Captured Bytes.|Bytes|Totaal|Captured Bytes for Microsoft.EventHub.|EntityName|
|Grootte|Grootte|Bytes|Average|Size of an EventHub in Bytes.|EntityName|
|INREQS|Incoming Requests (Deprecated)|Aantal|Totaal|Total incoming send requests for a namespace (Deprecated)|No Dimensions|
|SUCCREQ|Successful Requests (Deprecated)|Aantal|Totaal|Total successful requests for a namespace (Deprecated)|No Dimensions|
|FAILREQ|Failed Requests (Deprecated)|Aantal|Totaal|Total failed requests for a namespace (Deprecated)|No Dimensions|
|SVRBSY|Server Busy Errors (Deprecated)|Aantal|Totaal|Total server busy errors for a namespace (Deprecated)|No Dimensions|
|INTERR|Internal Server Errors (Deprecated)|Aantal|Totaal|Total internal server errors for a namespace (Deprecated)|No Dimensions|
|MISCERR|Other Errors (Deprecated)|Aantal|Totaal|Total failed requests for a namespace (Deprecated)|No Dimensions|
|INMSGS|Incoming Messages (Deprecated) (Deprecated)|Aantal|Totaal|Total incoming messages for a namespace. This metric is deprecated. Please use Incoming Messages metric instead (Deprecated)|No Dimensions|
|EHINMSGS|Incoming Messages (Deprecated)|Aantal|Totaal|Total incoming messages for a namespace (Deprecated)|No Dimensions|
|OUTMSGS|Outgoing Messages (Deprecated) (Deprecated)|Aantal|Totaal|Total outgoing messages for a namespace. This metric is deprecated. Please use Outgoing Messages metric instead (Deprecated)|No Dimensions|
|EHOUTMSGS|Outgoing Messages (Deprecated)|Aantal|Totaal|Total outgoing messages for a namespace (Deprecated)|No Dimensions|
|EHINMBS|Incoming bytes (Deprecated) (Deprecated)|Bytes|Totaal|Event Hub incoming message throughput for a namespace. This metric is deprecated. Please use Incoming bytes metric instead (Deprecated)|No Dimensions|
|EHINBYTES|Incoming bytes (Deprecated)|Bytes|Totaal|Event Hub incoming message throughput for a namespace (Deprecated)|No Dimensions|
|EHOUTMBS|Outgoing bytes (Deprecated) (Deprecated)|Bytes|Totaal|Event Hub outgoing message throughput for a namespace. This metric is deprecated. Please use Outgoing bytes metric instead (Deprecated)|No Dimensions|
|EHOUTBYTES|Outgoing bytes (Deprecated)|Bytes|Totaal|Event Hub outgoing message throughput for a namespace (Deprecated)|No Dimensions|
|EHABL|Archive backlog messages (Deprecated)|Aantal|Totaal|Event Hub archive messages in backlog for a namespace (Deprecated)|No Dimensions|
|EHAMSGS|Archive messages (Deprecated)|Aantal|Totaal|Event Hub archived messages in a namespace (Deprecated)|No Dimensions|
|EHAMBS|Archive message throughput (Deprecated)|Bytes|Totaal|Event Hub archived message throughput in a namespace (Deprecated)|No Dimensions|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SuccessfulRequests|Successful Requests (Preview)|Aantal|Totaal|Successful Requests for Microsoft.EventHub. (Preview)|No Dimensions|
|ServerErrors|Server Errors. (Preview)|Aantal|Totaal|Server Errors for Microsoft.EventHub. (Preview)|No Dimensions|
|UserErrors|User Errors. (Preview)|Aantal|Totaal|User Errors for Microsoft.EventHub. (Preview)|No Dimensions|
|QuotaExceededErrors|Quota Exceeded Errors. (Preview)|Aantal|Totaal|Quota Exceeded Errors for Microsoft.EventHub. (Preview)|No Dimensions|
|ThrottledRequests|Throttled Requests. (Preview)|Aantal|Totaal|Throttled Requests for Microsoft.EventHub. (Preview)|No Dimensions|
|IncomingRequests|Incoming Requests (Preview)|Aantal|Totaal|Incoming Requests for Microsoft.EventHub. (Preview)|No Dimensions|
|IncomingMessages|Incoming Messages (Preview)|Aantal|Totaal|Incoming Messages for Microsoft.EventHub. (Preview)|No Dimensions|
|OutgoingMessages|Outgoing Messages (Preview)|Aantal|Totaal|Outgoing Messages for Microsoft.EventHub. (Preview)|No Dimensions|
|IncomingBytes|Incoming Bytes. (Preview)|Bytes|Totaal|Incoming Bytes for Microsoft.EventHub. (Preview)|No Dimensions|
|OutgoingBytes|Outgoing Bytes. (Preview)|Bytes|Totaal|Outgoing Bytes for Microsoft.EventHub. (Preview)|No Dimensions|
|ActiveConnections|ActiveConnections (Preview)|Aantal|Average|Total Active Connections for Microsoft.EventHub. (Preview)|No Dimensions|
|ConnectionsOpened|Connections Opened. (Preview)|Aantal|Average|Connections Opened for Microsoft.EventHub. (Preview)|No Dimensions|
|ConnectionsClosed|Connections Closed. (Preview)|Aantal|Average|Connections Closed for Microsoft.EventHub. (Preview)|No Dimensions|
|CaptureBacklog|Capture Backlog. (Preview)|Aantal|Totaal|Capture Backlog for Microsoft.EventHub. (Preview)|No Dimensions|
|CapturedMessages|Captured Messages. (Preview)|Aantal|Totaal|Captured Messages for Microsoft.EventHub. (Preview)|No Dimensions|
|CapturedBytes|Captured Bytes. (Preview)|Bytes|Totaal|Captured Bytes for Microsoft.EventHub. (Preview)|No Dimensions|
|CPU|CPU (Preview)|Procent|Maximum|CPU utilization for the Event Hub Cluster as a percentage|Rol|
|AvailableMemory|Available Memory (Preview)|Aantal|Maximum|Available memory for the Event Hub Cluster in bytes|Rol|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|GatewayRequests|Gateway Requests|Aantal|Totaal|Number of gateway requests|ClusterDnsName, HttpStatus|
|CategorizedGatewayRequests|Categorized Gateway Requests|Aantal|Totaal|Number of gateway requests by categories (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HttpStatus|
|NumActiveWorkers|Number of Active Workers|Aantal|Maximum|Number of Active Workers|ClusterDnsName, MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ObservedMetricValue|Observed Metric Value|Aantal|Average|The value computed by autoscale when executed|MetricTriggerSource|
|MetricThreshold|Metric Threshold|Aantal|Average|The configured autoscale threshold when autoscale ran.|MetricTriggerRule|
|ObservedCapacity|Observed Capacity|Aantal|Average|The capacity reported to autoscale when it executed.|No Dimensions|
|ScaleActionsInitiated|Scale Actions Initiated|Aantal|Totaal|The direction of the scale operation.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

(Public Preview)

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Beschikbaarheid|Procent|Average|Percentage of successfully completed availability tests|availabilityResult/name, availabilityResult/location|
|availabilityResults/count|Beschikbaarheidstests|Aantal|Aantal|Count of availability tests|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|availabilityResults/duration|Availability test duration|MilliSeconds|Average|Availability test duration|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|browserTimings/networkDuration|Page load network connect time|MilliSeconds|Average|Time between user request and network connection. Includes DNS lookup and transport connection.|No Dimensions|
|browserTimings/processingDuration|Client processing time|MilliSeconds|Average|Time between receiving the last byte of a document until the DOM is loaded. Async requests may still be processing.|No Dimensions|
|browserTimings/receiveDuration|Receiving response time|MilliSeconds|Average|Time between the first and last bytes, or until disconnection.|No Dimensions|
|browserTimings/sendDuration|Send request time|MilliSeconds|Average|Time between network connection and receiving the first byte.|No Dimensions|
|browserTimings/totalDuration|Browser page load time|MilliSeconds|Average|Time from user request until DOM, stylesheets, scripts and images are loaded.|No Dimensions|
|dependencies/count|Dependency calls|Aantal|Aantal|Count of calls made by the application to external resources.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/duration|Dependency duration|MilliSeconds|Average|Duration of calls made by the application to external resources.|dependency/type, dependency/performanceBucket, dependency/success, operation/synthetic, cloud/roleInstance, cloud/roleName|
|dependencies/failed|Dependency call failures|Aantal|Aantal|Count of failed dependency calls made by the application to external resources.|dependency/type, dependency/performanceBucket, operation/synthetic, cloud/roleInstance, cloud/roleName|
|pageViews/count|Page views|Aantal|Aantal|Count of page views.|operation/synthetic|
|pageViews/duration|Page view load time|MilliSeconds|Average|Page view load time|operation/synthetic|
|performanceCounters/requestExecutionTime|HTTP request execution time|MilliSeconds|Average|Execution time of the most recent request.|cloud/roleInstance|
|performanceCounters/requestsInQueue|HTTP requests in application queue|Aantal|Average|Length of the application request queue.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|HTTP request rate|CountPerSecond|Average|Rate of all requests to the application per second from ASP.NET.|cloud/roleInstance|
|performanceCounters/exceptionsPerSecond|Exception rate|CountPerSecond|Average|Count of handled and unhandled exceptions reported to windows, including .NET exceptions and unmanaged exceptions that are converted into .NET exceptions.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Process IO rate|BytesPerSecond|Average|Total bytes per second read and written to files, network and devices.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Process CPU|Procent|Average|The percentage of elapsed time that all process threads used the processor to execute instructions. This can vary between 0 to 100. This metric indicates the performance of w3wp process alone.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Processor time|Procent|Average|The percentage of time that the processor spends in non-idle threads.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Available memory|Bytes|Average|Physical memory immediately available for allocation to a process or for system use.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Process private bytes|Bytes|Average|Memory exclusively assigned to the monitored application's processes.|cloud/roleInstance|
|requests/duration|Server response time|MilliSeconds|Average|Time between receiving an HTTP request and finishing sending the response.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/count|Server requests|Aantal|Aantal|Count of HTTP requests completed.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|requests/failed|Mislukte aanvragen|Aantal|Aantal|Count of HTTP requests marked as failed. In most cases these are requests with a response code >= 400 and not equal to 401.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|requests/rate|Server request rate|CountPerSecond|Average|Rate of server requests per second|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|exceptions/count|Uitzonderingen|Aantal|Aantal|Combined count of all uncaught exceptions.|cloud/roleName, cloud/roleInstance, client/type|
|exceptions/browser|Browseruitzonderingen|Aantal|Aantal|Count of uncaught exceptions thrown in the browser.|No Dimensions|
|exceptions/server|Server exceptions|Aantal|Aantal|Count of uncaught exceptions thrown in the server application.|cloud/roleName, cloud/roleInstance|
|traces/count|Traces|Aantal|Aantal|Trace document count|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ServiceApiHit|Total Service Api Hits|Aantal|Aantal|Number of total service api hits|ActivityType, ActivityName|
|ServiceApiLatency|Overall Service Api Latency|Milliseconds|Average|Overall latency of service api requests|ActivityType, ActivityName, StatusCode|
|ServiceApiResult|Total Service Api Results|Aantal|Aantal|Number of total service api results|ActivityType, ActivityName, StatusCode|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CacheUtilization|Cache Utilization|Procent|Average|Utilization level in the cluster scope|Geen|
|QueryDuration|Query Duration|Milliseconds|Average|Queries’ duration in seconds|Query Status|
|IngestionUtilization|Ingestion Utilization|Procent|Average|Ratio of used ingestion slots in the cluster|Geen|
|KeepAlive|Keep Alive|Aantal|Average|Sanity check indicates the cluster responds to queries|Geen|
|IngestionVolumeInMB|Ingestion Volume (In MB)|Aantal|Totaal|Overall volume of ingested data to the cluster (in MB)|Database|
|IngestionLatencyInSeconds|Ingestion Latency (In seconds)|Seconden|Average|Ingestion time from the source (e.g. message is in EventHub) to the cluster in seconds|Geen|
|EventProcessedForEventHubs|Events Processed (for Event Hubs)|Aantal|Totaal|Number of events processed by the cluster when ingesting from Event Hub|Geen|
|IngestionResult|Ingestion Result|Aantal|Aantal|Number of ingestion operations|Status|
|CPU|CPU|Procent|Average|CPU utilization level|Geen|
| ContinuousExportNumOfRecordsExported | Number of records exported in continuous export | Aantal | Totaal | Number of records exported for every storage artifact written during the export operation  | Geen |
| ExportUtilization | Export Utilization | Procent | Maximum | Export utilization | Geen |
| ContinuousExportPendingCount | Continuous Export Pending Count | Aantal | Maximum | The number of pending continuous export jobs ready for execution | Geen |
| ContinuousExportMaxLatenessMinutes | Continuous Export Max Lateness Minutes | Aantal | Maximum | The max time in minutes of all continuous exports which are pending and ready for execution | Geen |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Gebruik|Gebruik|Aantal|Aantal|Count of API calls|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|RunsStarted|Runs Started|Aantal|Totaal|Number of workflow runs started.|No Dimensions|
|RunsCompleted|Runs Completed|Aantal|Totaal|Number of workflow runs completed.|No Dimensions|
|RunsSucceeded|Runs Succeeded|Aantal|Totaal|Number of workflow runs succeeded.|No Dimensions|
|RunsFailed|Runs Failed|Aantal|Totaal|Number of workflow runs failed.|No Dimensions|
|RunsCancelled|Runs Canceled|Aantal|Totaal|Number of workflow runs canceled.|No Dimensions|
|RunLatency|Run Latency|Seconden|Average|Latency of completed workflow runs.|No Dimensions|
|RunSuccessLatency|Run Success Latency|Seconden|Average|Latency of succeeded workflow runs.|No Dimensions|
|RunThrottledEvents|Run Throttled Events|Aantal|Totaal|Number of workflow action or trigger throttled events.|No Dimensions|
|RunFailurePercentage|Run Failure Percentage|Procent|Totaal|Percentage of workflow runs failed.|No Dimensions|
|ActionsStarted|Actions Started |Aantal|Totaal|Number of workflow actions started.|No Dimensions|
|ActionsCompleted|Actions Completed |Aantal|Totaal|Number of workflow actions completed.|No Dimensions|
|ActionsSucceeded|Actions Succeeded |Aantal|Totaal|Number of workflow actions succeeded.|No Dimensions|
|ActionsFailed|Actions Failed|Aantal|Totaal|Number of workflow actions failed.|No Dimensions|
|ActionsSkipped|Actions Skipped |Aantal|Totaal|Number of workflow actions skipped.|No Dimensions|
|ActionLatency|Action Latency |Seconden|Average|Latency of completed workflow actions.|No Dimensions|
|ActionSuccessLatency|Action Success Latency |Seconden|Average|Latency of succeeded workflow actions.|No Dimensions|
|ActionThrottledEvents|Action Throttled Events|Aantal|Totaal|Number of workflow action throttled events..|No Dimensions|
|TriggersStarted|Triggers Started |Aantal|Totaal|Number of workflow triggers started.|No Dimensions|
|TriggersCompleted|Triggers Completed |Aantal|Totaal|Number of workflow triggers completed.|No Dimensions|
|TriggersSucceeded|Triggers Succeeded |Aantal|Totaal|Number of workflow triggers succeeded.|No Dimensions|
|TriggersFailed|Triggers Failed |Aantal|Totaal|Number of workflow triggers failed.|No Dimensions|
|TriggersSkipped|Triggers Skipped|Aantal|Totaal|Number of workflow triggers skipped.|No Dimensions|
|TriggersFired|Triggers Fired |Aantal|Totaal|Number of workflow triggers fired.|No Dimensions|
|TriggerLatency|Trigger Latency |Seconden|Average|Latency of completed workflow triggers.|No Dimensions|
|TriggerFireLatency|Trigger Fire Latency |Seconden|Average|Latency of fired workflow triggers.|No Dimensions|
|TriggerSuccessLatency|Trigger Success Latency |Seconden|Average|Latency of succeeded workflow triggers.|No Dimensions|
|TriggerThrottledEvents|Trigger Throttled Events|Aantal|Totaal|Number of workflow trigger throttled events.|No Dimensions|
|BillableActionExecutions|Billable Action Executions|Aantal|Totaal|Number of workflow action executions getting billed.|No Dimensions|
|BillableTriggerExecutions|Billable Trigger Executions|Aantal|Totaal|Number of workflow trigger executions getting billed.|No Dimensions|
|TotalBillableExecutions|Total Billable Executions|Aantal|Totaal|Number of workflow executions getting billed.|No Dimensions|
|BillingUsageNativeOperation|Billing Usage for Native Operation Executions|Aantal|Totaal|Number of native operation executions getting billed.|No Dimensions|
|BillingUsageStandardConnector|Billing Usage for Standard Connector Executions|Aantal|Totaal|Number of standard connector executions getting billed.|No Dimensions|
|BillingUsageStorageConsumption|Billing Usage for Storage Consumption Executions|Aantal|Totaal|Number of storage consumption executions getting billed.|No Dimensions|
|BillingUsageNativeOperation|Billing Usage for Native Operation Executions|Aantal|Totaal|Number of native operation executions getting billed.|No Dimensions|
|BillingUsageStandardConnector|Billing Usage for Standard Connector Executions|Aantal|Totaal|Number of standard connector executions getting billed.|No Dimensions|
|BillingUsageStorageConsumption|Billing Usage for Storage Consumption Executions|Aantal|Totaal|Number of storage consumption executions getting billed.|No Dimensions|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|RunsStarted|Runs Started|Aantal|Totaal|Number of workflow runs started.|No Dimensions|
|RunsCompleted|Runs Completed|Aantal|Totaal|Number of workflow runs completed.|No Dimensions|
|RunsSucceeded|Runs Succeeded|Aantal|Totaal|Number of workflow runs succeeded.|No Dimensions|
|RunsFailed|Runs Failed|Aantal|Totaal|Number of workflow runs failed.|No Dimensions|
|RunsCancelled|Runs Canceled|Aantal|Totaal|Number of workflow runs canceled.|No Dimensions|
|RunLatency|Run Latency|Seconden|Average|Latency of completed workflow runs.|No Dimensions|
|RunSuccessLatency|Run Success Latency|Seconden|Average|Latency of succeeded workflow runs.|No Dimensions|
|RunThrottledEvents|Run Throttled Events|Aantal|Totaal|Number of workflow action or trigger throttled events.|No Dimensions|
|RunStartThrottledEvents|Run Start Throttled Events|Aantal|Totaal|Number of workflow run start throttled events.|No Dimensions|
|RunFailurePercentage|Run Failure Percentage|Procent|Totaal|Percentage of workflow runs failed.|No Dimensions|
|ActionsStarted|Actions Started |Aantal|Totaal|Number of workflow actions started.|No Dimensions|
|ActionsCompleted|Actions Completed |Aantal|Totaal|Number of workflow actions completed.|No Dimensions|
|ActionsSucceeded|Actions Succeeded |Aantal|Totaal|Number of workflow actions succeeded.|No Dimensions|
|ActionsFailed|Actions Failed |Aantal|Totaal|Number of workflow actions failed.|No Dimensions|
|ActionsSkipped|Actions Skipped |Aantal|Totaal|Number of workflow actions skipped.|No Dimensions|
|ActionLatency|Action Latency |Seconden|Average|Latency of completed workflow actions.|No Dimensions|
|ActionSuccessLatency|Action Success Latency |Seconden|Average|Latency of succeeded workflow actions.|No Dimensions|
|ActionThrottledEvents|Action Throttled Events|Aantal|Totaal|Number of workflow action throttled events..|No Dimensions|
|TriggersStarted|Triggers Started |Aantal|Totaal|Number of workflow triggers started.|No Dimensions|
|TriggersCompleted|Triggers Completed |Aantal|Totaal|Number of workflow triggers completed.|No Dimensions|
|TriggersSucceeded|Triggers Succeeded |Aantal|Totaal|Number of workflow triggers succeeded.|No Dimensions|
|TriggersFailed|Triggers Failed |Aantal|Totaal|Number of workflow triggers failed.|No Dimensions|
|TriggersSkipped|Triggers Skipped|Aantal|Totaal|Number of workflow triggers skipped.|No Dimensions|
|TriggersFired|Triggers Fired |Aantal|Totaal|Number of workflow triggers fired.|No Dimensions|
|TriggerLatency|Trigger Latency |Seconden|Average|Latency of completed workflow triggers.|No Dimensions|
|TriggerFireLatency|Trigger Fire Latency |Seconden|Average|Latency of fired workflow triggers.|No Dimensions|
|TriggerSuccessLatency|Trigger Success Latency |Seconden|Average|Latency of succeeded workflow triggers.|No Dimensions|
|TriggerThrottledEvents|Trigger Throttled Events|Aantal|Totaal|Number of workflow trigger throttled events.|No Dimensions|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Workflow Processor Usage for Integration Service Environment|Procent|Average|Workflow processor usage for integration service environment.|No Dimensions|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Workflow Memory Usage for Integration Service Environment|Procent|Average|Workflow memory usage for integration service environment.|No Dimensions|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Connector Processor Usage for Integration Service Environment|Procent|Average|Connector processor usage for integration service environment.|No Dimensions|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Connector Memory Usage for Integration Service Environment|Procent|Average|Connector memory usage for integration service environment.|No Dimensions|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Completed Runs|Completed Runs|Aantal|Totaal|Number of runs completed successfully for this workspace|Scenario|
|Started Runs|Started Runs|Aantal|Totaal|Number of runs started for this workspace|Scenario|
|Failed Runs|Failed Runs|Aantal|Totaal|Number of runs failed for this workspace|Scenario|

## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Gebruik|Gebruik|Aantal|Aantal|Count of API calls|ApiCategory, ApiName, ResultType, ResponseCode|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|Availability of the APIs|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AverageOtherLatency|Average other latency|ms/op|Average|Average other latency (that is not read or write) in milliseconds per operation|No Dimensions|
|AverageReadLatency|Average read latency|ms/op|Average|Average read latency in milliseconds per operation|No Dimensions|
|AverageTotalLatency|Average total latency|ms/op|Average|Average total latency in milliseconds per operation|No Dimensions|
|AverageWriteLatency|Average write latency|ms/op|Average|Average write latency in milliseconds per operation|No Dimensions|
|FilesystemOtherOps|Filesystem other ops|ops|Average|Number of filesystem other operations (that is not read or write)|No Dimensions|
|FilesystemReadOps|Filesystem read ops|ops|Average|Number of filesystem read operations|No Dimensions|
|FilesystemTotalOps|Filesystem total ops|ops|Average|Sum of all filesystem operations|No Dimensions|
|FilesystemWriteOps|Filesystem write ops|ops|Average|Number of filesystem write operations|No Dimensions|
|IoBytesPerOtherOps|Io bytes per other ops|bytes/op|Average|Number of In/out bytes per other operations (that is not read or write)|No Dimensions|
|IoBytesPerReadOps|Io bytes per read ops|bytes/op|Average|Number of In/out bytes per read operation|No Dimensions|
|IoBytesPerTotalOps|Io bytes per op across all operations|bytes/op|Average|Sum of all In/out bytes operation|No Dimensions|
|IoBytesPerWriteOps|Io bytes per write ops|bytes/op|Average|Number of In/out bytes per write operation|No Dimensions|
|OtherIops|Other iops|operations/second|Average|Other In/out operation per second|No Dimensions|
|OtherThroughput|Other throughput|MBps|Average|Other throughput (that is not read or write) in megabytes per second|No Dimensions|
|ReadIops|Read iops|operations/second|Average|Read In/out operations per second|No Dimensions|
|ReadThroughput|Read throughput|MBps|Average|Read throughput in megabytes per second|No Dimensions|
|TotalIops|Total iops|operations/second|Average|Sum of all In/out operations per second|No Dimensions|
|TotalThroughput|Total throughput|MBps|Average|Sum of all throughput in megabytes per second|No Dimensions|
|VolumeAllocatedSize|Volume allocated size|bytes|Average|Allocated size of the volume (Not the actual used bytes)|No Dimensions|
|VolumeLogicalSize|Volume logical size|bytes|Average|Logical size of the volume (used bytes)|No Dimensions|
|VolumeSnapshotSize|Volume snapshot size|bytes|Average|Size of all snapshots in volume|No Dimensions|
|WriteIops|Write iops|operations/second|Average|Write In/out operations per second|No Dimensions|
|WriteThroughput|Write throughput|MBps|Average|Write throughput in megabytes per second|No Dimensions|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Volume pool allocated size|bytes|Average|Allocated size of the pool (Not the actual used bytes)|No Dimensions|
|VolumePoolAllocatedUsed|Volume pool allocated used|bytes|Average|Allocated used size of the pool|No Dimensions|
|VolumePoolTotalLogicalSize|Volume pool total logical size|bytes|Average|Sum of the logical size of all the volumes belonging to the pool|No Dimensions|
|VolumePoolTotalSnapshotSize|Volume pool total snapshot size|bytes|Average|Sum of all snapshots in pool|No Dimensions|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BytesSentRate|Bytes Sent|Aantal|Totaal|Number of bytes the Network Interface sent|No Dimensions|
|BytesReceivedRate|Bytes Received|Aantal|Totaal|Number of bytes the Network Interface received|No Dimensions|
|PacketsSentRate|Packets Sent|Aantal|Totaal|Number of packets the Network Interface sent|No Dimensions|
|PacketsReceivedRate|Packets Received|Aantal|Totaal|Number of packets the Network Interface received|No Dimensions|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|VipAvailability|Data Path Availability|Aantal|Average|Average Load Balancer data path availability per time duration|FrontendIPAddress, FrontendPort|
|DipAvailability|Health Probe Status|Aantal|Average|Average Load Balancer health probe status per time duration|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Byte Count|Aantal|Totaal|Total number of Bytes transmitted within time period|FrontendIPAddress, FrontendPort, Direction|
|PacketCount|Packet Count|Aantal|Totaal|Total number of Packets transmitted within time period|FrontendIPAddress, FrontendPort, Direction|
|SYNCount|SYN Count|Aantal|Totaal|Total number of SYN Packets transmitted within time period|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|SNAT Connection Count|Aantal|Totaal|Total number of new SNAT connections created within time period|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Allocated SNAT Ports (Preview)|Aantal|Totaal|Total number of SNAT ports allocated within time period|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Used SNAT Ports (Preview)|Aantal|Totaal|Total number of SNAT ports used within time period|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|QueryVolume|Query Volume|Aantal|Totaal|Number of queries served for a DNS zone|No Dimensions|
|RecordSetCount|Record Set Count|Aantal|Maximum|Number of Record Sets in a DNS zone|No Dimensions|
|RecordSetCapacityUtilization|Record Set Capacity Utilization|Procent|Maximum|Percent of Record Set capacity utilized by a DNS zone|No Dimensions|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PacketsInDDoS|Inbound packets DDoS|CountPerSecond|Maximum|Inbound packets DDoS|No Dimensions|
|PacketsDroppedDDoS|Inbound packets dropped DDoS|CountPerSecond|Maximum|Inbound packets dropped DDoS|No Dimensions|
|PacketsForwardedDDoS|Inbound packets forwarded DDoS|CountPerSecond|Maximum|Inbound packets forwarded DDoS|No Dimensions|
|TCPPacketsInDDoS|Inbound TCP packets DDoS|CountPerSecond|Maximum|Inbound TCP packets DDoS|No Dimensions|
|TCPPacketsDroppedDDoS|Inbound TCP packets dropped DDoS|CountPerSecond|Maximum|Inbound TCP packets dropped DDoS|No Dimensions|
|TCPPacketsForwardedDDoS|Inbound TCP packets forwarded DDoS|CountPerSecond|Maximum|Inbound TCP packets forwarded DDoS|No Dimensions|
|UDPPacketsInDDoS|Inbound UDP packets DDoS|CountPerSecond|Maximum|Inbound UDP packets DDoS|No Dimensions|
|UDPPacketsDroppedDDoS|Inbound UDP packets dropped DDoS|CountPerSecond|Maximum|Inbound UDP packets dropped DDoS|No Dimensions|
|UDPPacketsForwardedDDoS|Inbound UDP packets forwarded DDoS|CountPerSecond|Maximum|Inbound UDP packets forwarded DDoS|No Dimensions|
|BytesInDDoS|Inbound bytes DDoS|BytesPerSecond|Maximum|Inbound bytes DDoS|No Dimensions|
|BytesDroppedDDoS|Inbound bytes dropped DDoS|BytesPerSecond|Maximum|Inbound bytes dropped DDoS|No Dimensions|
|BytesForwardedDDoS|Inbound bytes forwarded DDoS|BytesPerSecond|Maximum|Inbound bytes forwarded DDoS|No Dimensions|
|TCPBytesInDDoS|Inbound TCP bytes DDoS|BytesPerSecond|Maximum|Inbound TCP bytes DDoS|No Dimensions|
|TCPBytesDroppedDDoS|Inbound TCP bytes dropped DDoS|BytesPerSecond|Maximum|Inbound TCP bytes dropped DDoS|No Dimensions|
|TCPBytesForwardedDDoS|Inbound TCP bytes forwarded DDoS|BytesPerSecond|Maximum|Inbound TCP bytes forwarded DDoS|No Dimensions|
|UDPBytesInDDoS|Inbound UDP bytes DDoS|BytesPerSecond|Maximum|Inbound UDP bytes DDoS|No Dimensions|
|UDPBytesDroppedDDoS|Inbound UDP bytes dropped DDoS|BytesPerSecond|Maximum|Inbound UDP bytes dropped DDoS|No Dimensions|
|UDPBytesForwardedDDoS|Inbound UDP bytes forwarded DDoS|BytesPerSecond|Maximum|Inbound UDP bytes forwarded DDoS|No Dimensions|
|IfUnderDDoSAttack|Under DDoS attack or not|Aantal|Maximum|Under DDoS attack or not|No Dimensions|
|DDoSTriggerTCPPackets|Inbound TCP packets to trigger DDoS mitigation|CountPerSecond|Maximum|Inbound TCP packets to trigger DDoS mitigation|No Dimensions|
|DDoSTriggerUDPPackets|Inbound UDP packets to trigger DDoS mitigation|CountPerSecond|Maximum|Inbound UDP packets to trigger DDoS mitigation|No Dimensions|
|DDoSTriggerSYNPackets|Inbound SYN packets to trigger DDoS mitigation|CountPerSecond|Maximum|Inbound SYN packets to trigger DDoS mitigation|No Dimensions|
|VipAvailability|Data Path Availability|Aantal|Average|Average IP Address availability per time duration|Port|
|ByteCount|Byte Count|Aantal|Totaal|Total number of Bytes transmitted within time period|Port, Direction|
|PacketCount|Packet Count|Aantal|Totaal|Total number of Packets transmitted within time period|Port, Direction|
|SynCount|SYN Count|Aantal|Totaal|Total number of SYN Packets transmitted within time period|Port, Direction|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ApplicationRuleHit|Application rules hit count|Aantal|Totaal|Number of times Application rules were hit|Status, Reason, Protocol|
|NetworkRuleHit|Network rules hit count|Aantal|Totaal|Number of times Network rules were hit|Status, Reason, Protocol|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Doorvoer|Doorvoer|BytesPerSecond|Totaal|Number of bytes per second the Application Gateway has served|No Dimensions|
|UnhealthyHostCount|Unhealthy Host Count|Aantal|Average|Number of unhealthy backend hosts|BackendSettingsPool|
|HealthyHostCount|Healthy Host Count|Aantal|Average|Number of healthy backend hosts|BackendSettingsPool|
|TotalRequests|Totaal aantal aanvragen|Aantal|Totaal|Count of successful requests that Application Gateway has served|BackendSettingsPool|
|FailedRequests|Mislukte aanvragen|Aantal|Totaal|Count of failed requests that Application Gateway has served|BackendSettingsPool|
|ResponseStatus|Response Status|Aantal|Totaal|Http response status returned by Application Gateway|HttpStatusGroup|
|CurrentConnections|Current Connections|Aantal|Totaal|Count of current connections established with Application Gateway|No Dimensions|
|CapacityUnits|Current Capacity Units|Aantal|Average|Capacity Units consumed|No Dimensions|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AverageBandwidth|Gateway S2S Bandwidth|BytesPerSecond|Average|Average site-to-site bandwidth of a gateway in bytes per second|No Dimensions|
|P2SBandwidth|Gateway P2S Bandwidth|BytesPerSecond|Average|Average point-to-site bandwidth of a gateway in bytes per second|No Dimensions|
|P2SConnectionCount|P2S Connection Count|Aantal|Maximum|Point-to-site connection count of a gateway|Protocol|
|TunnelAverageBandwidth|Tunnel Bandwidth|BytesPerSecond|Average|Average bandwidth of a tunnel in bytes per second|ConnectionName, RemoteIP|
|TunnelEgressBytes|Tunnel Egress Bytes|Bytes|Totaal|Outgoing bytes of a tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Tunnel Ingress Bytes|Bytes|Totaal|Incoming bytes of a tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Tunnel Egress Packets|Aantal|Totaal|Outgoing packet count of a tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Tunnel Ingress Packets|Aantal|Totaal|Incoming packet count of a tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Tunnel Egress TS Mismatch Packet Drop|Aantal|Totaal|Outgoing packet drop count from traffic selector mismatch of a tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Tunnel Ingress TS Mismatch Packet Drop|Aantal|Totaal|Incoming packet drop count from traffic selector mismatch of a tunnel|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Bits ingressing Azure per second|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Bits egressing Azure per second|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Bits ingressing Azure per second|No Dimensions|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Bits egressing Azure per second|No Dimensions|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Bits ingressing Azure per second|No Dimensions|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Bits egressing Azure per second|No Dimensions|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|QpsByEndpoint|Queries by Endpoint Returned|Aantal|Totaal|Number of times a Traffic Manager endpoint was returned in the given time frame|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Endpoint Status by Endpoint|Aantal|Maximum|1 if an endpoint's probe status is "Enabled", 0 otherwise.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Probes Failed|Procent|Average|% of connectivity monitoring probes failed|No Dimensions|
|AverageRoundtripMs|Avg. Round-trip Time (ms)|MilliSeconds|Average|Average network round-trip time (ms) for connectivity monitoring probes sent between source and destination|No Dimensions|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|RequestCount|Aantal aanvragen|Aantal|Totaal|The number of client requests served by the HTTP/S proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Request Size|Bytes|Totaal|The number of bytes sent as requests from clients to the HTTP/S proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Response Size|Bytes|Totaal|The number of bytes sent as responses from HTTP/S proxy to clients|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Backend Request Count|Aantal|Totaal|The number of requests sent from the HTTP/S proxy to backends|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|Backend Request Latency|MilliSeconds|Average|The time calculated from when the request was sent by the HTTP/S proxy to the backend until the HTTP/S proxy received the last response byte from the backend|Backend|
|TotalLatency|Total Latency|MilliSeconds|Average|The time calculated from when the client request was received by the HTTP/S proxy until the client acknowledged the last response byte from the HTTP/S proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Backend Health Percentage|Procent|Average|The percentage of successful health probes from the HTTP/S proxy to backends|Backend, BackendPool|
|WebApplicationFirewallRequestCount|Web Application Firewall Request Count|Aantal|Totaal|The number of client requests processed by the Web Application Firewall|PolicyName, RuleName, Action|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|registration.all|Registration Operations|Aantal|Totaal|The count of all successful registration operations (creations updates queries and deletions). |No Dimensions|
|registration.create|Registration Create Operations|Aantal|Totaal|The count of all successful registration creations.|No Dimensions|
|registration.update|Registration Update Operations|Aantal|Totaal|The count of all successful registration updates.|No Dimensions|
|registration.get|Registration Read Operations|Aantal|Totaal|The count of all successful registration queries.|No Dimensions|
|registration.delete|Registration Delete Operations|Aantal|Totaal|The count of all successful registration deletions.|No Dimensions|
|incoming|Incoming Messages|Aantal|Totaal|The count of all successful send API calls. |No Dimensions|
|incoming.scheduled|Scheduled Push Notifications Sent|Aantal|Totaal|Scheduled Push Notifications Canceled|No Dimensions|
|incoming.scheduled.cancel|Scheduled Push Notifications Canceled|Aantal|Totaal|Scheduled Push Notifications Canceled|No Dimensions|
|scheduled.pending|Pending Scheduled Notifications|Aantal|Totaal|Pending Scheduled Notifications|No Dimensions|
|installation.all|Installation Management Operations|Aantal|Totaal|Installation Management Operations|No Dimensions|
|installation.get|Get Installation Operations|Aantal|Totaal|Get Installation Operations|No Dimensions|
|installation.upsert|Create or Update Installation Operations|Aantal|Totaal|Create or Update Installation Operations|No Dimensions|
|installation.patch|Patch Installation Operations|Aantal|Totaal|Patch Installation Operations|No Dimensions|
|installation.delete|Delete Installation Operations|Aantal|Totaal|Delete Installation Operations|No Dimensions|
|outgoing.allpns.success|Successful notifications|Aantal|Totaal|The count of all successful notifications.|No Dimensions|
|outgoing.allpns.invalidpayload|Payload Errors|Aantal|Totaal|The count of pushes that failed because the PNS returned a bad payload error.|No Dimensions|
|outgoing.allpns.pnserror|External Notification System Errors|Aantal|Totaal|The count of pushes that failed because there was a problem communicating with the PNS (excludes authentication problems).|No Dimensions|
|outgoing.allpns.channelerror|Channel Errors|Aantal|Totaal|The count of pushes that failed because the channel was invalid not associated with the correct app throttled or expired.|No Dimensions|
|outgoing.allpns.badorexpiredchannel|Bad or Expired Channel Errors|Aantal|Totaal|The count of pushes that failed because the channel/token/registrationId in the registration was expired or invalid.|No Dimensions|
|outgoing.wns.success|WNS Successful Notifications|Aantal|Totaal|The count of all successful notifications.|No Dimensions|
|outgoing.wns.invalidcredentials|WNS Authorization Errors (Invalid Credentials)|Aantal|Totaal|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked. (Windows Live does not recognize the credentials).|No Dimensions|
|outgoing.wns.badchannel|WNS Bad Channel Error|Aantal|Totaal|The count of pushes that failed because the ChannelURI in the registration was not recognized (WNS status: 404 not found).|No Dimensions|
|outgoing.wns.expiredchannel|WNS Expired Channel Error|Aantal|Totaal|The count of pushes that failed because the ChannelURI is expired (WNS status: 410 Gone).|No Dimensions|
|outgoing.wns.throttled|WNS Throttled Notifications|Aantal|Totaal|The count of pushes that failed because WNS is throttling this app (WNS status: 406 Not Acceptable).|No Dimensions|
|outgoing.wns.tokenproviderunreachable|WNS Authorization Errors (Unreachable)|Aantal|Totaal|Windows Live is not reachable.|No Dimensions|
|outgoing.wns.invalidtoken|WNS Authorization Errors (Invalid Token)|Aantal|Totaal|The token provided to WNS is not valid (WNS status: 401 Unauthorized).|No Dimensions|
|outgoing.wns.wrongtoken|WNS Authorization Errors (Wrong Token)|Aantal|Totaal|The token provided to WNS is valid but for another application (WNS status: 403 Forbidden). This can happen if the ChannelURI in the registration is associated with another app. Check that the client app is associated with the same app whose credentials are in the notification hub.|No Dimensions|
|outgoing.wns.invalidnotificationformat|WNS Invalid Notification Format|Aantal|Totaal|The format of the notification is invalid (WNS status: 400). Note that WNS does not reject all invalid payloads.|No Dimensions|
|outgoing.wns.invalidnotificationsize|WNS Invalid Notification Size Error|Aantal|Totaal|The notification payload is too large (WNS status: 413).|No Dimensions|
|outgoing.wns.channelthrottled|WNS Channel Throttled|Aantal|Totaal|The notification was dropped because the ChannelURI in the registration is throttled (WNS response header: X-WNS-NotificationStatus:channelThrottled).|No Dimensions|
|outgoing.wns.channeldisconnected|WNS Channel Disconnected|Aantal|Totaal|The notification was dropped because the ChannelURI in the registration is throttled (WNS response header: X-WNS-DeviceConnectionStatus: disconnected).|No Dimensions|
|outgoing.wns.dropped|WNS Dropped Notifications|Aantal|Totaal|The notification was dropped because the ChannelURI in the registration is throttled (X-WNS-NotificationStatus: dropped but not X-WNS-DeviceConnectionStatus: disconnected).|No Dimensions|
|outgoing.wns.pnserror|WNS Errors|Aantal|Totaal|Notification not delivered because of errors communicating with WNS.|No Dimensions|
|outgoing.wns.authenticationerror|WNS Authentication Errors|Aantal|Totaal|Notification not delivered because of errors communicating with Windows Live invalid credentials or wrong token.|No Dimensions|
|outgoing.apns.success|APNS Successful Notifications|Aantal|Totaal|The count of all successful notifications.|No Dimensions|
|outgoing.apns.invalidcredentials|APNS Authorization Errors|Aantal|Totaal|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked.|No Dimensions|
|outgoing.apns.badchannel|APNS Bad Channel Error|Aantal|Totaal|The count of pushes that failed because the token is invalid (APNS binary protocol status code: 8. APNS HTTP protocol status code: 400 with "BadDeviceToken").|No Dimensions|
|outgoing.apns.expiredchannel|APNS Expired Channel Error|Aantal|Totaal|The count of token that were invalidated by the APNS feedback channel.|No Dimensions|
|outgoing.apns.invalidnotificationsize|APNS Invalid Notification Size Error|Aantal|Totaal|The count of pushes that failed because the payload was too large (APNS binary protocol status code: 7).|No Dimensions|
|outgoing.apns.pnserror|APNS Errors|Aantal|Totaal|The count of pushes that failed because of errors communicating with APNS.|No Dimensions|
|outgoing.gcm.success|GCM Successful Notifications|Aantal|Totaal|The count of all successful notifications.|No Dimensions|
|outgoing.gcm.invalidcredentials|GCM Authorization Errors (Invalid Credentials)|Aantal|Totaal|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked.|No Dimensions|
|outgoing.gcm.badchannel|GCM Bad Channel Error|Aantal|Totaal|The count of pushes that failed because the registrationId in the registration was not recognized (GCM result: Invalid Registration).|No Dimensions|
|outgoing.gcm.expiredchannel|GCM Expired Channel Error|Aantal|Totaal|The count of pushes that failed because the registrationId in the registration was expired (GCM result: NotRegistered).|No Dimensions|
|outgoing.gcm.throttled|GCM Throttled Notifications|Aantal|Totaal|The count of pushes that failed because GCM throttled this app (GCM status code: 501-599 or result:Unavailable).|No Dimensions|
|outgoing.gcm.invalidnotificationformat|GCM Invalid Notification Format|Aantal|Totaal|The count of pushes that failed because the payload was not formatted correctly (GCM result: InvalidDataKey or InvalidTtl).|No Dimensions|
|outgoing.gcm.invalidnotificationsize|GCM Invalid Notification Size Error|Aantal|Totaal|The count of pushes that failed because the payload was too large (GCM result: MessageTooBig).|No Dimensions|
|outgoing.gcm.wrongchannel|GCM Wrong Channel Error|Aantal|Totaal|The count of pushes that failed because the registrationId in the registration is not associated to the current app (GCM result: InvalidPackageName).|No Dimensions|
|outgoing.gcm.pnserror|GCM Errors|Aantal|Totaal|The count of pushes that failed because of errors communicating with GCM.|No Dimensions|
|outgoing.gcm.authenticationerror|GCM Authentication Errors|Aantal|Totaal|The count of pushes that failed because the PNS did not accept the provided credentials the credentials are blocked or the SenderId is not correctly configured in the app (GCM result: MismatchedSenderId).|No Dimensions|
|outgoing.mpns.success|MPNS Successful Notifications|Aantal|Totaal|The count of all successful notifications.|No Dimensions|
|outgoing.mpns.invalidcredentials|MPNS Invalid Credentials|Aantal|Totaal|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked.|No Dimensions|
|outgoing.mpns.badchannel|MPNS Bad Channel Error|Aantal|Totaal|The count of pushes that failed because the ChannelURI in the registration was not recognized (MPNS status: 404 not found).|No Dimensions|
|outgoing.mpns.throttled|MPNS Throttled Notifications|Aantal|Totaal|The count of pushes that failed because MPNS is throttling this app (WNS MPNS: 406 Not Acceptable).|No Dimensions|
|outgoing.mpns.invalidnotificationformat|MPNS Invalid Notification Format|Aantal|Totaal|The count of pushes that failed because the payload of the notification was too large.|No Dimensions|
|outgoing.mpns.channeldisconnected|MPNS Channel Disconnected|Aantal|Totaal|The count of pushes that failed because the ChannelURI in the registration was disconnected (MPNS status: 412 not found).|No Dimensions|
|outgoing.mpns.dropped|MPNS Dropped Notifications|Aantal|Totaal|The count of pushes that were dropped by MPNS (MPNS response header: X-NotificationStatus: QueueFull or Suppressed).|No Dimensions|
|outgoing.mpns.pnserror|MPNS Errors|Aantal|Totaal|The count of pushes that failed because of errors communicating with MPNS.|No Dimensions|
|outgoing.mpns.authenticationerror|MPNS Authentication Errors|Aantal|Totaal|The count of pushes that failed because the PNS did not accept the provided credentials or the credentials are blocked.|No Dimensions|
|notificationhub.pushes|All Outgoing Notifications|Aantal|Totaal|All outgoing notifications of the notification hub|No Dimensions|
|incoming.all.requests|All Incoming Requests|Aantal|Totaal|Total incoming requests for a notification hub|No Dimensions|
|incoming.all.failedrequests|All Incoming Failed Requests|Aantal|Totaal|Total incoming failed requests for a notification hub|No Dimensions|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Average_% Free Inodes|% Free Inodes|Aantal|Average|Average_% Free Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|% Free Space|Aantal|Average|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Inodes|% Used Inodes|Aantal|Average|Average_% Used Inodes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Space|% Used Space|Aantal|Average|Average_% Used Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Read Bytes/sec|Disk Read Bytes/sec|Aantal|Average|Average_Disk Read Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Disk Reads/sec|Aantal|Average|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Aantal|Average|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Write Bytes/sec|Disk Write Bytes/sec|Aantal|Average|Average_Disk Write Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Disk Writes/sec|Aantal|Average|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Free Megabytes|Aantal|Average|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk Bytes/sec|Logical Disk Bytes/sec|Aantal|Average|Average_Logical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Memory|% Available Memory|Aantal|Average|Average_% Available Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Available Swap Space|% Available Swap Space|Aantal|Average|Average_% Available Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Memory|% Used Memory|Aantal|Average|Average_% Used Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Used Swap Space|% Used Swap Space|Aantal|Average|Average_% Used Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Memory|Available MBytes Memory|Aantal|Average|Average_Available MBytes Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes Swap|Available MBytes Swap|Aantal|Average|Average_Available MBytes Swap|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Reads/sec|Page Reads/sec|Aantal|Average|Average_Page Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page Writes/sec|Page Writes/sec|Aantal|Average|Average_Page Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages/sec|Pages/sec|Aantal|Average|Average_Pages/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MBytes Swap Space|Used MBytes Swap Space|Aantal|Average|Average_Used MBytes Swap Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory MBytes|Used Memory MBytes|Aantal|Average|Average_Used Memory MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Transmitted|Total Bytes Transmitted|Aantal|Average|Average_Total Bytes Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes Received|Total Bytes Received|Aantal|Average|Average_Total Bytes Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Bytes|Total Bytes|Aantal|Average|Average_Total Bytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Transmitted|Total Packets Transmitted|Aantal|Average|Average_Total Packets Transmitted|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Packets Received|Total Packets Received|Aantal|Average|Average_Total Packets Received|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx Errors|Total Rx Errors|Aantal|Average|Average_Total Rx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx Errors|Total Tx Errors|Aantal|Average|Average_Total Tx Errors|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Collisions|Total Collisions|Aantal|Average|Average_Total Collisions|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Read|Avg. Disk sec/Read|Aantal|Average|Average_Avg. Disk sec/Read|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Transfer|Avg. Disk sec/Transfer|Aantal|Average|Average_Avg. Disk sec/Transfer|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Write|Avg. Disk sec/Write|Aantal|Average|Average_Avg. Disk sec/Write|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk Bytes/sec|Physical Disk Bytes/sec|Aantal|Average|Average_Physical Disk Bytes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct Privileged Time|Pct Privileged Time|Aantal|Average|Average_Pct Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct User Time|Pct User Time|Aantal|Average|Average_Pct User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used Memory kBytes|Used Memory kBytes|Aantal|Average|Average_Used Memory kBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual Shared Memory|Virtual Shared Memory|Aantal|Average|Average_Virtual Shared Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC Time|% DPC Time|Aantal|Average|Average_% DPC Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Idle Time|% Idle Time|Aantal|Average|Average_% Idle Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Interrupt Time|% Interrupt Time|Aantal|Average|Average_% Interrupt Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% IO Wait Time|% IO Wait Time|Aantal|Average|Average_% IO Wait Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Nice Time|% Nice Time|Aantal|Average|Average_% Nice Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Privileged Time|% Privileged Time|Aantal|Average|Average_% Privileged Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|% Processor Time|Aantal|Average|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% User Time|% User Time|Aantal|Average|Average_% User Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Physical Memory|Free Physical Memory|Aantal|Average|Average_Free Physical Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Space in Paging Files|Free Space in Paging Files|Aantal|Average|Average_Free Space in Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Virtual Memory|Free Virtual Memory|Aantal|Average|Average_Free Virtual Memory|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Processen|Aantal|Average|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size Stored In Paging Files|Size Stored In Paging Files|Aantal|Average|Average_Size Stored In Paging Files|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Uptime|Aantal|Average|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Gebruikers|Aantal|Average|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Read|Avg. Disk sec/Read|Aantal|Average|Average_Avg. Disk sec/Read|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk sec/Write|Avg. Disk sec/Write|Aantal|Average|Average_Avg. Disk sec/Write|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current Disk Queue Length|Current Disk Queue Length|Aantal|Average|Average_Current Disk Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Reads/sec|Disk Reads/sec|Aantal|Average|Average_Disk Reads/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Transfers/sec|Disk Transfers/sec|Aantal|Average|Average_Disk Transfers/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk Writes/sec|Disk Writes/sec|Aantal|Average|Average_Disk Writes/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free Megabytes|Free Megabytes|Aantal|Average|Average_Free Megabytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Free Space|% Free Space|Aantal|Average|Average_% Free Space|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MBytes|Available MBytes|Aantal|Average|Average_Available MBytes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Committed Bytes In Use|% Committed Bytes In Use|Aantal|Average|Average_% Committed Bytes In Use|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Received/sec|Bytes Received/sec|Aantal|Average|Average_Bytes Received/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Sent/sec|Bytes Sent/sec|Aantal|Average|Average_Bytes Sent/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes Total/sec|Bytes Total/sec|Aantal|Average|Average_Bytes Total/sec|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Processor Time|% Processor Time|Aantal|Average|Average_% Processor Time|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Queue Length|Processor Queue Length|Aantal|Average|Average_Processor Queue Length|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Heartbeat|Heartbeat|Aantal|Totaal|Heartbeat|Computer, OSType, Version, SourceComputerId|
|Bijwerken|Bijwerken|Aantal|Average|Bijwerken|Computer, Product, Classification, UpdateState, Optional, Approved|
|Gebeurtenis|Gebeurtenis|Aantal|Average|Gebeurtenis|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|

## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|QueryDuration|Query Duration|Milliseconds|Average|DAX Query duration in last interval|No Dimensions|
|QueryPoolJobQueueLength|Threads: Query pool job queue length|Aantal|Average|Number of jobs in the queue of the query thread pool.|No Dimensions|
|qpu_high_utilization_metric|QPU High Utilization|Aantal|Totaal|QPU High Utilization In Last Minute, 1 For High QPU Utilization, Otherwise 0|No Dimensions|
|memory_metric|Geheugen|Bytes|Average|Memory. Range 0-3 GB for A1, 0-5 GB for A2, 0-10 GB for A3, 0-25 GB for A4, 0-50 GB for A5 and 0-100 GB for A6|No Dimensions|
|memory_thrashing_metric|Memory Thrashing|Procent|Average|Average memory thrashing.|No Dimensions|

## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namespaces

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Aantal|Totaal|Successful ListenerConnections for Microsoft.Relay.|EntityName|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Aantal|Totaal|ClientError on ListenerConnections for Microsoft.Relay.|EntityName|
|ListenerConnections-ServerError|ListenerConnections-ServerError|Aantal|Totaal|ServerError on ListenerConnections for Microsoft.Relay.|EntityName|
|SenderConnections-Success|SenderConnections-Success|Aantal|Totaal|Successful SenderConnections for Microsoft.Relay.|EntityName|
|SenderConnections-ClientError|SenderConnections-ClientError|Aantal|Totaal|ClientError on SenderConnections for Microsoft.Relay.|EntityName|
|SenderConnections-ServerError|SenderConnections-ServerError|Aantal|Totaal|ServerError on SenderConnections for Microsoft.Relay.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Aantal|Totaal|Total ListenerConnections for Microsoft.Relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Aantal|Totaal|Total SenderConnections requests for Microsoft.Relay.|EntityName|
|ActiveConnections|ActiveConnections|Aantal|Totaal|Total ActiveConnections for Microsoft.Relay.|EntityName|
|ActiveListeners|ActiveListeners|Aantal|Totaal|Total ActiveListeners for Microsoft.Relay.|EntityName|
|BytesTransferred|BytesTransferred|Aantal|Totaal|Total BytesTransferred for Microsoft.Relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Aantal|Totaal|Total ListenerDisconnects for Microsoft.Relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Aantal|Totaal|Total SenderDisconnects for Microsoft.Relay.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SearchLatency|Search Latency|Seconden|Average|Average search latency for the search service|No Dimensions|
|SearchQueriesPerSecond|Search queries per second|CountPerSecond|Average|Search queries per second for the search service|No Dimensions|
|ThrottledSearchQueriesPercentage|Throttled search queries percentage|Procent|Average|Percentage of search queries that were throttled for the search service|No Dimensions|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SuccessfulRequests|Successful Requests (Preview)|Aantal|Totaal|Total successful requests for a namespace (Preview)|EntityName|
|ServerErrors|Server Errors. (Preview)|Aantal|Totaal|Server Errors for Microsoft.ServiceBus. (Preview)|EntityName|
|UserErrors|User Errors. (Preview)|Aantal|Totaal|User Errors for Microsoft.ServiceBus. (Preview)|EntityName|
|ThrottledRequests|Throttled Requests. (Preview)|Aantal|Totaal|Throttled Requests for Microsoft.ServiceBus. (Preview)|EntityName|
|IncomingRequests|Incoming Requests (Preview)|Aantal|Totaal|Incoming Requests for Microsoft.ServiceBus. (Preview)|EntityName|
|IncomingMessages|Incoming Messages (Preview)|Aantal|Totaal|Incoming Messages for Microsoft.ServiceBus. (Preview)|EntityName|
|OutgoingMessages|Outgoing Messages (Preview)|Aantal|Totaal|Outgoing Messages for Microsoft.ServiceBus. (Preview)|EntityName|
|ActiveConnections|ActiveConnections (Preview)|Aantal|Totaal|Total Active Connections for Microsoft.ServiceBus. (Preview)|No Dimensions|
|Grootte|Size (Preview)|Bytes|Average|Size of an Queue/Topic in Bytes. (Preview)|EntityName|
|Berichten|Count of messages in a Queue/Topic. (Preview)|Aantal|Average|Count of messages in a Queue/Topic. (Preview)|EntityName|
|ActiveMessages|Count of active messages in a Queue/Topic. (Preview)|Aantal|Average|Count of active messages in a Queue/Topic. (Preview)|EntityName|
|DeadletteredMessages|Count of dead-lettered messages in a Queue/Topic. (Preview)|Aantal|Average|Count of dead-lettered messages in a Queue/Topic. (Preview)|EntityName|
|ScheduledMessages|Count of scheduled messages in a Queue/Topic. (Preview)|Aantal|Average|Count of scheduled messages in a Queue/Topic. (Preview)|EntityName|
|CPUXNS|CPU usage per namespace|Procent|Maximum|Service bus premium namespace CPU usage metric|No Dimensions|
|WSXNS|Memory size usage per namespace|Procent|Maximum|Service bus premium namespace memory usage metric|No Dimensions|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Aantal|Average|Cpu allocated to this container in millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Bytes|Average|Memory allocated to this container in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Aantal|Average|Actual CPU usage in millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Bytes|Average|Actual memory usage in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Procent|Average|Utilization of CPU for this container as percentage of AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Procent|Average|Utilization of CPU for this container as percentage of AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Aantal|Average|Status of Service Fabric Mesh application|ApplicationName, Status|
|ServiceStatus|ServiceStatus|Aantal|Average|Health Status of a service in Service Fabric Mesh application|ApplicationName, Status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Aantal|Average|Health Status of a service replica in Service Fabric Mesh application|ApplicationName, Status, ServiceName, ServiceReplicaName|
|ContainerStatus|ContainerStatus|Aantal|Average|Status of the container in Service Fabric Mesh application|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, Status|
|RestartCount|RestartCount|Aantal|Average|Restart count of a container in Service Fabric Mesh application|ApplicationName, Status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ConnectionCount|Connection Count|Aantal|Maximum|The amount of user connection.|Eindpunt|
|MessageCount|Message Count|Aantal|Totaal|The total amount of messages.|No Dimensions|
|InboundTraffic|Binnenkomend verkeer|Bytes|Totaal|The inbound traffic of service|No Dimensions|
|OutboundTraffic|Uitgaand verkeer|Bytes|Totaal|The outbound traffic of service|No Dimensions|
|UserErrors|User Errors|Procent|Maximum|The percentage of user errors|No Dimensions|
|SystemErrors|System Errors|Procent|Maximum|The percentage of system errors|No Dimensions|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|allocated_data_storage|Data space allocated|Bytes|Average|Data space allocated. Not applicable to data warehouses.|No Dimensions|
|app_cpu_billed|App CPU billed|Aantal|Totaal|App CPU billed. Applies to serverless databases.|No Dimensions|
|app_cpu_percent|App CPU percentage|Procent|Average|App CPU percentage. Applies to serverless databases.|No Dimensions|
|app_memory_percent|App memory used percentage|Procent|Average|App memory used percentage. Applies to serverless databases.|No Dimensions|
|blocked_by_firewall|Blocked by Firewall|Aantal|Totaal|Blocked by Firewall|No Dimensions|
|cache_hit_percent|Cache hit percentage|Procent|Maximum|Cache hit percentage. Applies only to data warehouses.|No Dimensions|
|cache_used_percent|Cache used percentage|Procent|Maximum|Cache used percentage. Applies only to data warehouses.|No Dimensions|
|connection_failed|Mislukte verbindingen|Aantal|Totaal|Mislukte verbindingen|No Dimensions|
|connection_successful|Successful Connections|Aantal|Totaal|Successful Connections|No Dimensions|
|cpu_percent|CPU-percentage|Procent|Average|CPU-percentage|No Dimensions|
|cpu_limit|CPU limit|Aantal|Average|CPU limit. Applies to vCore-based databases.|No Dimensions|
|cpu_used|CPU used|Aantal|Average|CPU used. Applies to vCore-based databases.|No Dimensions|
|deadlock|Deadlocks|Aantal|Totaal|Deadlocks. Not applicable to data warehouses.|No Dimensions|
|diff_backup_size_bytes|Differential backup storage size|Bytes|Maximum|Cumulative differential backup storage size. Applies to General Purpose and Business Critical databases. Does not currently apply to Manage instance databases.|No Dimensions|
|dtu_limit|DTU Limit|Aantal|Average|DTU Limit. Applies to DTU-based databases.|No Dimensions|
|dtu_consumption_percent|DTU-percentage|Procent|Average|DTU percentage. Applies to DTU-based databases.|No Dimensions|
|dtu_used|DTU used|Aantal|Average|DTU used. Applies to DTU-based databases.|No Dimensions|
|dw_cpu_percent|DW node level CPU percentage|Procent|Average|DW node level CPU percentage|DwLogicalNodeId|
|dw_physical_data_read_percent|DW node level Data IO percentage|Procent|Average|DW node level Data IO percentage|DwLogicalNodeId|
|dwu_consumption_percent|DWU percentage|Procent|Maximum|DWU percentage. Applies only to data warehouses.|No Dimensions|
|dwu_limit|DWU limit|Aantal|Maximum|DWU limit. Applies only to data warehouses.|No Dimensions|
|dwu_used|DWU used|Aantal|Maximum|DWU used. Applies only to data warehouses.|No Dimensions|
|full_backup_size_bytes|Full backup storage size|Bytes|Maximum|Cumulative full backup storage size. Applies to General Purpose and Business Critical databases. Does not currently apply to Manage instance databases.|No Dimensions|
|local_tempdb_usage_percent|Local tempdb percentage|Procent|Average|Local tempdb percentage. Applies only to data warehouses.|No Dimensions|
|log_backup_size_bytes|Log backup storage size|Bytes|Maximum|Cumulative log backup storage size. Applies to General Purpose and Business Critical databases. Does not currently apply to Manage instance databases.|No Dimensions|
|log_write_percent|Log IO percentage|Procent|Average|Log IO percentage. Not applicable to data warehouses.|No Dimensions|
|physical_data_read_percent|Gegevens-I/O-percentage|Procent|Average|Gegevens-I/O-percentage|No Dimensions|
|sessions_percent|Sessions percentage|Procent|Average|Sessions percentage. Not applicable to data warehouses.|No Dimensions|
|sqlserver_process_core_percent|SQL Server process core percent|Procent|Maximum|This metric is a placeholder and not populated at this time.|No Dimensions|
|sqlserver_process_memory_percent|SQL Server process memory percent|Procent|Maximum|This metric is a placeholder and not populated at this time.|No Dimensions|
|opslag|Data space used|Bytes|Maximum|Total database size. Not applicable to data warehouses.|No Dimensions|
|storage_percent|Data space used percent|Procent|Maximum|Database size percentage. Not applicable to data warehouses or hyperscale databases.|No Dimensions|
|tempdb_data_size|Tempdb Data File Size Kilobytes|Aantal|Maximum|Tempdb Data File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|tempdb_log_size|Tempdb Log File Size Kilobytes|Aantal|Maximum|Tempdb Log File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|tempdb_log_used_percent|Tempdb Percent Log Used|Procent|Maximum|Tempdb Percent Log Used. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|workers_percent|Workers percentage|Procent|Average|Workers percentage. Not applicable to data warehouses.|No Dimensions|
|xtp_storage_percent|In-Memory OLTP storage percent|Procent|Average|In-Memory OLTP storage percent. Not applicable to data warehouses.|No Dimensions|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|allocated_data_storage|Data space allocated|Bytes|Average|Data space allocated|No Dimensions|
|allocated_data_storage_percent|Data space allocated percent|Procent|Maximum|Data space allocated percent|No Dimensions|
|cpu_limit|CPU limit|Aantal|Average|CPU limit. Applies to vCore-based elastic pools.|No Dimensions|
|cpu_percent|CPU-percentage|Procent|Average|CPU-percentage|No Dimensions|
|cpu_used|CPU used|Aantal|Average|CPU used. Applies to vCore-based elastic pools.|No Dimensions|
|dtu_consumption_percent|DTU-percentage|Procent|Average|DTU percentage. Applies to DTU-based elastic pools.|No Dimensions|
|eDTU_limit|eDTU limit|Aantal|Average|eDTU limit. Applies to DTU-based elastic pools.|No Dimensions|
|eDTU_used|eDTU used|Aantal|Average|eDTU used. Applies to DTU-based elastic pools.|No Dimensions|
|log_write_percent|Log IO percentage|Procent|Average|Log IO percentage|No Dimensions|
|physical_data_read_percent|Gegevens-I/O-percentage|Procent|Average|Gegevens-I/O-percentage|No Dimensions|
|sessions_percent|Sessions percentage|Procent|Average|Sessions percentage|No Dimensions|
|storage_limit|Data max size|Bytes|Average|Storage limit|No Dimensions|
|storage_percent|Data space used percent||Procent|Average|Storage percentage|No Dimensions|
|storage_used|Data space used|Bytes|Average|Gebruikte opslag|No Dimensions|
|sqlserver_process_core_percent|SQL Server process core percent|Procent|Maximum|This metric is a placeholder and not populated at this time.|No Dimensions|
|sqlserver_process_memory_percent|SQL Server process memory percent|Procent|Maximum|This metric is a placeholder and not populated at this time.|No Dimensions|
|tempdb_data_size|Tempdb Data File Size Kilobytes|Aantal|Maximum|Tempdb Data File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|tempdb_log_size|Tempdb Log File Size Kilobytes|Aantal|Maximum|Tempdb Log File Size Kilobytes. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|tempdb_log_used_percent|Tempdb Percent Log Used|Procent|Maximum|Tempdb Percent Log Used. Not applicable to data warehouses. This metric will be available for databases using the vCore purchasing model or 100 DTU and higher for DTU-based purchasing models.|No Dimensions|
|workers_percent|Workers percentage|Procent|Average|Workers percentage|No Dimensions|
|xtp_storage_percent|In-Memory OLTP storage percent|Procent|Average|In-Memory OLTP storage percent|No Dimensions|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|avg_cpu_percent|Average CPU percentage|Procent|Average|Average CPU percentage|No Dimensions|
|io_bytes_read|IO bytes read|Bytes|Average|IO bytes read|No Dimensions|
|io_requests|IO requests count|Aantal|Average|IO requests count|No Dimensions|
|io_bytes_written|IO bytes written|Bytes|Average|IO bytes written|No Dimensions|
|reserved_storage_mb|Storage space reserved|Aantal|Average|Storage space reserved|No Dimensions|
|storage_space_used_mb|Storage space used|Aantal|Average|Storage space used|No Dimensions|
|virtual_core_count|Virtual core count|Aantal|Average|Virtual core count|No Dimensions|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|UsedCapacity|Used capacity|Bytes|Average|Account used capacity|No Dimensions|
|Transacties|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Binnenkomend|Binnenkomend|Bytes|Totaal|The amount of ingress data, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|GeoType, ApiName, Authentication|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|The amount of egress data, in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Average|The average latency used by Azure Storage to process a successful request, in milliseconds. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Average|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|GeoType, ApiName, Authentication|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|The percentage of availability for the storage service or the specified API operation. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BlobCapacity|Blob Capacity|Bytes|Average|The amount of storage used by the storage account’s Blob service in bytes.|BlobType, Tier|
|BlobCount|Blob Count|Aantal|Totaal|The number of Blob in the storage account’s Blob service.|BlobType|       |BlobCount|Blob Count|Aantal|Average|The number of Blob in the storage account’s Blob service.|BlobType, Tier|
|ContainerCount|Blob Container Count|Aantal|Average|The number of containers in the storage account’s Blob service.|No Dimensions|
|IndexCapacity|Index Capacity|Bytes|Average|The amount of storage used by ADLS Gen2 (Hierarchical) Index in bytes.|No Dimensions|
|Transacties|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Binnenkomend|Binnenkomend|Bytes|Totaal|The amount of ingress data, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|GeoType, ApiName, Authentication|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|The amount of egress data, in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Average|The average latency used by Azure Storage to process a successful request, in milliseconds. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Average|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|GeoType, ApiName, Authentication|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|The percentage of availability for the storage service or the specified API operation. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|FileCapacity|File Capacity|Bytes|Average|The amount of storage used by the storage account’s File service in bytes.|No Dimensions|
|FileCount|File Count|Aantal|Average|The number of file in the storage account’s File service.|No Dimensions|
|FileShareCount|File Share Count|Aantal|Average|The number of file shares in the storage account’s File service.|No Dimensions|
|Transacties|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Binnenkomend|Binnenkomend|Bytes|Totaal|The amount of ingress data, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|GeoType, ApiName, Authentication|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|The amount of egress data, in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Average|The average latency used by Azure Storage to process a successful request, in milliseconds. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Average|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|GeoType, ApiName, Authentication|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|The percentage of availability for the storage service or the specified API operation. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|QueueCapacity|Queue Capacity|Bytes|Average|The amount of storage used by the storage account’s Queue service in bytes.|No Dimensions|
|QueueCount|Queue Count|Aantal|Average|The number of queue in the storage account’s Queue service.|No Dimensions|
|QueueMessageCount|Queue Message Count|Aantal|Average|The approximate number of queue messages in the storage account’s Queue service.|No Dimensions|
|Transacties|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Binnenkomend|Binnenkomend|Bytes|Totaal|The amount of ingress data, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|GeoType, ApiName, Authentication|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|The amount of egress data, in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Average|The average latency used by Azure Storage to process a successful request, in milliseconds. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Average|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|GeoType, ApiName, Authentication|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|The percentage of availability for the storage service or the specified API operation. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TableCapacity|Table Capacity|Bytes|Average|The amount of storage used by the storage account’s Table service in bytes.|No Dimensions|
|TableCount|Table Count|Aantal|Average|The number of table in the storage account’s Table service.|No Dimensions|
|TableEntityCount|Table Entity Count|Aantal|Average|The number of table entities in the storage account’s Table service.|No Dimensions|
|Transacties|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Use ResponseType dimension for the number of different type of response.|ResponseType, GeoType, ApiName, Authentication|
|Binnenkomend|Binnenkomend|Bytes|Totaal|The amount of ingress data, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|GeoType, ApiName, Authentication|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|The amount of egress data, in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|GeoType, ApiName, Authentication|
|SuccessServerLatency|Success Server Latency|Milliseconds|Average|The average latency used by Azure Storage to process a successful request, in milliseconds. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|GeoType, ApiName, Authentication|
|SuccessE2ELatency|Success E2E Latency|Milliseconds|Average|The average end-to-end latency of successful requests made to a storage service or the specified API operation, in milliseconds. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|GeoType, ApiName, Authentication|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|The percentage of availability for the storage service or the specified API operation. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|GeoType, ApiName, Authentication|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Sync Session Result|Aantal|Average|Metric that logs a value of 1 each time the Server Endpoint successfully completes a Sync Session with the Cloud Endpoint|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Files Synced|Aantal|Totaal|Count of Files synced|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Files not syncing|Aantal|Totaal|Count of files failed to sync|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Bytes synced|Bytes|Totaal|Total file size transferred for Sync Sessions|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Server Online Status|Aantal|Maximum|Metric that logs a value of 1 each time the registered server successfully records a heartbeat with the Cloud Endpoint|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Cloud tiering recall|Bytes|Totaal|Total size of data recalled by the server|ServerName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ResourceUtilization|SU % Utilization|Procent|Maximum|SU % Utilization|LogicalName, PartitionId|
|InputEvents|Input Events|Aantal|Totaal|Input Events|LogicalName, PartitionId|
|InputEventBytes|Input Event Bytes|Bytes|Totaal|Input Event Bytes|LogicalName, PartitionId|
|LateInputEvents|Late Input Events|Aantal|Totaal|Late Input Events|LogicalName, PartitionId|
|OutputEvents|Output Events|Aantal|Totaal|Output Events|LogicalName, PartitionId|
|ConversionErrors|Data Conversion Errors|Aantal|Totaal|Data Conversion Errors|LogicalName, PartitionId|
|Fouten|Runtime Errors|Aantal|Totaal|Runtime Errors|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Out of order Events|Aantal|Totaal|Out of order Events|LogicalName, PartitionId|
|AMLCalloutRequests|Function Requests|Aantal|Totaal|Function Requests|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Failed Function Requests|Aantal|Totaal|Failed Function Requests|LogicalName, PartitionId|
|AMLCalloutInputEvents|Function Events|Aantal|Totaal|Function Events|LogicalName, PartitionId|
|DeserializationError|Input Deserialization Errors|Aantal|Totaal|Input Deserialization Errors|LogicalName, PartitionId|
|EarlyInputEvents|Early Input Events|Aantal|Totaal|Early Input Events|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Watermark Delay|Seconden|Maximum|Watermark Delay|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Backlogged Input Events|Aantal|Maximum|Backlogged Input Events|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Input Sources Received|Aantal|Totaal|Input Sources Received|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages|Aantal|Totaal|Count of messages read from all Event hub or IoT hub event sources|No Dimensions|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages|Aantal|Totaal|Count of invalid messages read from all Event hub or IoT hub event sources|No Dimensions|
|IngressReceivedBytes|Ingress Received Bytes|Bytes|Totaal|Count of bytes read from all event sources|No Dimensions|
|IngressStoredBytes|Ingress Stored Bytes|Bytes|Totaal|Total size of events successfully processed and available for query|No Dimensions|
|IngressStoredEvents|Ingress Stored Events|Aantal|Totaal|Count of flattened events successfully processed and available for query|No Dimensions|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag|Seconden|Maximum|Difference between the time that the message is enqueued in the event source and the time it is processed in Ingress|No Dimensions|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag|Aantal|Average|Difference between the sequence number of last enqueued message in the event source partition and sequence number of message being processed in Ingress|No Dimensions|
|WarmStorageMaxProperties|Warm Storage Max Properties|Aantal|Maximum|Maximum number of properties used allowed by the environment for S1/S2 SKU and maximum number of properties allowed by Warm Store for PAYG SKU|No Dimensions|
|WarmStorageUsedProperties|Warm Storage Used Properties |Aantal|Maximum|Number of properties used by the environment for S1/S2 SKU and number of properties used by Warm Store for PAYG SKU|No Dimensions|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ingress Received Messages|Aantal|Totaal|Count of messages read from the event source|No Dimensions|
|IngressReceivedInvalidMessages|Ingress Received Invalid Messages|Aantal|Totaal|Count of invalid messages read from the event source|No Dimensions|
|IngressReceivedBytes|Ingress Received Bytes|Bytes|Totaal|Count of bytes read from the event source|No Dimensions|
|IngressStoredBytes|Ingress Stored Bytes|Bytes|Totaal|Total size of events successfully processed and available for query|No Dimensions|
|IngressStoredEvents|Ingress Stored Events|Aantal|Totaal|Count of flattened events successfully processed and available for query|No Dimensions|
|IngressReceivedMessagesTimeLag|Ingress Received Messages Time Lag|Seconden|Maximum|Difference between the time that the message is enqueued in the event source and the time it is processed in Ingress|No Dimensions|
|IngressReceivedMessagesCountLag|Ingress Received Messages Count Lag|Aantal|Average|Difference between the sequence number of last enqueued message in the event source partition and sequence number of message being processed in Ingress|No Dimensions|
|WarmStorageMaxProperties|Warm Storage Max Properties|Aantal|Maximum|Maximum number of properties used allowed by the environment for S1/S2 SKU and maximum number of properties allowed by Warm Store for PAYG SKU|No Dimensions|
|WarmStorageUsedProperties|Warm Storage Used Properties |Aantal|Maximum|Number of properties used by the environment for S1/S2 SKU and number of properties used by Warm Store for PAYG SKU|No Dimensions|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Disk Read Bytes/Sec|BytesPerSecond|Average|Average disk throughput due to read operations over the sample period.|No Dimensions|
|DiskWriteBytesPerSecond|Disk Write Bytes/Sec|BytesPerSecond|Average|Average disk throughput due to write operations over the sample period.|No Dimensions|
|Disk Read Bytes|Disk Read Bytes|Bytes|Totaal|Total disk throughput due to read operations over the sample period.|No Dimensions|
|Disk Write Bytes|Disk Write Bytes|Bytes|Totaal|Total disk throughput due to write operations over the sample period.|No Dimensions|
|DiskReadOperations|Disk Read Operations|Aantal|Totaal|The number of IO read operations in the previous sample period. Note that these operations may be variable sized.|No Dimensions|
|DiskWriteOperations|Disk Write Operations|Aantal|Totaal|The number of IO write operations in the previous sample period. Note that these operations may be variable sized.|No Dimensions|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Average|The average number of IO read operations in the previous sample period. Note that these operations may be variable sized.|No Dimensions|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Average|The average number of IO write operations in the previous sample period. Note that these operations may be variable sized.|No Dimensions|
|DiskReadLatency|Disk Read Latency|Milliseconds|Average|Total read latency. The sum of the device and kernel read latencies.|No Dimensions|
|DiskWriteLatency|Disk Write Latency|Milliseconds|Average|Total write latency. The sum of the device and kernel write latencies.|No Dimensions|
|NetworkInBytesPerSecond|Network In Bytes/Sec|BytesPerSecond|Average|Average network throughput for received traffic.|No Dimensions|
|NetworkOutBytesPerSecond|Network Out Bytes/Sec|BytesPerSecond|Average|Average network throughput for transmitted traffic.|No Dimensions|
|Netwerk in|Netwerk in|Bytes|Totaal|Total network throughput for received traffic.|No Dimensions|
|Netwerk uit|Netwerk uit|Bytes|Totaal|Total network throughput for transmitted traffic.|No Dimensions|
|MemoryUsed|Memory Used|Bytes|Average|The amount of machine memory that is in use by the VM.|No Dimensions|
|MemoryGranted|Memory Granted|Bytes|Average|The amount of memory that was granted to the VM by the host. Memory is not granted to the host until it is touched one time and granted memory may be swapped out or ballooned away if the VMkernel needs the memory.|No Dimensions|
|MemoryActive|Memory Active|Bytes|Average|The amount of memory used by the VM in the past small window of time. This is the "true" number of how much memory the VM currently has need of. Additional, unused memory may be swapped out or ballooned with no impact to the guest's performance.|No Dimensions|
|Percentage CPU|Percentage CPU|Procent|Average|The CPU utilization. This value is reported with 100% representing all processor cores on the system. As an example, a 2-way VM using 50% of a four-core system is completely using two cores.|No Dimensions|
|PercentageCpuReady|Percentage CPU Ready|Milliseconds|Totaal|Ready time is the time spend waiting for CPU(s) to become available in the past update interval.|No Dimensions|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CpuPercentage|CPU-percentage|Procent|Average|CPU-percentage|Instantie|
|MemoryPercentage|Memory Percentage|Procent|Average|Memory Percentage|Instantie|
|DiskQueueLength|Disk Queue Length|Aantal|Average|Disk Queue Length|Instantie|
|HttpQueueLength|Http Queue Length|Aantal|Average|Http Queue Length|Instantie|
|BytesReceived|Data In|Bytes|Totaal|Data In|Instantie|
|BytesSent|Data Out|Bytes|Totaal|Data Out|Instantie|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (excluding functions)

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CpuTime|CPU Time|Seconden|Totaal|CPU Time|Instantie|
|Aanvragen|Aanvragen|Aantal|Totaal|Aanvragen|Instantie|
|BytesReceived|Data In|Bytes|Totaal|Data In|Instantie|
|BytesSent|Data Out|Bytes|Totaal|Data Out|Instantie|
|Http101|Http 101|Aantal|Totaal|Http 101|Instantie|
|Http2xx|Http 2xx|Aantal|Totaal|Http 2xx|Instantie|
|Http3xx|Http 3xx|Aantal|Totaal|Http 3xx|Instantie|
|Http401|Http 401|Aantal|Totaal|Http 401|Instantie|
|Http403|Http 403|Aantal|Totaal|Http 403|Instantie|
|Http404|Http 404|Aantal|Totaal|Http 404|Instantie|
|Http406|Http 406|Aantal|Totaal|Http 406|Instantie|
|Http4xx|Http 4xx|Aantal|Totaal|Http 4xx|Instantie|
|Http5xx|Http Server Errors|Aantal|Totaal|Http Server Errors|Instantie|
|MemoryWorkingSet|Memory working set|Bytes|Average|Memory working set|Instantie|
|AverageMemoryWorkingSet|Average memory working set|Bytes|Average|Average memory working set|Instantie|
|AverageResponseTime|Average Response Time|Seconden|Average|Average Response Time|Instantie|
|AppConnections|Verbindingen|Aantal|Average|Verbindingen|Instantie|
|Handles|Handle Count|Aantal|Average|Handle Count|Instantie|
|Threads|Thread Count|Aantal|Average|Thread Count|Instantie|
|PrivateBytes|Private Bytes|Bytes|Average|Private Bytes|Instantie|
|IoReadBytesPerSecond|IO Read Bytes Per Second|BytesPerSecond|Totaal|IO Read Bytes Per Second|Instantie|
|IoWriteBytesPerSecond|IO Write Bytes Per Second|BytesPerSecond|Totaal|IO Write Bytes Per Second|Instantie|
|IoOtherBytesPerSecond|IO Other Bytes Per Second|BytesPerSecond|Totaal|IO Other Bytes Per Second|Instantie|
|IoReadOperationsPerSecond|IO Read Operations Per Second|BytesPerSecond|Totaal|IO Read Operations Per Second|Instantie|
|IoWriteOperationsPerSecond|IO Write Operations Per Second|BytesPerSecond|Totaal|IO Write Operations Per Second|Instantie|
|IoOtherOperationsPerSecond|IO Other Operations Per Second|BytesPerSecond|Totaal|IO Other Operations Per Second|Instantie|
|RequestsInApplicationQueue|Requests In Application Queue|Aantal|Average|Requests In Application Queue|Instantie|
|CurrentAssemblies|Current Assemblies|Aantal|Average|Current Assemblies|Instantie|
|TotalAppDomains|Total App Domains|Aantal|Average|Total App Domains|Instantie|
|TotalAppDomainsUnloaded|Total App Domains Unloaded|Aantal|Average|Total App Domains Unloaded|Instantie|
|Gen0Collections|Gen 0 Garbage Collections|Aantal|Totaal|Gen 0 Garbage Collections|Instantie|
|Gen1Collections|Gen 1 Garbage Collections|Aantal|Totaal|Gen 1 Garbage Collections|Instantie|
|Gen2Collections|Gen 2 Garbage Collections|Aantal|Totaal|Gen 2 Garbage Collections|Instantie|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (functions)

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BytesReceived|Data In|Bytes|Totaal|Data In|Instantie|
|BytesSent|Data Out|Bytes|Totaal|Data Out|Instantie|
|Http5xx|Http Server Errors|Aantal|Totaal|Http Server Errors|Instantie|
|MemoryWorkingSet|Memory working set|Bytes|Average|Memory working set|Instantie|
|AverageMemoryWorkingSet|Average memory working set|Bytes|Average|Average memory working set|Instantie|
|FunctionExecutionUnits|Function Execution Units|MB / Milliseconds|Totaal|[Function Execution Units](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instantie|
|FunctionExecutionCount|Function Execution Count|Aantal|Totaal|Function Execution Count|Instantie|
|PrivateBytes|Private Bytes|Bytes|Average|Private Bytes|Instantie|
|IoReadBytesPerSecond|IO Read Bytes Per Second|BytesPerSecond|Totaal|IO Read Bytes Per Second|Instantie|
|IoWriteBytesPerSecond|IO Write Bytes Per Second|BytesPerSecond|Totaal|IO Write Bytes Per Second|Instantie|
|IoOtherBytesPerSecond|IO Other Bytes Per Second|BytesPerSecond|Totaal|IO Other Bytes Per Second|Instantie|
|IoReadOperationsPerSecond|IO Read Operations Per Second|BytesPerSecond|Totaal|IO Read Operations Per Second|Instantie|
|IoWriteOperationsPerSecond|IO Write Operations Per Second|BytesPerSecond|Totaal|IO Write Operations Per Second|Instantie|
|IoOtherOperationsPerSecond|IO Other Operations Per Second|BytesPerSecond|Totaal|IO Other Operations Per Second|Instantie|
|RequestsInApplicationQueue|Requests In Application Queue|Aantal|Average|Requests In Application Queue|Instantie|
|CurrentAssemblies|Current Assemblies|Aantal|Average|Current Assemblies|Instantie|
|TotalAppDomains|Total App Domains|Aantal|Average|Total App Domains|Instantie|
|TotalAppDomainsUnloaded|Total App Domains Unloaded|Aantal|Average|Total App Domains Unloaded|Instantie|
|Gen0Collections|Gen 0 Garbage Collections|Aantal|Totaal|Gen 0 Garbage Collections|Instantie|
|Gen1Collections|Gen 1 Garbage Collections|Aantal|Totaal|Gen 1 Garbage Collections|Instantie|
|Gen2Collections|Gen 2 Garbage Collections|Aantal|Totaal|Gen 2 Garbage Collections|Instantie|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CpuTime|CPU Time|Seconden|Totaal|CPU Time|Instantie|
|Aanvragen|Aanvragen|Aantal|Totaal|Aanvragen|Instantie|
|BytesReceived|Data In|Bytes|Totaal|Data In|Instantie|
|BytesSent|Data Out|Bytes|Totaal|Data Out|Instantie|
|Http101|Http 101|Aantal|Totaal|Http 101|Instantie|
|Http2xx|Http 2xx|Aantal|Totaal|Http 2xx|Instantie|
|Http3xx|Http 3xx|Aantal|Totaal|Http 3xx|Instantie|
|Http401|Http 401|Aantal|Totaal|Http 401|Instantie|
|Http403|Http 403|Aantal|Totaal|Http 403|Instantie|
|Http404|Http 404|Aantal|Totaal|Http 404|Instantie|
|Http406|Http 406|Aantal|Totaal|Http 406|Instantie|
|Http4xx|Http 4xx|Aantal|Totaal|Http 4xx|Instantie|
|Http5xx|Http Server Errors|Aantal|Totaal|Http Server Errors|Instantie|
|MemoryWorkingSet|Memory working set|Bytes|Average|Memory working set|Instantie|
|AverageMemoryWorkingSet|Average memory working set|Bytes|Average|Average memory working set|Instantie|
|AverageResponseTime|Average Response Time|Seconden|Average|Average Response Time|Instantie|
|FunctionExecutionUnits|Function Execution Units|Aantal|Totaal|Function Execution Units|Instantie|
|FunctionExecutionCount|Function Execution Count|Aantal|Totaal|Function Execution Count|Instantie|
|AppConnections|Verbindingen|Aantal|Average|Verbindingen|Instantie|
|Handles|Handle Count|Aantal|Average|Handle Count|Instantie|
|Threads|Thread Count|Aantal|Average|Thread Count|Instantie|
|PrivateBytes|Private Bytes|Bytes|Average|Private Bytes|Instantie|
|IoReadBytesPerSecond|IO Read Bytes Per Second|BytesPerSecond|Totaal|IO Read Bytes Per Second|Instantie|
|IoWriteBytesPerSecond|IO Write Bytes Per Second|BytesPerSecond|Totaal|IO Write Bytes Per Second|Instantie|
|IoOtherBytesPerSecond|IO Other Bytes Per Second|BytesPerSecond|Totaal|IO Other Bytes Per Second|Instantie|
|IoReadOperationsPerSecond|IO Read Operations Per Second|BytesPerSecond|Totaal|IO Read Operations Per Second|Instantie|
|IoWriteOperationsPerSecond|IO Write Operations Per Second|BytesPerSecond|Totaal|IO Write Operations Per Second|Instantie|
|IoOtherOperationsPerSecond|IO Other Operations Per Second|BytesPerSecond|Totaal|IO Other Operations Per Second|Instantie|
|RequestsInApplicationQueue|Requests In Application Queue|Aantal|Average|Requests In Application Queue|Instantie|
|CurrentAssemblies|Current Assemblies|Aantal|Average|Current Assemblies|Instantie|
|TotalAppDomains|Total App Domains|Aantal|Average|Total App Domains|Instantie|
|TotalAppDomainsUnloaded|Total App Domains Unloaded|Aantal|Average|Total App Domains Unloaded|Instantie|
|Gen0Collections|Gen 0 Garbage Collections|Aantal|Totaal|Gen 0 Garbage Collections|Instantie|
|Gen1Collections|Gen 1 Garbage Collections|Aantal|Totaal|Gen 1 Garbage Collections|Instantie|
|Gen2Collections|Gen 2 Garbage Collections|Aantal|Totaal|Gen 2 Garbage Collections|Instantie|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Aanvragen|Aanvragen|Aantal|Totaal|Aanvragen|Instantie|
|BytesReceived|Data In|Bytes|Totaal|Data In|Instantie|
|BytesSent|Data Out|Bytes|Totaal|Data Out|Instantie|
|Http101|Http 101|Aantal|Totaal|Http 101|Instantie|
|Http2xx|Http 2xx|Aantal|Totaal|Http 2xx|Instantie|
|Http3xx|Http 3xx|Aantal|Totaal|Http 3xx|Instantie|
|Http401|Http 401|Aantal|Totaal|Http 401|Instantie|
|Http403|Http 403|Aantal|Totaal|Http 403|Instantie|
|Http404|Http 404|Aantal|Totaal|Http 404|Instantie|
|Http406|Http 406|Aantal|Totaal|Http 406|Instantie|
|Http4xx|Http 4xx|Aantal|Totaal|Http 4xx|Instantie|
|Http5xx|Http Server Errors|Aantal|Totaal|Http Server Errors|Instantie|
|AverageResponseTime|Average Response Time|Seconden|Average|Average Response Time|Instantie|
|CpuPercentage|CPU-percentage|Procent|Average|CPU-percentage|Instantie|
|MemoryPercentage|Memory Percentage|Procent|Average|Memory Percentage|Instantie|
|DiskQueueLength|Disk Queue Length|Aantal|Average|Disk Queue Length|Instantie|
|HttpQueueLength|Http Queue Length|Aantal|Average|Http Queue Length|Instantie|
|ActiveRequests|Active Requests|Aantal|Totaal|Active Requests|Instantie|
|TotalFrontEnds|Total Front Ends|Aantal|Average|Total Front Ends|No Dimensions|
|SmallAppServicePlanInstances|Small App Service Plan Workers|Aantal|Average|Small App Service Plan Workers|No Dimensions|
|MediumAppServicePlanInstances|Medium App Service Plan Workers|Aantal|Average|Medium App Service Plan Workers|No Dimensions|
|LargeAppServicePlanInstances|Large App Service Plan Workers|Aantal|Average|Large App Service Plan Workers|No Dimensions|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Gegevens|Metric Display Name|Eenheid|Aggregation Type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|WorkersTotal|Total Workers|Aantal|Average|Total Workers|No Dimensions|
|WorkersAvailable|Available Workers|Aantal|Average|Available Workers|No Dimensions|
|WorkersUsed|Used Workers|Aantal|Average|Used Workers|No Dimensions|
|CpuPercentage|CPU-percentage|Procent|Average|CPU-percentage|Instantie|
|MemoryPercentage|Memory Percentage|Procent|Average|Memory Percentage|Instantie|

## <a name="next-steps"></a>Volgende stappen
* [Read about metrics in Azure Monitor](data-platform.md)
* [Create alerts on metrics](alerts-overview.md)
* [Export metrics to storage, Event Hub, or Log Analytics](resource-logs-overview.md)
