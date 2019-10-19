---
title: Azure Monitor ondersteunde metrische gegevens per resource type
description: Een lijst met metrische gegevens die beschikbaar zijn voor elk resource type met Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/20/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: aab5d9a4cb7527e8a2085f826febc64bbd74854c
ms.sourcegitcommit: ae461c90cada1231f496bf442ee0c4dcdb6396bc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/17/2019
ms.locfileid: "72551963"
---
# <a name="supported-metrics-with-azure-monitor"></a>Ondersteunde metrische gegevens met Azure Monitor

Azure Monitor biedt verschillende manieren om te communiceren met metrische gegevens, zoals het maken van grafieken in de portal, het openen ervan via de REST API of het opvragen van query's via Power shell of CLI. Hieronder vindt u een volledige lijst met alle metrische gegevens die momenteel beschikbaar zijn met de metrische pijp lijn van de Azure Monitor. Andere metrische gegevens zijn mogelijk beschikbaar in de portal of met behulp van verouderde Api's. Deze lijst hieronder bevat alleen metrische gegevens die beschikbaar zijn via de geconsolideerde Azure Monitor metrische pijp lijn. Als u deze metrische gegevens wilt opvragen en openen, gebruikt u de [2018-01-01-API-versie](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> Het verzenden van multidimensionale metrische gegevens via diagnostische instellingen wordt momenteel niet ondersteund. Metrische gegevens met dimensies worden geëxporteerd als platte eendimensionale metrische gegevens, als totaal van alle dimensiewaarden.
>
> *Een voorbeeld*: de meetwaarde 'Binnenkomende berichten' voor een Event Hub kan worden verkend en uitgezet op wachtrijniveau. Wanneer de waarde wordt geëxporteerd via diagnostische instellingen, wordt deze echter voorgesteld als alle binnenkomende berichten voor alle wachtrijen in de Event Hub.
>
>

## <a name="microsoftanalysisservicesservers"></a>Micro soft. AnalysisServices/servers

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|qpu_metric|QPU|Aantal|Average|QPU. Bereik 0-100 voor S1, 0-200 voor S2 en 0-400 voor S4|ServerResourceType|
|memory_metric|Geheugen|Bytes|Average|Geheugenmetabase. Bereik 0-25 GB voor S1, 0-50 GB voor S2 en 0-100 GB voor S4|ServerResourceType|
|private_bytes_metric|Privé-bytes |Bytes|Average|De totale hoeveelheid geheugen die het Analysis Services engine proces en de mashup-container processen hebben toegewezen, exclusief geheugen gedeeld met andere processen.|ServerResourceType|
|virtual_bytes_metric|Virtuele bytes |Bytes|Average|De huidige grootte van de virtuele adres ruimte die Analysis Services engine proces en mashup-container processen gebruiken.|ServerResourceType|
|TotalConnectionRequests|Totaal aantal verbindings aanvragen|Aantal|Average|Totaal aantal verbindings aanvragen. Dit zijn ontvangsten.|ServerResourceType|
|SuccessfullConnectionsPerSec|Geslaagde verbindingen per seconde|CountPerSecond|Average|Snelheid van geslaagde verbindings voltooiingen.|ServerResourceType|
|TotalConnectionFailures|Totaal aantal verbindings fouten|Aantal|Average|Totaal aantal mislukte Verbindings pogingen.|ServerResourceType|
|CurrentUserSessions|Huidige gebruikers sessies|Aantal|Average|Het huidige aantal gebruikers sessies dat tot stand is gebracht.|ServerResourceType|
|QueryPoolBusyThreads|Query's pool bezette threads|Aantal|Average|Het aantal actieve threads in de query thread pool.|ServerResourceType|
|CommandPoolJobQueueLength|Wachtrij lengte van de opdracht pool taak|Aantal|Average|Aantal taken in de wachtrij van de opdracht thread pool.|ServerResourceType|
|ProcessingPoolJobQueueLength|Wachtrij lengte van de pool taak wordt verwerkt|Aantal|Average|Het aantal niet-I/O-taken in de wachtrij van de verwer kende thread pool.|ServerResourceType|
|CurrentConnections|Verbinding: huidige verbindingen|Aantal|Average|Het huidige aantal client verbindingen dat tot stand is gebracht.|ServerResourceType|
|CleanerCurrentPrice|Geheugen: huidige prijs opschonen|Aantal|Average|Huidige prijs van geheugen, $/byte/tijd), genormaliseerd naar 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Geheugen: verkleinbaar geheugen|Bytes|Average|De hoeveelheid geheugen (in bytes) die wordt verwijderd door de achtergrond opschoning.|ServerResourceType|
|CleanerMemoryNonshrinkable|Geheugen: Removal-geheugen kan niet worden verkleind|Bytes|Average|Hoeveelheid geheugen (in bytes) die niet wordt verwijderd door de achtergrond opschoning.|ServerResourceType|
|MemoryUsage|Geheugen: geheugen gebruik|Bytes|Average|Geheugen gebruik van het Server proces zoals gebruikt bij het berekenen van de prijs voor het schonen van geheugen. Gelijk aan Counter Process\PrivateBytes plus de grootte van gegevens die zijn toegewezen door het geheugen, waarbij elk geheugen wordt genegeerd dat is toegewezen aan of toegewezen door de xVelocity in-Memory Analytics Engine (VertiPaq) die de geheugen limiet van xVelocity-engine overschrijdt.|ServerResourceType|
|MemoryLimitHard|Geheugen: vaste geheugen limiet|Bytes|Average|Vaste geheugen limiet, van configuratie bestand.|ServerResourceType|
|MemoryLimitHigh|Geheugen: hoge geheugen limiet|Bytes|Average|Hoge geheugen limiet, van configuratie bestand.|ServerResourceType|
|MemoryLimitLow|Geheugen: lage geheugen limiet|Bytes|Average|Limiet voor weinig geheugen, van configuratie bestand.|ServerResourceType|
|MemoryLimitVertiPaq|Geheugen: VertiPaq-geheugen limiet|Bytes|Average|In-Memory limiet, van configuratie bestand.|ServerResourceType|
|Quota|Geheugen: quotum|Bytes|Average|Huidig geheugen quotum, in bytes. Geheugen quota wordt ook wel een geheugen toekenning of geheugen reservering genoemd.|ServerResourceType|
|QuotaBlocked|Geheugen: quotum geblokkeerd|Aantal|Average|Het huidige aantal quotum aanvragen dat is geblokkeerd totdat andere geheugen quota zijn vrijgemaakt.|ServerResourceType|
|VertiPaqNonpaged|Geheugen: VertiPaq niet-wisselbaar|Bytes|Average|Geheugen bytes vergrendeld in de werkset voor gebruik door de in-Memory engine.|ServerResourceType|
|VertiPaqPaged|Geheugen: VertiPaq-pagina|Bytes|Average|Bytes van wisselbaar geheugen die in gebruik zijn voor in-Memory gegevens.|ServerResourceType|
|RowsReadPerSec|Verwerken: gelezen rijen per seconde|CountPerSecond|Average|Het aantal rijen dat van alle relationele data bases is gelezen.|ServerResourceType|
|RowsConvertedPerSec|Verwerken: geconverteerde rijen per seconde|CountPerSecond|Average|Het aantal rijen dat tijdens de verwerking is geconverteerd.|ServerResourceType|
|RowsWrittenPerSec|Verwerken: geschreven rijen per seconde|CountPerSecond|Average|Het aantal rijen dat tijdens de verwerking is geschreven.|ServerResourceType|
|CommandPoolBusyThreads|Threads: actieve threads van opdracht pool|Aantal|Average|Het aantal actieve threads in de opdracht thread pool.|ServerResourceType|
|CommandPoolIdleThreads|Threads: niet-actieve threads van opdracht pool|Aantal|Average|Het aantal niet-actieve threads in de opdracht thread pool.|ServerResourceType|
|LongParsingBusyThreads|Threads: bezette threads voor lang parseren|Aantal|Average|Het aantal actieve threads in de thread pool voor lang parseren.|ServerResourceType|
|LongParsingIdleThreads|Threads: niet-actieve threads voor lang parseren|Aantal|Average|Het aantal niet-actieve threads in de thread pool voor lang parseren.|ServerResourceType|
|LongParsingJobQueueLength|Threads: lengte van taak wachtrij voor lang parseren|Aantal|Average|Aantal taken in de wachtrij van de thread pool voor lang parseren.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Threads: bezig met verwerken van I/O-taak threads van pool|Aantal|Average|Het aantal threads waarmee I/O-taken worden uitgevoerd in de verwer kende thread pool.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Threads: bezig met het verwerken van niet-I/O-threads van de groep|Aantal|Average|Het aantal threads met niet-I/O-taken in de verwer kende thread pool.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Threads: lengte van I/O-taak wachtrij voor verwerking van groep|Aantal|Average|Het aantal I/O-taken in de wachtrij van de verwer kende thread pool.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Threads: niet-actieve I/O-taak threads van de groep verwerken|Aantal|Average|Het aantal niet-actieve threads voor I/O-taken in de verwer kende thread pool.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Threads: niet-I/O-threads van de groep worden verwerkt|Aantal|Average|Het aantal niet-actieve threads in de verwer kende thread pool dat is gereserveerd voor niet-I/O-taken.|ServerResourceType|
|QueryPoolIdleThreads|Threads: niet-actieve threads van query pool|Aantal|Average|Het aantal niet-actieve threads voor I/O-taken in de verwer kende thread pool.|ServerResourceType|
|QueryPoolJobQueueLength|Threads: lengte van taak wachtrij voor query pool|Aantal|Average|Aantal taken in de wachtrij van de query thread pool.|ServerResourceType|
|ShortParsingBusyThreads|Threads: bezette threads voor kort parseren|Aantal|Average|Het aantal actieve threads in de thread pool voor kort parseren.|ServerResourceType|
|ShortParsingIdleThreads|Threads: niet-actieve threads voor kort parseren|Aantal|Average|Het aantal niet-actieve threads in de thread pool voor kort parseren.|ServerResourceType|
|ShortParsingJobQueueLength|Threads: lengte van taak wachtrij voor kort parseren|Aantal|Average|Aantal taken in de wachtrij van de thread pool voor kort parseren.|ServerResourceType|
|memory_thrashing_metric|Geheugen overbelasting|Procent|Average|Gemiddeld geheugen overbelasting.|ServerResourceType|
|mashup_engine_qpu_metric|M-engine QPU|Aantal|Average|QPU-gebruik door mashup-engine processen|ServerResourceType|
|mashup_engine_memory_metric|M-engine geheugen|Bytes|Average|Geheugen gebruik door mashup-engine processen|ServerResourceType|
|mashup_engine_private_bytes_metric|M-engine-eigen bytes |Bytes|Average|De totale hoeveelheid geheugen-mashup-container processen zijn toegewezen, exclusief geheugen gedeeld met andere processen.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|M-engine virtuele bytes |Bytes|Average|De huidige grootte van de mashup-container processen van de virtuele adres ruimte wordt gebruikt.|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Aanvragen|Aanvragen|Aantal|Totaal|Het totale aantal gateway aanvragen in een bepaalde periode. Het kan worden gesegmenteerd door verschillende dimensies om u te helpen bij het vaststellen van problemen. |Locatie, BackendResponseCode, LastErrorReason, GatewayResponseCode|
|TotalRequests|Totaal aantal gateway-aanvragen|Aantal|Totaal|Het totale aantal gateway aanvragen in een bepaalde periode. Deze metriek is afgeschaft, wij raden u aan de nieuwe `Requests` metriek te gebruiken. |Locatie, hostnaam|
|SuccessfulRequests|Geslaagde gateway-aanvragen|Aantal|Totaal|Het totaal aantal geslaagde gateway aanvragen in een bepaalde periode. Deze metriek is afgeschaft, wij raden u aan de nieuwe `Requests` metriek te gebruiken.|Locatie, hostnaam|
|UnauthorizedRequests|Niet-geautoriseerde gateway-aanvragen|Aantal|Totaal| Het totale aantal niet-geautoriseerde gateway aanvragen in een bepaalde periode. Deze metriek is afgeschaft, wij raden u aan de nieuwe `Requests` metriek te gebruiken.|Locatie, hostnaam|
|FailedRequests|Mislukte gateway-aanvragen|Aantal|Totaal|Het totale aantal mislukte gateway aanvragen in een bepaalde periode. Deze metriek is afgeschaft, wij raden u aan de nieuwe `Requests` metriek te gebruiken.|Locatie, hostnaam|
|OtherRequests|Andere gateway aanvragen|Aantal|Totaal|Het totale aantal gateway aanvragen in een bepaalde periode die niet in geslaagde, niet-geautoriseerde of mislukte categorieën vallen. Deze metriek is afgeschaft, wij raden u aan de nieuwe `Requests` metriek te gebruiken. |Locatie, hostnaam|
|Duur|Totale duur van gateway aanvragen|Milliseconden|Average|De tijd tussen het API Management van een aanvraag van een client en het retour neren van een reactie op de client.|Locatie, hostnaam|
|Capaciteit|Capaciteit|Procent|Average|De belasting indicator voor een API Management-exemplaar voor het nemen van gefundeerde beslissingen, ongeacht of u het exemplaar wilt schalen om meer belasting te bieden.|Locatie|
|EventHubTotalEvents|Totaal aantal EventHub-gebeurtenissen|Aantal|Totaal|Het totale aantal gebeurtenissen dat is verzonden naar EventHub van API Management in een bepaalde periode.|Locatie|
|EventHubSuccessfulEvents|Geslaagde EventHub-gebeurtenissen|Aantal|Totaal|Het totale aantal geslaagde EventHub-gebeurtenissen in een bepaalde periode.|Locatie|
|EventHubTotalFailedEvents|Mislukte EventHub-gebeurtenissen|Aantal|Totaal|Het totale aantal mislukte EventHub-gebeurtenissen in een bepaalde periode.|Locatie|
|EventHubRejectedEvents|Geweigerde EventHub-gebeurtenissen|Aantal|Totaal|Het totale aantal geweigerde EventHub-gebeurtenissen (onjuiste configuratie of niet-geautoriseerd) in een bepaalde periode.|Locatie|
|EventHubThrottledEvents|Vertraagde EventHub-gebeurtenissen|Aantal|Totaal|Het totale aantal vertraagde EventHub-gebeurtenissen in een bepaalde periode.|Locatie|
|EventHubTimedoutEvents|Time-out EventHub-gebeurtenissen|Aantal|Totaal|Het totale aantal time-out EventHub-gebeurtenissen in een bepaalde periode.|Locatie|
|EventHubDroppedEvents|Verwijderde EventHub-gebeurtenissen|Aantal|Totaal|Het totale aantal gebeurtenissen dat is overgeslagen omdat de maximale grootte van de wachtrij is bereikt in een bepaalde periode.|Locatie|
|EventHubTotalBytesSent|Grootte van EventHub-gebeurtenissen|Bytes|Totaal|De totale grootte van EventHub-gebeurtenissen in bytes in een bepaalde periode.|Locatie|


## <a name="microsoftautomationautomationaccounts"></a>Micro soft. Automation/automationAccounts

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TotalJob|Totaal aantal taken|Aantal|Totaal|Het totale aantal taken|Runbook, status|
|TotalUpdateDeploymentRuns|Totaal aantal uitgevoerde update-implementaties|Aantal|Totaal|Totale aantal uitgevoerde software-update-implementaties|SoftwareUpdateConfigurationName, status|
|TotalUpdateDeploymentMachineRuns|Totaal aantal uitgevoerde update-implementatie computers|Aantal|Totaal|Het totale aantal uitgevoerde software-update-implementatie computers in een software-update-implementatie|SoftwareUpdateConfigurationName, status, target computer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Micro soft. batch/batchAccounts

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CoreCount|Aantal toegewezen kernen|Aantal|Totaal|Totaal aantal toegewezen kernen in het batch-account|Geen dimensies|
|TotalNodeCount|Aantal toegewezen knoop punten|Aantal|Totaal|Totaal aantal toegewezen knoop punten in het batch-account|Geen dimensies|
|LowPriorityCoreCount|Aantal LowPriority kernen|Aantal|Totaal|Totaal aantal kernen met lage prioriteit in het batch-account|Geen dimensies|
|TotalLowPriorityNodeCount|Aantal knoop punten met een lage prioriteit|Aantal|Totaal|Totaal aantal knoop punten met een lage prioriteit in het batch-account|Geen dimensies|
|CreatingNodeCount|Aantal knoop punten maken|Aantal|Totaal|Aantal knoop punten dat wordt gemaakt|Geen dimensies|
|StartingNodeCount|Begin aantal knoop punten|Aantal|Totaal|Aantal knoop punten dat begint|Geen dimensies|
|WaitingForStartTaskNodeCount|Wachten op aantal begin taak knooppunten|Aantal|Totaal|Aantal knoop punten dat wacht tot de begin taak is voltooid|Geen dimensies|
|StartTaskFailedNodeCount|Aantal mislukte knoop punten van begin taak|Aantal|Totaal|Aantal knoop punten waarop de begin taak is mislukt|Geen dimensies|
|IdleNodeCount|Aantal niet-actieve knoop punten|Aantal|Totaal|Aantal niet-actieve knoop punten|Geen dimensies|
|OfflineNodeCount|Aantal offline knooppunten|Aantal|Totaal|Aantal offline knooppunten|Geen dimensies|
|RebootingNodeCount|Aantal knoop punten opnieuw opstarten|Aantal|Totaal|Aantal knoop punten dat opnieuw wordt opgestart|Geen dimensies|
|ReimagingNodeCount|Telling van het aantal knoop punten|Aantal|Totaal|Aantal knoop punten van installatie kopieën|Geen dimensies|
|RunningNodeCount|Aantal actieve knoop punten|Aantal|Totaal|Aantal actieve knoop punten|Geen dimensies|
|LeavingPoolNodeCount|Aantal groeps knooppunten verlaten|Aantal|Totaal|Aantal knoop punten dat de pool verlaat|Geen dimensies|
|UnusableNodeCount|Aantal niet-bruikbare knoop punten|Aantal|Totaal|Aantal niet-bruikbare knoop punten|Geen dimensies|
|PreemptedNodeCount|Aantal knoop punten in herhaling|Aantal|Totaal|Aantal knoop punten dat is afgebroken|Geen dimensies|
|TaskStartEvent|Taak begin gebeurtenissen|Aantal|Totaal|Totaal aantal taken dat is gestart|Geen dimensies|
|TaskCompleteEvent|Taak voltooid gebeurtenissen|Aantal|Totaal|Totaal aantal taken dat is voltooid|Geen dimensies|
|TaskFailEvent|Taak fout gebeurtenissen|Aantal|Totaal|Totaal aantal taken dat is voltooid met de status mislukt|Geen dimensies|
|PoolCreateEvent|Groeps gebeurtenissen maken|Aantal|Totaal|Totaal aantal Pools dat is gemaakt|Geen dimensies|
|PoolResizeStartEvent|Begin gebeurtenissen van groeps grootte wijzigen|Aantal|Totaal|Totaal aantal hergroottes van de groep die zijn gestart|Geen dimensies|
|PoolResizeCompleteEvent|Volledige gebeurtenissen voor het wijzigen van de pool|Aantal|Totaal|Totaal aantal hergroottes van de groep die zijn voltooid|Geen dimensies|
|PoolDeleteStartEvent|Begin gebeurtenissen groep verwijderen|Aantal|Totaal|Totaal aantal verwijderde groepen dat is gestart|Geen dimensies|
|PoolDeleteCompleteEvent|Voltooide gebeurtenissen van groep verwijderen|Aantal|Totaal|Totaal aantal verwijderde groepen dat is voltooid|Geen dimensies|
|JobDeleteCompleteEvent|Voltooide gebeurtenissen van taak verwijderen|Aantal|Totaal|Het totale aantal taken dat is verwijderd.|Geen dimensies|
|JobDeleteStartEvent|Taak begin gebeurtenissen verwijderen|Aantal|Totaal|Het totale aantal taken dat is aangevraagd om te worden verwijderd.|Geen dimensies|
|JobDisableCompleteEvent|Voltooide gebeurtenissen voor taak uitschakelen|Aantal|Totaal|Het totale aantal taken dat is uitgeschakeld.|Geen dimensies|
|JobDisableStartEvent|Taak start gebeurtenissen uitschakelen|Aantal|Totaal|Het totale aantal taken dat is aangevraagd om te worden uitgeschakeld.|Geen dimensies|
|JobStartEvent|Taak begin gebeurtenissen|Aantal|Totaal|Het totale aantal taken dat is gestart.|Geen dimensies|
|JobTerminateCompleteEvent|Voltooide gebeurtenissen voor taak beëindigen|Aantal|Totaal|Het totale aantal taken dat is beëindigd.|Geen dimensies|
|JobTerminateStartEvent|Taak start gebeurtenissen beëindigen|Aantal|Totaal|Het totale aantal taken dat is aangevraagd om te worden beëindigd.|Geen dimensies|

