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
ms.openlocfilehash: 58c0db1f8200a757c95d7f66f9d821968e7e1839
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74121121"
---
# <a name="supported-metrics-with-azure-monitor"></a>Ondersteunde metrische gegevens met Azure Monitor

Azure Monitor biedt verschillende manieren om te communiceren met metrische gegevens, zoals het maken van grafieken in de portal, het openen ervan via de REST API of het opvragen van query's via Power shell of CLI. Hieronder vindt u een volledige lijst met alle metrische gegevens die momenteel beschikbaar zijn met de metrische pijp lijn van de Azure Monitor. Andere metrische gegevens zijn mogelijk beschikbaar in de portal of met behulp van verouderde Api's. Deze lijst hieronder bevat alleen metrische gegevens die beschikbaar zijn via de geconsolideerde Azure Monitor metrische pijp lijn. Als u deze metrische gegevens wilt opvragen en openen, gebruikt u de [2018-01-01-API-versie](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> Het verzenden van multidimensionale metrische gegevens via diagnostische instellingen wordt momenteel niet ondersteund. Metrische gegevens met dimensies worden geëxporteerd als platte eendimensionale metrische gegevens, als totaal van alle dimensiewaarden.
>
> *Een voorbeeld*: de metriek 'Binnenkomende berichten' voor een Event Hub kan worden verkend en uitgezet op wachtrijniveau. Wanneer de waarde wordt geëxporteerd via diagnostische instellingen, wordt deze echter voorgesteld als alle binnenkomende berichten voor alle wachtrijen in de Event Hub.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|qpu_metric|QPU|Count|Average|QPU. Bereik 0-100 voor S1, 0-200 voor S2 en 0-400 voor S4|ServerResourceType|
|memory_metric|Geheugen|Bytes|Average|Geheugenmetabase. Bereik 0-25 GB voor S1, 0-50 GB voor S2 en 0-100 GB voor S4|ServerResourceType|
|private_bytes_metric|Privé-bytes |Bytes|Average|De totale hoeveelheid geheugen die het Analysis Services engine proces en de mashup-container processen hebben toegewezen, exclusief geheugen gedeeld met andere processen.|ServerResourceType|
|virtual_bytes_metric|Virtuele bytes |Bytes|Average|De huidige grootte van de virtuele adres ruimte die Analysis Services engine proces en mashup-container processen gebruiken.|ServerResourceType|
|TotalConnectionRequests|Totaal aantal verbindings aanvragen|Count|Average|Totaal aantal verbindings aanvragen. Dit zijn ontvangsten.|ServerResourceType|
|SuccessfullConnectionsPerSec|Geslaagde verbindingen per seconde|CountPerSecond|Average|Snelheid van geslaagde verbindings voltooiingen.|ServerResourceType|
|TotalConnectionFailures|Totaal aantal verbindings fouten|Count|Average|Totaal aantal mislukte Verbindings pogingen.|ServerResourceType|
|CurrentUserSessions|Huidige gebruikers sessies|Count|Average|Het huidige aantal gebruikers sessies dat tot stand is gebracht.|ServerResourceType|
|QueryPoolBusyThreads|Query's pool bezette threads|Count|Average|Het aantal actieve threads in de query thread pool.|ServerResourceType|
|CommandPoolJobQueueLength|Wachtrij lengte van de opdracht pool taak|Count|Average|Aantal taken in de wachtrij van de opdracht thread pool.|ServerResourceType|
|ProcessingPoolJobQueueLength|Wachtrij lengte van de pool taak wordt verwerkt|Count|Average|Het aantal niet-I/O-taken in de wachtrij van de verwer kende thread pool.|ServerResourceType|
|CurrentConnections|Verbinding: huidige verbindingen|Count|Average|Het huidige aantal client verbindingen dat tot stand is gebracht.|ServerResourceType|
|CleanerCurrentPrice|Geheugen: huidige prijs opschonen|Count|Average|Huidige prijs van geheugen, $/byte/tijd), genormaliseerd naar 1000.|ServerResourceType|
|CleanerMemoryShrinkable|Geheugen: verkleinbaar geheugen|Bytes|Average|De hoeveelheid geheugen (in bytes) die wordt verwijderd door de achtergrond opschoning.|ServerResourceType|
|CleanerMemoryNonshrinkable|Geheugen: Removal-geheugen kan niet worden verkleind|Bytes|Average|Hoeveelheid geheugen (in bytes) die niet wordt verwijderd door de achtergrond opschoning.|ServerResourceType|
|MemoryUsage|Geheugen: geheugen gebruik|Bytes|Average|Geheugen gebruik van het Server proces zoals gebruikt bij het berekenen van de prijs voor het schonen van geheugen. Gelijk aan Counter Process\PrivateBytes plus de grootte van gegevens die zijn toegewezen door het geheugen, waarbij elk geheugen wordt genegeerd dat is toegewezen aan of toegewezen door de xVelocity in-Memory Analytics Engine (VertiPaq) die de geheugen limiet van xVelocity-engine overschrijdt.|ServerResourceType|
|MemoryLimitHard|Geheugen: vaste geheugen limiet|Bytes|Average|Vaste geheugen limiet, van configuratie bestand.|ServerResourceType|
|MemoryLimitHigh|Geheugen: hoge geheugen limiet|Bytes|Average|Hoge geheugen limiet, van configuratie bestand.|ServerResourceType|
|MemoryLimitLow|Geheugen: lage geheugen limiet|Bytes|Average|Limiet voor weinig geheugen, van configuratie bestand.|ServerResourceType|
|MemoryLimitVertiPaq|Geheugen: VertiPaq-geheugen limiet|Bytes|Average|In-Memory limiet, van configuratie bestand.|ServerResourceType|
|Quota|Geheugen: quotum|Bytes|Average|Huidig geheugen quotum, in bytes. Geheugen quota wordt ook wel een geheugen toekenning of geheugen reservering genoemd.|ServerResourceType|
|QuotaBlocked|Geheugen: quotum geblokkeerd|Count|Average|Het huidige aantal quotum aanvragen dat is geblokkeerd totdat andere geheugen quota zijn vrijgemaakt.|ServerResourceType|
|VertiPaqNonpaged|Geheugen: VertiPaq niet-wisselbaar|Bytes|Average|Geheugen bytes vergrendeld in de werkset voor gebruik door de in-Memory engine.|ServerResourceType|
|VertiPaqPaged|Geheugen: VertiPaq-pagina|Bytes|Average|Bytes van wisselbaar geheugen die in gebruik zijn voor in-Memory gegevens.|ServerResourceType|
|RowsReadPerSec|Verwerken: gelezen rijen per seconde|CountPerSecond|Average|Het aantal rijen dat van alle relationele data bases is gelezen.|ServerResourceType|
|RowsConvertedPerSec|Verwerken: geconverteerde rijen per seconde|CountPerSecond|Average|Het aantal rijen dat tijdens de verwerking is geconverteerd.|ServerResourceType|
|RowsWrittenPerSec|Verwerken: geschreven rijen per seconde|CountPerSecond|Average|Het aantal rijen dat tijdens de verwerking is geschreven.|ServerResourceType|
|CommandPoolBusyThreads|Threads: actieve threads van opdracht pool|Count|Average|Het aantal actieve threads in de opdracht thread pool.|ServerResourceType|
|CommandPoolIdleThreads|Threads: niet-actieve threads van opdracht pool|Count|Average|Het aantal niet-actieve threads in de opdracht thread pool.|ServerResourceType|
|LongParsingBusyThreads|Threads: bezette threads voor lang parseren|Count|Average|Het aantal actieve threads in de thread pool voor lang parseren.|ServerResourceType|
|LongParsingIdleThreads|Threads: niet-actieve threads voor lang parseren|Count|Average|Het aantal niet-actieve threads in de thread pool voor lang parseren.|ServerResourceType|
|LongParsingJobQueueLength|Threads: lengte van taak wachtrij voor lang parseren|Count|Average|Aantal taken in de wachtrij van de thread pool voor lang parseren.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Threads: bezig met verwerken van I/O-taak threads van pool|Count|Average|Het aantal threads waarmee I/O-taken worden uitgevoerd in de verwer kende thread pool.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Threads: bezig met het verwerken van niet-I/O-threads van de groep|Count|Average|Het aantal threads met niet-I/O-taken in de verwer kende thread pool.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Threads: lengte van I/O-taak wachtrij voor verwerking van groep|Count|Average|Het aantal I/O-taken in de wachtrij van de verwer kende thread pool.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Threads: niet-actieve I/O-taak threads van de groep verwerken|Count|Average|Het aantal niet-actieve threads voor I/O-taken in de verwer kende thread pool.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Threads: niet-I/O-threads van de groep worden verwerkt|Count|Average|Het aantal niet-actieve threads in de verwer kende thread pool dat is gereserveerd voor niet-I/O-taken.|ServerResourceType|
|QueryPoolIdleThreads|Threads: niet-actieve threads van query pool|Count|Average|Het aantal niet-actieve threads voor I/O-taken in de verwer kende thread pool.|ServerResourceType|
|QueryPoolJobQueueLength|Threads: lengte van taak wachtrij voor query pool|Count|Average|Aantal taken in de wachtrij van de query thread pool.|ServerResourceType|
|ShortParsingBusyThreads|Threads: bezette threads voor kort parseren|Count|Average|Het aantal actieve threads in de thread pool voor kort parseren.|ServerResourceType|
|ShortParsingIdleThreads|Threads: niet-actieve threads voor kort parseren|Count|Average|Het aantal niet-actieve threads in de thread pool voor kort parseren.|ServerResourceType|
|ShortParsingJobQueueLength|Threads: lengte van taak wachtrij voor kort parseren|Count|Average|Aantal taken in de wachtrij van de thread pool voor kort parseren.|ServerResourceType|
|memory_thrashing_metric|Geheugen overbelasting|Procent|Average|Gemiddeld geheugen overbelasting.|ServerResourceType|
|mashup_engine_qpu_metric|M-engine QPU|Count|Average|QPU-gebruik door mashup-engine processen|ServerResourceType|
|mashup_engine_memory_metric|M-engine geheugen|Bytes|Average|Geheugen gebruik door mashup-engine processen|ServerResourceType|
|mashup_engine_private_bytes_metric|M-engine-eigen bytes |Bytes|Average|De totale hoeveelheid geheugen-mashup-container processen zijn toegewezen, exclusief geheugen gedeeld met andere processen.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|M-engine virtuele bytes |Bytes|Average|De huidige grootte van de mashup-container processen van de virtuele adres ruimte wordt gebruikt.|ServerResourceType|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Aanvragen|Aanvragen|Count|Totaal|Het totale aantal gateway aanvragen in een bepaalde periode. Het kan worden gesegmenteerd door verschillende dimensies om u te helpen bij het vaststellen van problemen. |Locatie, BackendResponseCode, LastErrorReason, GatewayResponseCode|
|TotalRequests|Totaal aantal gateway-aanvragen|Count|Totaal|Het totale aantal gateway aanvragen in een bepaalde periode. Deze metriek is afgeschaft, wij raden u aan de nieuwe `Requests` metriek te gebruiken. |Locatie, hostnaam|
|SuccessfulRequests|Geslaagde gateway-aanvragen|Count|Totaal|Het totaal aantal geslaagde gateway aanvragen in een bepaalde periode. Deze metriek is afgeschaft, wij raden u aan de nieuwe `Requests` metriek te gebruiken.|Locatie, hostnaam|
|UnauthorizedRequests|Niet-geautoriseerde gateway-aanvragen|Count|Totaal| Het totale aantal niet-geautoriseerde gateway aanvragen in een bepaalde periode. Deze metriek is afgeschaft, wij raden u aan de nieuwe `Requests` metriek te gebruiken.|Locatie, hostnaam|
|FailedRequests|Mislukte gateway-aanvragen|Count|Totaal|Het totale aantal mislukte gateway aanvragen in een bepaalde periode. Deze metriek is afgeschaft, wij raden u aan de nieuwe `Requests` metriek te gebruiken.|Locatie, hostnaam|
|OtherRequests|Andere gateway aanvragen|Count|Totaal|Het totale aantal gateway aanvragen in een bepaalde periode die niet in geslaagde, niet-geautoriseerde of mislukte categorieën vallen. Deze metriek is afgeschaft, wij raden u aan de nieuwe `Requests` metriek te gebruiken. |Locatie, hostnaam|
|Duur|Totale duur van gateway aanvragen|Milliseconden|Average|De tijd tussen het API Management van een aanvraag van een client en het retour neren van een reactie op de client.|Locatie, hostnaam|
|Capaciteit|Capaciteit|Procent|Average|De belasting indicator voor een API Management-exemplaar voor het nemen van gefundeerde beslissingen, ongeacht of u het exemplaar wilt schalen om meer belasting te bieden.|Locatie|
|EventHubTotalEvents|Totaal aantal EventHub-gebeurtenissen|Count|Totaal|Het totale aantal gebeurtenissen dat is verzonden naar EventHub van API Management in een bepaalde periode.|Locatie|
|EventHubSuccessfulEvents|Geslaagde EventHub-gebeurtenissen|Count|Totaal|Het totale aantal geslaagde EventHub-gebeurtenissen in een bepaalde periode.|Locatie|
|EventHubTotalFailedEvents|Mislukte EventHub-gebeurtenissen|Count|Totaal|Het totale aantal mislukte EventHub-gebeurtenissen in een bepaalde periode.|Locatie|
|EventHubRejectedEvents|Geweigerde EventHub-gebeurtenissen|Count|Totaal|Het totale aantal geweigerde EventHub-gebeurtenissen (onjuiste configuratie of niet-geautoriseerd) in een bepaalde periode.|Locatie|
|EventHubThrottledEvents|Vertraagde EventHub-gebeurtenissen|Count|Totaal|Het totale aantal vertraagde EventHub-gebeurtenissen in een bepaalde periode.|Locatie|
|EventHubTimedoutEvents|Time-out EventHub-gebeurtenissen|Count|Totaal|Het totale aantal time-out EventHub-gebeurtenissen in een bepaalde periode.|Locatie|
|EventHubDroppedEvents|Verwijderde EventHub-gebeurtenissen|Count|Totaal|Het totale aantal gebeurtenissen dat is overgeslagen omdat de maximale grootte van de wachtrij is bereikt in een bepaalde periode.|Locatie|
|EventHubTotalBytesSent|Grootte van EventHub-gebeurtenissen|Bytes|Totaal|De totale grootte van EventHub-gebeurtenissen in bytes in een bepaalde periode.|Locatie|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TotalJob|Totaal aantal taken|Count|Totaal|Het totale aantal taken|Runbook, status|
|TotalUpdateDeploymentRuns|Totaal aantal uitgevoerde update-implementaties|Count|Totaal|Totale aantal uitgevoerde software-update-implementaties|SoftwareUpdateConfigurationName, Status|
|TotalUpdateDeploymentMachineRuns|Totaal aantal uitgevoerde update-implementatie computers|Count|Totaal|Het totale aantal uitgevoerde software-update-implementatie computers in een software-update-implementatie|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CoreCount|Aantal toegewezen kernen|Count|Totaal|Totaal aantal toegewezen kernen in het batch-account|Er zijn geen dimensies|
|TotalNodeCount|Aantal toegewezen knoop punten|Count|Totaal|Totaal aantal toegewezen knoop punten in het batch-account|Er zijn geen dimensies|
|LowPriorityCoreCount|Aantal LowPriority kernen|Count|Totaal|Totaal aantal kernen met lage prioriteit in het batch-account|Er zijn geen dimensies|
|TotalLowPriorityNodeCount|Aantal knoop punten met een lage prioriteit|Count|Totaal|Totaal aantal knoop punten met een lage prioriteit in het batch-account|Er zijn geen dimensies|
|CreatingNodeCount|Aantal knoop punten maken|Count|Totaal|Aantal knoop punten dat wordt gemaakt|Er zijn geen dimensies|
|StartingNodeCount|Begin aantal knoop punten|Count|Totaal|Aantal knoop punten dat begint|Er zijn geen dimensies|
|WaitingForStartTaskNodeCount|Wachten op aantal begin taak knooppunten|Count|Totaal|Aantal knoop punten dat wacht tot de begin taak is voltooid|Er zijn geen dimensies|
|StartTaskFailedNodeCount|Aantal mislukte knoop punten van begin taak|Count|Totaal|Aantal knoop punten waarop de begin taak is mislukt|Er zijn geen dimensies|
|IdleNodeCount|Aantal niet-actieve knoop punten|Count|Totaal|Aantal niet-actieve knoop punten|Er zijn geen dimensies|
|OfflineNodeCount|Aantal offline knooppunten|Count|Totaal|Aantal offline knooppunten|Er zijn geen dimensies|
|RebootingNodeCount|Aantal knoop punten opnieuw opstarten|Count|Totaal|Aantal knoop punten dat opnieuw wordt opgestart|Er zijn geen dimensies|
|ReimagingNodeCount|Telling van het aantal knoop punten|Count|Totaal|Aantal knoop punten van installatie kopieën|Er zijn geen dimensies|
|RunningNodeCount|Aantal actieve knoop punten|Count|Totaal|Aantal actieve knoop punten|Er zijn geen dimensies|
|LeavingPoolNodeCount|Aantal groeps knooppunten verlaten|Count|Totaal|Aantal knoop punten dat de pool verlaat|Er zijn geen dimensies|
|UnusableNodeCount|Aantal niet-bruikbare knoop punten|Count|Totaal|Aantal niet-bruikbare knoop punten|Er zijn geen dimensies|
|PreemptedNodeCount|Aantal knoop punten in herhaling|Count|Totaal|Aantal knoop punten dat is afgebroken|Er zijn geen dimensies|
|TaskStartEvent|Taak begin gebeurtenissen|Count|Totaal|Totaal aantal taken dat is gestart|Er zijn geen dimensies|
|TaskCompleteEvent|Taak voltooid gebeurtenissen|Count|Totaal|Totaal aantal taken dat is voltooid|Er zijn geen dimensies|
|TaskFailEvent|Taak fout gebeurtenissen|Count|Totaal|Totaal aantal taken dat is voltooid met de status mislukt|Er zijn geen dimensies|
|PoolCreateEvent|Groeps gebeurtenissen maken|Count|Totaal|Totaal aantal Pools dat is gemaakt|Er zijn geen dimensies|
|PoolResizeStartEvent|Begin gebeurtenissen van groeps grootte wijzigen|Count|Totaal|Totaal aantal hergroottes van de groep die zijn gestart|Er zijn geen dimensies|
|PoolResizeCompleteEvent|Volledige gebeurtenissen voor het wijzigen van de pool|Count|Totaal|Totaal aantal hergroottes van de groep die zijn voltooid|Er zijn geen dimensies|
|PoolDeleteStartEvent|Begin gebeurtenissen groep verwijderen|Count|Totaal|Totaal aantal verwijderde groepen dat is gestart|Er zijn geen dimensies|
|PoolDeleteCompleteEvent|Voltooide gebeurtenissen van groep verwijderen|Count|Totaal|Totaal aantal verwijderde groepen dat is voltooid|Er zijn geen dimensies|
|JobDeleteCompleteEvent|Voltooide gebeurtenissen van taak verwijderen|Count|Totaal|Het totale aantal taken dat is verwijderd.|Er zijn geen dimensies|
|JobDeleteStartEvent|Taak begin gebeurtenissen verwijderen|Count|Totaal|Het totale aantal taken dat is aangevraagd om te worden verwijderd.|Er zijn geen dimensies|
|JobDisableCompleteEvent|Voltooide gebeurtenissen voor taak uitschakelen|Count|Totaal|Het totale aantal taken dat is uitgeschakeld.|Er zijn geen dimensies|
|JobDisableStartEvent|Taak start gebeurtenissen uitschakelen|Count|Totaal|Het totale aantal taken dat is aangevraagd om te worden uitgeschakeld.|Er zijn geen dimensies|
|JobStartEvent|Taak begin gebeurtenissen|Count|Totaal|Het totale aantal taken dat is gestart.|Er zijn geen dimensies|
|JobTerminateCompleteEvent|Voltooide gebeurtenissen voor taak beëindigen|Count|Totaal|Het totale aantal taken dat is beëindigd.|Er zijn geen dimensies|
|JobTerminateStartEvent|Taak start gebeurtenissen beëindigen|Count|Totaal|Het totale aantal taken dat is aangevraagd om te worden beëindigd.|Er zijn geen dimensies|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|connectedclients|Verbonden clients|Count|Maximum||ShardId|
|totalcommandsprocessed|Totaal aantal bewerkingen|Count|Totaal||ShardId|
|cachehits|Cache treffers|Count|Totaal||ShardId|
|cachemisses|Cache missers|Count|Totaal||ShardId|
|getcommands|Methode|Count|Totaal||ShardId|
|setcommands|Groepen|Count|Totaal||ShardId|
|operationsPerSecond|Bewerkingen per seconde|Count|Maximum||ShardId|
|evictedkeys|Verwijderde sleutels|Count|Totaal||ShardId|
|totalkeys|Totaal aantal sleutels|Count|Maximum||ShardId|
|expiredkeys|Verlopen sleutels|Count|Totaal||ShardId|
|usedmemory|Gebruikt geheugen|Bytes|Maximum||ShardId|
|usedmemorypercentage|Percentage gebruikt geheugen|Procent|Maximum||ShardId|
|usedmemoryRss|Gebruikte geheugen-RSS|Bytes|Maximum||ShardId|
|serverLoad|Server belasting|Procent|Maximum||ShardId|
|cacheWrite|Cache schrijven|BytesPerSecond|Maximum||ShardId|
|cacheRead|Lees bewerking in cache|BytesPerSecond|Maximum||ShardId|
|percentProcessorTime|CPU|Procent|Maximum||ShardId|
|cacheLatency|Cache latentie micro seconden (preview-versie)|Count|Average||ShardId, SampleType|
|fouten|Fouten|Count|Maximum||ShardId, error type|
|connectedclients0|Verbonden clients (Shard 0)|Count|Maximum||Er zijn geen dimensies|
|totalcommandsprocessed0|Totaal aantal bewerkingen (Shard 0)|Count|Totaal||Er zijn geen dimensies|
|cachehits0|Cache treffers (Shard 0)|Count|Totaal||Er zijn geen dimensies|
|cachemisses0|Cache missers (Shard 0)|Count|Totaal||Er zijn geen dimensies|
|getcommands0|Hiermee wordt opgehaald (Shard 0)|Count|Totaal||Er zijn geen dimensies|
|setcommands0|Sets (Shard 0)|Count|Totaal||Er zijn geen dimensies|
|operationsPerSecond0|Bewerkingen per seconde (Shard 0)|Count|Maximum||Er zijn geen dimensies|
|evictedkeys0|Verwijderde sleutels (Shard 0)|Count|Totaal||Er zijn geen dimensies|
|totalkeys0|Totaal aantal sleutels (Shard 0)|Count|Maximum||Er zijn geen dimensies|
|expiredkeys0|Verlopen sleutels (Shard 0)|Count|Totaal||Er zijn geen dimensies|
|usedmemory0|Gebruikt geheugen (Shard 0)|Bytes|Maximum||Er zijn geen dimensies|
|usedmemoryRss0|Gebruikt geheugen RSS (Shard 0)|Bytes|Maximum||Er zijn geen dimensies|
|serverLoad0|Server belasting (Shard 0)|Procent|Maximum||Er zijn geen dimensies|
|cacheWrite0|Cache schrijven (Shard 0)|BytesPerSecond|Maximum||Er zijn geen dimensies|
|cacheRead0|Cache gelezen (Shard 0)|BytesPerSecond|Maximum||Er zijn geen dimensies|
|percentProcessorTime0|CPU (Shard 0)|Procent|Maximum||Er zijn geen dimensies|
|connectedclients1|Verbonden clients (Shard 1)|Count|Maximum||Er zijn geen dimensies|
|totalcommandsprocessed1|Totaal aantal bewerkingen (Shard 1)|Count|Totaal||Er zijn geen dimensies|
|cachehits1|Cache treffers (Shard 1)|Count|Totaal||Er zijn geen dimensies|
|cachemisses1|Cache missers (Shard 1)|Count|Totaal||Er zijn geen dimensies|
|getcommands1|Hiermee wordt opgehaald (Shard 1)|Count|Totaal||Er zijn geen dimensies|
|setcommands1|Sets (Shard 1)|Count|Totaal||Er zijn geen dimensies|
|operationsPerSecond1|Bewerkingen per seconde (Shard 1)|Count|Maximum||Er zijn geen dimensies|
|evictedkeys1|Verwijderde sleutels (Shard 1)|Count|Totaal||Er zijn geen dimensies|
|totalkeys1|Totaal aantal sleutels (Shard 1)|Count|Maximum||Er zijn geen dimensies|
|expiredkeys1|Verlopen sleutels (Shard 1)|Count|Totaal||Er zijn geen dimensies|
|usedmemory1|Gebruikt geheugen (Shard 1)|Bytes|Maximum||Er zijn geen dimensies|
|usedmemoryRss1|Gebruikte geheugen RSS (Shard 1)|Bytes|Maximum||Er zijn geen dimensies|
|serverLoad1|Server belasting (Shard 1)|Procent|Maximum||Er zijn geen dimensies|
|cacheWrite1|Cache schrijven (Shard 1)|BytesPerSecond|Maximum||Er zijn geen dimensies|
|cacheRead1|Lees bewerking in cache (Shard 1)|BytesPerSecond|Maximum||Er zijn geen dimensies|
|percentProcessorTime1|CPU (Shard 1)|Procent|Maximum||Er zijn geen dimensies|
|connectedclients2|Verbonden clients (Shard 2)|Count|Maximum||Er zijn geen dimensies|
|totalcommandsprocessed2|Totaal aantal bewerkingen (Shard 2)|Count|Totaal||Er zijn geen dimensies|
|cachehits2|Cache treffers (Shard 2)|Count|Totaal||Er zijn geen dimensies|
|cachemisses2|Cache missers (Shard 2)|Count|Totaal||Er zijn geen dimensies|
|getcommands2|Hiermee wordt opgehaald (Shard 2)|Count|Totaal||Er zijn geen dimensies|
|setcommands2|Sets (Shard 2)|Count|Totaal||Er zijn geen dimensies|
|operationsPerSecond2|Bewerkingen per seconde (Shard 2)|Count|Maximum||Er zijn geen dimensies|
|evictedkeys2|Verwijderde sleutels (Shard 2)|Count|Totaal||Er zijn geen dimensies|
|totalkeys2|Totaal aantal sleutels (Shard 2)|Count|Maximum||Er zijn geen dimensies|
|expiredkeys2|Verlopen sleutels (Shard 2)|Count|Totaal||Er zijn geen dimensies|
|usedmemory2|Gebruikt geheugen (Shard 2)|Bytes|Maximum||Er zijn geen dimensies|
|usedmemoryRss2|Gebruikte geheugen RSS (Shard 2)|Bytes|Maximum||Er zijn geen dimensies|
|serverLoad2|Server belasting (Shard 2)|Procent|Maximum||Er zijn geen dimensies|
|cacheWrite2|Cache schrijven (Shard 2)|BytesPerSecond|Maximum||Er zijn geen dimensies|
|cacheRead2|Lees bewerking in cache (Shard 2)|BytesPerSecond|Maximum||Er zijn geen dimensies|
|percentProcessorTime2|CPU (Shard 2)|Procent|Maximum||Er zijn geen dimensies|
|connectedclients3|Verbonden clients (Shard 3)|Count|Maximum||Er zijn geen dimensies|
|totalcommandsprocessed3|Totaal aantal bewerkingen (Shard 3)|Count|Totaal||Er zijn geen dimensies|
|cachehits3|Cache treffers (Shard 3)|Count|Totaal||Er zijn geen dimensies|
|cachemisses3|Cache missers (Shard 3)|Count|Totaal||Er zijn geen dimensies|
|getcommands3|Hiermee wordt opgehaald (Shard 3)|Count|Totaal||Er zijn geen dimensies|
|setcommands3|Sets (Shard 3)|Count|Totaal||Er zijn geen dimensies|
|operationsPerSecond3|Bewerkingen per seconde (Shard 3)|Count|Maximum||Er zijn geen dimensies|
|evictedkeys3|Verwijderde sleutels (Shard 3)|Count|Totaal||Er zijn geen dimensies|
|totalkeys3|Totaal aantal sleutels (Shard 3)|Count|Maximum||Er zijn geen dimensies|
|expiredkeys3|Verlopen sleutels (Shard 3)|Count|Totaal||Er zijn geen dimensies|
|usedmemory3|Gebruikt geheugen (Shard 3)|Bytes|Maximum||Er zijn geen dimensies|
|usedmemoryRss3|Gebruikte geheugen RSS (Shard 3)|Bytes|Maximum||Er zijn geen dimensies|
|serverLoad3|Server belasting (Shard 3)|Procent|Maximum||Er zijn geen dimensies|
|cacheWrite3|Cache schrijven (Shard 3)|BytesPerSecond|Maximum||Er zijn geen dimensies|
|cacheRead3|Lees bewerking in cache (Shard 3)|BytesPerSecond|Maximum||Er zijn geen dimensies|
|percentProcessorTime3|CPU (Shard 3)|Procent|Maximum||Er zijn geen dimensies|
|connectedclients4|Verbonden clients (Shard 4)|Count|Maximum||Er zijn geen dimensies|
|totalcommandsprocessed4|Totaal aantal bewerkingen (Shard 4)|Count|Totaal||Er zijn geen dimensies|
|cachehits4|Cache treffers (Shard 4)|Count|Totaal||Er zijn geen dimensies|
|cachemisses4|Cache missers (Shard 4)|Count|Totaal||Er zijn geen dimensies|
|getcommands4|Hiermee wordt opgehaald (Shard 4)|Count|Totaal||Er zijn geen dimensies|
|setcommands4|Sets (Shard 4)|Count|Totaal||Er zijn geen dimensies|
|operationsPerSecond4|Bewerkingen per seconde (Shard 4)|Count|Maximum||Er zijn geen dimensies|
|evictedkeys4|Verwijderde sleutels (Shard 4)|Count|Totaal||Er zijn geen dimensies|
|totalkeys4|Totaal aantal sleutels (Shard 4)|Count|Maximum||Er zijn geen dimensies|
|expiredkeys4|Verlopen sleutels (Shard 4)|Count|Totaal||Er zijn geen dimensies|
|usedmemory4|Gebruikt geheugen (Shard 4)|Bytes|Maximum||Er zijn geen dimensies|
|usedmemoryRss4|Gebruikte geheugen RSS (Shard 4)|Bytes|Maximum||Er zijn geen dimensies|
|serverLoad4|Server belasting (Shard 4)|Procent|Maximum||Er zijn geen dimensies|
|cacheWrite4|Cache schrijven (Shard 4)|BytesPerSecond|Maximum||Er zijn geen dimensies|
|cacheRead4|Lees bewerking in cache (Shard 4)|BytesPerSecond|Maximum||Er zijn geen dimensies|
|percentProcessorTime4|CPU (Shard 4)|Procent|Maximum||Er zijn geen dimensies|
|connectedclients5|Verbonden clients (Shard 5)|Count|Maximum||Er zijn geen dimensies|
|totalcommandsprocessed5|Totaal aantal bewerkingen (Shard 5)|Count|Totaal||Er zijn geen dimensies|
|cachehits5|Cache treffers (Shard 5)|Count|Totaal||Er zijn geen dimensies|
|cachemisses5|Cache missers (Shard 5)|Count|Totaal||Er zijn geen dimensies|
|getcommands5|Hiermee wordt opgehaald (Shard 5)|Count|Totaal||Er zijn geen dimensies|
|setcommands5|Sets (Shard 5)|Count|Totaal||Er zijn geen dimensies|
|operationsPerSecond5|Bewerkingen per seconde (Shard 5)|Count|Maximum||Er zijn geen dimensies|
|evictedkeys5|Verwijderde sleutels (Shard 5)|Count|Totaal||Er zijn geen dimensies|
|totalkeys5|Totaal aantal sleutels (Shard 5)|Count|Maximum||Er zijn geen dimensies|
|expiredkeys5|Verlopen sleutels (Shard 5)|Count|Totaal||Er zijn geen dimensies|
|usedmemory5|Gebruikt geheugen (Shard 5)|Bytes|Maximum||Er zijn geen dimensies|
|usedmemoryRss5|Gebruikte geheugen RSS (Shard 5)|Bytes|Maximum||Er zijn geen dimensies|
|serverLoad5|Server belasting (Shard 5)|Procent|Maximum||Er zijn geen dimensies|
|cacheWrite5|Cache schrijven (Shard 5)|BytesPerSecond|Maximum||Er zijn geen dimensies|
|cacheRead5|Lees bewerking in cache (Shard 5)|BytesPerSecond|Maximum||Er zijn geen dimensies|
|percentProcessorTime5|CPU (Shard 5)|Procent|Maximum||Er zijn geen dimensies|
|connectedclients6|Verbonden clients (Shard 6)|Count|Maximum||Er zijn geen dimensies|
|totalcommandsprocessed6|Totaal aantal bewerkingen (Shard 6)|Count|Totaal||Er zijn geen dimensies|
|cachehits6|Cache treffers (Shard 6)|Count|Totaal||Er zijn geen dimensies|
|cachemisses6|Cache missers (Shard 6)|Count|Totaal||Er zijn geen dimensies|
|getcommands6|Hiermee wordt opgehaald (Shard 6)|Count|Totaal||Er zijn geen dimensies|
|setcommands6|Sets (Shard 6)|Count|Totaal||Er zijn geen dimensies|
|operationsPerSecond6|Bewerkingen per seconde (Shard 6)|Count|Maximum||Er zijn geen dimensies|
|evictedkeys6|Verwijderde sleutels (Shard 6)|Count|Totaal||Er zijn geen dimensies|
|totalkeys6|Totaal aantal sleutels (Shard 6)|Count|Maximum||Er zijn geen dimensies|
|expiredkeys6|Verlopen sleutels (Shard 6)|Count|Totaal||Er zijn geen dimensies|
|usedmemory6|Gebruikt geheugen (Shard 6)|Bytes|Maximum||Er zijn geen dimensies|
|usedmemoryRss6|Gebruikte geheugen RSS (Shard 6)|Bytes|Maximum||Er zijn geen dimensies|
|serverLoad6|Server belasting (Shard 6)|Procent|Maximum||Er zijn geen dimensies|
|cacheWrite6|Cache schrijven (Shard 6)|BytesPerSecond|Maximum||Er zijn geen dimensies|
|cacheRead6|Lees bewerking in cache (Shard 6)|BytesPerSecond|Maximum||Er zijn geen dimensies|
|percentProcessorTime6|CPU (Shard 6)|Procent|Maximum||Er zijn geen dimensies|
|connectedclients7|Verbonden clients (Shard 7)|Count|Maximum||Er zijn geen dimensies|
|totalcommandsprocessed7|Totaal aantal bewerkingen (Shard 7)|Count|Totaal||Er zijn geen dimensies|
|cachehits7|Cache treffers (Shard 7)|Count|Totaal||Er zijn geen dimensies|
|cachemisses7|Cache missers (Shard 7)|Count|Totaal||Er zijn geen dimensies|
|getcommands7|Hiermee wordt opgehaald (Shard 7)|Count|Totaal||Er zijn geen dimensies|
|setcommands7|Sets (Shard 7)|Count|Totaal||Er zijn geen dimensies|
|operationsPerSecond7|Bewerkingen per seconde (Shard 7)|Count|Maximum||Er zijn geen dimensies|
|evictedkeys7|Verwijderde sleutels (Shard 7)|Count|Totaal||Er zijn geen dimensies|
|totalkeys7|Totaal aantal sleutels (Shard 7)|Count|Maximum||Er zijn geen dimensies|
|expiredkeys7|Verlopen sleutels (Shard 7)|Count|Totaal||Er zijn geen dimensies|
|usedmemory7|Gebruikt geheugen (Shard 7)|Bytes|Maximum||Er zijn geen dimensies|
|usedmemoryRss7|Gebruikte geheugen RSS (Shard 7)|Bytes|Maximum||Er zijn geen dimensies|
|serverLoad7|Server belasting (Shard 7)|Procent|Maximum||Er zijn geen dimensies|
|cacheWrite7|Cache schrijven (Shard 7)|BytesPerSecond|Maximum||Er zijn geen dimensies|
|cacheRead7|Lees bewerking in cache (Shard 7)|BytesPerSecond|Maximum||Er zijn geen dimensies|
|percentProcessorTime7|CPU (Shard 7)|Procent|Maximum||Er zijn geen dimensies|
|connectedclients8|Verbonden clients (Shard 8)|Count|Maximum||Er zijn geen dimensies|
|totalcommandsprocessed8|Totaal aantal bewerkingen (Shard 8)|Count|Totaal||Er zijn geen dimensies|
|cachehits8|Cache treffers (Shard 8)|Count|Totaal||Er zijn geen dimensies|
|cachemisses8|Cache missers (Shard 8)|Count|Totaal||Er zijn geen dimensies|
|getcommands8|Hiermee wordt opgehaald (Shard 8)|Count|Totaal||Er zijn geen dimensies|
|setcommands8|Sets (Shard 8)|Count|Totaal||Er zijn geen dimensies|
|operationsPerSecond8|Bewerkingen per seconde (Shard 8)|Count|Maximum||Er zijn geen dimensies|
|evictedkeys8|Verwijderde sleutels (Shard 8)|Count|Totaal||Er zijn geen dimensies|
|totalkeys8|Totaal aantal sleutels (Shard 8)|Count|Maximum||Er zijn geen dimensies|
|expiredkeys8|Verlopen sleutels (Shard 8)|Count|Totaal||Er zijn geen dimensies|
|usedmemory8|Gebruikt geheugen (Shard 8)|Bytes|Maximum||Er zijn geen dimensies|
|usedmemoryRss8|Gebruikt geheugen RSS (Shard 8)|Bytes|Maximum||Er zijn geen dimensies|
|serverLoad8|Server belasting (Shard 8)|Procent|Maximum||Er zijn geen dimensies|
|cacheWrite8|Cache schrijven (Shard 8)|BytesPerSecond|Maximum||Er zijn geen dimensies|
|cacheRead8|Lees bewerking in cache (Shard 8)|BytesPerSecond|Maximum||Er zijn geen dimensies|
|percentProcessorTime8|CPU (Shard 8)|Procent|Maximum||Er zijn geen dimensies|
|connectedclients9|Verbonden clients (Shard 9)|Count|Maximum||Er zijn geen dimensies|
|totalcommandsprocessed9|Totaal aantal bewerkingen (Shard 9)|Count|Totaal||Er zijn geen dimensies|
|cachehits9|Cache treffers (Shard 9)|Count|Totaal||Er zijn geen dimensies|
|cachemisses9|Cache missers (Shard 9)|Count|Totaal||Er zijn geen dimensies|
|getcommands9|Hiermee wordt opgehaald (Shard 9)|Count|Totaal||Er zijn geen dimensies|
|setcommands9|Sets (Shard 9)|Count|Totaal||Er zijn geen dimensies|
|operationsPerSecond9|Bewerkingen per seconde (Shard 9)|Count|Maximum||Er zijn geen dimensies|
|evictedkeys9|Verwijderde sleutels (Shard 9)|Count|Totaal||Er zijn geen dimensies|
|totalkeys9|Totaal aantal sleutels (Shard 9)|Count|Maximum||Er zijn geen dimensies|
|expiredkeys9|Verlopen sleutels (Shard 9)|Count|Totaal||Er zijn geen dimensies|
|usedmemory9|Gebruikt geheugen (Shard 9)|Bytes|Maximum||Er zijn geen dimensies|
|usedmemoryRss9|Gebruikte geheugen RSS (Shard 9)|Bytes|Maximum||Er zijn geen dimensies|
|serverLoad9|Server belasting (Shard 9)|Procent|Maximum||Er zijn geen dimensies|
|cacheWrite9|Cache schrijven (Shard 9)|BytesPerSecond|Maximum||Er zijn geen dimensies|
|cacheRead9|Lees bewerking in cache (Shard 9)|BytesPerSecond|Maximum||Er zijn geen dimensies|
|percentProcessorTime9|CPU (Shard 9)|Procent|Maximum||Er zijn geen dimensies|

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Procent|Average|Het percentage toegewezen reken eenheden dat momenteel wordt gebruikt door de virtuele machine (s).|Er zijn geen dimensies|
|Netwerk in|Netwerk in|Bytes|Totaal|Het aantal bytes dat is ontvangen op alle netwerk interfaces door de virtuele machine (s) (binnenkomend verkeer).|Er zijn geen dimensies|
|Netwerk uit|Netwerk uit|Bytes|Totaal|Het aantal uitgaande bytes op alle netwerk interfaces door de virtuele machine (s) (uitgaand verkeer).|Er zijn geen dimensies|
|Gelezen bytes per seconde|Schijf lezen|BytesPerSecond|Average|Gemiddeld aantal gelezen bytes van de schijf tijdens de controle periode.|Er zijn geen dimensies|
|Geschreven bytes per seconde|Schijf schrijven|BytesPerSecond|Average|Gemiddeld aantal bytes dat tijdens de controle periode naar de schijf wordt geschreven.|Er zijn geen dimensies|
|Lees bewerkingen op de schijf per seconde|Lees bewerkingen op de schijf per seconde|CountPerSecond|Average|Read-IOPS van schijf.|Er zijn geen dimensies|
|Schrijf bewerkingen op de schijf per seconde|Schrijf bewerkingen op de schijf per seconde|CountPerSecond|Average|Schijf schrijf-IOPS.|Er zijn geen dimensies|

## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Micro soft. ClassicCompute/domein naam/sleuven/rollen

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Procent|Average|Het percentage toegewezen reken eenheden dat momenteel wordt gebruikt door de virtuele machine (s).|RoleInstanceId|
|Netwerk in|Netwerk in|Bytes|Totaal|Het aantal bytes dat is ontvangen op alle netwerk interfaces door de virtuele machine (s) (binnenkomend verkeer).|RoleInstanceId|
|Netwerk uit|Netwerk uit|Bytes|Totaal|Het aantal uitgaande bytes op alle netwerk interfaces door de virtuele machine (s) (uitgaand verkeer).|RoleInstanceId|
|Gelezen bytes per seconde|Schijf lezen|BytesPerSecond|Average|Gemiddeld aantal gelezen bytes van de schijf tijdens de controle periode.|RoleInstanceId|
|Geschreven bytes per seconde|Schijf schrijven|BytesPerSecond|Average|Gemiddeld aantal bytes dat tijdens de controle periode naar de schijf wordt geschreven.|RoleInstanceId|
|Lees bewerkingen op de schijf per seconde|Lees bewerkingen op de schijf per seconde|CountPerSecond|Average|Read-IOPS van schijf.|RoleInstanceId|
|Schrijf bewerkingen op de schijf per seconde|Schrijf bewerkingen op de schijf per seconde|CountPerSecond|Average|Schijf schrijf-IOPS.|RoleInstanceId|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TotalCalls|Totaal aantal aanroepen|Count|Totaal|Totaal aantal aanroepen.|ApiName, OperationName, Region|
|SuccessfulCalls|Geslaagde aanroepen|Count|Totaal|Aantal geslaagde aanroepen.|ApiName, OperationName, Region|
|TotalErrors|Totaal aantal fouten|Count|Totaal|Totaal aantal aanroepen met een fout respons (HTTP-antwoord code 4xx of 5xx).|ApiName, OperationName, Region|
|BlockedCalls|Geblokkeerde aanroepen|Count|Totaal|Aantal aanroepen dat de frequentie of quotum limiet heeft overschreden.|ApiName, OperationName, Region|
|ServerErrors|Server fouten|Count|Totaal|Het aantal aanroepen met een interne fout in de service (HTTP-antwoord code 5xx).|ApiName, OperationName, Region|
|ClientErrors|Client fouten|Count|Totaal|Het aantal aanroepen met een fout aan de client zijde (HTTP-antwoord code 4xx).|ApiName, OperationName, Region|
|DataIn|Gegevens in|Bytes|Totaal|Grootte van binnenkomende gegevens in bytes.|ApiName, OperationName, Region|
|DataOut|Gegevens uit|Bytes|Totaal|Grootte van uitgaande gegevens in bytes.|ApiName, OperationName, Region|
|Latentie|Latentie|MilliSeconds|Average|Latentie in milliseconden.|ApiName, OperationName, Region|
|CharactersTranslated|Geconverteerde tekens|Count|Totaal|Totaal aantal tekens in binnenkomende-tekst aanvraag.|ApiName, OperationName, Region|
|CharactersTrained|Getrainde tekens|Count|Totaal|Totaal aantal getrainde tekens.|ApiName, OperationName, Region|
|SpeechSessionDuration|Spraak sessie duur|Seconden|Totaal|Totale duur van de spraak sessie in seconden.|ApiName, OperationName, Region|
|TotalTransactions|Totaal aantal trans acties|Count|Totaal|Totaal aantal trans acties.|Er zijn geen dimensies|
|TotalTokenCalls|Totaal aantal token aanroepen|Count|Totaal|Totaal aantal token aanroepen.|ApiName, OperationName, Region|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Procent|Average|Het percentage toegewezen berekeningseenheden dat momenteel door de virtuele machine(s) wordt gebruikt|Er zijn geen dimensies|
|Netwerk in|In factureer bare netwerk|Bytes|Totaal|Het aantal factureer bare bytes dat is ontvangen op alle netwerk interfaces door de virtuele machine (s) (binnenkomend verkeer)|Er zijn geen dimensies|
|Netwerk uit|Onfactureerbaar netwerk|Bytes|Totaal|Het aantal factureer bare bytes op alle netwerk interfaces door de virtuele machine (s) (uitgaand verkeer)|Er zijn geen dimensies|
|Gelezen bytes op de schijf|Gelezen bytes op de schijf|Bytes|Totaal|Gelezen bytes van de schijf tijdens de controle periode|Er zijn geen dimensies|
|Geschreven bytes op de schijf|Geschreven bytes op de schijf|Bytes|Totaal|Naar schijf geschreven bytes tijdens de controle periode|Er zijn geen dimensies|
|Lees bewerkingen op de schijf per seconde|Lees bewerkingen op de schijf per seconde|CountPerSecond|Average|Lees-IOPS schijf|Er zijn geen dimensies|
|Schrijf bewerkingen op de schijf per seconde|Schrijf bewerkingen op de schijf per seconde|CountPerSecond|Average|Schijf schrijf-IOPS|Er zijn geen dimensies|
|Resterende CPU-tegoeden|Resterende CPU-tegoeden|Count|Average|Totaal aantal beschik bare tegoeden voor burst|Er zijn geen dimensies|
|Verbruikte CPU-tegoeden|Verbruikte CPU-tegoeden|Count|Average|Totaal aantal tegoeden dat door de virtuele machine wordt verbruikt|Er zijn geen dimensies|
|Per schijf gelezen bytes/sec.|Gegevens schijf gelezen bytes per seconde (afgeschaft)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode|SlotId|
|Schrijf bewerkingen per schijf/sec.|Gegevens schijf schrijf bewerkingen in bytes per seconde (afgeschaft)|CountPerSecond|Average|Tijdens de controle periode naar één schijf geschreven bytes per seconde|SlotId|
|Lees bewerkingen per schijf/sec.|Lees bewerkingen op de gegevens schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode|SlotId|
|Schrijf bewerkingen per schijf/SEC|Schrijf bewerkingen op de gegevens schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS tijdens de controle periode van één schijf schrijven|SlotId|
|WACHTRIJ diepte per schijf|WACHTRIJ diepte van de gegevens schijf (afgeschaft)|Count|Average|Wachtrij diepte van gegevens schijf (of wachtrij lengte)|SlotId|
|BESTURINGSSYSTEEM per schijf gelezen bytes per seconde|BESTURINGSSYSTEEM schijf gelezen bytes per seconde (afgeschaft)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode voor de besturingssysteem schijf|Er zijn geen dimensies|
|Per schijf geschreven bytes/sec.|BESTURINGSSYSTEEM schijf schrijf bewerkingen in bytes per seconde (afgeschaft)|CountPerSecond|Average|Geschreven bytes per seconde tijdens de controle periode voor de besturingssysteem schijf|Er zijn geen dimensies|
|Lees bewerkingen per schijf voor het besturings systeem/sec.|Lees bewerkingen op de besturingssysteem schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode voor de besturingssysteem schijf|Er zijn geen dimensies|
|Schrijf bewerkingen per schijf van het besturings systeem/SEC|Schrijf bewerkingen op de besturingssysteem schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS tijdens de controle periode voor de besturingssysteem schijf schrijven vanaf één schijf|Er zijn geen dimensies|
|WACHTRIJ diepte voor het besturings systeem per schijf|WACHTRIJ diepte van de besturingssysteem schijf (afgeschaft)|Count|Average|Wachtrij diepte van het besturings systeem (of wachtrij lengte)|Er zijn geen dimensies|
|Gegevens schijf gelezen bytes per seconde|Gegevens schijf gelezen bytes per seconde (preview)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode|LUN|
|Geschreven bytes per seconde gegevens schijf|Geschreven bytes per seconde (preview) gegevens schijf|CountPerSecond|Average|Tijdens de controle periode naar één schijf geschreven bytes per seconde|LUN|
|Lees bewerkingen op de gegevens schijf per seconde|Lees bewerkingen op de gegevens schijf per seconde (preview)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode|LUN|
|Schrijf bewerkingen op de gegevens schijf per seconde|Schrijf bewerkingen op de gegevens schijf per seconde (preview)|CountPerSecond|Average|IOPS tijdens de controle periode van één schijf schrijven|LUN|
|Wachtrij diepte van gegevens schijf|Wachtrij diepte van gegevens schijf (preview-versie)|Count|Average|Wachtrij diepte van gegevens schijf (of wachtrij lengte)|LUN|
|BESTURINGSSYSTEEM schijf gelezen bytes per seconde|BESTURINGSSYSTEEM schijf gelezen bytes per seconde (preview)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode voor de besturingssysteem schijf|Er zijn geen dimensies|
|Schrijf bewerkingen op de besturingssysteem schijf per seconde|Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)|CountPerSecond|Average|Geschreven bytes per seconde tijdens de controle periode voor de besturingssysteem schijf|Er zijn geen dimensies|
|Lees bewerkingen van de besturingssysteem schijf per seconde|Lees bewerkingen op de besturingssysteem schijf per seconde (preview)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode voor de besturingssysteem schijf|Er zijn geen dimensies|
|Schrijf bewerkingen op de besturingssysteem schijf per seconde|Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)|CountPerSecond|Average|IOPS tijdens de controle periode voor de besturingssysteem schijf schrijven vanaf één schijf|Er zijn geen dimensies|
|Wachtrij diepte van besturingssysteem schijf|Wachtrij diepte van de besturingssysteem schijf (preview-versie)|Count|Average|Wachtrij diepte van het besturings systeem (of wachtrij lengte)|Er zijn geen dimensies|
|Binnenkomende stromen|Binnenkomende stromen (preview-versie)|Count|Average|Inkomende stromen zijn het aantal huidige stromen in de binnenkomende richting (verkeer dat wordt verzonden naar de virtuele machine)|Er zijn geen dimensies|
|Uitgaande stromen|Uitgaande stromen (preview-versie)|Count|Average|Uitgaande stromen zijn het aantal huidige stromen in de uitgaande richting (verkeer dat uit de virtuele machine wordt verzonden)|Er zijn geen dimensies|
|Maximum aanmaak frequentie inkomende stromen|Maximum aantal aanmaak snelheden inkomende stromen (preview-versie)|CountPerSecond|Average|Het maximale aantal inkomende stromen (verkeer dat wordt verzonden naar de virtuele machine)|Er zijn geen dimensies|
|Maximum aanmaak frequentie van uitgaande stromen|Maximum aantal uitgaande stromen voor maken (preview)|CountPerSecond|Average|De maximale aanmaak frequentie van uitgaande stromen (verkeer dat uit de virtuele machine wordt verzonden)|Er zijn geen dimensies|
|Treffer voor Premium data-schijf cache lezen|Cache geheugen voor lezen van Premium-gegevens schijf (preview-versie)|Procent|Average|Treffer voor Premium data-schijf cache lezen|LUN|
|Lees missers cache Premium-gegevens schijf|Cache voor lezen van Premium-gegevens schijf (preview)|Procent|Average|Lees missers cache Premium-gegevens schijf|LUN|
|Treffer voor Premium-besturingssysteem schijf cache lezen|Treffer voor het lezen van een Premium-besturingssysteem schijf cache (preview-versie)|Procent|Average|Treffer voor Premium-besturingssysteem schijf cache lezen|Er zijn geen dimensies|
|Leesmij voor Premium-besturingssysteem schijf cache lezen|Schijf cache voor Premium-Lees-missers (preview-versie)|Procent|Average|Leesmij voor Premium-besturingssysteem schijf cache lezen|Er zijn geen dimensies|
|Totaal netwerk|Totaal netwerk|Bytes|Totaal|Het aantal ontvangen bytes op alle netwerk interfaces door de virtuele machine (s) (binnenkomend verkeer)|Er zijn geen dimensies|
|Totaal aantal netwerk|Totaal aantal netwerk|Bytes|Totaal|Het aantal uitgaande bytes op alle netwerk interfaces door de virtuele machine (s) (uitgaand verkeer)|Er zijn geen dimensies|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Procent|Average|Het percentage toegewezen berekeningseenheden dat momenteel door de virtuele machine(s) wordt gebruikt|VMName|
|Netwerk in|In factureer bare netwerk|Bytes|Totaal|Het aantal factureer bare bytes dat is ontvangen op alle netwerk interfaces door de virtuele machine (s) (binnenkomend verkeer)|VMName|
|Netwerk uit|Onfactureerbaar netwerk|Bytes|Totaal|Het aantal factureer bare bytes op alle netwerk interfaces door de virtuele machine (s) (uitgaand verkeer)|VMName|
|Gelezen bytes op de schijf|Gelezen bytes op de schijf|Bytes|Totaal|Gelezen bytes van de schijf tijdens de controle periode|VMName|
|Geschreven bytes op de schijf|Geschreven bytes op de schijf|Bytes|Totaal|Naar schijf geschreven bytes tijdens de controle periode|VMName|
|Lees bewerkingen op de schijf per seconde|Lees bewerkingen op de schijf per seconde|CountPerSecond|Average|Lees-IOPS schijf|VMName|
|Schrijf bewerkingen op de schijf per seconde|Schrijf bewerkingen op de schijf per seconde|CountPerSecond|Average|Schijf schrijf-IOPS|VMName|
|Resterende CPU-tegoeden|Resterende CPU-tegoeden|Count|Average|Totaal aantal beschik bare tegoeden voor burst|Er zijn geen dimensies|
|Verbruikte CPU-tegoeden|Verbruikte CPU-tegoeden|Count|Average|Totaal aantal tegoeden dat door de virtuele machine wordt verbruikt|Er zijn geen dimensies|
|Per schijf gelezen bytes/sec.|Gegevens schijf gelezen bytes per seconde (afgeschaft)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode|SlotId|
|Schrijf bewerkingen per schijf/sec.|Gegevens schijf schrijf bewerkingen in bytes per seconde (afgeschaft)|CountPerSecond|Average|Tijdens de controle periode naar één schijf geschreven bytes per seconde|SlotId|
|Lees bewerkingen per schijf/sec.|Lees bewerkingen op de gegevens schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode|SlotId|
|Schrijf bewerkingen per schijf/SEC|Schrijf bewerkingen op de gegevens schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS tijdens de controle periode van één schijf schrijven|SlotId|
|WACHTRIJ diepte per schijf|WACHTRIJ diepte van de gegevens schijf (afgeschaft)|Count|Average|Wachtrij diepte van gegevens schijf (of wachtrij lengte)|SlotId|
|BESTURINGSSYSTEEM per schijf gelezen bytes per seconde|BESTURINGSSYSTEEM schijf gelezen bytes per seconde (afgeschaft)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode voor de besturingssysteem schijf|Er zijn geen dimensies|
|Per schijf geschreven bytes/sec.|BESTURINGSSYSTEEM schijf schrijf bewerkingen in bytes per seconde (afgeschaft)|CountPerSecond|Average|Geschreven bytes per seconde tijdens de controle periode voor de besturingssysteem schijf|Er zijn geen dimensies|
|Lees bewerkingen per schijf voor het besturings systeem/sec.|Lees bewerkingen op de besturingssysteem schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode voor de besturingssysteem schijf|Er zijn geen dimensies|
|Schrijf bewerkingen per schijf van het besturings systeem/SEC|Schrijf bewerkingen op de besturingssysteem schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS tijdens de controle periode voor de besturingssysteem schijf schrijven vanaf één schijf|Er zijn geen dimensies|
|WACHTRIJ diepte voor het besturings systeem per schijf|WACHTRIJ diepte van de besturingssysteem schijf (afgeschaft)|Count|Average|Wachtrij diepte van het besturings systeem (of wachtrij lengte)|Er zijn geen dimensies|
|Gegevens schijf gelezen bytes per seconde|Gegevens schijf gelezen bytes per seconde (preview)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode|LUN, VMName|
|Geschreven bytes per seconde gegevens schijf|Geschreven bytes per seconde (preview) gegevens schijf|CountPerSecond|Average|Tijdens de controle periode naar één schijf geschreven bytes per seconde|LUN, VMName|
|Lees bewerkingen op de gegevens schijf per seconde|Lees bewerkingen op de gegevens schijf per seconde (preview)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode|LUN, VMName|
|Schrijf bewerkingen op de gegevens schijf per seconde|Schrijf bewerkingen op de gegevens schijf per seconde (preview)|CountPerSecond|Average|IOPS tijdens de controle periode van één schijf schrijven|LUN, VMName|
|Wachtrij diepte van gegevens schijf|Wachtrij diepte van gegevens schijf (preview-versie)|Count|Average|Wachtrij diepte van gegevens schijf (of wachtrij lengte)|LUN, VMName|
|BESTURINGSSYSTEEM schijf gelezen bytes per seconde|BESTURINGSSYSTEEM schijf gelezen bytes per seconde (preview)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode voor de besturingssysteem schijf|VMName|
|Schrijf bewerkingen op de besturingssysteem schijf per seconde|Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)|CountPerSecond|Average|Geschreven bytes per seconde tijdens de controle periode voor de besturingssysteem schijf|VMName|
|Lees bewerkingen van de besturingssysteem schijf per seconde|Lees bewerkingen op de besturingssysteem schijf per seconde (preview)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode voor de besturingssysteem schijf|VMName|
|Schrijf bewerkingen op de besturingssysteem schijf per seconde|Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)|CountPerSecond|Average|IOPS tijdens de controle periode voor de besturingssysteem schijf schrijven vanaf één schijf|VMName|
|Wachtrij diepte van besturingssysteem schijf|Wachtrij diepte van de besturingssysteem schijf (preview-versie)|Count|Average|Wachtrij diepte van het besturings systeem (of wachtrij lengte)|VMName|
|Binnenkomende stromen|Binnenkomende stromen (preview-versie)|Count|Average|Inkomende stromen zijn het aantal huidige stromen in de binnenkomende richting (verkeer dat wordt verzonden naar de virtuele machine)|VMName|
|Uitgaande stromen|Uitgaande stromen (preview-versie)|Count|Average|Uitgaande stromen zijn het aantal huidige stromen in de uitgaande richting (verkeer dat uit de virtuele machine wordt verzonden)|VMName|
|Maximum aanmaak frequentie inkomende stromen|Maximum aantal aanmaak snelheden inkomende stromen (preview-versie)|CountPerSecond|Average|Het maximale aantal inkomende stromen (verkeer dat wordt verzonden naar de virtuele machine)|VMName|
|Maximum aanmaak frequentie van uitgaande stromen|Maximum aantal uitgaande stromen voor maken (preview)|CountPerSecond|Average|De maximale aanmaak frequentie van uitgaande stromen (verkeer dat uit de virtuele machine wordt verzonden)|VMName|
|Treffer voor Premium data-schijf cache lezen|Cache geheugen voor lezen van Premium-gegevens schijf (preview-versie)|Procent|Average|Treffer voor Premium data-schijf cache lezen|LUN, VMName|
|Lees missers cache Premium-gegevens schijf|Cache voor lezen van Premium-gegevens schijf (preview)|Procent|Average|Lees missers cache Premium-gegevens schijf|LUN, VMName|
|Treffer voor Premium-besturingssysteem schijf cache lezen|Treffer voor het lezen van een Premium-besturingssysteem schijf cache (preview-versie)|Procent|Average|Treffer voor Premium-besturingssysteem schijf cache lezen|VMName|
|Leesmij voor Premium-besturingssysteem schijf cache lezen|Schijf cache voor Premium-Lees-missers (preview-versie)|Procent|Average|Leesmij voor Premium-besturingssysteem schijf cache lezen|VMName|
|Totaal netwerk|Totaal netwerk|Bytes|Totaal|Het aantal ontvangen bytes op alle netwerk interfaces door de virtuele machine (s) (binnenkomend verkeer)|VMName|
|Totaal aantal netwerk|Totaal aantal netwerk|Bytes|Totaal|Het aantal uitgaande bytes op alle netwerk interfaces door de virtuele machine (s) (uitgaand verkeer)|VMName|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CpuUsage|CPU-gebruik|Count|Average|CPU-gebruik op alle kernen in millicores.|containerName|
|MemoryUsage|Geheugen gebruik|Bytes|Average|Totaal geheugen gebruik in bytes.|containerName|
|NetworkBytesReceivedPerSecond|Ontvangen netwerk bytes per seconde|Bytes|Average|Het netwerk ontvangen bytes per seconde.|Er zijn geen dimensies|
|NetworkBytesTransmittedPerSecond|Verzonden netwerk bytes per seconde|Bytes|Average|Het netwerk aantal verzonden bytes per seconde.|Er zijn geen dimensies|