## <a name="microsoftcacheredis"></a>Micro soft. cache/redis

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|connectedclients|Verbonden clients|Aantal|Maximum||ShardId|
|totalcommandsprocessed|Totaal aantal bewerkingen|Aantal|Totaal||ShardId|
|cachehits|Cache treffers|Aantal|Totaal||ShardId|
|cachemisses|Cache missers|Aantal|Totaal||ShardId|
|getcommands|Methode|Aantal|Totaal||ShardId|
|setcommands|Groepen|Aantal|Totaal||ShardId|
|operationsPerSecond|Bewerkingen per seconde|Aantal|Maximum||ShardId|
|evictedkeys|Verwijderde sleutels|Aantal|Totaal||ShardId|
|totalkeys|Totaal aantal sleutels|Aantal|Maximum||ShardId|
|expiredkeys|Verlopen sleutels|Aantal|Totaal||ShardId|
|usedmemory|Gebruikt geheugen|Bytes|Maximum||ShardId|
|usedmemorypercentage|Percentage gebruikt geheugen|Procent|Maximum||ShardId|
|usedmemoryRss|Gebruikte geheugen-RSS|Bytes|Maximum||ShardId|
|serverLoad|Server belasting|Procent|Maximum||ShardId|
|cacheWrite|Cache schrijven|BytesPerSecond|Maximum||ShardId|
|cacheRead|Lees bewerking in cache|BytesPerSecond|Maximum||ShardId|
|percentProcessorTime|CPU|Procent|Maximum||ShardId|
|cacheLatency|Cache latentie micro seconden (preview-versie)|Aantal|Average||ShardId, SampleType|
|fouten|Fouten|Aantal|Maximum||ShardId, error type|
|connectedclients0|Verbonden clients (Shard 0)|Aantal|Maximum||Geen dimensies|
|totalcommandsprocessed0|Totaal aantal bewerkingen (Shard 0)|Aantal|Totaal||Geen dimensies|
|cachehits0|Cache treffers (Shard 0)|Aantal|Totaal||Geen dimensies|
|cachemisses0|Cache missers (Shard 0)|Aantal|Totaal||Geen dimensies|
|getcommands0|Hiermee wordt opgehaald (Shard 0)|Aantal|Totaal||Geen dimensies|
|setcommands0|Sets (Shard 0)|Aantal|Totaal||Geen dimensies|
|operationsPerSecond0|Bewerkingen per seconde (Shard 0)|Aantal|Maximum||Geen dimensies|
|evictedkeys0|Verwijderde sleutels (Shard 0)|Aantal|Totaal||Geen dimensies|
|totalkeys0|Totaal aantal sleutels (Shard 0)|Aantal|Maximum||Geen dimensies|
|expiredkeys0|Verlopen sleutels (Shard 0)|Aantal|Totaal||Geen dimensies|
|usedmemory0|Gebruikt geheugen (Shard 0)|Bytes|Maximum||Geen dimensies|
|usedmemoryRss0|Gebruikt geheugen RSS (Shard 0)|Bytes|Maximum||Geen dimensies|
|serverLoad0|Server belasting (Shard 0)|Procent|Maximum||Geen dimensies|
|cacheWrite0|Cache schrijven (Shard 0)|BytesPerSecond|Maximum||Geen dimensies|
|cacheRead0|Cache gelezen (Shard 0)|BytesPerSecond|Maximum||Geen dimensies|
|percentProcessorTime0|CPU (Shard 0)|Procent|Maximum||Geen dimensies|
|connectedclients1|Verbonden clients (Shard 1)|Aantal|Maximum||Geen dimensies|
|totalcommandsprocessed1|Totaal aantal bewerkingen (Shard 1)|Aantal|Totaal||Geen dimensies|
|cachehits1|Cache treffers (Shard 1)|Aantal|Totaal||Geen dimensies|
|cachemisses1|Cache missers (Shard 1)|Aantal|Totaal||Geen dimensies|
|getcommands1|Hiermee wordt opgehaald (Shard 1)|Aantal|Totaal||Geen dimensies|
|setcommands1|Sets (Shard 1)|Aantal|Totaal||Geen dimensies|
|operationsPerSecond1|Bewerkingen per seconde (Shard 1)|Aantal|Maximum||Geen dimensies|
|evictedkeys1|Verwijderde sleutels (Shard 1)|Aantal|Totaal||Geen dimensies|
|totalkeys1|Totaal aantal sleutels (Shard 1)|Aantal|Maximum||Geen dimensies|
|expiredkeys1|Verlopen sleutels (Shard 1)|Aantal|Totaal||Geen dimensies|
|usedmemory1|Gebruikt geheugen (Shard 1)|Bytes|Maximum||Geen dimensies|
|usedmemoryRss1|Gebruikte geheugen RSS (Shard 1)|Bytes|Maximum||Geen dimensies|
|serverLoad1|Server belasting (Shard 1)|Procent|Maximum||Geen dimensies|
|cacheWrite1|Cache schrijven (Shard 1)|BytesPerSecond|Maximum||Geen dimensies|
|cacheRead1|Lees bewerking in cache (Shard 1)|BytesPerSecond|Maximum||Geen dimensies|
|percentProcessorTime1|CPU (Shard 1)|Procent|Maximum||Geen dimensies|
|connectedclients2|Verbonden clients (Shard 2)|Aantal|Maximum||Geen dimensies|
|totalcommandsprocessed2|Totaal aantal bewerkingen (Shard 2)|Aantal|Totaal||Geen dimensies|
|cachehits2|Cache treffers (Shard 2)|Aantal|Totaal||Geen dimensies|
|cachemisses2|Cache missers (Shard 2)|Aantal|Totaal||Geen dimensies|
|getcommands2|Hiermee wordt opgehaald (Shard 2)|Aantal|Totaal||Geen dimensies|
|setcommands2|Sets (Shard 2)|Aantal|Totaal||Geen dimensies|
|operationsPerSecond2|Bewerkingen per seconde (Shard 2)|Aantal|Maximum||Geen dimensies|
|evictedkeys2|Verwijderde sleutels (Shard 2)|Aantal|Totaal||Geen dimensies|
|totalkeys2|Totaal aantal sleutels (Shard 2)|Aantal|Maximum||Geen dimensies|
|expiredkeys2|Verlopen sleutels (Shard 2)|Aantal|Totaal||Geen dimensies|
|usedmemory2|Gebruikt geheugen (Shard 2)|Bytes|Maximum||Geen dimensies|
|usedmemoryRss2|Gebruikte geheugen RSS (Shard 2)|Bytes|Maximum||Geen dimensies|
|serverLoad2|Server belasting (Shard 2)|Procent|Maximum||Geen dimensies|
|cacheWrite2|Cache schrijven (Shard 2)|BytesPerSecond|Maximum||Geen dimensies|
|cacheRead2|Lees bewerking in cache (Shard 2)|BytesPerSecond|Maximum||Geen dimensies|
|percentProcessorTime2|CPU (Shard 2)|Procent|Maximum||Geen dimensies|
|connectedclients3|Verbonden clients (Shard 3)|Aantal|Maximum||Geen dimensies|
|totalcommandsprocessed3|Totaal aantal bewerkingen (Shard 3)|Aantal|Totaal||Geen dimensies|
|cachehits3|Cache treffers (Shard 3)|Aantal|Totaal||Geen dimensies|
|cachemisses3|Cache missers (Shard 3)|Aantal|Totaal||Geen dimensies|
|getcommands3|Hiermee wordt opgehaald (Shard 3)|Aantal|Totaal||Geen dimensies|
|setcommands3|Sets (Shard 3)|Aantal|Totaal||Geen dimensies|
|operationsPerSecond3|Bewerkingen per seconde (Shard 3)|Aantal|Maximum||Geen dimensies|
|evictedkeys3|Verwijderde sleutels (Shard 3)|Aantal|Totaal||Geen dimensies|
|totalkeys3|Totaal aantal sleutels (Shard 3)|Aantal|Maximum||Geen dimensies|
|expiredkeys3|Verlopen sleutels (Shard 3)|Aantal|Totaal||Geen dimensies|
|usedmemory3|Gebruikt geheugen (Shard 3)|Bytes|Maximum||Geen dimensies|
|usedmemoryRss3|Gebruikte geheugen RSS (Shard 3)|Bytes|Maximum||Geen dimensies|
|serverLoad3|Server belasting (Shard 3)|Procent|Maximum||Geen dimensies|
|cacheWrite3|Cache schrijven (Shard 3)|BytesPerSecond|Maximum||Geen dimensies|
|cacheRead3|Lees bewerking in cache (Shard 3)|BytesPerSecond|Maximum||Geen dimensies|
|percentProcessorTime3|CPU (Shard 3)|Procent|Maximum||Geen dimensies|
|connectedclients4|Verbonden clients (Shard 4)|Aantal|Maximum||Geen dimensies|
|totalcommandsprocessed4|Totaal aantal bewerkingen (Shard 4)|Aantal|Totaal||Geen dimensies|
|cachehits4|Cache treffers (Shard 4)|Aantal|Totaal||Geen dimensies|
|cachemisses4|Cache missers (Shard 4)|Aantal|Totaal||Geen dimensies|
|getcommands4|Hiermee wordt opgehaald (Shard 4)|Aantal|Totaal||Geen dimensies|
|setcommands4|Sets (Shard 4)|Aantal|Totaal||Geen dimensies|
|operationsPerSecond4|Bewerkingen per seconde (Shard 4)|Aantal|Maximum||Geen dimensies|
|evictedkeys4|Verwijderde sleutels (Shard 4)|Aantal|Totaal||Geen dimensies|
|totalkeys4|Totaal aantal sleutels (Shard 4)|Aantal|Maximum||Geen dimensies|
|expiredkeys4|Verlopen sleutels (Shard 4)|Aantal|Totaal||Geen dimensies|
|usedmemory4|Gebruikt geheugen (Shard 4)|Bytes|Maximum||Geen dimensies|
|usedmemoryRss4|Gebruikte geheugen RSS (Shard 4)|Bytes|Maximum||Geen dimensies|
|serverLoad4|Server belasting (Shard 4)|Procent|Maximum||Geen dimensies|
|cacheWrite4|Cache schrijven (Shard 4)|BytesPerSecond|Maximum||Geen dimensies|
|cacheRead4|Lees bewerking in cache (Shard 4)|BytesPerSecond|Maximum||Geen dimensies|
|percentProcessorTime4|CPU (Shard 4)|Procent|Maximum||Geen dimensies|
|connectedclients5|Verbonden clients (Shard 5)|Aantal|Maximum||Geen dimensies|
|totalcommandsprocessed5|Totaal aantal bewerkingen (Shard 5)|Aantal|Totaal||Geen dimensies|
|cachehits5|Cache treffers (Shard 5)|Aantal|Totaal||Geen dimensies|
|cachemisses5|Cache missers (Shard 5)|Aantal|Totaal||Geen dimensies|
|getcommands5|Hiermee wordt opgehaald (Shard 5)|Aantal|Totaal||Geen dimensies|
|setcommands5|Sets (Shard 5)|Aantal|Totaal||Geen dimensies|
|operationsPerSecond5|Bewerkingen per seconde (Shard 5)|Aantal|Maximum||Geen dimensies|
|evictedkeys5|Verwijderde sleutels (Shard 5)|Aantal|Totaal||Geen dimensies|
|totalkeys5|Totaal aantal sleutels (Shard 5)|Aantal|Maximum||Geen dimensies|
|expiredkeys5|Verlopen sleutels (Shard 5)|Aantal|Totaal||Geen dimensies|
|usedmemory5|Gebruikt geheugen (Shard 5)|Bytes|Maximum||Geen dimensies|
|usedmemoryRss5|Gebruikte geheugen RSS (Shard 5)|Bytes|Maximum||Geen dimensies|
|serverLoad5|Server belasting (Shard 5)|Procent|Maximum||Geen dimensies|
|cacheWrite5|Cache schrijven (Shard 5)|BytesPerSecond|Maximum||Geen dimensies|
|cacheRead5|Lees bewerking in cache (Shard 5)|BytesPerSecond|Maximum||Geen dimensies|
|percentProcessorTime5|CPU (Shard 5)|Procent|Maximum||Geen dimensies|
|connectedclients6|Verbonden clients (Shard 6)|Aantal|Maximum||Geen dimensies|
|totalcommandsprocessed6|Totaal aantal bewerkingen (Shard 6)|Aantal|Totaal||Geen dimensies|
|cachehits6|Cache treffers (Shard 6)|Aantal|Totaal||Geen dimensies|
|cachemisses6|Cache missers (Shard 6)|Aantal|Totaal||Geen dimensies|
|getcommands6|Hiermee wordt opgehaald (Shard 6)|Aantal|Totaal||Geen dimensies|
|setcommands6|Sets (Shard 6)|Aantal|Totaal||Geen dimensies|
|operationsPerSecond6|Bewerkingen per seconde (Shard 6)|Aantal|Maximum||Geen dimensies|
|evictedkeys6|Verwijderde sleutels (Shard 6)|Aantal|Totaal||Geen dimensies|
|totalkeys6|Totaal aantal sleutels (Shard 6)|Aantal|Maximum||Geen dimensies|
|expiredkeys6|Verlopen sleutels (Shard 6)|Aantal|Totaal||Geen dimensies|
|usedmemory6|Gebruikt geheugen (Shard 6)|Bytes|Maximum||Geen dimensies|
|usedmemoryRss6|Gebruikte geheugen RSS (Shard 6)|Bytes|Maximum||Geen dimensies|
|serverLoad6|Server belasting (Shard 6)|Procent|Maximum||Geen dimensies|
|cacheWrite6|Cache schrijven (Shard 6)|BytesPerSecond|Maximum||Geen dimensies|
|cacheRead6|Lees bewerking in cache (Shard 6)|BytesPerSecond|Maximum||Geen dimensies|
|percentProcessorTime6|CPU (Shard 6)|Procent|Maximum||Geen dimensies|
|connectedclients7|Verbonden clients (Shard 7)|Aantal|Maximum||Geen dimensies|
|totalcommandsprocessed7|Totaal aantal bewerkingen (Shard 7)|Aantal|Totaal||Geen dimensies|
|cachehits7|Cache treffers (Shard 7)|Aantal|Totaal||Geen dimensies|
|cachemisses7|Cache missers (Shard 7)|Aantal|Totaal||Geen dimensies|
|getcommands7|Hiermee wordt opgehaald (Shard 7)|Aantal|Totaal||Geen dimensies|
|setcommands7|Sets (Shard 7)|Aantal|Totaal||Geen dimensies|
|operationsPerSecond7|Bewerkingen per seconde (Shard 7)|Aantal|Maximum||Geen dimensies|
|evictedkeys7|Verwijderde sleutels (Shard 7)|Aantal|Totaal||Geen dimensies|
|totalkeys7|Totaal aantal sleutels (Shard 7)|Aantal|Maximum||Geen dimensies|
|expiredkeys7|Verlopen sleutels (Shard 7)|Aantal|Totaal||Geen dimensies|
|usedmemory7|Gebruikt geheugen (Shard 7)|Bytes|Maximum||Geen dimensies|
|usedmemoryRss7|Gebruikte geheugen RSS (Shard 7)|Bytes|Maximum||Geen dimensies|
|serverLoad7|Server belasting (Shard 7)|Procent|Maximum||Geen dimensies|
|cacheWrite7|Cache schrijven (Shard 7)|BytesPerSecond|Maximum||Geen dimensies|
|cacheRead7|Lees bewerking in cache (Shard 7)|BytesPerSecond|Maximum||Geen dimensies|
|percentProcessorTime7|CPU (Shard 7)|Procent|Maximum||Geen dimensies|
|connectedclients8|Verbonden clients (Shard 8)|Aantal|Maximum||Geen dimensies|
|totalcommandsprocessed8|Totaal aantal bewerkingen (Shard 8)|Aantal|Totaal||Geen dimensies|
|cachehits8|Cache treffers (Shard 8)|Aantal|Totaal||Geen dimensies|
|cachemisses8|Cache missers (Shard 8)|Aantal|Totaal||Geen dimensies|
|getcommands8|Hiermee wordt opgehaald (Shard 8)|Aantal|Totaal||Geen dimensies|
|setcommands8|Sets (Shard 8)|Aantal|Totaal||Geen dimensies|
|operationsPerSecond8|Bewerkingen per seconde (Shard 8)|Aantal|Maximum||Geen dimensies|
|evictedkeys8|Verwijderde sleutels (Shard 8)|Aantal|Totaal||Geen dimensies|
|totalkeys8|Totaal aantal sleutels (Shard 8)|Aantal|Maximum||Geen dimensies|
|expiredkeys8|Verlopen sleutels (Shard 8)|Aantal|Totaal||Geen dimensies|
|usedmemory8|Gebruikt geheugen (Shard 8)|Bytes|Maximum||Geen dimensies|
|usedmemoryRss8|Gebruikt geheugen RSS (Shard 8)|Bytes|Maximum||Geen dimensies|
|serverLoad8|Server belasting (Shard 8)|Procent|Maximum||Geen dimensies|
|cacheWrite8|Cache schrijven (Shard 8)|BytesPerSecond|Maximum||Geen dimensies|
|cacheRead8|Lees bewerking in cache (Shard 8)|BytesPerSecond|Maximum||Geen dimensies|
|percentProcessorTime8|CPU (Shard 8)|Procent|Maximum||Geen dimensies|
|connectedclients9|Verbonden clients (Shard 9)|Aantal|Maximum||Geen dimensies|
|totalcommandsprocessed9|Totaal aantal bewerkingen (Shard 9)|Aantal|Totaal||Geen dimensies|
|cachehits9|Cache treffers (Shard 9)|Aantal|Totaal||Geen dimensies|
|cachemisses9|Cache missers (Shard 9)|Aantal|Totaal||Geen dimensies|
|getcommands9|Hiermee wordt opgehaald (Shard 9)|Aantal|Totaal||Geen dimensies|
|setcommands9|Sets (Shard 9)|Aantal|Totaal||Geen dimensies|
|operationsPerSecond9|Bewerkingen per seconde (Shard 9)|Aantal|Maximum||Geen dimensies|
|evictedkeys9|Verwijderde sleutels (Shard 9)|Aantal|Totaal||Geen dimensies|
|totalkeys9|Totaal aantal sleutels (Shard 9)|Aantal|Maximum||Geen dimensies|
|expiredkeys9|Verlopen sleutels (Shard 9)|Aantal|Totaal||Geen dimensies|
|usedmemory9|Gebruikt geheugen (Shard 9)|Bytes|Maximum||Geen dimensies|
|usedmemoryRss9|Gebruikte geheugen RSS (Shard 9)|Bytes|Maximum||Geen dimensies|
|serverLoad9|Server belasting (Shard 9)|Procent|Maximum||Geen dimensies|
|cacheWrite9|Cache schrijven (Shard 9)|BytesPerSecond|Maximum||Geen dimensies|
|cacheRead9|Lees bewerking in cache (Shard 9)|BytesPerSecond|Maximum||Geen dimensies|
|percentProcessorTime9|CPU (Shard 9)|Procent|Maximum||Geen dimensies|

## <a name="microsoftclassiccomputevirtualmachines"></a>Micro soft. ClassicCompute/informatie

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Procent|Average|Het percentage toegewezen reken eenheden dat momenteel wordt gebruikt door de virtuele machine (s).|Geen dimensies|
|Netwerk in|Netwerk in|Bytes|Totaal|Het aantal bytes dat is ontvangen op alle netwerk interfaces door de virtuele machine (s) (binnenkomend verkeer).|Geen dimensies|
|Netwerk uit|Netwerk uit|Bytes|Totaal|Het aantal uitgaande bytes op alle netwerk interfaces door de virtuele machine (s) (uitgaand verkeer).|Geen dimensies|
|Gelezen bytes per seconde|Schijf lezen|BytesPerSecond|Average|Gemiddeld aantal gelezen bytes van de schijf tijdens de controle periode.|Geen dimensies|
|Geschreven bytes per seconde|Schijf schrijven|BytesPerSecond|Average|Gemiddeld aantal bytes dat tijdens de controle periode naar de schijf wordt geschreven.|Geen dimensies|
|Lees bewerkingen op de schijf per seconde|Lees bewerkingen op de schijf per seconde|CountPerSecond|Average|Read-IOPS van schijf.|Geen dimensies|
|Schrijf bewerkingen op de schijf per seconde|Schrijf bewerkingen op de schijf per seconde|CountPerSecond|Average|Schijf schrijf-IOPS.|Geen dimensies|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Micro soft. ClassicCompute/domein naam/sleuven/rollen

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Procent|Average|Het percentage toegewezen reken eenheden dat momenteel wordt gebruikt door de virtuele machine (s).|RoleInstanceId|
|Netwerk in|Netwerk in|Bytes|Totaal|Het aantal bytes dat is ontvangen op alle netwerk interfaces door de virtuele machine (s) (binnenkomend verkeer).|RoleInstanceId|
|Netwerk uit|Netwerk uit|Bytes|Totaal|Het aantal uitgaande bytes op alle netwerk interfaces door de virtuele machine (s) (uitgaand verkeer).|RoleInstanceId|
|Gelezen bytes per seconde|Schijf lezen|BytesPerSecond|Average|Gemiddeld aantal gelezen bytes van de schijf tijdens de controle periode.|RoleInstanceId|
|Geschreven bytes per seconde|Schijf schrijven|BytesPerSecond|Average|Gemiddeld aantal bytes dat tijdens de controle periode naar de schijf wordt geschreven.|RoleInstanceId|
|Lees bewerkingen op de schijf per seconde|Lees bewerkingen op de schijf per seconde|CountPerSecond|Average|Read-IOPS van schijf.|RoleInstanceId|
|Schrijf bewerkingen op de schijf per seconde|Schrijf bewerkingen op de schijf per seconde|CountPerSecond|Average|Schijf schrijf-IOPS.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Micro soft. CognitiveServices/accounts

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TotalCalls|Totaal aantal aanroepen|Aantal|Totaal|Totaal aantal aanroepen.|ApiName, Operationname, regio|
|SuccessfulCalls|Geslaagde aanroepen|Aantal|Totaal|Aantal geslaagde aanroepen.|ApiName, Operationname, regio|
|TotalErrors|Totaal aantal fouten|Aantal|Totaal|Totaal aantal aanroepen met een fout respons (HTTP-antwoord code 4xx of 5xx).|ApiName, Operationname, regio|
|BlockedCalls|Geblokkeerde aanroepen|Aantal|Totaal|Aantal aanroepen dat de frequentie of quotum limiet heeft overschreden.|ApiName, Operationname, regio|
|ServerErrors|Server fouten|Aantal|Totaal|Het aantal aanroepen met een interne fout in de service (HTTP-antwoord code 5xx).|ApiName, Operationname, regio|
|ClientErrors|Client fouten|Aantal|Totaal|Het aantal aanroepen met een fout aan de client zijde (HTTP-antwoord code 4xx).|ApiName, Operationname, regio|
|DataIn|Gegevens in|Bytes|Totaal|Grootte van binnenkomende gegevens in bytes.|ApiName, Operationname, regio|
|DataOut|Gegevens uit|Bytes|Totaal|Grootte van uitgaande gegevens in bytes.|ApiName, Operationname, regio|
|Latentie|Latentie|Milliseconden|Average|Latentie in milliseconden.|ApiName, Operationname, regio|
|CharactersTranslated|Geconverteerde tekens|Aantal|Totaal|Totaal aantal tekens in binnenkomende-tekst aanvraag.|ApiName, Operationname, regio|
|CharactersTrained|Getrainde tekens|Aantal|Totaal|Totaal aantal getrainde tekens.|ApiName, Operationname, regio|
|SpeechSessionDuration|Spraak sessie duur|Seconden|Totaal|Totale duur van de spraak sessie in seconden.|ApiName, Operationname, regio|
|TotalTransactions|Totaal aantal trans acties|Aantal|Totaal|Totaal aantal trans acties.|Geen dimensies|
|TotalTokenCalls|Totaal aantal token aanroepen|Aantal|Totaal|Totaal aantal token aanroepen.|ApiName, Operationname, regio|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Procent|Average|Het percentage toegewezen reken eenheden dat momenteel wordt gebruikt door de virtuele machine (s)|Geen dimensies|
|Netwerk in|In factureer bare netwerk|Bytes|Totaal|Het aantal factureer bare bytes dat is ontvangen op alle netwerk interfaces door de virtuele machine (s) (binnenkomend verkeer)|Geen dimensies|
|Netwerk uit|Onfactureerbaar netwerk|Bytes|Totaal|Het aantal factureer bare bytes op alle netwerk interfaces door de virtuele machine (s) (uitgaand verkeer)|Geen dimensies|
|Gelezen bytes op de schijf|Gelezen bytes op de schijf|Bytes|Totaal|Gelezen bytes van de schijf tijdens de controle periode|Geen dimensies|
|Geschreven bytes op de schijf|Geschreven bytes op de schijf|Bytes|Totaal|Naar schijf geschreven bytes tijdens de controle periode|Geen dimensies|
|Lees bewerkingen op de schijf per seconde|Lees bewerkingen op de schijf per seconde|CountPerSecond|Average|Lees-IOPS schijf|Geen dimensies|
|Schrijf bewerkingen op de schijf per seconde|Schrijf bewerkingen op de schijf per seconde|CountPerSecond|Average|Schijf schrijf-IOPS|Geen dimensies|
|Resterende CPU-tegoeden|Resterende CPU-tegoeden|Aantal|Average|Totaal aantal beschik bare tegoeden voor burst|Geen dimensies|
|Verbruikte CPU-tegoeden|Verbruikte CPU-tegoeden|Aantal|Average|Totaal aantal tegoeden dat door de virtuele machine wordt verbruikt|Geen dimensies|
|Per schijf gelezen bytes/sec.|Gegevens schijf gelezen bytes per seconde (afgeschaft)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode|SlotId|
|Schrijf bewerkingen per schijf/sec.|Gegevens schijf schrijf bewerkingen in bytes per seconde (afgeschaft)|CountPerSecond|Average|Tijdens de controle periode naar één schijf geschreven bytes per seconde|SlotId|
|Lees bewerkingen per schijf/sec.|Lees bewerkingen op de gegevens schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode|SlotId|
|Schrijf bewerkingen per schijf/SEC|Schrijf bewerkingen op de gegevens schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS tijdens de controle periode van één schijf schrijven|SlotId|
|WACHTRIJ diepte per schijf|WACHTRIJ diepte van de gegevens schijf (afgeschaft)|Aantal|Average|Wachtrij diepte van gegevens schijf (of wachtrij lengte)|SlotId|
|BESTURINGSSYSTEEM per schijf gelezen bytes per seconde|BESTURINGSSYSTEEM schijf gelezen bytes per seconde (afgeschaft)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode voor de besturingssysteem schijf|Geen dimensies|
|Per schijf geschreven bytes/sec.|BESTURINGSSYSTEEM schijf schrijf bewerkingen in bytes per seconde (afgeschaft)|CountPerSecond|Average|Geschreven bytes per seconde tijdens de controle periode voor de besturingssysteem schijf|Geen dimensies|
|Lees bewerkingen per schijf voor het besturings systeem/sec.|Lees bewerkingen op de besturingssysteem schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode voor de besturingssysteem schijf|Geen dimensies|
|Schrijf bewerkingen per schijf van het besturings systeem/SEC|Schrijf bewerkingen op de besturingssysteem schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS tijdens de controle periode voor de besturingssysteem schijf schrijven vanaf één schijf|Geen dimensies|
|WACHTRIJ diepte voor het besturings systeem per schijf|WACHTRIJ diepte van de besturingssysteem schijf (afgeschaft)|Aantal|Average|Wachtrij diepte van het besturings systeem (of wachtrij lengte)|Geen dimensies|
|Gegevens schijf gelezen bytes per seconde|Gegevens schijf gelezen bytes per seconde (preview)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode|LUN|
|Geschreven bytes per seconde gegevens schijf|Geschreven bytes per seconde (preview) gegevens schijf|CountPerSecond|Average|Tijdens de controle periode naar één schijf geschreven bytes per seconde|LUN|
|Lees bewerkingen op de gegevens schijf per seconde|Lees bewerkingen op de gegevens schijf per seconde (preview)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode|LUN|
|Schrijf bewerkingen op de gegevens schijf per seconde|Schrijf bewerkingen op de gegevens schijf per seconde (preview)|CountPerSecond|Average|IOPS tijdens de controle periode van één schijf schrijven|LUN|
|Wachtrij diepte van gegevens schijf|Wachtrij diepte van gegevens schijf (preview-versie)|Aantal|Average|Wachtrij diepte van gegevens schijf (of wachtrij lengte)|LUN|
|BESTURINGSSYSTEEM schijf gelezen bytes per seconde|BESTURINGSSYSTEEM schijf gelezen bytes per seconde (preview)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode voor de besturingssysteem schijf|Geen dimensies|
|Schrijf bewerkingen op de besturingssysteem schijf per seconde|Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)|CountPerSecond|Average|Geschreven bytes per seconde tijdens de controle periode voor de besturingssysteem schijf|Geen dimensies|
|Lees bewerkingen van de besturingssysteem schijf per seconde|Lees bewerkingen op de besturingssysteem schijf per seconde (preview)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode voor de besturingssysteem schijf|Geen dimensies|
|Schrijf bewerkingen op de besturingssysteem schijf per seconde|Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)|CountPerSecond|Average|IOPS tijdens de controle periode voor de besturingssysteem schijf schrijven vanaf één schijf|Geen dimensies|
|Wachtrij diepte van besturingssysteem schijf|Wachtrij diepte van de besturingssysteem schijf (preview-versie)|Aantal|Average|Wachtrij diepte van het besturings systeem (of wachtrij lengte)|Geen dimensies|
|Binnenkomende stromen|Binnenkomende stromen (preview-versie)|Aantal|Average|Inkomende stromen zijn het aantal huidige stromen in de binnenkomende richting (verkeer dat wordt verzonden naar de virtuele machine)|Geen dimensies|
|Uitgaande stromen|Uitgaande stromen (preview-versie)|Aantal|Average|Uitgaande stromen zijn het aantal huidige stromen in de uitgaande richting (verkeer dat uit de virtuele machine wordt verzonden)|Geen dimensies|
|Maximum aanmaak frequentie inkomende stromen|Maximum aantal aanmaak snelheden inkomende stromen (preview-versie)|CountPerSecond|Average|Het maximale aantal inkomende stromen (verkeer dat wordt verzonden naar de virtuele machine)|Geen dimensies|
|Maximum aanmaak frequentie van uitgaande stromen|Maximum aantal uitgaande stromen voor maken (preview)|CountPerSecond|Average|De maximale aanmaak frequentie van uitgaande stromen (verkeer dat uit de virtuele machine wordt verzonden)|Geen dimensies|
|Treffer voor Premium data-schijf cache lezen|Cache geheugen voor lezen van Premium-gegevens schijf (preview-versie)|Procent|Average|Treffer voor Premium data-schijf cache lezen|LUN|
|Lees missers cache Premium-gegevens schijf|Cache voor lezen van Premium-gegevens schijf (preview)|Procent|Average|Lees missers cache Premium-gegevens schijf|LUN|
|Treffer voor Premium-besturingssysteem schijf cache lezen|Treffer voor het lezen van een Premium-besturingssysteem schijf cache (preview-versie)|Procent|Average|Treffer voor Premium-besturingssysteem schijf cache lezen|Geen dimensies|
|Leesmij voor Premium-besturingssysteem schijf cache lezen|Schijf cache voor Premium-Lees-missers (preview-versie)|Procent|Average|Leesmij voor Premium-besturingssysteem schijf cache lezen|Geen dimensies|
|Totaal netwerk|Totaal netwerk|Bytes|Totaal|Het aantal ontvangen bytes op alle netwerk interfaces door de virtuele machine (s) (binnenkomend verkeer)|Geen dimensies|
|Totaal aantal netwerk|Totaal aantal netwerk|Bytes|Totaal|Het aantal uitgaande bytes op alle netwerk interfaces door de virtuele machine (s) (uitgaand verkeer)|Geen dimensies|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Procent|Average|Het percentage toegewezen reken eenheden dat momenteel wordt gebruikt door de virtuele machine (s)|VMName|
|Netwerk in|In factureer bare netwerk|Bytes|Totaal|Het aantal factureer bare bytes dat is ontvangen op alle netwerk interfaces door de virtuele machine (s) (binnenkomend verkeer)|VMName|
|Netwerk uit|Onfactureerbaar netwerk|Bytes|Totaal|Het aantal factureer bare bytes op alle netwerk interfaces door de virtuele machine (s) (uitgaand verkeer)|VMName|
|Gelezen bytes op de schijf|Gelezen bytes op de schijf|Bytes|Totaal|Gelezen bytes van de schijf tijdens de controle periode|VMName|
|Geschreven bytes op de schijf|Geschreven bytes op de schijf|Bytes|Totaal|Naar schijf geschreven bytes tijdens de controle periode|VMName|
|Lees bewerkingen op de schijf per seconde|Lees bewerkingen op de schijf per seconde|CountPerSecond|Average|Lees-IOPS schijf|VMName|
|Schrijf bewerkingen op de schijf per seconde|Schrijf bewerkingen op de schijf per seconde|CountPerSecond|Average|Schijf schrijf-IOPS|VMName|
|Resterende CPU-tegoeden|Resterende CPU-tegoeden|Aantal|Average|Totaal aantal beschik bare tegoeden voor burst|Geen dimensies|
|Verbruikte CPU-tegoeden|Verbruikte CPU-tegoeden|Aantal|Average|Totaal aantal tegoeden dat door de virtuele machine wordt verbruikt|Geen dimensies|
|Per schijf gelezen bytes/sec.|Gegevens schijf gelezen bytes per seconde (afgeschaft)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode|SlotId|
|Schrijf bewerkingen per schijf/sec.|Gegevens schijf schrijf bewerkingen in bytes per seconde (afgeschaft)|CountPerSecond|Average|Tijdens de controle periode naar één schijf geschreven bytes per seconde|SlotId|
|Lees bewerkingen per schijf/sec.|Lees bewerkingen op de gegevens schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode|SlotId|
|Schrijf bewerkingen per schijf/SEC|Schrijf bewerkingen op de gegevens schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS tijdens de controle periode van één schijf schrijven|SlotId|
|WACHTRIJ diepte per schijf|WACHTRIJ diepte van de gegevens schijf (afgeschaft)|Aantal|Average|Wachtrij diepte van gegevens schijf (of wachtrij lengte)|SlotId|
|BESTURINGSSYSTEEM per schijf gelezen bytes per seconde|BESTURINGSSYSTEEM schijf gelezen bytes per seconde (afgeschaft)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode voor de besturingssysteem schijf|Geen dimensies|
|Per schijf geschreven bytes/sec.|BESTURINGSSYSTEEM schijf schrijf bewerkingen in bytes per seconde (afgeschaft)|CountPerSecond|Average|Geschreven bytes per seconde tijdens de controle periode voor de besturingssysteem schijf|Geen dimensies|
|Lees bewerkingen per schijf voor het besturings systeem/sec.|Lees bewerkingen op de besturingssysteem schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode voor de besturingssysteem schijf|Geen dimensies|
|Schrijf bewerkingen per schijf van het besturings systeem/SEC|Schrijf bewerkingen op de besturingssysteem schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS tijdens de controle periode voor de besturingssysteem schijf schrijven vanaf één schijf|Geen dimensies|
|WACHTRIJ diepte voor het besturings systeem per schijf|WACHTRIJ diepte van de besturingssysteem schijf (afgeschaft)|Aantal|Average|Wachtrij diepte van het besturings systeem (of wachtrij lengte)|Geen dimensies|
|Gegevens schijf gelezen bytes per seconde|Gegevens schijf gelezen bytes per seconde (preview)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode|LUN, VMName|
|Geschreven bytes per seconde gegevens schijf|Geschreven bytes per seconde (preview) gegevens schijf|CountPerSecond|Average|Tijdens de controle periode naar één schijf geschreven bytes per seconde|LUN, VMName|
|Lees bewerkingen op de gegevens schijf per seconde|Lees bewerkingen op de gegevens schijf per seconde (preview)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode|LUN, VMName|
|Schrijf bewerkingen op de gegevens schijf per seconde|Schrijf bewerkingen op de gegevens schijf per seconde (preview)|CountPerSecond|Average|IOPS tijdens de controle periode van één schijf schrijven|LUN, VMName|
|Wachtrij diepte van gegevens schijf|Wachtrij diepte van gegevens schijf (preview-versie)|Aantal|Average|Wachtrij diepte van gegevens schijf (of wachtrij lengte)|LUN, VMName|
|BESTURINGSSYSTEEM schijf gelezen bytes per seconde|BESTURINGSSYSTEEM schijf gelezen bytes per seconde (preview)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode voor de besturingssysteem schijf|VMName|
|Schrijf bewerkingen op de besturingssysteem schijf per seconde|Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)|CountPerSecond|Average|Geschreven bytes per seconde tijdens de controle periode voor de besturingssysteem schijf|VMName|
|Lees bewerkingen van de besturingssysteem schijf per seconde|Lees bewerkingen op de besturingssysteem schijf per seconde (preview)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode voor de besturingssysteem schijf|VMName|
|Schrijf bewerkingen op de besturingssysteem schijf per seconde|Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)|CountPerSecond|Average|IOPS tijdens de controle periode voor de besturingssysteem schijf schrijven vanaf één schijf|VMName|
|Wachtrij diepte van besturingssysteem schijf|Wachtrij diepte van de besturingssysteem schijf (preview-versie)|Aantal|Average|Wachtrij diepte van het besturings systeem (of wachtrij lengte)|VMName|
|Binnenkomende stromen|Binnenkomende stromen (preview-versie)|Aantal|Average|Inkomende stromen zijn het aantal huidige stromen in de binnenkomende richting (verkeer dat wordt verzonden naar de virtuele machine)|VMName|
|Uitgaande stromen|Uitgaande stromen (preview-versie)|Aantal|Average|Uitgaande stromen zijn het aantal huidige stromen in de uitgaande richting (verkeer dat uit de virtuele machine wordt verzonden)|VMName|
|Maximum aanmaak frequentie inkomende stromen|Maximum aantal aanmaak snelheden inkomende stromen (preview-versie)|CountPerSecond|Average|Het maximale aantal inkomende stromen (verkeer dat wordt verzonden naar de virtuele machine)|VMName|
|Maximum aanmaak frequentie van uitgaande stromen|Maximum aantal uitgaande stromen voor maken (preview)|CountPerSecond|Average|De maximale aanmaak frequentie van uitgaande stromen (verkeer dat uit de virtuele machine wordt verzonden)|VMName|
|Treffer voor Premium data-schijf cache lezen|Cache geheugen voor lezen van Premium-gegevens schijf (preview-versie)|Procent|Average|Treffer voor Premium data-schijf cache lezen|LUN, VMName|
|Lees missers cache Premium-gegevens schijf|Cache voor lezen van Premium-gegevens schijf (preview)|Procent|Average|Lees missers cache Premium-gegevens schijf|LUN, VMName|
|Treffer voor Premium-besturingssysteem schijf cache lezen|Treffer voor het lezen van een Premium-besturingssysteem schijf cache (preview-versie)|Procent|Average|Treffer voor Premium-besturingssysteem schijf cache lezen|VMName|
|Leesmij voor Premium-besturingssysteem schijf cache lezen|Schijf cache voor Premium-Lees-missers (preview-versie)|Procent|Average|Leesmij voor Premium-besturingssysteem schijf cache lezen|VMName|
|Totaal netwerk|Totaal netwerk|Bytes|Totaal|Het aantal ontvangen bytes op alle netwerk interfaces door de virtuele machine (s) (binnenkomend verkeer)|VMName|
|Totaal aantal netwerk|Totaal aantal netwerk|Bytes|Totaal|Het aantal uitgaande bytes op alle netwerk interfaces door de virtuele machine (s) (uitgaand verkeer)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Micro soft. ContainerInstance/containerGroups

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CpuUsage|CPU-gebruik|Aantal|Average|CPU-gebruik op alle kernen in millicores.|containerName|
|MemoryUsage|Geheugen gebruik|Bytes|Average|Totaal geheugen gebruik in bytes.|containerName|
|NetworkBytesReceivedPerSecond|Ontvangen netwerk bytes per seconde|Bytes|Average|Het netwerk ontvangen bytes per seconde.|Geen dimensies|
|NetworkBytesTransmittedPerSecond|Verzonden netwerk bytes per seconde|Bytes|Average|Het netwerk aantal verzonden bytes per seconde.|Geen dimensies|

## <a name="microsoftcontainerregistryregistries"></a>Micro soft. ContainerRegistry/registers

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TotalPullCount|Totaal aantal pull-bewerkingen|Aantal|Average|Aantal opgehaalde afbeeldingen in totaal|Geen dimensies|
|SuccessfulPullCount|Aantal geslaagde pull-bewerkingen|Aantal|Average|Aantal geslaagde installatie kopieën|Geen dimensies|
|TotalPushCount|Totaal aantal push berichten|Aantal|Average|Aantal afbeeldings pushes in totaal|Geen dimensies|
|SuccessfulPushCount|Aantal geslaagde push berichten|Aantal|Average|Aantal geslaagde pushes voor installatie kopie|Geen dimensies|
|RunDuration|Uitvoerings duur|Milliseconden|Totaal|Uitvoerings duur in milliseconden|Geen dimensies|

## <a name="microsoftcontainerservicemanagedclusters"></a>Micro soft. container service/managedClusters

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Totaal aantal beschik bare CPU-kernen in een beheerd cluster|Aantal|Totaal|Totaal aantal beschik bare CPU-kernen in een beheerd cluster|Geen dimensies|
|kube_node_status_allocatable_memory_bytes|Totale hoeveelheid beschikbaar geheugen in een beheerd cluster|Bytes|Totaal|Totale hoeveelheid beschikbaar geheugen in een beheerd cluster|Geen dimensies|
|kube_pod_status_ready|Aantal in de status gereed|Aantal|Totaal|Aantal in de status gereed|naam ruimte, pod|
|kube_node_status_condition|Statussen voor de verschillende knooppunt voorwaarden|Aantal|Totaal|Statussen voor de verschillende knooppunt voorwaarden|voor waarde, status, status2, knoop punt|
|kube_pod_status_phase|Aantal per fase|Aantal|Totaal|Aantal per fase|fase, naam ruimte, pod|

## <a name="microsoftcustomerinsightshubs"></a>Micro soft. CustomerInsights/hubs

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights-API-aanroepen|Aantal|Totaal||Geen dimensies|
|DCIMappingImportOperationSuccessfulLines|Voltooide regels voor het importeren van de toewijzing|Aantal|Totaal||Geen dimensies|
|DCIMappingImportOperationFailedLines|De regels voor het importeren van de toewijzing zijn mislukt|Aantal|Totaal||Geen dimensies|
|DCIMappingImportOperationTotalLines|Totaal regels voor import bewerking toewijzen|Aantal|Totaal||Geen dimensies|
|DCIMappingImportOperationRuntimeInSeconds|Runtime van import bewerking in seconden toewijzen|Seconden|Totaal||Geen dimensies|
|DCIOutboundProfileExportSucceeded|Het uitgaande profiel is geëxporteerd|Aantal|Totaal||Geen dimensies|
|DCIOutboundProfileExportFailed|Exporteren van uitgaand profiel is mislukt|Aantal|Totaal||Geen dimensies|
|DCIOutboundProfileExportDuration|Export duur van uitgaand profiel|Seconden|Totaal||Geen dimensies|
|DCIOutboundKpiExportSucceeded|Het exporteren van de uitgaande KPI is voltooid|Aantal|Totaal||Geen dimensies|
|DCIOutboundKpiExportFailed|Het exporteren van de uitgaande KPI is mislukt|Aantal|Totaal||Geen dimensies|
|DCIOutboundKpiExportDuration|Export duur van uitgaande KPI|Seconden|Totaal||Geen dimensies|
|DCIOutboundKpiExportStarted|Het exporteren van de uitgaande KPI is gestart|Seconden|Totaal||Geen dimensies|
|DCIOutboundKpiRecordCount|Aantal uitgaande KPI-records|Seconden|Totaal||Geen dimensies|
|DCIOutboundProfileExportCount|Aantal export uitgaande profielen|Seconden|Totaal||Geen dimensies|
|DCIOutboundInitialProfileExportFailed|Kan het uitgaande initiële profiel niet exporteren|Seconden|Totaal||Geen dimensies|
|DCIOutboundInitialProfileExportSucceeded|Het uitgaande initiële profiel is geëxporteerd|Seconden|Totaal||Geen dimensies|
|DCIOutboundInitialKpiExportFailed|Het exporteren van de uitgaande begin KPI is mislukt|Seconden|Totaal||Geen dimensies|
|DCIOutboundInitialKpiExportSucceeded|Het exporteren van de uitgaande begin KPI is voltooid|Seconden|Totaal||Geen dimensies|
|DCIOutboundInitialProfileExportDurationInSeconds|Export duur van het uitgaande begin profiel in enkele seconden|Seconden|Totaal||Geen dimensies|
|AdlaJobForStandardKpiFailed|De Adla-taak voor de standaard-KPI is in enkele seconden mislukt|Seconden|Totaal||Geen dimensies|
|AdlaJobForStandardKpiTimeOut|Adla-taak voor standaard-KPI-time-out in seconden|Seconden|Totaal||Geen dimensies|
|AdlaJobForStandardKpiCompleted|De Adla-taak voor de standaard-KPI is in enkele seconden voltooid|Seconden|Totaal||Geen dimensies|
|ImportASAValuesFailed|Aantal mislukte import-waarden van ASA|Aantal|Totaal||Geen dimensies|
|ImportASAValuesSucceeded|Aantal geslaagde activeringen voor ASA|Aantal|Totaal||Geen dimensies|
|DCIProfilesCount|Aantal exemplaren van profiel|Aantal|Duren||Geen dimensies|
|DCIInteractionsPerMonthCount|Aantal interacties per maand|Aantal|Duren||Geen dimensies|
|DCIKpisCount|Aantal KPI'S|Aantal|Duren||Geen dimensies|
|DCISegmentsCount|Aantal segmenten|Aantal|Duren||Geen dimensies|
|DCIPredictiveMatchPoliciesCount|Aantal voorspellende overeenkomsten|Aantal|Duren||Geen dimensies|
|DCIPredictionsCount|Aantal voorspellingen|Aantal|Duren||Geen dimensies|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Micro soft. DataBoxEdge/dataBoxEdgeDevices

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|NICReadThroughput|Lees doorvoer (netwerk)|BytesPerSecond|Average|De Lees doorvoer van de netwerk interface op het apparaat in de rapportage periode voor alle volumes in de gateway.|InstanceName|
|NICWriteThroughput|Schrijf doorvoer (netwerk)|BytesPerSecond|Average|De schrijf doorvoer van de netwerk interface op het apparaat in de rapportage periode voor alle volumes in de gateway.|InstanceName|
|CloudReadThroughputPerShare|Door Voer van Cloud downloaden (delen)|BytesPerSecond|Average|De door Voer van downloaden naar Azure vanaf een share tijdens de rapportage periode.|Delen|
|CloudUploadThroughputPerShare|Upload doorvoer van Cloud (delen)|BytesPerSecond|Average|De upload doorvoer naar Azure vanaf een share tijdens de rapportage periode.|Delen|
|BytesUploadedToCloudPerShare|Geüploade Cloud bytes (delen)|Bytes|Average|Het totaal aantal bytes dat is geüpload naar Azure vanaf een share tijdens de rapportage periode.|Delen|
|TotalCapacity|Totale capaciteit|Bytes|Average|Totale capaciteit|Geen dimensies|
|Availablecapacity;)|Beschik bare capaciteit|Bytes|Average|De beschik bare capaciteit in bytes tijdens de rapportage periode.|Geen dimensies|
|CloudUploadThroughput|Upload doorvoer van Cloud|BytesPerSecond|Average|De upload doorvoer van de Cloud naar Azure tijdens de rapportage periode.|Geen dimensies|
|CloudReadThroughput|Door Voer van Cloud downloaden|BytesPerSecond|Average|De door Voer van de Cloud downloadt naar Azure tijdens de rapportage periode.|Geen dimensies|
|BytesUploadedToCloud|Geüploade Cloud bytes (apparaat)|Bytes|Average|Het totale aantal bytes dat tijdens de rapportage periode naar Azure wordt geüpload vanaf een apparaat.|Geen dimensies|
|HyperVVirtualProcessorUtilization|Edge Compute-percentage CPU|Procent|Average|Percentage CPU-gebruik|InstanceName|
|HyperVMemoryUtilization|Edge Compute-geheugen gebruik|Procent|Average|Hoeveelheid RAM-geheugen in gebruik|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Micro soft. DataFactory/datafactories

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|FailedRuns|Mislukte uitvoeringen|Aantal|Totaal||pipelineName, activiteitsnummer|
|SuccessfulRuns|Geslaagde uitvoeringen|Aantal|Totaal||pipelineName, activiteitsnummer|

## <a name="microsoftdatafactoryfactories"></a>Micro soft. DataFactory/fabrieken

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PipelineFailedRuns|Metrische gegevens van mislukte pijplijn uitvoeringen|Aantal|Totaal||FailureType, naam|
|PipelineSucceededRuns|Metrische uitvoerings metingen geslaagde pijp lijnen|Aantal|Totaal||FailureType, naam|
|ActivityFailedRuns|Metrische gegevens mislukte uitvoering van activiteit|Aantal|Totaal||Activity type, PipelineName, FailureType, name|
|ActivitySucceededRuns|Metrische gegevens uitvoeringen uitgevoerde activiteit|Aantal|Totaal||Activity type, PipelineName, FailureType, name|
|TriggerFailedRuns|Meet waarden voor uitvoering van mislukte triggers|Aantal|Totaal||Naam, FailureType|
|TriggerSucceededRuns|Meet waarden voor uitvoering van geslaagde triggers|Aantal|Totaal||Naam, FailureType|
|IntegrationRuntimeCpuPercentage|CPU-gebruik van Integration runtime|Procent|Average||IntegrationRuntimeName, knooppuntnaam|
|IntegrationRuntimeAvailableMemory|Beschik bare geheugen voor Integration runtime|Bytes|Average||IntegrationRuntimeName, knooppuntnaam|
|MaxAllowedResourceCount|Maximum aantal toegestane entiteiten|Aantal|Maximum||Geen dimensies|
|MaxAllowedFactorySizeInGbUnits|Maxi maal toegestane grootte van de fabriek (GB-eenheid)|Aantal|Maximum||Geen dimensies|
|ResourceCount|Totaal aantal entiteiten|Aantal|Maximum||Geen dimensies|
|FactorySizeInGbUnits|Totale grootte van de fabriek (GB-eenheid)|Aantal|Maximum||Geen dimensies|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Micro soft. DataLakeAnalytics/accounts

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|JobEndedSuccess|Geslaagde taken|Aantal|Totaal|Aantal geslaagde taken.|Geen dimensies|
|JobEndedFailure|Mislukte taken|Aantal|Totaal|Aantal mislukte taken.|Geen dimensies|
|JobEndedCancelled|Geannuleerde taken|Aantal|Totaal|Aantal geannuleerde taken.|Geen dimensies|
|JobAUEndedSuccess|Geslaagde AU-tijd|Seconden|Totaal|Totale AU-tijd voor voltooide taken.|Geen dimensies|
|JobAUEndedFailure|Mislukte AU-tijd|Seconden|Totaal|Totale AU-tijd voor mislukte taken.|Geen dimensies|
|JobAUEndedCancelled|Geannuleerde AU-tijd|Seconden|Totaal|Totale AU-tijd voor geannuleerde taken.|Geen dimensies|

## <a name="microsoftdatalakestoreaccounts"></a>Micro soft. data Lake Store/accounts

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TotalStorage|Totale opslag|Bytes|Maximum|De totale hoeveelheid gegevens die in het account is opgeslagen.|Geen dimensies|
|DataWritten|Gegevens geschreven|Bytes|Totaal|De totale hoeveelheid gegevens die naar het account wordt geschreven.|Geen dimensies|
|DataRead|Gegevens lezen|Bytes|Totaal|De totale hoeveelheid gegevens die uit het account is gelezen.|Geen dimensies|
|WriteRequests|Aanvragen schrijven|Aantal|Totaal|Het aantal aanvragen voor het schrijven van gegevens naar het account.|Geen dimensies|
|ReadRequests|Aanvragen lezen|Aantal|Totaal|Aantal aanvragen voor het lezen van gegevens voor het account.|Geen dimensies|

## <a name="microsoftdbformariadbservers"></a>Micro soft. DBforMariaDB/servers

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Procent|Average|CPU-percentage|Geen dimensies|
|memory_percent|Geheugen percentage|Procent|Average|Geheugen percentage|Geen dimensies|
|io_consumption_percent|IO-percentage|Procent|Average|IO-percentage|Geen dimensies|
|storage_percent|Opslag percentage|Procent|Average|Opslag percentage|Geen dimensies|
|storage_used|Gebruikte opslag|Bytes|Average|Gebruikte opslag|Geen dimensies|
|storage_limit|Opslag limiet|Bytes|Average|Opslag limiet|Geen dimensies|
|serverlog_storage_percent|Percentage server logboek opslag|Procent|Average|Percentage server logboek opslag|Geen dimensies|
|serverlog_storage_usage|Gebruikte server logboek opslag|Bytes|Average|Gebruikte server logboek opslag|Geen dimensies|
|serverlog_storage_limit|Opslag limiet voor server logboek|Bytes|Average|Opslag limiet voor server logboek|Geen dimensies|
|active_connections|Actieve verbindingen|Aantal|Average|Actieve verbindingen|Geen dimensies|
|connections_failed|Mislukte verbindingen|Aantal|Totaal|Mislukte verbindingen|Geen dimensies|
|seconds_behind_master|Replicatie vertraging in seconden|Aantal|Average|Replicatie vertraging in seconden|Geen dimensies|
|backup_storage_used|Gebruikte back-upopslag|Bytes|Average|Gebruikte back-upopslag|Geen dimensies|
|network_bytes_egress|Netwerk uit|Bytes|Totaal|Netwerk uit over actieve verbindingen|Geen dimensies|
|network_bytes_ingress|Netwerk in|Bytes|Totaal|Netwerk in meerdere actieve verbindingen|Geen dimensies|

## <a name="microsoftdbformysqlservers"></a>Micro soft. DBforMySQL/servers

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Procent|Average|CPU-percentage|Geen dimensies|
|memory_percent|Geheugen percentage|Procent|Average|Geheugen percentage|Geen dimensies|
|io_consumption_percent|IO-percentage|Procent|Average|IO-percentage|Geen dimensies|
|storage_percent|Opslag percentage|Procent|Average|Opslag percentage|Geen dimensies|
|storage_used|Gebruikte opslag|Bytes|Average|Gebruikte opslag|Geen dimensies|
|storage_limit|Opslag limiet|Bytes|Average|Opslag limiet|Geen dimensies|
|serverlog_storage_percent|Percentage server logboek opslag|Procent|Average|Percentage server logboek opslag|Geen dimensies|
|serverlog_storage_usage|Gebruikte server logboek opslag|Bytes|Average|Gebruikte server logboek opslag|Geen dimensies|
|serverlog_storage_limit|Opslag limiet voor server logboek|Bytes|Average|Opslag limiet voor server logboek|Geen dimensies|
|active_connections|Actieve verbindingen|Aantal|Average|Actieve verbindingen|Geen dimensies|
|connections_failed|Mislukte verbindingen|Aantal|Totaal|Mislukte verbindingen|Geen dimensies|
|seconds_behind_master|Replicatie vertraging in seconden|Aantal|Average|Replicatie vertraging in seconden|Geen dimensies|
|backup_storage_used|Gebruikte back-upopslag|Bytes|Average|Gebruikte back-upopslag|Geen dimensies|
|network_bytes_egress|Netwerk uit|Bytes|Totaal|Netwerk uit over actieve verbindingen|Geen dimensies|
|network_bytes_ingress|Netwerk in|Bytes|Totaal|Netwerk in meerdere actieve verbindingen|Geen dimensies|

## <a name="microsoftdbforpostgresqlservers"></a>Micro soft. DBforPostgreSQL/servers

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Procent|Average|CPU-percentage|Geen dimensies|
|memory_percent|Geheugen percentage|Procent|Average|Geheugen percentage|Geen dimensies|
|io_consumption_percent|IO-percentage|Procent|Average|IO-percentage|Geen dimensies|
|storage_percent|Opslag percentage|Procent|Average|Opslag percentage|Geen dimensies|
|storage_used|Gebruikte opslag|Bytes|Average|Gebruikte opslag|Geen dimensies|
|storage_limit|Opslag limiet|Bytes|Average|Opslag limiet|Geen dimensies|
|serverlog_storage_percent|Percentage server logboek opslag|Procent|Average|Percentage server logboek opslag|Geen dimensies|
|serverlog_storage_usage|Gebruikte server logboek opslag|Bytes|Average|Gebruikte server logboek opslag|Geen dimensies|
|serverlog_storage_limit|Opslag limiet voor server logboek|Bytes|Average|Opslag limiet voor server logboek|Geen dimensies|
|active_connections|Actieve verbindingen|Aantal|Average|Actieve verbindingen|Geen dimensies|
|connections_failed|Mislukte verbindingen|Aantal|Totaal|Mislukte verbindingen|Geen dimensies|
|backup_storage_used|Gebruikte back-upopslag|Bytes|Average|Gebruikte back-upopslag|Geen dimensies|
|network_bytes_egress|Netwerk uit|Bytes|Totaal|Netwerk uit over actieve verbindingen|Geen dimensies|
|network_bytes_ingress|Netwerk in|Bytes|Totaal|Netwerk in meerdere actieve verbindingen|Geen dimensies|
|pg_replica_log_delay_in_seconds|Replica vertraging|Seconden|Maximum|Replica vertraging in seconden|Geen dimensies|
|pg_replica_log_delay_in_bytes|Maximale vertraging in Replica's|Bytes|Maximum|Vertraging in bytes van de meest vertraagde replica|Geen dimensies|

## <a name="microsoftdbforpostgresqlserversv2"></a>Micro soft. DBforPostgreSQL/serversv2

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Procent|Average|CPU-percentage|Geen dimensies|
|memory_percent|Geheugen percentage|Procent|Average|Geheugen percentage|Geen dimensies|
|IOPS|IOPS|Aantal|Average|I/o-bewerkingen per seconde|Geen dimensies|
|storage_percent|Opslag percentage|Procent|Average|Opslag percentage|Geen dimensies|
|storage_used|Gebruikte opslag|Bytes|Average|Gebruikte opslag|Geen dimensies|
|active_connections|Actieve verbindingen|Aantal|Average|Actieve verbindingen|Geen dimensies|
|network_bytes_egress|Netwerk uit|Bytes|Totaal|Netwerk uit over actieve verbindingen|Geen dimensies|
|network_bytes_ingress|Netwerk in|Bytes|Totaal|Netwerk in meerdere actieve verbindingen|Geen dimensies|