## <a name="microsoftcontainerregistryregistries"></a>Micro soft. ContainerRegistry/registers

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TotalPullCount|Totaal aantal pull-bewerkingen|Count|Average|Aantal opgehaalde afbeeldingen in totaal|Er zijn geen dimensies|
|SuccessfulPullCount|Aantal geslaagde pull-bewerkingen|Count|Average|Aantal geslaagde installatie kopieën|Er zijn geen dimensies|
|TotalPushCount|Totaal aantal push berichten|Count|Average|Aantal afbeeldings pushes in totaal|Er zijn geen dimensies|
|SuccessfulPushCount|Aantal geslaagde push berichten|Count|Average|Aantal geslaagde pushes voor installatie kopie|Er zijn geen dimensies|
|RunDuration|Uitvoerings duur|Milliseconden|Totaal|Uitvoerings duur in milliseconden|Er zijn geen dimensies|

## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Totaal aantal beschik bare CPU-kernen in een beheerd cluster|Count|Totaal|Totaal aantal beschik bare CPU-kernen in een beheerd cluster|Er zijn geen dimensies|
|kube_node_status_allocatable_memory_bytes|Totale hoeveelheid beschikbaar geheugen in een beheerd cluster|Bytes|Totaal|Totale hoeveelheid beschikbaar geheugen in een beheerd cluster|Er zijn geen dimensies|
|kube_pod_status_ready|Aantal in de status gereed|Count|Totaal|Aantal in de status gereed|naam ruimte, pod|
|kube_node_status_condition|Statussen voor de verschillende knooppunt voorwaarden|Count|Totaal|Statussen voor de verschillende knooppunt voorwaarden|voor waarde, status, status2, knoop punt|
|kube_pod_status_phase|Aantal per fase|Count|Totaal|Aantal per fase|fase, naam ruimte, pod|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|DCIApiCalls|Customer Insights-API-aanroepen|Count|Totaal||Er zijn geen dimensies|
|DCIMappingImportOperationSuccessfulLines|Voltooide regels voor het importeren van de toewijzing|Count|Totaal||Er zijn geen dimensies|
|DCIMappingImportOperationFailedLines|De regels voor het importeren van de toewijzing zijn mislukt|Count|Totaal||Er zijn geen dimensies|
|DCIMappingImportOperationTotalLines|Totaal regels voor import bewerking toewijzen|Count|Totaal||Er zijn geen dimensies|
|DCIMappingImportOperationRuntimeInSeconds|Runtime van import bewerking in seconden toewijzen|Seconden|Totaal||Er zijn geen dimensies|
|DCIOutboundProfileExportSucceeded|Het uitgaande profiel is geëxporteerd|Count|Totaal||Er zijn geen dimensies|
|DCIOutboundProfileExportFailed|Exporteren van uitgaand profiel is mislukt|Count|Totaal||Er zijn geen dimensies|
|DCIOutboundProfileExportDuration|Export duur van uitgaand profiel|Seconden|Totaal||Er zijn geen dimensies|
|DCIOutboundKpiExportSucceeded|Het exporteren van de uitgaande KPI is voltooid|Count|Totaal||Er zijn geen dimensies|
|DCIOutboundKpiExportFailed|Het exporteren van de uitgaande KPI is mislukt|Count|Totaal||Er zijn geen dimensies|
|DCIOutboundKpiExportDuration|Export duur van uitgaande KPI|Seconden|Totaal||Er zijn geen dimensies|
|DCIOutboundKpiExportStarted|Het exporteren van de uitgaande KPI is gestart|Seconden|Totaal||Er zijn geen dimensies|
|DCIOutboundKpiRecordCount|Aantal uitgaande KPI-records|Seconden|Totaal||Er zijn geen dimensies|
|DCIOutboundProfileExportCount|Aantal export uitgaande profielen|Seconden|Totaal||Er zijn geen dimensies|
|DCIOutboundInitialProfileExportFailed|Kan het uitgaande initiële profiel niet exporteren|Seconden|Totaal||Er zijn geen dimensies|
|DCIOutboundInitialProfileExportSucceeded|Het uitgaande initiële profiel is geëxporteerd|Seconden|Totaal||Er zijn geen dimensies|
|DCIOutboundInitialKpiExportFailed|Het exporteren van de uitgaande begin KPI is mislukt|Seconden|Totaal||Er zijn geen dimensies|
|DCIOutboundInitialKpiExportSucceeded|Het exporteren van de uitgaande begin KPI is voltooid|Seconden|Totaal||Er zijn geen dimensies|
|DCIOutboundInitialProfileExportDurationInSeconds|Export duur van het uitgaande begin profiel in enkele seconden|Seconden|Totaal||Er zijn geen dimensies|
|AdlaJobForStandardKpiFailed|De Adla-taak voor de standaard-KPI is in enkele seconden mislukt|Seconden|Totaal||Er zijn geen dimensies|
|AdlaJobForStandardKpiTimeOut|Adla-taak voor standaard-KPI-time-out in seconden|Seconden|Totaal||Er zijn geen dimensies|
|AdlaJobForStandardKpiCompleted|De Adla-taak voor de standaard-KPI is in enkele seconden voltooid|Seconden|Totaal||Er zijn geen dimensies|
|ImportASAValuesFailed|Aantal mislukte import-waarden van ASA|Count|Totaal||Er zijn geen dimensies|
|ImportASAValuesSucceeded|Aantal geslaagde activeringen voor ASA|Count|Totaal||Er zijn geen dimensies|
|DCIProfilesCount|Aantal exemplaren van profiel|Count|Laatste||Er zijn geen dimensies|
|DCIInteractionsPerMonthCount|Aantal interacties per maand|Count|Laatste||Er zijn geen dimensies|
|DCIKpisCount|Aantal KPI'S|Count|Laatste||Er zijn geen dimensies|
|DCISegmentsCount|Aantal segmenten|Count|Laatste||Er zijn geen dimensies|
|DCIPredictiveMatchPoliciesCount|Aantal voorspellende overeenkomsten|Count|Laatste||Er zijn geen dimensies|
|DCIPredictionsCount|Aantal voorspellingen|Count|Laatste||Er zijn geen dimensies|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|NICReadThroughput|Lees doorvoer (netwerk)|BytesPerSecond|Average|De Lees doorvoer van de netwerk interface op het apparaat in de rapportage periode voor alle volumes in de gateway.|InstanceName|
|NICWriteThroughput|Schrijf doorvoer (netwerk)|BytesPerSecond|Average|De schrijf doorvoer van de netwerk interface op het apparaat in de rapportage periode voor alle volumes in de gateway.|InstanceName|
|CloudReadThroughputPerShare|Door Voer van Cloud downloaden (delen)|BytesPerSecond|Average|De door Voer van downloaden naar Azure vanaf een share tijdens de rapportage periode.|Delen|
|CloudUploadThroughputPerShare|Upload doorvoer van Cloud (delen)|BytesPerSecond|Average|De upload doorvoer naar Azure vanaf een share tijdens de rapportage periode.|Delen|
|BytesUploadedToCloudPerShare|Geüploade Cloud bytes (delen)|Bytes|Average|Het totaal aantal bytes dat is geüpload naar Azure vanaf een share tijdens de rapportage periode.|Delen|
|TotalCapacity|Totale capaciteit|Bytes|Average|Totale capaciteit|Er zijn geen dimensies|
|Availablecapacity;)|Beschik bare capaciteit|Bytes|Average|De beschik bare capaciteit in bytes tijdens de rapportage periode.|Er zijn geen dimensies|
|CloudUploadThroughput|Upload doorvoer van Cloud|BytesPerSecond|Average|De upload doorvoer van de Cloud naar Azure tijdens de rapportage periode.|Er zijn geen dimensies|
|CloudReadThroughput|Door Voer van Cloud downloaden|BytesPerSecond|Average|De door Voer van de Cloud downloadt naar Azure tijdens de rapportage periode.|Er zijn geen dimensies|
|BytesUploadedToCloud|Geüploade Cloud bytes (apparaat)|Bytes|Average|Het totale aantal bytes dat tijdens de rapportage periode naar Azure wordt geüpload vanaf een apparaat.|Er zijn geen dimensies|
|HyperVVirtualProcessorUtilization|Edge Compute-percentage CPU|Procent|Average|Percentage CPU-gebruik|InstanceName|
|HyperVMemoryUtilization|Edge Compute-geheugen gebruik|Procent|Average|Hoeveelheid RAM-geheugen in gebruik|InstanceName|

## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|FailedRuns|Mislukte uitvoeringen|Count|Totaal||pipelineName, activiteitsnummer|
|SuccessfulRuns|Geslaagde uitvoeringen|Count|Totaal||pipelineName, activiteitsnummer|

## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PipelineFailedRuns|Metrische gegevens van mislukte pijplijn uitvoeringen|Count|Totaal||FailureType, naam|
|PipelineSucceededRuns|Metrische uitvoerings metingen geslaagde pijp lijnen|Count|Totaal||FailureType, naam|
|ActivityFailedRuns|Metrische gegevens mislukte uitvoering van activiteit|Count|Totaal||Activity type, PipelineName, FailureType, name|
|ActivitySucceededRuns|Metrische gegevens uitvoeringen uitgevoerde activiteit|Count|Totaal||Activity type, PipelineName, FailureType, name|
|TriggerFailedRuns|Meet waarden voor uitvoering van mislukte triggers|Count|Totaal||Naam, FailureType|
|TriggerSucceededRuns|Meet waarden voor uitvoering van geslaagde triggers|Count|Totaal||Naam, FailureType|
|IntegrationRuntimeCpuPercentage|CPU-gebruik van Integration runtime|Procent|Average||IntegrationRuntimeName, knooppuntnaam|
|IntegrationRuntimeAvailableMemory|Beschik bare geheugen voor Integration runtime|Bytes|Average||IntegrationRuntimeName, knooppuntnaam|
|MaxAllowedResourceCount|Maximum aantal toegestane entiteiten|Count|Maximum||Er zijn geen dimensies|
|MaxAllowedFactorySizeInGbUnits|Maxi maal toegestane grootte van de fabriek (GB-eenheid)|Count|Maximum||Er zijn geen dimensies|
|ResourceCount|Totaal aantal entiteiten|Count|Maximum||Er zijn geen dimensies|
|FactorySizeInGbUnits|Totale grootte van de fabriek (GB-eenheid)|Count|Maximum||Er zijn geen dimensies|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|JobEndedSuccess|Geslaagde taken|Count|Totaal|Aantal geslaagde taken.|Er zijn geen dimensies|
|JobEndedFailure|Mislukte taken|Count|Totaal|Aantal mislukte taken.|Er zijn geen dimensies|
|JobEndedCancelled|Geannuleerde taken|Count|Totaal|Aantal geannuleerde taken.|Er zijn geen dimensies|
|JobAUEndedSuccess|Geslaagde AU-tijd|Seconden|Totaal|Totale AU-tijd voor voltooide taken.|Er zijn geen dimensies|
|JobAUEndedFailure|Mislukte AU-tijd|Seconden|Totaal|Totale AU-tijd voor mislukte taken.|Er zijn geen dimensies|
|JobAUEndedCancelled|Geannuleerde AU-tijd|Seconden|Totaal|Totale AU-tijd voor geannuleerde taken.|Er zijn geen dimensies|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TotalStorage|Totale opslag|Bytes|Maximum|De totale hoeveelheid gegevens die in het account is opgeslagen.|Er zijn geen dimensies|
|DataWritten|Gegevens geschreven|Bytes|Totaal|De totale hoeveelheid gegevens die naar het account wordt geschreven.|Er zijn geen dimensies|
|DataRead|Gegevens lezen|Bytes|Totaal|De totale hoeveelheid gegevens die uit het account is gelezen.|Er zijn geen dimensies|
|WriteRequests|Aanvragen schrijven|Count|Totaal|Het aantal aanvragen voor het schrijven van gegevens naar het account.|Er zijn geen dimensies|
|ReadRequests|Aanvragen lezen|Count|Totaal|Aantal aanvragen voor het lezen van gegevens voor het account.|Er zijn geen dimensies|

## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Procent|Average|CPU-percentage|Er zijn geen dimensies|
|memory_percent|Geheugen percentage|Procent|Average|Geheugen percentage|Er zijn geen dimensies|
|io_consumption_percent|IO-percentage|Procent|Average|IO-percentage|Er zijn geen dimensies|
|storage_percent|Opslag percentage|Procent|Average|Opslag percentage|Er zijn geen dimensies|
|storage_used|Gebruikte opslag|Bytes|Average|Gebruikte opslag|Er zijn geen dimensies|
|storage_limit|Opslag limiet|Bytes|Average|Opslag limiet|Er zijn geen dimensies|
|serverlog_storage_percent|Percentage server logboek opslag|Procent|Average|Percentage server logboek opslag|Er zijn geen dimensies|
|serverlog_storage_usage|Gebruikte server logboek opslag|Bytes|Average|Gebruikte server logboek opslag|Er zijn geen dimensies|
|serverlog_storage_limit|Opslag limiet voor server logboek|Bytes|Average|Opslag limiet voor server logboek|Er zijn geen dimensies|
|active_connections|Actieve verbindingen|Count|Average|Actieve verbindingen|Er zijn geen dimensies|
|connections_failed|Mislukte verbindingen|Count|Totaal|Mislukte verbindingen|Er zijn geen dimensies|
|seconds_behind_master|Replicatie vertraging in seconden|Count|Average|Replicatie vertraging in seconden|Er zijn geen dimensies|
|backup_storage_used|Gebruikte back-upopslag|Bytes|Average|Gebruikte back-upopslag|Er zijn geen dimensies|
|network_bytes_egress|Netwerk uit|Bytes|Totaal|Netwerk uit over actieve verbindingen|Er zijn geen dimensies|
|network_bytes_ingress|Netwerk in|Bytes|Totaal|Netwerk in meerdere actieve verbindingen|Er zijn geen dimensies|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Procent|Average|CPU-percentage|Er zijn geen dimensies|
|memory_percent|Geheugen percentage|Procent|Average|Geheugen percentage|Er zijn geen dimensies|
|io_consumption_percent|IO-percentage|Procent|Average|IO-percentage|Er zijn geen dimensies|
|storage_percent|Opslag percentage|Procent|Average|Opslag percentage|Er zijn geen dimensies|
|storage_used|Gebruikte opslag|Bytes|Average|Gebruikte opslag|Er zijn geen dimensies|
|storage_limit|Opslag limiet|Bytes|Average|Opslag limiet|Er zijn geen dimensies|
|serverlog_storage_percent|Percentage server logboek opslag|Procent|Average|Percentage server logboek opslag|Er zijn geen dimensies|
|serverlog_storage_usage|Gebruikte server logboek opslag|Bytes|Average|Gebruikte server logboek opslag|Er zijn geen dimensies|
|serverlog_storage_limit|Opslag limiet voor server logboek|Bytes|Average|Opslag limiet voor server logboek|Er zijn geen dimensies|
|active_connections|Actieve verbindingen|Count|Average|Actieve verbindingen|Er zijn geen dimensies|
|connections_failed|Mislukte verbindingen|Count|Totaal|Mislukte verbindingen|Er zijn geen dimensies|
|seconds_behind_master|Replicatie vertraging in seconden|Count|Average|Replicatie vertraging in seconden|Er zijn geen dimensies|
|backup_storage_used|Gebruikte back-upopslag|Bytes|Average|Gebruikte back-upopslag|Er zijn geen dimensies|
|network_bytes_egress|Netwerk uit|Bytes|Totaal|Netwerk uit over actieve verbindingen|Er zijn geen dimensies|
|network_bytes_ingress|Netwerk in|Bytes|Totaal|Netwerk in meerdere actieve verbindingen|Er zijn geen dimensies|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Procent|Average|CPU-percentage|Er zijn geen dimensies|
|memory_percent|Geheugen percentage|Procent|Average|Geheugen percentage|Er zijn geen dimensies|
|io_consumption_percent|IO-percentage|Procent|Average|IO-percentage|Er zijn geen dimensies|
|storage_percent|Opslag percentage|Procent|Average|Opslag percentage|Er zijn geen dimensies|
|storage_used|Gebruikte opslag|Bytes|Average|Gebruikte opslag|Er zijn geen dimensies|
|storage_limit|Opslag limiet|Bytes|Average|Opslag limiet|Er zijn geen dimensies|
|serverlog_storage_percent|Percentage server logboek opslag|Procent|Average|Percentage server logboek opslag|Er zijn geen dimensies|
|serverlog_storage_usage|Gebruikte server logboek opslag|Bytes|Average|Gebruikte server logboek opslag|Er zijn geen dimensies|
|serverlog_storage_limit|Opslag limiet voor server logboek|Bytes|Average|Opslag limiet voor server logboek|Er zijn geen dimensies|
|active_connections|Actieve verbindingen|Count|Average|Actieve verbindingen|Er zijn geen dimensies|
|connections_failed|Mislukte verbindingen|Count|Totaal|Mislukte verbindingen|Er zijn geen dimensies|
|backup_storage_used|Gebruikte back-upopslag|Bytes|Average|Gebruikte back-upopslag|Er zijn geen dimensies|
|network_bytes_egress|Netwerk uit|Bytes|Totaal|Netwerk uit over actieve verbindingen|Er zijn geen dimensies|
|network_bytes_ingress|Netwerk in|Bytes|Totaal|Netwerk in meerdere actieve verbindingen|Er zijn geen dimensies|
|pg_replica_log_delay_in_seconds|Replica vertraging|Seconden|Maximum|Replica vertraging in seconden|Er zijn geen dimensies|
|pg_replica_log_delay_in_bytes|Maximale vertraging in Replica's|Bytes|Maximum|Vertraging in bytes van de meest vertraagde replica|Er zijn geen dimensies|

## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Procent|Average|CPU-percentage|Er zijn geen dimensies|
|memory_percent|Geheugen percentage|Procent|Average|Geheugen percentage|Er zijn geen dimensies|
|IOPS|IOPS|Count|Average|I/o-bewerkingen per seconde|Er zijn geen dimensies|
|storage_percent|Opslag percentage|Procent|Average|Opslag percentage|Er zijn geen dimensies|
|storage_used|Gebruikte opslag|Bytes|Average|Gebruikte opslag|Er zijn geen dimensies|
|active_connections|Actieve verbindingen|Count|Average|Actieve verbindingen|Er zijn geen dimensies|
|network_bytes_egress|Netwerk uit|Bytes|Totaal|Netwerk uit over actieve verbindingen|Er zijn geen dimensies|
|network_bytes_ingress|Netwerk in|Bytes|Totaal|Netwerk in meerdere actieve verbindingen|Er zijn geen dimensies|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Verzend pogingen voor telemetrie-berichten|Count|Totaal|Aantal pogingen voor het verzenden van apparaat-naar-Cloud-telemetrie naar uw IoT hub|Er zijn geen dimensies|
|d2c.telemetry.ingress.success|Verzonden telemetriegegevens|Count|Totaal|Aantal te verzenden apparaat-naar-Cloud-telemetrie-berichten naar uw IoT-hub|Er zijn geen dimensies|
|C2D. commands. OUTuitgang. complete. geslaagd|Opdrachten voltooid|Count|Totaal|Aantal Cloud-naar-apparaat-opdrachten die zijn voltooid door het apparaat|Er zijn geen dimensies|
|c2d.commands.egress.abandon.success|Opdrachten afgebroken|Count|Totaal|Aantal Cloud-naar-apparaat-opdrachten die zijn afgebroken door het apparaat|Er zijn geen dimensies|
|c2d.commands.egress.reject.success|Geweigerde opdrachten|Count|Totaal|Aantal Cloud-naar-apparaat-opdrachten dat door het apparaat is afgewezen|Er zijn geen dimensies|
|devices.totalDevices|Totaal aantal apparaten (afgeschaft)|Count|Totaal|Aantal apparaten dat is geregistreerd bij uw IoT-hub|Er zijn geen dimensies|
|devices.connectedDevices.allProtocol|Verbonden apparaten (afgeschaft) |Count|Totaal|Aantal apparaten dat is verbonden met uw IoT-hub|Er zijn geen dimensies|
|d2c.telemetry.egress.success|Route ring: telemetrie-berichten|Count|Totaal|Het aantal keren dat berichten zijn bezorgd bij alle eind punten met behulp van IoT Hub route ring. Als een bericht wordt doorgestuurd naar meerdere eind punten, wordt deze waarde met één verhoogd voor elke geslaagde levering. Als een bericht meerdere keren op hetzelfde eind punt wordt bezorgd, wordt deze waarde met één verhoogd voor elke geslaagde levering.|Er zijn geen dimensies|
|d2c.telemetry.egress.dropped|Route ring: telemetrie-berichten verwijderd |Count|Totaal|Het aantal keren dat berichten zijn verwijderd door IoT Hub route ring vanwege Dead-eind punten. Deze waarde telt geen berichten die worden bezorgd als terugval route als berichten die worden verzonden, niet worden bezorgd.|Er zijn geen dimensies|
|d2c.telemetry.egress.orphaned|Route ring: telemetriegegevens van zwevende berichten |Count|Totaal|Het aantal keren dat berichten zijn zwevend door IoT Hub route ring, omdat ze niet overeenkomen met de regels voor door sturen (inclusief de terugval regel). |Er zijn geen dimensies|
|d2c.telemetry.egress.invalid|Route ring: telemetrie-berichten incompatibel|Count|Totaal|Het aantal keren dat IoT Hub route ring geen berichten kan leveren als gevolg van incompatibiliteit met het eind punt. Deze waarde omvat geen nieuwe pogingen.|Er zijn geen dimensies|
|d2c.telemetry.egress.fallback|Route ring: berichten worden bezorgd bij terugval|Count|Totaal|Het aantal keren dat de route ring van berichten IoT Hub verzonden naar het eind punt dat is gekoppeld aan de terugval route.|Er zijn geen dimensies|
|d2c.endpoints.egress.eventHubs|Route ring: berichten worden bezorgd bij Event hub|Count|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan Event hub-eind punten.|Er zijn geen dimensies|
|d2c.endpoints.latency.eventHubs|Route ring: bericht latentie voor Event hub|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten IoT Hub en het binnenkomen van berichten in een event hub-eind punt.|Er zijn geen dimensies|
|d2c.endpoints.egress.serviceBusQueues|Route ring: berichten worden bezorgd bij Service Bus wachtrij|Count|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan Service Bus-wachtrij-eind punten.|Er zijn geen dimensies|
|d2c.endpoints.latency.serviceBusQueues|Route ring: bericht latentie voor Service Bus wachtrij|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en telemetrie-berichten in een Service Bus wachtrij-eind punt.|Er zijn geen dimensies|
|d2c.endpoints.egress.serviceBusTopics|Route ring: berichten die worden bezorgd bij Service Bus onderwerp|Count|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan Service Bus onderwerp-eind punten.|Er zijn geen dimensies|
|d2c.endpoints.latency.serviceBusTopics|Route ring: bericht latentie voor Service Bus onderwerp|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en telemetrie-berichten in het eind punt van een Service Bus onderwerp.|Er zijn geen dimensies|
|D2C. endpoints. uitgangs punt. builtIn. Events|Route ring: berichten worden bezorgd bij berichten/gebeurtenissen|Count|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan het ingebouwde eind punt (berichten/gebeurtenissen). Deze metrische gegevens worden alleen gestart als route ring is ingeschakeld (https://aka.ms/iotrouting) voor de IoT-hub.|Er zijn geen dimensies|
|d2c.endpoints.latency.builtIn.events|Route ring: bericht latentie voor berichten/gebeurtenissen|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en het inkomend telemetrie-bericht in het ingebouwde eind punt (berichten/gebeurtenissen). Deze metrische gegevens worden alleen gestart als route ring is ingeschakeld (https://aka.ms/iotrouting) voor de IoT-hub.|Er zijn geen dimensies|
|d2c.endpoints.egress.storage|Route ring: berichten worden bezorgd bij de opslag|Count|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan de opslag eindpunten.|Er zijn geen dimensies|
|d2c.endpoints.latency.storage|Route ring: bericht latentie voor opslag|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en telemetrie-berichten in een opslag eindpunt.|Er zijn geen dimensies|
|D2C. endpoints. out. storage. bytes|Route ring: gegevens worden geleverd aan de opslag|Bytes|Totaal|De hoeveelheid gegevens (bytes) IoT Hub route ring die aan de opslag eindpunten wordt geleverd.|Er zijn geen dimensies|
|d2c.endpoints.egress.storage.blobs|Route ring: blobs die aan de opslag worden geleverd|Count|Totaal|Het aantal keren dat IoT Hub route ring blobs naar opslag eindpunten heeft geleverd.|Er zijn geen dimensies|
|EventGridDeliveries|Event Grid leveringen (preview-versie)|Count|Totaal|Het aantal IoT Hub gebeurtenissen dat is gepubliceerd op Event Grid. Gebruik de dimensie resultaat voor het aantal geslaagde en mislukte aanvragen. De dimensie type-tekst geeft het soort gebeurtenis weer (https://aka.ms/ioteventgrid).|Resultaat, type gebeurtenis|
|EventGridLatency|De gemiddelde latentie (in milliseconden) vanaf het moment waarop de IOT hub-gebeurtenis werd gegenereerd toen de gebeurtenis werd gepubliceerd in Event Grid. Dit getal is een gemiddelde tussen alle gebeurtenis typen. Gebruik de dimensie type type om de latentie van een specifiek soort gebeurtenis weer te geven.|EventType|
|d2c.twin.read.success|Geslaagde dubbele Lees bewerkingen van apparaten|Count|Totaal|De telling van alle geslaagde apparaten met dubbele Lees bewerkingen.|Er zijn geen dimensies|
|D2C. dubbele. Read. failure|Mislukte dubbele Lees bewerkingen van apparaten|Count|Totaal|Het aantal apparaten dat niet kan worden gestart, dubbele Lees bewerkingen.|Er zijn geen dimensies|
|d2c.twin.read.size|Reactie grootte van dubbele Lees bewerkingen van apparaten|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde apparaten-geïnitieerde dubbele Lees bewerkingen.|Er zijn geen dimensies|
|d2c.twin.update.success|Geslaagde dubbele updates van apparaten|Count|Totaal|De telling van alle geslaagde, door het apparaat geïnitieerde dubbele updates.|Er zijn geen dimensies|
|D2C. dubbele. update. failure|Mislukte dubbele updates van apparaten|Count|Totaal|Het aantal apparaten dat door een apparaat is gestart en dubbele updates heeft uitgevoerd.|Er zijn geen dimensies|
|D2C. dubbele. update. grootte|Grootte van dubbele updates van apparaten|Bytes|Average|Het gemiddelde, het minimum en de maximale grootte van alle geslaagde, door het apparaat geïnitieerde dubbele updates.|Er zijn geen dimensies|
|C2D. methods. geslaagd|Geslaagde directe aanroepen van de methode|Count|Totaal|Het aantal voltooide direct-methode aanroepen.|Er zijn geen dimensies|
|C2D. methods. failure|Mislukte directe aanroepen van methode|Count|Totaal|Het aantal mislukte direct-methode aanroepen.|Er zijn geen dimensies|
|C2D. methods. requestSize|Aanvraag grootte van directe-methode aanroepen|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde direct-methode aanvragen.|Er zijn geen dimensies|
|C2D. methods. responseSize|Antwoord grootte van directe methode aanroepen|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde reacties van de methode direct.|Er zijn geen dimensies|
|c2d.twin.read.success|Geslaagde dubbele Lees bewerkingen van back-end|Count|Totaal|Het aantal geslaagde back-end-geïnitieerde dubbele Lees bewerkingen.|Er zijn geen dimensies|
|c2d.twin.read.failure|Mislukte dubbele Lees bewerkingen van back-end|Count|Totaal|Het aantal mislukte back-end-geïnitieerde dubbele Lees bewerkingen.|Er zijn geen dimensies|
|c2d.twin.read.size|Reactie grootte van dubbele Lees bewerkingen van de back-end|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde back-end-geïnitieerde dubbele Lees bewerkingen.|Er zijn geen dimensies|
|c2d.twin.update.success|Geslaagde dubbele updates van back-end|Count|Totaal|Het aantal geslaagde, door de back-end gestarte dubbele updates.|Er zijn geen dimensies|
|C2D. dubbele. update. failure|Mislukte dubbele updates van back-end|Count|Totaal|Het aantal niet-geslaagde, door de back-end geïnitieerde dubbele updates.|Er zijn geen dimensies|
|C2D. dubbele. update. grootte|Grootte van dubbele updates van back-end|Bytes|Average|Het gemiddelde, het minimum en de maximale grootte van alle geslaagde back-end-geïnitieerde dubbele updates.|Er zijn geen dimensies|
|twinQueries.success|Geslaagde dubbele query's|Count|Totaal|Het aantal geslaagde dubbele query's.|Er zijn geen dimensies|
|twinQueries.failure|Mislukte dubbele query's|Count|Totaal|Het aantal mislukte dubbele query's.|Er zijn geen dimensies|
|twinQueries.resultSize|Resultaat grootte van dubbele query's|Bytes|Average|Het gemiddelde, het minimum en het maximum van de resultaat grootte van alle geslaagde dubbele query's.|Er zijn geen dimensies|
|jobs.createTwinUpdateJob.success|Geslaagde creatie van dubbele update taken|Count|Totaal|Het aantal van alle geslaagde taken voor het maken van dubbele updates.|Er zijn geen dimensies|
|jobs.createTwinUpdateJob.failure|Kan geen dubbele update taken uitvoeren|Count|Totaal|Het aantal mislukte het maken van dubbele update taken.|Er zijn geen dimensies|
|jobs.createDirectMethodJob.success|Geslaagde creatie van methode aanroep taken|Count|Totaal|Het aantal van alle geslaagde aanroepen van directe methode aanroep taken.|Er zijn geen dimensies|
|jobs.createDirectMethodJob.failure|Kan geen aanroepen van methode aanroep taken uitvoeren|Count|Totaal|Het aantal van alle mislukte aanroepen van directe methode aanroep taken.|Er zijn geen dimensies|
|jobs.listJobs.success|Geslaagde aanroepen naar lijst taken|Count|Totaal|Het aantal geslaagde aanroepen naar lijst taken.|Er zijn geen dimensies|
|jobs.listJobs.failure|Mislukte aanroepen naar lijst taken|Count|Totaal|Het aantal mislukte aanroepen naar lijst taken.|Er zijn geen dimensies|
|jobs.cancelJob.success|Voltooide taak annuleringen|Count|Totaal|Het aantal geslaagde aanroepen om een taak te annuleren.|Er zijn geen dimensies|
|jobs.cancelJob.failure|Mislukte taak annuleringen|Count|Totaal|Het aantal mislukte aanroepen om een taak te annuleren.|Er zijn geen dimensies|
|jobs.queryJobs.success|Geslaagde taak query's|Count|Totaal|Het aantal geslaagde aanroepen naar query taken.|Er zijn geen dimensies|
|jobs.queryJobs.failure|Mislukte taak query's|Count|Totaal|Het aantal mislukte aanroepen naar query taken.|Er zijn geen dimensies|
|Jobs. voltooid|Voltooide taken|Count|Totaal|Het aantal voltooide taken.|Er zijn geen dimensies|
|Jobs. mislukt|Mislukte taken|Count|Totaal|Het aantal mislukte taken.|Er zijn geen dimensies|
|d2c.telemetry.ingress.sendThrottle|Aantal beperkings fouten|Count|Totaal|Aantal beperkings fouten door doorvoer vertraging van apparaat|Er zijn geen dimensies|
|dailyMessageQuotaUsed|Totaal aantal gebruikte berichten|Count|Average|Het totale aantal berichten dat momenteel wordt gebruikt. Dit is een cumulatieve waarde die elke dag opnieuw wordt ingesteld op nul om 00:00 UTC.|Er zijn geen dimensies|
|deviceDataUsage|Totale hoeveelheid gegevens gebruik van apparaat|Bytes|Totaal|Verzonden bytes van en naar apparaten die zijn verbonden met IotHub|Er zijn geen dimensies|
|totalDeviceCount|Totaal aantal apparaten (preview-versie)|Count|Average|Aantal apparaten dat is geregistreerd bij uw IoT-hub|Er zijn geen dimensies|
|connectedDeviceCount|Verbonden apparaten (preview-versie)|Count|Average|Aantal apparaten dat is verbonden met uw IoT-hub|Er zijn geen dimensies|
|configuraties|Metrische configuratie gegevens|Count|Totaal|Metrische gegevens voor configuratie bewerkingen|Er zijn geen dimensies|

## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|RegistrationAttempts|Registratie pogingen|Count|Totaal|Aantal pogingen voor apparaatregistratie|ProvisioningServiceName, IotHubName, Status|
|DeviceAssignments|Apparaten toegewezen|Count|Totaal|Aantal apparaten dat is toegewezen aan een IoT-hub|ProvisioningServiceName, IotHubName|
|AttestationAttempts|Attestation-pogingen|Count|Totaal|Aantal pogingen voor het uitvoeren van een apparaat|ProvisioningServiceName, Status, Protocol|

## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AvailableStorage|Beschik bare opslag|Bytes|Totaal|Totale beschik bare opslag gerapporteerd bij een granulatie van 5 minuten|Verzamelingnaam, databasenaam, regio|
|CassandraConnectionClosures|Cassandra-verbinding sluiten|Count|Totaal|Aantal Cassandra-verbindingen dat is gesloten, gerapporteerd met een granulatie van 1 minuut|Regio, ClosureReason|
|CassandraRequestCharges|Kosten voor Cassandra-aanvragen|Count|Totaal|RUs gebruikt voor Cassandra-aanvragen|DATABASENAME, verzamel-, regio, OperationType, resource type|
|CassandraRequests|Cassandra aanvragen|Count|Count|Aantal gemaakte Cassandra-aanvragen|DATABASENAME, verzamel-, regio, OperationType, resource type, error code|
|DataUsage|Gegevensgebruik|Bytes|Totaal|Totaal gegevens gebruik gerapporteerd bij 5 minuten granulariteit|Verzamelingnaam, databasenaam, regio|
|DocumentCount|Aantal documenten|Count|Totaal|Totaal aantal documenten gemeld bij 5 minuten granulariteit|Verzamelingnaam, databasenaam, regio|
|DocumentQuota|Document quotum|Bytes|Totaal|Totale opslag quotum gerapporteerd bij een granulatie van 5 minuten|Verzamelingnaam, databasenaam, regio|
|IndexUsage|Index gebruik|Bytes|Totaal|Totaal gebruik van index gerapporteerd bij een granulatie van 5 minuten|Verzamelingnaam, databasenaam, regio|
|MetadataRequests|Meta gegevens aanvragen|Count|Count|Aantal aanvragen voor meta gegevens. Cosmos DB onderhoudt de verzameling van meta gegevens van het systeem voor elk account, waarmee u verzamelingen, data bases, enzovoort, en hun configuraties, kosteloos kunt inventariseren.|DATABASENAME, verzamel-, regio, status code, |
|MongoRequestCharge|Kosten voor Mongo-aanvragen|Count|Totaal|Verbruikte Mongo-aanvraag eenheden|DATABASENAME, verzamel-, regio, opdrachtnaam, error code|
|MongoRequests|Mongo aanvragen|Count|Count|Aantal gemaakte Mongo-aanvragen|DATABASENAME, verzamel-, regio, opdrachtnaam, error code|
|ProvisionedThroughput|Ingerichte doorvoer|Count|Maximum|Ingerichte doorvoer|DATABASENAME, verzamelnaam|
|ReplicationLatency|P99-replicatie latentie|MilliSeconds|Average|Replicatie latentie van P99 voor de bron-en doel regio's voor geografisch ingeschakelde account|SourceRegion, TargetRegion|
|ServiceAvailability|Service beschikbaarheid|Procent|Average|Beschik baarheid van account aanvragen op één uur, dag of maand granulatie|Er zijn geen dimensies|
|TotalRequestUnits|Totaal aantal aanvraag eenheden|Count|Totaal|Verbruikte aanvraag eenheden|DATABASENAME, verzamel-, regio, status code, OperationType|
|TotalRequests|Totaal aantal aanvragen|Count|Count|Aantal aanvragen dat is gedaan|DATABASENAME, verzamel-, regio, status code, OperationType|

## <a name="microsofteventgridtopics"></a>Micro soft. EventGrid/topics

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PublishSuccessCount|Gepubliceerde gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat naar dit onderwerp is gepubliceerd|Er zijn geen dimensies|
|PublishFailCount|Mislukte gebeurtenissen publiceren|Count|Totaal|Totaal aantal gebeurtenissen dat niet naar dit onderwerp kan worden gepubliceerd|ErrorType, Error|
|UnmatchedEventCount|Niet-overeenkomende gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat niet overeenkomt met een van de gebeurtenis abonnementen voor dit onderwerp|Er zijn geen dimensies|
|PublishSuccessLatencyInMs|Latentie van publicatie geslaagd|Count|Totaal|Latentie van geslaagde publicatie in milliseconden|Er zijn geen dimensies|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|MatchedEventCount|Overeenkomende gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|Er zijn geen dimensies|
|DeliveryAttemptFailCount|Mislukte leverings gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat niet aan dit gebeurtenis abonnement kan worden geleverd|Error, ErrorType|
|DeliverySuccessCount|Geleverde gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat aan dit gebeurtenis abonnement is geleverd|Er zijn geen dimensies|
|DestinationProcessingDurationInMs|Doel verwerkings duur|Milliseconden|Average|Doel verwerkings duur in milliseconden|Er zijn geen dimensies|
|DroppedEventCount|Verwijderde gebeurtenissen|Count|Totaal|Totaal aantal verloren gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|DropReason|
|DeadLetteredCount|Gebeurtenissen met onbestelbare berichten|Count|Totaal|Totaal aantal gebeurtenissen met onbestelbare berichten die overeenkomen met dit gebeurtenis abonnement|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PublishSuccessCount|Gepubliceerde gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat naar dit onderwerp is gepubliceerd|Er zijn geen dimensies|
|PublishFailCount|Mislukte gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat niet naar dit onderwerp kan worden gepubliceerd|ErrorType, Error|
|UnmatchedEventCount|Niet-overeenkomende gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat niet overeenkomt met een van de gebeurtenis abonnementen voor dit onderwerp|Er zijn geen dimensies|
|PublishSuccessLatencyInMs|Latentie van publicatie geslaagd|Count|Totaal|Latentie van geslaagde publicatie in milliseconden|Er zijn geen dimensies|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SuccessfulRequests|Geslaagde aanvragen|Count|Totaal|Geslaagde aanvragen voor micro soft. EventHub.|EntityName |
|ServerErrors|Server fouten.|Count|Totaal|Server fouten voor micro soft. EventHub.|EntityName |
|UserErrors|Gebruikers fouten.|Count|Totaal|Gebruikers fouten voor micro soft. EventHub.|EntityName |
|QuotaExceededErrors|Quota overschreden fouten.|Count|Totaal|Quotum overschrijdt fouten voor micro soft. EventHub.|EntityName |
|ThrottledRequests|Vertraagde aanvragen.|Count|Totaal|Beperkte aanvragen voor micro soft. EventHub.|EntityName |
|IncomingRequests|Binnenkomende aanvragen|Count|Totaal|Binnenkomende aanvragen voor micro soft. EventHub.|EntityName|
|IncomingMessages|Inkomende berichten|Count|Totaal|Binnenkomende berichten voor micro soft. EventHub.|EntityName|
|OutgoingMessages|Uitgaande berichten|Count|Totaal|Uitgaande berichten voor micro soft. EventHub.|EntityName|
|IncomingBytes|Binnenkomende bytes.|Bytes|Totaal|Binnenkomende bytes voor micro soft. EventHub.|EntityName|
|OutgoingBytes|Uitgaande bytes.|Bytes|Totaal|Uitgaande bytes voor micro soft. EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Count|Average|Totaal aantal actieve verbindingen voor micro soft. EventHub.|Er zijn geen dimensies|
|ConnectionsOpened|Geopende verbindingen.|Count|Average|Geopende verbindingen voor micro soft. EventHub.|EntityName|
|ConnectionsClosed|Verbindingen gesloten.|Count|Average|Gesloten verbindingen voor micro soft. EventHub.|EntityName|
|CaptureBacklog|Achterstand vastleggen.|Count|Totaal|Achterstand vastleggen voor micro soft. EventHub.|EntityName|
|CapturedMessages|Vastgelegde berichten.|Count|Totaal|Vastgelegde berichten voor micro soft. EventHub.|EntityName|
|CapturedBytes|Vastgelegde bytes.|Bytes|Totaal|Vastgelegde bytes voor micro soft. EventHub.|EntityName|
|Grootte|Grootte|Bytes|Average|Grootte van een EventHub in bytes.|EntityName|
|INREQS|Binnenkomende aanvragen (afgeschaft)|Count|Totaal|Totaal aantal binnenkomende verzend aanvragen voor een naam ruimte (afgeschaft)|Er zijn geen dimensies|
|SUCCREQ|Geslaagde aanvragen (afgeschaft)|Count|Totaal|Totaal aantal geslaagde aanvragen voor een naam ruimte (afgeschaft)|Er zijn geen dimensies|
|FAILREQ|Mislukte aanvragen (afgeschaft)|Count|Totaal|Totaal aantal mislukte aanvragen voor een naam ruimte (afgeschaft)|Er zijn geen dimensies|
|SVRBSY|Fouten bij server bezet (afgeschaft)|Count|Totaal|Totaal aantal fouten bij server bezet voor een naam ruimte (afgeschaft)|Er zijn geen dimensies|
|INTERer|Interne server fouten (afgeschaft)|Count|Totaal|Totaal aantal interne server fouten voor een naam ruimte (afgeschaft)|Er zijn geen dimensies|
|MISCERR|Andere fouten (afgeschaft)|Count|Totaal|Totaal aantal mislukte aanvragen voor een naam ruimte (afgeschaft)|Er zijn geen dimensies|
|INMSGS|Binnenkomende berichten (afgeschaft) (afgeschaft)|Count|Totaal|Totaal aantal inkomende berichten voor een naam ruimte. Deze metriek is afgeschaft. Gebruik in plaats daarvan de metriek van binnenkomende berichten (afgeschaft)|Er zijn geen dimensies|
|EHINMSGS|Inkomende berichten (afgeschaft)|Count|Totaal|Totaal aantal inkomende berichten voor een naam ruimte (afgeschaft)|Er zijn geen dimensies|
|OUTMSGS|Uitgaande berichten (afgeschaft) (afgeschaft)|Count|Totaal|Totaal aantal uitgaande berichten voor een naam ruimte. Deze metriek is afgeschaft. Gebruik in plaats daarvan de metrische gegevens van uitgaande berichten (afgeschaft)|Er zijn geen dimensies|
|EHOUTMSGS|Uitgaande berichten (afgeschaft)|Count|Totaal|Totaal aantal uitgaande berichten voor een naam ruimte (afgeschaft)|Er zijn geen dimensies|
|EHINMBS|Binnenkomende bytes (afgeschaft) (afgeschaft)|Bytes|Totaal|Door Voer van binnenkomende berichten van Event hub voor een naam ruimte. Deze metriek is afgeschaft. Gebruik in plaats daarvan de metrische gegevens over binnenkomende bytes (afgeschaft)|Er zijn geen dimensies|
|EHINBYTES|Binnenkomende bytes (afgeschaft)|Bytes|Totaal|Door Voer van inkomende berichten van Event hub voor een naam ruimte (afgeschaft)|Er zijn geen dimensies|
|EHOUTMBS|Uitgaande bytes (afgeschaft) (afgeschaft)|Bytes|Totaal|Door Voer van Event hub voor een naam ruimte. Deze metriek is afgeschaft. Gebruik in plaats hiervan de metrische gegevens van uitgaande bytes (afgeschaft)|Er zijn geen dimensies|
|EHOUTBYTES|Uitgaande bytes (afgeschaft)|Bytes|Totaal|Door Voer van Event hub voor een naam ruimte (afgeschaft)|Er zijn geen dimensies|
|EHABL|Achterstallige berichten archiveren (afgeschaft)|Count|Totaal|Event hub-archief berichten in achterstand voor een naam ruimte (afgeschaft)|Er zijn geen dimensies|
|EHAMSGS|Berichten archiveren (afgeschaft)|Count|Totaal|Event hub heeft berichten gearchiveerd in een naam ruimte (afgeschaft)|Er zijn geen dimensies|
|EHAMBS|Berichten doorvoer archiveren (afgeschaft)|Bytes|Totaal|Door Voer van Event hub-bericht doorvoer in een naam ruimte (afgeschaft)|Er zijn geen dimensies|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SuccessfulRequests|Geslaagde aanvragen (preview-versie)|Count|Totaal|Geslaagde aanvragen voor micro soft. EventHub. (Preview-versie)|Er zijn geen dimensies|
|ServerErrors|Server fouten. (Preview-versie)|Count|Totaal|Server fouten voor micro soft. EventHub. (Preview-versie)|Er zijn geen dimensies|
|UserErrors|Gebruikers fouten. (Preview-versie)|Count|Totaal|Gebruikers fouten voor micro soft. EventHub. (Preview-versie)|Er zijn geen dimensies|
|QuotaExceededErrors|Quota overschreden fouten. (Preview-versie)|Count|Totaal|Quotum overschrijdt fouten voor micro soft. EventHub. (Preview-versie)|Er zijn geen dimensies|
|ThrottledRequests|Vertraagde aanvragen. (Preview-versie)|Count|Totaal|Beperkte aanvragen voor micro soft. EventHub. (Preview-versie)|Er zijn geen dimensies|
|IncomingRequests|Inkomende aanvragen (preview-versie)|Count|Totaal|Binnenkomende aanvragen voor micro soft. EventHub. (Preview-versie)|Er zijn geen dimensies|
|IncomingMessages|Inkomende berichten (preview-versie)|Count|Totaal|Binnenkomende berichten voor micro soft. EventHub. (Preview-versie)|Er zijn geen dimensies|
|OutgoingMessages|Uitgaande berichten (preview-versie)|Count|Totaal|Uitgaande berichten voor micro soft. EventHub. (Preview-versie)|Er zijn geen dimensies|
|IncomingBytes|Binnenkomende bytes. (Preview-versie)|Bytes|Totaal|Binnenkomende bytes voor micro soft. EventHub. (Preview-versie)|Er zijn geen dimensies|
|OutgoingBytes|Uitgaande bytes. (Preview-versie)|Bytes|Totaal|Uitgaande bytes voor micro soft. EventHub. (Preview-versie)|Er zijn geen dimensies|
|ActiveConnections|ActiveConnection (preview-versie)|Count|Average|Totaal aantal actieve verbindingen voor micro soft. EventHub. (Preview-versie)|Er zijn geen dimensies|
|ConnectionsOpened|Geopende verbindingen. (Preview-versie)|Count|Average|Geopende verbindingen voor micro soft. EventHub. (Preview-versie)|Er zijn geen dimensies|
|ConnectionsClosed|Verbindingen gesloten. (Preview-versie)|Count|Average|Gesloten verbindingen voor micro soft. EventHub. (Preview-versie)|Er zijn geen dimensies|
|CaptureBacklog|Achterstand vastleggen. (Preview-versie)|Count|Totaal|Achterstand vastleggen voor micro soft. EventHub. (Preview-versie)|Er zijn geen dimensies|
|CapturedMessages|Vastgelegde berichten. (Preview-versie)|Count|Totaal|Vastgelegde berichten voor micro soft. EventHub. (Preview-versie)|Er zijn geen dimensies|
|CapturedBytes|Vastgelegde bytes. (Preview-versie)|Bytes|Totaal|Vastgelegde bytes voor micro soft. EventHub. (Preview-versie)|Er zijn geen dimensies|
|CPU|CPU (preview-versie)|Procent|Maximum|CPU-gebruik voor het event hub-cluster als een percentage|Rol|
|AvailableMemory|Beschikbaar geheugen (preview-versie)|Count|Maximum|Beschikbaar geheugen voor het event hub-cluster in bytes|Rol|

## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|GatewayRequests|Gateway aanvragen|Count|Totaal|Aantal gateway-aanvragen|ClusterDnsName, HTTP status|
|CategorizedGatewayRequests|Gecategoriseerde gateway aanvragen|Count|Totaal|Aantal gateway aanvragen per categorie (1xx/2xx/3xx/4xx/5xx)|ClusterDnsName, HTTP status|
|NumActiveWorkers|Aantal actieve werk rollen|Count|Maximum|Aantal actieve werk rollen|ClusterDnsName, MetricName|

## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ObservedMetricValue|Waargenomen metrische waarde|Count|Average|De waarde die wordt berekend door automatisch schalen wanneer deze wordt uitgevoerd|MetricTriggerSource|
|MetricThreshold|Drempel waarde voor metrische gegevens|Count|Average|De geconfigureerde drempel waarde voor automatisch schalen wanneer automatisch schalen is uitgevoerd.|MetricTriggerRule|
|ObservedCapacity|Waargenomen capaciteit|Count|Average|De capaciteit die is gerapporteerd voor automatisch schalen wanneer deze wordt uitgevoerd.|Er zijn geen dimensies|
|ScaleActionsInitiated|Schaal acties gestart|Count|Totaal|De richting van de schaal bewerking.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

(Open bare preview)

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Beschikbaarheid|Procent|Average|Percentage voltooide beschikbaarheids tests|availabilityResult/naam, availabilityResult/locatie|
|availabilityResults/aantal|Beschikbaarheidstests|Count|Count|Aantal beschikbaarheids tests|availabilityResult/naam, availabilityResult/locatie, availabilityResult/geslaagd|
|availabilityResults/duur|Duur beschikbaarheids test|MilliSeconds|Average|Duur beschikbaarheids test|availabilityResult/naam, availabilityResult/locatie, availabilityResult/geslaagd|
|browserTimings/networkDuration|Netwerk verbindings tijd voor laden van pagina|MilliSeconds|Average|Tijd tussen de gebruikers aanvraag en de netwerk verbinding. Inclusief DNS-Zoek-en transport verbinding.|Er zijn geen dimensies|
|browserTimings/processingDuration|Verwerkings tijd van client|MilliSeconds|Average|Tijd tussen het ontvangen van de laatste byte van een document totdat de DOM is geladen. Asynchrone aanvragen kunnen nog steeds worden verwerkt.|Er zijn geen dimensies|
|browserTimings/receiveDuration|Reactie tijd van ontvangst|MilliSeconds|Average|Tijd tussen de eerste en laatste bytes, of tot de verbinding wordt verbroken.|Er zijn geen dimensies|
|browserTimings/sendDuration|Aanvraag tijd verzenden|MilliSeconds|Average|Tijd tussen netwerk verbinding en ontvangst van de eerste byte.|Er zijn geen dimensies|
|browserTimings/totalDuration|Laad tijd van browser pagina|MilliSeconds|Average|Tijd van de gebruikers aanvraag totdat DOM, opmaak modellen, scripts en installatie kopieën worden geladen.|Er zijn geen dimensies|
|afhankelijkheden/aantal|Afhankelijkheids aanroepen|Count|Count|Aantal aanroepen van de toepassing naar externe bronnen.|afhankelijkheid/type, afhankelijkheid/requests, afhankelijkheid/geslaagd, bewerking/synthetisch, Cloud/roleInstance, Cloud/rolnaam|
|afhankelijkheden/duur|Duur van afhankelijkheid|MilliSeconds|Average|Duur van de aanroepen van de toepassing naar externe bronnen.|afhankelijkheid/type, afhankelijkheid/requests, afhankelijkheid/geslaagd, bewerking/synthetisch, Cloud/roleInstance, Cloud/rolnaam|
|afhankelijkheden/mislukt|Mislukte afhankelijkheids aanroepen|Count|Count|Aantal mislukte afhankelijkheids aanroepen van de toepassing naar externe bronnen.|afhankelijkheid/type, afhankelijkheid/requests, bewerking/synthetisch, Cloud/roleInstance, Cloud/rolnaam|
|Page views/aantal|Pagina weergaven|Count|Count|Aantal pagina weergaven.|bewerking/synthetisch|
|Page views/duur|Laad tijd pagina weergave|MilliSeconds|Average|Laad tijd pagina weergave|bewerking/synthetisch|
|performanceCounters/requestExecutionTime|Uitvoerings tijd van de HTTP-aanvraag|MilliSeconds|Average|Uitvoerings tijd van de meest recente aanvraag.|Cloud-roleInstance|
|Performance Counters/requestsInQueue|HTTP-aanvragen in de toepassings wachtrij|Count|Average|Lengte van de wachtrij voor toepassings aanvragen.|Cloud-roleInstance|
|performanceCounters/requestsPerSecond|Frequentie van HTTP-aanvragen|CountPerSecond|Average|Het aantal aanvragen voor de toepassing per seconde van ASP.NET.|Cloud-roleInstance|
|performanceCounters/exceptionsPerSecond|Uitzonderings frequentie|CountPerSecond|Average|Aantal verwerkte en onverwerkte uitzonde ringen die worden gerapporteerd aan Windows, inclusief .NET-uitzonde ringen en onbeheerde uitzonde ringen die worden geconverteerd naar .NET-uitzonde ringen.|Cloud-roleInstance|
|performanceCounters/processIOBytesPerSecond|I/o-frequentie van processen|BytesPerSecond|Average|Totaal aantal in bestanden, netwerk en apparaten gelezen en geschreven bytes per seconde.|Cloud-roleInstance|
|performanceCounters/processCpuPercentage|CPU verwerken|Procent|Average|Het percentage van de verstreken tijd dat alle proces threads de processor hebben gebruikt om instructies uit te voeren. Dit kan variëren tussen 0 en 100. Deze metrische gegevens duiden de prestaties van alleen het W3wp-proces aan.|Cloud-roleInstance|
|performanceCounters/processorCpuPercentage|Processor tijd|Procent|Average|Het percentage tijd dat de processor spendeert aan niet-inactieve threads.|Cloud-roleInstance|
|performanceCounters/memoryAvailableBytes|Beschikbaar geheugen|Bytes|Average|Fysiek geheugen dat direct beschikbaar is voor toewijzing aan een proces of voor systeem gebruik.|Cloud-roleInstance|
|performanceCounters/processPrivateBytes|Privé-bytes verwerken|Bytes|Average|Geheugen dat exclusief wordt toegewezen aan de processen van de bewaakte toepassing.|Cloud-roleInstance|
|aanvragen/duur|Server reactietijd|MilliSeconds|Average|Tijd tussen het ontvangen van een HTTP-aanvraag en het volt ooien van het verzenden van het antwoord.|aanvraag-requests, aanvraag-resultCode, bewerking/synthetisch, Cloud/roleInstance, aanvraag/geslaagd, Cloud/rolnaam|
|aanvragen/aantal|Server aanvragen|Count|Count|Aantal voltooide HTTP-aanvragen.|aanvraag-requests, aanvraag-resultCode, bewerking/synthetisch, Cloud/roleInstance, aanvraag/geslaagd, Cloud/rolnaam|
|aanvragen/mislukt|Mislukte aanvragen|Count|Count|Het aantal HTTP-aanvragen dat is gemarkeerd als mislukt. In de meeste gevallen zijn dit aanvragen met een antwoord code > = 400 en niet gelijk is aan 401.|aanvraag-requests, aanvraag-resultCode, bewerking/synthetisch, Cloud/roleInstance, Cloud/rolnaam|
|aanvragen/frequentie|Aantal server aanvragen|CountPerSecond|Average|Aantal server aanvragen per seconde|aanvraag-requests, aanvraag-resultCode, bewerking/synthetisch, Cloud/roleInstance, aanvraag/geslaagd, Cloud/rolnaam|
|uitzonde ringen/aantal|Uitzonderingen|Count|Count|Totaal aantal niet-onderschepte uitzonde ringen.|Cloud/rolnaam, Cloud/roleInstance, client/type|
|uitzonde ringen/browser|Browseruitzonderingen|Count|Count|Aantal niet-onderschepte uitzonde ringen dat in de browser wordt gegenereerd.|Er zijn geen dimensies|
|uitzonde ringen/server|Server uitzonderingen|Count|Count|Aantal niet-onderschepte uitzonde ringen dat is opgetreden in de server toepassing.|Cloud/rolnaam, Cloud-roleInstance|
|traceringen/aantal|Traceringen|Count|Count|Aantal tracerings documenten|Trace/severityLevel, Operation/synthetisch, Cloud/rolnaam, Cloud/roleInstance|

## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ServiceApiHit|Totaal aantal treffers in de service-API|Count|Count|Totaal aantal treffers in de service-API|ActivityType, ActivityName|
|ServiceApiLatency|Algehele latentie van Service-API|Milliseconden|Average|Algemene latentie van Service-API-aanvragen|Activity type, Activiteitsnummer, status code|
|ServiceApiResult|Totale resultaten van Service-API|Count|Count|Totaal aantal resultaten van Service-API|Activity type, Activiteitsnummer, status code|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CacheUtilization|Cache gebruik|Procent|Average|Gebruiks niveau in het cluster bereik|None|
|QueryDuration|Query duur|Milliseconden|Average|De duur van query's in seconden|Querystatus|
|IngestionUtilization|Opname gebruik|Procent|Average|Verhouding van gebruikte opname sleuven in het cluster|None|
|KeepAlive|Actief houden|Count|Average|Sanity-controle geeft aan dat het cluster reageert op query's|None|
|IngestionVolumeInMB|Opname volume (in MB)|Count|Totaal|Totaal volume van opgenomen gegevens aan het cluster (in MB)|Database|
|IngestionLatencyInSeconds|Opname latentie (in seconden)|Seconden|Average|Opname tijd van de bron (bijvoorbeeld bericht is in EventHub) naar het cluster in enkele seconden|None|
|EventProcessedForEventHubs|Verwerkte gebeurtenissen (voor Event Hubs)|Count|Totaal|Aantal gebeurtenissen dat door het cluster wordt verwerkt bij het opnemen vanuit Event hub|None|
|IngestionResult|Opname resultaat|Count|Count|Aantal opname bewerkingen|Status|
|CPU|CPU|Procent|Average|Niveau CPU-gebruik|None|
| ContinuousExportNumOfRecordsExported | Het aantal records dat wordt geëxporteerd in continue export | Count | Totaal | Het aantal records dat wordt geëxporteerd voor elk opslag artefact dat is geschreven tijdens de export bewerking  | None |
| ExportUtilization | Gebruik exporteren | Procent | Maximum | Gebruik exporteren | None |
| ContinuousExportPendingCount | Aantal doorlopend exporteren in behandeling | Count | Maximum | Het aantal in behandeling zijnde doorlopende export taken dat gereed is voor uitvoering | None |
| ContinuousExportMaxLatenessMinutes | Maximale achterstand voor continue export | Count | Maximum | De maximale tijd in minuten van alle doorlopende uitvoer die in behandeling zijn en gereed zijn voor uitvoering | None |

## <a name="microsoftlocationbasedservicesaccounts"></a>Microsoft.LocationBasedServices/accounts

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Gebruik|Gebruik|Count|Count|Aantal API-aanroepen|ApiCategory, ApiName|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|RunsStarted|Uitvoeringen gestart|Count|Totaal|Aantal uitvoeringen van werk stroom gestart.|Er zijn geen dimensies|
|RunsCompleted|Uitvoeringen voltooid|Count|Totaal|Aantal voltooide werk stroom uitvoeringen.|Er zijn geen dimensies|
|RunsSucceeded|Geslaagde uitvoeringen|Count|Totaal|Aantal geslaagde werk stroom uitvoeringen.|Er zijn geen dimensies|
|RunsFailed|Uitvoeringen mislukt|Count|Totaal|Het aantal uitvoeringen van de werk stroom is mislukt.|Er zijn geen dimensies|
|RunsCancelled|Uitvoeringen geannuleerd|Count|Totaal|Aantal uitgevoerde werk stroom uitvoeringen geannuleerd.|Er zijn geen dimensies|
|RunLatency|Uitvoerings latentie|Seconden|Average|Latentie van voltooide werk stroom uitvoeringen.|Er zijn geen dimensies|
|RunSuccessLatency|Latentie van geslaagde uitvoering|Seconden|Average|Latentie van geslaagde werk stroom uitvoeringen.|Er zijn geen dimensies|
|RunThrottledEvents|Vertraagde gebeurtenissen uitvoeren|Count|Totaal|Aantal werk stroom acties of trigger vertraagde gebeurtenissen.|Er zijn geen dimensies|
|RunFailurePercentage|Percentage mislukte uitvoeringen|Procent|Totaal|Percentage mislukte werk stroom uitvoeringen.|Er zijn geen dimensies|
|ActionsStarted|Gestarte acties |Count|Totaal|Aantal gestarte werk stroom acties.|Er zijn geen dimensies|
|ActionsCompleted|Acties voltooid |Count|Totaal|Aantal voltooide werk stroom acties.|Er zijn geen dimensies|
|ActionsSucceeded|Acties geslaagd |Count|Totaal|Aantal geslaagde werk stroom acties.|Er zijn geen dimensies|
|ActionsFailed|Mislukte acties|Count|Totaal|Aantal mislukte werk stroom acties.|Er zijn geen dimensies|
|ActionsSkipped|Overgeslagen acties |Count|Totaal|Aantal overgeslagen werk stroom acties.|Er zijn geen dimensies|
|ActionLatency|Actie latentie |Seconden|Average|Latentie van voltooide werk stroom acties.|Er zijn geen dimensies|
|ActionSuccessLatency|Latentie geslaagde acties |Seconden|Average|Latentie van geslaagde werk stroom acties.|Er zijn geen dimensies|
|ActionThrottledEvents|Door actie vertraagde gebeurtenissen|Count|Totaal|Aantal door werk stroom actie vertraagde gebeurtenissen..|Er zijn geen dimensies|
|TriggersStarted|Triggers gestart |Count|Totaal|Aantal gestarte werk stroom triggers.|Er zijn geen dimensies|
|TriggersCompleted|Triggers voltooid |Count|Totaal|Aantal voltooide werk stroom triggers.|Er zijn geen dimensies|
|TriggersSucceeded|Geslaagde triggers |Count|Totaal|Aantal geslaagde werk stroom triggers.|Er zijn geen dimensies|
|TriggersFailed|Mislukte triggers |Count|Totaal|Aantal mislukte werk stroom triggers.|Er zijn geen dimensies|
|TriggersSkipped|Triggers overgeslagen|Count|Totaal|Aantal overgeslagen werk stroom triggers.|Er zijn geen dimensies|
|TriggersFired|Geactiveerde triggers |Count|Totaal|Aantal geactiveerde werk stroom triggers.|Er zijn geen dimensies|
|TriggerLatency|Latentie van trigger |Seconden|Average|Latentie van voltooide werk stroom triggers.|Er zijn geen dimensies|
|TriggerFireLatency|Brand latentie activeren |Seconden|Average|Latentie van geactiveerde werk stroom triggers.|Er zijn geen dimensies|
|TriggerSuccessLatency|Latentie van trigger geslaagd |Seconden|Average|Latentie van geslaagde werk stroom triggers.|Er zijn geen dimensies|
|TriggerThrottledEvents|Trigger beperkings gebeurtenissen|Count|Totaal|Aantal door werk stroom trigger vertraagde gebeurtenissen.|Er zijn geen dimensies|
|BillableActionExecutions|Factureer bare actie-uitvoeringen|Count|Totaal|Aantal uitvoeringen van werk stroom acties dat wordt gefactureerd.|Er zijn geen dimensies|
|BillableTriggerExecutions|Factureer bare trigger uitvoeringen|Count|Totaal|Aantal uitvoeringen van werk stroom trigger dat wordt gefactureerd.|Er zijn geen dimensies|
|TotalBillableExecutions|Totaal aantal factureer bare uitvoeringen|Count|Totaal|Aantal uitgevoerde werk stroom uitvoeringen dat wordt gefactureerd.|Er zijn geen dimensies|
|BillingUsageNativeOperation|Facturerings gebruik voor uitvoering van systeem eigen bewerkingen|Count|Totaal|Aantal uitvoeringen van de native bewerking dat wordt gefactureerd.|Er zijn geen dimensies|
|BillingUsageStandardConnector|Facturerings gebruik voor het uitvoeren van standaard-connectors|Count|Totaal|Aantal uitvoeringen van Standard-connector dat wordt gefactureerd.|Er zijn geen dimensies|
|BillingUsageStorageConsumption|Facturerings gebruik voor uitvoeringen van opslag verbruik|Count|Totaal|Aantal uitvoeringen van opslag verbruik dat wordt gefactureerd.|Er zijn geen dimensies|
|BillingUsageNativeOperation|Facturerings gebruik voor uitvoering van systeem eigen bewerkingen|Count|Totaal|Aantal uitvoeringen van de native bewerking dat wordt gefactureerd.|Er zijn geen dimensies|
|BillingUsageStandardConnector|Facturerings gebruik voor het uitvoeren van standaard-connectors|Count|Totaal|Aantal uitvoeringen van Standard-connector dat wordt gefactureerd.|Er zijn geen dimensies|
|BillingUsageStorageConsumption|Facturerings gebruik voor uitvoeringen van opslag verbruik|Count|Totaal|Aantal uitvoeringen van opslag verbruik dat wordt gefactureerd.|Er zijn geen dimensies|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|RunsStarted|Uitvoeringen gestart|Count|Totaal|Aantal uitvoeringen van werk stroom gestart.|Er zijn geen dimensies|
|RunsCompleted|Uitvoeringen voltooid|Count|Totaal|Aantal voltooide werk stroom uitvoeringen.|Er zijn geen dimensies|
|RunsSucceeded|Geslaagde uitvoeringen|Count|Totaal|Aantal geslaagde werk stroom uitvoeringen.|Er zijn geen dimensies|
|RunsFailed|Uitvoeringen mislukt|Count|Totaal|Het aantal uitvoeringen van de werk stroom is mislukt.|Er zijn geen dimensies|
|RunsCancelled|Uitvoeringen geannuleerd|Count|Totaal|Aantal uitgevoerde werk stroom uitvoeringen geannuleerd.|Er zijn geen dimensies|
|RunLatency|Uitvoerings latentie|Seconden|Average|Latentie van voltooide werk stroom uitvoeringen.|Er zijn geen dimensies|
|RunSuccessLatency|Latentie van geslaagde uitvoering|Seconden|Average|Latentie van geslaagde werk stroom uitvoeringen.|Er zijn geen dimensies|
|RunThrottledEvents|Vertraagde gebeurtenissen uitvoeren|Count|Totaal|Aantal werk stroom acties of trigger vertraagde gebeurtenissen.|Er zijn geen dimensies|
|RunStartThrottledEvents|Vertraagde gebeurtenissen uitvoeren|Count|Totaal|Aantal uitgevoerde vertraagde gebeurtenissen voor de werk stroom.|Er zijn geen dimensies|
|RunFailurePercentage|Percentage mislukte uitvoeringen|Procent|Totaal|Percentage mislukte werk stroom uitvoeringen.|Er zijn geen dimensies|
|ActionsStarted|Gestarte acties |Count|Totaal|Aantal gestarte werk stroom acties.|Er zijn geen dimensies|
|ActionsCompleted|Acties voltooid |Count|Totaal|Aantal voltooide werk stroom acties.|Er zijn geen dimensies|
|ActionsSucceeded|Acties geslaagd |Count|Totaal|Aantal geslaagde werk stroom acties.|Er zijn geen dimensies|
|ActionsFailed|Mislukte acties |Count|Totaal|Aantal mislukte werk stroom acties.|Er zijn geen dimensies|
|ActionsSkipped|Overgeslagen acties |Count|Totaal|Aantal overgeslagen werk stroom acties.|Er zijn geen dimensies|
|ActionLatency|Actie latentie |Seconden|Average|Latentie van voltooide werk stroom acties.|Er zijn geen dimensies|
|ActionSuccessLatency|Latentie geslaagde acties |Seconden|Average|Latentie van geslaagde werk stroom acties.|Er zijn geen dimensies|
|ActionThrottledEvents|Door actie vertraagde gebeurtenissen|Count|Totaal|Aantal door werk stroom actie vertraagde gebeurtenissen..|Er zijn geen dimensies|
|TriggersStarted|Triggers gestart |Count|Totaal|Aantal gestarte werk stroom triggers.|Er zijn geen dimensies|
|TriggersCompleted|Triggers voltooid |Count|Totaal|Aantal voltooide werk stroom triggers.|Er zijn geen dimensies|
|TriggersSucceeded|Geslaagde triggers |Count|Totaal|Aantal geslaagde werk stroom triggers.|Er zijn geen dimensies|
|TriggersFailed|Mislukte triggers |Count|Totaal|Aantal mislukte werk stroom triggers.|Er zijn geen dimensies|
|TriggersSkipped|Triggers overgeslagen|Count|Totaal|Aantal overgeslagen werk stroom triggers.|Er zijn geen dimensies|
|TriggersFired|Geactiveerde triggers |Count|Totaal|Aantal geactiveerde werk stroom triggers.|Er zijn geen dimensies|
|TriggerLatency|Latentie van trigger |Seconden|Average|Latentie van voltooide werk stroom triggers.|Er zijn geen dimensies|
|TriggerFireLatency|Brand latentie activeren |Seconden|Average|Latentie van geactiveerde werk stroom triggers.|Er zijn geen dimensies|
|TriggerSuccessLatency|Latentie van trigger geslaagd |Seconden|Average|Latentie van geslaagde werk stroom triggers.|Er zijn geen dimensies|
|TriggerThrottledEvents|Trigger beperkings gebeurtenissen|Count|Totaal|Aantal door werk stroom trigger vertraagde gebeurtenissen.|Er zijn geen dimensies|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Gebruik van werk stroom processor voor Integratieserviceomgeving|Procent|Average|Gebruik van werk stroom processoren voor de integratie service omgeving.|Er zijn geen dimensies|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Geheugen gebruik van werk stroom voor Integratieserviceomgeving|Procent|Average|Geheugen gebruik van werk stroom voor de integratie service omgeving.|Er zijn geen dimensies|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Processor gebruik van connector voor Integratieserviceomgeving|Procent|Average|Het processor gebruik van de connector voor de integratie service omgeving.|Er zijn geen dimensies|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Geheugen gebruik van connector voor Integratieserviceomgeving|Procent|Average|Geheugen gebruik van connector voor de integratie service omgeving.|Er zijn geen dimensies|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Voltooide uitvoeringen|Voltooide uitvoeringen|Count|Totaal|Het aantal uitvoeringen dat is voltooid voor deze werk ruimte|Scenario|
|Gestart uitvoeringen|Gestart uitvoeringen|Count|Totaal|Aantal uitvoeringen gestart voor deze werk ruimte|Scenario|
|Mislukte uitvoeringen|Mislukte uitvoeringen|Count|Totaal|Aantal uitvoeringen is mislukt voor deze werk ruimte|Scenario|

## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Gebruik|Gebruik|Count|Count|Aantal API-aanroepen|ApiCategory, ApiName, ResultType, ResponseCode|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|Beschik baarheid van de Api's|ApiCategory, ApiName|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/Volumes

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AverageOtherLatency|Gemiddelde andere latentie|ms/op|Average|De gemiddelde andere latentie (die niet lezen of schrijven is) in milliseconden per bewerking|Er zijn geen dimensies|
|AverageReadLatency|Gemiddelde lees latentie|ms/op|Average|Gemiddelde lees latentie in milliseconden per bewerking|Er zijn geen dimensies|
|AverageTotalLatency|Gemiddelde latentie in milliseconden|ms/op|Average|Gemiddelde totale latentie in milliseconden per bewerking|Er zijn geen dimensies|
|AverageWriteLatency|Gemiddelde schrijf latentie|ms/op|Average|Gemiddelde schrijf latentie in milliseconden per bewerking|Er zijn geen dimensies|
|FilesystemOtherOps|Andere OPS-bestands systeem|OPS|Average|Aantal andere bewerkingen van het bestands systeem (dat is niet lezen of schrijven)|Er zijn geen dimensies|
|FilesystemReadOps|OPS-lezen van bestands systeem|OPS|Average|Aantal lees bewerkingen van het bestands systeem|Er zijn geen dimensies|
|FilesystemTotalOps|Totaal aantal OPS-bestands systeem|OPS|Average|Som van alle bestandssysteem bewerkingen|Er zijn geen dimensies|
|FilesystemWriteOps|File System write OPS|OPS|Average|Aantal schrijf bewerkingen van het bestands systeem|Er zijn geen dimensies|
|IoBytesPerOtherOps|I/o-bytes per andere OPS|bytes/op|Average|Aantal in-en uitgaande bytes per andere bewerking (dat is niet lezen of schrijven)|Er zijn geen dimensies|
|IoBytesPerReadOps|I/o bytes per Read OPS|bytes/op|Average|Aantal bytes in/uit per Lees bewerking|Er zijn geen dimensies|
|IoBytesPerTotalOps|I/o-bytes per op alle bewerkingen|bytes/op|Average|Som van alle bewerkingen in/uit bytes|Er zijn geen dimensies|
|IoBytesPerWriteOps|I/o-bytes per write OPS|bytes/op|Average|Aantal in/uit bytes per schrijf bewerking|Er zijn geen dimensies|
|OtherIops|Andere IOPS|bewerkingen per seconde|Average|Andere in/uit-bewerking per seconde|Er zijn geen dimensies|
|OtherThroughput|Andere door Voer|MB|Average|Andere door Voer (dat is niet lezen of schrijven) in mega bytes per seconde|Er zijn geen dimensies|
|ReadIops|IOPS lezen|bewerkingen per seconde|Average|In-en uitvoer bewerkingen per seconde|Er zijn geen dimensies|
|ReadThroughput|Lees doorvoer|MB|Average|Lees doorvoer in mega bytes per seconde|Er zijn geen dimensies|
|TotalIops|Totaal aantal IOPS|bewerkingen per seconde|Average|Som van alle in-en uitvoer bewerkingen per seconde|Er zijn geen dimensies|
|TotalThroughput|Totale door Voer|MB|Average|Som van alle door Voer in mega bytes per seconde|Er zijn geen dimensies|
|VolumeAllocatedSize|Grootte van toegewezen volume|bytes|Average|Toegewezen grootte van het volume (niet de werkelijk gebruikte bytes)|Er zijn geen dimensies|
|VolumeLogicalSize|Logische volume grootte|bytes|Average|Logische grootte van het volume (gebruikte bytes)|Er zijn geen dimensies|
|VolumeSnapshotSize|Grootte van moment opname van volume|bytes|Average|Grootte van alle moment opnamen in volume|Er zijn geen dimensies|
|WriteIops|IOPS schrijven|bewerkingen per seconde|Average|In-en uitvoer bewerkingen per seconde|Er zijn geen dimensies|
|WriteThroughput|Schrijf doorvoer|MB|Average|Schrijf doorvoer in mega bytes per seconde|Er zijn geen dimensies|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Grootte toegewezen volume groep|bytes|Average|Toegewezen grootte van de pool (niet de werkelijk gebruikte bytes)|Er zijn geen dimensies|
|VolumePoolAllocatedUsed|Gebruikte volume groep|bytes|Average|Gebruikte grootte van de pool is toegewezen|Er zijn geen dimensies|
|VolumePoolTotalLogicalSize|Totale logische grootte van volume groep|bytes|Average|Som van de logische grootte van alle volumes die deel uitmaken van de groep|Er zijn geen dimensies|
|VolumePoolTotalSnapshotSize|Grootte van de moment opname van de volume groep|bytes|Average|Som van alle moment opnamen in de pool|Er zijn geen dimensies|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BytesSentRate|Verzonden bytes|Count|Totaal|Het aantal bytes dat de netwerk interface heeft verzonden|Er zijn geen dimensies|
|BytesReceivedRate|Ontvangen bytes|Count|Totaal|Het aantal bytes dat de netwerk interface heeft ontvangen|Er zijn geen dimensies|
|PacketsSentRate|Verzonden pakketten|Count|Totaal|Aantal pakketten dat de netwerk interface heeft verzonden|Er zijn geen dimensies|
|PacketsReceivedRate|Ontvangen pakketten|Count|Totaal|Aantal pakketten dat de netwerk interface heeft ontvangen|Er zijn geen dimensies|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|VipAvailability|Beschik baarheid gegevenspad|Count|Average|Gemiddelde Beschik baarheid van gegevens paden per tijds duur van Load Balancer|FrontendIPAddress, FrontendPort|
|DipAvailability|Status van Health probe|Count|Average|Gemiddelde status van Load Balancer Health probe per tijds duur|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Aantal bytes|Count|Totaal|Totaal aantal verzonden bytes binnen tijds periode|FrontendIPAddress, FrontendPort, direction|
|PacketCount|Aantal pakketten|Count|Totaal|Totaal aantal verzonden pakketten binnen tijds periode|FrontendIPAddress, FrontendPort, direction|
|SYNCount|SYN-aantal|Count|Totaal|Totaal aantal SYN-pakketten verzonden binnen tijds periode|FrontendIPAddress, FrontendPort, direction|
|SnatConnectionCount|Aantal SNAT-verbindingen|Count|Totaal|Totaal aantal nieuwe SNAT-verbindingen dat binnen een tijds periode is gemaakt|FrontendIPAddress, BackendIPAddress, ConnectionState|
|AllocatedSnatPorts|Toegewezen SNAT-poorten (preview-versie)|Count|Totaal|Totaal aantal toegewezen SNAT-poorten binnen tijds periode|FrontendIPAddress, BackendIPAddress, ProtocolType|
|UsedSnatPorts|Gebruikte SNAT-poorten (preview-versie)|Count|Totaal|Totaal aantal SNAT-poorten gebruikt binnen tijds periode|FrontendIPAddress, BackendIPAddress, ProtocolType|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|QueryVolume|Query volume|Count|Totaal|Aantal verzonden query's voor een DNS-zone|Er zijn geen dimensies|
|RecordSetCount|Aantal record sets|Count|Maximum|Aantal record sets in een DNS-zone|Er zijn geen dimensies|
|RecordSetCapacityUtilization|Capaciteits gebruik van record sets|Procent|Maximum|Percentage van de set-capaciteit van de record die wordt gebruikt door een DNS-zone|Er zijn geen dimensies|

## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PacketsInDDoS|DDoS inkomende pakketten|CountPerSecond|Maximum|DDoS inkomende pakketten|Er zijn geen dimensies|
|PacketsDroppedDDoS|DDoS inkomende pakketten verwijderd|CountPerSecond|Maximum|DDoS inkomende pakketten verwijderd|Er zijn geen dimensies|
|PacketsForwardedDDoS|DDoS inkomende pakketten doorgestuurd|CountPerSecond|Maximum|DDoS inkomende pakketten doorgestuurd|Er zijn geen dimensies|
|TCPPacketsInDDoS|Binnenkomende TCP-pakketten DDoS|CountPerSecond|Maximum|Binnenkomende TCP-pakketten DDoS|Er zijn geen dimensies|
|TCPPacketsDroppedDDoS|DDoS binnenkomende TCP-pakketten|CountPerSecond|Maximum|DDoS binnenkomende TCP-pakketten|Er zijn geen dimensies|
|TCPPacketsForwardedDDoS|Doorgestuurde binnenkomende TCP-pakketten DDoS|CountPerSecond|Maximum|Doorgestuurde binnenkomende TCP-pakketten DDoS|Er zijn geen dimensies|
|UDPPacketsInDDoS|Binnenkomende UDP-pakketten DDoS|CountPerSecond|Maximum|Binnenkomende UDP-pakketten DDoS|Er zijn geen dimensies|
|UDPPacketsDroppedDDoS|Verwijderde binnenkomende UDP-pakketten DDoS|CountPerSecond|Maximum|Verwijderde binnenkomende UDP-pakketten DDoS|Er zijn geen dimensies|
|UDPPacketsForwardedDDoS|Door inkomende UDP-pakketten DDoS doorgestuurd|CountPerSecond|Maximum|Door inkomende UDP-pakketten DDoS doorgestuurd|Er zijn geen dimensies|
|BytesInDDoS|Binnenkomende bytes DDoS|BytesPerSecond|Maximum|Binnenkomende bytes DDoS|Er zijn geen dimensies|
|BytesDroppedDDoS|Binnenkomende bytes verloren DDoS|BytesPerSecond|Maximum|Binnenkomende bytes verloren DDoS|Er zijn geen dimensies|
|BytesForwardedDDoS|Doorgestuurde binnenkomende bytes DDoS|BytesPerSecond|Maximum|Doorgestuurde binnenkomende bytes DDoS|Er zijn geen dimensies|
|TCPBytesInDDoS|DDoS binnenkomende TCP-bytes|BytesPerSecond|Maximum|DDoS binnenkomende TCP-bytes|Er zijn geen dimensies|
|TCPBytesDroppedDDoS|DDoS binnenkomende TCP-bytes|BytesPerSecond|Maximum|DDoS binnenkomende TCP-bytes|Er zijn geen dimensies|
|TCPBytesForwardedDDoS|DDoS doorgestuurde binnenkomende TCP-bytes|BytesPerSecond|Maximum|DDoS doorgestuurde binnenkomende TCP-bytes|Er zijn geen dimensies|
|UDPBytesInDDoS|Binnenkomende UDP-bytes DDoS|BytesPerSecond|Maximum|Binnenkomende UDP-bytes DDoS|Er zijn geen dimensies|
|UDPBytesDroppedDDoS|Binnenkomend UDP-bytes verloren DDoS|BytesPerSecond|Maximum|Binnenkomend UDP-bytes verloren DDoS|Er zijn geen dimensies|
|UDPBytesForwardedDDoS|Doorgestuurde binnenkomende UDP-bytes DDoS|BytesPerSecond|Maximum|Doorgestuurde binnenkomende UDP-bytes DDoS|Er zijn geen dimensies|
|IfUnderDDoSAttack|Onder DDoS-aanval of niet|Count|Maximum|Onder DDoS-aanval of niet|Er zijn geen dimensies|
|DDoSTriggerTCPPackets|Binnenkomende TCP-pakketten om DDoS-beperking te activeren|CountPerSecond|Maximum|Binnenkomende TCP-pakketten om DDoS-beperking te activeren|Er zijn geen dimensies|
|DDoSTriggerUDPPackets|Binnenkomende UDP-pakketten om DDoS-beperking te activeren|CountPerSecond|Maximum|Binnenkomende UDP-pakketten om DDoS-beperking te activeren|Er zijn geen dimensies|
|DDoSTriggerSYNPackets|Inkomende SYN-pakketten om DDoS-beperking te activeren|CountPerSecond|Maximum|Inkomende SYN-pakketten om DDoS-beperking te activeren|Er zijn geen dimensies|
|VipAvailability|Beschik baarheid gegevenspad|Count|Average|Gemiddelde Beschik baarheid van IP-adressen per tijds duur|Poort|
|ByteCount|Aantal bytes|Count|Totaal|Totaal aantal verzonden bytes binnen tijds periode|Poort, richting|
|PacketCount|Aantal pakketten|Count|Totaal|Totaal aantal verzonden pakketten binnen tijds periode|Poort, richting|
|SynCount|SYN-aantal|Count|Totaal|Totaal aantal SYN-pakketten verzonden binnen tijds periode|Poort, richting|

## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ApplicationRuleHit|Aantal treffers toepassings regels|Count|Totaal|Aantal keer dat toepassings regels zijn geraakt|Status, Reason, Protocol|
|NetworkRuleHit|Aantal treffers in netwerk regels|Count|Totaal|Aantal keren dat netwerk regels zijn geraakt|Status, Reason, Protocol|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Doorvoer|Doorvoer|BytesPerSecond|Totaal|Aantal bytes per seconde dat de Application Gateway heeft bediend|Er zijn geen dimensies|
|UnhealthyHostCount|Aantal hosts met slechte status|Count|Average|Aantal beschadigde backend-hosts|BackendSettingsPool|
|healthyHostCount|Aantal goede hosts|Count|Average|Aantal gezonde backend-hosts|BackendSettingsPool|
|TotalRequests|Totaal aantal aanvragen|Count|Totaal|Aantal geslaagde aanvragen dat Application Gateway heeft bediend|BackendSettingsPool|
|FailedRequests|Mislukte aanvragen|Count|Totaal|Aantal mislukte aanvragen dat Application Gateway heeft bediend|BackendSettingsPool|
|ResponseStatus|Reactie status|Count|Totaal|Http-antwoord status geretourneerd door Application Gateway|HttpStatusGroup|
|CurrentConnections|Huidige verbindingen|Count|Totaal|Aantal actieve verbindingen dat tot stand is gebracht met Application Gateway|Er zijn geen dimensies|
|CapacityUnits|Huidige capaciteits eenheden|Count|Average|Verbruikte capaciteits eenheden|Er zijn geen dimensies|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AverageBandwidth|Gateway-S2S-band breedte|BytesPerSecond|Average|Gemiddeld aantal site-naar-site-band breedte van een gateway in bytes per seconde|Er zijn geen dimensies|
|P2SBandwidth|Gateway P2S-band breedte|BytesPerSecond|Average|Gemiddelde Point-to-site band breedte van een gateway in bytes per seconde|Er zijn geen dimensies|
|P2SConnectionCount|Aantal P2S-verbindingen|Count|Maximum|Aantal Point-to-site-verbindingen van een gateway|Protocol|
|TunnelAverageBandwidth|Tunnel bandbreedte|BytesPerSecond|Average|Gemiddelde band breedte van een tunnel in bytes per seconde|ConnectionName, RemoteIP|
|TunnelEgressBytes|Bytes voor uitgaand tunnels|Bytes|Totaal|Uitgaande bytes van een tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Bytes van de tunnel ingang|Bytes|Totaal|Binnenkomende bytes van een tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Tunnel-uituitgangs pakketten|Count|Totaal|Aantal uitgaande pakketten van een tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Tunnel ingangs pakketten|Count|Totaal|Binnenkomend pakket aantal van een tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Uitschakeling van niet-overeenkomende TS-pakketten door tunnel|Count|Totaal|Aantal uitgevallen uitgaande pakketten van de selectie van de verkeers kiezer komen niet overeen met een tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Verloren gegane pakketten door de tunnel ingang TS komen niet overeen|Count|Totaal|Aantal inkomende pakketten in de verkeers selectie niet overeenkomen met een tunnel|ConnectionName, RemoteIP|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Bits die Azure per seconde binnenkomen|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Bits egressing Azure per seconde|PeeringType|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Bits die Azure per seconde binnenkomen|Er zijn geen dimensies|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Bits egressing Azure per seconde|Er zijn geen dimensies|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Bits die Azure per seconde binnenkomen|Er zijn geen dimensies|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Bits egressing Azure per seconde|Er zijn geen dimensies|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|QpsByEndpoint|Query's op eind punt geretourneerd|Count|Totaal|Aantal keren dat een Traffic Manager eind punt is geretourneerd in het opgegeven tijds bestek|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Eindpunt status op eind punt|Count|Maximum|1 als de test status van een eind punt is ingeschakeld, 0 anders.|EndpointName|

## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Tests mislukt|Procent|Average|% van de controles van connectiviteits controle is mislukt|Er zijn geen dimensies|
|AverageRoundtripMs|Gem. retour tijd (MS)|MilliSeconds|Average|Gemiddelde Round-retour tijd van het netwerk (MS) voor connectiviteits controle tests die zijn verzonden tussen de bron en het doel|Er zijn geen dimensies|

## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|RequestCount|Aantal aanvragen|Count|Totaal|Het aantal client aanvragen dat wordt geleverd door de HTTP/S-proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Aanvraag grootte|Bytes|Totaal|Het aantal bytes dat is verzonden als aanvragen van clients naar de HTTP/S-proxy|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Grootte van antwoord|Bytes|Totaal|Het aantal bytes dat is verzonden als reacties van HTTP/S-proxy naar clients|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Aantal back-aanvragen|Count|Totaal|Het aantal aanvragen dat is verzonden vanaf de HTTP/S-proxy naar back-end|Http status, HttpStatusGroup, back-end|
|BackendRequestLatency|Latentie van back-upaanvraag|MilliSeconds|Average|De tijd die wordt berekend vanaf het moment dat de aanvraag door de HTTP/S-proxy naar de back-end werd verzonden, totdat de HTTP/S-proxy de laatste antwoord byte van de back-end heeft ontvangen|Back-end|
|TotalLatency|Totale latentie|MilliSeconds|Average|De tijd die wordt berekend vanaf het moment dat de client aanvraag door de HTTP/S-proxy werd ontvangen totdat de client de laatste antwoord byte van de HTTP/S-proxy heeft bevestigd|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Back-status percentage|Procent|Average|Het percentage geslaagde status controles van de HTTP/S-proxy naar back-end|Back-end, hosts|
|WebApplicationFirewallRequestCount|Aantal aanvragen voor Web Application firewall|Count|Totaal|Het aantal client aanvragen dat is verwerkt door de Web Application firewall|Beleidsnaam, regelnaam, actie|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|registratie. alle|Registratie bewerkingen|Count|Totaal|Het aantal voltooide registratie bewerkingen (gemaakte bijwerk query's en verwijderingen). |Er zijn geen dimensies|
|registratie. Create|Bewerkingen voor het maken van registratie|Count|Totaal|Het aantal gemaakte registraties.|Er zijn geen dimensies|
|registratie. update|Registratie-update bewerkingen|Count|Totaal|Het aantal voltooide registratie-updates.|Er zijn geen dimensies|
|registratie. ophalen|Lees bewerkingen voor registratie|Count|Totaal|Het aantal geslaagde registratie query's.|Er zijn geen dimensies|
|registratie. Delete|Verwijderings bewerkingen voor registratie|Count|Totaal|Het aantal voltooide registraties dat is verwijderd.|Er zijn geen dimensies|
|e-mail|Inkomende berichten|Count|Totaal|Het aantal geslaagde verzend-API-aanroepen. |Er zijn geen dimensies|
|inkomend. gepland|Geplande push meldingen verzonden|Count|Totaal|Geplande push meldingen geannuleerd|Er zijn geen dimensies|
|Binnenkomend. gepland. annuleren|Geplande push meldingen geannuleerd|Count|Totaal|Geplande push meldingen geannuleerd|Er zijn geen dimensies|
|gepland. in behandeling|Geplande meldingen in behandeling|Count|Totaal|Geplande meldingen in behandeling|Er zijn geen dimensies|
|installatie. alle|Bewerkingen voor installatie beheer|Count|Totaal|Bewerkingen voor installatie beheer|Er zijn geen dimensies|
|installation.get|Installatie bewerkingen ophalen|Count|Totaal|Installatie bewerkingen ophalen|Er zijn geen dimensies|
|installatie. upsert|Installatie bewerkingen maken of bijwerken|Count|Totaal|Installatie bewerkingen maken of bijwerken|Er zijn geen dimensies|
|installatie. patch|Patch-installatie bewerkingen|Count|Totaal|Patch-installatie bewerkingen|Er zijn geen dimensies|
|installation.delete|Installatie bewerkingen verwijderen|Count|Totaal|Installatie bewerkingen verwijderen|Er zijn geen dimensies|
|outgoing.allpns.success|Geslaagde meldingen|Count|Totaal|Het aantal geslaagde meldingen.|Er zijn geen dimensies|
|outgoing.allpns.invalidpayload|Payload-fouten|Count|Totaal|Het aantal pushes dat is mislukt omdat de PNS een ongeldige Payload-fout heeft geretourneerd.|Er zijn geen dimensies|
|uitgaande. allpns. pnserror|Externe meldingen systeem fouten|Count|Totaal|Het aantal pushes dat is mislukt omdat er een probleem is opgetreden bij het communiceren met de PNS (er zijn verificatie problemen uitgesloten).|Er zijn geen dimensies|
|uitgaande. allpns. channelerror|Kanaal fouten|Count|Totaal|Het aantal pushes dat is mislukt omdat het kanaal ongeldig is en niet is gekoppeld aan de juiste app beperkt of verlopen.|Er zijn geen dimensies|
|outgoing.allpns.badorexpiredchannel|Ongeldige of verlopen kanaal fouten|Count|Totaal|Het aantal pushes dat is mislukt omdat het kanaal/token-registratie in de registratie is verlopen of ongeldig is.|Er zijn geen dimensies|
|outgoing.wns.success|Geslaagde meldingen WNS|Count|Totaal|Het aantal geslaagde meldingen.|Er zijn geen dimensies|
|outgoing.wns.invalidcredentials|WNS-verificatie fouten (ongeldige referenties)|Count|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd. (Windows Live herkent de referenties niet).|Er zijn geen dimensies|
|outgoing.wns.badchannel|WNS ongeldige kanaal fout|Count|Totaal|Het aantal pushes dat is mislukt omdat de kanaal in de registratie niet is herkend (WNS-status: 404 niet gevonden).|Er zijn geen dimensies|
|outgoing.wns.expiredchannel|WNS-fout met verlopen kanaal|Count|Totaal|Het aantal pushes dat is mislukt omdat de kanaal is verlopen (WNS-status: 410 verdwenen).|Er zijn geen dimensies|
|uitgaande. wns. throttled|WNS beperkte meldingen|Count|Totaal|Het aantal pushes dat is mislukt omdat WNS deze app beperkt (WNS-status: 406 niet toegestaan).|Er zijn geen dimensies|
|outgoing.wns.tokenproviderunreachable|WNS-verificatie fouten (onbereikbaar)|Count|Totaal|Windows Live is niet bereikbaar.|Er zijn geen dimensies|
|uitgaande. wns. invalidtoken|WNS-verificatie fouten (ongeldig token)|Count|Totaal|Het token dat is gegeven aan WNS is niet geldig (WNS-status: 401 niet toegestaan).|Er zijn geen dimensies|
|outgoing.wns.wrongtoken|WNS-autorisatie fouten (onjuist token)|Count|Totaal|Het token dat is gegeven aan WNS is geldig, maar voor een andere toepassing (WNS-status: 403 verboden). Dit kan gebeuren als de kanaal in de registratie is gekoppeld aan een andere app. Controleer of de client-app is gekoppeld aan dezelfde app waarvan de referenties zich in de notification hub bevinden.|Er zijn geen dimensies|
|outgoing.wns.invalidnotificationformat|Ongeldige indeling van WNS-melding|Count|Totaal|De indeling van de melding is ongeldig (WNS-status: 400). Houd er rekening mee dat WNS niet alle ongeldige payloads weigert.|Er zijn geen dimensies|
|outgoing.wns.invalidnotificationsize|Fout met ongeldige grootte van WNS-melding|Count|Totaal|De nettolading van de melding is te groot (WNS-status: 413).|Er zijn geen dimensies|
|outgoing.wns.channelthrottled|WNS-kanaal beperkt|Count|Totaal|De melding is verwijderd omdat de kanaal in de registratie is beperkt (WNS-reactie header: X-WNS-notification status: channelThrottled).|Er zijn geen dimensies|
|outgoing.wns.channeldisconnected|Verbinding met WNS-kanaal verbroken|Count|Totaal|De melding is verwijderd omdat de kanaal in de registratie is beperkt (WNS-reactie header: X-WNS-DeviceConnectionStatus: disconnected).|Er zijn geen dimensies|
|uitgaande. wns. dropd|WNS-verwijderde meldingen|Count|Totaal|De melding is verwijderd omdat de kanaal in de registratie is beperkt (X-WNS-notification status: verwijderd maar niet X-WNS-DeviceConnectionStatus: disconnected).|Er zijn geen dimensies|
|uitgaande. wns. pnserror|WNS-fouten|Count|Totaal|Er is geen melding bezorgd vanwege fouten in de communicatie met WNS.|Er zijn geen dimensies|
|uitgaande. wns. authenticationerror|WNS-verificatie fouten|Count|Totaal|Er is een melding niet bezorgd omdat er fouten zijn opgetreden tijdens de communicatie met Windows Live ongeldige referenties of een onjuist token.|Er zijn geen dimensies|
|outgoing.apns.success|Geslaagde meldingen van APNS|Count|Totaal|Het aantal geslaagde meldingen.|Er zijn geen dimensies|
|outgoing.apns.invalidcredentials|APNS-autorisatie fouten|Count|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd.|Er zijn geen dimensies|
|outgoing.apns.badchannel|Fout met ongeldige APNS-kanaal|Count|Totaal|Het aantal pushes dat is mislukt omdat het token ongeldig is (status code van het APNS-binaire Protocol: 8). Status code van het APNS-HTTP-protocol: 400 met ' BadDeviceToken ').|Er zijn geen dimensies|
|outgoing.apns.expiredchannel|Fout bij verlopen van APNS-kanaal|Count|Totaal|Het aantal tokens dat ongeldig is gemaakt door het APNS-feedback kanaal.|Er zijn geen dimensies|
|outgoing.apns.invalidnotificationsize|Fout door ongeldige grootte van APNS-melding|Count|Totaal|Het aantal pushes dat is mislukt omdat de payload te groot is (status code van het binaire APNS-Protocol: 7).|Er zijn geen dimensies|
|outgoing.apns.pnserror|APNS-fouten|Count|Totaal|Het aantal pushes dat is mislukt vanwege fouten in de communicatie met APNS.|Er zijn geen dimensies|
|outgoing.gcm.success|Geslaagde meldingen GCM|Count|Totaal|Het aantal geslaagde meldingen.|Er zijn geen dimensies|
|outgoing.gcm.invalidcredentials|GCM-verificatie fouten (ongeldige referenties)|Count|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd.|Er zijn geen dimensies|
|outgoing.gcm.badchannel|GCM ongeldige kanaal fout|Count|Totaal|Het aantal pushes dat is mislukt omdat de registratie in de registratie niet is herkend (GCM-resultaat: ongeldige registratie).|Er zijn geen dimensies|
|outgoing.gcm.expiredchannel|GCM-fout met verlopen kanaal|Count|Totaal|Het aantal pushes dat is mislukt omdat de registratie in de registratie is verlopen (GCM resultaat: NotRegistered).|Er zijn geen dimensies|
|uitgaande. GCM. throttled|GCM beperkte meldingen|Count|Totaal|Het aantal pushes dat is mislukt omdat GCM deze app heeft beperkt (GCM-status code: 501-599 of resultaat: niet beschikbaar).|Er zijn geen dimensies|
|uitgaande. GCM. invalidnotificationformat|Ongeldige indeling van GCM-melding|Count|Totaal|Het aantal pushes dat is mislukt omdat de payload niet juist is geformatteerd (GCM-resultaat: InvalidDataKey of InvalidTtl).|Er zijn geen dimensies|
|outgoing.gcm.invalidnotificationsize|Fout met ongeldige grootte van GCM-melding|Count|Totaal|Het aantal pushes dat is mislukt omdat de payload te groot is (GCM-resultaat: MessageTooBig).|Er zijn geen dimensies|
|outgoing.gcm.wrongchannel|GCM onjuiste kanaal fout|Count|Totaal|Het aantal pushes dat is mislukt omdat de registratie in de registratie niet is gekoppeld aan de huidige app (GCM-resultaat: InvalidPackageName).|Er zijn geen dimensies|
|uitgaande. GCM. pnserror|GCM-fouten|Count|Totaal|Het aantal pushes dat is mislukt vanwege fouten in de communicatie met GCM.|Er zijn geen dimensies|
|uitgaande. GCM. authenticationerror|GCM-verificatie fouten|Count|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd, de referenties zijn geblokkeerd of de SenderId niet juist is geconfigureerd in de app (GCM resultaat: MismatchedSenderId).|Er zijn geen dimensies|
|uitgaand. mpns. geslaagd|Geslaagde meldingen MPNS|Count|Totaal|Het aantal geslaagde meldingen.|Er zijn geen dimensies|
|outgoing.mpns.invalidcredentials|Ongeldige referenties MPNS|Count|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd.|Er zijn geen dimensies|
|outgoing.mpns.badchannel|MPNS ongeldige kanaal fout|Count|Totaal|Het aantal pushes dat is mislukt omdat de kanaal in de registratie niet is herkend (MPNS-status: 404 niet gevonden).|Er zijn geen dimensies|
|uitgaande. mpns. throttled|MPNS beperkte meldingen|Count|Totaal|Het aantal pushes dat is mislukt omdat MPNS deze app beperkt (WNS MPNS: 406 niet toegestaan).|Er zijn geen dimensies|
|uitgaande. mpns. invalidnotificationformat|Ongeldige indeling van MPNS-melding|Count|Totaal|Het aantal pushes dat is mislukt omdat de payload van de melding te groot is.|Er zijn geen dimensies|
|outgoing.mpns.channeldisconnected|Verbinding met MPNS-kanaal verbroken|Count|Totaal|Het aantal pushes dat is mislukt omdat de kanaal van de registratie is verbroken (MPNS-status: 412 niet gevonden).|Er zijn geen dimensies|
|uitgaande. mpns. dropd|MPNS-verwijderde meldingen|Count|Totaal|Het aantal pushes dat is verwijderd door MPNS (MPNS-reactie header: X-notification status: QueueFull of onderdrukt).|Er zijn geen dimensies|
|uitgaande. mpns. pnserror|MPNS-fouten|Count|Totaal|Het aantal pushes dat is mislukt vanwege fouten in de communicatie met MPNS.|Er zijn geen dimensies|
|uitgaande. mpns. authenticationerror|MPNS-verificatie fouten|Count|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd.|Er zijn geen dimensies|
|notificationhub. pushes|Alle uitgaande meldingen|Count|Totaal|Alle uitgaande meldingen van de notification hub|Er zijn geen dimensies|
|binnenkomende. alle. aanvragen|Alle inkomende aanvragen|Count|Totaal|Totaal aantal binnenkomende aanvragen voor een notification hub|Er zijn geen dimensies|
|inkomend. alle. failedrequests|Alle binnenkomende mislukte aanvragen|Count|Totaal|Totaal aantal binnenkomende mislukte aanvragen voor een notification hub|Er zijn geen dimensies|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage vrije inodes Average_|% Vrije inodes|Count|Average|Percentage vrije inodes Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|% Beschik bare ruimte Average_|Percentage vrije ruimte|Count|Average|% Beschik bare ruimte Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_% gebruikte inodes|% Gebruikte inodes|Count|Average|Average_% gebruikte inodes|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Percentage gebruikte ruimte Average_|Percentage gebruikte ruimte|Count|Average|Percentage gebruikte ruimte Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk Lees bewerkingen in bytes per seconde|Bytes gelezen op schijf/sec|Count|Average|Average_Disk Lees bewerkingen in bytes per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk Reads/sec|Schijf lezen per seconde|Count|Average|Average_Disk Reads/sec|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk overdrachten per seconde|Schijfoverdrachten per seconde|Count|Average|Average_Disk overdrachten per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk geschreven bytes per seconde|Bytes geschreven naar schijf/sec|Count|Average|Average_Disk geschreven bytes per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk schrijf bewerkingen per seconde|Schijf schrijven per seconde|Count|Average|Average_Disk schrijf bewerkingen per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Free Megabytes|Beschikbare Megabytes|Count|Average|Average_Free Megabytes|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Logical schijf bytes per seconde|Logische schijf Bytes per seconde|Count|Average|Average_Logical schijf bytes per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Percentage beschik bare geheugen Average_|Percentage beschikbaar geheugen|Count|Average|Percentage beschik bare geheugen Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_ percentage beschik bare wissel ruimte|Percentage beschik bare wissel ruimte|Count|Average|Average_ percentage beschik bare wissel ruimte|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_ percentage gebruikt geheugen|Percentage gebruikt geheugen|Count|Average|Average_ percentage gebruikt geheugen|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_ percentage gebruikte wissel ruimte|Percentage gebruikte wissel ruimte|Count|Average|Average_ percentage gebruikte wissel ruimte|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Available MB geheugen|Beschikbaar geheugen in megabytes|Count|Average|Average_Available MB geheugen|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Available MB wisselen|Beschik bare mega bytes wisselen|Count|Average|Average_Available MB wisselen|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Page Lees bewerkingen per seconde|Gelezen pagina's per seconde|Count|Average|Average_Page Lees bewerkingen per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Page schrijf bewerkingen per seconde|Geschreven pagina's per seconde|Count|Average|Average_Page schrijf bewerkingen per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Pages/sec.|Pagina's per seconde|Count|Average|Average_Pages/sec.|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Used MB wissel ruimte|Gebruikte MB wissel ruimte|Count|Average|Average_Used MB wissel ruimte|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Used geheugen MB|Gebruikt geheugen Mbytes|Count|Average|Average_Used geheugen MB|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Verzonden Average_Total bytes|Totaal aantal verzonden Bytes|Count|Average|Verzonden Average_Total bytes|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Ontvangen Average_Total bytes|Totaal aantal ontvangen Bytes|Count|Average|Ontvangen Average_Total bytes|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Total bytes|Totaal aantal bytes|Count|Average|Average_Total bytes|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Verzonden Average_Total pakketten|Totaal aantal verzonden pakketten|Count|Average|Verzonden Average_Total pakketten|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Ontvangen Average_Total pakketten|Totaal aantal ontvangen pakketten|Count|Average|Ontvangen Average_Total pakketten|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Total RX-fouten|Totaal aantal RX-fouten|Count|Average|Average_Total RX-fouten|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Total TX-fouten|Totaal aantal TX-fouten|Count|Average|Average_Total TX-fouten|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Total conflicten|Totaal aantal conflicten|Count|Average|Average_Total conflicten|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Avg. Schijf sec/lezen|Gemiddelde Lees tijd schijf|Count|Average|Average_Avg. Schijf sec/lezen|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Avg. Schijfoverdrachten per seconde|Gemiddelde tijd schijf overdracht|Count|Average|Average_Avg. Schijfoverdrachten per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Avg. Schijf sec/schrijven|Gemiddelde schrijf tijd schijf|Count|Average|Average_Avg. Schijf sec/schrijven|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Physical schijf bytes per seconde|Bytes van fysieke schijf per seconde|Count|Average|Average_Physical schijf bytes per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Beschermde tijd van Average_Pct|Pct-geprivilegieerde tijd|Count|Average|Beschermde tijd van Average_Pct|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Gebruikers tijd van Average_Pct|Pct-gebruikers tijd|Count|Average|Gebruikers tijd van Average_Pct|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Used geheugen KB|Gebruikte geheugen-kBytes|Count|Average|Average_Used geheugen KB|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Virtual gedeeld geheugen|Virtueel gedeeld geheugen|Count|Average|Average_Virtual gedeeld geheugen|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_ percentage DPC-tijd|Percentage DPC-tijd|Count|Average|Average_ percentage DPC-tijd|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|% Niet-actieve tijd Average_|Percentage niet-actieve tijd|Count|Average|% Niet-actieve tijd Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Percentage interrupt-tijd van Average_|Percentage interrupt-tijd|Count|Average|Percentage interrupt-tijd van Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_% i/o-wacht tijd|% I/o-wacht tijd|Count|Average|Average_% i/o-wacht tijd|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Tijd van Average_% leuk|Percentage tijd in Nice|Count|Average|Tijd van Average_% leuk|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Percentage tijd in beschermde modus Average_|Percentage tijd in beschermde modus|Count|Average|Percentage tijd in beschermde modus Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Percentage processor tijd van Average_|% Processortijd|Count|Average|Percentage processor tijd van Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Percentage gebruikers tijd van Average_|Percentage gebruikers tijd|Count|Average|Percentage gebruikers tijd van Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Free fysiek geheugen|Vrij fysiek geheugen|Count|Average|Average_Free fysiek geheugen|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Free ruimte in wissel geheugen bestanden|Vrije ruimte in wissel geheugen bestanden|Count|Average|Average_Free ruimte in wissel geheugen bestanden|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Virtueel geheugen Average_Free|Vrij virtueel geheugen|Count|Average|Virtueel geheugen Average_Free|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Processes|Processen|Count|Average|Average_Processes|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Size opgeslagen in Wissel bestanden|Grootte opgeslagen in Wissel bestanden|Count|Average|Average_Size opgeslagen in Wissel bestanden|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Uptime|Actieve tijdsduur|Count|Average|Average_Uptime|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Users|Gebruikers|Count|Average|Average_Users|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Avg. Schijf sec/lezen|Gemiddelde Lees tijd schijf|Count|Average|Average_Avg. Schijf sec/lezen|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Avg. Schijf sec/schrijven|Gemiddelde schrijf tijd schijf|Count|Average|Average_Avg. Schijf sec/schrijven|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Wachtrij lengte van Average_Current schijf|Huidige wachtrij lengte voor de schijf|Count|Average|Wachtrij lengte van Average_Current schijf|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk Reads/sec|Schijf lezen per seconde|Count|Average|Average_Disk Reads/sec|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk overdrachten per seconde|Schijfoverdrachten per seconde|Count|Average|Average_Disk overdrachten per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk schrijf bewerkingen per seconde|Schijf schrijven per seconde|Count|Average|Average_Disk schrijf bewerkingen per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Free Megabytes|Beschikbare Megabytes|Count|Average|Average_Free Megabytes|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|% Beschik bare ruimte Average_|Percentage vrije ruimte|Count|Average|% Beschik bare ruimte Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Available MB|Beschikbare megabytes (MB)|Count|Average|Average_Available MB|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_% toegewezen bytes in gebruik|% Toegewezen bytes in gebruik|Count|Average|Average_% toegewezen bytes in gebruik|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Ontvangen Average_Bytes per seconde|Ontvangen bytes per seconde|Count|Average|Ontvangen Average_Bytes per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Verzonden Average_Bytes per seconde|Verzonden bytes per seconde|Count|Average|Verzonden Average_Bytes per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Bytes totaal per seconde|Totaal aantal bytes per seconde|Count|Average|Average_Bytes totaal per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Percentage processor tijd van Average_|% Processortijd|Count|Average|Percentage processor tijd van Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Lengte van Average_Processor wachtrij|Lengte van de processor wachtrij|Count|Average|Lengte van Average_Processor wachtrij|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Hart|Hart|Count|Totaal|Hart|Computer, OSType, versie, SourceComputerId|
|Update|Update|Count|Average|Update|Computer, product, classificatie, update State, optioneel, goedgekeurd|
|Gebeurtenis|Gebeurtenis|Count|Average|Gebeurtenis|Source, EventLog, computer, EventCategory, EventLevel, EventLevelName, Event gebeurtenis|

## <a name="microsoftpowerbidedicatedcapacities"></a>Micro soft. PowerBIDedicated/capaciteiten

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|QueryDuration|Query duur|Milliseconden|Average|DAX-query duur in laatste interval|Er zijn geen dimensies|
|QueryPoolJobQueueLength|Threads: lengte van taak wachtrij voor query pool|Count|Average|Aantal taken in de wachtrij van de query thread pool.|Er zijn geen dimensies|
|qpu_high_utilization_metric|Hoog gebruik van QPU|Count|Totaal|QPU hoog gebruik in de laatste minuut, 1 voor hoog QPU gebruik, anders 0|Er zijn geen dimensies|
|memory_metric|Geheugen|Bytes|Average|Geheugenmetabase. Bereik 0-3 GB voor a1, 0-5 GB voor a2, 0-10 GB voor a3, 0-25 GB voor A4, 0-50 GB voor A5 en 0-100 GB voor A6|Er zijn geen dimensies|
|memory_thrashing_metric|Geheugen overbelasting|Procent|Average|Gemiddeld geheugen overbelasting.|Er zijn geen dimensies|

## <a name="microsoftrelaynamespaces"></a>Micro soft. relay/naam ruimten

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ListenerConnections-Success|ListenerConnections-Success|Count|Totaal|Geslaagde ListenerConnections voor micro soft. relay.|EntityName|
|ListenerConnections-client error|ListenerConnections-client error|Count|Totaal|Client error op ListenerConnections voor micro soft. relay.|EntityName|
|ListenerConnections-server error|ListenerConnections-server error|Count|Totaal|Server error op ListenerConnections voor micro soft. relay.|EntityName|
|SenderConnections-Success|SenderConnections-Success|Count|Totaal|Geslaagde SenderConnections voor micro soft. relay.|EntityName|
|SenderConnections-client error|SenderConnections-client error|Count|Totaal|Client error op SenderConnections voor micro soft. relay.|EntityName|
|SenderConnections-server error|SenderConnections-server error|Count|Totaal|Server error op SenderConnections voor micro soft. relay.|EntityName|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Count|Totaal|Totale ListenerConnections voor micro soft. relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Count|Totaal|Totaal aantal SenderConnections-aanvragen voor micro soft. relay.|EntityName|
|ActiveConnections|ActiveConnections|Count|Totaal|Totaal aantal ActiveConnection voor micro soft. relay.|EntityName|
|ActiveListeners|ActiveListeners|Count|Totaal|Totale ActiveListeners voor micro soft. relay.|EntityName|
|BytesTransferred|BytesTransferred|Count|Totaal|Totale BytesTransferred voor micro soft. relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Count|Totaal|Totale ListenerDisconnects voor micro soft. relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Count|Totaal|Totale SenderDisconnects voor micro soft. relay.|EntityName|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SearchLatency|Zoek latentie|Seconden|Average|Gemiddelde Zoek latentie voor de zoek service|Er zijn geen dimensies|
|SearchQueriesPerSecond|Zoek query's per seconde|CountPerSecond|Average|Zoek query's per seconde voor de zoek service|Er zijn geen dimensies|
|ThrottledSearchQueriesPercentage|Percentage vertraagde Zoek query's|Procent|Average|Percentage Zoek query's dat is beperkt tot de zoek service|Er zijn geen dimensies|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SuccessfulRequests|Geslaagde aanvragen (preview-versie)|Count|Totaal|Totaal aantal geslaagde aanvragen voor een naam ruimte (preview-versie)|EntityName|
|ServerErrors|Server fouten. (Preview-versie)|Count|Totaal|Server fouten voor micro soft. ServiceBus. (Preview-versie)|EntityName|
|UserErrors|Gebruikers fouten. (Preview-versie)|Count|Totaal|Gebruikers fouten voor micro soft. ServiceBus. (Preview-versie)|EntityName|
|ThrottledRequests|Vertraagde aanvragen. (Preview-versie)|Count|Totaal|Beperkte aanvragen voor micro soft. ServiceBus. (Preview-versie)|EntityName|
|IncomingRequests|Inkomende aanvragen (preview-versie)|Count|Totaal|Binnenkomende aanvragen voor micro soft. ServiceBus. (Preview-versie)|EntityName|
|IncomingMessages|Inkomende berichten (preview-versie)|Count|Totaal|Binnenkomende berichten voor micro soft. ServiceBus. (Preview-versie)|EntityName|
|OutgoingMessages|Uitgaande berichten (preview-versie)|Count|Totaal|Uitgaande berichten voor micro soft. ServiceBus. (Preview-versie)|EntityName|
|ActiveConnections|ActiveConnection (preview-versie)|Count|Totaal|Totaal aantal actieve verbindingen voor micro soft. ServiceBus. (Preview-versie)|Er zijn geen dimensies|
|Grootte|Grootte (preview-versie)|Bytes|Average|Grootte van een wachtrij/onderwerp in bytes. (Preview-versie)|EntityName|
|Berichten|Aantal berichten in een wachtrij/onderwerp. (Preview-versie)|Count|Average|Aantal berichten in een wachtrij/onderwerp. (Preview-versie)|EntityName|
|ActiveMessages|Aantal actieve berichten in een wachtrij/onderwerp. (Preview-versie)|Count|Average|Aantal actieve berichten in een wachtrij/onderwerp. (Preview-versie)|EntityName|
|DeadletteredMessages|Aantal onbestelbare berichten in een wachtrij/onderwerp. (Preview-versie)|Count|Average|Aantal onbestelbare berichten in een wachtrij/onderwerp. (Preview-versie)|EntityName|
|ScheduledMessages|Aantal geplande berichten in een wachtrij/onderwerp. (Preview-versie)|Count|Average|Aantal geplande berichten in een wachtrij/onderwerp. (Preview-versie)|EntityName|
|CPUXNS|CPU-gebruik per naam ruimte|Procent|Maximum|Metrische gegevens voor CPU-gebruik van service bus Premium-naam ruimte|Er zijn geen dimensies|
|WSXNS|Gebruik van geheugen grootte per naam ruimte|Procent|Maximum|Metrische gegevens over geheugen gebruik voor service bus Premium-naam ruimte|Er zijn geen dimensies|

## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Count|Average|CPU toegewezen aan deze container in millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Bytes|Average|Geheugen toegewezen aan deze container in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Count|Average|Werkelijk CPU-gebruik in millicores|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Bytes|Average|Werkelijk geheugen gebruik in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Procent|Average|Gebruik van CPU voor deze container als percentage van AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Procent|Average|Gebruik van CPU voor deze container als percentage van AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Count|Average|Status van Service Fabric mesh-toepassing|ApplicationName, status|
|ServiceStatus|ServiceStatus|Count|Average|Integriteits status van een service in Service Fabric mesh-toepassing|ApplicationName, Status, ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Count|Average|Integriteits status van een service replica in Service Fabric mesh-toepassing|ApplicationName, Status, ServiceName, ServiceReplicaName|
|Container status|Container status|Count|Average|Status van de container in Service Fabric mesh-toepassing|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, Status|
|RestartCount|RestartCount|Count|Average|Telling van een container in Service Fabric mesh-toepassing opnieuw starten|ApplicationName, Status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ConnectionCount|Aantal verbindingen|Count|Maximum|De hoeveelheid gebruikers verbinding.|Eindpunt|
|MessageCount|Aantal berichten|Count|Totaal|De totale hoeveelheid berichten.|Er zijn geen dimensies|
|InboundTraffic|Binnenkomend verkeer|Bytes|Totaal|Het inkomende verkeer van de service|Er zijn geen dimensies|
|OutboundTraffic|Uitgaand verkeer|Bytes|Totaal|Het uitgaande verkeer van de service|Er zijn geen dimensies|
|UserErrors|Gebruikers fouten|Procent|Maximum|Het percentage gebruikers fouten|Er zijn geen dimensies|
|SystemErrors|Systeem fouten|Procent|Maximum|Het percentage systeem fouten|Er zijn geen dimensies|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|allocated_data_storage|Toegewezen gegevens ruimte|Bytes|Average|Toegewezen gegevens ruimte. Niet van toepassing op data warehouses.|Er zijn geen dimensies|
|app_cpu_billed|App CPU gefactureerd|Count|Totaal|App CPU gefactureerd. Is van toepassing op serverloze data bases.|Er zijn geen dimensies|
|app_cpu_percent|CPU-percentage van app|Procent|Average|CPU-percentage van de app. Is van toepassing op serverloze data bases.|Er zijn geen dimensies|
|app_memory_percent|Percentage gebruikt geheugen van app|Procent|Average|Percentage gebruikt geheugen voor app. Is van toepassing op serverloze data bases.|Er zijn geen dimensies|
|blocked_by_firewall|Geblokkeerd door de firewall|Count|Totaal|Geblokkeerd door de firewall|Er zijn geen dimensies|
|cache_hit_percent|Percentage cache treffers|Procent|Maximum|Percentage van cache treffer. Is alleen van toepassing op data warehouses.|Er zijn geen dimensies|
|cache_used_percent|Percentage gebruikt cache|Procent|Maximum|Percentage gebruikt cache. Is alleen van toepassing op data warehouses.|Er zijn geen dimensies|
|connection_failed|Mislukte verbindingen|Count|Totaal|Mislukte verbindingen|Er zijn geen dimensies|
|connection_successful|Geslaagde verbindingen|Count|Totaal|Geslaagde verbindingen|Er zijn geen dimensies|
|cpu_percent|CPU-percentage|Procent|Average|CPU-percentage|Er zijn geen dimensies|
|cpu_limit|CPU-limiet|Count|Average|CPU-limiet. Is van toepassing op vCore-data bases.|Er zijn geen dimensies|
|cpu_used|CPU gebruikt|Count|Average|CPU gebruikt. Is van toepassing op vCore-data bases.|Er zijn geen dimensies|
|constateer|Deadlocks|Count|Totaal|Impassen. Niet van toepassing op data warehouses.|Er zijn geen dimensies|
|diff_backup_size_bytes|Grootte van differentiële back-upopslag|Bytes|Maximum|Cumulatieve differentiële back-upopslag. Is van toepassing op vCore-data bases. Niet van toepassing op grootschalige-data bases.|Er zijn geen dimensies|
|dtu_limit|DTU-limiet|Count|Average|DTU-limiet. Is van toepassing op DTU-gebaseerde data bases.|Er zijn geen dimensies|
|dtu_consumption_percent|DTU-percentage|Procent|Average|DTU-percentage. Is van toepassing op DTU-gebaseerde data bases.|Er zijn geen dimensies|
|dtu_used|DTU gebruikt|Count|Average|DTU gebruikt. Is van toepassing op DTU-gebaseerde data bases.|Er zijn geen dimensies|
|dw_cpu_percent|CPU-percentage van DW-knooppunt niveau|Procent|Average|CPU-percentage van DW-knooppunt niveau|DwLogicalNodeId|
|dw_physical_data_read_percent|IO-percentage gegevens-i/o-knooppunt niveau|Procent|Average|IO-percentage gegevens-i/o-knooppunt niveau|DwLogicalNodeId|
|dwu_consumption_percent|Percentage DWU|Procent|Maximum|DWU-percentage. Is alleen van toepassing op data warehouses.|Er zijn geen dimensies|
|dwu_limit|Limiet voor DWU|Count|Maximum|DWU-limiet. Is alleen van toepassing op data warehouses.|Er zijn geen dimensies|
|dwu_used|DWU gebruikt|Count|Maximum|DWU gebruikt. Is alleen van toepassing op data warehouses.|Er zijn geen dimensies|
|full_backup_size_bytes|Opslag grootte voor volledige back-up|Bytes|Maximum|Cumulatieve opslag grootte voor volledige back-ups. Is van toepassing op vCore-data bases. Niet van toepassing op grootschalige-data bases.|Er zijn geen dimensies|
|local_tempdb_usage_percent|Lokaal TempDB-percentage|Procent|Average|Lokaal TempDB-percentage. Is alleen van toepassing op data warehouses.|Er zijn geen dimensies|
|log_backup_size_bytes|Opslag grootte van logboek back-up|Bytes|Maximum|Cumulatieve opslag grootte van logboek back-up. Is van toepassing op vCore-data bases. Niet van toepassing op grootschalige-data bases.|Er zijn geen dimensies|
|log_write_percent|Logboek-IO-percentage|Procent|Average|Logboek-IO-percentage. Niet van toepassing op data warehouses.|Er zijn geen dimensies|
|physical_data_read_percent|Gegevens-I/O-percentage|Procent|Average|Gegevens-I/O-percentage|Er zijn geen dimensies|
|sessions_percent|Percentage sessies|Procent|Average|Percentage sessies. Niet van toepassing op data warehouses.|Er zijn geen dimensies|
|sqlserver_process_core_percent|Kern percentage van SQL Server proces|Procent|Maximum|Deze metrische waarde is een tijdelijke aanduiding die momenteel niet wordt ingevuld.|Er zijn geen dimensies|
|sqlserver_process_memory_percent|Percentage proces geheugen SQL Server|Procent|Maximum|Deze metrische waarde is een tijdelijke aanduiding die momenteel niet wordt ingevuld.|Er zijn geen dimensies|
|opslag|Gebruikte gegevens ruimte|Bytes|Maximum|Totale database grootte. Niet van toepassing op data warehouses.|Er zijn geen dimensies|
|storage_percent|Percentage gebruikte gegevens ruimte|Procent|Maximum|Percentage van de database grootte. Niet van toepassing op data warehouses of grootschalige-data bases.|Er zijn geen dimensies|
|tempdb_data_size|Data File grootte van tempdb|Count|Maximum|Data File grootte van tempdb-gegevens bestanden. Niet van toepassing op data warehouses. Deze metriek is beschikbaar voor data bases met behulp van het vCore-aankoop model of 100 DTU en hoger voor op DTU gebaseerde aankoop modellen.|Er zijn geen dimensies|
|tempdb_log_size|Grootte van logboek bestanden tempdb|Count|Maximum|Grootte van KB-logboek bestanden. Niet van toepassing op data warehouses. Deze metriek is beschikbaar voor data bases met behulp van het vCore-aankoop model of 100 DTU en hoger voor op DTU gebaseerde aankoop modellen.|Er zijn geen dimensies|
|tempdb_log_used_percent|Percentage gebruikt TempDB-logboek|Procent|Maximum|Percentage gebruikt TempDB-logboek. Niet van toepassing op data warehouses. Deze metriek is beschikbaar voor data bases met behulp van het vCore-aankoop model of 100 DTU en hoger voor op DTU gebaseerde aankoop modellen.|Er zijn geen dimensies|
|workers_percent|Percentage werk nemers|Procent|Average|Werknemers percentage. Niet van toepassing op data warehouses.|Er zijn geen dimensies|
|xtp_storage_percent|Percentage OLTP-opslag in het geheugen|Procent|Average|Percentage OLTP-opslag in het geheugen. Niet van toepassing op data warehouses.|Er zijn geen dimensies|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|allocated_data_storage|Toegewezen gegevens ruimte|Bytes|Average|Toegewezen gegevens ruimte|Er zijn geen dimensies|
|allocated_data_storage_percent|Percentage toegewezen gegevens ruimte|Procent|Maximum|Percentage toegewezen gegevens ruimte|Er zijn geen dimensies|
|cpu_limit|CPU-limiet|Count|Average|CPU-limiet. Is van toepassing op op vCore gebaseerde elastische Pools.|Er zijn geen dimensies|
|cpu_percent|CPU-percentage|Procent|Average|CPU-percentage|Er zijn geen dimensies|
|cpu_used|CPU gebruikt|Count|Average|CPU gebruikt. Is van toepassing op op vCore gebaseerde elastische Pools.|Er zijn geen dimensies|
|dtu_consumption_percent|DTU-percentage|Procent|Average|DTU-percentage. Is van toepassing op op DTU gebaseerde elastische Pools.|Er zijn geen dimensies|
|eDTU_limit|eDTU-limiet|Count|Average|eDTU-limiet. Is van toepassing op op DTU gebaseerde elastische Pools.|Er zijn geen dimensies|
|eDTU_used|eDTU gebruikt|Count|Average|eDTU gebruikt. Is van toepassing op op DTU gebaseerde elastische Pools.|Er zijn geen dimensies|
|log_write_percent|Logboek-IO-percentage|Procent|Average|Logboek-IO-percentage|Er zijn geen dimensies|
|physical_data_read_percent|Gegevens-I/O-percentage|Procent|Average|Gegevens-I/O-percentage|Er zijn geen dimensies|
|sessions_percent|Percentage sessies|Procent|Average|Percentage sessies|Er zijn geen dimensies|
|storage_limit|Maximale grootte van gegevens|Bytes|Average|Opslag limiet|Er zijn geen dimensies|
|storage_percent|Percentage gebruikte gegevens ruimte||Procent|Average|Opslag percentage|Er zijn geen dimensies|
|storage_used|Gebruikte gegevens ruimte|Bytes|Average|Gebruikte opslag|Er zijn geen dimensies|
|sqlserver_process_core_percent|Kern percentage van SQL Server proces|Procent|Maximum|Deze metrische waarde is een tijdelijke aanduiding die momenteel niet wordt ingevuld.|Er zijn geen dimensies|
|sqlserver_process_memory_percent|Percentage proces geheugen SQL Server|Procent|Maximum|Deze metrische waarde is een tijdelijke aanduiding die momenteel niet wordt ingevuld.|Er zijn geen dimensies|
|tempdb_data_size|Data File grootte van tempdb|Count|Maximum|Data File grootte van tempdb-gegevens bestanden. Niet van toepassing op data warehouses. Deze metriek is beschikbaar voor data bases met behulp van het vCore-aankoop model of 100 DTU en hoger voor op DTU gebaseerde aankoop modellen.|Er zijn geen dimensies|
|tempdb_log_size|Grootte van logboek bestanden tempdb|Count|Maximum|Grootte van KB-logboek bestanden. Niet van toepassing op data warehouses. Deze metriek is beschikbaar voor data bases met behulp van het vCore-aankoop model of 100 DTU en hoger voor op DTU gebaseerde aankoop modellen.|Er zijn geen dimensies|
|tempdb_log_used_percent|Percentage gebruikt TempDB-logboek|Procent|Maximum|Percentage gebruikt TempDB-logboek. Niet van toepassing op data warehouses. Deze metriek is beschikbaar voor data bases met behulp van het vCore-aankoop model of 100 DTU en hoger voor op DTU gebaseerde aankoop modellen.|Er zijn geen dimensies|
|workers_percent|Percentage werk nemers|Procent|Average|Percentage werk nemers|Er zijn geen dimensies|
|xtp_storage_percent|Percentage OLTP-opslag in het geheugen|Procent|Average|Percentage OLTP-opslag in het geheugen|Er zijn geen dimensies|

## <a name="microsoftsqlmanagedinstances"></a>Micro soft. SQL/managedInstances

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|avg_cpu_percent|Gemiddeld CPU-percentage|Procent|Average|Gemiddeld CPU-percentage|Er zijn geen dimensies|
|io_bytes_read|Gelezen IO-bytes|Bytes|Average|Gelezen IO-bytes|Er zijn geen dimensies|
|io_requests|Aantal i/o-aanvragen|Count|Average|Aantal i/o-aanvragen|Er zijn geen dimensies|
|io_bytes_written|Geschreven IO-bytes|Bytes|Average|Geschreven IO-bytes|Er zijn geen dimensies|
|reserved_storage_mb|Gereserveerde opslag ruimte|Count|Average|Gereserveerde opslag ruimte|Er zijn geen dimensies|
|storage_space_used_mb|Gebruikte opslag ruimte|Count|Average|Gebruikte opslag ruimte|Er zijn geen dimensies|
|virtual_core_count|Aantal virtuele kernen|Count|Average|Aantal virtuele kernen|Er zijn geen dimensies|

## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|UsedCapacity|Gebruikte capaciteit|Bytes|Average|Gebruikte capaciteit van account|Er zijn geen dimensies|
|Transacties|Transacties|Count|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de ResponseType-dimensie voor het aantal verschillende type reactie.|ResponseType, geotype, ApiName, authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid inkomende gegevens in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgaande gegevens in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie|
|SuccessServerLatency|Geslaagde serverlatentie|Milliseconden|Average|De gemiddelde latentie die wordt gebruikt door Azure Storage voor het verwerken van een aanvraag is gelukt, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|Geotype, ApiName, authenticatie|
|SuccessE2ELatency|Geslaagde E2E-latentie|Milliseconden|Average|De gemiddelde end-to-end latentie van geslaagde aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|Het percentage van de beschikbaarheid van de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BlobCapacity|Blobcapaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de Blob-service van het opslagaccount (in bytes).|BlobType, Tier|
|BlobCount|Aantal blobs|Count|Totaal|Het aantal blobs in de Blob-service van het opslagaccount.|BlobType|       |BlobCount|Aantal blobs|Count|Average|Het aantal blobs in de Blob-service van het opslagaccount.|BlobType, Tier|
|ContainerCount|Aantal blobcontainers|Count|Average|Het aantal containers in de Blob-service van het opslagaccount.|Er zijn geen dimensies|
|IndexCapacity|Index capaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de index van de ADLS Gen2 (hiërarchisch) in bytes.|Er zijn geen dimensies|
|Transacties|Transacties|Count|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de ResponseType-dimensie voor het aantal verschillende type reactie.|ResponseType, geotype, ApiName, authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid inkomende gegevens in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgaande gegevens in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie|
|SuccessServerLatency|Geslaagde serverlatentie|Milliseconden|Average|De gemiddelde latentie die wordt gebruikt door Azure Storage voor het verwerken van een aanvraag is gelukt, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|Geotype, ApiName, authenticatie|
|SuccessE2ELatency|Geslaagde E2E-latentie|Milliseconden|Average|De gemiddelde end-to-end latentie van geslaagde aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|Het percentage van de beschikbaarheid van de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|FileCapacity|Bestands capaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de bestands service van het opslag account in bytes.|Er zijn geen dimensies|
|FileCount|Aantal bestanden|Count|Average|Het aantal bestanden in de file-service van het opslag account.|Er zijn geen dimensies|
|FileShareCount|Aantal bestands shares|Count|Average|Het aantal bestands shares in de file-service van het opslag account.|Er zijn geen dimensies|
|Transacties|Transacties|Count|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de ResponseType-dimensie voor het aantal verschillende type reactie.|ResponseType, geotype, ApiName, authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid inkomende gegevens in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgaande gegevens in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie|
|SuccessServerLatency|Geslaagde serverlatentie|Milliseconden|Average|De gemiddelde latentie die wordt gebruikt door Azure Storage voor het verwerken van een aanvraag is gelukt, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|Geotype, ApiName, authenticatie|
|SuccessE2ELatency|Geslaagde E2E-latentie|Milliseconden|Average|De gemiddelde end-to-end latentie van geslaagde aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|Het percentage van de beschikbaarheid van de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|QueueCapacity|Wachtrijcapaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de Queue-service van het opslagaccount (in bytes).|Er zijn geen dimensies|
|QueueCount|Aantal wachtrijen|Count|Average|Het aantal wachtrijen in de Queue-service van het opslagaccount.|Er zijn geen dimensies|
|QueueMessageCount|Aantal wachtrijberichten|Count|Average|Het geschatte aantal wachtrijberichten in de Queue-service van het opslagaccount.|Er zijn geen dimensies|
|Transacties|Transacties|Count|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de ResponseType-dimensie voor het aantal verschillende type reactie.|ResponseType, geotype, ApiName, authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid inkomende gegevens in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgaande gegevens in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie|
|SuccessServerLatency|Geslaagde serverlatentie|Milliseconden|Average|De gemiddelde latentie die wordt gebruikt door Azure Storage voor het verwerken van een aanvraag is gelukt, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|Geotype, ApiName, authenticatie|
|SuccessE2ELatency|Geslaagde E2E-latentie|Milliseconden|Average|De gemiddelde end-to-end latentie van geslaagde aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|Het percentage van de beschikbaarheid van de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TableCapacity|Tabelcapaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de Table-service van het opslagaccount (in bytes).|Er zijn geen dimensies|
|TableCount|Aantal tabellen|Count|Average|Het aantal tabellen in de Table-service van het opslagaccount.|Er zijn geen dimensies|
|TableEntityCount|Aantal tabelentiteiten|Count|Average|Het aantal tabelentiteiten in de Table-service van het opslagaccount.|Er zijn geen dimensies|
|Transacties|Transacties|Count|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de ResponseType-dimensie voor het aantal verschillende type reactie.|ResponseType, geotype, ApiName, authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid inkomende gegevens in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgaande gegevens in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie|
|SuccessServerLatency|Geslaagde serverlatentie|Milliseconden|Average|De gemiddelde latentie die wordt gebruikt door Azure Storage voor het verwerken van een aanvraag is gelukt, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|Geotype, ApiName, authenticatie|
|SuccessE2ELatency|Geslaagde E2E-latentie|Milliseconden|Average|De gemiddelde end-to-end latentie van geslaagde aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|Het percentage van de beschikbaarheid van de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Resultaat van synchronisatie sessie|Count|Average|Metriek die een waarde van 1 registreert telkens wanneer het server eindpunt een synchronisatie sessie met het Cloud eindpunt heeft voltooid|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Gesynchroniseerde bestanden|Count|Totaal|Aantal gesynchroniseerde bestanden|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Bestanden die niet worden gesynchroniseerd|Count|Totaal|Aantal bestanden dat niet kan worden gesynchroniseerd|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Gesynchroniseerde bytes|Bytes|Totaal|Totale bestands grootte die is overgedragen voor synchronisatie sessies|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Online status van de server|Count|Maximum|Metriek die een waarde van 1 registreert telkens wanneer de geregistreerde server een heartbeat met het Cloud eindpunt heeft vastgelegd|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Cloud lagen intrekken|Bytes|Totaal|De totale grootte van de gegevens die door de server zijn ingetrokken|ServerName|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ResourceUtilization|Gebruikspercentage voor Streaming-eenheden|Procent|Maximum|Gebruikspercentage voor Streaming-eenheden|LogicalName, PartitionId|
|InputEvents|Invoergebeurtenissen|Count|Totaal|Invoergebeurtenissen|LogicalName, PartitionId|
|InputEventBytes|Invoergebeurtenisbytes|Bytes|Totaal|Invoergebeurtenisbytes|LogicalName, PartitionId|
|LateInputEvents|Late invoergebeurtenissen|Count|Totaal|Late invoergebeurtenissen|LogicalName, PartitionId|
|OutputEvents|Uitvoergebeurtenis|Count|Totaal|Uitvoergebeurtenis|LogicalName, PartitionId|
|ConversionErrors|Gegevensconversiefouten|Count|Totaal|Gegevensconversiefouten|LogicalName, PartitionId|
|Fouten|Runtimefouten|Count|Totaal|Runtimefouten|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Gebeurtenissen met een andere volg orde|Count|Totaal|Gebeurtenissen met een andere volg orde|LogicalName, PartitionId|
|AMLCalloutRequests|Functieaanvragen|Count|Totaal|Functieaanvragen|LogicalName, PartitionId|
|AMLCalloutFailedRequests|Mislukte functieaanvragen|Count|Totaal|Mislukte functieaanvragen|LogicalName, PartitionId|
|AMLCalloutInputEvents|Functiegebeurtenissen|Count|Totaal|Functiegebeurtenissen|LogicalName, PartitionId|
|DeserializationError|Fouten in invoerdeserialisatie|Count|Totaal|Fouten in invoerdeserialisatie|LogicalName, PartitionId|
|EarlyInputEvents|Vroege-invoergebeurtenissen|Count|Totaal|Vroege-invoergebeurtenissen|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Watermerkvertraging|Seconden|Maximum|Watermerkvertraging|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Invoervelden met achterstand|Count|Maximum|Invoervelden met achterstand|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Ontvangen invoerbronnen|Count|Totaal|Ontvangen invoerbronnen|LogicalName, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ontvangen berichten met ingang|Count|Totaal|Telling van berichten die zijn gelezen uit alle gebeurtenis bronnen van Event hub of IoT hub|Er zijn geen dimensies|
|IngressReceivedInvalidMessages|Ongeldige berichten ontvangen|Count|Totaal|Aantal ongeldige berichten gelezen uit alle gebeurtenis bronnen van Event hub of IoT hub|Er zijn geen dimensies|
|IngressReceivedBytes|Ontvangen bytes van binnenkomend verkeer|Bytes|Totaal|Het aantal gelezen bytes van alle gebeurtenis bronnen|Er zijn geen dimensies|
|IngressStoredBytes|In ingangs opgeslagen bytes|Bytes|Totaal|De totale grootte van de gebeurtenissen die zijn verwerkt en beschikbaar voor de query|Er zijn geen dimensies|
|IngressStoredEvents|Opgeslagen gebeurtenissen in ingangs|Count|Totaal|Aantal samengevoegde gebeurtenissen dat is verwerkt en beschikbaar is voor query|Er zijn geen dimensies|
|IngressReceivedMessagesTimeLag|Tijds vertraging ontvangen inkomende berichten|Seconden|Maximum|Verschil tussen het tijdstip waarop het bericht in de bron van de gebeurtenis in de wachtrij staat en de tijd die wordt verwerkt in ingangs|Er zijn geen dimensies|
|IngressReceivedMessagesCountLag|Vertraging ontvangen inkomende berichten van ingang|Count|Average|Verschil tussen het Volg nummer van de laatste bericht in de bron partitie en het Volg nummer van het bericht dat wordt verwerkt in ingangs gebeurtenissen|Er zijn geen dimensies|
|WarmStorageMaxProperties|Maximale eigenschappen van warme opslag|Count|Maximum|Maximum aantal eigenschappen dat is toegestaan door de omgeving voor de SKU van S1/S2 en het maximum aantal eigenschappen dat is toegestaan door de warme Store voor PAYG SKU|Er zijn geen dimensies|
|WarmStorageUsedProperties|Eigenschappen voor warme opslag gebruikt |Count|Maximum|Aantal eigenschappen dat wordt gebruikt door de omgeving voor de SKU van S1/S2 en het aantal eigenschappen dat door warme Store voor PAYG SKU wordt gebruikt|Er zijn geen dimensies|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ontvangen berichten met ingang|Count|Totaal|Aantal berichten dat uit de gebeurtenis bron is gelezen|Er zijn geen dimensies|
|IngressReceivedInvalidMessages|Ongeldige berichten ontvangen|Count|Totaal|Aantal ongeldige berichten gelezen uit de gebeurtenis bron|Er zijn geen dimensies|
|IngressReceivedBytes|Ontvangen bytes van binnenkomend verkeer|Bytes|Totaal|Aantal gelezen bytes van de gebeurtenis bron|Er zijn geen dimensies|
|IngressStoredBytes|In ingangs opgeslagen bytes|Bytes|Totaal|De totale grootte van de gebeurtenissen die zijn verwerkt en beschikbaar voor de query|Er zijn geen dimensies|
|IngressStoredEvents|Opgeslagen gebeurtenissen in ingangs|Count|Totaal|Aantal samengevoegde gebeurtenissen dat is verwerkt en beschikbaar is voor query|Er zijn geen dimensies|
|IngressReceivedMessagesTimeLag|Tijds vertraging ontvangen inkomende berichten|Seconden|Maximum|Verschil tussen het tijdstip waarop het bericht in de bron van de gebeurtenis in de wachtrij staat en de tijd die wordt verwerkt in ingangs|Er zijn geen dimensies|
|IngressReceivedMessagesCountLag|Vertraging ontvangen inkomende berichten van ingang|Count|Average|Verschil tussen het Volg nummer van de laatste bericht in de bron partitie en het Volg nummer van het bericht dat wordt verwerkt in ingangs gebeurtenissen|Er zijn geen dimensies|
|WarmStorageMaxProperties|Maximale eigenschappen van warme opslag|Count|Maximum|Maximum aantal eigenschappen dat is toegestaan door de omgeving voor de SKU van S1/S2 en het maximum aantal eigenschappen dat is toegestaan door de warme Store voor PAYG SKU|Er zijn geen dimensies|
|WarmStorageUsedProperties|Eigenschappen voor warme opslag gebruikt |Count|Maximum|Aantal eigenschappen dat wordt gebruikt door de omgeving voor de SKU van S1/S2 en het aantal eigenschappen dat door warme Store voor PAYG SKU wordt gebruikt|Er zijn geen dimensies|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Gelezen bytes per seconde|BytesPerSecond|Average|Gemiddelde door Voer van schijf vanwege Lees bewerkingen gedurende de voorbeeld periode.|Er zijn geen dimensies|
|DiskWriteBytesPerSecond|Geschreven bytes per seconde|BytesPerSecond|Average|Gemiddelde doorvoer snelheid van schijven vanwege schrijf bewerkingen gedurende de voorbeeld periode.|Er zijn geen dimensies|
|Gelezen bytes op de schijf|Gelezen bytes op de schijf|Bytes|Totaal|Totale schijf doorvoer vanwege Lees bewerkingen gedurende de voorbeeld periode.|Er zijn geen dimensies|
|Geschreven bytes op de schijf|Geschreven bytes op de schijf|Bytes|Totaal|Totale schijf doorvoer vanwege schrijf bewerkingen gedurende de voorbeeld periode.|Er zijn geen dimensies|
|DiskReadOperations|Lees bewerkingen op de schijf|Count|Totaal|Het aantal i/o-Lees bewerkingen in de vorige voorbeeld periode. Houd er rekening mee dat deze bewerkingen variabele grootte kunnen hebben.|Er zijn geen dimensies|
|DiskWriteOperations|Schrijf bewerkingen op de schijf|Count|Totaal|Het aantal i/o-schrijf bewerkingen in de vorige voorbeeld periode. Houd er rekening mee dat deze bewerkingen variabele grootte kunnen hebben.|Er zijn geen dimensies|
|Lees bewerkingen op de schijf per seconde|Lees bewerkingen op de schijf per seconde|CountPerSecond|Average|Het gemiddelde aantal i/o-Lees bewerkingen in de vorige voorbeeld periode. Houd er rekening mee dat deze bewerkingen variabele grootte kunnen hebben.|Er zijn geen dimensies|
|Schrijf bewerkingen op de schijf per seconde|Schrijf bewerkingen op de schijf per seconde|CountPerSecond|Average|Het gemiddelde aantal i/o-schrijf bewerkingen in de vorige voorbeeld periode. Houd er rekening mee dat deze bewerkingen variabele grootte kunnen hebben.|Er zijn geen dimensies|
|DiskReadLatency|Lees latentie van schijf|Milliseconden|Average|Totale lees latentie. De som van de lees latentie van het apparaat en de kernel.|Er zijn geen dimensies|
|DiskWriteLatency|Schrijf latentie schijf|Milliseconden|Average|Totale schrijf latentie. De som van de latentie voor het schrijven van apparaten en kernels.|Er zijn geen dimensies|
|NetworkInBytesPerSecond|Netwerk in bytes per seconde|BytesPerSecond|Average|Gemiddelde netwerk doorvoer voor ontvangen verkeer.|Er zijn geen dimensies|
|NetworkOutBytesPerSecond|Netwerk uitgaande bytes per seconde|BytesPerSecond|Average|Gemiddelde netwerk doorvoer voor verzonden verkeer.|Er zijn geen dimensies|
|Netwerk in|Netwerk in|Bytes|Totaal|Totale netwerk doorvoer voor ontvangen verkeer.|Er zijn geen dimensies|
|Netwerk uit|Netwerk uit|Bytes|Totaal|Totale netwerk doorvoer voor verzonden verkeer.|Er zijn geen dimensies|
|MemoryUsed|Gebruikt geheugen|Bytes|Average|De hoeveelheid machine geheugen die wordt gebruikt door de VM.|Er zijn geen dimensies|
|MemoryGranted|Toegewezen geheugen|Bytes|Average|De hoeveelheid geheugen die door de host is toegewezen aan de virtuele machine. Er wordt geen geheugen verleend aan de host totdat deze één keer wordt aangevallen en het toegewezen geheugen kan worden uitgewisseld of geballon weg als de VMkernel het geheugen nodig heeft.|Er zijn geen dimensies|
|MemoryActive|Actief geheugen|Bytes|Average|De hoeveelheid geheugen die wordt gebruikt door de virtuele machine in het verleden kleine tijd venster. Dit is het ' True ' nummer van de hoeveelheid geheugen die momenteel nodig is voor de VM. Extra, ongebruikt geheugen kan worden uitgewisseld of geballond zonder gevolgen voor de prestaties van de gast.|Er zijn geen dimensies|
|Percentage CPU|Percentage CPU|Procent|Average|Het CPU-gebruik. Deze waarde wordt gerapporteerd met 100% voor alle processor kernen op het systeem. Een voor beeld: een twee richtings-VM met 50% van een systeem met vier kern geheugens is volledig met twee kernen.|Er zijn geen dimensies|
|PercentageCpuReady|Percentage CPU gereed|Milliseconden|Totaal|Beschik bare tijd is de tijd die nodig is voor het wachten op CPU ('s) in het afgelopen update-interval.|Er zijn geen dimensies|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CpuPercentage|CPU-percentage|Procent|Average|CPU-percentage|Exemplaar|
|MemoryPercentage|Geheugen percentage|Procent|Average|Geheugen percentage|Exemplaar|
|DiskQueueLength|Wachtrij lengte voor schijf|Count|Average|Wachtrij lengte voor schijf|Exemplaar|
|HttpQueueLength|Lengte van http-wachtrij|Count|Average|Lengte van http-wachtrij|Exemplaar|
|BytesReceived|Gegevens in|Bytes|Totaal|Gegevens in|Exemplaar|
|BytesSent|Gegevens uit|Bytes|Totaal|Gegevens uit|Exemplaar|

## <a name="microsoftwebsites-excluding-functions"></a>Micro soft. web/sites (met uitzonde ring van functies)

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CpuTime|CPU-tijd|Seconden|Totaal|CPU-tijd|Exemplaar|
|Aanvragen|Aanvragen|Count|Totaal|Aanvragen|Exemplaar|
|BytesReceived|Gegevens in|Bytes|Totaal|Gegevens in|Exemplaar|
|BytesSent|Gegevens uit|Bytes|Totaal|Gegevens uit|Exemplaar|
|Http101|Http 101|Count|Totaal|Http 101|Exemplaar|
|Http2xx|Http-2xx|Count|Totaal|Http-2xx|Exemplaar|
|Http3xx|HTTP-3xx|Count|Totaal|HTTP-3xx|Exemplaar|
|Http401|HTTP 401|Count|Totaal|HTTP 401|Exemplaar|
|Http403|HTTP 403|Count|Totaal|HTTP 403|Exemplaar|
|Http404|Http 404|Count|Totaal|Http 404|Exemplaar|
|Http406|Http 406|Count|Totaal|Http 406|Exemplaar|
|Http4xx|Http 4xx|Count|Totaal|Http 4xx|Exemplaar|
|Http5xx|Http-server fouten|Count|Totaal|Http-server fouten|Exemplaar|
|MemoryWorkingSet|Werkset geheugen|Bytes|Average|Werkset geheugen|Exemplaar|
|AverageMemoryWorkingSet|Gemiddelde werkset geheugen|Bytes|Average|Gemiddelde werkset geheugen|Exemplaar|
|AverageResponseTime|Gemiddelde reactie tijd|Seconden|Average|Gemiddelde reactie tijd|Exemplaar|
|AppConnections|Verbindingen|Count|Average|Verbindingen|Exemplaar|
|Formuleer|Aantal ingangen|Count|Average|Aantal ingangen|Exemplaar|
|Lijnen|Aantal threads|Count|Average|Aantal threads|Exemplaar|
|PrivateBytes|Privé-bytes|Bytes|Average|Privé-bytes|Exemplaar|
|IoReadBytesPerSecond|I/o gelezen bytes per seconde|BytesPerSecond|Totaal|I/o gelezen bytes per seconde|Exemplaar|
|IoWriteBytesPerSecond|I/o-schrijf bewerkingen in bytes per seconde|BytesPerSecond|Totaal|I/o-schrijf bewerkingen in bytes per seconde|Exemplaar|
|IoOtherBytesPerSecond|Andere i/o-bytes per seconde|BytesPerSecond|Totaal|Andere i/o-bytes per seconde|Exemplaar|
|IoReadOperationsPerSecond|I/o-Lees bewerkingen per seconde|BytesPerSecond|Totaal|I/o-Lees bewerkingen per seconde|Exemplaar|
|IoWriteOperationsPerSecond|I/o-schrijf bewerkingen per seconde|BytesPerSecond|Totaal|I/o-schrijf bewerkingen per seconde|Exemplaar|
|IoOtherOperationsPerSecond|Andere i/o-bewerkingen per seconde|BytesPerSecond|Totaal|Andere i/o-bewerkingen per seconde|Exemplaar|
|RequestsInApplicationQueue|Aanvragen in de wachtrij van de toepassing|Count|Average|Aanvragen in de wachtrij van de toepassing|Exemplaar|
|CurrentAssemblies|Huidige Assembly's|Count|Average|Huidige Assembly's|Exemplaar|
|TotalAppDomains|Totaal aantal app-domeinen|Count|Average|Totaal aantal app-domeinen|Exemplaar|
|TotalAppDomainsUnloaded|Totaal aantal verwijderde app-domeinen|Count|Average|Totaal aantal verwijderde app-domeinen|Exemplaar|
|Gen0Collections|Schone verzamelingen van 0 gen|Count|Totaal|Schone verzamelingen van 0 gen|Exemplaar|
|Gen1Collections|1 garbagecollection-verzamelingen|Count|Totaal|1 garbagecollection-verzamelingen|Exemplaar|
|Gen2Collections|Opschoon verzamelingen van generatie 2|Count|Totaal|Opschoon verzamelingen van generatie 2|Exemplaar|

## <a name="microsoftwebsites-functions"></a>Micro soft. web/sites (functies)

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BytesReceived|Gegevens in|Bytes|Totaal|Gegevens in|Exemplaar|
|BytesSent|Gegevens uit|Bytes|Totaal|Gegevens uit|Exemplaar|
|Http5xx|Http-server fouten|Count|Totaal|Http-server fouten|Exemplaar|
|MemoryWorkingSet|Werkset geheugen|Bytes|Average|Werkset geheugen|Exemplaar|
|AverageMemoryWorkingSet|Gemiddelde werkset geheugen|Bytes|Average|Gemiddelde werkset geheugen|Exemplaar|
|FunctionExecutionUnits|Eenheden voor functie-uitvoering|MB/milliseconden|Totaal|[Eenheden voor functie-uitvoering](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Exemplaar|
|FunctionExecutionCount|Aantal functie-uitvoeringen|Count|Totaal|Aantal functie-uitvoeringen|Exemplaar|
|PrivateBytes|Privé-bytes|Bytes|Average|Privé-bytes|Exemplaar|
|IoReadBytesPerSecond|I/o gelezen bytes per seconde|BytesPerSecond|Totaal|I/o gelezen bytes per seconde|Exemplaar|
|IoWriteBytesPerSecond|I/o-schrijf bewerkingen in bytes per seconde|BytesPerSecond|Totaal|I/o-schrijf bewerkingen in bytes per seconde|Exemplaar|
|IoOtherBytesPerSecond|Andere i/o-bytes per seconde|BytesPerSecond|Totaal|Andere i/o-bytes per seconde|Exemplaar|
|IoReadOperationsPerSecond|I/o-Lees bewerkingen per seconde|BytesPerSecond|Totaal|I/o-Lees bewerkingen per seconde|Exemplaar|
|IoWriteOperationsPerSecond|I/o-schrijf bewerkingen per seconde|BytesPerSecond|Totaal|I/o-schrijf bewerkingen per seconde|Exemplaar|
|IoOtherOperationsPerSecond|Andere i/o-bewerkingen per seconde|BytesPerSecond|Totaal|Andere i/o-bewerkingen per seconde|Exemplaar|
|RequestsInApplicationQueue|Aanvragen in de wachtrij van de toepassing|Count|Average|Aanvragen in de wachtrij van de toepassing|Exemplaar|
|CurrentAssemblies|Huidige Assembly's|Count|Average|Huidige Assembly's|Exemplaar|
|TotalAppDomains|Totaal aantal app-domeinen|Count|Average|Totaal aantal app-domeinen|Exemplaar|
|TotalAppDomainsUnloaded|Totaal aantal verwijderde app-domeinen|Count|Average|Totaal aantal verwijderde app-domeinen|Exemplaar|
|Gen0Collections|Schone verzamelingen van 0 gen|Count|Totaal|Schone verzamelingen van 0 gen|Exemplaar|
|Gen1Collections|1 garbagecollection-verzamelingen|Count|Totaal|1 garbagecollection-verzamelingen|Exemplaar|
|Gen2Collections|Opschoon verzamelingen van generatie 2|Count|Totaal|Opschoon verzamelingen van generatie 2|Exemplaar|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CpuTime|CPU-tijd|Seconden|Totaal|CPU-tijd|Exemplaar|
|Aanvragen|Aanvragen|Count|Totaal|Aanvragen|Exemplaar|
|BytesReceived|Gegevens in|Bytes|Totaal|Gegevens in|Exemplaar|
|BytesSent|Gegevens uit|Bytes|Totaal|Gegevens uit|Exemplaar|
|Http101|Http 101|Count|Totaal|Http 101|Exemplaar|
|Http2xx|Http-2xx|Count|Totaal|Http-2xx|Exemplaar|
|Http3xx|HTTP-3xx|Count|Totaal|HTTP-3xx|Exemplaar|
|Http401|HTTP 401|Count|Totaal|HTTP 401|Exemplaar|
|Http403|HTTP 403|Count|Totaal|HTTP 403|Exemplaar|
|Http404|Http 404|Count|Totaal|Http 404|Exemplaar|
|Http406|Http 406|Count|Totaal|Http 406|Exemplaar|
|Http4xx|Http 4xx|Count|Totaal|Http 4xx|Exemplaar|
|Http5xx|Http-server fouten|Count|Totaal|Http-server fouten|Exemplaar|
|MemoryWorkingSet|Werkset geheugen|Bytes|Average|Werkset geheugen|Exemplaar|
|AverageMemoryWorkingSet|Gemiddelde werkset geheugen|Bytes|Average|Gemiddelde werkset geheugen|Exemplaar|
|AverageResponseTime|Gemiddelde reactie tijd|Seconden|Average|Gemiddelde reactie tijd|Exemplaar|
|FunctionExecutionUnits|Eenheden voor functie-uitvoering|Count|Totaal|Eenheden voor functie-uitvoering|Exemplaar|
|FunctionExecutionCount|Aantal functie-uitvoeringen|Count|Totaal|Aantal functie-uitvoeringen|Exemplaar|
|AppConnections|Verbindingen|Count|Average|Verbindingen|Exemplaar|
|Formuleer|Aantal ingangen|Count|Average|Aantal ingangen|Exemplaar|
|Lijnen|Aantal threads|Count|Average|Aantal threads|Exemplaar|
|PrivateBytes|Privé-bytes|Bytes|Average|Privé-bytes|Exemplaar|
|IoReadBytesPerSecond|I/o gelezen bytes per seconde|BytesPerSecond|Totaal|I/o gelezen bytes per seconde|Exemplaar|
|IoWriteBytesPerSecond|I/o-schrijf bewerkingen in bytes per seconde|BytesPerSecond|Totaal|I/o-schrijf bewerkingen in bytes per seconde|Exemplaar|
|IoOtherBytesPerSecond|Andere i/o-bytes per seconde|BytesPerSecond|Totaal|Andere i/o-bytes per seconde|Exemplaar|
|IoReadOperationsPerSecond|I/o-Lees bewerkingen per seconde|BytesPerSecond|Totaal|I/o-Lees bewerkingen per seconde|Exemplaar|
|IoWriteOperationsPerSecond|I/o-schrijf bewerkingen per seconde|BytesPerSecond|Totaal|I/o-schrijf bewerkingen per seconde|Exemplaar|
|IoOtherOperationsPerSecond|Andere i/o-bewerkingen per seconde|BytesPerSecond|Totaal|Andere i/o-bewerkingen per seconde|Exemplaar|
|RequestsInApplicationQueue|Aanvragen in de wachtrij van de toepassing|Count|Average|Aanvragen in de wachtrij van de toepassing|Exemplaar|
|CurrentAssemblies|Huidige Assembly's|Count|Average|Huidige Assembly's|Exemplaar|
|TotalAppDomains|Totaal aantal app-domeinen|Count|Average|Totaal aantal app-domeinen|Exemplaar|
|TotalAppDomainsUnloaded|Totaal aantal verwijderde app-domeinen|Count|Average|Totaal aantal verwijderde app-domeinen|Exemplaar|
|Gen0Collections|Schone verzamelingen van 0 gen|Count|Totaal|Schone verzamelingen van 0 gen|Exemplaar|
|Gen1Collections|1 garbagecollection-verzamelingen|Count|Totaal|1 garbagecollection-verzamelingen|Exemplaar|
|Gen2Collections|Opschoon verzamelingen van generatie 2|Count|Totaal|Opschoon verzamelingen van generatie 2|Exemplaar|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Aanvragen|Aanvragen|Count|Totaal|Aanvragen|Exemplaar|
|BytesReceived|Gegevens in|Bytes|Totaal|Gegevens in|Exemplaar|
|BytesSent|Gegevens uit|Bytes|Totaal|Gegevens uit|Exemplaar|
|Http101|Http 101|Count|Totaal|Http 101|Exemplaar|
|Http2xx|Http-2xx|Count|Totaal|Http-2xx|Exemplaar|
|Http3xx|HTTP-3xx|Count|Totaal|HTTP-3xx|Exemplaar|
|Http401|HTTP 401|Count|Totaal|HTTP 401|Exemplaar|
|Http403|HTTP 403|Count|Totaal|HTTP 403|Exemplaar|
|Http404|Http 404|Count|Totaal|Http 404|Exemplaar|
|Http406|Http 406|Count|Totaal|Http 406|Exemplaar|
|Http4xx|Http 4xx|Count|Totaal|Http 4xx|Exemplaar|
|Http5xx|Http-server fouten|Count|Totaal|Http-server fouten|Exemplaar|
|AverageResponseTime|Gemiddelde reactie tijd|Seconden|Average|Gemiddelde reactie tijd|Exemplaar|
|CpuPercentage|CPU-percentage|Procent|Average|CPU-percentage|Exemplaar|
|MemoryPercentage|Geheugen percentage|Procent|Average|Geheugen percentage|Exemplaar|
|DiskQueueLength|Wachtrij lengte voor schijf|Count|Average|Wachtrij lengte voor schijf|Exemplaar|
|HttpQueueLength|Lengte van http-wachtrij|Count|Average|Lengte van http-wachtrij|Exemplaar|
|ActiveRequests|Actieve aanvragen|Count|Totaal|Actieve aanvragen|Exemplaar|
|TotalFrontEnds|Totale front-ends|Count|Average|Totale front-ends|Er zijn geen dimensies|
|SmallAppServicePlanInstances|Werk rollen voor kleine App Service plannen|Count|Average|Werk rollen voor kleine App Service plannen|Er zijn geen dimensies|
|MediumAppServicePlanInstances|Werk nemers met gemiddeld App Service plannen|Count|Average|Werk nemers met gemiddeld App Service plannen|Er zijn geen dimensies|
|LargeAppServicePlanInstances|Werk rollen voor grote App Service plannen|Count|Average|Werk rollen voor grote App Service plannen|Er zijn geen dimensies|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|WorkersTotal|Totaal aantal werk rollen|Count|Average|Totaal aantal werk rollen|Er zijn geen dimensies|
|WorkersAvailable|Beschik bare werk nemers|Count|Average|Beschik bare werk nemers|Er zijn geen dimensies|
|WorkersUsed|Gebruikte werk rollen|Count|Average|Gebruikte werk rollen|Er zijn geen dimensies|
|CpuPercentage|CPU-percentage|Procent|Average|CPU-percentage|Exemplaar|
|MemoryPercentage|Geheugen percentage|Procent|Average|Geheugen percentage|Exemplaar|

## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over metrische gegevens in Azure Monitor](data-platform.md)
* [Waarschuwingen maken voor metrische gegevens](alerts-overview.md)
* [Metrische gegevens exporteren naar opslag, Event hub of Log Analytics](resource-logs-overview.md)