## <a name="microsoftdevicesiothubs"></a>Micro soft. devices/IotHubs

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|D2C. telemetrie. ingress. allProtocol|Verzend pogingen voor telemetrie-berichten|Aantal|Totaal|Aantal pogingen voor het verzenden van apparaat-naar-Cloud-telemetrie naar uw IoT hub|Geen dimensies|
|D2C. telemetrie. ingress. geslaagd|Verzonden telemetriegegevens|Aantal|Totaal|Aantal te verzenden apparaat-naar-Cloud-telemetrie-berichten naar uw IoT-hub|Geen dimensies|
|C2D. commands. OUTuitgang. complete. geslaagd|Opdrachten voltooid|Aantal|Totaal|Aantal Cloud-naar-apparaat-opdrachten die zijn voltooid door het apparaat|Geen dimensies|
|C2D. commands. uitgangs. Abandon. geslaagd|Opdrachten afgebroken|Aantal|Totaal|Aantal Cloud-naar-apparaat-opdrachten die zijn afgebroken door het apparaat|Geen dimensies|
|C2D. commands. uitgangs. reject. geslaagd|Geweigerde opdrachten|Aantal|Totaal|Aantal Cloud-naar-apparaat-opdrachten dat door het apparaat is afgewezen|Geen dimensies|
|apparaten. totalDevices|Totaal aantal apparaten (afgeschaft)|Aantal|Totaal|Aantal apparaten dat is geregistreerd bij uw IoT-hub|Geen dimensies|
|apparaten. connectedDevices. allProtocol|Verbonden apparaten (afgeschaft) |Aantal|Totaal|Aantal apparaten dat is verbonden met uw IoT-hub|Geen dimensies|
|D2C. telemetrie. uitgangs. geslaagd|Route ring: telemetrie-berichten|Aantal|Totaal|Het aantal keren dat berichten zijn bezorgd bij alle eind punten met behulp van IoT Hub route ring. Als een bericht wordt doorgestuurd naar meerdere eind punten, wordt deze waarde met één verhoogd voor elke geslaagde levering. Als een bericht meerdere keren op hetzelfde eind punt wordt bezorgd, wordt deze waarde met één verhoogd voor elke geslaagde levering.|Geen dimensies|
|D2C. telemetrie. uitgangs. verwijderd|Route ring: telemetrie-berichten verwijderd |Aantal|Totaal|Het aantal keren dat berichten zijn verwijderd door IoT Hub route ring vanwege Dead-eind punten. Deze waarde telt geen berichten die worden bezorgd als terugval route als berichten die worden verzonden, niet worden bezorgd.|Geen dimensies|
|D2C. telemetrie. zwevend|Route ring: telemetriegegevens van zwevende berichten |Aantal|Totaal|Het aantal keren dat berichten zijn zwevend door IoT Hub route ring, omdat ze niet overeenkomen met de regels voor door sturen (inclusief de terugval regel). |Geen dimensies|
|D2C. telemetrie. uitgangs. ongeldig|Route ring: telemetrie-berichten incompatibel|Aantal|Totaal|Het aantal keren dat IoT Hub route ring geen berichten kan leveren als gevolg van incompatibiliteit met het eind punt. Deze waarde omvat geen nieuwe pogingen.|Geen dimensies|
|D2C. telemetrie. fallback|Route ring: berichten worden bezorgd bij terugval|Aantal|Totaal|Het aantal keren dat de route ring van berichten IoT Hub verzonden naar het eind punt dat is gekoppeld aan de terugval route.|Geen dimensies|
|D2C. endpoints. uitgangs. Event hubs|Route ring: berichten worden bezorgd bij Event hub|Aantal|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan Event hub-eind punten.|Geen dimensies|
|D2C. endpoints. latentie. Event hubs|Route ring: bericht latentie voor Event hub|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten IoT Hub en het binnenkomen van berichten in een event hub-eind punt.|Geen dimensies|
|D2C. endpoints. uitgangs. serviceBusQueues|Route ring: berichten worden bezorgd bij Service Bus wachtrij|Aantal|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan Service Bus-wachtrij-eind punten.|Geen dimensies|
|D2C. endpoints. latentie. serviceBusQueues|Route ring: bericht latentie voor Service Bus wachtrij|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en telemetrie-berichten in een Service Bus wachtrij-eind punt.|Geen dimensies|
|D2C. endpoints. uitgangs. serviceBusTopics|Route ring: berichten die worden bezorgd bij Service Bus onderwerp|Aantal|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan Service Bus onderwerp-eind punten.|Geen dimensies|
|D2C. endpoints. latentie. serviceBusTopics|Route ring: bericht latentie voor Service Bus onderwerp|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en telemetrie-berichten in het eind punt van een Service Bus onderwerp.|Geen dimensies|
|D2C. endpoints. uitgangs punt. builtIn. Events|Route ring: berichten worden bezorgd bij berichten/gebeurtenissen|Aantal|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan het ingebouwde eind punt (berichten/gebeurtenissen). Deze metrische gegevens worden alleen gestart als route ring is ingeschakeld (https://aka.ms/iotrouting) voor de IoT-hub.|Geen dimensies|
|D2C. endpoints. latentie. builtIn. Events|Route ring: bericht latentie voor berichten/gebeurtenissen|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en het inkomend telemetrie-bericht in het ingebouwde eind punt (berichten/gebeurtenissen). Deze metrische gegevens worden alleen gestart als route ring is ingeschakeld (https://aka.ms/iotrouting) voor de IoT-hub.|Geen dimensies|
|D2C. endpoints. outwaarde. Storage|Route ring: berichten worden bezorgd bij de opslag|Aantal|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan de opslag eindpunten.|Geen dimensies|
|D2C. endpoints. latentie. opslag|Route ring: bericht latentie voor opslag|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en telemetrie-berichten in een opslag eindpunt.|Geen dimensies|
|D2C. endpoints. out. storage. bytes|Route ring: gegevens worden geleverd aan de opslag|Bytes|Totaal|De hoeveelheid gegevens (bytes) IoT Hub route ring die aan de opslag eindpunten wordt geleverd.|Geen dimensies|
|D2C. endpoints. outwaar. storage. blobs|Route ring: blobs die aan de opslag worden geleverd|Aantal|Totaal|Het aantal keren dat IoT Hub route ring blobs naar opslag eindpunten heeft geleverd.|Geen dimensies|
|EventGridDeliveries|Event Grid leveringen (preview-versie)|Aantal|Totaal|Het aantal IoT Hub gebeurtenissen dat is gepubliceerd op Event Grid. Gebruik de dimensie resultaat voor het aantal geslaagde en mislukte aanvragen. De dimensie type-tekst geeft het soort gebeurtenis weer (https://aka.ms/ioteventgrid).|Resultaat, type gebeurtenis|
|EventGridLatency|De gemiddelde latentie (in milliseconden) vanaf het moment waarop de IOT hub-gebeurtenis werd gegenereerd toen de gebeurtenis werd gepubliceerd in Event Grid. Dit getal is een gemiddelde tussen alle gebeurtenis typen. Gebruik de dimensie type type om de latentie van een specifiek soort gebeurtenis weer te geven.|Type|
|D2C. dubbele. lezen. geslaagd|Geslaagde dubbele Lees bewerkingen van apparaten|Aantal|Totaal|De telling van alle geslaagde apparaten met dubbele Lees bewerkingen.|Geen dimensies|
|D2C. dubbele. Read. failure|Mislukte dubbele Lees bewerkingen van apparaten|Aantal|Totaal|Het aantal apparaten dat niet kan worden gestart, dubbele Lees bewerkingen.|Geen dimensies|
|D2C. dubbele. Lees. grootte|Reactie grootte van dubbele Lees bewerkingen van apparaten|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde apparaten-geïnitieerde dubbele Lees bewerkingen.|Geen dimensies|
|D2C. dubbele. update. geslaagd|Geslaagde dubbele updates van apparaten|Aantal|Totaal|De telling van alle geslaagde, door het apparaat geïnitieerde dubbele updates.|Geen dimensies|
|D2C. dubbele. update. failure|Mislukte dubbele updates van apparaten|Aantal|Totaal|Het aantal apparaten dat door een apparaat is gestart en dubbele updates heeft uitgevoerd.|Geen dimensies|
|D2C. dubbele. update. grootte|Grootte van dubbele updates van apparaten|Bytes|Average|Het gemiddelde, het minimum en de maximale grootte van alle geslaagde, door het apparaat geïnitieerde dubbele updates.|Geen dimensies|
|C2D. methods. geslaagd|Geslaagde directe aanroepen van de methode|Aantal|Totaal|Het aantal voltooide direct-methode aanroepen.|Geen dimensies|
|C2D. methods. failure|Mislukte directe aanroepen van methode|Aantal|Totaal|Het aantal mislukte direct-methode aanroepen.|Geen dimensies|
|C2D. methods. requestSize|Aanvraag grootte van directe-methode aanroepen|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde direct-methode aanvragen.|Geen dimensies|
|C2D. methods. responseSize|Antwoord grootte van directe methode aanroepen|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde reacties van de methode direct.|Geen dimensies|
|C2D. dubbele. lezen. geslaagd|Geslaagde dubbele Lees bewerkingen van back-end|Aantal|Totaal|Het aantal geslaagde back-end-geïnitieerde dubbele Lees bewerkingen.|Geen dimensies|
|C2D. dubbele. Read. failure|Mislukte dubbele Lees bewerkingen van back-end|Aantal|Totaal|Het aantal mislukte back-end-geïnitieerde dubbele Lees bewerkingen.|Geen dimensies|
|C2D. dubbele. Lees. grootte|Reactie grootte van dubbele Lees bewerkingen van de back-end|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde back-end-geïnitieerde dubbele Lees bewerkingen.|Geen dimensies|
|C2D. dubbele. update. geslaagd|Geslaagde dubbele updates van back-end|Aantal|Totaal|Het aantal geslaagde, door de back-end gestarte dubbele updates.|Geen dimensies|
|C2D. dubbele. update. failure|Mislukte dubbele updates van back-end|Aantal|Totaal|Het aantal niet-geslaagde, door de back-end geïnitieerde dubbele updates.|Geen dimensies|
|C2D. dubbele. update. grootte|Grootte van dubbele updates van back-end|Bytes|Average|Het gemiddelde, het minimum en de maximale grootte van alle geslaagde back-end-geïnitieerde dubbele updates.|Geen dimensies|
|twinQueries. geslaagd|Geslaagde dubbele query's|Aantal|Totaal|Het aantal geslaagde dubbele query's.|Geen dimensies|
|twinQueries. failure|Mislukte dubbele query's|Aantal|Totaal|Het aantal mislukte dubbele query's.|Geen dimensies|
|twinQueries.resultSize|Resultaat grootte van dubbele query's|Bytes|Average|Het gemiddelde, het minimum en het maximum van de resultaat grootte van alle geslaagde dubbele query's.|Geen dimensies|
|Jobs. createTwinUpdateJob. geslaagd|Geslaagde creatie van dubbele update taken|Aantal|Totaal|Het aantal van alle geslaagde taken voor het maken van dubbele updates.|Geen dimensies|
|Jobs. createTwinUpdateJob. failure|Kan geen dubbele update taken uitvoeren|Aantal|Totaal|Het aantal mislukte het maken van dubbele update taken.|Geen dimensies|
|Jobs. createDirectMethodJob. geslaagd|Geslaagde creatie van methode aanroep taken|Aantal|Totaal|Het aantal van alle geslaagde aanroepen van directe methode aanroep taken.|Geen dimensies|
|Jobs. createDirectMethodJob. failure|Kan geen aanroepen van methode aanroep taken uitvoeren|Aantal|Totaal|Het aantal van alle mislukte aanroepen van directe methode aanroep taken.|Geen dimensies|
|Jobs. listJobs. geslaagd|Geslaagde aanroepen naar lijst taken|Aantal|Totaal|Het aantal geslaagde aanroepen naar lijst taken.|Geen dimensies|
|Jobs. listJobs. failure|Mislukte aanroepen naar lijst taken|Aantal|Totaal|Het aantal mislukte aanroepen naar lijst taken.|Geen dimensies|
|Jobs. cancelJob. geslaagd|Voltooide taak annuleringen|Aantal|Totaal|Het aantal geslaagde aanroepen om een taak te annuleren.|Geen dimensies|
|Jobs. cancelJob. failure|Mislukte taak annuleringen|Aantal|Totaal|Het aantal mislukte aanroepen om een taak te annuleren.|Geen dimensies|
|Jobs. queryJobs. geslaagd|Geslaagde taak query's|Aantal|Totaal|Het aantal geslaagde aanroepen naar query taken.|Geen dimensies|
|Jobs. queryJobs. failure|Mislukte taak query's|Aantal|Totaal|Het aantal mislukte aanroepen naar query taken.|Geen dimensies|
|Jobs. voltooid|Voltooide taken|Aantal|Totaal|Het aantal voltooide taken.|Geen dimensies|
|Jobs. mislukt|Mislukte taken|Aantal|Totaal|Het aantal mislukte taken.|Geen dimensies|
|D2C. telemetrie. ingress. sendThrottle|Aantal beperkings fouten|Aantal|Totaal|Aantal beperkings fouten door doorvoer vertraging van apparaat|Geen dimensies|
|dailyMessageQuotaUsed|Totaal aantal gebruikte berichten|Aantal|Average|Het totale aantal berichten dat momenteel wordt gebruikt. Dit is een cumulatieve waarde die elke dag opnieuw wordt ingesteld op nul om 00:00 UTC.|Geen dimensies|
|deviceDataUsage|Totale hoeveelheid gegevens gebruik van apparaat|Bytes|Totaal|Verzonden bytes van en naar apparaten die zijn verbonden met IotHub|Geen dimensies|
|totalDeviceCount|Totaal aantal apparaten (preview-versie)|Aantal|Average|Aantal apparaten dat is geregistreerd bij uw IoT-hub|Geen dimensies|
|connectedDeviceCount|Verbonden apparaten (preview-versie)|Aantal|Average|Aantal apparaten dat is verbonden met uw IoT-hub|Geen dimensies|
|Configuraties|Metrische configuratie gegevens|Aantal|Totaal|Metrische gegevens voor configuratie bewerkingen|Geen dimensies|

## <a name="microsoftdevicesprovisioningservices"></a>Micro soft. devices/provisioningServices

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|RegistrationAttempts|Registratie pogingen|Aantal|Totaal|Aantal pogingen voor apparaatregistratie|ProvisioningServiceName, IotHubName, status|
|DeviceAssignments|Apparaten toegewezen|Aantal|Totaal|Aantal apparaten dat is toegewezen aan een IoT-hub|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Attestation-pogingen|Aantal|Totaal|Aantal pogingen voor het uitvoeren van een apparaat|ProvisioningServiceName, status, protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Micro soft. DocumentDB/databaseAccounts

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AvailableStorage|Beschik bare opslag|Bytes|Totaal|Totale beschik bare opslag gerapporteerd bij een granulatie van 5 minuten|Verzamelingnaam, databasenaam, regio|
|CassandraConnectionClosures|Cassandra-verbinding sluiten|Aantal|Totaal|Aantal Cassandra-verbindingen dat is gesloten, gerapporteerd met een granulatie van 1 minuut|Regio, ClosureReason|
|CassandraRequestCharges|Kosten voor Cassandra-aanvragen|Aantal|Totaal|RUs gebruikt voor Cassandra-aanvragen|DATABASENAME, verzamel-, regio, OperationType, resource type|
|CassandraRequests|Cassandra aanvragen|Aantal|Aantal|Aantal gemaakte Cassandra-aanvragen|DATABASENAME, verzamel-, regio, OperationType, resource type, error code|
|DataUsage|Gegevensgebruik|Bytes|Totaal|Totaal gegevens gebruik gerapporteerd bij 5 minuten granulariteit|Verzamelingnaam, databasenaam, regio|
|DocumentCount|Aantal documenten|Aantal|Totaal|Totaal aantal documenten gemeld bij 5 minuten granulariteit|Verzamelingnaam, databasenaam, regio|
|DocumentQuota|Document quotum|Bytes|Totaal|Totale opslag quotum gerapporteerd bij een granulatie van 5 minuten|Verzamelingnaam, databasenaam, regio|
|IndexUsage|Index gebruik|Bytes|Totaal|Totaal gebruik van index gerapporteerd bij een granulatie van 5 minuten|Verzamelingnaam, databasenaam, regio|
|MetadataRequests|Meta gegevens aanvragen|Aantal|Aantal|Aantal aanvragen voor meta gegevens. Cosmos DB onderhoudt de verzameling van meta gegevens van het systeem voor elk account, waarmee u verzamelingen, data bases, enzovoort, en hun configuraties, kosteloos kunt inventariseren.|DATABASENAME, verzamel-, regio, status code, |
|MongoRequestCharge|Kosten voor Mongo-aanvragen|Aantal|Totaal|Verbruikte Mongo-aanvraag eenheden|DATABASENAME, verzamel-, regio, opdrachtnaam, error code|
|MongoRequests|Mongo aanvragen|Aantal|Aantal|Aantal gemaakte Mongo-aanvragen|DATABASENAME, verzamel-, regio, opdrachtnaam, error code|
|ProvisionedThroughput|Ingerichte doorvoer|Aantal|Maximum|Ingerichte doorvoer|DATABASENAME, verzamelnaam|
|ReplicationLatency|P99-replicatie latentie|Milliseconden|Average|Replicatie latentie van P99 voor de bron-en doel regio's voor geografisch ingeschakelde account|SourceRegion, TargetRegion|
|ServiceAvailability|Service beschikbaarheid|Procent|Average|Beschik baarheid van account aanvragen op één uur, dag of maand granulatie|Geen dimensies|
|TotalRequestUnits|Totaal aantal aanvraag eenheden|Aantal|Totaal|Verbruikte aanvraag eenheden|DATABASENAME, verzamel-, regio, status code, OperationType|
|TotalRequests|Totaal aantal aanvragen|Aantal|Aantal|Aantal aanvragen dat is gedaan|DATABASENAME, verzamel-, regio, status code, OperationType|

## <a name="microsofteventgridtopics"></a>Micro soft. EventGrid/topics

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PublishSuccessCount|Gepubliceerde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat naar dit onderwerp is gepubliceerd|Geen dimensies|
|PublishFailCount|Mislukte gebeurtenissen publiceren|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet naar dit onderwerp kan worden gepubliceerd|Error type, fout|
|UnmatchedEventCount|Niet-overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet overeenkomt met een van de gebeurtenis abonnementen voor dit onderwerp|Geen dimensies|
|PublishSuccessLatencyInMs|Latentie van publicatie geslaagd|Aantal|Totaal|Latentie van geslaagde publicatie in milliseconden|Geen dimensies|

## <a name="microsofteventgrideventsubscriptions"></a>Micro soft. EventGrid/eventSubscriptions

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|MatchedEventCount|Overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|Geen dimensies|
|DeliveryAttemptFailCount|Mislukte leverings gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet aan dit gebeurtenis abonnement kan worden geleverd|Fout, error type|
|DeliverySuccessCount|Geleverde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat aan dit gebeurtenis abonnement is geleverd|Geen dimensies|
|DestinationProcessingDurationInMs|Doel verwerkings duur|Milliseconden|Average|Doel verwerkings duur in milliseconden|Geen dimensies|
|DroppedEventCount|Verwijderde gebeurtenissen|Aantal|Totaal|Totaal aantal verloren gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|DropReason|
|DeadLetteredCount|Gebeurtenissen met onbestelbare berichten|Aantal|Totaal|Totaal aantal gebeurtenissen met onbestelbare berichten die overeenkomen met dit gebeurtenis abonnement|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Micro soft. EventGrid/extensionTopics

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PublishSuccessCount|Gepubliceerde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat naar dit onderwerp is gepubliceerd|Geen dimensies|
|PublishFailCount|Mislukte gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet naar dit onderwerp kan worden gepubliceerd|Error type, fout|
|UnmatchedEventCount|Niet-overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet overeenkomt met een van de gebeurtenis abonnementen voor dit onderwerp|Geen dimensies|
|PublishSuccessLatencyInMs|Latentie van publicatie geslaagd|Aantal|Totaal|Latentie van geslaagde publicatie in milliseconden|Geen dimensies|

## <a name="microsofteventhubnamespaces"></a>Micro soft. EventHub/naam ruimten

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SuccessfulRequests|Geslaagde aanvragen|Aantal|Totaal|Geslaagde aanvragen voor micro soft. EventHub.|EntityName |
|ServerErrors|Server fouten.|Aantal|Totaal|Server fouten voor micro soft. EventHub.|EntityName |
|UserErrors|Gebruikers fouten.|Aantal|Totaal|Gebruikers fouten voor micro soft. EventHub.|EntityName |
|QuotaExceededErrors|Quota overschreden fouten.|Aantal|Totaal|Quotum overschrijdt fouten voor micro soft. EventHub.|EntityName |
|ThrottledRequests|Vertraagde aanvragen.|Aantal|Totaal|Beperkte aanvragen voor micro soft. EventHub.|EntityName |
|IncomingRequests|Binnenkomende aanvragen|Aantal|Totaal|Binnenkomende aanvragen voor micro soft. EventHub.|entityName|
|IncomingMessages|Inkomende berichten|Aantal|Totaal|Binnenkomende berichten voor micro soft. EventHub.|entityName|
|OutgoingMessages|Uitgaande berichten|Aantal|Totaal|Uitgaande berichten voor micro soft. EventHub.|entityName|
|IncomingBytes|Binnenkomende bytes.|Bytes|Totaal|Binnenkomende bytes voor micro soft. EventHub.|entityName|
|OutgoingBytes|Uitgaande bytes.|Bytes|Totaal|Uitgaande bytes voor micro soft. EventHub.|entityName|
|ActiveConnections|ActiveConnections|Aantal|Average|Totaal aantal actieve verbindingen voor micro soft. EventHub.|Geen dimensies|
|ConnectionsOpened|Geopende verbindingen.|Aantal|Average|Geopende verbindingen voor micro soft. EventHub.|entityName|
|ConnectionsClosed|Verbindingen gesloten.|Aantal|Average|Gesloten verbindingen voor micro soft. EventHub.|entityName|
|CaptureBacklog|Achterstand vastleggen.|Aantal|Totaal|Achterstand vastleggen voor micro soft. EventHub.|entityName|
|CapturedMessages|Vastgelegde berichten.|Aantal|Totaal|Vastgelegde berichten voor micro soft. EventHub.|entityName|
|CapturedBytes|Vastgelegde bytes.|Bytes|Totaal|Vastgelegde bytes voor micro soft. EventHub.|entityName|
|Grootte|Grootte|Bytes|Average|Grootte van een EventHub in bytes.|entityName|
|INREQS|Binnenkomende aanvragen (afgeschaft)|Aantal|Totaal|Totaal aantal binnenkomende verzend aanvragen voor een naam ruimte (afgeschaft)|Geen dimensies|
|SUCCREQ|Geslaagde aanvragen (afgeschaft)|Aantal|Totaal|Totaal aantal geslaagde aanvragen voor een naam ruimte (afgeschaft)|Geen dimensies|
|FAILREQ|Mislukte aanvragen (afgeschaft)|Aantal|Totaal|Totaal aantal mislukte aanvragen voor een naam ruimte (afgeschaft)|Geen dimensies|
|SVRBSY|Fouten bij server bezet (afgeschaft)|Aantal|Totaal|Totaal aantal fouten bij server bezet voor een naam ruimte (afgeschaft)|Geen dimensies|
|INTERer|Interne server fouten (afgeschaft)|Aantal|Totaal|Totaal aantal interne server fouten voor een naam ruimte (afgeschaft)|Geen dimensies|
|MISCERR|Andere fouten (afgeschaft)|Aantal|Totaal|Totaal aantal mislukte aanvragen voor een naam ruimte (afgeschaft)|Geen dimensies|
|INMSGS|Binnenkomende berichten (afgeschaft) (afgeschaft)|Aantal|Totaal|Totaal aantal inkomende berichten voor een naam ruimte. Deze metriek is afgeschaft. Gebruik in plaats daarvan de metriek van binnenkomende berichten (afgeschaft)|Geen dimensies|
|EHINMSGS|Inkomende berichten (afgeschaft)|Aantal|Totaal|Totaal aantal inkomende berichten voor een naam ruimte (afgeschaft)|Geen dimensies|
|OUTMSGS|Uitgaande berichten (afgeschaft) (afgeschaft)|Aantal|Totaal|Totaal aantal uitgaande berichten voor een naam ruimte. Deze metriek is afgeschaft. Gebruik in plaats daarvan de metrische gegevens van uitgaande berichten (afgeschaft)|Geen dimensies|
|EHOUTMSGS|Uitgaande berichten (afgeschaft)|Aantal|Totaal|Totaal aantal uitgaande berichten voor een naam ruimte (afgeschaft)|Geen dimensies|
|EHINMBS|Binnenkomende bytes (afgeschaft) (afgeschaft)|Bytes|Totaal|Door Voer van binnenkomende berichten van Event hub voor een naam ruimte. Deze metriek is afgeschaft. Gebruik in plaats daarvan de metrische gegevens over binnenkomende bytes (afgeschaft)|Geen dimensies|
|EHINBYTES|Binnenkomende bytes (afgeschaft)|Bytes|Totaal|Door Voer van inkomende berichten van Event hub voor een naam ruimte (afgeschaft)|Geen dimensies|
|EHOUTMBS|Uitgaande bytes (afgeschaft) (afgeschaft)|Bytes|Totaal|Door Voer van Event hub voor een naam ruimte. Deze metriek is afgeschaft. Gebruik in plaats hiervan de metrische gegevens van uitgaande bytes (afgeschaft)|Geen dimensies|
|EHOUTBYTES|Uitgaande bytes (afgeschaft)|Bytes|Totaal|Door Voer van Event hub voor een naam ruimte (afgeschaft)|Geen dimensies|
|EHABL|Achterstallige berichten archiveren (afgeschaft)|Aantal|Totaal|Event hub-archief berichten in achterstand voor een naam ruimte (afgeschaft)|Geen dimensies|
|EHAMSGS|Berichten archiveren (afgeschaft)|Aantal|Totaal|Event hub heeft berichten gearchiveerd in een naam ruimte (afgeschaft)|Geen dimensies|
|EHAMBS|Berichten doorvoer archiveren (afgeschaft)|Bytes|Totaal|Door Voer van Event hub-bericht doorvoer in een naam ruimte (afgeschaft)|Geen dimensies|

## <a name="microsofteventhubclusters"></a>Micro soft. EventHub/clusters

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SuccessfulRequests|Geslaagde aanvragen (preview-versie)|Aantal|Totaal|Geslaagde aanvragen voor micro soft. EventHub. (Preview)|Geen dimensies|
|ServerErrors|Server fouten. (Preview)|Aantal|Totaal|Server fouten voor micro soft. EventHub. (Preview)|Geen dimensies|
|UserErrors|Gebruikers fouten. (Preview)|Aantal|Totaal|Gebruikers fouten voor micro soft. EventHub. (Preview)|Geen dimensies|
|QuotaExceededErrors|Quota overschreden fouten. (Preview)|Aantal|Totaal|Quotum overschrijdt fouten voor micro soft. EventHub. (Preview)|Geen dimensies|
|ThrottledRequests|Vertraagde aanvragen. (Preview)|Aantal|Totaal|Beperkte aanvragen voor micro soft. EventHub. (Preview)|Geen dimensies|
|IncomingRequests|Inkomende aanvragen (preview-versie)|Aantal|Totaal|Binnenkomende aanvragen voor micro soft. EventHub. (Preview)|Geen dimensies|
|IncomingMessages|Inkomende berichten (preview-versie)|Aantal|Totaal|Binnenkomende berichten voor micro soft. EventHub. (Preview)|Geen dimensies|
|OutgoingMessages|Uitgaande berichten (preview-versie)|Aantal|Totaal|Uitgaande berichten voor micro soft. EventHub. (Preview)|Geen dimensies|
|IncomingBytes|Binnenkomende bytes. (Preview)|Bytes|Totaal|Binnenkomende bytes voor micro soft. EventHub. (Preview)|Geen dimensies|
|OutgoingBytes|Uitgaande bytes. (Preview)|Bytes|Totaal|Uitgaande bytes voor micro soft. EventHub. (Preview)|Geen dimensies|
|ActiveConnections|ActiveConnection (preview-versie)|Aantal|Average|Totaal aantal actieve verbindingen voor micro soft. EventHub. (Preview)|Geen dimensies|
|ConnectionsOpened|Geopende verbindingen. (Preview)|Aantal|Average|Geopende verbindingen voor micro soft. EventHub. (Preview)|Geen dimensies|
|ConnectionsClosed|Verbindingen gesloten. (Preview)|Aantal|Average|Gesloten verbindingen voor micro soft. EventHub. (Preview)|Geen dimensies|
|CaptureBacklog|Achterstand vastleggen. (Preview)|Aantal|Totaal|Achterstand vastleggen voor micro soft. EventHub. (Preview)|Geen dimensies|
|CapturedMessages|Vastgelegde berichten. (Preview)|Aantal|Totaal|Vastgelegde berichten voor micro soft. EventHub. (Preview)|Geen dimensies|
|CapturedBytes|Vastgelegde bytes. (Preview)|Bytes|Totaal|Vastgelegde bytes voor micro soft. EventHub. (Preview)|Geen dimensies|
|CPU|CPU (preview-versie)|Procent|Maximum|CPU-gebruik voor het event hub-cluster als een percentage|Rol|
|AvailableMemory|Beschikbaar geheugen (preview-versie)|Aantal|Maximum|Beschikbaar geheugen voor het event hub-cluster in bytes|Rol|

## <a name="microsofthdinsightclusters"></a>Micro soft. HDInsight/clusters

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|GatewayRequests|Gateway aanvragen|Aantal|Totaal|Aantal gateway-aanvragen|ClusterDnsName, HTTP status|
|CategorizedGatewayRequests|Gecategoriseerde gateway aanvragen|Aantal|Totaal|Aantal gateway aanvragen per categorie (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HTTP status|
|NumActiveWorkers|Aantal actieve werk rollen|Aantal|Maximum|Aantal actieve werk rollen|ClusterDnsName, metrische waarde|

## <a name="microsoftinsightsautoscalesettings"></a>Micro soft. Insights/AutoscaleSettings

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ObservedMetricValue|Waargenomen metrische waarde|Aantal|Average|De waarde die wordt berekend door automatisch schalen wanneer deze wordt uitgevoerd|MetricTriggerSource|
|MetricThreshold|Drempel waarde voor metrische gegevens|Aantal|Average|De geconfigureerde drempel waarde voor automatisch schalen wanneer automatisch schalen is uitgevoerd.|MetricTriggerRule|
|ObservedCapacity|Waargenomen capaciteit|Aantal|Average|De capaciteit die is gerapporteerd voor automatisch schalen wanneer deze wordt uitgevoerd.|Geen dimensies|
|ScaleActionsInitiated|Schaal acties gestart|Aantal|Totaal|De richting van de schaal bewerking.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Micro soft. Insights/onderdelen

(Open bare preview)

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Beschikbaarheid|Procent|Average|Percentage voltooide beschikbaarheids tests|availabilityResult/naam, availabilityResult/locatie|
|availabilityResults/aantal|Beschikbaarheidstests|Aantal|Aantal|Aantal beschikbaarheids tests|availabilityResult/naam, availabilityResult/locatie, availabilityResult/geslaagd|
|availabilityResults/duur|Duur beschikbaarheids test|Milliseconden|Average|Duur beschikbaarheids test|availabilityResult/naam, availabilityResult/locatie, availabilityResult/geslaagd|
|browserTimings/networkDuration|Netwerk verbindings tijd voor laden van pagina|Milliseconden|Average|Tijd tussen de gebruikers aanvraag en de netwerk verbinding. Inclusief DNS-Zoek-en transport verbinding.|Geen dimensies|
|browserTimings/processingDuration|Verwerkings tijd van client|Milliseconden|Average|Tijd tussen het ontvangen van de laatste byte van een document totdat de DOM is geladen. Asynchrone aanvragen kunnen nog steeds worden verwerkt.|Geen dimensies|
|browserTimings/receiveDuration|Reactie tijd van ontvangst|Milliseconden|Average|Tijd tussen de eerste en laatste bytes, of tot de verbinding wordt verbroken.|Geen dimensies|
|browserTimings/sendDuration|Aanvraag tijd verzenden|Milliseconden|Average|Tijd tussen netwerk verbinding en ontvangst van de eerste byte.|Geen dimensies|
|browserTimings/totalDuration|Laad tijd van browser pagina|Milliseconden|Average|Tijd van de gebruikers aanvraag totdat DOM, opmaak modellen, scripts en installatie kopieën worden geladen.|Geen dimensies|
|afhankelijkheden/aantal|Afhankelijkheids aanroepen|Aantal|Aantal|Aantal aanroepen van de toepassing naar externe bronnen.|afhankelijkheid/type, afhankelijkheid/requests, afhankelijkheid/geslaagd, bewerking/synthetisch, Cloud/roleInstance, Cloud/rolnaam|
|afhankelijkheden/duur|Duur van afhankelijkheid|Milliseconden|Average|Duur van de aanroepen van de toepassing naar externe bronnen.|afhankelijkheid/type, afhankelijkheid/requests, afhankelijkheid/geslaagd, bewerking/synthetisch, Cloud/roleInstance, Cloud/rolnaam|
|afhankelijkheden/mislukt|Mislukte afhankelijkheids aanroepen|Aantal|Aantal|Aantal mislukte afhankelijkheids aanroepen van de toepassing naar externe bronnen.|afhankelijkheid/type, afhankelijkheid/requests, bewerking/synthetisch, Cloud/roleInstance, Cloud/rolnaam|
|Page views/aantal|Pagina weergaven|Aantal|Aantal|Aantal pagina weergaven.|bewerking/synthetisch|
|Page views/duur|Laad tijd pagina weergave|Milliseconden|Average|Laad tijd pagina weergave|bewerking/synthetisch|
|Performance Counters/requestExecutionTime|Uitvoerings tijd van de HTTP-aanvraag|Milliseconden|Average|Uitvoerings tijd van de meest recente aanvraag.|Cloud-roleInstance|
|Performance Counters/requestsInQueue|HTTP-aanvragen in de toepassings wachtrij|Aantal|Average|Lengte van de wachtrij voor toepassings aanvragen.|Cloud-roleInstance|
|Performance Counters/requestsPerSecond|Frequentie van HTTP-aanvragen|CountPerSecond|Average|Het aantal aanvragen voor de toepassing per seconde van ASP.NET.|Cloud-roleInstance|
|Performance Counters/exceptionsPerSecond|Uitzonderings frequentie|CountPerSecond|Average|Aantal verwerkte en onverwerkte uitzonde ringen die worden gerapporteerd aan Windows, inclusief .NET-uitzonde ringen en onbeheerde uitzonde ringen die worden geconverteerd naar .NET-uitzonde ringen.|Cloud-roleInstance|
|Performance Counters/processIOBytesPerSecond|I/o-frequentie van processen|BytesPerSecond|Average|Totaal aantal in bestanden, netwerk en apparaten gelezen en geschreven bytes per seconde.|Cloud-roleInstance|
|Performance Counters/processCpuPercentage|CPU verwerken|Procent|Average|Het percentage van de verstreken tijd dat alle proces threads de processor hebben gebruikt om instructies uit te voeren. Dit kan variëren tussen 0 en 100. Deze metrische gegevens duiden de prestaties van alleen het W3wp-proces aan.|Cloud-roleInstance|
|Performance Counters/processorCpuPercentage|Processor tijd|Procent|Average|Het percentage tijd dat de processor spendeert aan niet-inactieve threads.|Cloud-roleInstance|
|Performance Counters/memoryAvailableBytes|Beschikbaar geheugen|Bytes|Average|Fysiek geheugen dat direct beschikbaar is voor toewijzing aan een proces of voor systeem gebruik.|Cloud-roleInstance|
|Performance Counters/processPrivateBytes|Privé-bytes verwerken|Bytes|Average|Geheugen dat exclusief wordt toegewezen aan de processen van de bewaakte toepassing.|Cloud-roleInstance|
|aanvragen/duur|Server reactietijd|Milliseconden|Average|Tijd tussen het ontvangen van een HTTP-aanvraag en het volt ooien van het verzenden van het antwoord.|aanvraag-requests, aanvraag-resultCode, bewerking/synthetisch, Cloud/roleInstance, aanvraag/geslaagd, Cloud/rolnaam|
|aanvragen/aantal|Server aanvragen|Aantal|Aantal|Aantal voltooide HTTP-aanvragen.|aanvraag-requests, aanvraag-resultCode, bewerking/synthetisch, Cloud/roleInstance, aanvraag/geslaagd, Cloud/rolnaam|
|aanvragen/mislukt|Mislukte aanvragen|Aantal|Aantal|Het aantal HTTP-aanvragen dat is gemarkeerd als mislukt. In de meeste gevallen zijn dit aanvragen met een antwoord code > = 400 en niet gelijk is aan 401.|aanvraag-requests, aanvraag-resultCode, bewerking/synthetisch, Cloud/roleInstance, Cloud/rolnaam|
|aanvragen/frequentie|Aantal server aanvragen|CountPerSecond|Average|Aantal server aanvragen per seconde|aanvraag-requests, aanvraag-resultCode, bewerking/synthetisch, Cloud/roleInstance, aanvraag/geslaagd, Cloud/rolnaam|
|uitzonde ringen/aantal|Uitzonderingen|Aantal|Aantal|Totaal aantal niet-onderschepte uitzonde ringen.|Cloud/rolnaam, Cloud/roleInstance, client/type|
|uitzonde ringen/browser|Browseruitzonderingen|Aantal|Aantal|Aantal niet-onderschepte uitzonde ringen dat in de browser wordt gegenereerd.|Geen dimensies|
|uitzonde ringen/server|Server uitzonderingen|Aantal|Aantal|Aantal niet-onderschepte uitzonde ringen dat is opgetreden in de server toepassing.|Cloud/rolnaam, Cloud-roleInstance|
|traceringen/aantal|Traceringen|Aantal|Aantal|Aantal tracerings documenten|Trace/severityLevel, Operation/synthetisch, Cloud/rolnaam, Cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Micro soft.-sleutel kluis/-kluizen

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ServiceApiHit|Totaal aantal treffers in de service-API|Aantal|Aantal|Totaal aantal treffers in de service-API|Activity type, Activiteitsnummer|
|ServiceApiLatency|Algehele latentie van Service-API|Milliseconden|Average|Algemene latentie van Service-API-aanvragen|Activity type, Activiteitsnummer, status code|
|ServiceApiResult|Totale resultaten van Service-API|Aantal|Aantal|Totaal aantal resultaten van Service-API|Activity type, Activiteitsnummer, status code|

## <a name="microsoftkustoclusters"></a>Micro soft. Kusto/clusters

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CacheUtilization|Cache gebruik|Procent|Average|Gebruiks niveau in het cluster bereik|Geen|
|QueryDuration|Query duur|Milliseconden|Average|De duur van query's in seconden|Query status|
|IngestionUtilization|Opname gebruik|Procent|Average|Verhouding van gebruikte opname sleuven in het cluster|Geen|
|KeepAlive|Actief houden|Aantal|Average|Sanity-controle geeft aan dat het cluster reageert op query's|Geen|
|IngestionVolumeInMB|Opname volume (in MB)|Aantal|Totaal|Totaal volume van opgenomen gegevens aan het cluster (in MB)|Database|
|IngestionLatencyInSeconds|Opname latentie (in seconden)|Seconden|Average|Opname tijd van de bron (bijvoorbeeld bericht is in EventHub) naar het cluster in enkele seconden|Geen|
|EventProcessedForEventHubs|Verwerkte gebeurtenissen (voor Event Hubs)|Aantal|Totaal|Aantal gebeurtenissen dat door het cluster wordt verwerkt bij het opnemen vanuit Event hub|Geen|
|IngestionResult|Opname resultaat|Aantal|Aantal|Aantal opname bewerkingen|Status|
|CPU|CPU|Procent|Average|Niveau CPU-gebruik|Geen|
| ContinuousExportNumOfRecordsExported | Het aantal records dat wordt geëxporteerd in continue export | Aantal | Totaal | Het aantal records dat wordt geëxporteerd voor elk opslag artefact dat is geschreven tijdens de export bewerking  | Geen |
| ExportUtilization | Gebruik exporteren | Procent | Maximum | Gebruik exporteren | Geen |
| ContinuousExportPendingCount | Aantal doorlopend exporteren in behandeling | Aantal | Maximum | Het aantal in behandeling zijnde doorlopende export taken dat gereed is voor uitvoering | Geen |
| ContinuousExportMaxLatenessMinutes | Maximale achterstand voor continue export | Aantal | Maximum | De maximale tijd in minuten van alle doorlopende uitvoer die in behandeling zijn en gereed zijn voor uitvoering | Geen |

## <a name="microsoftlocationbasedservicesaccounts"></a>Micro soft. LocationBasedServices/accounts

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Gebruik|Gebruik|Aantal|Aantal|Aantal API-aanroepen|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Micro soft. Logic/werk stromen

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|RunsStarted|Uitvoeringen gestart|Aantal|Totaal|Aantal uitvoeringen van werk stroom gestart.|Geen dimensies|
|RunsCompleted|Uitvoeringen voltooid|Aantal|Totaal|Aantal voltooide werk stroom uitvoeringen.|Geen dimensies|
|RunsSucceeded|Geslaagde uitvoeringen|Aantal|Totaal|Aantal geslaagde werk stroom uitvoeringen.|Geen dimensies|
|RunsFailed|Uitvoeringen mislukt|Aantal|Totaal|Het aantal uitvoeringen van de werk stroom is mislukt.|Geen dimensies|
|RunsCancelled|Uitvoeringen geannuleerd|Aantal|Totaal|Aantal uitgevoerde werk stroom uitvoeringen geannuleerd.|Geen dimensies|
|RunLatency|Uitvoerings latentie|Seconden|Average|Latentie van voltooide werk stroom uitvoeringen.|Geen dimensies|
|RunSuccessLatency|Latentie van geslaagde uitvoering|Seconden|Average|Latentie van geslaagde werk stroom uitvoeringen.|Geen dimensies|
|RunThrottledEvents|Vertraagde gebeurtenissen uitvoeren|Aantal|Totaal|Aantal werk stroom acties of trigger vertraagde gebeurtenissen.|Geen dimensies|
|RunFailurePercentage|Percentage mislukte uitvoeringen|Procent|Totaal|Percentage mislukte werk stroom uitvoeringen.|Geen dimensies|
|ActionsStarted|Gestarte acties |Aantal|Totaal|Aantal gestarte werk stroom acties.|Geen dimensies|
|ActionsCompleted|Acties voltooid |Aantal|Totaal|Aantal voltooide werk stroom acties.|Geen dimensies|
|ActionsSucceeded|Acties geslaagd |Aantal|Totaal|Aantal geslaagde werk stroom acties.|Geen dimensies|
|ActionsFailed|Mislukte acties|Aantal|Totaal|Aantal mislukte werk stroom acties.|Geen dimensies|
|ActionsSkipped|Overgeslagen acties |Aantal|Totaal|Aantal overgeslagen werk stroom acties.|Geen dimensies|
|ActionLatency|Actie latentie |Seconden|Average|Latentie van voltooide werk stroom acties.|Geen dimensies|
|ActionSuccessLatency|Latentie geslaagde acties |Seconden|Average|Latentie van geslaagde werk stroom acties.|Geen dimensies|
|ActionThrottledEvents|Door actie vertraagde gebeurtenissen|Aantal|Totaal|Aantal door werk stroom actie vertraagde gebeurtenissen.|Geen dimensies|
|TriggersStarted|Triggers gestart |Aantal|Totaal|Aantal gestarte werk stroom triggers.|Geen dimensies|
|TriggersCompleted|Triggers voltooid |Aantal|Totaal|Aantal voltooide werk stroom triggers.|Geen dimensies|
|TriggersSucceeded|Geslaagde triggers |Aantal|Totaal|Aantal geslaagde werk stroom triggers.|Geen dimensies|
|TriggersFailed|Mislukte triggers |Aantal|Totaal|Aantal mislukte werk stroom triggers.|Geen dimensies|
|TriggersSkipped|Triggers overgeslagen|Aantal|Totaal|Aantal overgeslagen werk stroom triggers.|Geen dimensies|
|TriggersFired|Geactiveerde triggers |Aantal|Totaal|Aantal geactiveerde werk stroom triggers.|Geen dimensies|
|TriggerLatency|Latentie van trigger |Seconden|Average|Latentie van voltooide werk stroom triggers.|Geen dimensies|
|TriggerFireLatency|Brand latentie activeren |Seconden|Average|Latentie van geactiveerde werk stroom triggers.|Geen dimensies|
|TriggerSuccessLatency|Latentie van trigger geslaagd |Seconden|Average|Latentie van geslaagde werk stroom triggers.|Geen dimensies|
|TriggerThrottledEvents|Trigger beperkings gebeurtenissen|Aantal|Totaal|Aantal door werk stroom trigger vertraagde gebeurtenissen.|Geen dimensies|
|BillableActionExecutions|Factureer bare actie-uitvoeringen|Aantal|Totaal|Aantal uitvoeringen van werk stroom acties dat wordt gefactureerd.|Geen dimensies|
|BillableTriggerExecutions|Factureer bare trigger uitvoeringen|Aantal|Totaal|Aantal uitvoeringen van werk stroom trigger dat wordt gefactureerd.|Geen dimensies|
|TotalBillableExecutions|Totaal aantal factureer bare uitvoeringen|Aantal|Totaal|Aantal uitgevoerde werk stroom uitvoeringen dat wordt gefactureerd.|Geen dimensies|
|BillingUsageNativeOperation|Facturerings gebruik voor uitvoering van systeem eigen bewerkingen|Aantal|Totaal|Aantal uitvoeringen van de native bewerking dat wordt gefactureerd.|Geen dimensies|
|BillingUsageStandardConnector|Facturerings gebruik voor het uitvoeren van standaard-connectors|Aantal|Totaal|Aantal uitvoeringen van Standard-connector dat wordt gefactureerd.|Geen dimensies|
|BillingUsageStorageConsumption|Facturerings gebruik voor uitvoeringen van opslag verbruik|Aantal|Totaal|Aantal uitvoeringen van opslag verbruik dat wordt gefactureerd.|Geen dimensies|
|BillingUsageNativeOperation|Facturerings gebruik voor uitvoering van systeem eigen bewerkingen|Aantal|Totaal|Aantal uitvoeringen van de native bewerking dat wordt gefactureerd.|Geen dimensies|
|BillingUsageStandardConnector|Facturerings gebruik voor het uitvoeren van standaard-connectors|Aantal|Totaal|Aantal uitvoeringen van Standard-connector dat wordt gefactureerd.|Geen dimensies|
|BillingUsageStorageConsumption|Facturerings gebruik voor uitvoeringen van opslag verbruik|Aantal|Totaal|Aantal uitvoeringen van opslag verbruik dat wordt gefactureerd.|Geen dimensies|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Micro soft. Logic/integrationServiceEnvironments

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|RunsStarted|Uitvoeringen gestart|Aantal|Totaal|Aantal uitvoeringen van werk stroom gestart.|Geen dimensies|
|RunsCompleted|Uitvoeringen voltooid|Aantal|Totaal|Aantal voltooide werk stroom uitvoeringen.|Geen dimensies|
|RunsSucceeded|Geslaagde uitvoeringen|Aantal|Totaal|Aantal geslaagde werk stroom uitvoeringen.|Geen dimensies|
|RunsFailed|Uitvoeringen mislukt|Aantal|Totaal|Het aantal uitvoeringen van de werk stroom is mislukt.|Geen dimensies|
|RunsCancelled|Uitvoeringen geannuleerd|Aantal|Totaal|Aantal uitgevoerde werk stroom uitvoeringen geannuleerd.|Geen dimensies|
|RunLatency|Uitvoerings latentie|Seconden|Average|Latentie van voltooide werk stroom uitvoeringen.|Geen dimensies|
|RunSuccessLatency|Latentie van geslaagde uitvoering|Seconden|Average|Latentie van geslaagde werk stroom uitvoeringen.|Geen dimensies|
|RunThrottledEvents|Vertraagde gebeurtenissen uitvoeren|Aantal|Totaal|Aantal werk stroom acties of trigger vertraagde gebeurtenissen.|Geen dimensies|
|RunStartThrottledEvents|Vertraagde gebeurtenissen uitvoeren|Aantal|Totaal|Aantal uitgevoerde vertraagde gebeurtenissen voor de werk stroom.|Geen dimensies|
|RunFailurePercentage|Percentage mislukte uitvoeringen|Procent|Totaal|Percentage mislukte werk stroom uitvoeringen.|Geen dimensies|
|ActionsStarted|Gestarte acties |Aantal|Totaal|Aantal gestarte werk stroom acties.|Geen dimensies|
|ActionsCompleted|Acties voltooid |Aantal|Totaal|Aantal voltooide werk stroom acties.|Geen dimensies|
|ActionsSucceeded|Acties geslaagd |Aantal|Totaal|Aantal geslaagde werk stroom acties.|Geen dimensies|
|ActionsFailed|Mislukte acties |Aantal|Totaal|Aantal mislukte werk stroom acties.|Geen dimensies|
|ActionsSkipped|Overgeslagen acties |Aantal|Totaal|Aantal overgeslagen werk stroom acties.|Geen dimensies|
|ActionLatency|Actie latentie |Seconden|Average|Latentie van voltooide werk stroom acties.|Geen dimensies|
|ActionSuccessLatency|Latentie geslaagde acties |Seconden|Average|Latentie van geslaagde werk stroom acties.|Geen dimensies|
|ActionThrottledEvents|Door actie vertraagde gebeurtenissen|Aantal|Totaal|Aantal door werk stroom actie vertraagde gebeurtenissen.|Geen dimensies|
|TriggersStarted|Triggers gestart |Aantal|Totaal|Aantal gestarte werk stroom triggers.|Geen dimensies|
|TriggersCompleted|Triggers voltooid |Aantal|Totaal|Aantal voltooide werk stroom triggers.|Geen dimensies|
|TriggersSucceeded|Geslaagde triggers |Aantal|Totaal|Aantal geslaagde werk stroom triggers.|Geen dimensies|
|TriggersFailed|Mislukte triggers |Aantal|Totaal|Aantal mislukte werk stroom triggers.|Geen dimensies|
|TriggersSkipped|Triggers overgeslagen|Aantal|Totaal|Aantal overgeslagen werk stroom triggers.|Geen dimensies|
|TriggersFired|Geactiveerde triggers |Aantal|Totaal|Aantal geactiveerde werk stroom triggers.|Geen dimensies|
|TriggerLatency|Latentie van trigger |Seconden|Average|Latentie van voltooide werk stroom triggers.|Geen dimensies|
|TriggerFireLatency|Brand latentie activeren |Seconden|Average|Latentie van geactiveerde werk stroom triggers.|Geen dimensies|
|TriggerSuccessLatency|Latentie van trigger geslaagd |Seconden|Average|Latentie van geslaagde werk stroom triggers.|Geen dimensies|
|TriggerThrottledEvents|Trigger beperkings gebeurtenissen|Aantal|Totaal|Aantal door werk stroom trigger vertraagde gebeurtenissen.|Geen dimensies|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Gebruik van werk stroom processor voor Integratieserviceomgeving|Procent|Average|Gebruik van werk stroom processoren voor de integratie service omgeving.|Geen dimensies|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Geheugen gebruik van werk stroom voor Integratieserviceomgeving|Procent|Average|Geheugen gebruik van werk stroom voor de integratie service omgeving.|Geen dimensies|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Processor gebruik van connector voor Integratieserviceomgeving|Procent|Average|Het processor gebruik van de connector voor de integratie service omgeving.|Geen dimensies|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Geheugen gebruik van connector voor Integratieserviceomgeving|Procent|Average|Geheugen gebruik van connector voor de integratie service omgeving.|Geen dimensies|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Micro soft. MachineLearningServices/werk ruimten

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Voltooide uitvoeringen|Voltooide uitvoeringen|Aantal|Totaal|Het aantal uitvoeringen dat is voltooid voor deze werk ruimte|Scenario|
|Gestart uitvoeringen|Gestart uitvoeringen|Aantal|Totaal|Aantal uitvoeringen gestart voor deze werk ruimte|Scenario|
|Mislukte uitvoeringen|Mislukte uitvoeringen|Aantal|Totaal|Aantal uitvoeringen is mislukt voor deze werk ruimte|Scenario|

## <a name="microsoftmapsaccounts"></a>Micro soft. Maps/accounts

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Gebruik|Gebruik|Aantal|Aantal|Aantal API-aanroepen|ApiCategory, ApiName, ResultType, ResponseCode|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|Beschik baarheid van de Api's|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Micro soft. NetApp/netAppAccounts/capacityPools/volumes

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AverageOtherLatency|Gemiddelde andere latentie|MS/op|Average|De gemiddelde andere latentie (die niet lezen of schrijven is) in milliseconden per bewerking|Geen dimensies|
|AverageReadLatency|Gemiddelde lees latentie|MS/op|Average|Gemiddelde lees latentie in milliseconden per bewerking|Geen dimensies|
|AverageTotalLatency|Gemiddelde latentie in milliseconden|MS/op|Average|Gemiddelde totale latentie in milliseconden per bewerking|Geen dimensies|
|AverageWriteLatency|Gemiddelde schrijf latentie|MS/op|Average|Gemiddelde schrijf latentie in milliseconden per bewerking|Geen dimensies|
|FilesystemOtherOps|Andere OPS-bestands systeem|OPS|Average|Aantal andere bewerkingen van het bestands systeem (dat is niet lezen of schrijven)|Geen dimensies|
|FilesystemReadOps|OPS-lezen van bestands systeem|OPS|Average|Aantal lees bewerkingen van het bestands systeem|Geen dimensies|
|FilesystemTotalOps|Totaal aantal OPS-bestands systeem|OPS|Average|Som van alle bestandssysteem bewerkingen|Geen dimensies|
|FilesystemWriteOps|File System write OPS|OPS|Average|Aantal schrijf bewerkingen van het bestands systeem|Geen dimensies|
|IoBytesPerOtherOps|I/o-bytes per andere OPS|bytes/op|Average|Aantal in-en uitgaande bytes per andere bewerking (dat is niet lezen of schrijven)|Geen dimensies|
|IoBytesPerReadOps|I/o bytes per Read OPS|bytes/op|Average|Aantal bytes in/uit per Lees bewerking|Geen dimensies|
|IoBytesPerTotalOps|I/o-bytes per op alle bewerkingen|bytes/op|Average|Som van alle bewerkingen in/uit bytes|Geen dimensies|
|IoBytesPerWriteOps|I/o-bytes per write OPS|bytes/op|Average|Aantal in/uit bytes per schrijf bewerking|Geen dimensies|
|OtherIops|Andere IOPS|bewerkingen per seconde|Average|Andere in/uit-bewerking per seconde|Geen dimensies|
|OtherThroughput|Andere door Voer|MB|Average|Andere door Voer (dat is niet lezen of schrijven) in mega bytes per seconde|Geen dimensies|
|ReadIops|IOPS lezen|bewerkingen per seconde|Average|In-en uitvoer bewerkingen per seconde|Geen dimensies|
|ReadThroughput|Lees doorvoer|MB|Average|Lees doorvoer in mega bytes per seconde|Geen dimensies|
|TotalIops|Totaal aantal IOPS|bewerkingen per seconde|Average|Som van alle in-en uitvoer bewerkingen per seconde|Geen dimensies|
|TotalThroughput|Totale door Voer|MB|Average|Som van alle door Voer in mega bytes per seconde|Geen dimensies|
|VolumeAllocatedSize|Grootte van toegewezen volume|Bytes|Average|Toegewezen grootte van het volume (niet de werkelijk gebruikte bytes)|Geen dimensies|
|VolumeLogicalSize|Logische volume grootte|Bytes|Average|Logische grootte van het volume (gebruikte bytes)|Geen dimensies|
|VolumeSnapshotSize|Grootte van moment opname van volume|Bytes|Average|Grootte van alle moment opnamen in volume|Geen dimensies|
|WriteIops|IOPS schrijven|bewerkingen per seconde|Average|In-en uitvoer bewerkingen per seconde|Geen dimensies|
|WriteThroughput|Schrijf doorvoer|MB|Average|Schrijf doorvoer in mega bytes per seconde|Geen dimensies|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Micro soft. NetApp/netAppAccounts/capacityPools

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Grootte toegewezen volume groep|Bytes|Average|Toegewezen grootte van de pool (niet de werkelijk gebruikte bytes)|Geen dimensies|
|VolumePoolAllocatedUsed|Gebruikte volume groep|Bytes|Average|Gebruikte grootte van de pool is toegewezen|Geen dimensies|
|VolumePoolTotalLogicalSize|Totale logische grootte van volume groep|Bytes|Average|Som van de logische grootte van alle volumes die deel uitmaken van de groep|Geen dimensies|
|VolumePoolTotalSnapshotSize|Grootte van de moment opname van de volume groep|Bytes|Average|Som van alle moment opnamen in de pool|Geen dimensies|

## <a name="microsoftnetworknetworkinterfaces"></a>Micro soft. Network/networkInterfaces

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BytesSentRate|Verzonden bytes|Aantal|Totaal|Het aantal bytes dat de netwerk interface heeft verzonden|Geen dimensies|
|BytesReceivedRate|Ontvangen bytes|Aantal|Totaal|Het aantal bytes dat de netwerk interface heeft ontvangen|Geen dimensies|
|PacketsSentRate|Verzonden pakketten|Aantal|Totaal|Aantal pakketten dat de netwerk interface heeft verzonden|Geen dimensies|
|PacketsReceivedRate|Ontvangen pakketten|Aantal|Totaal|Aantal pakketten dat de netwerk interface heeft ontvangen|Geen dimensies|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|VipAvailability|Beschik baarheid gegevenspad|Aantal|Average|Gemiddelde Beschik baarheid van gegevens paden per tijds duur van Load Balancer|FrontendIPAddress, FrontendPort|
|DipAvailability|Status van Health probe|Aantal|Average|Gemiddelde status van Load Balancer Health probe per tijds duur|Protocol type, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Aantal bytes|Aantal|Totaal|Totaal aantal verzonden bytes binnen tijds periode|FrontendIPAddress, FrontendPort, direction|
|PacketCount|Aantal pakketten|Aantal|Totaal|Totaal aantal verzonden pakketten binnen tijds periode|FrontendIPAddress, FrontendPort, direction|
|SYNCount|SYN-aantal|Aantal|Totaal|Totaal aantal SYN-pakketten verzonden binnen tijds periode|FrontendIPAddress, FrontendPort, direction|
|SnatConnectionCount|Aantal SNAT-verbindingen|Aantal|Totaal|Totaal aantal nieuwe SNAT-verbindingen dat binnen een tijds periode is gemaakt|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Toegewezen SNAT-poorten (preview-versie)|Aantal|Totaal|Totaal aantal toegewezen SNAT-poorten binnen tijds periode|FrontendIPAddress, BackendIPAddress, protocol type|
|UsedSnatPorts|Gebruikte SNAT-poorten (preview-versie)|Aantal|Totaal|Totaal aantal SNAT-poorten gebruikt binnen tijds periode|FrontendIPAddress, BackendIPAddress, protocol type|

## <a name="microsoftnetworkdnszones"></a>Micro soft. Network/dnszones

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|QueryVolume|Query volume|Aantal|Totaal|Aantal verzonden query's voor een DNS-zone|Geen dimensies|
|RecordSetCount|Aantal record sets|Aantal|Maximum|Aantal record sets in een DNS-zone|Geen dimensies|
|RecordSetCapacityUtilization|Capaciteits gebruik van record sets|Procent|Maximum|Percentage van de set-capaciteit van de record die wordt gebruikt door een DNS-zone|Geen dimensies|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PacketsInDDoS|DDoS inkomende pakketten|CountPerSecond|Maximum|DDoS inkomende pakketten|Geen dimensies|
|PacketsDroppedDDoS|DDoS inkomende pakketten verwijderd|CountPerSecond|Maximum|DDoS inkomende pakketten verwijderd|Geen dimensies|
|PacketsForwardedDDoS|DDoS inkomende pakketten doorgestuurd|CountPerSecond|Maximum|DDoS inkomende pakketten doorgestuurd|Geen dimensies|
|TCPPacketsInDDoS|Binnenkomende TCP-pakketten DDoS|CountPerSecond|Maximum|Binnenkomende TCP-pakketten DDoS|Geen dimensies|
|TCPPacketsDroppedDDoS|DDoS binnenkomende TCP-pakketten|CountPerSecond|Maximum|DDoS binnenkomende TCP-pakketten|Geen dimensies|
|TCPPacketsForwardedDDoS|Doorgestuurde binnenkomende TCP-pakketten DDoS|CountPerSecond|Maximum|Doorgestuurde binnenkomende TCP-pakketten DDoS|Geen dimensies|
|UDPPacketsInDDoS|Binnenkomende UDP-pakketten DDoS|CountPerSecond|Maximum|Binnenkomende UDP-pakketten DDoS|Geen dimensies|
|UDPPacketsDroppedDDoS|Verwijderde binnenkomende UDP-pakketten DDoS|CountPerSecond|Maximum|Verwijderde binnenkomende UDP-pakketten DDoS|Geen dimensies|
|UDPPacketsForwardedDDoS|Door inkomende UDP-pakketten DDoS doorgestuurd|CountPerSecond|Maximum|Door inkomende UDP-pakketten DDoS doorgestuurd|Geen dimensies|
|BytesInDDoS|Binnenkomende bytes DDoS|BytesPerSecond|Maximum|Binnenkomende bytes DDoS|Geen dimensies|
|BytesDroppedDDoS|Binnenkomende bytes verloren DDoS|BytesPerSecond|Maximum|Binnenkomende bytes verloren DDoS|Geen dimensies|
|BytesForwardedDDoS|Doorgestuurde binnenkomende bytes DDoS|BytesPerSecond|Maximum|Doorgestuurde binnenkomende bytes DDoS|Geen dimensies|
|TCPBytesInDDoS|DDoS binnenkomende TCP-bytes|BytesPerSecond|Maximum|DDoS binnenkomende TCP-bytes|Geen dimensies|
|TCPBytesDroppedDDoS|DDoS binnenkomende TCP-bytes|BytesPerSecond|Maximum|DDoS binnenkomende TCP-bytes|Geen dimensies|
|TCPBytesForwardedDDoS|DDoS doorgestuurde binnenkomende TCP-bytes|BytesPerSecond|Maximum|DDoS doorgestuurde binnenkomende TCP-bytes|Geen dimensies|
|UDPBytesInDDoS|Binnenkomende UDP-bytes DDoS|BytesPerSecond|Maximum|Binnenkomende UDP-bytes DDoS|Geen dimensies|
|UDPBytesDroppedDDoS|Binnenkomend UDP-bytes verloren DDoS|BytesPerSecond|Maximum|Binnenkomend UDP-bytes verloren DDoS|Geen dimensies|
|UDPBytesForwardedDDoS|Doorgestuurde binnenkomende UDP-bytes DDoS|BytesPerSecond|Maximum|Doorgestuurde binnenkomende UDP-bytes DDoS|Geen dimensies|
|IfUnderDDoSAttack|Onder DDoS-aanval of niet|Aantal|Maximum|Onder DDoS-aanval of niet|Geen dimensies|
|DDoSTriggerTCPPackets|Binnenkomende TCP-pakketten om DDoS-beperking te activeren|CountPerSecond|Maximum|Binnenkomende TCP-pakketten om DDoS-beperking te activeren|Geen dimensies|
|DDoSTriggerUDPPackets|Binnenkomende UDP-pakketten om DDoS-beperking te activeren|CountPerSecond|Maximum|Binnenkomende UDP-pakketten om DDoS-beperking te activeren|Geen dimensies|
|DDoSTriggerSYNPackets|Inkomende SYN-pakketten om DDoS-beperking te activeren|CountPerSecond|Maximum|Inkomende SYN-pakketten om DDoS-beperking te activeren|Geen dimensies|
|VipAvailability|Beschik baarheid gegevenspad|Aantal|Average|Gemiddelde Beschik baarheid van IP-adressen per tijds duur|Port|
|ByteCount|Aantal bytes|Aantal|Totaal|Totaal aantal verzonden bytes binnen tijds periode|Poort, richting|
|PacketCount|Aantal pakketten|Aantal|Totaal|Totaal aantal verzonden pakketten binnen tijds periode|Poort, richting|
|SynCount|SYN-aantal|Aantal|Totaal|Totaal aantal SYN-pakketten verzonden binnen tijds periode|Poort, richting|

## <a name="microsoftnetworkazurefirewalls"></a>Micro soft. Network/azurefirewalls

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ApplicationRuleHit|Aantal treffers toepassings regels|Aantal|Totaal|Aantal keer dat toepassings regels zijn geraakt|Status, reden, protocol|
|NetworkRuleHit|Aantal treffers in netwerk regels|Aantal|Totaal|Aantal keren dat netwerk regels zijn geraakt|Status, reden, protocol|

## <a name="microsoftnetworkapplicationgateways"></a>Micro soft. Network/applicationGateways

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Doorvoer|Doorvoer|BytesPerSecond|Totaal|Aantal bytes per seconde dat de Application Gateway heeft bediend|Geen dimensies|
|UnhealthyHostCount|Aantal hosts met slechte status|Aantal|Average|Aantal beschadigde backend-hosts|BackendSettingsPool|
|healthyHostCount|Aantal goede hosts|Aantal|Average|Aantal gezonde backend-hosts|BackendSettingsPool|
|TotalRequests|Totaal aantal aanvragen|Aantal|Totaal|Aantal geslaagde aanvragen dat Application Gateway heeft bediend|BackendSettingsPool|
|FailedRequests|Mislukte aanvragen|Aantal|Totaal|Aantal mislukte aanvragen dat Application Gateway heeft bediend|BackendSettingsPool|
|ResponseStatus|Reactie status|Aantal|Totaal|Http-antwoord status geretourneerd door Application Gateway|HttpStatusGroup|
|CurrentConnections|Huidige verbindingen|Aantal|Totaal|Aantal actieve verbindingen dat tot stand is gebracht met Application Gateway|Geen dimensies|
|CapacityUnits|Huidige capaciteits eenheden|Aantal|Average|Verbruikte capaciteits eenheden|Geen dimensies|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Micro soft. Network/virtualNetworkGateways

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AverageBandwidth|Gateway-S2S-band breedte|BytesPerSecond|Average|Gemiddeld aantal site-naar-site-band breedte van een gateway in bytes per seconde|Geen dimensies|
|P2SBandwidth|Gateway P2S-band breedte|BytesPerSecond|Average|Gemiddelde Point-to-site band breedte van een gateway in bytes per seconde|Geen dimensies|
|P2SConnectionCount|Aantal P2S-verbindingen|Aantal|Maximum|Aantal Point-to-site-verbindingen van een gateway|Protocol|
|TunnelAverageBandwidth|Tunnel bandbreedte|BytesPerSecond|Average|Gemiddelde band breedte van een tunnel in bytes per seconde|ConnectionName, RemoteIP|
|TunnelEgressBytes|Bytes voor uitgaand tunnels|Bytes|Totaal|Uitgaande bytes van een tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Bytes van de tunnel ingang|Bytes|Totaal|Binnenkomende bytes van een tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Tunnel-uituitgangs pakketten|Aantal|Totaal|Aantal uitgaande pakketten van een tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Tunnel ingangs pakketten|Aantal|Totaal|Binnenkomend pakket aantal van een tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Uitschakeling van niet-overeenkomende TS-pakketten door tunnel|Aantal|Totaal|Aantal uitgevallen uitgaande pakketten van de selectie van de verkeers kiezer komen niet overeen met een tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Verloren gegane pakketten door de tunnel ingang TS komen niet overeen|Aantal|Totaal|Aantal inkomende pakketten in de verkeers selectie niet overeenkomen met een tunnel|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Micro soft. Network/expressRouteCircuits

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Bits die Azure per seconde binnenkomen|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Bits egressing Azure per seconde|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Micro soft. Network/expressRouteCircuits/peerings

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Bits die Azure per seconde binnenkomen|Geen dimensies|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Bits egressing Azure per seconde|Geen dimensies|

## <a name="microsoftnetworkconnections"></a>Micro soft. Network/Connections

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Bits die Azure per seconde binnenkomen|Geen dimensies|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Bits egressing Azure per seconde|Geen dimensies|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Micro soft. Network/trafficManagerProfiles

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|QpsByEndpoint|Query's op eind punt geretourneerd|Aantal|Totaal|Aantal keren dat een Traffic Manager eind punt is geretourneerd in het opgegeven tijds bestek|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Eindpunt status op eind punt|Aantal|Maximum|1 als de test status van een eind punt is ingeschakeld, 0 anders.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Micro soft. Network/networkWatchers/connectionMonitors

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Tests mislukt|Procent|Average|% van de controles van connectiviteits controle is mislukt|Geen dimensies|
|AverageRoundtripMs|Gem. retour tijd (MS)|Milliseconden|Average|Gemiddelde Round-retour tijd van het netwerk (MS) voor connectiviteits controle tests die zijn verzonden tussen de bron en het doel|Geen dimensies|

## <a name="microsoftnetworkfrontdoors"></a>Micro soft. Network/frontdoors

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|requestCount|Aantal aanvragen|Aantal|Totaal|Het aantal client aanvragen dat wordt geleverd door de HTTP/S-proxy|Http status, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Aanvraag grootte|Bytes|Totaal|Het aantal bytes dat is verzonden als aanvragen van clients naar de HTTP/S-proxy|Http status, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Grootte van antwoord|Bytes|Totaal|Het aantal bytes dat is verzonden als reacties van HTTP/S-proxy naar clients|Http status, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Aantal back-aanvragen|Aantal|Totaal|Het aantal aanvragen dat is verzonden vanaf de HTTP/S-proxy naar back-end|Http status, HttpStatusGroup, back-end|
|BackendRequestLatency|Latentie van back-upaanvraag|Milliseconden|Average|De tijd die wordt berekend vanaf het moment dat de aanvraag door de HTTP/S-proxy naar de back-end werd verzonden, totdat de HTTP/S-proxy de laatste antwoord byte van de back-end heeft ontvangen|Back-end|
|TotalLatency|Totale latentie|Milliseconden|Average|De tijd die wordt berekend vanaf het moment dat de client aanvraag door de HTTP/S-proxy werd ontvangen totdat de client de laatste antwoord byte van de HTTP/S-proxy heeft bevestigd|Http status, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Back-status percentage|Procent|Average|Het percentage geslaagde status controles van de HTTP/S-proxy naar back-end|Back-end, hosts|
|WebApplicationFirewallRequestCount|Aantal aanvragen voor Web Application firewall|Aantal|Totaal|Het aantal client aanvragen dat is verwerkt door de Web Application firewall|Beleidsnaam, regelnaam, actie|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Micro soft. notification hubs/naam ruimten/notification hubs

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|registratie. alle|Registratie bewerkingen|Aantal|Totaal|Het aantal voltooide registratie bewerkingen (gemaakte bijwerk query's en verwijderingen). |Geen dimensies|
|registratie. Create|Bewerkingen voor het maken van registratie|Aantal|Totaal|Het aantal gemaakte registraties.|Geen dimensies|
|registratie. update|Registratie-update bewerkingen|Aantal|Totaal|Het aantal voltooide registratie-updates.|Geen dimensies|
|registratie. ophalen|Lees bewerkingen voor registratie|Aantal|Totaal|Het aantal geslaagde registratie query's.|Geen dimensies|
|registratie. Delete|Verwijderings bewerkingen voor registratie|Aantal|Totaal|Het aantal voltooide registraties dat is verwijderd.|Geen dimensies|
|E-mail|Inkomende berichten|Aantal|Totaal|Het aantal geslaagde verzend-API-aanroepen. |Geen dimensies|
|inkomend. gepland|Geplande push meldingen verzonden|Aantal|Totaal|Geplande push meldingen geannuleerd|Geen dimensies|
|Binnenkomend. gepland. annuleren|Geplande push meldingen geannuleerd|Aantal|Totaal|Geplande push meldingen geannuleerd|Geen dimensies|
|gepland. in behandeling|Geplande meldingen in behandeling|Aantal|Totaal|Geplande meldingen in behandeling|Geen dimensies|
|installatie. alle|Bewerkingen voor installatie beheer|Aantal|Totaal|Bewerkingen voor installatie beheer|Geen dimensies|
|installatie. Get|Installatie bewerkingen ophalen|Aantal|Totaal|Installatie bewerkingen ophalen|Geen dimensies|
|installatie. upsert|Installatie bewerkingen maken of bijwerken|Aantal|Totaal|Installatie bewerkingen maken of bijwerken|Geen dimensies|
|installatie. patch|Patch-installatie bewerkingen|Aantal|Totaal|Patch-installatie bewerkingen|Geen dimensies|
|installatie. Delete|Installatie bewerkingen verwijderen|Aantal|Totaal|Installatie bewerkingen verwijderen|Geen dimensies|
|uitgaand. allpns. geslaagd|Geslaagde meldingen|Aantal|Totaal|Het aantal geslaagde meldingen.|Geen dimensies|
|uitgaande. allpns. invalidpayload|Payload-fouten|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de PNS een ongeldige Payload-fout heeft geretourneerd.|Geen dimensies|
|uitgaande. allpns. pnserror|Externe meldingen systeem fouten|Aantal|Totaal|Het aantal pushes dat is mislukt omdat er een probleem is opgetreden bij het communiceren met de PNS (er zijn verificatie problemen uitgesloten).|Geen dimensies|
|uitgaande. allpns. channelerror|Kanaal fouten|Aantal|Totaal|Het aantal pushes dat is mislukt omdat het kanaal ongeldig is en niet is gekoppeld aan de juiste app beperkt of verlopen.|Geen dimensies|
|uitgaande. allpns. badorexpiredchannel|Ongeldige of verlopen kanaal fouten|Aantal|Totaal|Het aantal pushes dat is mislukt omdat het kanaal/token-registratie in de registratie is verlopen of ongeldig is.|Geen dimensies|
|uitgaand. wns. geslaagd|Geslaagde meldingen WNS|Aantal|Totaal|Het aantal geslaagde meldingen.|Geen dimensies|
|uitgaande. wns. invalidcredentials|WNS-verificatie fouten (ongeldige referenties)|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd. (Windows Live herkent de referenties niet).|Geen dimensies|
|uitgaande. wns. badchannel|WNS ongeldige kanaal fout|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de kanaal in de registratie niet is herkend (WNS-status: 404 niet gevonden).|Geen dimensies|
|uitgaande. wns. expiredchannel|WNS-fout met verlopen kanaal|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de kanaal is verlopen (WNS-status: 410 verdwenen).|Geen dimensies|
|uitgaande. wns. throttled|WNS beperkte meldingen|Aantal|Totaal|Het aantal pushes dat is mislukt omdat WNS deze app beperkt (WNS-status: 406 niet toegestaan).|Geen dimensies|
|uitgaande. wns. tokenproviderunreachable|WNS-verificatie fouten (onbereikbaar)|Aantal|Totaal|Windows Live is niet bereikbaar.|Geen dimensies|
|uitgaande. wns. invalidtoken|WNS-verificatie fouten (ongeldig token)|Aantal|Totaal|Het token dat is gegeven aan WNS is niet geldig (WNS-status: 401 niet toegestaan).|Geen dimensies|
|uitgaande. wns. wrongtoken|WNS-autorisatie fouten (onjuist token)|Aantal|Totaal|Het token dat is gegeven aan WNS is geldig, maar voor een andere toepassing (WNS-status: 403 verboden). Dit kan gebeuren als de kanaal in de registratie is gekoppeld aan een andere app. Controleer of de client-app is gekoppeld aan dezelfde app waarvan de referenties zich in de notification hub bevinden.|Geen dimensies|
|uitgaande. wns. invalidnotificationformat|Ongeldige indeling van WNS-melding|Aantal|Totaal|De indeling van de melding is ongeldig (WNS-status: 400). Houd er rekening mee dat WNS niet alle ongeldige payloads weigert.|Geen dimensies|
|uitgaande. wns. invalidnotificationsize|Fout met ongeldige grootte van WNS-melding|Aantal|Totaal|De nettolading van de melding is te groot (WNS-status: 413).|Geen dimensies|
|uitgaande. wns. channelthrottled|WNS-kanaal beperkt|Aantal|Totaal|De melding is verwijderd omdat de kanaal in de registratie is beperkt (WNS-reactie header: X-WNS-notification status: channelThrottled).|Geen dimensies|
|uitgaande. wns. channeldisconnected|Verbinding met WNS-kanaal verbroken|Aantal|Totaal|De melding is verwijderd omdat de kanaal in de registratie is beperkt (WNS-reactie header: X-WNS-DeviceConnectionStatus: disconnected).|Geen dimensies|
|uitgaande. wns. dropd|WNS-verwijderde meldingen|Aantal|Totaal|De melding is verwijderd omdat de kanaal in de registratie is beperkt (X-WNS-notification status: verwijderd maar niet X-WNS-DeviceConnectionStatus: disconnected).|Geen dimensies|
|uitgaande. wns. pnserror|WNS-fouten|Aantal|Totaal|Er is geen melding bezorgd vanwege fouten in de communicatie met WNS.|Geen dimensies|
|uitgaande. wns. authenticationerror|WNS-verificatie fouten|Aantal|Totaal|Er is een melding niet bezorgd omdat er fouten zijn opgetreden tijdens de communicatie met Windows Live ongeldige referenties of een onjuist token.|Geen dimensies|
|uitgaand. apns. geslaagd|Geslaagde meldingen van APNS|Aantal|Totaal|Het aantal geslaagde meldingen.|Geen dimensies|
|uitgaand. apns. invalidcredentials|APNS-autorisatie fouten|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd.|Geen dimensies|
|uitgaand. apns. badchannel|Fout met ongeldige APNS-kanaal|Aantal|Totaal|Het aantal pushes dat is mislukt omdat het token ongeldig is (status code van het APNS-binaire Protocol: 8). Status code van het APNS-HTTP-protocol: 400 met ' BadDeviceToken ').|Geen dimensies|
|uitgaand. apns. expiredchannel|Fout bij verlopen van APNS-kanaal|Aantal|Totaal|Het aantal tokens dat ongeldig is gemaakt door het APNS-feedback kanaal.|Geen dimensies|
|uitgaand. apns. invalidnotificationsize|Fout door ongeldige grootte van APNS-melding|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de payload te groot is (status code van het binaire APNS-Protocol: 7).|Geen dimensies|
|uitgaand. apns. pnserror|APNS-fouten|Aantal|Totaal|Het aantal pushes dat is mislukt vanwege fouten in de communicatie met APNS.|Geen dimensies|
|uitgaand. GCM. geslaagd|Geslaagde meldingen GCM|Aantal|Totaal|Het aantal geslaagde meldingen.|Geen dimensies|
|uitgaande. GCM. invalidcredentials|GCM-verificatie fouten (ongeldige referenties)|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd.|Geen dimensies|
|uitgaande. GCM. badchannel|GCM ongeldige kanaal fout|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de registratie in de registratie niet is herkend (GCM-resultaat: ongeldige registratie).|Geen dimensies|
|uitgaande. GCM. expiredchannel|GCM-fout met verlopen kanaal|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de registratie in de registratie is verlopen (GCM resultaat: NotRegistered).|Geen dimensies|
|uitgaande. GCM. throttled|GCM beperkte meldingen|Aantal|Totaal|Het aantal pushes dat is mislukt omdat GCM deze app heeft beperkt (GCM-status code: 501-599 of resultaat: niet beschikbaar).|Geen dimensies|
|uitgaande. GCM. invalidnotificationformat|Ongeldige indeling van GCM-melding|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de payload niet juist is geformatteerd (GCM-resultaat: InvalidDataKey of InvalidTtl).|Geen dimensies|
|uitgaande. GCM. invalidnotificationsize|Fout met ongeldige grootte van GCM-melding|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de payload te groot is (GCM-resultaat: MessageTooBig).|Geen dimensies|
|uitgaande. GCM. wrongchannel|GCM onjuiste kanaal fout|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de registratie in de registratie niet is gekoppeld aan de huidige app (GCM-resultaat: InvalidPackageName).|Geen dimensies|
|uitgaande. GCM. pnserror|GCM-fouten|Aantal|Totaal|Het aantal pushes dat is mislukt vanwege fouten in de communicatie met GCM.|Geen dimensies|
|uitgaande. GCM. authenticationerror|GCM-verificatie fouten|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd, de referenties zijn geblokkeerd of de SenderId niet juist is geconfigureerd in de app (GCM resultaat: MismatchedSenderId).|Geen dimensies|
|uitgaand. mpns. geslaagd|Geslaagde meldingen MPNS|Aantal|Totaal|Het aantal geslaagde meldingen.|Geen dimensies|
|uitgaande. mpns. invalidcredentials|Ongeldige referenties MPNS|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd.|Geen dimensies|
|uitgaande. mpns. badchannel|MPNS ongeldige kanaal fout|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de kanaal in de registratie niet is herkend (MPNS-status: 404 niet gevonden).|Geen dimensies|
|uitgaande. mpns. throttled|MPNS beperkte meldingen|Aantal|Totaal|Het aantal pushes dat is mislukt omdat MPNS deze app beperkt (WNS MPNS: 406 niet toegestaan).|Geen dimensies|
|uitgaande. mpns. invalidnotificationformat|Ongeldige indeling van MPNS-melding|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de payload van de melding te groot is.|Geen dimensies|
|uitgaande. mpns. channeldisconnected|Verbinding met MPNS-kanaal verbroken|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de kanaal van de registratie is verbroken (MPNS-status: 412 niet gevonden).|Geen dimensies|
|uitgaande. mpns. dropd|MPNS-verwijderde meldingen|Aantal|Totaal|Het aantal pushes dat is verwijderd door MPNS (MPNS-reactie header: X-notification status: QueueFull of onderdrukt).|Geen dimensies|
|uitgaande. mpns. pnserror|MPNS-fouten|Aantal|Totaal|Het aantal pushes dat is mislukt vanwege fouten in de communicatie met MPNS.|Geen dimensies|
|uitgaande. mpns. authenticationerror|MPNS-verificatie fouten|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd.|Geen dimensies|
|notificationhub. pushes|Alle uitgaande meldingen|Aantal|Totaal|Alle uitgaande meldingen van de notification hub|Geen dimensies|
|binnenkomende. alle. aanvragen|Alle inkomende aanvragen|Aantal|Totaal|Totaal aantal binnenkomende aanvragen voor een notification hub|Geen dimensies|
|inkomend. alle. failedrequests|Alle binnenkomende mislukte aanvragen|Aantal|Totaal|Totaal aantal binnenkomende mislukte aanvragen voor een notification hub|Geen dimensies|

## <a name="microsoftoperationalinsightsworkspaces"></a>Micro soft. OperationalInsights/werk ruimten

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Average_% vrije inodes|% Vrije inodes|Aantal|Average|Average_% vrije inodes|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_% beschik bare ruimte|Percentage beschik bare ruimte|Aantal|Average|Average_% beschik bare ruimte|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_% gebruikte inodes|% Gebruikte inodes|Aantal|Average|Average_% gebruikte inodes|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_ percentage gebruikte ruimte|Percentage gebruikte ruimte|Aantal|Average|Average_ percentage gebruikte ruimte|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk Lees bewerkingen in bytes per seconde|Gelezen bytes per seconde|Aantal|Average|Average_Disk Lees bewerkingen in bytes per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk Lees bewerkingen per seconde|Lees bewerkingen per seconde|Aantal|Average|Average_Disk Lees bewerkingen per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk-overdrachten per seconde|Schijf overdrachten per seconde|Aantal|Average|Average_Disk-overdrachten per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk geschreven bytes per seconde|Geschreven bytes per seconde|Aantal|Average|Average_Disk geschreven bytes per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk schrijf bewerkingen per seconde|Schrijf bewerkingen per seconde|Aantal|Average|Average_Disk schrijf bewerkingen per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Free mega bytes|Beschik bare mega bytes|Aantal|Average|Average_Free mega bytes|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Logical schijf bytes per seconde|Bytes van logische schijf per seconde|Aantal|Average|Average_Logical schijf bytes per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_ percentage beschikbaar geheugen|Percentage beschikbaar geheugen|Aantal|Average|Average_ percentage beschikbaar geheugen|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_% beschik bare wissel ruimte|Percentage beschik bare wissel ruimte|Aantal|Average|Average_% beschik bare wissel ruimte|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_ percentage gebruikt geheugen|Percentage gebruikt geheugen|Aantal|Average|Average_ percentage gebruikt geheugen|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_% gebruikte wissel ruimte|Percentage gebruikte wissel ruimte|Aantal|Average|Average_% gebruikte wissel ruimte|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Available MB geheugen|Beschikbaar geheugen in mega bytes|Aantal|Average|Average_Available MB geheugen|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Available mega bytes swap|Beschik bare mega bytes wisselen|Aantal|Average|Average_Available mega bytes swap|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Page Lees bewerkingen per seconde|Gelezen pagina's per seconde|Aantal|Average|Average_Page Lees bewerkingen per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Page schrijf bewerkingen per seconde|Geschreven pagina's per seconde|Aantal|Average|Average_Page schrijf bewerkingen per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Pages per seconde|Pagina's per seconde|Aantal|Average|Average_Pages per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Used mega bytes swap-ruimte|Gebruikte MB wissel ruimte|Aantal|Average|Average_Used mega bytes swap-ruimte|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Used Memory MB|Gebruikt geheugen Mbytes|Aantal|Average|Average_Used Memory MB|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Verzonden Average_Total bytes|Totaal aantal verzonden bytes|Aantal|Average|Verzonden Average_Total bytes|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Ontvangen Average_Total bytes|Totaal aantal ontvangen bytes|Aantal|Average|Ontvangen Average_Total bytes|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Total bytes|Totaal aantal bytes|Aantal|Average|Average_Total bytes|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Verzonden Average_Total-pakketten|Totaal aantal verzonden pakketten|Aantal|Average|Verzonden Average_Total-pakketten|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Ontvangen Average_Total-pakketten|Totaal aantal ontvangen pakketten|Aantal|Average|Ontvangen Average_Total-pakketten|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Total RX-fouten|Totaal aantal RX-fouten|Aantal|Average|Average_Total RX-fouten|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Total TX-fouten|Totaal aantal TX-fouten|Aantal|Average|Average_Total TX-fouten|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Total-conflicten|Totaal aantal conflicten|Aantal|Average|Average_Total-conflicten|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Avg. Lees tijd schijf|Gemiddelde Lees tijd schijf|Aantal|Average|Average_Avg. Lees tijd schijf|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Avg. Seconde/overdracht schijf|Gemiddelde tijd schijf overdracht|Aantal|Average|Average_Avg. Seconde/overdracht schijf|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Avg. Schrijf tijd schijf|Gemiddelde schrijf tijd schijf|Aantal|Average|Average_Avg. Schrijf tijd schijf|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Physical schijf bytes per seconde|Bytes van fysieke schijf per seconde|Aantal|Average|Average_Physical schijf bytes per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Pct geprivilegieerde tijd|Pct-geprivilegieerde tijd|Aantal|Average|Average_Pct geprivilegieerde tijd|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Pct gebruikers tijd|Pct-gebruikers tijd|Aantal|Average|Average_Pct gebruikers tijd|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Used geheugen kBytes|Gebruikte geheugen-kBytes|Aantal|Average|Average_Used geheugen kBytes|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Gedeeld Average_Virtual-geheugen|Virtueel gedeeld geheugen|Aantal|Average|Gedeeld Average_Virtual-geheugen|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_ percentage DPC-tijd|Percentage DPC-tijd|Aantal|Average|Average_ percentage DPC-tijd|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_ percentage niet-actieve tijd|Percentage niet-actieve tijd|Aantal|Average|Average_ percentage niet-actieve tijd|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Percentage interrupt-tijd Average_|Percentage interrupt-tijd|Aantal|Average|Percentage interrupt-tijd Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_% i/o-wacht tijd|% I/o-wacht tijd|Aantal|Average|Average_% i/o-wacht tijd|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_% Nice tijd|Percentage tijd in Nice|Aantal|Average|Average_% Nice tijd|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_ percentage tijd in beschermde modus|Percentage tijd in beschermde modus|Aantal|Average|Average_ percentage tijd in beschermde modus|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_% processor tijd|Percentage processor tijd|Aantal|Average|Average_% processor tijd|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_% gebruikers tijd|Percentage gebruikers tijd|Aantal|Average|Average_% gebruikers tijd|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Free fysiek geheugen|Vrij fysiek geheugen|Aantal|Average|Average_Free fysiek geheugen|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Free ruimte in Wissel bestanden|Vrije ruimte in wissel geheugen bestanden|Aantal|Average|Average_Free ruimte in Wissel bestanden|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Virtueel geheugen Average_Free|Vrij virtueel geheugen|Aantal|Average|Virtueel geheugen Average_Free|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Processes|Processen|Aantal|Average|Average_Processes|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Size opgeslagen in Wissel bestanden|Grootte opgeslagen in Wissel bestanden|Aantal|Average|Average_Size opgeslagen in Wissel bestanden|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Uptime|Systeem|Aantal|Average|Average_Uptime|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Users|Gebruikers|Aantal|Average|Average_Users|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Avg. Lees tijd schijf|Gemiddelde Lees tijd schijf|Aantal|Average|Average_Avg. Lees tijd schijf|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Avg. Schrijf tijd schijf|Gemiddelde schrijf tijd schijf|Aantal|Average|Average_Avg. Schrijf tijd schijf|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Wachtrij lengte van Average_Current-schijf|Huidige wachtrij lengte voor de schijf|Aantal|Average|Wachtrij lengte van Average_Current-schijf|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk Lees bewerkingen per seconde|Lees bewerkingen per seconde|Aantal|Average|Average_Disk Lees bewerkingen per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk-overdrachten per seconde|Schijf overdrachten per seconde|Aantal|Average|Average_Disk-overdrachten per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk schrijf bewerkingen per seconde|Schrijf bewerkingen per seconde|Aantal|Average|Average_Disk schrijf bewerkingen per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Free mega bytes|Beschik bare mega bytes|Aantal|Average|Average_Free mega bytes|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_% beschik bare ruimte|Percentage beschik bare ruimte|Aantal|Average|Average_% beschik bare ruimte|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Available mega bytes|Beschik bare Mbytes|Aantal|Average|Average_Available mega bytes|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_% toegewezen bytes in gebruik|% Toegewezen bytes in gebruik|Aantal|Average|Average_% toegewezen bytes in gebruik|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Ontvangen Average_Bytes per seconde|Ontvangen bytes per seconde|Aantal|Average|Ontvangen Average_Bytes per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Verzonden Average_Bytes per seconde|Verzonden bytes per seconde|Aantal|Average|Verzonden Average_Bytes per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Totaal aantal Average_Bytes per seconde|Totaal aantal bytes per seconde|Aantal|Average|Totaal aantal Average_Bytes per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_% processor tijd|Percentage processor tijd|Aantal|Average|Average_% processor tijd|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Lengte van Average_Processor-wachtrij|Lengte van de processor wachtrij|Aantal|Average|Lengte van Average_Processor-wachtrij|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Hart|Hart|Aantal|Totaal|Hart|Computer, OSType, versie, SourceComputerId|
|Bijwerken|Bijwerken|Aantal|Average|Bijwerken|Computer, product, classificatie, update State, optioneel, goedgekeurd|
|Gebeurtenis|Gebeurtenis|Aantal|Average|Gebeurtenis|Source, EventLog, computer, EventCategory, EventLevel, EventLevelName, Event gebeurtenis|

## <a name="microsoftpowerbidedicatedcapacities"></a>Micro soft. PowerBIDedicated/capaciteiten

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|QueryDuration|Query duur|Milliseconden|Average|DAX-query duur in laatste interval|Geen dimensies|
|QueryPoolJobQueueLength|Threads: lengte van taak wachtrij voor query pool|Aantal|Average|Aantal taken in de wachtrij van de query thread pool.|Geen dimensies|
|qpu_high_utilization_metric|Hoog gebruik van QPU|Aantal|Totaal|QPU hoog gebruik in de laatste minuut, 1 voor hoog QPU gebruik, anders 0|Geen dimensies|
|memory_metric|Geheugen|Bytes|Average|Geheugenmetabase. Bereik 0-3 GB voor a1, 0-5 GB voor a2, 0-10 GB voor a3, 0-25 GB voor A4, 0-50 GB voor A5 en 0-100 GB voor A6|Geen dimensies|
|memory_thrashing_metric|Geheugen overbelasting|Procent|Average|Gemiddeld geheugen overbelasting.|Geen dimensies|

## <a name="microsoftrelaynamespaces"></a>Micro soft. relay/naam ruimten

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ListenerConnections-geslaagd|ListenerConnections-geslaagd|Aantal|Totaal|Geslaagde ListenerConnections voor micro soft. relay.|entityName|
|ListenerConnections-client error|ListenerConnections-client error|Aantal|Totaal|Client error op ListenerConnections voor micro soft. relay.|entityName|
|ListenerConnections-server error|ListenerConnections-server error|Aantal|Totaal|Server error op ListenerConnections voor micro soft. relay.|entityName|
|SenderConnections-geslaagd|SenderConnections-geslaagd|Aantal|Totaal|Geslaagde SenderConnections voor micro soft. relay.|entityName|
|SenderConnections-client error|SenderConnections-client error|Aantal|Totaal|Client error op SenderConnections voor micro soft. relay.|entityName|
|SenderConnections-server error|SenderConnections-server error|Aantal|Totaal|Server error op SenderConnections voor micro soft. relay.|entityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Aantal|Totaal|Totale ListenerConnections voor micro soft. relay.|entityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Aantal|Totaal|Totaal aantal SenderConnections-aanvragen voor micro soft. relay.|entityName|
|ActiveConnections|ActiveConnections|Aantal|Totaal|Totaal aantal ActiveConnection voor micro soft. relay.|entityName|
|ActiveListeners|ActiveListeners|Aantal|Totaal|Totale ActiveListeners voor micro soft. relay.|entityName|
|BytesTransferred|BytesTransferred|Aantal|Totaal|Totale BytesTransferred voor micro soft. relay.|entityName|
|ListenerDisconnects|ListenerDisconnects|Aantal|Totaal|Totale ListenerDisconnects voor micro soft. relay.|entityName|
|SenderDisconnects|SenderDisconnects|Aantal|Totaal|Totale SenderDisconnects voor micro soft. relay.|entityName|

## <a name="microsoftsearchsearchservices"></a>Micro soft. Search/searchServices

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SearchLatency|Zoek latentie|Seconden|Average|Gemiddelde Zoek latentie voor de zoek service|Geen dimensies|
|SearchQueriesPerSecond|Zoek query's per seconde|CountPerSecond|Average|Zoek query's per seconde voor de zoek service|Geen dimensies|
|ThrottledSearchQueriesPercentage|Percentage vertraagde Zoek query's|Procent|Average|Percentage Zoek query's dat is beperkt tot de zoek service|Geen dimensies|

## <a name="microsoftservicebusnamespaces"></a>Micro soft. ServiceBus/naam ruimten

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SuccessfulRequests|Geslaagde aanvragen (preview-versie)|Aantal|Totaal|Totaal aantal geslaagde aanvragen voor een naam ruimte (preview-versie)|entityName|
|ServerErrors|Server fouten. (Preview)|Aantal|Totaal|Server fouten voor micro soft. ServiceBus. (Preview)|entityName|
|UserErrors|Gebruikers fouten. (Preview)|Aantal|Totaal|Gebruikers fouten voor micro soft. ServiceBus. (Preview)|entityName|
|ThrottledRequests|Vertraagde aanvragen. (Preview)|Aantal|Totaal|Beperkte aanvragen voor micro soft. ServiceBus. (Preview)|entityName|
|IncomingRequests|Inkomende aanvragen (preview-versie)|Aantal|Totaal|Binnenkomende aanvragen voor micro soft. ServiceBus. (Preview)|entityName|
|IncomingMessages|Inkomende berichten (preview-versie)|Aantal|Totaal|Binnenkomende berichten voor micro soft. ServiceBus. (Preview)|entityName|
|OutgoingMessages|Uitgaande berichten (preview-versie)|Aantal|Totaal|Uitgaande berichten voor micro soft. ServiceBus. (Preview)|entityName|
|ActiveConnections|ActiveConnection (preview-versie)|Aantal|Totaal|Totaal aantal actieve verbindingen voor micro soft. ServiceBus. (Preview)|Geen dimensies|
|Grootte|Grootte (preview-versie)|Bytes|Average|Grootte van een wachtrij/onderwerp in bytes. (Preview)|entityName|
|Berichten|Aantal berichten in een wachtrij/onderwerp. (Preview)|Aantal|Average|Aantal berichten in een wachtrij/onderwerp. (Preview)|entityName|
|ActiveMessages|Aantal actieve berichten in een wachtrij/onderwerp. (Preview)|Aantal|Average|Aantal actieve berichten in een wachtrij/onderwerp. (Preview)|entityName|
|DeadletteredMessages|Aantal onbestelbare berichten in een wachtrij/onderwerp. (Preview)|Aantal|Average|Aantal onbestelbare berichten in een wachtrij/onderwerp. (Preview)|entityName|
|ScheduledMessages|Aantal geplande berichten in een wachtrij/onderwerp. (Preview)|Aantal|Average|Aantal geplande berichten in een wachtrij/onderwerp. (Preview)|entityName|
|CPUXNS|CPU-gebruik per naam ruimte|Procent|Maximum|Metrische gegevens voor CPU-gebruik van service bus Premium-naam ruimte|Geen dimensies|
|WSXNS|Gebruik van geheugen grootte per naam ruimte|Procent|Maximum|Metrische gegevens over geheugen gebruik voor service bus Premium-naam ruimte|Geen dimensies|

## <a name="microsoftservicefabricmeshapplications"></a>Micro soft. ServiceFabricMesh/toepassingen

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Aantal|Average|CPU toegewezen aan deze container in millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Bytes|Average|Geheugen toegewezen aan deze container in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Aantal|Average|Werkelijk CPU-gebruik in millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Bytes|Average|Werkelijk geheugen gebruik in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Procent|Average|Gebruik van CPU voor deze container als percentage van AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Procent|Average|Gebruik van CPU voor deze container als percentage van AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Aantal|Average|Status van Service Fabric mesh-toepassing|ApplicationName, status|
|ServiceStatus|ServiceStatus|Aantal|Average|Integriteits status van een service in Service Fabric mesh-toepassing|ApplicationName, status, servicenaam|
|ServiceReplicaStatus|ServiceReplicaStatus|Aantal|Average|Integriteits status van een service replica in Service Fabric mesh-toepassing|ApplicationName, status, ServiceName, ServiceReplicaName|
|Container status|Container status|Aantal|Average|Status van de container in Service Fabric mesh-toepassing|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, status|
|RestartCount|RestartCount|Aantal|Average|Telling van een container in Service Fabric mesh-toepassing opnieuw starten|ApplicationName, status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Micro soft. SignalRService/Signa lering

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ConnectionCount|Aantal verbindingen|Aantal|Maximum|De hoeveelheid gebruikers verbinding.|Eindpunt|
|MessageCount|Aantal berichten|Aantal|Totaal|De totale hoeveelheid berichten.|Geen dimensies|
|InboundTraffic|Binnenkomend verkeer|Bytes|Totaal|Het inkomende verkeer van de service|Geen dimensies|
|OutboundTraffic|Uitgaand verkeer|Bytes|Totaal|Het uitgaande verkeer van de service|Geen dimensies|
|UserErrors|Gebruikers fouten|Procent|Maximum|Het percentage gebruikers fouten|Geen dimensies|
|SystemErrors|Systeem fouten|Procent|Maximum|Het percentage systeem fouten|Geen dimensies|

## <a name="microsoftsqlserversdatabases"></a>Micro soft. SQL/servers/data bases

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|allocated_data_storage|Toegewezen gegevens ruimte|Bytes|Average|Toegewezen gegevens ruimte. Niet van toepassing op data warehouses.|Geen dimensies|
|app_cpu_billed|App CPU gefactureerd|Aantal|Totaal|App CPU gefactureerd. Is van toepassing op serverloze data bases.|Geen dimensies|
|app_cpu_percent|CPU-percentage van app|Procent|Average|CPU-percentage van de app. Is van toepassing op serverloze data bases.|Geen dimensies|
|app_memory_percent|Percentage gebruikt geheugen van app|Procent|Average|Percentage gebruikt geheugen voor app. Is van toepassing op serverloze data bases.|Geen dimensies|
|blocked_by_firewall|Geblokkeerd door de firewall|Aantal|Totaal|Geblokkeerd door de firewall|Geen dimensies|
|cache_hit_percent|Percentage cache treffers|Procent|Maximum|Percentage van cache treffer. Is alleen van toepassing op data warehouses.|Geen dimensies|
|cache_used_percent|Percentage gebruikt cache|Procent|Maximum|Percentage gebruikt cache. Is alleen van toepassing op data warehouses.|Geen dimensies|
|connection_failed|Mislukte verbindingen|Aantal|Totaal|Mislukte verbindingen|Geen dimensies|
|connection_successful|Geslaagde verbindingen|Aantal|Totaal|Geslaagde verbindingen|Geen dimensies|
|cpu_percent|CPU-percentage|Procent|Average|CPU-percentage|Geen dimensies|
|cpu_limit|CPU-limiet|Aantal|Average|CPU-limiet. Is van toepassing op vCore-data bases.|Geen dimensies|
|cpu_used|CPU gebruikt|Aantal|Average|CPU gebruikt. Is van toepassing op vCore-data bases.|Geen dimensies|
|Constateer|Impassen|Aantal|Totaal|Impassen. Niet van toepassing op data warehouses.|Geen dimensies|
|dtu_limit|DTU-limiet|Aantal|Average|DTU-limiet. Is van toepassing op DTU-gebaseerde data bases.|Geen dimensies|
|dtu_consumption_percent|DTU-percentage|Procent|Average|DTU-percentage. Is van toepassing op DTU-gebaseerde data bases.|Geen dimensies|
|dtu_used|DTU gebruikt|Aantal|Average|DTU gebruikt. Is van toepassing op DTU-gebaseerde data bases.|Geen dimensies|
|dw_cpu_percent|CPU-percentage van DW-knooppunt niveau|Procent|Average|CPU-percentage van DW-knooppunt niveau|DwLogicalNodeId|
|dw_physical_data_read_percent|IO-percentage gegevens-i/o-knooppunt niveau|Procent|Average|IO-percentage gegevens-i/o-knooppunt niveau|DwLogicalNodeId|
|dwu_consumption_percent|Percentage DWU|Procent|Maximum|DWU-percentage. Is alleen van toepassing op data warehouses.|Geen dimensies|
|dwu_limit|Limiet voor DWU|Aantal|Maximum|DWU-limiet. Is alleen van toepassing op data warehouses.|Geen dimensies|
|dwu_used|DWU gebruikt|Aantal|Maximum|DWU gebruikt. Is alleen van toepassing op data warehouses.|Geen dimensies|
|local_tempdb_usage_percent|Lokaal TempDB-percentage|Procent|Average|Lokaal TempDB-percentage. Is alleen van toepassing op data warehouses.|Geen dimensies|
|log_write_percent|Logboek-IO-percentage|Procent|Average|Logboek-IO-percentage. Niet van toepassing op data warehouses.|Geen dimensies|
|physical_data_read_percent|Gegevens-I/O-percentage|Procent|Average|Gegevens-I/O-percentage|Geen dimensies|
|sessions_percent|Percentage sessies|Procent|Average|Percentage sessies. Niet van toepassing op data warehouses.|Geen dimensies|
|sqlserver_process_core_percent|Kern percentage van SQL Server proces|Procent|Maximum|Deze metrische waarde is een tijdelijke aanduiding die momenteel niet wordt ingevuld.|Geen dimensies|
|sqlserver_process_memory_percent|Percentage proces geheugen SQL Server|Procent|Maximum|Deze metrische waarde is een tijdelijke aanduiding die momenteel niet wordt ingevuld.|Geen dimensies|
|opslag|Gebruikte gegevens ruimte|Bytes|Maximum|Totale database grootte. Niet van toepassing op data warehouses.|Geen dimensies|
|storage_percent|Percentage gebruikte gegevens ruimte|Procent|Maximum|Percentage van de database grootte. Niet van toepassing op data warehouses of grootschalige-data bases.|Geen dimensies|
|tempdb_data_size|Data File grootte van tempdb|Aantal|Maximum|Data File grootte van tempdb-gegevens bestanden. Niet van toepassing op data warehouses. Deze metriek is beschikbaar voor data bases met behulp van het vCore-aankoop model of 100 DTU en hoger voor op DTU gebaseerde aankoop modellen.|Geen dimensies|
|tempdb_log_size|Grootte van logboek bestanden tempdb|Aantal|Maximum|Grootte van KB-logboek bestanden. Niet van toepassing op data warehouses. Deze metriek is beschikbaar voor data bases met behulp van het vCore-aankoop model of 100 DTU en hoger voor op DTU gebaseerde aankoop modellen.|Geen dimensies|
|tempdb_log_used_percent|Percentage gebruikt TempDB-logboek|Procent|Maximum|Percentage gebruikt TempDB-logboek. Niet van toepassing op data warehouses. Deze metriek is beschikbaar voor data bases met behulp van het vCore-aankoop model of 100 DTU en hoger voor op DTU gebaseerde aankoop modellen.|Geen dimensies|
|workers_percent|Percentage werk nemers|Procent|Average|Werknemers percentage. Niet van toepassing op data warehouses.|Geen dimensies|
|xtp_storage_percent|Percentage OLTP-opslag in het geheugen|Procent|Average|Percentage OLTP-opslag in het geheugen. Niet van toepassing op data warehouses.|Geen dimensies|

## <a name="microsoftsqlserverselasticpools"></a>Micro soft. SQL/servers/elasticPools

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|allocated_data_storage|Toegewezen gegevens ruimte|Bytes|Average|Toegewezen gegevens ruimte|Geen dimensies|
|allocated_data_storage_percent|Percentage toegewezen gegevens ruimte|Procent|Maximum|Percentage toegewezen gegevens ruimte|Geen dimensies|
|cpu_limit|CPU-limiet|Aantal|Average|CPU-limiet. Is van toepassing op op vCore gebaseerde elastische Pools.|Geen dimensies|
|cpu_percent|CPU-percentage|Procent|Average|CPU-percentage|Geen dimensies|
|cpu_used|CPU gebruikt|Aantal|Average|CPU gebruikt. Is van toepassing op op vCore gebaseerde elastische Pools.|Geen dimensies|
|dtu_consumption_percent|DTU-percentage|Procent|Average|DTU-percentage. Is van toepassing op op DTU gebaseerde elastische Pools.|Geen dimensies|
|eDTU_limit|eDTU-limiet|Aantal|Average|eDTU-limiet. Is van toepassing op op DTU gebaseerde elastische Pools.|Geen dimensies|
|eDTU_used|eDTU gebruikt|Aantal|Average|eDTU gebruikt. Is van toepassing op op DTU gebaseerde elastische Pools.|Geen dimensies|
|log_write_percent|Logboek-IO-percentage|Procent|Average|Logboek-IO-percentage|Geen dimensies|
|physical_data_read_percent|Gegevens-I/O-percentage|Procent|Average|Gegevens-I/O-percentage|Geen dimensies|
|sessions_percent|Percentage sessies|Procent|Average|Percentage sessies|Geen dimensies|
|storage_limit|Maximale grootte van gegevens|Bytes|Average|Opslag limiet|Geen dimensies|
|storage_percent|Percentage gebruikte gegevens ruimte||Procent|Average|Opslag percentage|Geen dimensies|
|storage_used|Gebruikte gegevens ruimte|Bytes|Average|Gebruikte opslag|Geen dimensies|
|sqlserver_process_core_percent|Kern percentage van SQL Server proces|Procent|Maximum|Deze metrische waarde is een tijdelijke aanduiding die momenteel niet wordt ingevuld.|Geen dimensies|
|sqlserver_process_memory_percent|Percentage proces geheugen SQL Server|Procent|Maximum|Deze metrische waarde is een tijdelijke aanduiding die momenteel niet wordt ingevuld.|Geen dimensies|
|tempdb_data_size|Data File grootte van tempdb|Aantal|Maximum|Data File grootte van tempdb-gegevens bestanden. Niet van toepassing op data warehouses. Deze metriek is beschikbaar voor data bases met behulp van het vCore-aankoop model of 100 DTU en hoger voor op DTU gebaseerde aankoop modellen.|Geen dimensies|
|tempdb_log_size|Grootte van logboek bestanden tempdb|Aantal|Maximum|Grootte van KB-logboek bestanden. Niet van toepassing op data warehouses. Deze metriek is beschikbaar voor data bases met behulp van het vCore-aankoop model of 100 DTU en hoger voor op DTU gebaseerde aankoop modellen.|Geen dimensies|
|tempdb_log_used_percent|Percentage gebruikt TempDB-logboek|Procent|Maximum|Percentage gebruikt TempDB-logboek. Niet van toepassing op data warehouses. Deze metriek is beschikbaar voor data bases met behulp van het vCore-aankoop model of 100 DTU en hoger voor op DTU gebaseerde aankoop modellen.|Geen dimensies|
|workers_percent|Percentage werk nemers|Procent|Average|Percentage werk nemers|Geen dimensies|
|xtp_storage_percent|Percentage OLTP-opslag in het geheugen|Procent|Average|Percentage OLTP-opslag in het geheugen|Geen dimensies|

## <a name="microsoftsqlmanagedinstances"></a>Micro soft. SQL/managedInstances

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|avg_cpu_percent|Gemiddeld CPU-percentage|Procent|Average|Gemiddeld CPU-percentage|Geen dimensies|
|io_bytes_read|Gelezen IO-bytes|Bytes|Average|Gelezen IO-bytes|Geen dimensies|
|io_requests|Aantal i/o-aanvragen|Aantal|Average|Aantal i/o-aanvragen|Geen dimensies|
|io_bytes_written|Geschreven IO-bytes|Bytes|Average|Geschreven IO-bytes|Geen dimensies|
|reserved_storage_mb|Gereserveerde opslag ruimte|Aantal|Average|Gereserveerde opslag ruimte|Geen dimensies|
|storage_space_used_mb|Gebruikte opslag ruimte|Aantal|Average|Gebruikte opslag ruimte|Geen dimensies|
|virtual_core_count|Aantal virtuele kernen|Aantal|Average|Aantal virtuele kernen|Geen dimensies|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|UsedCapacity|Gebruikte capaciteit|Bytes|Average|Gebruikte capaciteit van account|Geen dimensies|
|Transacties|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de dimensie ResponseType voor het aantal verschillende typen reacties.|ResponseType, geotype, ApiName, authenticatie|
|Binnenkomend|Binnenkomend|Bytes|Totaal|De hoeveelheid ingangs gegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgangs gegevens, in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie|
|SuccessServerLatency|Geslaagde server latentie|Milliseconden|Average|De gemiddelde latentie die door Azure Storage wordt gebruikt voor het verwerken van een geslaagde aanvraag, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|Geotype, ApiName, authenticatie|
|SuccessE2ELatency|Geslaagde E2E-latentie|Milliseconden|Average|De gemiddelde end-to-end-latentie van geslaagde aanvragen voor een opslag service of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|Het percentage Beschik baarheid voor de opslag service of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Micro soft. Storage/Storage accounts/blobServices

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BlobCapacity|BLOB-capaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de Blob service van het opslag account in bytes.|BlobType, Tier|
|BlobCount|Aantal blobs|Aantal|Totaal|Het aantal blobs in de Blob service van het opslag account.|BlobType|       |BlobCount|Aantal blobs|Aantal|Average|Het aantal blobs in de Blob service van het opslag account.|BlobType, Tier|
|ContainerCount|Aantal BLOB-containers|Aantal|Average|Het aantal containers in het Blob service van het opslag account.|Geen dimensies|
|IndexCapacity|Index capaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de index van de ADLS Gen2 (hiërarchisch) in bytes.|Geen dimensies|
|Transacties|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de dimensie ResponseType voor het aantal verschillende typen reacties.|ResponseType, geotype, ApiName, authenticatie|
|Binnenkomend|Binnenkomend|Bytes|Totaal|De hoeveelheid ingangs gegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgangs gegevens, in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie|
|SuccessServerLatency|Geslaagde server latentie|Milliseconden|Average|De gemiddelde latentie die door Azure Storage wordt gebruikt voor het verwerken van een geslaagde aanvraag, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|Geotype, ApiName, authenticatie|
|SuccessE2ELatency|Geslaagde E2E-latentie|Milliseconden|Average|De gemiddelde end-to-end-latentie van geslaagde aanvragen voor een opslag service of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|Het percentage Beschik baarheid voor de opslag service of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Micro soft. Storage/Storage accounts/fileServices

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|FileCapacity|Bestands capaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de bestands service van het opslag account in bytes.|Geen dimensies|
|FileCount|Aantal bestanden|Aantal|Average|Het aantal bestanden in de file-service van het opslag account.|Geen dimensies|
|FileShareCount|Aantal bestands shares|Aantal|Average|Het aantal bestands shares in de file-service van het opslag account.|Geen dimensies|
|Transacties|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de dimensie ResponseType voor het aantal verschillende typen reacties.|ResponseType, geotype, ApiName, authenticatie|
|Binnenkomend|Binnenkomend|Bytes|Totaal|De hoeveelheid ingangs gegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgangs gegevens, in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie|
|SuccessServerLatency|Geslaagde server latentie|Milliseconden|Average|De gemiddelde latentie die door Azure Storage wordt gebruikt voor het verwerken van een geslaagde aanvraag, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|Geotype, ApiName, authenticatie|
|SuccessE2ELatency|Geslaagde E2E-latentie|Milliseconden|Average|De gemiddelde end-to-end-latentie van geslaagde aanvragen voor een opslag service of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|Het percentage Beschik baarheid voor de opslag service of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Micro soft. Storage/Storage accounts/queueServices

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|QueueCapacity|Wachtrij capaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de Queue-service van het opslag account in bytes.|Geen dimensies|
|QueueCount|Aantal wachtrijen|Aantal|Average|Het aantal wacht rijen in de Queue-service van het opslag account.|Geen dimensies|
|QueueMessageCount|Aantal wachtrij berichten|Aantal|Average|Het geschatte aantal wachtrij berichten in de Queue-service van het opslag account.|Geen dimensies|
|Transacties|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de dimensie ResponseType voor het aantal verschillende typen reacties.|ResponseType, geotype, ApiName, authenticatie|
|Binnenkomend|Binnenkomend|Bytes|Totaal|De hoeveelheid ingangs gegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgangs gegevens, in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie|
|SuccessServerLatency|Geslaagde server latentie|Milliseconden|Average|De gemiddelde latentie die door Azure Storage wordt gebruikt voor het verwerken van een geslaagde aanvraag, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|Geotype, ApiName, authenticatie|
|SuccessE2ELatency|Geslaagde E2E-latentie|Milliseconden|Average|De gemiddelde end-to-end-latentie van geslaagde aanvragen voor een opslag service of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|Het percentage Beschik baarheid voor de opslag service of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie|

## <a name="microsoftstoragestorageaccountstableservices"></a>Micro soft. Storage/Storage accounts/tableServices

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TableCapacity|Tabel capaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de Table service van het opslag account in bytes.|Geen dimensies|
|TableCount|Aantal tabellen|Aantal|Average|Het aantal tabellen in de Table service van het opslag account.|Geen dimensies|
|TableEntityCount|Aantal tabel entiteiten|Aantal|Average|Het aantal tabel entiteiten in de Table service van het opslag account.|Geen dimensies|
|Transacties|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de dimensie ResponseType voor het aantal verschillende typen reacties.|ResponseType, geotype, ApiName, authenticatie|
|Binnenkomend|Binnenkomend|Bytes|Totaal|De hoeveelheid ingangs gegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgangs gegevens, in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie|
|SuccessServerLatency|Geslaagde server latentie|Milliseconden|Average|De gemiddelde latentie die door Azure Storage wordt gebruikt voor het verwerken van een geslaagde aanvraag, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|Geotype, ApiName, authenticatie|
|SuccessE2ELatency|Geslaagde E2E-latentie|Milliseconden|Average|De gemiddelde end-to-end-latentie van geslaagde aanvragen voor een opslag service of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|Het percentage Beschik baarheid voor de opslag service of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie|

## <a name="microsoftstoragesyncstoragesyncservices"></a>micro soft. storagesync/storageSyncServices

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Resultaat van synchronisatie sessie|Aantal|Average|Metriek die een waarde van 1 registreert telkens wanneer het server eindpunt een synchronisatie sessie met het Cloud eindpunt heeft voltooid|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Gesynchroniseerde bestanden|Aantal|Totaal|Aantal gesynchroniseerde bestanden|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Bestanden die niet worden gesynchroniseerd|Aantal|Totaal|Aantal bestanden dat niet kan worden gesynchroniseerd|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Gesynchroniseerde bytes|Bytes|Totaal|Totale bestands grootte die is overgedragen voor synchronisatie sessies|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Online status van de server|Aantal|Maximum|Metriek die een waarde van 1 registreert telkens wanneer de geregistreerde server een heartbeat met het Cloud eindpunt heeft vastgelegd|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Cloud lagen intrekken|Bytes|Totaal|De totale grootte van de gegevens die door de server zijn ingetrokken|ServerName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Micro soft. StreamAnalytics/streamingjobs

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ResourceUtilization|% Gebruik|Procent|Maximum|% Gebruik|Logischenaam, PartitionId|
|InputEvents|Invoer gebeurtenissen|Aantal|Totaal|Invoer gebeurtenissen|Logischenaam, PartitionId|
|InputEventBytes|Invoer gebeurtenis bytes|Bytes|Totaal|Invoer gebeurtenis bytes|Logischenaam, PartitionId|
|LateInputEvents|Late invoer gebeurtenissen|Aantal|Totaal|Late invoer gebeurtenissen|Logischenaam, PartitionId|
|OutputEvents|Uitvoer gebeurtenissen|Aantal|Totaal|Uitvoer gebeurtenissen|Logischenaam, PartitionId|
|ConversionErrors|Gegevens conversie fouten|Aantal|Totaal|Gegevens conversie fouten|Logischenaam, PartitionId|
|Fouten|Runtime-fouten|Aantal|Totaal|Runtime-fouten|Logischenaam, PartitionId|
|DroppedOrAdjustedEvents|Gebeurtenissen met een andere volg orde|Aantal|Totaal|Gebeurtenissen met een andere volg orde|Logischenaam, PartitionId|
|AMLCalloutRequests|Functie aanvragen|Aantal|Totaal|Functie aanvragen|Logischenaam, PartitionId|
|AMLCalloutFailedRequests|Mislukte functie aanvragen|Aantal|Totaal|Mislukte functie aanvragen|Logischenaam, PartitionId|
|AMLCalloutInputEvents|Functie gebeurtenissen|Aantal|Totaal|Functie gebeurtenissen|Logischenaam, PartitionId|
|DeserializationError|Fouten bij het deserialiseren van de invoer|Aantal|Totaal|Fouten bij het deserialiseren van de invoer|Logischenaam, PartitionId|
|EarlyInputEvents|Vroege invoer gebeurtenissen|Aantal|Totaal|Vroege invoer gebeurtenissen|Logischenaam, PartitionId|
|OutputWatermarkDelaySeconds|Watermerk vertraging|Seconden|Maximum|Watermerk vertraging|Logischenaam, PartitionId|
|InputEventsSourcesBacklogged|Inachterstand, invoer gebeurtenissen|Aantal|Maximum|Inachterstand, invoer gebeurtenissen|Logischenaam, PartitionId|
|InputEventsSourcesPerSecond|Invoer bronnen ontvangen|Aantal|Totaal|Invoer bronnen ontvangen|Logischenaam, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Micro soft. TimeSeriesInsights/omgevingen

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ontvangen berichten met ingang|Aantal|Totaal|Telling van berichten die zijn gelezen uit alle gebeurtenis bronnen van Event hub of IoT hub|Geen dimensies|
|IngressReceivedInvalidMessages|Ongeldige berichten ontvangen|Aantal|Totaal|Aantal ongeldige berichten gelezen uit alle gebeurtenis bronnen van Event hub of IoT hub|Geen dimensies|
|IngressReceivedBytes|Ontvangen bytes van binnenkomend verkeer|Bytes|Totaal|Het aantal gelezen bytes van alle gebeurtenis bronnen|Geen dimensies|
|IngressStoredBytes|In ingangs opgeslagen bytes|Bytes|Totaal|De totale grootte van de gebeurtenissen die zijn verwerkt en beschikbaar voor de query|Geen dimensies|
|IngressStoredEvents|Opgeslagen gebeurtenissen in ingangs|Aantal|Totaal|Aantal samengevoegde gebeurtenissen dat is verwerkt en beschikbaar is voor query|Geen dimensies|
|IngressReceivedMessagesTimeLag|Tijds vertraging ontvangen inkomende berichten|Seconden|Maximum|Verschil tussen het tijdstip waarop het bericht in de bron van de gebeurtenis in de wachtrij staat en de tijd die wordt verwerkt in ingangs|Geen dimensies|
|IngressReceivedMessagesCountLag|Vertraging ontvangen inkomende berichten van ingang|Aantal|Average|Verschil tussen het Volg nummer van de laatste bericht in de bron partitie en het Volg nummer van het bericht dat wordt verwerkt in ingangs gebeurtenissen|Geen dimensies|
|WarmStorageMaxProperties|Maximale eigenschappen van warme opslag|Aantal|Maximum|Maximum aantal eigenschappen dat is toegestaan door de omgeving voor de SKU van S1/S2 en het maximum aantal eigenschappen dat is toegestaan door de warme Store voor PAYG SKU|Geen dimensies|
|WarmStorageUsedProperties|Eigenschappen voor warme opslag gebruikt |Aantal|Maximum|Aantal eigenschappen dat wordt gebruikt door de omgeving voor de SKU van S1/S2 en het aantal eigenschappen dat door warme Store voor PAYG SKU wordt gebruikt|Geen dimensies|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Micro soft. TimeSeriesInsights/omgevingen/eventsources

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ontvangen berichten met ingang|Aantal|Totaal|Aantal berichten dat uit de gebeurtenis bron is gelezen|Geen dimensies|
|IngressReceivedInvalidMessages|Ongeldige berichten ontvangen|Aantal|Totaal|Aantal ongeldige berichten gelezen uit de gebeurtenis bron|Geen dimensies|
|IngressReceivedBytes|Ontvangen bytes van binnenkomend verkeer|Bytes|Totaal|Aantal gelezen bytes van de gebeurtenis bron|Geen dimensies|
|IngressStoredBytes|In ingangs opgeslagen bytes|Bytes|Totaal|De totale grootte van de gebeurtenissen die zijn verwerkt en beschikbaar voor de query|Geen dimensies|
|IngressStoredEvents|Opgeslagen gebeurtenissen in ingangs|Aantal|Totaal|Aantal samengevoegde gebeurtenissen dat is verwerkt en beschikbaar is voor query|Geen dimensies|
|IngressReceivedMessagesTimeLag|Tijds vertraging ontvangen inkomende berichten|Seconden|Maximum|Verschil tussen het tijdstip waarop het bericht in de bron van de gebeurtenis in de wachtrij staat en de tijd die wordt verwerkt in ingangs|Geen dimensies|
|IngressReceivedMessagesCountLag|Vertraging ontvangen inkomende berichten van ingang|Aantal|Average|Verschil tussen het Volg nummer van de laatste bericht in de bron partitie en het Volg nummer van het bericht dat wordt verwerkt in ingangs gebeurtenissen|Geen dimensies|
|WarmStorageMaxProperties|Maximale eigenschappen van warme opslag|Aantal|Maximum|Maximum aantal eigenschappen dat is toegestaan door de omgeving voor de SKU van S1/S2 en het maximum aantal eigenschappen dat is toegestaan door de warme Store voor PAYG SKU|Geen dimensies|
|WarmStorageUsedProperties|Eigenschappen voor warme opslag gebruikt |Aantal|Maximum|Aantal eigenschappen dat wordt gebruikt door de omgeving voor de SKU van S1/S2 en het aantal eigenschappen dat door warme Store voor PAYG SKU wordt gebruikt|Geen dimensies|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Micro soft. VMwareCloudSimple/informatie

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Gelezen bytes per seconde|BytesPerSecond|Average|Gemiddelde door Voer van schijf vanwege Lees bewerkingen gedurende de voorbeeld periode.|Geen dimensies|
|DiskWriteBytesPerSecond|Geschreven bytes per seconde|BytesPerSecond|Average|Gemiddelde doorvoer snelheid van schijven vanwege schrijf bewerkingen gedurende de voorbeeld periode.|Geen dimensies|
|Gelezen bytes op de schijf|Gelezen bytes op de schijf|Bytes|Totaal|Totale schijf doorvoer vanwege Lees bewerkingen gedurende de voorbeeld periode.|Geen dimensies|
|Geschreven bytes op de schijf|Geschreven bytes op de schijf|Bytes|Totaal|Totale schijf doorvoer vanwege schrijf bewerkingen gedurende de voorbeeld periode.|Geen dimensies|
|DiskReadOperations|Lees bewerkingen op de schijf|Aantal|Totaal|Het aantal i/o-Lees bewerkingen in de vorige voorbeeld periode. Houd er rekening mee dat deze bewerkingen variabele grootte kunnen hebben.|Geen dimensies|
|DiskWriteOperations|Schrijf bewerkingen op de schijf|Aantal|Totaal|Het aantal i/o-schrijf bewerkingen in de vorige voorbeeld periode. Houd er rekening mee dat deze bewerkingen variabele grootte kunnen hebben.|Geen dimensies|
|Lees bewerkingen op de schijf per seconde|Lees bewerkingen op de schijf per seconde|CountPerSecond|Average|Het gemiddelde aantal i/o-Lees bewerkingen in de vorige voorbeeld periode. Houd er rekening mee dat deze bewerkingen variabele grootte kunnen hebben.|Geen dimensies|
|Schrijf bewerkingen op de schijf per seconde|Schrijf bewerkingen op de schijf per seconde|CountPerSecond|Average|Het gemiddelde aantal i/o-schrijf bewerkingen in de vorige voorbeeld periode. Houd er rekening mee dat deze bewerkingen variabele grootte kunnen hebben.|Geen dimensies|
|DiskReadLatency|Lees latentie van schijf|Milliseconden|Average|Totale lees latentie. De som van de lees latentie van het apparaat en de kernel.|Geen dimensies|
|DiskWriteLatency|Schrijf latentie schijf|Milliseconden|Average|Totale schrijf latentie. De som van de latentie voor het schrijven van apparaten en kernels.|Geen dimensies|
|NetworkInBytesPerSecond|Netwerk in bytes per seconde|BytesPerSecond|Average|Gemiddelde netwerk doorvoer voor ontvangen verkeer.|Geen dimensies|
|NetworkOutBytesPerSecond|Netwerk uitgaande bytes per seconde|BytesPerSecond|Average|Gemiddelde netwerk doorvoer voor verzonden verkeer.|Geen dimensies|
|Netwerk in|Netwerk in|Bytes|Totaal|Totale netwerk doorvoer voor ontvangen verkeer.|Geen dimensies|
|Netwerk uit|Netwerk uit|Bytes|Totaal|Totale netwerk doorvoer voor verzonden verkeer.|Geen dimensies|
|MemoryUsed|Gebruikt geheugen|Bytes|Average|De hoeveelheid machine geheugen die wordt gebruikt door de VM.|Geen dimensies|
|MemoryGranted|Toegewezen geheugen|Bytes|Average|De hoeveelheid geheugen die door de host is toegewezen aan de virtuele machine. Er wordt geen geheugen verleend aan de host totdat deze één keer wordt aangevallen en het toegewezen geheugen kan worden uitgewisseld of geballon weg als de VMkernel het geheugen nodig heeft.|Geen dimensies|
|MemoryActive|Actief geheugen|Bytes|Average|De hoeveelheid geheugen die wordt gebruikt door de virtuele machine in het verleden kleine tijd venster. Dit is het ' True ' nummer van de hoeveelheid geheugen die momenteel nodig is voor de VM. Extra, ongebruikt geheugen kan worden uitgewisseld of geballond zonder gevolgen voor de prestaties van de gast.|Geen dimensies|
|Percentage CPU|Percentage CPU|Procent|Average|Het CPU-gebruik. Deze waarde wordt gerapporteerd met 100% voor alle processor kernen op het systeem. Een voor beeld: een twee richtings-VM met 50% van een systeem met vier kern geheugens is volledig met twee kernen.|Geen dimensies|
|PercentageCpuReady|Percentage CPU gereed|Milliseconden|Totaal|Beschik bare tijd is de tijd die nodig is voor het wachten op CPU ('s) in het afgelopen update-interval.|Geen dimensies|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CpuPercentage|CPU-percentage|Procent|Average|CPU-percentage|Instantie|
|MemoryPercentage|Geheugen percentage|Procent|Average|Geheugen percentage|Instantie|
|DiskQueueLength|Wachtrij lengte voor schijf|Aantal|Average|Wachtrij lengte voor schijf|Instantie|
|HttpQueueLength|Lengte van http-wachtrij|Aantal|Average|Lengte van http-wachtrij|Instantie|
|BytesReceived|Gegevens in|Bytes|Totaal|Gegevens in|Instantie|
|Bytes sent|Gegevens uit|Bytes|Totaal|Gegevens uit|Instantie|

## <a name="microsoftwebsites-excluding-functions"></a>Micro soft. web/sites (met uitzonde ring van functies)

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CpuTime|CPU-tijd|Seconden|Totaal|CPU-tijd|Instantie|
|Aanvragen|Aanvragen|Aantal|Totaal|Aanvragen|Instantie|
|BytesReceived|Gegevens in|Bytes|Totaal|Gegevens in|Instantie|
|Bytes sent|Gegevens uit|Bytes|Totaal|Gegevens uit|Instantie|
|Http101|Http 101|Aantal|Totaal|Http 101|Instantie|
|Http2xx|Http-2xx|Aantal|Totaal|Http-2xx|Instantie|
|Http3xx|HTTP-3xx|Aantal|Totaal|HTTP-3xx|Instantie|
|Http401|HTTP 401|Aantal|Totaal|HTTP 401|Instantie|
|Http403|HTTP 403|Aantal|Totaal|HTTP 403|Instantie|
|Http404|Http 404|Aantal|Totaal|Http 404|Instantie|
|Http406|Http 406|Aantal|Totaal|Http 406|Instantie|
|Http4xx|Http-4xx|Aantal|Totaal|Http-4xx|Instantie|
|Http5xx|Http-server fouten|Aantal|Totaal|Http-server fouten|Instantie|
|MemoryWorkingSet|Werkset geheugen|Bytes|Average|Werkset geheugen|Instantie|
|AverageMemoryWorkingSet|Gemiddelde werkset geheugen|Bytes|Average|Gemiddelde werkset geheugen|Instantie|
|AverageResponseTime|Gemiddelde reactie tijd|Seconden|Average|Gemiddelde reactie tijd|Instantie|
|AppConnections|Verbindingen|Aantal|Average|Verbindingen|Instantie|
|Formuleer|Aantal ingangen|Aantal|Average|Aantal ingangen|Instantie|
|Lijnen|Aantal threads|Aantal|Average|Aantal threads|Instantie|
|PrivateBytes|Privé-bytes|Bytes|Average|Privé-bytes|Instantie|
|IoReadBytesPerSecond|I/o gelezen bytes per seconde|BytesPerSecond|Totaal|I/o gelezen bytes per seconde|Instantie|
|IoWriteBytesPerSecond|I/o-schrijf bewerkingen in bytes per seconde|BytesPerSecond|Totaal|I/o-schrijf bewerkingen in bytes per seconde|Instantie|
|IoOtherBytesPerSecond|Andere i/o-bytes per seconde|BytesPerSecond|Totaal|Andere i/o-bytes per seconde|Instantie|
|IoReadOperationsPerSecond|I/o-Lees bewerkingen per seconde|BytesPerSecond|Totaal|I/o-Lees bewerkingen per seconde|Instantie|
|IoWriteOperationsPerSecond|I/o-schrijf bewerkingen per seconde|BytesPerSecond|Totaal|I/o-schrijf bewerkingen per seconde|Instantie|
|IoOtherOperationsPerSecond|Andere i/o-bewerkingen per seconde|BytesPerSecond|Totaal|Andere i/o-bewerkingen per seconde|Instantie|
|RequestsInApplicationQueue|Aanvragen in de wachtrij van de toepassing|Aantal|Average|Aanvragen in de wachtrij van de toepassing|Instantie|
|CurrentAssemblies|Huidige Assembly's|Aantal|Average|Huidige Assembly's|Instantie|
|TotalAppDomains|Totaal aantal app-domeinen|Aantal|Average|Totaal aantal app-domeinen|Instantie|
|TotalAppDomainsUnloaded|Totaal aantal verwijderde app-domeinen|Aantal|Average|Totaal aantal verwijderde app-domeinen|Instantie|
|Gen0Collections|Schone verzamelingen van 0 gen|Aantal|Totaal|Schone verzamelingen van 0 gen|Instantie|
|Gen1Collections|1 garbagecollection-verzamelingen|Aantal|Totaal|1 garbagecollection-verzamelingen|Instantie|
|Gen2Collections|Opschoon verzamelingen van generatie 2|Aantal|Totaal|Opschoon verzamelingen van generatie 2|Instantie|

## <a name="microsoftwebsites-functions"></a>Micro soft. web/sites (functies)

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BytesReceived|Gegevens in|Bytes|Totaal|Gegevens in|Instantie|
|Bytes sent|Gegevens uit|Bytes|Totaal|Gegevens uit|Instantie|
|Http5xx|Http-server fouten|Aantal|Totaal|Http-server fouten|Instantie|
|MemoryWorkingSet|Werkset geheugen|Bytes|Average|Werkset geheugen|Instantie|
|AverageMemoryWorkingSet|Gemiddelde werkset geheugen|Bytes|Average|Gemiddelde werkset geheugen|Instantie|
|FunctionExecutionUnits|Eenheden voor functie-uitvoering|MB/milliseconden|Totaal|[Eenheden voor functie-uitvoering](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Instantie|
|FunctionExecutionCount|Aantal functie-uitvoeringen|Aantal|Totaal|Aantal functie-uitvoeringen|Instantie|
|PrivateBytes|Privé-bytes|Bytes|Average|Privé-bytes|Instantie|
|IoReadBytesPerSecond|I/o gelezen bytes per seconde|BytesPerSecond|Totaal|I/o gelezen bytes per seconde|Instantie|
|IoWriteBytesPerSecond|I/o-schrijf bewerkingen in bytes per seconde|BytesPerSecond|Totaal|I/o-schrijf bewerkingen in bytes per seconde|Instantie|
|IoOtherBytesPerSecond|Andere i/o-bytes per seconde|BytesPerSecond|Totaal|Andere i/o-bytes per seconde|Instantie|
|IoReadOperationsPerSecond|I/o-Lees bewerkingen per seconde|BytesPerSecond|Totaal|I/o-Lees bewerkingen per seconde|Instantie|
|IoWriteOperationsPerSecond|I/o-schrijf bewerkingen per seconde|BytesPerSecond|Totaal|I/o-schrijf bewerkingen per seconde|Instantie|
|IoOtherOperationsPerSecond|Andere i/o-bewerkingen per seconde|BytesPerSecond|Totaal|Andere i/o-bewerkingen per seconde|Instantie|
|RequestsInApplicationQueue|Aanvragen in de wachtrij van de toepassing|Aantal|Average|Aanvragen in de wachtrij van de toepassing|Instantie|
|CurrentAssemblies|Huidige Assembly's|Aantal|Average|Huidige Assembly's|Instantie|
|TotalAppDomains|Totaal aantal app-domeinen|Aantal|Average|Totaal aantal app-domeinen|Instantie|
|TotalAppDomainsUnloaded|Totaal aantal verwijderde app-domeinen|Aantal|Average|Totaal aantal verwijderde app-domeinen|Instantie|
|Gen0Collections|Schone verzamelingen van 0 gen|Aantal|Totaal|Schone verzamelingen van 0 gen|Instantie|
|Gen1Collections|1 garbagecollection-verzamelingen|Aantal|Totaal|1 garbagecollection-verzamelingen|Instantie|
|Gen2Collections|Opschoon verzamelingen van generatie 2|Aantal|Totaal|Opschoon verzamelingen van generatie 2|Instantie|

## <a name="microsoftwebsitesslots"></a>Micro soft. web/sites/sleuven

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CpuTime|CPU-tijd|Seconden|Totaal|CPU-tijd|Instantie|
|Aanvragen|Aanvragen|Aantal|Totaal|Aanvragen|Instantie|
|BytesReceived|Gegevens in|Bytes|Totaal|Gegevens in|Instantie|
|Bytes sent|Gegevens uit|Bytes|Totaal|Gegevens uit|Instantie|
|Http101|Http 101|Aantal|Totaal|Http 101|Instantie|
|Http2xx|Http-2xx|Aantal|Totaal|Http-2xx|Instantie|
|Http3xx|HTTP-3xx|Aantal|Totaal|HTTP-3xx|Instantie|
|Http401|HTTP 401|Aantal|Totaal|HTTP 401|Instantie|
|Http403|HTTP 403|Aantal|Totaal|HTTP 403|Instantie|
|Http404|Http 404|Aantal|Totaal|Http 404|Instantie|
|Http406|Http 406|Aantal|Totaal|Http 406|Instantie|
|Http4xx|Http-4xx|Aantal|Totaal|Http-4xx|Instantie|
|Http5xx|Http-server fouten|Aantal|Totaal|Http-server fouten|Instantie|
|MemoryWorkingSet|Werkset geheugen|Bytes|Average|Werkset geheugen|Instantie|
|AverageMemoryWorkingSet|Gemiddelde werkset geheugen|Bytes|Average|Gemiddelde werkset geheugen|Instantie|
|AverageResponseTime|Gemiddelde reactie tijd|Seconden|Average|Gemiddelde reactie tijd|Instantie|
|FunctionExecutionUnits|Eenheden voor functie-uitvoering|Aantal|Totaal|Eenheden voor functie-uitvoering|Instantie|
|FunctionExecutionCount|Aantal functie-uitvoeringen|Aantal|Totaal|Aantal functie-uitvoeringen|Instantie|
|AppConnections|Verbindingen|Aantal|Average|Verbindingen|Instantie|
|Formuleer|Aantal ingangen|Aantal|Average|Aantal ingangen|Instantie|
|Lijnen|Aantal threads|Aantal|Average|Aantal threads|Instantie|
|PrivateBytes|Privé-bytes|Bytes|Average|Privé-bytes|Instantie|
|IoReadBytesPerSecond|I/o gelezen bytes per seconde|BytesPerSecond|Totaal|I/o gelezen bytes per seconde|Instantie|
|IoWriteBytesPerSecond|I/o-schrijf bewerkingen in bytes per seconde|BytesPerSecond|Totaal|I/o-schrijf bewerkingen in bytes per seconde|Instantie|
|IoOtherBytesPerSecond|Andere i/o-bytes per seconde|BytesPerSecond|Totaal|Andere i/o-bytes per seconde|Instantie|
|IoReadOperationsPerSecond|I/o-Lees bewerkingen per seconde|BytesPerSecond|Totaal|I/o-Lees bewerkingen per seconde|Instantie|
|IoWriteOperationsPerSecond|I/o-schrijf bewerkingen per seconde|BytesPerSecond|Totaal|I/o-schrijf bewerkingen per seconde|Instantie|
|IoOtherOperationsPerSecond|Andere i/o-bewerkingen per seconde|BytesPerSecond|Totaal|Andere i/o-bewerkingen per seconde|Instantie|
|RequestsInApplicationQueue|Aanvragen in de wachtrij van de toepassing|Aantal|Average|Aanvragen in de wachtrij van de toepassing|Instantie|
|CurrentAssemblies|Huidige Assembly's|Aantal|Average|Huidige Assembly's|Instantie|
|TotalAppDomains|Totaal aantal app-domeinen|Aantal|Average|Totaal aantal app-domeinen|Instantie|
|TotalAppDomainsUnloaded|Totaal aantal verwijderde app-domeinen|Aantal|Average|Totaal aantal verwijderde app-domeinen|Instantie|
|Gen0Collections|Schone verzamelingen van 0 gen|Aantal|Totaal|Schone verzamelingen van 0 gen|Instantie|
|Gen1Collections|1 garbagecollection-verzamelingen|Aantal|Totaal|1 garbagecollection-verzamelingen|Instantie|
|Gen2Collections|Opschoon verzamelingen van generatie 2|Aantal|Totaal|Opschoon verzamelingen van generatie 2|Instantie|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Micro soft. Web/hostingEnvironments/multiRolePools

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Aanvragen|Aanvragen|Aantal|Totaal|Aanvragen|Instantie|
|BytesReceived|Gegevens in|Bytes|Totaal|Gegevens in|Instantie|
|Bytes sent|Gegevens uit|Bytes|Totaal|Gegevens uit|Instantie|
|Http101|Http 101|Aantal|Totaal|Http 101|Instantie|
|Http2xx|Http-2xx|Aantal|Totaal|Http-2xx|Instantie|
|Http3xx|HTTP-3xx|Aantal|Totaal|HTTP-3xx|Instantie|
|Http401|HTTP 401|Aantal|Totaal|HTTP 401|Instantie|
|Http403|HTTP 403|Aantal|Totaal|HTTP 403|Instantie|
|Http404|Http 404|Aantal|Totaal|Http 404|Instantie|
|Http406|Http 406|Aantal|Totaal|Http 406|Instantie|
|Http4xx|Http-4xx|Aantal|Totaal|Http-4xx|Instantie|
|Http5xx|Http-server fouten|Aantal|Totaal|Http-server fouten|Instantie|
|AverageResponseTime|Gemiddelde reactie tijd|Seconden|Average|Gemiddelde reactie tijd|Instantie|
|CpuPercentage|CPU-percentage|Procent|Average|CPU-percentage|Instantie|
|MemoryPercentage|Geheugen percentage|Procent|Average|Geheugen percentage|Instantie|
|DiskQueueLength|Wachtrij lengte voor schijf|Aantal|Average|Wachtrij lengte voor schijf|Instantie|
|HttpQueueLength|Lengte van http-wachtrij|Aantal|Average|Lengte van http-wachtrij|Instantie|
|ActiveRequests|Actieve aanvragen|Aantal|Totaal|Actieve aanvragen|Instantie|
|TotalFrontEnds|Totale front-ends|Aantal|Average|Totale front-ends|Geen dimensies|
|SmallAppServicePlanInstances|Werk rollen voor kleine App Service plannen|Aantal|Average|Werk rollen voor kleine App Service plannen|Geen dimensies|
|MediumAppServicePlanInstances|Werk nemers met gemiddeld App Service plannen|Aantal|Average|Werk nemers met gemiddeld App Service plannen|Geen dimensies|
|LargeAppServicePlanInstances|Werk rollen voor grote App Service plannen|Aantal|Average|Werk rollen voor grote App Service plannen|Geen dimensies|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Micro soft. Web/hostingEnvironments/workerPools

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatie type|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|WorkersTotal|Totaal aantal werk rollen|Aantal|Average|Totaal aantal werk rollen|Geen dimensies|
|WorkersAvailable|Beschik bare werk nemers|Aantal|Average|Beschik bare werk nemers|Geen dimensies|
|WorkersUsed|Gebruikte werk rollen|Aantal|Average|Gebruikte werk rollen|Geen dimensies|
|CpuPercentage|CPU-percentage|Procent|Average|CPU-percentage|Instantie|
|MemoryPercentage|Geheugen percentage|Procent|Average|Geheugen percentage|Instantie|

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over metrische gegevens in Azure Monitor](data-platform.md)
* [Waarschuwingen maken voor metrische gegevens](alerts-overview.md)
* [Metrische gegevens exporteren naar opslag, Event hub of Log Analytics](resource-logs-overview.md)
