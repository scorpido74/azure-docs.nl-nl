---
title: Azure Monitor ondersteunde metrische gegevens per resource type
description: Een lijst met metrische gegevens die beschikbaar zijn voor elk resource type met Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.topic: reference
ms.date: 12/18/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 3e43d2baf4337e7a986d59c47f805183a920c7a1
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275384"
---
# <a name="supported-metrics-with-azure-monitor"></a>Ondersteunde metrische gegevens met Azure Monitor

Azure Monitor biedt verschillende manieren om te communiceren met metrische gegevens, zoals het maken van grafieken in de portal, het openen ervan via de REST API of het opvragen van query's via Power shell of CLI. Hieronder vindt u een volledige lijst met alle metrische gegevens die momenteel beschikbaar zijn met de metrische pijp lijn van de Azure Monitor. Andere metrische gegevens zijn mogelijk beschikbaar in de portal of met behulp van verouderde Api's. Deze lijst hieronder bevat alleen metrische gegevens die beschikbaar zijn via de geconsolideerde Azure Monitor metrische pijp lijn. De metrische gegevens zijn geordend op naam ruimte. Zie [resource providers voor Azure-Services](../../azure-resource-manager/management/azure-services-resource-providers.md)voor een lijst met Services en de naam ruimten die bij hen horen. Als u via een programma wilt zoeken naar en toegang wilt krijgen tot deze metrische gegevens, gebruikt u de [2018-01-01-API-versie](https://docs.microsoft.com/rest/api/monitor/metricdefinitions)

> [!NOTE]
> Het verzenden van multidimensionale metrische gegevens via diagnostische instellingen wordt momenteel niet ondersteund. Metrische gegevens met dimensies worden geëxporteerd als platte eendimensionale metrische gegevens, als totaal van alle dimensiewaarden.
>
> *Een voorbeeld*: de metriek 'Binnenkomende berichten' voor een Event Hub kan worden verkend en uitgezet op wachtrijniveau. Wanneer de waarde wordt geëxporteerd via diagnostische instellingen, wordt deze echter voorgesteld als alle binnenkomende berichten voor alle wachtrijen in de Event Hub.
>
> Raadpleeg [dit artikel](metrics-supported-export-diagnostic-settings.md)voor een lijst met platform metrische gegevens die kunnen worden geëxporteerd via Diagnostische instellingen.



## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|qpu_metric|QPU|Count|Average|QPU. Bereik 0-100 voor S1, 0-200 voor S2 en 0-400 voor S4|ServerResourceType|
|memory_metric|Geheugen|Bytes|Average|Geheugenmetabase. Bereik 0-25 GB voor S1, 0-50 GB voor S2 en 0-100 GB voor S4|ServerResourceType|
|private_bytes_metric|Eigen Bytes|Bytes|Average|Privé-bytes.|ServerResourceType|
|virtual_bytes_metric|Virtuele bytes|Bytes|Average|Virtuele bytes.|ServerResourceType|
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
|Overschreden|Geheugen: quotum|Bytes|Average|Huidig geheugen quotum, in bytes. Geheugen quota wordt ook wel een geheugen toekenning of geheugen reservering genoemd.|ServerResourceType|
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
|QueryPoolJobQueueLength|Threads: lengte van van de taak wachtrij van de query pool|Count|Average|Aantal taken in de wachtrij van de query thread pool.|ServerResourceType|
|ShortParsingBusyThreads|Threads: bezette threads voor kort parseren|Count|Average|Het aantal actieve threads in de thread pool voor kort parseren.|ServerResourceType|
|ShortParsingIdleThreads|Threads: niet-actieve threads voor kort parseren|Count|Average|Het aantal niet-actieve threads in de thread pool voor kort parseren.|ServerResourceType|
|ShortParsingJobQueueLength|Threads: lengte van taak wachtrij voor kort parseren|Count|Average|Aantal taken in de wachtrij van de thread pool voor kort parseren.|ServerResourceType|
|memory_thrashing_metric|Geheugen overbelasting|Procent|Average|Gemiddeld geheugen overbelasting.|ServerResourceType|
|mashup_engine_qpu_metric|M-engine QPU|Count|Average|QPU-gebruik door mashup-engine processen|ServerResourceType|
|mashup_engine_memory_metric|M-engine geheugen|Bytes|Average|Geheugen gebruik door mashup-engine processen|ServerResourceType|
|mashup_engine_private_bytes_metric|M-engine-eigen bytes|Bytes|Average|Privé-bytes gebruik door mashup-engine processen.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|M-engine virtuele bytes|Bytes|Average|Gebruik van virtuele bytes door mashup-engine processen.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TotalRequests|Totaal aantal gateway aanvragen (afgeschaft)|Count|Totaal|Aantal gateway aanvragen-metrische aanvraag voor meerdere dimensies met GatewayResponseCodeCategory dimensie gebruiken|Locatie, hostnaam|
|SuccessfulRequests|Geslaagde gateway aanvragen (afgeschaft)|Count|Totaal|Aantal geslaagde gateway aanvragen-metrische aanvraag voor meerdere dimensies met GatewayResponseCodeCategory dimensie gebruiken|Locatie, hostnaam|
|UnauthorizedRequests|Niet-geautoriseerde gateway aanvragen (afgeschaft)|Count|Totaal|Aantal niet-geautoriseerde gateway aanvragen-metrische aanvraag voor meerdere dimensies met GatewayResponseCodeCategory dimensie gebruiken|Locatie, hostnaam|
|FailedRequests|Mislukte gateway aanvragen (afgeschaft)|Count|Totaal|Aantal fouten in gateway aanvragen-metrische aanvraag voor meerdere dimensies met GatewayResponseCodeCategory dimensie gebruiken|Locatie, hostnaam|
|OtherRequests|Andere gateway aanvragen (afgeschaft)|Count|Totaal|Aantal andere gateway aanvragen-metrische aanvraag voor meerdere dimensies met GatewayResponseCodeCategory dimensie gebruiken|Locatie, hostnaam|
|Duur|Totale duur van gateway aanvragen|Milliseconden|Average|Totale duur van gateway aanvragen in milliseconden|Locatie, hostnaam|
|BackendDuration|Duur van back-end-aanvragen|Milliseconden|Average|Duur van back-end-aanvragen in milliseconden|Locatie, hostnaam|
|Capaciteit|Capaciteit|Procent|Average|De metrische gegevens over het gebruik van de ApiManagement-service|Locatie|
|EventHubTotalEvents|Totaal aantal EventHub-gebeurtenissen|Count|Totaal|Aantal gebeurtenissen dat naar EventHub is verzonden|Locatie|
|EventHubSuccessfulEvents|Geslaagde EventHub-gebeurtenissen|Count|Totaal|Aantal geslaagde EventHub-gebeurtenissen|Locatie|
|EventHubTotalFailedEvents|Mislukte EventHub-gebeurtenissen|Count|Totaal|Aantal mislukte EventHub-gebeurtenissen|Locatie|
|EventHubRejectedEvents|Geweigerde EventHub-gebeurtenissen|Count|Totaal|Aantal geweigerde EventHub-gebeurtenissen (onjuiste configuratie of niet-geautoriseerd)|Locatie|
|EventHubThrottledEvents|Vertraagde EventHub-gebeurtenissen|Count|Totaal|Aantal vertraagde EventHub-gebeurtenissen|Locatie|
|EventHubTimedoutEvents|Time-out EventHub-gebeurtenissen|Count|Totaal|Aantal time-out EventHub-gebeurtenissen|Locatie|
|EventHubDroppedEvents|Verwijderde EventHub-gebeurtenissen|Count|Totaal|Het aantal gebeurtenissen dat is overgeslagen omdat de maximale grootte van de wachtrij is bereikt|Locatie|
|EventHubTotalBytesSent|Grootte van EventHub-gebeurtenissen|Bytes|Totaal|Totale grootte van EventHub-gebeurtenissen in bytes|Locatie|
|Aanvragen|Aanvragen|Count|Totaal|Metrische gegevens van gateway aanvragen met meerdere dimensies|Location, hostname, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Count|Count|Totaal aantal binnenkomende HTTP-aanvragen.|None|
|FailedHttpRequestCount|FailedHttpRequestCount|Count|Count|Mislukte HTTP-aanvragen.|None|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|Count|Average|Latentie voor een HTTP-aanvraag.|None|


## <a name="microsoftappplatformspring"></a>Micro soft. AppPlatform/lente

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SystemCpuUsagePercentage|Percentage van het CPU-gebruik van het systeem|Procent|Average|Het recente CPU-gebruik voor het hele systeem|AppName, pod|
|AppCpuUsagePercentage|CPU-gebruiks percentage van app|Procent|Average|JVM CPU-gebruiks percentage van app|AppName, pod|
|AppMemoryCommitted|Toegewezen app-geheugen|Bytes|Average|Geheugen toegewezen aan JVM in bytes|AppName, pod|
|AppMemoryUsed|Gebruikt app-geheugen|Bytes|Average|Gebruikt app-geheugen in bytes|AppName, pod|
|AppMemoryMax|Maxi maal app-geheugen|Bytes|Maximum|De maximale hoeveelheid geheugen in bytes die kan worden gebruikt voor geheugen beheer|AppName, pod|
|MaxOldGenMemoryPoolBytes|Maxi maal beschik bare oude generatie gegevens grootte|Bytes|Average|Maximale grootte van de geheugen groep voor de oude generatie|AppName, pod|
|OldGenMemoryPoolBytes|Gegevens grootte van oude generatie|Bytes|Average|Grootte van de geheugen groep van de oude generatie na een volledige GC|AppName, pod|
|OldGenPromotedBytes|Promo veren tot oude generatie gegevens grootte|Bytes|Maximum|Aantal positieve toename van de grootte van de oude generatie geheugengroep vóór GC tot na GC|AppName, pod|
|YoungGenPromotedBytes|Promo veren tot jonge generatie gegevens grootte|Bytes|Maximum|Verhoogd voor een toename van de grootte van de Memory pool voor de jonge generatie na een GC tot de volgende|AppName, pod|
|GCPauseTotalCount|Aantal GC-onderbrekingen|Count|Totaal|Aantal GC-onderbrekingen|AppName, pod|
|GCPauseTotalTime|Totale tijd van de GC-onderbreking|Milliseconden|Totaal|Totale tijd van de GC-onderbreking|AppName, pod|
|TomcatSentBytes|Totaal aantal verzonden bytes in Tomcat|Bytes|Totaal|Totaal aantal verzonden bytes in Tomcat|AppName, pod|
|TomcatReceivedBytes|Totaal aantal bytes ontvangen Tomcat|Bytes|Totaal|Totaal aantal bytes ontvangen Tomcat|AppName, pod|
|TomcatRequestTotalTime|Totaal aantal keer Tomcat-aanvragen|Milliseconden|Totaal|Totaal aantal keer Tomcat-aanvragen|AppName, pod|
|TomcatRequestTotalCount|Totaal aantal Tomcat-aanvragen|Count|Totaal|Totaal aantal Tomcat-aanvragen|AppName, pod|
|TomcatResponseAvgTime|Gemiddelde tijd Tomcat-aanvraag|Milliseconden|Average|Gemiddelde tijd Tomcat-aanvraag|AppName, pod|
|TomcatRequestMaxTime|Maximale tijd voor tomcat-aanvraag|Milliseconden|Maximum|Maximale tijd voor tomcat-aanvraag|AppName, pod|
|TomcatErrorCount|Tomcat Global-fout|Count|Totaal|Tomcat Global-fout|AppName, pod|
|TomcatSessionActiveMaxCount|Aantal actieve Tomcat-sessies|Count|Totaal|Aantal actieve Tomcat-sessies|AppName, pod|
|TomcatSessionAliveMaxTime|Time-outperiode van Tomcat-sessie|Milliseconden|Maximum|Time-outperiode van Tomcat-sessie|AppName, pod|
|TomcatSessionActiveCurrentCount|Aantal Tomcat-sessies|Count|Totaal|Aantal Tomcat-sessies|AppName, pod|
|TomcatSessionCreatedCount|Aantal gemaakte Tomcat-sessies|Count|Totaal|Aantal gemaakte Tomcat-sessies|AppName, pod|
|TomcatSessionExpiredCount|Aantal verlopen Tomcat-sessies|Count|Totaal|Aantal verlopen Tomcat-sessies|AppName, pod|
|TomcatSessionRejectedCount|Aantal geweigerde Tomcat-sessies|Count|Totaal|Aantal geweigerde Tomcat-sessies|AppName, pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TotalJob|Totaal aantal taken|Count|Totaal|Het totale aantal taken|Runbook, status|
|TotalUpdateDeploymentRuns|Totaal aantal uitgevoerde update-implementaties|Count|Totaal|Totale aantal uitgevoerde software-update-implementaties|SoftwareUpdateConfigurationName, status|
|TotalUpdateDeploymentMachineRuns|Totaal aantal uitgevoerde update-implementatie computers|Count|Totaal|Het totale aantal uitgevoerde software-update-implementatie computers in een software-update-implementatie|SoftwareUpdateConfigurationName, status, target computer, SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CoreCount|Aantal toegewezen kernen|Count|Totaal|Totaal aantal toegewezen kernen in het batch-account|None|
|TotalNodeCount|Aantal toegewezen knoop punten|Count|Totaal|Totaal aantal toegewezen knoop punten in het batch-account|None|
|LowPriorityCoreCount|Aantal LowPriority kernen|Count|Totaal|Totaal aantal kernen met lage prioriteit in het batch-account|None|
|TotalLowPriorityNodeCount|Aantal knoop punten met een lage prioriteit|Count|Totaal|Totaal aantal knoop punten met een lage prioriteit in het batch-account|None|
|CreatingNodeCount|Aantal knoop punten maken|Count|Totaal|Aantal knoop punten dat wordt gemaakt|None|
|StartingNodeCount|Begin aantal knoop punten|Count|Totaal|Aantal knoop punten dat begint|None|
|WaitingForStartTaskNodeCount|Wachten op aantal begin taak knooppunten|Count|Totaal|Aantal knoop punten dat wacht tot de begin taak is voltooid|None|
|StartTaskFailedNodeCount|Aantal mislukte knoop punten van begin taak|Count|Totaal|Aantal knoop punten waarop de begin taak is mislukt|None|
|IdleNodeCount|Aantal niet-actieve knoop punten|Count|Totaal|Aantal niet-actieve knoop punten|None|
|OfflineNodeCount|Aantal offline knooppunten|Count|Totaal|Aantal offline knooppunten|None|
|RebootingNodeCount|Aantal knoop punten opnieuw opstarten|Count|Totaal|Aantal knoop punten dat opnieuw wordt opgestart|None|
|ReimagingNodeCount|Telling van het aantal knoop punten|Count|Totaal|Aantal knoop punten van installatie kopieën|None|
|RunningNodeCount|Aantal actieve knoop punten|Count|Totaal|Aantal actieve knoop punten|None|
|LeavingPoolNodeCount|Aantal groeps knooppunten verlaten|Count|Totaal|Aantal knoop punten dat de pool verlaat|None|
|UnusableNodeCount|Aantal niet-bruikbare knoop punten|Count|Totaal|Aantal niet-bruikbare knoop punten|None|
|PreemptedNodeCount|Aantal knoop punten in herhaling|Count|Totaal|Aantal knoop punten dat is afgebroken|None|
|TaskStartEvent|Taak begin gebeurtenissen|Count|Totaal|Totaal aantal taken dat is gestart|None|
|TaskCompleteEvent|Taak voltooid gebeurtenissen|Count|Totaal|Totaal aantal taken dat is voltooid|None|
|TaskFailEvent|Taak fout gebeurtenissen|Count|Totaal|Totaal aantal taken dat is voltooid met de status mislukt|None|
|PoolCreateEvent|Groeps gebeurtenissen maken|Count|Totaal|Totaal aantal Pools dat is gemaakt|None|
|PoolResizeStartEvent|Begin gebeurtenissen van groeps grootte wijzigen|Count|Totaal|Totaal aantal hergroottes van de groep die zijn gestart|None|
|PoolResizeCompleteEvent|Volledige gebeurtenissen voor het wijzigen van de pool|Count|Totaal|Totaal aantal hergroottes van de groep die zijn voltooid|None|
|PoolDeleteStartEvent|Begin gebeurtenissen groep verwijderen|Count|Totaal|Totaal aantal verwijderde groepen dat is gestart|None|
|PoolDeleteCompleteEvent|Voltooide gebeurtenissen van groep verwijderen|Count|Totaal|Totaal aantal verwijderde groepen dat is voltooid|None|
|JobDeleteCompleteEvent|Voltooide gebeurtenissen van taak verwijderen|Count|Totaal|Het totale aantal taken dat is verwijderd.|None|
|JobDeleteStartEvent|Taak begin gebeurtenissen verwijderen|Count|Totaal|Het totale aantal taken dat is aangevraagd om te worden verwijderd.|None|
|JobDisableCompleteEvent|Voltooide gebeurtenissen voor taak uitschakelen|Count|Totaal|Het totale aantal taken dat is uitgeschakeld.|None|
|JobDisableStartEvent|Taak start gebeurtenissen uitschakelen|Count|Totaal|Het totale aantal taken dat is aangevraagd om te worden uitgeschakeld.|None|
|JobStartEvent|Taak begin gebeurtenissen|Count|Totaal|Het totale aantal taken dat is gestart.|None|
|JobTerminateCompleteEvent|Voltooide gebeurtenissen voor taak beëindigen|Count|Totaal|Het totale aantal taken dat is beëindigd.|None|
|JobTerminateStartEvent|Taak start gebeurtenissen beëindigen|Count|Totaal|Het totale aantal taken dat is aangevraagd om te worden beëindigd.|None|


## <a name="microsoftbatchaiworkspaces"></a>Micro soft. BatchAI/werk ruimten

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Taak verzonden|Taak verzonden|Count|Totaal|Aantal verzonden taken|Scenario, clustername|
|Taak is voltooid|Taak is voltooid|Count|Totaal|Aantal voltooide taken|Scenario, clustername, ResultType|
|Totaal aantal knoop punten|Totaal aantal knoop punten|Count|Average|Totaal aantal knoop punten|Scenario, clustername|
|Actieve knoop punten|Actieve knoop punten|Count|Average|Aantal actieve knoop punten|Scenario, clustername|
|Niet-actieve knoop punten|Niet-actieve knoop punten|Count|Average|Aantal niet-actieve knoop punten|Scenario, clustername|
|Niet-bruikbare knoop punten|Niet-bruikbare knoop punten|Count|Average|Aantal niet-bruikbare knoop punten|Scenario, clustername|
|Knoop punten die zijn afgebroken|Knoop punten die zijn afgebroken|Count|Average|Aantal knoop punten dat is afgebroken|Scenario, clustername|
|Knoop punten verlaten|Knoop punten verlaten|Count|Average|Aantal verlaatde knoop punten|Scenario, clustername|
|Totaal aantal kernen|Totaal aantal kernen|Count|Average|Aantal totale kernen|Scenario, clustername|
|Actieve kernen|Actieve kernen|Count|Average|Aantal actieve kernen|Scenario, clustername|
|Niet-actieve kernen|Niet-actieve kernen|Count|Average|Aantal niet-actieve kern geheugens|Scenario, clustername|
|Onbruikbaar aantal kern geheugens|Onbruikbaar aantal kern geheugens|Count|Average|Aantal niet-bruikbare kernen|Scenario, clustername|
|Afgebroken kernen|Afgebroken kernen|Count|Average|Aantal afgebroken kernen|Scenario, clustername|
|Kernen verlaten|Kernen verlaten|Count|Average|Aantal te verlaten kernen|Scenario, clustername|
|Percentage quotum gebruik|Percentage quotum gebruik|Count|Average|Percentage van gebruikte quota|Scenario, clustername, VmFamilyName, VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|Percentage CPU-gebruik|Procent|Maximum|Percentage CPU-gebruik|Knooppunt|
|MemoryUsage|Geheugen gebruik|Bytes|Average|Geheugen gebruik|Knooppunt|
|MemoryLimit|Geheugen limiet|Bytes|Average|Geheugen limiet|Knooppunt|
|MemoryUsagePercentageInDouble|Percentage geheugen gebruik|Procent|Average|Percentage geheugen gebruik|Knooppunt|
|StorageUsage|Opslag gebruik|Bytes|Average|Opslag gebruik|Knooppunt|
|IOReadBytes|I/o gelezen bytes|Bytes|Totaal|I/o gelezen bytes|Knooppunt|
|IOWriteBytes|I/o-schrijf bytes|Bytes|Totaal|I/o-schrijf bytes|Knooppunt|
|ConnectionAccepted|Geaccepteerde verbindingen|Count|Totaal|Geaccepteerde verbindingen|Knooppunt|
|ConnectionHandled|Afgehandelde verbindingen|Count|Totaal|Afgehandelde verbindingen|Knooppunt|
|ConnectionActive|Actieve verbindingen|Count|Average|Actieve verbindingen|Knooppunt|
|RequestHandled|Verwerkte aanvragen|Count|Totaal|Verwerkte aanvragen|Knooppunt|
|ProcessedBlocks|Verwerkte blokken|Count|Totaal|Verwerkte blokken|Knooppunt|
|ProcessedTransactions|Verwerkte trans acties|Count|Totaal|Verwerkte trans acties|Knooppunt|
|PendingTransactions|Trans acties in behandeling|Count|Average|Trans acties in behandeling|Knooppunt|
|QueuedTransactions|Trans acties in de wachtrij|Count|Average|Trans acties in de wachtrij|Knooppunt|



## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|connectedclients|Verbonden clients|Count|Maximum||ShardId|
|totalcommandsprocessed|Totaal aantal bewerkingen|Count|Totaal||ShardId|
|cachehits|Cache treffers|Count|Totaal||ShardId|
|cachemisses|Cache missers|Count|Totaal||ShardId|
|cachemissrate|Aantal missers in cache|Procent|cachemissrate||ShardId|
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
|cacheLatency|Cache latentie micro seconden (preview-versie)|Count|Average||ShardId|
|fouten|Fouten|Count|Maximum||ShardId, error type|
|connectedclients0|Verbonden clients (Shard 0)|Count|Maximum||None|
|totalcommandsprocessed0|Totaal aantal bewerkingen (Shard 0)|Count|Totaal||None|
|cachehits0|Cache treffers (Shard 0)|Count|Totaal||None|
|cachemisses0|Cache missers (Shard 0)|Count|Totaal||None|
|getcommands0|Hiermee wordt opgehaald (Shard 0)|Count|Totaal||None|
|setcommands0|Sets (Shard 0)|Count|Totaal||None|
|operationsPerSecond0|Bewerkingen per seconde (Shard 0)|Count|Maximum||None|
|evictedkeys0|Verwijderde sleutels (Shard 0)|Count|Totaal||None|
|totalkeys0|Totaal aantal sleutels (Shard 0)|Count|Maximum||None|
|expiredkeys0|Verlopen sleutels (Shard 0)|Count|Totaal||None|
|usedmemory0|Gebruikt geheugen (Shard 0)|Bytes|Maximum||None|
|usedmemoryRss0|Gebruikt geheugen RSS (Shard 0)|Bytes|Maximum||None|
|serverLoad0|Server belasting (Shard 0)|Procent|Maximum||None|
|cacheWrite0|Cache schrijven (Shard 0)|BytesPerSecond|Maximum||None|
|cacheRead0|Cache gelezen (Shard 0)|BytesPerSecond|Maximum||None|
|percentProcessorTime0|CPU (Shard 0)|Procent|Maximum||None|
|connectedclients1|Verbonden clients (Shard 1)|Count|Maximum||None|
|totalcommandsprocessed1|Totaal aantal bewerkingen (Shard 1)|Count|Totaal||None|
|cachehits1|Cache treffers (Shard 1)|Count|Totaal||None|
|cachemisses1|Cache missers (Shard 1)|Count|Totaal||None|
|getcommands1|Hiermee wordt opgehaald (Shard 1)|Count|Totaal||None|
|setcommands1|Sets (Shard 1)|Count|Totaal||None|
|operationsPerSecond1|Bewerkingen per seconde (Shard 1)|Count|Maximum||None|
|evictedkeys1|Verwijderde sleutels (Shard 1)|Count|Totaal||None|
|totalkeys1|Totaal aantal sleutels (Shard 1)|Count|Maximum||None|
|expiredkeys1|Verlopen sleutels (Shard 1)|Count|Totaal||None|
|usedmemory1|Gebruikt geheugen (Shard 1)|Bytes|Maximum||None|
|usedmemoryRss1|Gebruikte geheugen RSS (Shard 1)|Bytes|Maximum||None|
|serverLoad1|Server belasting (Shard 1)|Procent|Maximum||None|
|cacheWrite1|Cache schrijven (Shard 1)|BytesPerSecond|Maximum||None|
|cacheRead1|Lees bewerking in cache (Shard 1)|BytesPerSecond|Maximum||None|
|percentProcessorTime1|CPU (Shard 1)|Procent|Maximum||None|
|connectedclients2|Verbonden clients (Shard 2)|Count|Maximum||None|
|totalcommandsprocessed2|Totaal aantal bewerkingen (Shard 2)|Count|Totaal||None|
|cachehits2|Cache treffers (Shard 2)|Count|Totaal||None|
|cachemisses2|Cache missers (Shard 2)|Count|Totaal||None|
|getcommands2|Hiermee wordt opgehaald (Shard 2)|Count|Totaal||None|
|setcommands2|Sets (Shard 2)|Count|Totaal||None|
|operationsPerSecond2|Bewerkingen per seconde (Shard 2)|Count|Maximum||None|
|evictedkeys2|Verwijderde sleutels (Shard 2)|Count|Totaal||None|
|totalkeys2|Totaal aantal sleutels (Shard 2)|Count|Maximum||None|
|expiredkeys2|Verlopen sleutels (Shard 2)|Count|Totaal||None|
|usedmemory2|Gebruikt geheugen (Shard 2)|Bytes|Maximum||None|
|usedmemoryRss2|Gebruikte geheugen RSS (Shard 2)|Bytes|Maximum||None|
|serverLoad2|Server belasting (Shard 2)|Procent|Maximum||None|
|cacheWrite2|Cache schrijven (Shard 2)|BytesPerSecond|Maximum||None|
|cacheRead2|Lees bewerking in cache (Shard 2)|BytesPerSecond|Maximum||None|
|percentProcessorTime2|CPU (Shard 2)|Procent|Maximum||None|
|connectedclients3|Verbonden clients (Shard 3)|Count|Maximum||None|
|totalcommandsprocessed3|Totaal aantal bewerkingen (Shard 3)|Count|Totaal||None|
|cachehits3|Cache treffers (Shard 3)|Count|Totaal||None|
|cachemisses3|Cache missers (Shard 3)|Count|Totaal||None|
|getcommands3|Hiermee wordt opgehaald (Shard 3)|Count|Totaal||None|
|setcommands3|Sets (Shard 3)|Count|Totaal||None|
|operationsPerSecond3|Bewerkingen per seconde (Shard 3)|Count|Maximum||None|
|evictedkeys3|Verwijderde sleutels (Shard 3)|Count|Totaal||None|
|totalkeys3|Totaal aantal sleutels (Shard 3)|Count|Maximum||None|
|expiredkeys3|Verlopen sleutels (Shard 3)|Count|Totaal||None|
|usedmemory3|Gebruikt geheugen (Shard 3)|Bytes|Maximum||None|
|usedmemoryRss3|Gebruikte geheugen RSS (Shard 3)|Bytes|Maximum||None|
|serverLoad3|Server belasting (Shard 3)|Procent|Maximum||None|
|cacheWrite3|Cache schrijven (Shard 3)|BytesPerSecond|Maximum||None|
|cacheRead3|Lees bewerking in cache (Shard 3)|BytesPerSecond|Maximum||None|
|percentProcessorTime3|CPU (Shard 3)|Procent|Maximum||None|
|connectedclients4|Verbonden clients (Shard 4)|Count|Maximum||None|
|totalcommandsprocessed4|Totaal aantal bewerkingen (Shard 4)|Count|Totaal||None|
|cachehits4|Cache treffers (Shard 4)|Count|Totaal||None|
|cachemisses4|Cache missers (Shard 4)|Count|Totaal||None|
|getcommands4|Hiermee wordt opgehaald (Shard 4)|Count|Totaal||None|
|setcommands4|Sets (Shard 4)|Count|Totaal||None|
|operationsPerSecond4|Bewerkingen per seconde (Shard 4)|Count|Maximum||None|
|evictedkeys4|Verwijderde sleutels (Shard 4)|Count|Totaal||None|
|totalkeys4|Totaal aantal sleutels (Shard 4)|Count|Maximum||None|
|expiredkeys4|Verlopen sleutels (Shard 4)|Count|Totaal||None|
|usedmemory4|Gebruikt geheugen (Shard 4)|Bytes|Maximum||None|
|usedmemoryRss4|Gebruikte geheugen RSS (Shard 4)|Bytes|Maximum||None|
|serverLoad4|Server belasting (Shard 4)|Procent|Maximum||None|
|cacheWrite4|Cache schrijven (Shard 4)|BytesPerSecond|Maximum||None|
|cacheRead4|Lees bewerking in cache (Shard 4)|BytesPerSecond|Maximum||None|
|percentProcessorTime4|CPU (Shard 4)|Procent|Maximum||None|
|connectedclients5|Verbonden clients (Shard 5)|Count|Maximum||None|
|totalcommandsprocessed5|Totaal aantal bewerkingen (Shard 5)|Count|Totaal||None|
|cachehits5|Cache treffers (Shard 5)|Count|Totaal||None|
|cachemisses5|Cache missers (Shard 5)|Count|Totaal||None|
|getcommands5|Hiermee wordt opgehaald (Shard 5)|Count|Totaal||None|
|setcommands5|Sets (Shard 5)|Count|Totaal||None|
|operationsPerSecond5|Bewerkingen per seconde (Shard 5)|Count|Maximum||None|
|evictedkeys5|Verwijderde sleutels (Shard 5)|Count|Totaal||None|
|totalkeys5|Totaal aantal sleutels (Shard 5)|Count|Maximum||None|
|expiredkeys5|Verlopen sleutels (Shard 5)|Count|Totaal||None|
|usedmemory5|Gebruikt geheugen (Shard 5)|Bytes|Maximum||None|
|usedmemoryRss5|Gebruikte geheugen RSS (Shard 5)|Bytes|Maximum||None|
|serverLoad5|Server belasting (Shard 5)|Procent|Maximum||None|
|cacheWrite5|Cache schrijven (Shard 5)|BytesPerSecond|Maximum||None|
|cacheRead5|Lees bewerking in cache (Shard 5)|BytesPerSecond|Maximum||None|
|percentProcessorTime5|CPU (Shard 5)|Procent|Maximum||None|
|connectedclients6|Verbonden clients (Shard 6)|Count|Maximum||None|
|totalcommandsprocessed6|Totaal aantal bewerkingen (Shard 6)|Count|Totaal||None|
|cachehits6|Cache treffers (Shard 6)|Count|Totaal||None|
|cachemisses6|Cache missers (Shard 6)|Count|Totaal||None|
|getcommands6|Hiermee wordt opgehaald (Shard 6)|Count|Totaal||None|
|setcommands6|Sets (Shard 6)|Count|Totaal||None|
|operationsPerSecond6|Bewerkingen per seconde (Shard 6)|Count|Maximum||None|
|evictedkeys6|Verwijderde sleutels (Shard 6)|Count|Totaal||None|
|totalkeys6|Totaal aantal sleutels (Shard 6)|Count|Maximum||None|
|expiredkeys6|Verlopen sleutels (Shard 6)|Count|Totaal||None|
|usedmemory6|Gebruikt geheugen (Shard 6)|Bytes|Maximum||None|
|usedmemoryRss6|Gebruikte geheugen RSS (Shard 6)|Bytes|Maximum||None|
|serverLoad6|Server belasting (Shard 6)|Procent|Maximum||None|
|cacheWrite6|Cache schrijven (Shard 6)|BytesPerSecond|Maximum||None|
|cacheRead6|Lees bewerking in cache (Shard 6)|BytesPerSecond|Maximum||None|
|percentProcessorTime6|CPU (Shard 6)|Procent|Maximum||None|
|connectedclients7|Verbonden clients (Shard 7)|Count|Maximum||None|
|totalcommandsprocessed7|Totaal aantal bewerkingen (Shard 7)|Count|Totaal||None|
|cachehits7|Cache treffers (Shard 7)|Count|Totaal||None|
|cachemisses7|Cache missers (Shard 7)|Count|Totaal||None|
|getcommands7|Hiermee wordt opgehaald (Shard 7)|Count|Totaal||None|
|setcommands7|Sets (Shard 7)|Count|Totaal||None|
|operationsPerSecond7|Bewerkingen per seconde (Shard 7)|Count|Maximum||None|
|evictedkeys7|Verwijderde sleutels (Shard 7)|Count|Totaal||None|
|totalkeys7|Totaal aantal sleutels (Shard 7)|Count|Maximum||None|
|expiredkeys7|Verlopen sleutels (Shard 7)|Count|Totaal||None|
|usedmemory7|Gebruikt geheugen (Shard 7)|Bytes|Maximum||None|
|usedmemoryRss7|Gebruikte geheugen RSS (Shard 7)|Bytes|Maximum||None|
|serverLoad7|Server belasting (Shard 7)|Procent|Maximum||None|
|cacheWrite7|Cache schrijven (Shard 7)|BytesPerSecond|Maximum||None|
|cacheRead7|Lees bewerking in cache (Shard 7)|BytesPerSecond|Maximum||None|
|percentProcessorTime7|CPU (Shard 7)|Procent|Maximum||None|
|connectedclients8|Verbonden clients (Shard 8)|Count|Maximum||None|
|totalcommandsprocessed8|Totaal aantal bewerkingen (Shard 8)|Count|Totaal||None|
|cachehits8|Cache treffers (Shard 8)|Count|Totaal||None|
|cachemisses8|Cache missers (Shard 8)|Count|Totaal||None|
|getcommands8|Hiermee wordt opgehaald (Shard 8)|Count|Totaal||None|
|setcommands8|Sets (Shard 8)|Count|Totaal||None|
|operationsPerSecond8|Bewerkingen per seconde (Shard 8)|Count|Maximum||None|
|evictedkeys8|Verwijderde sleutels (Shard 8)|Count|Totaal||None|
|totalkeys8|Totaal aantal sleutels (Shard 8)|Count|Maximum||None|
|expiredkeys8|Verlopen sleutels (Shard 8)|Count|Totaal||None|
|usedmemory8|Gebruikt geheugen (Shard 8)|Bytes|Maximum||None|
|usedmemoryRss8|Gebruikt geheugen RSS (Shard 8)|Bytes|Maximum||None|
|serverLoad8|Server belasting (Shard 8)|Procent|Maximum||None|
|cacheWrite8|Cache schrijven (Shard 8)|BytesPerSecond|Maximum||None|
|cacheRead8|Lees bewerking in cache (Shard 8)|BytesPerSecond|Maximum||None|
|percentProcessorTime8|CPU (Shard 8)|Procent|Maximum||None|
|connectedclients9|Verbonden clients (Shard 9)|Count|Maximum||None|
|totalcommandsprocessed9|Totaal aantal bewerkingen (Shard 9)|Count|Totaal||None|
|cachehits9|Cache treffers (Shard 9)|Count|Totaal||None|
|cachemisses9|Cache missers (Shard 9)|Count|Totaal||None|
|getcommands9|Hiermee wordt opgehaald (Shard 9)|Count|Totaal||None|
|setcommands9|Sets (Shard 9)|Count|Totaal||None|
|operationsPerSecond9|Bewerkingen per seconde (Shard 9)|Count|Maximum||None|
|evictedkeys9|Verwijderde sleutels (Shard 9)|Count|Totaal||None|
|totalkeys9|Totaal aantal sleutels (Shard 9)|Count|Maximum||None|
|expiredkeys9|Verlopen sleutels (Shard 9)|Count|Totaal||None|
|usedmemory9|Gebruikt geheugen (Shard 9)|Bytes|Maximum||None|
|usedmemoryRss9|Gebruikte geheugen RSS (Shard 9)|Bytes|Maximum||None|
|serverLoad9|Server belasting (Shard 9)|Procent|Maximum||None|
|cacheWrite9|Cache schrijven (Shard 9)|BytesPerSecond|Maximum||None|
|cacheRead9|Lees bewerking in cache (Shard 9)|BytesPerSecond|Maximum||None|
|percentProcessorTime9|CPU (Shard 9)|Procent|Maximum||None|



## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Micro soft. CDN/cdnwebapplicationfirewallpolicies

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Aantal aanvragen voor Web Application firewall|Count|Totaal|Het aantal client aanvragen dat is verwerkt door de Web Application firewall|Beleidsnaam, regelnaam, actie|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Procent|Average|Het percentage toegewezen reken eenheden dat momenteel wordt gebruikt door de virtuele machine (s).|None|
|Netwerk in|Netwerk in|Bytes|Totaal|Het aantal bytes dat is ontvangen op alle netwerk interfaces door de virtuele machine (s) (binnenkomend verkeer).|None|
|Netwerk uit|Netwerk uit|Bytes|Totaal|Het aantal uitgaande bytes op alle netwerk interfaces door de virtuele machine (s) (uitgaand verkeer).|None|
|Gelezen bytes per seconde|Schijf lezen|BytesPerSecond|Average|Gemiddeld aantal gelezen bytes van de schijf tijdens de controle periode.|None|
|Geschreven bytes per seconde|Schijf schrijven|BytesPerSecond|Average|Gemiddeld aantal bytes dat tijdens de controle periode naar de schijf wordt geschreven.|None|
|Lees bewerkingen op de schijf per seconde|Lees bewerkingen op de schijf per seconde|CountPerSecond|Average|Read-IOPS van schijf.|None|
|Schrijf bewerkingen op de schijf per seconde|Schrijf bewerkingen op de schijf per seconde|CountPerSecond|Average|Schijf schrijf-IOPS.|None|


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



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|UsedCapacity|Gebruikte capaciteit|Bytes|Average|Gebruikte capaciteit van account|None|
|Transacties|Transacties|Count|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de ResponseType-dimensie voor het aantal verschillende type reactie.|ResponseType, geotype, ApiName, authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid inkomende gegevens in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgaande gegevens in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie|
|SuccessServerLatency|Geslaagde serverlatentie|Milliseconden|Average|De latentie die door Azure Storage wordt gebruikt voor het verwerken van een geslaagde aanvraag, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency.|Geotype, ApiName, authenticatie|
|SuccessE2ELatency|Geslaagde E2E-latentie|Milliseconden|Average|De end-to-end latentie van geslaagde aanvragen voor een opslag service of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|Het percentage van de beschikbaarheid van de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Micro soft. ClassicStorage/Storage accounts/blobServices

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BlobCapacity|Blobcapaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de Blob-service van het opslagaccount (in bytes).|BlobType, Tier|
|BlobCount|Aantal blobs|Count|Average|Het aantal blobs in de Blob-service van het opslagaccount.|BlobType, Tier|
|ContainerCount|Aantal blobcontainers|Count|Average|Het aantal containers in de Blob-service van het opslagaccount.|None|
|IndexCapacity|Index capaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de index van de ADLS Gen2 (hiërarchisch) in bytes.|None|
|Transacties|Transacties|Count|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de ResponseType-dimensie voor het aantal verschillende type reactie.|ResponseType, geotype, ApiName, authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid inkomende gegevens in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgaande gegevens in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie|
|SuccessServerLatency|Geslaagde serverlatentie|Milliseconden|Average|De latentie die door Azure Storage wordt gebruikt voor het verwerken van een geslaagde aanvraag, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency.|Geotype, ApiName, authenticatie|
|SuccessE2ELatency|Geslaagde E2E-latentie|Milliseconden|Average|De end-to-end latentie van geslaagde aanvragen voor een opslag service of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|Het percentage van de beschikbaarheid van de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Micro soft. ClassicStorage/Storage accounts/tableServices

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TableCapacity|Tabelcapaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de Table-service van het opslagaccount (in bytes).|None|
|TableCount|Aantal tabellen|Count|Average|Het aantal tabellen in de Table-service van het opslagaccount.|None|
|TableEntityCount|Aantal tabelentiteiten|Count|Average|Het aantal tabelentiteiten in de Table-service van het opslagaccount.|None|
|Transacties|Transacties|Count|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de ResponseType-dimensie voor het aantal verschillende type reactie.|ResponseType, geotype, ApiName, authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid inkomende gegevens in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgaande gegevens in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie|
|SuccessServerLatency|Geslaagde serverlatentie|Milliseconden|Average|De latentie die door Azure Storage wordt gebruikt voor het verwerken van een geslaagde aanvraag, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency.|Geotype, ApiName, authenticatie|
|SuccessE2ELatency|Geslaagde E2E-latentie|Milliseconden|Average|De end-to-end latentie van geslaagde aanvragen voor een opslag service of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|Het percentage van de beschikbaarheid van de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Micro soft. ClassicStorage/Storage accounts/fileServices

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|FileCapacity|Bestands capaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de bestands service van het opslag account in bytes.|Bestands share|
|FileCount|Aantal bestanden|Count|Average|Het aantal bestanden in de file-service van het opslag account.|Bestands share|
|FileShareCount|Aantal bestands shares|Count|Average|Het aantal bestands shares in de file-service van het opslag account.|None|
|FileShareSnapshotCount|Aantal moment opnamen van bestands shares|Count|Average|Het aantal moment opnamen dat aanwezig is op de share in de bestanden service van het opslag account.|Bestands share|
|FileShareSnapshotSize|Grootte van moment opname van bestands share|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de moment opnamen in de bestands service van het opslag account in bytes.|Bestands share|
|FileShareQuota|Quota grootte van bestands share|Bytes|Average|De bovengrens voor de hoeveelheid opslag die kan worden gebruikt door Azure Files service in bytes.|Bestands share|
|Transacties|Transacties|Count|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de ResponseType-dimensie voor het aantal verschillende type reactie.|ResponseType, geotype, ApiName, Authentication, file share|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid inkomende gegevens in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie, bestands share|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgaande gegevens in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie, bestands share|
|SuccessServerLatency|Geslaagde serverlatentie|Milliseconden|Average|De latentie die door Azure Storage wordt gebruikt voor het verwerken van een geslaagde aanvraag, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency.|Geotype, ApiName, authenticatie, bestands share|
|SuccessE2ELatency|Geslaagde E2E-latentie|Milliseconden|Average|De end-to-end latentie van geslaagde aanvragen voor een opslag service of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie, bestands share|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|Het percentage van de beschikbaarheid van de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie, bestands share|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Micro soft. ClassicStorage/Storage accounts/queueServices

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|QueueCapacity|Wachtrijcapaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de Queue-service van het opslagaccount (in bytes).|None|
|QueueCount|Aantal wachtrijen|Count|Average|Het aantal wachtrijen in de Queue-service van het opslagaccount.|None|
|QueueMessageCount|Aantal wachtrijberichten|Count|Average|Het geschatte aantal wachtrijberichten in de Queue-service van het opslagaccount.|None|
|Transacties|Transacties|Count|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de ResponseType-dimensie voor het aantal verschillende type reactie.|ResponseType, geotype, ApiName, authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid inkomende gegevens in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgaande gegevens in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie|
|SuccessServerLatency|Geslaagde serverlatentie|Milliseconden|Average|De latentie die door Azure Storage wordt gebruikt voor het verwerken van een geslaagde aanvraag, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency.|Geotype, ApiName, authenticatie|
|SuccessE2ELatency|Geslaagde E2E-latentie|Milliseconden|Average|De end-to-end latentie van geslaagde aanvragen voor een opslag service of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|Het percentage van de beschikbaarheid van de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TotalCalls|Totaal aantal aanroepen|Count|Totaal|Totaal aantal aanroepen.|ApiName, Operationname, regio|
|SuccessfulCalls|Geslaagde aanroepen|Count|Totaal|Aantal geslaagde aanroepen.|ApiName, Operationname, regio|
|TotalErrors|Totale aantal fouten|Count|Totaal|Totaal aantal aanroepen met een fout respons (HTTP-antwoord code 4xx of 5xx).|ApiName, Operationname, regio|
|BlockedCalls|Geblokkeerde aanroepen|Count|Totaal|Aantal aanroepen dat de frequentie of quotum limiet heeft overschreden.|ApiName, Operationname, regio|
|ServerErrors|Server fouten|Count|Totaal|Het aantal aanroepen met een interne fout in de service (HTTP-antwoord code 5xx).|ApiName, Operationname, regio|
|ClientErrors|Client fouten|Count|Totaal|Het aantal aanroepen met een fout aan de client zijde (HTTP-antwoord code 4xx).|ApiName, Operationname, regio|
|DataIn|Gegevens in|Bytes|Totaal|Grootte van binnenkomende gegevens in bytes.|ApiName, Operationname, regio|
|DataOut|Gegevens uit|Bytes|Totaal|Grootte van uitgaande gegevens in bytes.|ApiName, Operationname, regio|
|Latentie|Latentie|MilliSeconds|Average|Latentie in milliseconden.|ApiName, Operationname, regio|
|CharactersTranslated|Geconverteerde tekens|Count|Totaal|Totaal aantal tekens in binnenkomende-tekst aanvraag.|ApiName, Operationname, regio|
|CharactersTrained|Getrainde tekens|Count|Totaal|Totaal aantal getrainde tekens.|ApiName, Operationname, regio|
|SpeechSessionDuration|Spraak sessie duur|Waarna|Totaal|Totale duur van de spraak sessie in seconden.|ApiName, Operationname, regio|
|TotalTransactions|Totaal aantal trans acties|Count|Totaal|Totaal aantal trans acties.|None|
|TotalTokenCalls|Totaal aantal token aanroepen|Count|Totaal|Totaal aantal token aanroepen.|ApiName, Operationname, regio|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Procent|Average|Het percentage toegewezen berekeningseenheden dat momenteel door de virtuele machine(s) wordt gebruikt|None|
|Netwerk in|Netwerk in Factureerbaar (afgeschaft)|Bytes|Totaal|Het aantal factureer bare bytes dat is ontvangen op alle netwerk interfaces door de virtuele machine (s) (binnenkomend verkeer) (afgeschaft)|None|
|Netwerk uit|Gefactureerd netwerk (afgeschaft)|Bytes|Totaal|Het aantal factureer bare bytes op alle netwerk interfaces door de virtuele machine (s) (uitgaand verkeer) (afgeschaft)|None|
|Gelezen bytes op de schijf|Gelezen bytes op de schijf|Bytes|Totaal|Gelezen bytes van de schijf tijdens de controle periode|None|
|Geschreven bytes op de schijf|Geschreven bytes op de schijf|Bytes|Totaal|Naar schijf geschreven bytes tijdens de controle periode|None|
|Lees bewerkingen op de schijf per seconde|Lees bewerkingen op de schijf per seconde|CountPerSecond|Average|Lees-IOPS schijf|None|
|Schrijf bewerkingen op de schijf per seconde|Schrijf bewerkingen op de schijf per seconde|CountPerSecond|Average|Schijf schrijf-IOPS|None|
|Resterende CPU-tegoeden|Resterende CPU-tegoeden|Count|Average|Totaal aantal beschik bare tegoeden voor burst|None|
|Verbruikte CPU-tegoeden|Verbruikte CPU-tegoeden|Count|Average|Totaal aantal tegoeden dat door de virtuele machine wordt verbruikt|None|
|Per schijf gelezen bytes/sec.|Gegevens schijf gelezen bytes per seconde (afgeschaft)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode|SlotId|
|Schrijf bewerkingen per schijf/sec.|Gegevens schijf schrijf bewerkingen in bytes per seconde (afgeschaft)|CountPerSecond|Average|Tijdens de controle periode naar één schijf geschreven bytes per seconde|SlotId|
|Lees bewerkingen per schijf/sec.|Lees bewerkingen op de gegevens schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode|SlotId|
|Schrijf bewerkingen per schijf/SEC|Schrijf bewerkingen op de gegevens schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS tijdens de controle periode van één schijf schrijven|SlotId|
|WACHTRIJ diepte per schijf|WACHTRIJ diepte van de gegevens schijf (afgeschaft)|Count|Average|Wachtrij diepte van gegevens schijf (of wachtrij lengte)|SlotId|
|BESTURINGSSYSTEEM per schijf gelezen bytes per seconde|BESTURINGSSYSTEEM schijf gelezen bytes per seconde (afgeschaft)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode voor de besturingssysteem schijf|None|
|Per schijf geschreven bytes/sec.|BESTURINGSSYSTEEM schijf schrijf bewerkingen in bytes per seconde (afgeschaft)|CountPerSecond|Average|Geschreven bytes per seconde tijdens de controle periode voor de besturingssysteem schijf|None|
|Lees bewerkingen per schijf voor het besturings systeem/sec.|Lees bewerkingen op de besturingssysteem schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode voor de besturingssysteem schijf|None|
|Schrijf bewerkingen per schijf van het besturings systeem/SEC|Schrijf bewerkingen op de besturingssysteem schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS tijdens de controle periode voor de besturingssysteem schijf schrijven vanaf één schijf|None|
|WACHTRIJ diepte voor het besturings systeem per schijf|WACHTRIJ diepte van de besturingssysteem schijf (afgeschaft)|Count|Average|Wachtrij diepte van het besturings systeem (of wachtrij lengte)|None|
|Gegevens schijf gelezen bytes per seconde|Gegevens schijf gelezen bytes per seconde (preview)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode|LUN|
|Geschreven bytes per seconde gegevens schijf|Geschreven bytes per seconde (preview) gegevens schijf|CountPerSecond|Average|Tijdens de controle periode naar één schijf geschreven bytes per seconde|LUN|
|Lees bewerkingen op de gegevens schijf per seconde|Lees bewerkingen op de gegevens schijf per seconde (preview)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode|LUN|
|Schrijf bewerkingen op de gegevens schijf per seconde|Schrijf bewerkingen op de gegevens schijf per seconde (preview)|CountPerSecond|Average|IOPS tijdens de controle periode van één schijf schrijven|LUN|
|Wachtrij diepte van gegevens schijf|Wachtrij diepte van gegevens schijf (preview-versie)|Count|Average|Wachtrij diepte van gegevens schijf (of wachtrij lengte)|LUN|
|BESTURINGSSYSTEEM schijf gelezen bytes per seconde|BESTURINGSSYSTEEM schijf gelezen bytes per seconde (preview)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode voor de besturingssysteem schijf|None|
|Schrijf bewerkingen op de besturingssysteem schijf per seconde|Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)|CountPerSecond|Average|Geschreven bytes per seconde tijdens de controle periode voor de besturingssysteem schijf|None|
|Lees bewerkingen van de besturingssysteem schijf per seconde|Lees bewerkingen op de besturingssysteem schijf per seconde (preview)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode voor de besturingssysteem schijf|None|
|Schrijf bewerkingen op de besturingssysteem schijf per seconde|Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)|CountPerSecond|Average|IOPS tijdens de controle periode voor de besturingssysteem schijf schrijven vanaf één schijf|None|
|Wachtrij diepte van besturingssysteem schijf|Wachtrij diepte van de besturingssysteem schijf (preview-versie)|Count|Average|Wachtrij diepte van het besturings systeem (of wachtrij lengte)|None|
|Binnenkomende stromen|Binnenkomende stromen|Count|Average|Inkomende stromen zijn het aantal huidige stromen in de binnenkomende richting (verkeer dat wordt verzonden naar de virtuele machine)|None|
|Uitgaande stromen|Uitgaande stromen|Count|Average|Uitgaande stromen zijn het aantal huidige stromen in de uitgaande richting (verkeer dat uit de virtuele machine wordt verzonden)|None|
|Maximum aanmaak frequentie inkomende stromen|Maximum aantal aanmaak snelheden inkomende stromen (preview-versie)|CountPerSecond|Average|Het maximale aantal inkomende stromen (verkeer dat wordt verzonden naar de virtuele machine)|None|
|Maximum aanmaak frequentie van uitgaande stromen|Maximum aantal uitgaande stromen voor maken (preview)|CountPerSecond|Average|De maximale aanmaak frequentie van uitgaande stromen (verkeer dat uit de virtuele machine wordt verzonden)|None|
|Treffer voor Premium data-schijf cache lezen|Cache geheugen voor lezen van Premium-gegevens schijf (preview-versie)|Procent|Average|Treffer voor Premium data-schijf cache lezen|LUN|
|Lees missers cache Premium-gegevens schijf|Cache voor lezen van Premium-gegevens schijf (preview)|Procent|Average|Lees missers cache Premium-gegevens schijf|LUN|
|Treffer voor Premium-besturingssysteem schijf cache lezen|Treffer voor het lezen van een Premium-besturingssysteem schijf cache (preview-versie)|Procent|Average|Treffer voor Premium-besturingssysteem schijf cache lezen|None|
|Leesmij voor Premium-besturingssysteem schijf cache lezen|Schijf cache voor Premium-Lees-missers (preview-versie)|Procent|Average|Leesmij voor Premium-besturingssysteem schijf cache lezen|None|
|Totaal netwerk|Totaal netwerk|Bytes|Totaal|Het aantal ontvangen bytes op alle netwerk interfaces door de virtuele machine (s) (binnenkomend verkeer)|None|
|Totaal aantal netwerk|Totaal aantal netwerk|Bytes|Totaal|Het aantal uitgaande bytes op alle netwerk interfaces door de virtuele machine (s) (uitgaand verkeer)|None|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Procent|Average|Het percentage toegewezen berekeningseenheden dat momenteel door de virtuele machine(s) wordt gebruikt|VMName|
|Netwerk in|Netwerk in Factureerbaar (afgeschaft)|Bytes|Totaal|Het aantal factureer bare bytes dat is ontvangen op alle netwerk interfaces door de virtuele machine (s) (binnenkomend verkeer) (afgeschaft)|VMName|
|Netwerk uit|Gefactureerd netwerk (afgeschaft)|Bytes|Totaal|Het aantal factureer bare bytes op alle netwerk interfaces door de virtuele machine (s) (uitgaand verkeer) (afgeschaft)|VMName|
|Gelezen bytes op de schijf|Gelezen bytes op de schijf|Bytes|Totaal|Gelezen bytes van de schijf tijdens de controle periode|VMName|
|Geschreven bytes op de schijf|Geschreven bytes op de schijf|Bytes|Totaal|Naar schijf geschreven bytes tijdens de controle periode|VMName|
|Lees bewerkingen op de schijf per seconde|Lees bewerkingen op de schijf per seconde|CountPerSecond|Average|Lees-IOPS schijf|VMName|
|Schrijf bewerkingen op de schijf per seconde|Schrijf bewerkingen op de schijf per seconde|CountPerSecond|Average|Schijf schrijf-IOPS|VMName|
|Resterende CPU-tegoeden|Resterende CPU-tegoeden|Count|Average|Totaal aantal beschik bare tegoeden voor burst|None|
|Verbruikte CPU-tegoeden|Verbruikte CPU-tegoeden|Count|Average|Totaal aantal tegoeden dat door de virtuele machine wordt verbruikt|None|
|Per schijf gelezen bytes/sec.|Gegevens schijf gelezen bytes per seconde (afgeschaft)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode|SlotId|
|Schrijf bewerkingen per schijf/sec.|Gegevens schijf schrijf bewerkingen in bytes per seconde (afgeschaft)|CountPerSecond|Average|Tijdens de controle periode naar één schijf geschreven bytes per seconde|SlotId|
|Lees bewerkingen per schijf/sec.|Lees bewerkingen op de gegevens schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode|SlotId|
|Schrijf bewerkingen per schijf/SEC|Schrijf bewerkingen op de gegevens schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS tijdens de controle periode van één schijf schrijven|SlotId|
|WACHTRIJ diepte per schijf|WACHTRIJ diepte van de gegevens schijf (afgeschaft)|Count|Average|Wachtrij diepte van gegevens schijf (of wachtrij lengte)|SlotId|
|BESTURINGSSYSTEEM per schijf gelezen bytes per seconde|BESTURINGSSYSTEEM schijf gelezen bytes per seconde (afgeschaft)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode voor de besturingssysteem schijf|None|
|Per schijf geschreven bytes/sec.|BESTURINGSSYSTEEM schijf schrijf bewerkingen in bytes per seconde (afgeschaft)|CountPerSecond|Average|Geschreven bytes per seconde tijdens de controle periode voor de besturingssysteem schijf|None|
|Lees bewerkingen per schijf voor het besturings systeem/sec.|Lees bewerkingen op de besturingssysteem schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode voor de besturingssysteem schijf|None|
|Schrijf bewerkingen per schijf van het besturings systeem/SEC|Schrijf bewerkingen op de besturingssysteem schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS tijdens de controle periode voor de besturingssysteem schijf schrijven vanaf één schijf|None|
|WACHTRIJ diepte voor het besturings systeem per schijf|WACHTRIJ diepte van de besturingssysteem schijf (afgeschaft)|Count|Average|Wachtrij diepte van het besturings systeem (of wachtrij lengte)|None|
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
|Binnenkomende stromen|Binnenkomende stromen|Count|Average|Inkomende stromen zijn het aantal huidige stromen in de binnenkomende richting (verkeer dat wordt verzonden naar de virtuele machine)|VMName|
|Uitgaande stromen|Uitgaande stromen|Count|Average|Uitgaande stromen zijn het aantal huidige stromen in de uitgaande richting (verkeer dat uit de virtuele machine wordt verzonden)|VMName|
|Maximum aanmaak frequentie inkomende stromen|Maximum aantal aanmaak snelheden inkomende stromen (preview-versie)|CountPerSecond|Average|Het maximale aantal inkomende stromen (verkeer dat wordt verzonden naar de virtuele machine)|VMName|
|Maximum aanmaak frequentie van uitgaande stromen|Maximum aantal uitgaande stromen voor maken (preview)|CountPerSecond|Average|De maximale aanmaak frequentie van uitgaande stromen (verkeer dat uit de virtuele machine wordt verzonden)|VMName|
|Treffer voor Premium data-schijf cache lezen|Cache geheugen voor lezen van Premium-gegevens schijf (preview-versie)|Procent|Average|Treffer voor Premium data-schijf cache lezen|LUN, VMName|
|Lees missers cache Premium-gegevens schijf|Cache voor lezen van Premium-gegevens schijf (preview)|Procent|Average|Lees missers cache Premium-gegevens schijf|LUN, VMName|
|Treffer voor Premium-besturingssysteem schijf cache lezen|Treffer voor het lezen van een Premium-besturingssysteem schijf cache (preview-versie)|Procent|Average|Treffer voor Premium-besturingssysteem schijf cache lezen|VMName|
|Leesmij voor Premium-besturingssysteem schijf cache lezen|Schijf cache voor Premium-Lees-missers (preview-versie)|Procent|Average|Leesmij voor Premium-besturingssysteem schijf cache lezen|VMName|
|Totaal netwerk|Totaal netwerk|Bytes|Totaal|Het aantal ontvangen bytes op alle netwerk interfaces door de virtuele machine (s) (binnenkomend verkeer)|VMName|
|Totaal aantal netwerk|Totaal aantal netwerk|Bytes|Totaal|Het aantal uitgaande bytes op alle netwerk interfaces door de virtuele machine (s) (uitgaand verkeer)|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Procent|Average|Het percentage toegewezen berekeningseenheden dat momenteel door de virtuele machine(s) wordt gebruikt|None|
|Netwerk in|Netwerk in Factureerbaar (afgeschaft)|Bytes|Totaal|Het aantal factureer bare bytes dat is ontvangen op alle netwerk interfaces door de virtuele machine (s) (binnenkomend verkeer) (afgeschaft)|None|
|Netwerk uit|Gefactureerd netwerk (afgeschaft)|Bytes|Totaal|Het aantal factureer bare bytes op alle netwerk interfaces door de virtuele machine (s) (uitgaand verkeer) (afgeschaft)|None|
|Gelezen bytes op de schijf|Gelezen bytes op de schijf|Bytes|Totaal|Gelezen bytes van de schijf tijdens de controle periode|None|
|Geschreven bytes op de schijf|Geschreven bytes op de schijf|Bytes|Totaal|Naar schijf geschreven bytes tijdens de controle periode|None|
|Lees bewerkingen op de schijf per seconde|Lees bewerkingen op de schijf per seconde|CountPerSecond|Average|Lees-IOPS schijf|None|
|Schrijf bewerkingen op de schijf per seconde|Schrijf bewerkingen op de schijf per seconde|CountPerSecond|Average|Schijf schrijf-IOPS|None|
|Resterende CPU-tegoeden|Resterende CPU-tegoeden|Count|Average|Totaal aantal beschik bare tegoeden voor burst|None|
|Verbruikte CPU-tegoeden|Verbruikte CPU-tegoeden|Count|Average|Totaal aantal tegoeden dat door de virtuele machine wordt verbruikt|None|
|Per schijf gelezen bytes/sec.|Gegevens schijf gelezen bytes per seconde (afgeschaft)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode|SlotId|
|Schrijf bewerkingen per schijf/sec.|Gegevens schijf schrijf bewerkingen in bytes per seconde (afgeschaft)|CountPerSecond|Average|Tijdens de controle periode naar één schijf geschreven bytes per seconde|SlotId|
|Lees bewerkingen per schijf/sec.|Lees bewerkingen op de gegevens schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode|SlotId|
|Schrijf bewerkingen per schijf/SEC|Schrijf bewerkingen op de gegevens schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS tijdens de controle periode van één schijf schrijven|SlotId|
|WACHTRIJ diepte per schijf|WACHTRIJ diepte van de gegevens schijf (afgeschaft)|Count|Average|Wachtrij diepte van gegevens schijf (of wachtrij lengte)|SlotId|
|BESTURINGSSYSTEEM per schijf gelezen bytes per seconde|BESTURINGSSYSTEEM schijf gelezen bytes per seconde (afgeschaft)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode voor de besturingssysteem schijf|None|
|Per schijf geschreven bytes/sec.|BESTURINGSSYSTEEM schijf schrijf bewerkingen in bytes per seconde (afgeschaft)|CountPerSecond|Average|Geschreven bytes per seconde tijdens de controle periode voor de besturingssysteem schijf|None|
|Lees bewerkingen per schijf voor het besturings systeem/sec.|Lees bewerkingen op de besturingssysteem schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode voor de besturingssysteem schijf|None|
|Schrijf bewerkingen per schijf van het besturings systeem/SEC|Schrijf bewerkingen op de besturingssysteem schijf per seconde (afgeschaft)|CountPerSecond|Average|IOPS tijdens de controle periode voor de besturingssysteem schijf schrijven vanaf één schijf|None|
|WACHTRIJ diepte voor het besturings systeem per schijf|WACHTRIJ diepte van de besturingssysteem schijf (afgeschaft)|Count|Average|Wachtrij diepte van het besturings systeem (of wachtrij lengte)|None|
|Gegevens schijf gelezen bytes per seconde|Gegevens schijf gelezen bytes per seconde (preview)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode|LUN|
|Geschreven bytes per seconde gegevens schijf|Geschreven bytes per seconde (preview) gegevens schijf|CountPerSecond|Average|Tijdens de controle periode naar één schijf geschreven bytes per seconde|LUN|
|Lees bewerkingen op de gegevens schijf per seconde|Lees bewerkingen op de gegevens schijf per seconde (preview)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode|LUN|
|Schrijf bewerkingen op de gegevens schijf per seconde|Schrijf bewerkingen op de gegevens schijf per seconde (preview)|CountPerSecond|Average|IOPS tijdens de controle periode van één schijf schrijven|LUN|
|Wachtrij diepte van gegevens schijf|Wachtrij diepte van gegevens schijf (preview-versie)|Count|Average|Wachtrij diepte van gegevens schijf (of wachtrij lengte)|LUN|
|BESTURINGSSYSTEEM schijf gelezen bytes per seconde|BESTURINGSSYSTEEM schijf gelezen bytes per seconde (preview)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode voor de besturingssysteem schijf|None|
|Schrijf bewerkingen op de besturingssysteem schijf per seconde|Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)|CountPerSecond|Average|Geschreven bytes per seconde tijdens de controle periode voor de besturingssysteem schijf|None|
|Lees bewerkingen van de besturingssysteem schijf per seconde|Lees bewerkingen op de besturingssysteem schijf per seconde (preview)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode voor de besturingssysteem schijf|None|
|Schrijf bewerkingen op de besturingssysteem schijf per seconde|Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)|CountPerSecond|Average|IOPS tijdens de controle periode voor de besturingssysteem schijf schrijven vanaf één schijf|None|
|Wachtrij diepte van besturingssysteem schijf|Wachtrij diepte van de besturingssysteem schijf (preview-versie)|Count|Average|Wachtrij diepte van het besturings systeem (of wachtrij lengte)|None|
|Binnenkomende stromen|Binnenkomende stromen|Count|Average|Inkomende stromen zijn het aantal huidige stromen in de binnenkomende richting (verkeer dat wordt verzonden naar de virtuele machine)|None|
|Uitgaande stromen|Uitgaande stromen|Count|Average|Uitgaande stromen zijn het aantal huidige stromen in de uitgaande richting (verkeer dat uit de virtuele machine wordt verzonden)|None|
|Maximum aanmaak frequentie inkomende stromen|Maximum aantal aanmaak snelheden inkomende stromen (preview-versie)|CountPerSecond|Average|Het maximale aantal inkomende stromen (verkeer dat wordt verzonden naar de virtuele machine)|None|
|Maximum aanmaak frequentie van uitgaande stromen|Maximum aantal uitgaande stromen voor maken (preview)|CountPerSecond|Average|De maximale aanmaak frequentie van uitgaande stromen (verkeer dat uit de virtuele machine wordt verzonden)|None|
|Treffer voor Premium data-schijf cache lezen|Cache geheugen voor lezen van Premium-gegevens schijf (preview-versie)|Procent|Average|Treffer voor Premium data-schijf cache lezen|LUN|
|Lees missers cache Premium-gegevens schijf|Cache voor lezen van Premium-gegevens schijf (preview)|Procent|Average|Lees missers cache Premium-gegevens schijf|LUN|
|Treffer voor Premium-besturingssysteem schijf cache lezen|Treffer voor het lezen van een Premium-besturingssysteem schijf cache (preview-versie)|Procent|Average|Treffer voor Premium-besturingssysteem schijf cache lezen|None|
|Leesmij voor Premium-besturingssysteem schijf cache lezen|Schijf cache voor Premium-Lees-missers (preview-versie)|Procent|Average|Leesmij voor Premium-besturingssysteem schijf cache lezen|None|
|Totaal netwerk|Totaal netwerk|Bytes|Totaal|Het aantal ontvangen bytes op alle netwerk interfaces door de virtuele machine (s) (binnenkomend verkeer)|None|
|Totaal aantal netwerk|Totaal aantal netwerk|Bytes|Totaal|Het aantal uitgaande bytes op alle netwerk interfaces door de virtuele machine (s) (uitgaand verkeer)|None|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CpuUsage|CPU-gebruik|Count|Average|CPU-gebruik op alle kernen in millicores.|containerName|
|MemoryUsage|Geheugen gebruik|Bytes|Average|Totaal geheugen gebruik in bytes.|containerName|
|NetworkBytesReceivedPerSecond|Ontvangen netwerk bytes per seconde|Bytes|Average|Het netwerk ontvangen bytes per seconde.|None|
|NetworkBytesTransmittedPerSecond|Verzonden netwerk bytes per seconde|Bytes|Average|Het netwerk aantal verzonden bytes per seconde.|None|

## <a name="microsoftcontainerregistryregistries"></a>Micro soft. ContainerRegistry/registers

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TotalPullCount|Totaal aantal pull-bewerkingen|Count|Average|Aantal opgehaalde afbeeldingen in totaal|None|
|SuccessfulPullCount|Aantal geslaagde pull-bewerkingen|Count|Average|Aantal geslaagde installatie kopieën|None|
|TotalPushCount|Totaal aantal push berichten|Count|Average|Aantal afbeeldings pushes in totaal|None|
|SuccessfulPushCount|Aantal geslaagde push berichten|Count|Average|Aantal geslaagde pushes voor installatie kopie|None|
|RunDuration|Uitvoerings duur|Milliseconden|Totaal|Uitvoerings duur in milliseconden|None|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Totaal aantal beschik bare CPU-kernen in een beheerd cluster|Count|Average|Totaal aantal beschik bare CPU-kernen in een beheerd cluster|None|
|kube_node_status_allocatable_memory_bytes|Totale hoeveelheid beschikbaar geheugen in een beheerd cluster|Bytes|Average|Totale hoeveelheid beschikbaar geheugen in een beheerd cluster|None|
|kube_pod_status_ready|Aantal in de status gereed|Count|Average|Aantal in de status gereed|naam ruimte, pod|
|kube_node_status_condition|Statussen voor de verschillende knooppunt voorwaarden|Count|Average|Statussen voor de verschillende knooppunt voorwaarden|voor waarde, status, status2, knoop punt|
|kube_pod_status_phase|Aantal per fase|Count|Average|Aantal per fase|fase, naam ruimte, pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Micro soft. CustomProviders/resourceproviders

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SuccessfullRequests|Geslaagde aanvragen|Count|Totaal|Geslaagde aanvragen van de aangepaste provider|HttpMethod, CallPath, status code|
|FailedRequests|Mislukte aanvragen|Count|Totaal|Hiermee worden de beschik bare logboeken voor aangepaste resource providers opgehaald|HttpMethod, CallPath, status code|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|NICReadThroughput|Lees doorvoer (netwerk)|BytesPerSecond|Average|De Lees doorvoer van de netwerk interface op het apparaat in de rapportage periode voor alle volumes in de gateway.|InstanceName|
|NICWriteThroughput|Schrijf doorvoer (netwerk)|BytesPerSecond|Average|De schrijf doorvoer van de netwerk interface op het apparaat in de rapportage periode voor alle volumes in de gateway.|InstanceName|
|CloudReadThroughputPerShare|Door Voer van Cloud downloaden (delen)|BytesPerSecond|Average|De door Voer van downloaden naar Azure vanaf een share tijdens de rapportage periode.|Delen|
|CloudUploadThroughputPerShare|Upload doorvoer van Cloud (delen)|BytesPerSecond|Average|De upload doorvoer naar Azure vanaf een share tijdens de rapportage periode.|Delen|
|BytesUploadedToCloudPerShare|Geüploade Cloud bytes (delen)|Bytes|Average|Het totaal aantal bytes dat is geüpload naar Azure vanaf een share tijdens de rapportage periode.|Delen|
|TotalCapacity|Totale capaciteit|Bytes|Average|Totale capaciteit|None|
|Availablecapacity;)|Beschik bare capaciteit|Bytes|Average|De beschik bare capaciteit in bytes tijdens de rapportage periode.|None|
|CloudUploadThroughput|Upload doorvoer van Cloud|BytesPerSecond|Average|De upload doorvoer van de Cloud naar Azure tijdens de rapportage periode.|None|
|CloudReadThroughput|Door Voer van Cloud downloaden|BytesPerSecond|Average|De door Voer van de Cloud downloadt naar Azure tijdens de rapportage periode.|None|
|BytesUploadedToCloud|Geüploade Cloud bytes (apparaat)|Bytes|Average|Het totale aantal bytes dat tijdens de rapportage periode naar Azure wordt geüpload vanaf een apparaat.|None|
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
|PipelineCancelledRuns|Metrische gegevens van de pijplijn uitvoeringen geannuleerd|Count|Totaal||FailureType, naam|
|ActivityFailedRuns|Metrische gegevens mislukte uitvoering van activiteit|Count|Totaal||Activity type, PipelineName, FailureType, name|
|ActivitySucceededRuns|Metrische gegevens uitvoeringen uitgevoerde activiteit|Count|Totaal||Activity type, PipelineName, FailureType, name|
|ActivityCancelledRuns|Metrische gegevens voor geannuleerde activiteit uitgevoerd|Count|Totaal||Activity type, PipelineName, FailureType, name|
|TriggerFailedRuns|Meet waarden voor uitvoering van mislukte triggers|Count|Totaal||Naam, FailureType|
|TriggerSucceededRuns|Meet waarden voor uitvoering van geslaagde triggers|Count|Totaal||Naam, FailureType|
|TriggerCancelledRuns|Metrische gegevens over de trigger is geannuleerd|Count|Totaal||Naam, FailureType|
|IntegrationRuntimeCpuPercentage|CPU-gebruik van Integration runtime|Procent|Average||IntegrationRuntimeName, knooppuntnaam|
|IntegrationRuntimeAvailableMemory|Beschik bare geheugen voor Integration runtime|Bytes|Average||IntegrationRuntimeName, knooppuntnaam|
|IntegrationRuntimeAverageTaskPickupDelay|Duur van de wachtrij voor Integration runtime|Waarna|Average||IntegrationRuntimeName|
|IntegrationRuntimeQueueLength|Lengte van de wachtrij voor Integration runtime|Count|Average||IntegrationRuntimeName|
|IntegrationRuntimeAvailableNodeNumber|Aantal beschik bare knoop punten voor Integration runtime|Count|Average||IntegrationRuntimeName|
|MaxAllowedResourceCount|Maximum aantal toegestane entiteiten|Count|Maximum||None|
|MaxAllowedFactorySizeInGbUnits|Maxi maal toegestane grootte van de fabriek (GB-eenheid)|Count|Maximum||None|
|ResourceCount|Totaal aantal entiteiten|Count|Maximum||None|
|FactorySizeInGbUnits|Totale grootte van de fabriek (GB-eenheid)|Count|Maximum||None|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|JobEndedSuccess|Geslaagde taken|Count|Totaal|Aantal geslaagde taken.|None|
|JobEndedFailure|Mislukte taken|Count|Totaal|Aantal mislukte taken.|None|
|JobEndedCancelled|Geannuleerde taken|Count|Totaal|Aantal geannuleerde taken.|None|
|JobAUEndedSuccess|Geslaagde AU-tijd|Waarna|Totaal|Totale AU-tijd voor voltooide taken.|None|
|JobAUEndedFailure|Mislukte AU-tijd|Waarna|Totaal|Totale AU-tijd voor mislukte taken.|None|
|JobAUEndedCancelled|Geannuleerde AU-tijd|Waarna|Totaal|Totale AU-tijd voor geannuleerde taken.|None|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TotalStorage|Totale opslag|Bytes|Maximum|De totale hoeveelheid gegevens die in het account is opgeslagen.|None|
|DataWritten|Gegevens geschreven|Bytes|Totaal|De totale hoeveelheid gegevens die naar het account wordt geschreven.|None|
|DataRead|Gegevens lezen|Bytes|Totaal|De totale hoeveelheid gegevens die uit het account is gelezen.|None|
|WriteRequests|Aanvragen schrijven|Count|Totaal|Het aantal aanvragen voor het schrijven van gegevens naar het account.|None|
|ReadRequests|Aanvragen lezen|Count|Totaal|Aantal aanvragen voor het lezen van gegevens voor het account.|None|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Procent|Average|CPU-percentage|None|
|memory_percent|Geheugen percentage|Procent|Average|Geheugen percentage|None|
|io_consumption_percent|IO-percentage|Procent|Average|IO-percentage|None|
|storage_percent|Opslag percentage|Procent|Average|Opslag percentage|None|
|storage_used|Gebruikte opslag|Bytes|Average|Gebruikte opslag|None|
|storage_limit|Opslag limiet|Bytes|Maximum|Opslag limiet|None|
|serverlog_storage_percent|Percentage server logboek opslag|Procent|Average|Percentage server logboek opslag|None|
|serverlog_storage_usage|Gebruikte server logboek opslag|Bytes|Average|Gebruikte server logboek opslag|None|
|serverlog_storage_limit|Opslag limiet voor server logboek|Bytes|Average|Opslag limiet voor server logboek|None|
|active_connections|Actieve verbindingen|Count|Average|Actieve verbindingen|None|
|connections_failed|Mislukte verbindingen|Count|Totaal|Mislukte verbindingen|None|
|seconds_behind_master|Replicatie vertraging in seconden|Count|Maximum|Replicatie vertraging in seconden|None|
|backup_storage_used|Gebruikte back-upopslag|Bytes|Average|Gebruikte back-upopslag|None|
|network_bytes_egress|Netwerk uit|Bytes|Totaal|Netwerk uit over actieve verbindingen|None|
|network_bytes_ingress|Netwerk in|Bytes|Totaal|Netwerk in meerdere actieve verbindingen|None|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Procent|Average|CPU-percentage|None|
|memory_percent|Geheugen percentage|Procent|Average|Geheugen percentage|None|
|io_consumption_percent|IO-percentage|Procent|Average|IO-percentage|None|
|storage_percent|Opslag percentage|Procent|Average|Opslag percentage|None|
|storage_used|Gebruikte opslag|Bytes|Average|Gebruikte opslag|None|
|storage_limit|Opslag limiet|Bytes|Maximum|Opslag limiet|None|
|serverlog_storage_percent|Percentage server logboek opslag|Procent|Average|Percentage server logboek opslag|None|
|serverlog_storage_usage|Gebruikte server logboek opslag|Bytes|Average|Gebruikte server logboek opslag|None|
|serverlog_storage_limit|Opslag limiet voor server logboek|Bytes|Maximum|Opslag limiet voor server logboek|None|
|active_connections|Actieve verbindingen|Count|Average|Actieve verbindingen|None|
|connections_failed|Mislukte verbindingen|Count|Totaal|Mislukte verbindingen|None|
|seconds_behind_master|Replicatie vertraging in seconden|Count|Maximum|Replicatie vertraging in seconden|None|
|backup_storage_used|Gebruikte back-upopslag|Bytes|Average|Gebruikte back-upopslag|None|
|network_bytes_egress|Netwerk uit|Bytes|Totaal|Netwerk uit over actieve verbindingen|None|
|network_bytes_ingress|Netwerk in|Bytes|Totaal|Netwerk in meerdere actieve verbindingen|None|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Procent|Average|CPU-percentage|None|
|memory_percent|Geheugen percentage|Procent|Average|Geheugen percentage|None|
|io_consumption_percent|IO-percentage|Procent|Average|IO-percentage|None|
|storage_percent|Opslag percentage|Procent|Average|Opslag percentage|None|
|storage_used|Gebruikte opslag|Bytes|Average|Gebruikte opslag|None|
|storage_limit|Opslag limiet|Bytes|Maximum|Opslag limiet|None|
|serverlog_storage_percent|Percentage server logboek opslag|Procent|Average|Percentage server logboek opslag|None|
|serverlog_storage_usage|Gebruikte server logboek opslag|Bytes|Average|Gebruikte server logboek opslag|None|
|serverlog_storage_limit|Opslag limiet voor server logboek|Bytes|Maximum|Opslag limiet voor server logboek|None|
|active_connections|Actieve verbindingen|Count|Average|Actieve verbindingen|None|
|connections_failed|Mislukte verbindingen|Count|Totaal|Mislukte verbindingen|None|
|backup_storage_used|Gebruikte back-upopslag|Bytes|Average|Gebruikte back-upopslag|None|
|network_bytes_egress|Netwerk uit|Bytes|Totaal|Netwerk uit over actieve verbindingen|None|
|network_bytes_ingress|Netwerk in|Bytes|Totaal|Netwerk in meerdere actieve verbindingen|None|
|pg_replica_log_delay_in_seconds|Replica vertraging|Waarna|Maximum|Replica vertraging in seconden|None|
|pg_replica_log_delay_in_bytes|Maximale vertraging in Replica's|Bytes|Maximum|Vertraging in bytes van de meest vertraagde replica|None|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Procent|Average|CPU-percentage|None|
|memory_percent|Geheugen percentage|Procent|Average|Geheugen percentage|None|
|IOPS|IOPS|Count|Average|I/o-bewerkingen per seconde|None|
|storage_percent|Opslag percentage|Procent|Average|Opslag percentage|None|
|storage_used|Gebruikte opslag|Bytes|Average|Gebruikte opslag|None|
|active_connections|Actieve verbindingen|Count|Average|Actieve verbindingen|None|
|network_bytes_egress|Netwerk uit|Bytes|Totaal|Netwerk uit over actieve verbindingen|None|
|network_bytes_ingress|Netwerk in|Bytes|Totaal|Netwerk in meerdere actieve verbindingen|None|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Verzend pogingen voor telemetrie-berichten|Count|Totaal|Aantal pogingen voor het verzenden van apparaat-naar-Cloud-telemetrie naar uw IoT hub|None|
|d2c.telemetry.ingress.success|Verzonden telemetriegegevens|Count|Totaal|Aantal te verzenden apparaat-naar-Cloud-telemetrie-berichten naar uw IoT-hub|None|
|C2D. commands. OUTuitgang. complete. geslaagd|C2D-bericht leveringen voltooid|Count|Totaal|Aantal bezorgingen van Cloud-naar-apparaat-berichten voltooid door het apparaat|None|
|c2d.commands.egress.abandon.success|C2D-berichten zijn afgebroken|Count|Totaal|Aantal Cloud-naar-apparaat-berichten die zijn afgebroken door het apparaat|None|
|c2d.commands.egress.reject.success|Geweigerde C2D-berichten|Count|Totaal|Aantal Cloud-naar-apparaat-berichten dat door het apparaat is geweigerd|None|
|C2DMessagesExpired|C2D-berichten verlopen (preview-versie)|Count|Totaal|Aantal verlopen Cloud-naar-apparaat-berichten|None|
|devices.totalDevices|Totaal aantal apparaten (afgeschaft)|Count|Totaal|Aantal apparaten dat is geregistreerd bij uw IoT-hub|None|
|devices.connectedDevices.allProtocol|Verbonden apparaten (afgeschaft) |Count|Totaal|Aantal apparaten dat is verbonden met uw IoT-hub|None|
|d2c.telemetry.egress.success|Route ring: telemetrie-berichten|Count|Totaal|Het aantal keren dat berichten zijn bezorgd bij alle eind punten met behulp van IoT Hub route ring. Als een bericht wordt doorgestuurd naar meerdere eind punten, wordt deze waarde met één verhoogd voor elke geslaagde levering. Als een bericht meerdere keren op hetzelfde eind punt wordt bezorgd, wordt deze waarde met één verhoogd voor elke geslaagde levering.|None|
|d2c.telemetry.egress.dropped|Route ring: telemetrie-berichten verwijderd |Count|Totaal|Het aantal keren dat berichten zijn verwijderd door IoT Hub route ring vanwege Dead-eind punten. Deze waarde telt geen berichten die worden bezorgd als terugval route als berichten die worden verzonden, niet worden bezorgd.|None|
|d2c.telemetry.egress.orphaned|Route ring: telemetriegegevens van zwevende berichten |Count|Totaal|Het aantal keren dat berichten zijn zwevend door IoT Hub route ring, omdat ze niet overeenkomen met de regels voor door sturen (inclusief de terugval regel). |None|
|d2c.telemetry.egress.invalid|Route ring: telemetrie-berichten incompatibel|Count|Totaal|Het aantal keren dat IoT Hub route ring geen berichten kan leveren als gevolg van incompatibiliteit met het eind punt. Deze waarde omvat geen nieuwe pogingen.|None|
|d2c.telemetry.egress.fallback|Route ring: berichten worden bezorgd bij terugval|Count|Totaal|Het aantal keren dat de route ring van berichten IoT Hub verzonden naar het eind punt dat is gekoppeld aan de terugval route.|None|
|d2c.endpoints.egress.eventHubs|Route ring: berichten worden bezorgd bij Event hub|Count|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan Event hub-eind punten.|None|
|d2c.endpoints.latency.eventHubs|Route ring: bericht latentie voor Event hub|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten IoT Hub en het binnenkomen van berichten in een event hub-eind punt.|None|
|d2c.endpoints.egress.serviceBusQueues|Route ring: berichten worden bezorgd bij Service Bus wachtrij|Count|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan Service Bus-wachtrij-eind punten.|None|
|d2c.endpoints.latency.serviceBusQueues|Route ring: bericht latentie voor Service Bus wachtrij|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en telemetrie-berichten in een Service Bus wachtrij-eind punt.|None|
|d2c.endpoints.egress.serviceBusTopics|Route ring: berichten die worden bezorgd bij Service Bus onderwerp|Count|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan Service Bus onderwerp-eind punten.|None|
|d2c.endpoints.latency.serviceBusTopics|Route ring: bericht latentie voor Service Bus onderwerp|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en telemetrie-berichten in het eind punt van een Service Bus onderwerp.|None|
|D2C. endpoints. uitgangs punt. builtIn. Events|Route ring: berichten worden bezorgd bij berichten/gebeurtenissen|Count|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan het ingebouwde eind punt (berichten/gebeurtenissen).|None|
|d2c.endpoints.latency.builtIn.events|Route ring: bericht latentie voor berichten/gebeurtenissen|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en het inkomend telemetrie-bericht in het ingebouwde eind punt (berichten/gebeurtenissen).|None|
|d2c.endpoints.egress.storage|Route ring: berichten worden bezorgd bij de opslag|Count|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan de opslag eindpunten.|None|
|d2c.endpoints.latency.storage|Route ring: bericht latentie voor opslag|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en telemetrie-berichten in een opslag eindpunt.|None|
|D2C. endpoints. out. storage. bytes|Route ring: gegevens worden geleverd aan de opslag|Bytes|Totaal|De hoeveelheid gegevens (bytes) IoT Hub route ring die aan de opslag eindpunten wordt geleverd.|None|
|d2c.endpoints.egress.storage.blobs|Route ring: blobs die aan de opslag worden geleverd|Count|Totaal|Het aantal keren dat IoT Hub route ring blobs naar opslag eindpunten heeft geleverd.|None|
|EventGridDeliveries|Event Grid leveringen (preview-versie)|Count|Totaal|Het aantal IoT Hub gebeurtenissen dat is gepubliceerd op Event Grid. Gebruik de dimensie resultaat voor het aantal geslaagde en mislukte aanvragen. De dimensie type-tekst geeft het soort gebeurtenis weer (https://aka.ms/ioteventgrid).|ResourceId, resultaat, type gebeurtenis|
|EventGridLatency|Event Grid latentie (preview-versie)|Milliseconden|Average|De gemiddelde latentie (in milliseconden) vanaf het moment waarop de IOT hub-gebeurtenis werd gegenereerd toen de gebeurtenis werd gepubliceerd in Event Grid. Dit getal is een gemiddelde tussen alle gebeurtenis typen. Gebruik de dimensie type type om de latentie van een specifiek soort gebeurtenis weer te geven.|ResourceId, Event type|
|d2c.twin.read.success|Geslaagde dubbele Lees bewerkingen van apparaten|Count|Totaal|De telling van alle geslaagde apparaten met dubbele Lees bewerkingen.|None|
|D2C. dubbele. Read. failure|Mislukte dubbele Lees bewerkingen van apparaten|Count|Totaal|Het aantal apparaten dat niet kan worden gestart, dubbele Lees bewerkingen.|None|
|d2c.twin.read.size|Reactie grootte van dubbele Lees bewerkingen van apparaten|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde apparaten-geïnitieerde dubbele Lees bewerkingen.|None|
|d2c.twin.update.success|Geslaagde dubbele updates van apparaten|Count|Totaal|De telling van alle geslaagde, door het apparaat geïnitieerde dubbele updates.|None|
|D2C. dubbele. update. failure|Mislukte dubbele updates van apparaten|Count|Totaal|Het aantal apparaten dat door een apparaat is gestart en dubbele updates heeft uitgevoerd.|None|
|D2C. dubbele. update. grootte|Grootte van dubbele updates van apparaten|Bytes|Average|Het gemiddelde, het minimum en de maximale grootte van alle geslaagde, door het apparaat geïnitieerde dubbele updates.|None|
|C2D. methods. geslaagd|Geslaagde directe aanroepen van de methode|Count|Totaal|Het aantal voltooide direct-methode aanroepen.|None|
|C2D. methods. failure|Mislukte directe aanroepen van methode|Count|Totaal|Het aantal mislukte direct-methode aanroepen.|None|
|C2D. methods. requestSize|Aanvraag grootte van directe-methode aanroepen|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde direct-methode aanvragen.|None|
|C2D. methods. responseSize|Antwoord grootte van directe methode aanroepen|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde reacties van de methode direct.|None|
|c2d.twin.read.success|Geslaagde dubbele Lees bewerkingen van back-end|Count|Totaal|Het aantal geslaagde back-end-geïnitieerde dubbele Lees bewerkingen.|None|
|c2d.twin.read.failure|Mislukte dubbele Lees bewerkingen van back-end|Count|Totaal|Het aantal mislukte back-end-geïnitieerde dubbele Lees bewerkingen.|None|
|c2d.twin.read.size|Reactie grootte van dubbele Lees bewerkingen van de back-end|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde back-end-geïnitieerde dubbele Lees bewerkingen.|None|
|c2d.twin.update.success|Geslaagde dubbele updates van back-end|Count|Totaal|Het aantal geslaagde, door de back-end gestarte dubbele updates.|None|
|C2D. dubbele. update. failure|Mislukte dubbele updates van back-end|Count|Totaal|Het aantal niet-geslaagde, door de back-end geïnitieerde dubbele updates.|None|
|C2D. dubbele. update. grootte|Grootte van dubbele updates van back-end|Bytes|Average|Het gemiddelde, het minimum en de maximale grootte van alle geslaagde back-end-geïnitieerde dubbele updates.|None|
|twinQueries.success|Geslaagde dubbele query's|Count|Totaal|Het aantal geslaagde dubbele query's.|None|
|twinQueries.failure|Mislukte dubbele query's|Count|Totaal|Het aantal mislukte dubbele query's.|None|
|twinQueries.resultSize|Resultaat grootte van dubbele query's|Bytes|Average|Het gemiddelde, het minimum en het maximum van de resultaat grootte van alle geslaagde dubbele query's.|None|
|jobs.createTwinUpdateJob.success|Geslaagde creatie van dubbele update taken|Count|Totaal|Het aantal van alle geslaagde taken voor het maken van dubbele updates.|None|
|jobs.createTwinUpdateJob.failure|Kan geen dubbele update taken uitvoeren|Count|Totaal|Het aantal mislukte het maken van dubbele update taken.|None|
|jobs.createDirectMethodJob.success|Geslaagde creatie van methode aanroep taken|Count|Totaal|Het aantal van alle geslaagde aanroepen van directe methode aanroep taken.|None|
|jobs.createDirectMethodJob.failure|Kan geen aanroepen van methode aanroep taken uitvoeren|Count|Totaal|Het aantal van alle mislukte aanroepen van directe methode aanroep taken.|None|
|jobs.listJobs.success|Geslaagde aanroepen naar lijst taken|Count|Totaal|Het aantal geslaagde aanroepen naar lijst taken.|None|
|jobs.listJobs.failure|Mislukte aanroepen naar lijst taken|Count|Totaal|Het aantal mislukte aanroepen naar lijst taken.|None|
|jobs.cancelJob.success|Voltooide taak annuleringen|Count|Totaal|Het aantal geslaagde aanroepen om een taak te annuleren.|None|
|jobs.cancelJob.failure|Mislukte taak annuleringen|Count|Totaal|Het aantal mislukte aanroepen om een taak te annuleren.|None|
|jobs.queryJobs.success|Geslaagde taak query's|Count|Totaal|Het aantal geslaagde aanroepen naar query taken.|None|
|jobs.queryJobs.failure|Mislukte taak query's|Count|Totaal|Het aantal mislukte aanroepen naar query taken.|None|
|Jobs. voltooid|Voltooide taken|Count|Totaal|Het aantal voltooide taken.|None|
|Jobs. mislukt|Mislukte taken|Count|Totaal|Het aantal mislukte taken.|None|
|d2c.telemetry.ingress.sendThrottle|Aantal beperkings fouten|Count|Totaal|Aantal beperkings fouten door doorvoer vertraging van apparaat|None|
|dailyMessageQuotaUsed|Totaal aantal gebruikte berichten|Count|Average|Totaal aantal gebruikte berichten vandaag|None|
|deviceDataUsage|Totale hoeveelheid gegevens gebruik van apparaat|Bytes|Totaal|Verzonden bytes van en naar apparaten die zijn verbonden met IotHub|None|
|deviceDataUsageV2|Totaal gebruik van apparaatgegevens (preview-versie)|Bytes|Totaal|Verzonden bytes van en naar apparaten die zijn verbonden met IotHub|None|
|totalDeviceCount|Totaal aantal apparaten (preview-versie)|Count|Average|Aantal apparaten dat is geregistreerd bij uw IoT-hub|None|
|connectedDeviceCount|Verbonden apparaten (preview-versie)|Count|Average|Aantal apparaten dat is verbonden met uw IoT-hub|None|
|configuraties|Metrische configuratie gegevens|Count|Totaal|Metrische gegevens voor configuratie bewerkingen|None|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|RegistrationAttempts|Registratie pogingen|Count|Totaal|Aantal pogingen voor apparaatregistratie|ProvisioningServiceName, IotHubName, status|
|DeviceAssignments|Apparaten toegewezen|Count|Totaal|Aantal apparaten dat is toegewezen aan een IoT-hub|ProvisioningServiceName,IotHubName|
|AttestationAttempts|Attestation-pogingen|Count|Totaal|Aantal pogingen voor het uitvoeren van een apparaat|ProvisioningServiceName, status, protocol|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AddRegion|Regio toegevoegd|Count|Count|Regio toegevoegd|Regio|
|AvailableStorage|Beschikbare opslag|Bytes|Totaal|Totale beschik bare opslag gerapporteerd bij een granulatie van 5 minuten|Verzamelingnaam, databasenaam, regio|
|CassandraConnectionClosures|Cassandra-verbinding sluiten|Count|Totaal|Aantal Cassandra-verbindingen dat is gesloten, gerapporteerd met een granulatie van 1 minuut|APIType, regio, ClosureReason|
|CassandraRequestCharges|Kosten voor Cassandra-aanvragen|Count|Totaal|RUs gebruikt voor Cassandra-aanvragen|APIType, DATABASENAME, verzamelingnaam, regio, OperationType, resource type|
|CassandraRequests|Cassandra aanvragen|Count|Count|Aantal gemaakte Cassandra-aanvragen|APIType, DATABASENAME, verzamelingnaam, regio, OperationType, resource type, error code|
|CreateAccount|Account gemaakt|Count|Count|Account gemaakt|None|
|DataUsage|Gegevensgebruik|Bytes|Totaal|Totaal gegevens gebruik gerapporteerd bij 5 minuten granulariteit|Verzamelingnaam, databasenaam, regio|
|DeleteAccount|Account is verwijderd|Count|Count|Account is verwijderd|None|
|DocumentCount|Aantal documenten|Count|Totaal|Totaal aantal documenten gemeld bij 5 minuten granulariteit|Verzamelingnaam, databasenaam, regio|
|DocumentQuota|Document quotum|Bytes|Totaal|Totale opslag quotum gerapporteerd bij een granulatie van 5 minuten|Verzamelingnaam, databasenaam, regio|
|IndexUsage|Index gebruik|Bytes|Totaal|Totaal gebruik van index gerapporteerd bij een granulatie van 5 minuten|Verzamelingnaam, databasenaam, regio|
|MetadataRequests|Meta gegevens aanvragen|Count|Count|Aantal aanvragen voor meta gegevens. Cosmos DB onderhoudt de verzameling van meta gegevens van het systeem voor elk account, waarmee u verzamelingen, data bases, enzovoort en hun configuraties gratis kunt inventariseren.|DATABASENAME, verzamel-, regio, status code, rol|
|MongoRequestCharge|Kosten voor Mongo-aanvragen|Count|Totaal|Verbruikte Mongo-aanvraag eenheden|DATABASENAME, verzamel-, regio, opdrachtnaam, error code|
|MongoRequests|Mongo aanvragen|Count|Count|Aantal gemaakte Mongo-aanvragen|DATABASENAME, verzamel-, regio, opdrachtnaam, error code|
|MongoRequestsCount|Frequentie van Mongo-aanvragen|CountPerSecond|Average|Aantal Mongo per seconde|DATABASENAME, verzamel-, regio, opdrachtnaam, error code|
|MongoRequestsDelete|Aantal Mongo-aanvragen voor verwijderen|CountPerSecond|Average|Mongo-aanvraag voor verwijderen per seconde|DATABASENAME, verzamel-, regio, opdrachtnaam, error code|
|MongoRequestsInsert|Aantal Mongo invoegen|CountPerSecond|Average|Aantal Mongo per seconde|DATABASENAME, verzamel-, regio, opdrachtnaam, error code|
|MongoRequestsQuery|Frequentie van Mongo-query aanvragen|CountPerSecond|Average|Mongo-query aanvraag per seconde|DATABASENAME, verzamel-, regio, opdrachtnaam, error code|
|MongoRequestsUpdate|Frequentie van Mongo-update aanvragen|CountPerSecond|Average|Aanvraag voor Mongo-update per seconde|DATABASENAME, verzamel-, regio, opdrachtnaam, error code|
|ProvisionedThroughput|Ingerichte doorvoer|Count|Maximum|Ingerichte doorvoer|DATABASENAME, verzamelnaam|
|RegionFailover|Er is een failover uitgevoerd voor de regio|Count|Count|Er is een failover uitgevoerd voor de regio|None|
|RemoveRegion|Regio is verwijderd|Count|Count|Regio is verwijderd|Regio|
|ReplicationLatency|P99-replicatie latentie|MilliSeconds|Average|Replicatie latentie van P99 voor de bron-en doel regio's voor geografisch ingeschakelde account|SourceRegion,TargetRegion|
|ServiceAvailability|Service beschikbaarheid|Procent|Average|Beschik baarheid van account aanvragen op één uur, dag of maand granulatie|None|
|TotalRequestUnits|Totaal aantal aanvraag eenheden|Count|Totaal|Verbruikte aanvraag eenheden|DATABASENAME, verzamel-, regio, status code, OperationType|
|TotalRequests|Totaal aantal aanvragen|Count|Count|Aantal aanvragen dat is gedaan|DATABASENAME, verzamel-, regio, status code, OperationType|
|UpdateAccountKeys|Bijgewerkte account sleutels|Count|Count|Bijgewerkte account sleutels|KeyType|
|UpdateAccountNetworkSettings|De netwerk instellingen voor het account zijn bijgewerkt|Count|Count|De netwerk instellingen voor het account zijn bijgewerkt|None|
|UpdateAccountReplicationSettings|De replicatie-instellingen voor het account zijn bijgewerkt|Count|Count|De replicatie-instellingen voor het account zijn bijgewerkt|None|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Micro soft. EnterpriseKnowledgeGraph/Services

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TransactionCount|Aantal trans acties|Count|Count|Totaal aantal trans acties|TransactionCount|
|SuccessCount|Aantal geslaagde pogingen|Count|Count|Aantal geslaagde trans acties|SuccessCount|
|FailureCount|Aantal fouten|Count|Count|Aantal mislukte trans acties|FailureCount|
|SuccessLatency|Geslaagde latentie|MilliSeconds|Average|Latentie van geslaagde trans acties|SuccessCount|

## <a name="microsofteventgriddomains"></a>Micro soft. EventGrid/domeinen

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PublishSuccessCount|Gepubliceerde gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat naar dit onderwerp is gepubliceerd|Onderwerp|
|PublishFailCount|Mislukte gebeurtenissen publiceren|Count|Totaal|Totaal aantal gebeurtenissen dat niet naar dit onderwerp kan worden gepubliceerd|Onderwerp, error type, fout|
|PublishSuccessLatencyInMs|Latentie van publicatie geslaagd|Milliseconden|Totaal|Latentie van geslaagde publicatie in milliseconden|None|
|MatchedEventCount|Overeenkomende gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName|
|DeliveryAttemptFailCount|Mislukte leverings gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat niet aan dit gebeurtenis abonnement kan worden geleverd|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName, fout, error type|
|DeliverySuccessCount|Geleverde gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat aan dit gebeurtenis abonnement is geleverd|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Doel verwerkings duur|Milliseconden|Average|Doel verwerkings duur in milliseconden|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Verwijderde gebeurtenissen|Count|Totaal|Totaal aantal verloren gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|DeadLetteredCount|Gebeurtenissen met onbestelbare berichten|Count|Totaal|Totaal aantal gebeurtenissen met onbestelbare berichten die overeenkomen met dit gebeurtenis abonnement|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Micro soft. EventGrid/topics

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PublishSuccessCount|Gepubliceerde gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat naar dit onderwerp is gepubliceerd|None|
|PublishFailCount|Mislukte gebeurtenissen publiceren|Count|Totaal|Totaal aantal gebeurtenissen dat niet naar dit onderwerp kan worden gepubliceerd|Error type, fout|
|UnmatchedEventCount|Niet-overeenkomende gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat niet overeenkomt met een van de gebeurtenis abonnementen voor dit onderwerp|None|
|PublishSuccessLatencyInMs|Latentie van publicatie geslaagd|Milliseconden|Totaal|Latentie van geslaagde publicatie in milliseconden|None|
|MatchedEventCount|Overeenkomende gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|EventSubscriptionName|
|DeliveryAttemptFailCount|Mislukte leverings gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat niet aan dit gebeurtenis abonnement kan worden geleverd|Fout, error type, EventSubscriptionName|
|DeliverySuccessCount|Geleverde gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat aan dit gebeurtenis abonnement is geleverd|EventSubscriptionName|
|DestinationProcessingDurationInMs|Doel verwerkings duur|Milliseconden|Average|Doel verwerkings duur in milliseconden|EventSubscriptionName|
|DroppedEventCount|Verwijderde gebeurtenissen|Count|Totaal|Totaal aantal verloren gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|DropReason,EventSubscriptionName|
|DeadLetteredCount|Gebeurtenissen met onbestelbare berichten|Count|Totaal|Totaal aantal gebeurtenissen met onbestelbare berichten die overeenkomen met dit gebeurtenis abonnement|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|MatchedEventCount|Overeenkomende gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|None|
|DeliveryAttemptFailCount|Mislukte leverings gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat niet aan dit gebeurtenis abonnement kan worden geleverd|Fout, error type|
|DeliverySuccessCount|Geleverde gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat aan dit gebeurtenis abonnement is geleverd|None|
|DestinationProcessingDurationInMs|Doel verwerkings duur|Milliseconden|Average|Doel verwerkings duur in milliseconden|None|
|DroppedEventCount|Verwijderde gebeurtenissen|Count|Totaal|Totaal aantal verloren gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|DropReason|
|DeadLetteredCount|Gebeurtenissen met onbestelbare berichten|Count|Totaal|Totaal aantal gebeurtenissen met onbestelbare berichten die overeenkomen met dit gebeurtenis abonnement|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PublishSuccessCount|Gepubliceerde gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat naar dit onderwerp is gepubliceerd|None|
|PublishFailCount|Mislukte gebeurtenissen publiceren|Count|Totaal|Totaal aantal gebeurtenissen dat niet naar dit onderwerp kan worden gepubliceerd|Error type, fout|
|UnmatchedEventCount|Niet-overeenkomende gebeurtenissen|Count|Totaal|Totaal aantal gebeurtenissen dat niet overeenkomt met een van de gebeurtenis abonnementen voor dit onderwerp|None|
|PublishSuccessLatencyInMs|Latentie van publicatie geslaagd|Milliseconden|Totaal|Latentie van geslaagde publicatie in milliseconden|None|



## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SuccessfulRequests|Geslaagde aanvragen|Count|Totaal|Geslaagde aanvragen voor micro soft. EventHub.|EntityName, kan operationresult niet|
|ServerErrors|Server fouten.|Count|Totaal|Server fouten voor micro soft. EventHub.|EntityName, kan operationresult niet|
|UserErrors|Gebruikers fouten.|Count|Totaal|Gebruikers fouten voor micro soft. EventHub.|EntityName, kan operationresult niet|
|QuotaExceededErrors|Quota overschreden fouten.|Count|Totaal|Quotum overschrijdt fouten voor micro soft. EventHub.|EntityName, kan operationresult niet|
|ThrottledRequests|Vertraagde aanvragen.|Count|Totaal|Beperkte aanvragen voor micro soft. EventHub.|EntityName, kan operationresult niet|
|IncomingRequests|Binnenkomende aanvragen|Count|Totaal|Binnenkomende aanvragen voor micro soft. EventHub.|EntityName|
|IncomingMessages|Inkomende berichten|Count|Totaal|Binnenkomende berichten voor micro soft. EventHub.|EntityName|
|OutgoingMessages|Uitgaande berichten|Count|Totaal|Uitgaande berichten voor micro soft. EventHub.|EntityName|
|IncomingBytes|Binnenkomende bytes.|Bytes|Totaal|Binnenkomende bytes voor micro soft. EventHub.|EntityName|
|OutgoingBytes|Uitgaande bytes.|Bytes|Totaal|Uitgaande bytes voor micro soft. EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Count|Average|Totaal aantal actieve verbindingen voor micro soft. EventHub.|None|
|ConnectionsOpened|Geopende verbindingen.|Count|Average|Geopende verbindingen voor micro soft. EventHub.|EntityName|
|ConnectionsClosed|Verbindingen gesloten.|Count|Average|Gesloten verbindingen voor micro soft. EventHub.|EntityName|
|CaptureBacklog|Achterstand vastleggen.|Count|Totaal|Achterstand vastleggen voor micro soft. EventHub.|EntityName|
|CapturedMessages|Vastgelegde berichten.|Count|Totaal|Vastgelegde berichten voor micro soft. EventHub.|EntityName|
|CapturedBytes|Vastgelegde bytes.|Bytes|Totaal|Vastgelegde bytes voor micro soft. EventHub.|EntityName|
|Grootte|Grootte|Bytes|Average|Grootte van een EventHub in bytes.|EntityName|
|INREQS|Binnenkomende aanvragen (afgeschaft)|Count|Totaal|Totaal aantal binnenkomende verzend aanvragen voor een naam ruimte (afgeschaft)|None|
|SUCCREQ|Geslaagde aanvragen (afgeschaft)|Count|Totaal|Totaal aantal geslaagde aanvragen voor een naam ruimte (afgeschaft)|None|
|FAILREQ|Mislukte aanvragen (afgeschaft)|Count|Totaal|Totaal aantal mislukte aanvragen voor een naam ruimte (afgeschaft)|None|
|SVRBSY|Fouten bij server bezet (afgeschaft)|Count|Totaal|Totaal aantal fouten bij server bezet voor een naam ruimte (afgeschaft)|None|
|INTERer|Interne server fouten (afgeschaft)|Count|Totaal|Totaal aantal interne server fouten voor een naam ruimte (afgeschaft)|None|
|MISCERR|Andere fouten (afgeschaft)|Count|Totaal|Totaal aantal mislukte aanvragen voor een naam ruimte (afgeschaft)|None|
|INMSGS|Binnenkomende berichten (verouderd) (afgeschaft)|Count|Totaal|Totaal aantal inkomende berichten voor een naam ruimte. Deze metriek is afgeschaft. Gebruik in plaats daarvan de metriek van binnenkomende berichten (afgeschaft)|None|
|EHINMSGS|Inkomende berichten (afgeschaft)|Count|Totaal|Totaal aantal inkomende berichten voor een naam ruimte (afgeschaft)|None|
|OUTMSGS|Uitgaande berichten (verouderd) (afgeschaft)|Count|Totaal|Totaal aantal uitgaande berichten voor een naam ruimte. Deze metriek is afgeschaft. Gebruik in plaats daarvan de metrische gegevens van uitgaande berichten (afgeschaft)|None|
|EHOUTMSGS|Uitgaande berichten (afgeschaft)|Count|Totaal|Totaal aantal uitgaande berichten voor een naam ruimte (afgeschaft)|None|
|EHINMBS|Binnenkomende bytes (verouderd) (afgeschaft)|Bytes|Totaal|Door Voer van binnenkomende berichten van Event hub voor een naam ruimte. Deze metriek is afgeschaft. Gebruik in plaats daarvan de metrische gegevens over binnenkomende bytes (afgeschaft)|None|
|EHINBYTES|Binnenkomende bytes (afgeschaft)|Bytes|Totaal|Door Voer van inkomende berichten van Event hub voor een naam ruimte (afgeschaft)|None|
|EHOUTMBS|Uitgaande bytes (verouderd) (afgeschaft)|Bytes|Totaal|Door Voer van Event hub voor een naam ruimte. Deze metriek is afgeschaft. Gebruik in plaats hiervan de metrische gegevens van uitgaande bytes (afgeschaft)|None|
|EHOUTBYTES|Uitgaande bytes (afgeschaft)|Bytes|Totaal|Door Voer van Event hub voor een naam ruimte (afgeschaft)|None|
|EHABL|Achterstallige berichten archiveren (afgeschaft)|Count|Totaal|Event hub-archief berichten in achterstand voor een naam ruimte (afgeschaft)|None|
|EHAMSGS|Berichten archiveren (afgeschaft)|Count|Totaal|Event hub heeft berichten gearchiveerd in een naam ruimte (afgeschaft)|None|
|EHAMBS|Berichten doorvoer archiveren (afgeschaft)|Bytes|Totaal|Door Voer van Event hub-bericht doorvoer in een naam ruimte (afgeschaft)|None|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SuccessfulRequests|Geslaagde aanvragen|Count|Totaal|Geslaagde aanvragen voor micro soft. EventHub.|Kan operationresult niet|
|ServerErrors|Server fouten.|Count|Totaal|Server fouten voor micro soft. EventHub.|Kan operationresult niet|
|UserErrors|Gebruikers fouten.|Count|Totaal|Gebruikers fouten voor micro soft. EventHub.|Kan operationresult niet|
|QuotaExceededErrors|Quota overschreden fouten.|Count|Totaal|Quotum overschrijdt fouten voor micro soft. EventHub.|Kan operationresult niet|
|ThrottledRequests|Vertraagde aanvragen.|Count|Totaal|Beperkte aanvragen voor micro soft. EventHub.|Kan operationresult niet|
|IncomingRequests|Binnenkomende aanvragen|Count|Totaal|Binnenkomende aanvragen voor micro soft. EventHub.|None|
|IncomingMessages|Inkomende berichten|Count|Totaal|Binnenkomende berichten voor micro soft. EventHub.|None|
|OutgoingMessages|Uitgaande berichten|Count|Totaal|Uitgaande berichten voor micro soft. EventHub.|None|
|IncomingBytes|Binnenkomende bytes.|Bytes|Totaal|Binnenkomende bytes voor micro soft. EventHub.|None|
|OutgoingBytes|Uitgaande bytes.|Bytes|Totaal|Uitgaande bytes voor micro soft. EventHub.|None|
|ActiveConnections|ActiveConnections|Count|Average|Totaal aantal actieve verbindingen voor micro soft. EventHub.|None|
|ConnectionsOpened|Geopende verbindingen.|Count|Average|Geopende verbindingen voor micro soft. EventHub.|None|
|ConnectionsClosed|Verbindingen gesloten.|Count|Average|Gesloten verbindingen voor micro soft. EventHub.|None|
|CaptureBacklog|Achterstand vastleggen.|Count|Totaal|Achterstand vastleggen voor micro soft. EventHub.|None|
|CapturedMessages|Vastgelegde berichten.|Count|Totaal|Vastgelegde berichten voor micro soft. EventHub.|None|
|CapturedBytes|Vastgelegde bytes.|Bytes|Totaal|Vastgelegde bytes voor micro soft. EventHub.|None|
|CPU|CPU|Procent|Maximum|CPU-gebruik voor het event hub-cluster als een percentage|Rol|
|AvailableMemory|Beschikbaar geheugen|Procent|Maximum|Beschikbaar geheugen voor het event hub-cluster als percentage van het totale geheugen.|Rol|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|GatewayRequests|Gateway aanvragen|Count|Totaal|Aantal gateway-aanvragen|HttpStatus|
|CategorizedGatewayRequests|Gecategoriseerde gateway aanvragen|Count|Totaal|Aantal gateway aanvragen per categorie (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|NumActiveWorkers|Aantal actieve werk rollen|Count|Maximum|Aantal actieve werk rollen|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/AutoscaleSettings

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ObservedMetricValue|Waargenomen metrische waarde|Count|Average|De waarde die wordt berekend door automatisch schalen wanneer deze wordt uitgevoerd|MetricTriggerSource|
|MetricThreshold|Drempel waarde voor metrische gegevens|Count|Average|De geconfigureerde drempel waarde voor automatisch schalen wanneer automatisch schalen is uitgevoerd.|MetricTriggerRule|
|ObservedCapacity|Waargenomen capaciteit|Count|Average|De capaciteit die is gerapporteerd voor automatisch schalen wanneer deze wordt uitgevoerd.|None|
|ScaleActionsInitiated|Schaal acties gestart|Count|Totaal|De richting van de schaal bewerking.|ScaleDirection|




## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Beschikbaarheid|Procent|Average|Percentage voltooide beschikbaarheids tests|availabilityResult/naam, availabilityResult/locatie|
|availabilityResults/aantal|Beschikbaarheidstests|Count|Count|Aantal beschikbaarheids tests|availabilityResult/naam, availabilityResult/locatie, availabilityResult/geslaagd|
|availabilityResults/duur|Duur beschikbaarheids test|MilliSeconds|Average|Duur beschikbaarheids test|availabilityResult/naam, availabilityResult/locatie, availabilityResult/geslaagd|
|browserTimings/networkDuration|Netwerk verbindings tijd voor laden van pagina|MilliSeconds|Average|Tijd tussen de gebruikers aanvraag en de netwerk verbinding. Inclusief DNS-Zoek-en transport verbinding.|None|
|browserTimings/processingDuration|Verwerkings tijd van client|MilliSeconds|Average|Tijd tussen het ontvangen van de laatste byte van een document totdat de DOM is geladen. Asynchrone aanvragen kunnen nog steeds worden verwerkt.|None|
|browserTimings/receiveDuration|Reactie tijd van ontvangst|MilliSeconds|Average|Tijd tussen de eerste en laatste bytes, of tot de verbinding wordt verbroken.|None|
|browserTimings/sendDuration|Aanvraag tijd verzenden|MilliSeconds|Average|Tijd tussen netwerk verbinding en ontvangst van de eerste byte.|None|
|browserTimings/totalDuration|Laad tijd van browser pagina|MilliSeconds|Average|Tijd van de gebruikers aanvraag totdat DOM, opmaak modellen, scripts en installatie kopieën worden geladen.|None|
|afhankelijkheden/aantal|Afhankelijkheids aanroepen|Count|Count|Aantal aanroepen van de toepassing naar externe bronnen.|afhankelijkheid/type, afhankelijkheid/requests, afhankelijkheid/geslaagd, afhankelijkheid/doel, bewerking/synthetisch, Cloud/roleInstance, Cloud/rolnaam|
|afhankelijkheden/duur|Duur van afhankelijkheid|MilliSeconds|Average|Duur van de aanroepen van de toepassing naar externe bronnen.|afhankelijkheid/type, afhankelijkheid/requests, afhankelijkheid/geslaagd, afhankelijkheid/doel, bewerking/synthetisch, Cloud/roleInstance, Cloud/rolnaam|
|afhankelijkheden/mislukt|Mislukte afhankelijkheids aanroepen|Count|Count|Aantal mislukte afhankelijkheids aanroepen van de toepassing naar externe bronnen.|afhankelijkheid/type, afhankelijkheid/requests, afhankelijkheid/geslaagd, afhankelijkheid/doel, bewerking/synthetisch, Cloud/roleInstance, Cloud/rolnaam|
|Page views/aantal|Pagina weergaven|Count|Count|Aantal pagina weergaven.|bewerking/synthetisch, Cloud/rolnaam|
|Page views/duur|Laad tijd pagina weergave|MilliSeconds|Average|Laad tijd pagina weergave|bewerking/synthetisch, Cloud/rolnaam|
|performanceCounters/requestExecutionTime|Uitvoerings tijd van de HTTP-aanvraag|MilliSeconds|Average|Uitvoerings tijd van de meest recente aanvraag.|Cloud-roleInstance|
|Performance Counters/requestsInQueue|HTTP-aanvragen in de toepassings wachtrij|Count|Average|Lengte van de wachtrij voor toepassings aanvragen.|Cloud-roleInstance|
|performanceCounters/requestsPerSecond|Frequentie van HTTP-aanvragen|CountPerSecond|Average|Het aantal aanvragen voor de toepassing per seconde van ASP.NET.|Cloud-roleInstance|
|performanceCounters/exceptionsPerSecond|Uitzonderings frequentie|CountPerSecond|Average|Aantal verwerkte en onverwerkte uitzonde ringen die worden gerapporteerd aan Windows, inclusief .NET-uitzonde ringen en onbeheerde uitzonde ringen die worden geconverteerd naar .NET-uitzonde ringen.|Cloud-roleInstance|
|performanceCounters/processIOBytesPerSecond|I/o-frequentie van processen|BytesPerSecond|Average|Totaal aantal in bestanden, netwerk en apparaten gelezen en geschreven bytes per seconde.|Cloud-roleInstance|
|performanceCounters/processCpuPercentage|CPU verwerken|Procent|Average|Het percentage van de verstreken tijd dat alle proces threads de processor hebben gebruikt om instructies uit te voeren. Dit kan variëren tussen 0 en 100. Deze metrische gegevens duiden de prestaties van alleen het W3wp-proces aan.|Cloud-roleInstance|
|performanceCounters/processorCpuPercentage|Processor tijd|Procent|Average|Het percentage tijd dat de processor spendeert aan niet-inactieve threads.|Cloud-roleInstance|
|performanceCounters/memoryAvailableBytes|Beschikbaar geheugen|Bytes|Average|Fysiek geheugen dat direct beschikbaar is voor toewijzing aan een proces of voor systeem gebruik.|Cloud-roleInstance|
|performanceCounters/processPrivateBytes|Privé-bytes verwerken|Bytes|Average|Geheugen dat exclusief wordt toegewezen aan de processen van de bewaakte toepassing.|Cloud-roleInstance|
|aanvragen/duur|Serverreactietijd|MilliSeconds|Average|Tijd tussen het ontvangen van een HTTP-aanvraag en het volt ooien van het verzenden van het antwoord.|aanvraag-requests, aanvraag-resultCode, bewerking/synthetisch, Cloud/roleInstance, aanvraag/geslaagd, Cloud/rolnaam|
|aanvragen/aantal|Server aanvragen|Count|Count|Aantal voltooide HTTP-aanvragen.|aanvraag-requests, aanvraag-resultCode, bewerking/synthetisch, Cloud/roleInstance, aanvraag/geslaagd, Cloud/rolnaam|
|aanvragen/mislukt|Mislukte aanvragen|Count|Count|Het aantal HTTP-aanvragen dat is gemarkeerd als mislukt. In de meeste gevallen zijn dit aanvragen met een antwoord code > = 400 en niet gelijk is aan 401.|aanvraag-requests, aanvraag-resultCode, aanvraag/geslaagd, bewerking/synthetisch, Cloud/roleInstance, Cloud/rolnaam|
|aanvragen/frequentie|Aantal server aanvragen|CountPerSecond|Average|Aantal server aanvragen per seconde|aanvraag-requests, aanvraag-resultCode, bewerking/synthetisch, Cloud/roleInstance, aanvraag/geslaagd, Cloud/rolnaam|
|uitzonde ringen/aantal|Uitzonderingen|Count|Count|Totaal aantal niet-onderschepte uitzonde ringen.|Cloud/rolnaam, Cloud/roleInstance, client/type|
|uitzonde ringen/browser|Browseruitzonderingen|Count|Count|Aantal niet-onderschepte uitzonde ringen dat in de browser wordt gegenereerd.|client-isServer, Cloud/rolnaam|
|uitzonde ringen/server|Server uitzonderingen|Count|Count|Aantal niet-onderschepte uitzonde ringen dat is opgetreden in de server toepassing.|client-isServer, Cloud/rolnaam, Cloud/roleInstance|
|traceringen/aantal|Traceringen|Count|Count|Aantal tracerings documenten|Trace/severityLevel, Operation/synthetisch, Cloud/rolnaam, Cloud/roleInstance|



## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ServiceApiHit|Totaal aantal treffers in de service-API|Count|Count|Totaal aantal treffers in de service-API|Activity type, Activiteitsnummer|
|ServiceApiLatency|Algehele latentie van Service-API|Milliseconden|Average|Algemene latentie van Service-API-aanvragen|Activity type, Activiteitsnummer, status code, StatusCodeClass|
|ServiceApiResult|Totale resultaten van Service-API|Count|Count|Totaal aantal resultaten van Service-API|Activity type, Activiteitsnummer, status code, StatusCodeClass|
|SaturationShoebox|Algehele intensiteit van de kluis|Procent|Average|Gebruikte kluis capaciteit|Activity type, Activiteitsnummer, TransactionType|
|Beschikbaarheid|Algemene Beschik baarheid van kluis|Procent|Average|Beschik baarheid van kluis aanvragen|Activity type, Activiteitsnummer, status code, StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CacheUtilization|Cache gebruik|Procent|Average|Gebruiks niveau in het cluster bereik|None|
|QueryDuration|Queryduur|Milliseconden|Average|De duur van query's in seconden|QueryStatus|
|IngestionUtilization|Opname gebruik|Procent|Average|Verhouding van gebruikte opname sleuven in het cluster|None|
|KeepAlive|Actief houden|Count|Average|Sanity-controle geeft aan dat het cluster reageert op query's|None|
|IngestionVolumeInMB|Opname volume (in MB)|Count|Totaal|Totaal volume van opgenomen gegevens aan het cluster (in MB)|Database|
|IngestionLatencyInSeconds|Opname latentie (in seconden)|Waarna|Average|Opname tijd van de bron (bijvoorbeeld bericht is in EventHub) naar het cluster in enkele seconden|None|
|EventsProcessedForEventHubs|Verwerkte gebeurtenissen (voor gebeurtenis/IoT-hubs)|Count|Totaal|Aantal gebeurtenissen dat door het cluster wordt verwerkt bij het opnemen van gebeurtenis/IoT Hub|EventStatus|
|IngestionResult|Opname resultaat|Count|Count|Aantal opname bewerkingen|IngestionResultDetails|
|CPU|CPU|Procent|Average|Niveau CPU-gebruik|None|
|ContinuousExportNumOfRecordsExported|Doorlopend exporteren: aantal geëxporteerde records|Count|Totaal|Het aantal geëxporteerde records dat wordt geactiveerd voor elk opslag artefact dat is geschreven tijdens de export bewerking|None|
|ExportUtilization|Gebruik exporteren|Procent|Maximum|Gebruik exporteren|None|
|ContinuousExportPendingCount|Aantal doorlopend exporteren in behandeling|Count|Maximum|Het aantal in behandeling zijnde doorlopende export taken dat gereed is voor uitvoering|None|
|ContinuousExportMaxLatenessMinutes|Maximale achterstand voor continue export|Count|Maximum|De maximale achterstand in minuten van alle doorlopende exports in behandeling en gereed voor uitvoering|None|
|ContinuousExportResult|Resultaat doorlopend exporteren|Count|Count|Hiermee wordt aangegeven of continue export is geslaagd of mislukt|ContinuousExportName, resultaat, data base|
|StreamingIngestDuration|Opname duur van streaming|Milliseconden|Average|De duur van het opnemen van gegevens stromen in milliseconden|None|
|StreamingIngestDataRate|Gegevens frequentie van streaming opname|Count|Average|Gegevens frequentie van streaming-opname (MB per seconde)|None|
|SteamingIngestRequestRate|Aanvraag frequentie voor streaming-opname|Count|RateRequestsPerSecond|Aanvraag frequentie voor streaming-opname (aanvragen per seconde)|None|
|StreamingIngestResults|Resultaat van streaming-opname|Count|Average|Resultaat van streaming-opname|Resultaat|
|TotalNumberOfConcurrentQueries|Totaal aantal gelijktijdige query's|Count|Totaal|Totaal aantal gelijktijdige query's|None|
|TotalNumberOfThrottledQueries|Totaal aantal vertraagde query's|Count|Totaal|Totaal aantal vertraagde query's|None|
|TotalNumberOfThrottledCommands|Totaal aantal vertraagde opdrachten|Count|Totaal|Totaal aantal vertraagde opdrachten|CommandType|
|TotalNumberOfExtents|Totaal aantal gebieden|Count|Totaal|Totaal aantal gegevens gebieden|None|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|RunsStarted|Uitvoeringen gestart|Count|Totaal|Aantal uitvoeringen van werk stroom gestart.|None|
|RunsCompleted|Uitvoeringen voltooid|Count|Totaal|Aantal voltooide werk stroom uitvoeringen.|None|
|RunsSucceeded|Geslaagde uitvoeringen|Count|Totaal|Aantal geslaagde werk stroom uitvoeringen.|None|
|RunsFailed|Uitvoeringen mislukt|Count|Totaal|Het aantal uitvoeringen van de werk stroom is mislukt.|None|
|RunsCancelled|Geannuleerde uitvoeringen|Count|Totaal|Aantal uitgevoerde werk stroom uitvoeringen geannuleerd.|None|
|RunLatency|Uitvoerings latentie|Waarna|Average|Latentie van voltooide werk stroom uitvoeringen.|None|
|RunSuccessLatency|Latentie van geslaagde uitvoering|Waarna|Average|Latentie van geslaagde werk stroom uitvoeringen.|None|
|RunThrottledEvents|Vertraagde gebeurtenissen uitvoeren|Count|Totaal|Aantal werk stroom acties of trigger vertraagde gebeurtenissen.|None|
|RunStartThrottledEvents|Vertraagde gebeurtenissen uitvoeren|Count|Totaal|Aantal uitgevoerde vertraagde gebeurtenissen voor de werk stroom.|None|
|RunFailurePercentage|Percentage mislukte uitvoeringen|Procent|Totaal|Percentage mislukte werk stroom uitvoeringen.|None|
|ActionsStarted|Gestarte acties |Count|Totaal|Aantal gestarte werk stroom acties.|None|
|ActionsCompleted|Acties voltooid |Count|Totaal|Aantal voltooide werk stroom acties.|None|
|ActionsSucceeded|Acties geslaagd |Count|Totaal|Aantal geslaagde werk stroom acties.|None|
|ActionsFailed|Mislukte acties |Count|Totaal|Aantal mislukte werk stroom acties.|None|
|ActionsSkipped|Overgeslagen acties |Count|Totaal|Aantal overgeslagen werk stroom acties.|None|
|ActionLatency|Actie latentie |Waarna|Average|Latentie van voltooide werk stroom acties.|None|
|ActionSuccessLatency|Latentie geslaagde acties |Waarna|Average|Latentie van geslaagde werk stroom acties.|None|
|ActionThrottledEvents|Door actie vertraagde gebeurtenissen|Count|Totaal|Aantal door werk stroom actie vertraagde gebeurtenissen..|None|
|TriggersStarted|Triggers gestart |Count|Totaal|Aantal gestarte werk stroom triggers.|None|
|TriggersCompleted|Triggers voltooid |Count|Totaal|Aantal voltooide werk stroom triggers.|None|
|TriggersSucceeded|Geslaagde triggers |Count|Totaal|Aantal geslaagde werk stroom triggers.|None|
|TriggersFailed|Mislukte triggers |Count|Totaal|Aantal mislukte werk stroom triggers.|None|
|TriggersSkipped|Triggers overgeslagen|Count|Totaal|Aantal overgeslagen werk stroom triggers.|None|
|TriggersFired|Geactiveerde triggers |Count|Totaal|Aantal geactiveerde werk stroom triggers.|None|
|TriggerLatency|Latentie van trigger |Waarna|Average|Latentie van voltooide werk stroom triggers.|None|
|TriggerFireLatency|Brand latentie activeren |Waarna|Average|Latentie van geactiveerde werk stroom triggers.|None|
|TriggerSuccessLatency|Latentie van trigger geslaagd |Waarna|Average|Latentie van geslaagde werk stroom triggers.|None|
|TriggerThrottledEvents|Trigger beperkings gebeurtenissen|Count|Totaal|Aantal door werk stroom trigger vertraagde gebeurtenissen.|None|
|BillableActionExecutions|Factureer bare actie-uitvoeringen|Count|Totaal|Aantal uitvoeringen van werk stroom acties dat wordt gefactureerd.|None|
|BillableTriggerExecutions|Factureer bare trigger uitvoeringen|Count|Totaal|Aantal uitvoeringen van werk stroom trigger dat wordt gefactureerd.|None|
|TotalBillableExecutions|Totaal aantal factureer bare uitvoeringen|Count|Totaal|Aantal uitgevoerde werk stroom uitvoeringen dat wordt gefactureerd.|None|
|BillingUsageNativeOperation|Facturerings gebruik voor uitvoering van systeem eigen bewerkingen|Count|Totaal|Aantal uitvoeringen van de native bewerking dat wordt gefactureerd.|None|
|BillingUsageStandardConnector|Facturerings gebruik voor het uitvoeren van standaard-connectors|Count|Totaal|Aantal uitvoeringen van Standard-connector dat wordt gefactureerd.|None|
|BillingUsageStorageConsumption|Facturerings gebruik voor uitvoeringen van opslag verbruik|Count|Totaal|Aantal uitvoeringen van opslag verbruik dat wordt gefactureerd.|None|
|BillingUsageNativeOperation|Facturerings gebruik voor uitvoering van systeem eigen bewerkingen|Count|Totaal|Aantal uitvoeringen van de native bewerking dat wordt gefactureerd.|None|
|BillingUsageStandardConnector|Facturerings gebruik voor het uitvoeren van standaard-connectors|Count|Totaal|Aantal uitvoeringen van Standard-connector dat wordt gefactureerd.|None|
|BillingUsageStorageConsumption|Facturerings gebruik voor uitvoeringen van opslag verbruik|Count|Totaal|Aantal uitvoeringen van opslag verbruik dat wordt gefactureerd.|None|



## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|RunsStarted|Uitvoeringen gestart|Count|Totaal|Aantal uitvoeringen van werk stroom gestart.|None|
|RunsCompleted|Uitvoeringen voltooid|Count|Totaal|Aantal voltooide werk stroom uitvoeringen.|None|
|RunsSucceeded|Geslaagde uitvoeringen|Count|Totaal|Aantal geslaagde werk stroom uitvoeringen.|None|
|RunsFailed|Uitvoeringen mislukt|Count|Totaal|Het aantal uitvoeringen van de werk stroom is mislukt.|None|
|RunsCancelled|Geannuleerde uitvoeringen|Count|Totaal|Aantal uitgevoerde werk stroom uitvoeringen geannuleerd.|None|
|RunLatency|Uitvoerings latentie|Waarna|Average|Latentie van voltooide werk stroom uitvoeringen.|None|
|RunSuccessLatency|Latentie van geslaagde uitvoering|Waarna|Average|Latentie van geslaagde werk stroom uitvoeringen.|None|
|RunThrottledEvents|Vertraagde gebeurtenissen uitvoeren|Count|Totaal|Aantal werk stroom acties of trigger vertraagde gebeurtenissen.|None|
|RunStartThrottledEvents|Vertraagde gebeurtenissen uitvoeren|Count|Totaal|Aantal uitgevoerde vertraagde gebeurtenissen voor de werk stroom.|None|
|RunFailurePercentage|Percentage mislukte uitvoeringen|Procent|Totaal|Percentage mislukte werk stroom uitvoeringen.|None|
|ActionsStarted|Gestarte acties |Count|Totaal|Aantal gestarte werk stroom acties.|None|
|ActionsCompleted|Acties voltooid |Count|Totaal|Aantal voltooide werk stroom acties.|None|
|ActionsSucceeded|Acties geslaagd |Count|Totaal|Aantal geslaagde werk stroom acties.|None|
|ActionsFailed|Mislukte acties |Count|Totaal|Aantal mislukte werk stroom acties.|None|
|ActionsSkipped|Overgeslagen acties |Count|Totaal|Aantal overgeslagen werk stroom acties.|None|
|ActionLatency|Actie latentie |Waarna|Average|Latentie van voltooide werk stroom acties.|None|
|ActionSuccessLatency|Latentie geslaagde acties |Waarna|Average|Latentie van geslaagde werk stroom acties.|None|
|ActionThrottledEvents|Door actie vertraagde gebeurtenissen|Count|Totaal|Aantal door werk stroom actie vertraagde gebeurtenissen..|None|
|TriggersStarted|Triggers gestart |Count|Totaal|Aantal gestarte werk stroom triggers.|None|
|TriggersCompleted|Triggers voltooid |Count|Totaal|Aantal voltooide werk stroom triggers.|None|
|TriggersSucceeded|Geslaagde triggers |Count|Totaal|Aantal geslaagde werk stroom triggers.|None|
|TriggersFailed|Mislukte triggers |Count|Totaal|Aantal mislukte werk stroom triggers.|None|
|TriggersSkipped|Triggers overgeslagen|Count|Totaal|Aantal overgeslagen werk stroom triggers.|None|
|TriggersFired|Geactiveerde triggers |Count|Totaal|Aantal geactiveerde werk stroom triggers.|None|
|TriggerLatency|Latentie van trigger |Waarna|Average|Latentie van voltooide werk stroom triggers.|None|
|TriggerFireLatency|Brand latentie activeren |Waarna|Average|Latentie van geactiveerde werk stroom triggers.|None|
|TriggerSuccessLatency|Latentie van trigger geslaagd |Waarna|Average|Latentie van geslaagde werk stroom triggers.|None|
|TriggerThrottledEvents|Trigger beperkings gebeurtenissen|Count|Totaal|Aantal door werk stroom trigger vertraagde gebeurtenissen.|None|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Gebruik van werk stroom processor voor Integratieserviceomgeving|Procent|Average|Gebruik van werk stroom processoren voor de integratie service omgeving.|None|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Geheugen gebruik van werk stroom voor Integratieserviceomgeving|Procent|Average|Geheugen gebruik van werk stroom voor de integratie service omgeving.|None|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Processor gebruik van connector voor Integratieserviceomgeving|Procent|Average|Het processor gebruik van de connector voor de integratie service omgeving.|None|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Geheugen gebruik van connector voor Integratieserviceomgeving|Procent|Average|Geheugen gebruik van connector voor de integratie service omgeving.|None|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Voltooide uitvoeringen|Voltooide uitvoeringen|Count|Totaal|Het aantal uitvoeringen dat is voltooid voor deze werk ruimte|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Gestart uitvoeringen|Gestart uitvoeringen|Count|Totaal|Aantal uitvoeringen gestart voor deze werk ruimte|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Mislukte uitvoeringen|Mislukte uitvoeringen|Count|Totaal|Aantal uitvoeringen is mislukt voor deze werk ruimte|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Model registratie is voltooid|Model registratie is voltooid|Count|Totaal|Aantal model registraties dat is geslaagd in deze werk ruimte|Scenario|
|Model register mislukt|Model register mislukt|Count|Totaal|Aantal model registraties dat is mislukt in deze werk ruimte|Scenario, status code|
|Modelimplementatie gestart|Modelimplementatie gestart|Count|Totaal|Aantal model implementaties gestart in deze werk ruimte|Scenario|
|Modelimplementatie geslaagd|Modelimplementatie geslaagd|Count|Totaal|Aantal model implementaties dat is geslaagd in deze werk ruimte|Scenario|
|Modelimplementatie is mislukt|Modelimplementatie is mislukt|Count|Totaal|Aantal model implementaties die zijn mislukt in deze werk ruimte|Scenario, status code|
|Totaal aantal knoop punten|Totaal aantal knoop punten|Count|Average|Totaal aantal knoop punten. Dit totaal omvat enkele actieve knoop punten, niet-actieve knoop punten, niet-bruikbare knoop punten, afgebroken knoop punten, waardoor knoop punten|Scenario, clustername|
|Actieve knoop punten|Actieve knoop punten|Count|Average|Aantal Active-knoop punten. Dit zijn de knoop punten waarop een taak actief wordt uitgevoerd.|Scenario, clustername|
|Niet-actieve knoop punten|Niet-actieve knoop punten|Count|Average|Aantal niet-actieve knoop punten. Niet-actieve knoop punten zijn de knoop punten waarop geen taken worden uitgevoerd, maar u kunt wel een nieuwe taak accepteren, indien beschikbaar.|Scenario, clustername|
|Niet-bruikbare knoop punten|Niet-bruikbare knoop punten|Count|Average|Aantal niet-bruikbare knoop punten. Niet-bruikbare knoop punten zijn niet functioneel vanwege een probleem met onherleidbare. Deze knoop punten worden door Azure gerecycled.|Scenario, clustername|
|Knoop punten die zijn afgebroken|Knoop punten die zijn afgebroken|Count|Average|Aantal knoop punten dat is afgebroken. Deze knoop punten zijn de knoop punten met een lage prioriteit die worden verwijderd uit de beschik bare knooppunt groep.|Scenario, clustername|
|Knoop punten verlaten|Knoop punten verlaten|Count|Average|Aantal knoop punten dat de poort verlaat. Als u knoop punten verlaat, worden de knoop punten die zojuist de verwerking van een taak hebben voltooid, naar de niet-actieve status verzonden.|Scenario, clustername|
|Totaal aantal kernen|Totaal aantal kernen|Count|Average|Aantal totale kernen|Scenario, clustername|
|Actieve kernen|Actieve kernen|Count|Average|Aantal actieve kernen|Scenario, clustername|
|Niet-actieve kernen|Niet-actieve kernen|Count|Average|Aantal niet-actieve kern geheugens|Scenario, clustername|
|Onbruikbaar aantal kern geheugens|Onbruikbaar aantal kern geheugens|Count|Average|Aantal niet-bruikbare kernen|Scenario, clustername|
|Afgebroken kernen|Afgebroken kernen|Count|Average|Aantal afgebroken kernen|Scenario, clustername|
|Kernen verlaten|Kernen verlaten|Count|Average|Aantal te verlaten kernen|Scenario, clustername|
|Percentage quotum gebruik|Percentage quotum gebruik|Count|Average|Percentage van gebruikte quota|Scenario, clustername, VmFamilyName, VmPriority|
|CpuUtilization|CpuUtilization|Count|Average|CPU (preview-versie)|Scenario, runId, NodeId, CreatedTime|
|GpuUtilization|GpuUtilization|Count|Average|GPU (preview-versie)|Scenario, runId, NodeId, CreatedTime, DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Gebruik|Gebruik|Count|Count|Aantal API-aanroepen|ApiCategory, ApiName, ResultType, ResponseCode|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|Beschik baarheid van de Api's|ApiCategory, ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Micro soft. Media/Media Services/streamingEndpoints

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgangs gegevens, in bytes.|Output|
|SuccessE2ELatency|Geslaagde end-to-end-latentie|Milliseconden|Average|De gemiddelde latentie voor voltooide aanvragen in milliseconden.|Output|
|Aanvragen|Aanvragen|Count|Totaal|Aanvragen voor een streaming-eind punt.|Output Format, HTTP status code, error code|


## <a name="microsoftmediamediaservices"></a>Micro soft. Media/Media Services

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AssetQuota|Activa quotum|Count|Average|Hoeveel assets zijn toegestaan voor het huidige media service-account|None|
|AssetCount|Aantal assets|Count|Average|Hoeveel activa er al zijn gemaakt in het huidige media service-account|None|
|AssetQuotaUsedPercentage|Percentage gebruikt voor het activa quotum|Procent|Average|Verbruikt percentage in huidige media service account|None|
|ContentKeyPolicyQuota|Quotum voor inhouds sleutel beleid|Count|Average|Hoeveel beleids regels voor inhouds sleutels zijn toegestaan voor het huidige media service-account|None|
|ContentKeyPolicyCount|Aantal beleids regels voor inhouds sleutels|Count|Average|Hoeveel beleids regels voor inhouds sleutels zijn al gemaakt in het huidige media service-account|None|
|ContentKeyPolicyQuotaUsedPercentage|Percentage gebruikt quotum voor inhouds sleutel beleid|Procent|Average|Gebruikt percentage van beleid voor inhouds sleutels in het huidige media service-account|None|
|StreamingPolicyQuota|Quota voor streaming-beleid|Count|Average|Hoeveel streaming-beleids regels zijn toegestaan voor het huidige media service-account|None|
|StreamingPolicyCount|Aantal stroomsgewijze beleids regels|Count|Average|Hoeveel streaming-beleids regels zijn al gemaakt in het huidige media service-account|None|
|StreamingPolicyQuotaUsedPercentage|Percentage gebruikt quotum voor het streaming-beleid|Procent|Average|Gebruikt beleid voor streamingbeleid in het huidige media service-account|None|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Micro soft. NetApp/netAppAccounts/capacityPools/volumes

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AverageReadLatency|Gemiddelde lees latentie|MilliSeconds|Average|Gemiddelde lees latentie in milliseconden per bewerking|None|
|AverageWriteLatency|Gemiddelde schrijf latentie|MilliSeconds|Average|Gemiddelde schrijf latentie in milliseconden per bewerking|None|
|VolumeLogicalSize|Logische volume grootte|Bytes|Average|Logische grootte van het volume (gebruikte bytes)|None|
|VolumeSnapshotSize|Grootte van moment opname van volume|Bytes|Average|Grootte van alle moment opnamen in volume|None|
|ReadIops|IOPS lezen|CountPerSecond|Average|In-en uitvoer bewerkingen per seconde|None|
|WriteIops|IOPS schrijven|CountPerSecond|Average|In-en uitvoer bewerkingen per seconde|None|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|Gebruikte volume groep|Bytes|Average|Gebruikte grootte van de pool is toegewezen|None|
|VolumePoolTotalLogicalSize|Totale logische grootte van volume groep|Bytes|Average|Som van de logische grootte van alle volumes die deel uitmaken van de groep|None|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BytesSentRate|Verzonden bytes|Bytes|Totaal|Het aantal bytes dat de netwerk interface heeft verzonden|None|
|BytesReceivedRate|Ontvangen bytes|Bytes|Totaal|Het aantal bytes dat de netwerk interface heeft ontvangen|None|
|PacketsSentRate|Verzonden pakketten|Count|Totaal|Aantal pakketten dat de netwerk interface heeft verzonden|None|
|PacketsReceivedRate|Ontvangen pakketten|Count|Totaal|Aantal pakketten dat de netwerk interface heeft ontvangen|None|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|VipAvailability|Beschik baarheid gegevenspad|Count|Average|Gemiddelde Beschik baarheid van gegevens paden per tijds duur van Load Balancer|FrontendIPAddress,FrontendPort|
|DipAvailability|Status van Health probe|Count|Average|Gemiddelde status van Load Balancer Health probe per tijds duur|Protocol type, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Aantal bytes|Count|Totaal|Totaal aantal verzonden bytes binnen tijds periode|FrontendIPAddress, FrontendPort, direction|
|PacketCount|Aantal pakketten|Count|Totaal|Totaal aantal verzonden pakketten binnen tijds periode|FrontendIPAddress, FrontendPort, direction|
|SYNCount|SYN-aantal|Count|Totaal|Totaal aantal SYN-pakketten verzonden binnen tijds periode|FrontendIPAddress, FrontendPort, direction|
|SnatConnectionCount|Aantal SNAT-verbindingen|Count|Totaal|Totaal aantal nieuwe SNAT-verbindingen dat binnen een tijds periode is gemaakt|FrontendIPAddress,BackendIPAddress,ConnectionState|
|AllocatedSnatPorts|Toegewezen SNAT-poorten (preview-versie)|Count|Totaal|Totaal aantal toegewezen SNAT-poorten binnen tijds periode|FrontendIPAddress, BackendIPAddress, protocol type, IsAwaitingRemoval|
|UsedSnatPorts|Gebruikte SNAT-poorten (preview-versie)|Count|Totaal|Totaal aantal SNAT-poorten gebruikt binnen tijds periode|FrontendIPAddress, BackendIPAddress, protocol type, IsAwaitingRemoval|


## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|QueryVolume|Query volume|Count|Totaal|Aantal verzonden query's voor een DNS-zone|None|
|RecordSetCount|Aantal record sets|Count|Maximum|Aantal record sets in een DNS-zone|None|
|RecordSetCapacityUtilization|Capaciteits gebruik van record sets|Procent|Maximum|Percentage van de set-capaciteit van de record die wordt gebruikt door een DNS-zone|None|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PacketsInDDoS|DDoS inkomende pakketten|CountPerSecond|Maximum|DDoS inkomende pakketten|None|
|PacketsDroppedDDoS|DDoS inkomende pakketten verwijderd|CountPerSecond|Maximum|DDoS inkomende pakketten verwijderd|None|
|PacketsForwardedDDoS|DDoS inkomende pakketten doorgestuurd|CountPerSecond|Maximum|DDoS inkomende pakketten doorgestuurd|None|
|TCPPacketsInDDoS|Binnenkomende TCP-pakketten DDoS|CountPerSecond|Maximum|Binnenkomende TCP-pakketten DDoS|None|
|TCPPacketsDroppedDDoS|DDoS binnenkomende TCP-pakketten|CountPerSecond|Maximum|DDoS binnenkomende TCP-pakketten|None|
|TCPPacketsForwardedDDoS|Doorgestuurde binnenkomende TCP-pakketten DDoS|CountPerSecond|Maximum|Doorgestuurde binnenkomende TCP-pakketten DDoS|None|
|UDPPacketsInDDoS|Binnenkomende UDP-pakketten DDoS|CountPerSecond|Maximum|Binnenkomende UDP-pakketten DDoS|None|
|UDPPacketsDroppedDDoS|Verwijderde binnenkomende UDP-pakketten DDoS|CountPerSecond|Maximum|Verwijderde binnenkomende UDP-pakketten DDoS|None|
|UDPPacketsForwardedDDoS|Door inkomende UDP-pakketten DDoS doorgestuurd|CountPerSecond|Maximum|Door inkomende UDP-pakketten DDoS doorgestuurd|None|
|BytesInDDoS|Binnenkomende bytes DDoS|BytesPerSecond|Maximum|Binnenkomende bytes DDoS|None|
|BytesDroppedDDoS|Binnenkomende bytes verloren DDoS|BytesPerSecond|Maximum|Binnenkomende bytes verloren DDoS|None|
|BytesForwardedDDoS|Doorgestuurde binnenkomende bytes DDoS|BytesPerSecond|Maximum|Doorgestuurde binnenkomende bytes DDoS|None|
|TCPBytesInDDoS|DDoS binnenkomende TCP-bytes|BytesPerSecond|Maximum|DDoS binnenkomende TCP-bytes|None|
|TCPBytesDroppedDDoS|DDoS binnenkomende TCP-bytes|BytesPerSecond|Maximum|DDoS binnenkomende TCP-bytes|None|
|TCPBytesForwardedDDoS|DDoS doorgestuurde binnenkomende TCP-bytes|BytesPerSecond|Maximum|DDoS doorgestuurde binnenkomende TCP-bytes|None|
|UDPBytesInDDoS|Binnenkomende UDP-bytes DDoS|BytesPerSecond|Maximum|Binnenkomende UDP-bytes DDoS|None|
|UDPBytesDroppedDDoS|Binnenkomend UDP-bytes verloren DDoS|BytesPerSecond|Maximum|Binnenkomend UDP-bytes verloren DDoS|None|
|UDPBytesForwardedDDoS|Doorgestuurde binnenkomende UDP-bytes DDoS|BytesPerSecond|Maximum|Doorgestuurde binnenkomende UDP-bytes DDoS|None|
|IfUnderDDoSAttack|Onder DDoS-aanval of niet|Count|Maximum|Onder DDoS-aanval of niet|None|
|DDoSTriggerTCPPackets|Binnenkomende TCP-pakketten om DDoS-beperking te activeren|CountPerSecond|Maximum|Binnenkomende TCP-pakketten om DDoS-beperking te activeren|None|
|DDoSTriggerUDPPackets|Binnenkomende UDP-pakketten om DDoS-beperking te activeren|CountPerSecond|Maximum|Binnenkomende UDP-pakketten om DDoS-beperking te activeren|None|
|DDoSTriggerSYNPackets|Inkomende SYN-pakketten om DDoS-beperking te activeren|CountPerSecond|Maximum|Inkomende SYN-pakketten om DDoS-beperking te activeren|None|
|VipAvailability|Beschik baarheid gegevenspad|Count|Average|Gemiddelde Beschik baarheid van IP-adressen per tijds duur|Poort|
|ByteCount|Aantal bytes|Count|Totaal|Totaal aantal verzonden bytes binnen tijds periode|Poort, richting|
|PacketCount|Aantal pakketten|Count|Totaal|Totaal aantal verzonden pakketten binnen tijds periode|Poort, richting|
|SynCount|SYN-aantal|Count|Totaal|Totaal aantal SYN-pakketten verzonden binnen tijds periode|Poort, richting|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Retour tijd voor pings naar een virtuele machine|MilliSeconds|Average|Retour tijd voor pings die naar een bestemmings-VM worden verzonden|SourceCustomerAddress,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Pingen naar een virtuele machine is mislukt|Procent|Average|Percentage van het aantal mislukte pings naar totale aantal verzonden Pings van een doel-VM|SourceCustomerAddress,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ApplicationRuleHit|Aantal treffers toepassings regels|Count|Totaal|Aantal keer dat toepassings regels zijn geraakt|Status, reden, protocol|
|NetworkRuleHit|Aantal treffers in netwerk regels|Count|Totaal|Aantal keren dat netwerk regels zijn geraakt|Status, reden, protocol|
|FirewallHealth|Status van Firewall|Procent|Average|Status van Firewall|Status, reden|
|DataProcessed|Verwerkte gegevens|Bytes|Totaal|Totale hoeveelheid gegevens die door de firewall worden verwerkt|None|
|SNATPortUtilization|Gebruik van SNAT-poort|Procent|Average|Gebruik van SNAT-poort|None|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Doorvoer|Doorvoer|BytesPerSecond|Average|Aantal bytes per seconde dat de Application Gateway heeft bediend|None|
|UnhealthyHostCount|Aantal hosts met slechte status|Count|Average|Aantal beschadigde backend-hosts|BackendSettingsPool|
|HealthyHostCount|Aantal goede hosts|Count|Average|Aantal gezonde backend-hosts|BackendSettingsPool|
|TotalRequests|Totaal aantal aanvragen|Count|Totaal|Aantal geslaagde aanvragen dat Application Gateway heeft bediend|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|Aanvragen per minuut per gegezonde host|Count|Average|Gemiddeld aantal aanvragen per minuut op een gezonde backend-host in een groep|BackendSettingsPool|
|FailedRequests|Mislukte aanvragen|Count|Totaal|Aantal mislukte aanvragen dat Application Gateway heeft bediend|BackendSettingsPool|
|ResponseStatus|Reactie status|Count|Totaal|Http-antwoord status geretourneerd door Application Gateway|HttpStatusGroup|
|CurrentConnections|Huidige verbindingen|Count|Totaal|Aantal actieve verbindingen dat tot stand is gebracht met Application Gateway|None|
|CpuUtilization|CPU-gebruik|Procent|Average|Huidig CPU-gebruik van de Application Gateway|None|
|CapacityUnits|Huidige capaciteits eenheden|Count|Average|Verbruikte capaciteits eenheden|None|
|ComputeUnits|Huidige reken eenheden|Count|Average|Verbruikte reken eenheden|None|
|BackendResponseStatus|Reactie status van back-end|Count|Totaal|Het aantal HTTP-antwoord codes dat door de back-end-leden is gegenereerd. Dit omvat geen antwoord codes die zijn gegenereerd door de Application Gateway.|BackendServer, hosts, BackendHttpSetting, HttpStatusGroup|
|TlsProtocol|TLS-protocol van client|Count|Totaal|Het aantal TLS-en niet-TLS-aanvragen dat door de client is gestart en die verbinding heeft gemaakt met de Application Gateway. Als u de TLS-protocol distributie wilt weer geven, filtert u op het TLS-protocol van de dimensie.|Listener, TlsProtocol|
|BytesSent|Verzonden bytes|Bytes|Totaal|Het totale aantal bytes dat is verzonden door de Application Gateway naar de clients|Listener|
|BytesReceived|Ontvangen bytes|Bytes|Totaal|Het totale aantal bytes dat is ontvangen door de Application Gateway van de clients|Listener|
|ClientRtt|Client RTT|MilliSeconds|Average|Gemiddelde Round-retour tijd tussen clients en Application Gateway. Met deze metriek wordt aangegeven hoe lang het duurt om verbindingen tot stand te brengen en bevestigingen te retour neren|Listener|
|ApplicationGatewayTotalTime|Totale tijd van Application Gateway|MilliSeconds|Average|De gemiddelde tijd die nodig is voor het verwerken van een aanvraag en het antwoord op verzen ding. Dit wordt berekend als gemiddelde van het interval van de tijd dat Application Gateway de eerste byte van een HTTP-aanvraag ontvangt naar het tijdstip waarop de bewerking voor het verzenden van het antwoord is voltooid. Het is belang rijk te weten dat dit doorgaans de verwerkings tijd van Application Gateway, de tijd dat de aanvraag-en antwoord pakketten op het netwerk onderweg zijn en het tijdstip waarop de back-end-server heeft gereageerd.|Listener|
|BackendConnectTime|Moment back-end verbinding|MilliSeconds|Average|Tijd die is besteed aan het tot stand brengen van een verbinding met een back-end-server|Listener, BackendServer, hosts, BackendHttpSetting|
|BackendFirstByteResponseTime|Reactie tijd eerste byte van back-end|MilliSeconds|Average|Tijds interval tussen begin van het tot stand brengen van een verbinding met de back-end-server en het ontvangen van de eerste byte van de reactie header, geschatte verwerkings tijd van de back-endserver|Listener, BackendServer, hosts, BackendHttpSetting|
|BackendLastByteResponseTime|Reactie tijd laatste byte van back-end|MilliSeconds|Average|Tijds interval tussen begin van het tot stand brengen van een verbinding met de back-endserver en het ontvangen van de laatste byte van de antwoord tekst|Listener, BackendServer, hosts, BackendHttpSetting|
|MatchedCount|Totale regel distributie Web Application firewall|Count|Totaal|Totale regel distributie Web Application Firewall voor het binnenkomende verkeer|RuleGroup, RuleId|
|BlockedCount|Regel distributie voor door Web Application firewall geblokkeerde aanvragen|Count|Totaal|Regel distributie voor door Web Application firewall geblokkeerde aanvragen|RuleGroup, RuleId|
|BlockedReqCount|Aantal geblokkeerde aanvragen voor Web Application firewall|Count|Totaal|Aantal geblokkeerde aanvragen voor Web Application firewall|None|

## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AverageBandwidth|Gateway-S2S-band breedte|BytesPerSecond|Average|Gemiddeld aantal site-naar-site-band breedte van een gateway in bytes per seconde|None|
|P2SBandwidth|Gateway P2S-band breedte|BytesPerSecond|Average|Gemiddelde Point-to-site band breedte van een gateway in bytes per seconde|None|
|P2SConnectionCount|Aantal P2S-verbindingen|Count|Maximum|Aantal Point-to-site-verbindingen van een gateway|Protocol|
|TunnelAverageBandwidth|Tunnel bandbreedte|BytesPerSecond|Average|Gemiddelde band breedte van een tunnel in bytes per seconde|ConnectionName, RemoteIP|
|TunnelEgressBytes|Bytes voor uitgaand tunnels|Bytes|Totaal|Uitgaande bytes van een tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Bytes van de tunnel ingang|Bytes|Totaal|Binnenkomende bytes van een tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Tunnel-uituitgangs pakketten|Count|Totaal|Aantal uitgaande pakketten van een tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Tunnel ingangs pakketten|Count|Totaal|Binnenkomend pakket aantal van een tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Uitschakeling van niet-overeenkomende TS-pakketten door tunnel|Count|Totaal|Aantal uitgevallen uitgaande pakketten van de selectie van de verkeers kiezer komen niet overeen met een tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Verloren gegane pakketten door de tunnel ingang TS komen niet overeen|Count|Totaal|Aantal inkomende pakketten in de verkeers selectie niet overeenkomen met een tunnel|ConnectionName, RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Micro soft. Network/expressRoutePorts

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|Count|Average|RX licht niveau in dBm|Koppeling, Lane|
|TxLightLevel|TxLightLevel|Count|Average|TX licht niveau in dBm|Koppeling, Lane|
|AdminState|AdminState|Count|Average|Beheer status van de poort|Koppeling|
|LineProtocol|LineProtocol|Count|Average|Status van het regel Protocol van de poort|Koppeling|
|PortBitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Bits die Azure per seconde binnenkomen|Koppeling|
|PortBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Bits egressing Azure per seconde|Koppeling|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Bits die Azure per seconde binnenkomen|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Bits egressing Azure per seconde|PeeringType|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|CountPerSecond|Average|Bits die Azure per seconde binnenkomen|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|CountPerSecond|Average|Bits egressing Azure per seconde|PeeredCircuitSKey|
|BgpAvailability|BGP-Beschik baarheid|Procent|Average|BGP-Beschik baarheid van MSEE naar alle peers.|PeeringType, peer|
|ArpAvailability|ARP-Beschik baarheid|Procent|Average|ARP-Beschik baarheid van MSEE naar alle peers.|PeeringType, peer|
|QosDropBitsInPerSecond|DroppedInBitsPerSecond|CountPerSecond|Average|Ingangs gegevens die per seconde worden verwijderd|None|
|QosDropBitsOutPerSecond|DroppedOutBitsPerSecond|CountPerSecond|Average|Uitgaande bits van gegevens die per seconde worden verwijderd|None|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Bits die Azure per seconde binnenkomen|None|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Bits egressing Azure per seconde|None|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Bits die Azure per seconde binnenkomen|None|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Bits egressing Azure per seconde|None|

## <a name="microsoftnetworkexpressroutegateways"></a>Micro soft. Network/expressRouteGateways

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Bits die Azure per seconde binnenkomen|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Bits egressing Azure per seconde|ConnectionName|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|QpsByEndpoint|Query's op eind punt geretourneerd|Count|Totaal|Aantal keren dat een Traffic Manager eind punt is geretourneerd in het opgegeven tijds bestek|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Eindpunt status op eind punt|Count|Maximum|1 als de test status van een eind punt is ingeschakeld, 0 anders.|EndpointName|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Tests mislukt|Procent|Average|% van de controles van connectiviteits controle is mislukt|None|
|AverageRoundtripMs|Gem. retour tijd (MS)|MilliSeconds|Average|Gemiddelde Round-retour tijd van het netwerk (MS) voor connectiviteits controle tests die zijn verzonden tussen de bron en het doel|None|
|ChecksFailedPercent|Percentage mislukte controles (preview-versie)|Procent|Average|% van het controleren van de connectiviteits controle is mislukt|SourceAddress, SourceName, SourceResourceId, Source type, protocol, DestinationAddress, doelhost, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|
|RoundTripTimeMs|Retour tijd (MS) (preview-versie)|MilliSeconds|Average|Retour tijd in milliseconden voor het controleren van de connectiviteits controle|SourceAddress, SourceName, SourceResourceId, Source type, protocol, DestinationAddress, doelhost, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|RequestCount|Aantal aanvragen|Count|Totaal|Het aantal client aanvragen dat wordt geleverd door de HTTP/S-proxy|Http status, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Aanvraag grootte|Bytes|Totaal|Het aantal bytes dat is verzonden als aanvragen van clients naar de HTTP/S-proxy|Http status, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Grootte van antwoord|Bytes|Totaal|Het aantal bytes dat is verzonden als reacties van HTTP/S-proxy naar clients|Http status, HttpStatusGroup, ClientRegion, ClientCountry|
|BillableResponseSize|Grootte van factureer bare antwoorden|Bytes|Totaal|Het aantal factureer bare bytes (minimale 2KB per aanvraag) dat wordt verzonden als antwoorden van HTTP/S-proxy naar clients.|Http status, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Aantal back-aanvragen|Count|Totaal|Het aantal aanvragen dat is verzonden vanaf de HTTP/S-proxy naar back-end|Http status, HttpStatusGroup, back-end|
|BackendRequestLatency|Latentie van back-upaanvraag|MilliSeconds|Average|De tijd die wordt berekend vanaf het moment dat de aanvraag door de HTTP/S-proxy naar de back-end werd verzonden, totdat de HTTP/S-proxy de laatste antwoord byte van de back-end heeft ontvangen|Back-end|
|TotalLatency|Totale latentie|MilliSeconds|Average|De tijd die wordt berekend vanaf het moment dat de client aanvraag door de HTTP/S-proxy werd ontvangen totdat de client de laatste antwoord byte van de HTTP/S-proxy heeft bevestigd|Http status, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Back-status percentage|Procent|Average|Het percentage geslaagde status controles van de HTTP/S-proxy naar back-end|Back-end, hosts|
|WebApplicationFirewallRequestCount|Aantal aanvragen voor Web Application firewall|Count|Totaal|Het aantal client aanvragen dat is verwerkt door de Web Application firewall|Beleidsnaam, regelnaam, actie|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|registratie. alle|Registratie bewerkingen|Count|Totaal|Het aantal voltooide registratie bewerkingen (gemaakte bijwerk query's en verwijderingen). |None|
|registratie. Create|Bewerkingen voor het maken van registratie|Count|Totaal|Het aantal gemaakte registraties.|None|
|registratie. update|Registratie-update bewerkingen|Count|Totaal|Het aantal voltooide registratie-updates.|None|
|registratie. ophalen|Lees bewerkingen voor registratie|Count|Totaal|Het aantal geslaagde registratie query's.|None|
|registratie. Delete|Verwijderings bewerkingen voor registratie|Count|Totaal|Het aantal voltooide registraties dat is verwijderd.|None|
|e-mail|Inkomende berichten|Count|Totaal|Het aantal geslaagde verzend-API-aanroepen. |None|
|inkomend. gepland|Geplande push meldingen verzonden|Count|Totaal|Geplande push meldingen geannuleerd|None|
|Binnenkomend. gepland. annuleren|Geplande push meldingen geannuleerd|Count|Totaal|Geplande push meldingen geannuleerd|None|
|gepland. in behandeling|Geplande meldingen in behandeling|Count|Totaal|Geplande meldingen in behandeling|None|
|installatie. alle|Bewerkingen voor installatie beheer|Count|Totaal|Bewerkingen voor installatie beheer|None|
|installation.get|Installatie bewerkingen ophalen|Count|Totaal|Installatie bewerkingen ophalen|None|
|installatie. upsert|Installatie bewerkingen maken of bijwerken|Count|Totaal|Installatie bewerkingen maken of bijwerken|None|
|installatie. patch|Patch-installatie bewerkingen|Count|Totaal|Patch-installatie bewerkingen|None|
|installation.delete|Installatie bewerkingen verwijderen|Count|Totaal|Installatie bewerkingen verwijderen|None|
|outgoing.allpns.success|Geslaagde meldingen|Count|Totaal|Het aantal geslaagde meldingen.|None|
|outgoing.allpns.invalidpayload|Payload-fouten|Count|Totaal|Het aantal pushes dat is mislukt omdat de PNS een ongeldige Payload-fout heeft geretourneerd.|None|
|uitgaande. allpns. pnserror|Externe meldingen systeem fouten|Count|Totaal|Het aantal pushes dat is mislukt omdat er een probleem is opgetreden bij het communiceren met de PNS (er zijn verificatie problemen uitgesloten).|None|
|uitgaande. allpns. channelerror|Kanaal fouten|Count|Totaal|Het aantal pushes dat is mislukt omdat het kanaal ongeldig is en niet is gekoppeld aan de juiste app beperkt of verlopen.|None|
|outgoing.allpns.badorexpiredchannel|Ongeldige of verlopen kanaal fouten|Count|Totaal|Het aantal pushes dat is mislukt omdat het kanaal/token-registratie in de registratie is verlopen of ongeldig is.|None|
|outgoing.wns.success|Geslaagde meldingen WNS|Count|Totaal|Het aantal geslaagde meldingen.|None|
|outgoing.wns.invalidcredentials|WNS-verificatie fouten (ongeldige referenties)|Count|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd. (Windows Live herkent de referenties niet).|None|
|outgoing.wns.badchannel|WNS ongeldige kanaal fout|Count|Totaal|Het aantal pushes dat is mislukt omdat de kanaal in de registratie niet is herkend (WNS-status: 404 niet gevonden).|None|
|outgoing.wns.expiredchannel|WNS-fout met verlopen kanaal|Count|Totaal|Het aantal pushes dat is mislukt omdat de kanaal is verlopen (WNS-status: 410 verdwenen).|None|
|uitgaande. wns. throttled|WNS beperkte meldingen|Count|Totaal|Het aantal pushes dat is mislukt omdat WNS deze app beperkt (WNS-status: 406 niet toegestaan).|None|
|outgoing.wns.tokenproviderunreachable|WNS-verificatie fouten (onbereikbaar)|Count|Totaal|Windows Live is niet bereikbaar.|None|
|uitgaande. wns. invalidtoken|WNS-verificatie fouten (ongeldig token)|Count|Totaal|Het token dat is gegeven aan WNS is niet geldig (WNS-status: 401 niet toegestaan).|None|
|outgoing.wns.wrongtoken|WNS-autorisatie fouten (onjuist token)|Count|Totaal|Het token dat is gegeven aan WNS is geldig, maar voor een andere toepassing (WNS-status: 403 verboden). Dit kan gebeuren als de kanaal in de registratie is gekoppeld aan een andere app. Controleer of de client-app is gekoppeld aan dezelfde app waarvan de referenties zich in de notification hub bevinden.|None|
|outgoing.wns.invalidnotificationformat|Ongeldige indeling van WNS-melding|Count|Totaal|De indeling van de melding is ongeldig (WNS-status: 400). Houd er rekening mee dat WNS niet alle ongeldige payloads weigert.|None|
|outgoing.wns.invalidnotificationsize|Fout met ongeldige grootte van WNS-melding|Count|Totaal|De nettolading van de melding is te groot (WNS-status: 413).|None|
|outgoing.wns.channelthrottled|WNS-kanaal beperkt|Count|Totaal|De melding is verwijderd omdat de kanaal in de registratie is beperkt (WNS-reactie header: X-WNS-notification status: channelThrottled).|None|
|outgoing.wns.channeldisconnected|Verbinding met WNS-kanaal verbroken|Count|Totaal|De melding is verwijderd omdat de kanaal in de registratie is beperkt (WNS-reactie header: X-WNS-DeviceConnectionStatus: disconnected).|None|
|uitgaande. wns. dropd|WNS-verwijderde meldingen|Count|Totaal|De melding is verwijderd omdat de kanaal in de registratie is beperkt (X-WNS-notification status: verwijderd maar niet X-WNS-DeviceConnectionStatus: disconnected).|None|
|uitgaande. wns. pnserror|WNS-fouten|Count|Totaal|Er is geen melding bezorgd vanwege fouten in de communicatie met WNS.|None|
|uitgaande. wns. authenticationerror|WNS-verificatie fouten|Count|Totaal|Er is een melding niet bezorgd omdat er fouten zijn opgetreden tijdens de communicatie met Windows Live ongeldige referenties of een onjuist token.|None|
|outgoing.apns.success|Geslaagde meldingen van APNS|Count|Totaal|Het aantal geslaagde meldingen.|None|
|outgoing.apns.invalidcredentials|APNS-autorisatie fouten|Count|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd.|None|
|outgoing.apns.badchannel|Fout met ongeldige APNS-kanaal|Count|Totaal|Het aantal pushes dat is mislukt omdat het token ongeldig is (APNS-status code: 8).|None|
|outgoing.apns.expiredchannel|Fout bij verlopen van APNS-kanaal|Count|Totaal|Het aantal tokens dat ongeldig is gemaakt door het APNS-feedback kanaal.|None|
|outgoing.apns.invalidnotificationsize|Fout door ongeldige grootte van APNS-melding|Count|Totaal|Het aantal pushes dat is mislukt omdat de payload te groot is (APNS-status code: 7).|None|
|outgoing.apns.pnserror|APNS-fouten|Count|Totaal|Het aantal pushes dat is mislukt vanwege fouten in de communicatie met APNS.|None|
|outgoing.gcm.success|Geslaagde meldingen GCM|Count|Totaal|Het aantal geslaagde meldingen.|None|
|outgoing.gcm.invalidcredentials|GCM-verificatie fouten (ongeldige referenties)|Count|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd.|None|
|outgoing.gcm.badchannel|GCM ongeldige kanaal fout|Count|Totaal|Het aantal pushes dat is mislukt omdat de registratie in de registratie niet is herkend (GCM-resultaat: ongeldige registratie).|None|
|outgoing.gcm.expiredchannel|GCM-fout met verlopen kanaal|Count|Totaal|Het aantal pushes dat is mislukt omdat de registratie in de registratie is verlopen (GCM resultaat: NotRegistered).|None|
|uitgaande. GCM. throttled|GCM beperkte meldingen|Count|Totaal|Het aantal pushes dat is mislukt omdat GCM deze app heeft beperkt (GCM-status code: 501-599 of resultaat: niet beschikbaar).|None|
|uitgaande. GCM. invalidnotificationformat|Ongeldige indeling van GCM-melding|Count|Totaal|Het aantal pushes dat is mislukt omdat de payload niet juist is geformatteerd (GCM-resultaat: InvalidDataKey of InvalidTtl).|None|
|outgoing.gcm.invalidnotificationsize|Fout met ongeldige grootte van GCM-melding|Count|Totaal|Het aantal pushes dat is mislukt omdat de payload te groot is (GCM-resultaat: MessageTooBig).|None|
|outgoing.gcm.wrongchannel|GCM onjuiste kanaal fout|Count|Totaal|Het aantal pushes dat is mislukt omdat de registratie in de registratie niet is gekoppeld aan de huidige app (GCM-resultaat: InvalidPackageName).|None|
|uitgaande. GCM. pnserror|GCM-fouten|Count|Totaal|Het aantal pushes dat is mislukt vanwege fouten in de communicatie met GCM.|None|
|uitgaande. GCM. authenticationerror|GCM-verificatie fouten|Count|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd, de referenties zijn geblokkeerd of de SenderId niet juist is geconfigureerd in de app (GCM resultaat: MismatchedSenderId).|None|
|uitgaand. mpns. geslaagd|Geslaagde meldingen MPNS|Count|Totaal|Het aantal geslaagde meldingen.|None|
|outgoing.mpns.invalidcredentials|Ongeldige referenties MPNS|Count|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd.|None|
|outgoing.mpns.badchannel|MPNS ongeldige kanaal fout|Count|Totaal|Het aantal pushes dat is mislukt omdat de kanaal in de registratie niet is herkend (MPNS-status: 404 niet gevonden).|None|
|uitgaande. mpns. throttled|MPNS beperkte meldingen|Count|Totaal|Het aantal pushes dat is mislukt omdat MPNS deze app beperkt (WNS MPNS: 406 niet toegestaan).|None|
|uitgaande. mpns. invalidnotificationformat|Ongeldige indeling van MPNS-melding|Count|Totaal|Het aantal pushes dat is mislukt omdat de payload van de melding te groot is.|None|
|outgoing.mpns.channeldisconnected|Verbinding met MPNS-kanaal verbroken|Count|Totaal|Het aantal pushes dat is mislukt omdat de kanaal van de registratie is verbroken (MPNS-status: 412 niet gevonden).|None|
|uitgaande. mpns. dropd|MPNS-verwijderde meldingen|Count|Totaal|Het aantal pushes dat is verwijderd door MPNS (MPNS-reactie header: X-notification status: QueueFull of onderdrukt).|None|
|uitgaande. mpns. pnserror|MPNS-fouten|Count|Totaal|Het aantal pushes dat is mislukt vanwege fouten in de communicatie met MPNS.|None|
|uitgaande. mpns. authenticationerror|MPNS-verificatie fouten|Count|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd.|None|
|notificationhub. pushes|Alle uitgaande meldingen|Count|Totaal|Alle uitgaande meldingen van de notification hub|None|
|binnenkomende. alle. aanvragen|Alle inkomende aanvragen|Count|Totaal|Totaal aantal binnenkomende aanvragen voor een notification hub|None|
|inkomend. alle. failedrequests|Alle binnenkomende mislukte aanvragen|Count|Totaal|Totaal aantal binnenkomende mislukte aanvragen voor een notification hub|None|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage vrije inodes Average_|% Vrije inodes|Count|Average|Percentage vrije inodes Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|% Beschik bare ruimte Average_|% vrije ruimte|Count|Average|% Beschik bare ruimte Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_% gebruikte inodes|% Gebruikte inodes|Count|Average|Average_% gebruikte inodes|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Percentage gebruikte ruimte Average_|Percentage gebruikte ruimte|Count|Average|Percentage gebruikte ruimte Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk Lees bewerkingen in bytes per seconde|gelezen bytes per seconde|Count|Average|Average_Disk Lees bewerkingen in bytes per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk Reads/sec|leesbewerkingen per seconde|Count|Average|Average_Disk Reads/sec|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk overdrachten per seconde|Schijfoverdrachten per seconde|Count|Average|Average_Disk overdrachten per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk geschreven bytes per seconde|geschreven Bytes per seconde|Count|Average|Average_Disk geschreven bytes per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk schrijf bewerkingen per seconde|schrijfbewerkingen per seconde|Count|Average|Average_Disk schrijf bewerkingen per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Free Megabytes|Beschikbare Megabytes|Count|Average|Average_Free Megabytes|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Logical schijf bytes per seconde|Logische schijf Bytes per seconde|Count|Average|Average_Logical schijf bytes per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Percentage beschik bare geheugen Average_|Percentage beschikbaar geheugen|Count|Average|Percentage beschik bare geheugen Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_ percentage beschik bare wissel ruimte|Percentage beschik bare wissel ruimte|Count|Average|Average_ percentage beschik bare wissel ruimte|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_ percentage gebruikt geheugen|Percentage gebruikt geheugen|Count|Average|Average_ percentage gebruikt geheugen|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_ percentage gebruikte wissel ruimte|Percentage gebruikte wissel ruimte|Count|Average|Average_ percentage gebruikte wissel ruimte|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Available MB geheugen|Beschikbaar geheugen in megabytes|Count|Average|Average_Available MB geheugen|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Available MB wisselen|Beschik bare mega bytes wisselen|Count|Average|Average_Available MB wisselen|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Page Lees bewerkingen per seconde|paginaleesbewerkingen per seconde|Count|Average|Average_Page Lees bewerkingen per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Page schrijf bewerkingen per seconde|paginaschrijfbewerkingen per seconde|Count|Average|Average_Page schrijf bewerkingen per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Pages/sec.|pagina's per seconde|Count|Average|Average_Pages/sec.|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
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
|Average_Avg. Gelezen bytes per seconde|Gemiddelde Lees tijd schijf|Count|Average|Average_Avg. Gelezen bytes per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Avg. overdrachttijd|Gemiddelde tijd schijf overdracht|Count|Average|Average_Avg. overdrachttijd|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Avg. voor de fysieke schijf|Gemiddelde schrijf tijd schijf|Count|Average|Average_Avg. voor de fysieke schijf|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
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
|Percentage processor tijd van Average_|Percentage processortijd|Count|Average|Percentage processor tijd van Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Percentage gebruikers tijd van Average_|Percentage gebruikers tijd|Count|Average|Percentage gebruikers tijd van Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Free fysiek geheugen|Vrij fysiek geheugen|Count|Average|Average_Free fysiek geheugen|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Free ruimte in wissel geheugen bestanden|Vrije ruimte in wissel geheugen bestanden|Count|Average|Average_Free ruimte in wissel geheugen bestanden|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Virtueel geheugen Average_Free|Vrij virtueel geheugen|Count|Average|Virtueel geheugen Average_Free|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Processes|Processen|Count|Average|Average_Processes|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Size opgeslagen in Wissel bestanden|Grootte opgeslagen in Wissel bestanden|Count|Average|Average_Size opgeslagen in Wissel bestanden|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Uptime|Bedrijfstijd|Count|Average|Average_Uptime|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Users|Gebruikers|Count|Average|Average_Users|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Avg. Gelezen bytes per seconde|Gemiddelde Lees tijd schijf|Count|Average|Average_Avg. Gelezen bytes per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Avg. voor de fysieke schijf|Gemiddelde schrijf tijd schijf|Count|Average|Average_Avg. voor de fysieke schijf|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Wachtrij lengte van Average_Current schijf|Huidige wachtrij lengte voor de schijf|Count|Average|Wachtrij lengte van Average_Current schijf|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk Reads/sec|leesbewerkingen per seconde|Count|Average|Average_Disk Reads/sec|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk overdrachten per seconde|Schijfoverdrachten per seconde|Count|Average|Average_Disk overdrachten per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk schrijf bewerkingen per seconde|schrijfbewerkingen per seconde|Count|Average|Average_Disk schrijf bewerkingen per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Free Megabytes|Beschikbare Megabytes|Count|Average|Average_Free Megabytes|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|% Beschik bare ruimte Average_|% vrije ruimte|Count|Average|% Beschik bare ruimte Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Available MB|Beschikbare megabytes (MB)|Count|Average|Average_Available MB|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_% toegewezen bytes in gebruik|% Toegewezen bytes in gebruik|Count|Average|Average_% toegewezen bytes in gebruik|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Ontvangen Average_Bytes per seconde|Ontvangen bytes per seconde|Count|Average|Ontvangen Average_Bytes per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Verzonden Average_Bytes per seconde|Verzonden bytes per seconde|Count|Average|Verzonden Average_Bytes per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Bytes totaal per seconde|Totaal aantal bytes per seconde|Count|Average|Average_Bytes totaal per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Percentage processor tijd van Average_|Percentage processortijd|Count|Average|Percentage processor tijd van Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Lengte van Average_Processor wachtrij|Lengte van de processor wachtrij|Count|Average|Lengte van Average_Processor wachtrij|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Heartbeat|Heartbeat|Count|Totaal|Heartbeat|Computer, OSType, versie, SourceComputerId|
|Bijwerken|Bijwerken|Count|Average|Bijwerken|Computer, product, classificatie, update State, optioneel, goedgekeurd|
|Gebeurtenis|Gebeurtenis|Count|Average|Gebeurtenis|Source, EventLog, computer, EventCategory, EventLevel, EventLevelName, Event gebeurtenis|

## <a name="microsoftpeeringpeeringservices"></a>Micro soft. peering/peeringServices

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PrefixLatency|Voorvoegsel latentie|Milliseconden|Average|Latentie van mediaan voorvoegsel|Voorvoegselnaam|

## <a name="microsoftpeeringpeerings"></a>Micro soft. peering/peering

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SessionAvailabilityV4|Sessie beschikbaarheid v4|Procent|Average|Beschik baarheid van de v4-sessie|ConnectionId|
|SessionAvailabilityV6|Sessie beschikbaarheid V6|Procent|Average|Beschik baarheid van de V6-sessie|ConnectionId|
|IngressTrafficRate|Frequentie van ingangs verkeer|BitsPerSecond|Average|Ingangs verkeers frequentie in bits per seconde|ConnectionId|
|EgressTrafficRate|Frequentie van uitgangs verkeer|BitsPerSecond|Average|Frequentie van uitgaand verkeer in bits per seconde|ConnectionId|

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
|ListenerConnections-Success|ListenerConnections-Success|Count|Totaal|Geslaagde ListenerConnections voor micro soft. relay.|EntityName, kan operationresult niet|
|ListenerConnections-client error|ListenerConnections-client error|Count|Totaal|Client error op ListenerConnections voor micro soft. relay.|EntityName, kan operationresult niet|
|ListenerConnections-server error|ListenerConnections-server error|Count|Totaal|Server error op ListenerConnections voor micro soft. relay.|EntityName, kan operationresult niet|
|SenderConnections-Success|SenderConnections-Success|Count|Totaal|Geslaagde SenderConnections voor micro soft. relay.|EntityName, kan operationresult niet|
|SenderConnections-client error|SenderConnections-client error|Count|Totaal|Client error op SenderConnections voor micro soft. relay.|EntityName, kan operationresult niet|
|SenderConnections-server error|SenderConnections-server error|Count|Totaal|Server error op SenderConnections voor micro soft. relay.|EntityName, kan operationresult niet|
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
|SearchLatency|Zoek latentie|Waarna|Average|Gemiddelde Zoek latentie voor de zoek service|None|
|SearchQueriesPerSecond|Zoek query's per seconde|CountPerSecond|Average|Zoek query's per seconde voor de zoek service|None|
|ThrottledSearchQueriesPercentage|Percentage vertraagde Zoek query's|Procent|Average|Percentage Zoek query's dat is beperkt tot de zoek service|None|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SuccessfulRequests|Geslaagde aanvragen|Count|Totaal|Totaal aantal geslaagde aanvragen voor een naam ruimte|EntityName, kan operationresult niet|
|ServerErrors|Server fouten.|Count|Totaal|Server fouten voor micro soft. ServiceBus.|EntityName, kan operationresult niet|
|UserErrors|Gebruikers fouten.|Count|Totaal|Gebruikers fouten voor micro soft. ServiceBus.|EntityName, kan operationresult niet|
|ThrottledRequests|Vertraagde aanvragen.|Count|Totaal|Beperkte aanvragen voor micro soft. ServiceBus.|EntityName, kan operationresult niet|
|IncomingRequests|Binnenkomende aanvragen|Count|Totaal|Binnenkomende aanvragen voor micro soft. ServiceBus.|EntityName|
|IncomingMessages|Inkomende berichten|Count|Totaal|Binnenkomende berichten voor micro soft. ServiceBus.|EntityName|
|OutgoingMessages|Uitgaande berichten|Count|Totaal|Uitgaande berichten voor micro soft. ServiceBus.|EntityName|
|ActiveConnections|ActiveConnections|Count|Totaal|Totaal aantal actieve verbindingen voor micro soft. ServiceBus.|None|
|ConnectionsOpened|Geopende verbindingen.|Count|Average|Geopende verbindingen voor micro soft. ServiceBus.|EntityName|
|ConnectionsClosed|Verbindingen gesloten.|Count|Average|Gesloten verbindingen voor micro soft. ServiceBus.|EntityName|
|Grootte|Grootte|Bytes|Average|Grootte van een wachtrij/onderwerp in bytes.|EntityName|
|Berichten|Aantal berichten in een wachtrij/onderwerp.|Count|Average|Aantal berichten in een wachtrij/onderwerp.|EntityName|
|ActiveMessages|Aantal actieve berichten in een wachtrij/onderwerp.|Count|Average|Aantal actieve berichten in een wachtrij/onderwerp.|EntityName|
|DeadletteredMessages|Aantal onbestelbare berichten in een wachtrij/onderwerp.|Count|Average|Aantal onbestelbare berichten in een wachtrij/onderwerp.|EntityName|
|ScheduledMessages|Aantal geplande berichten in een wachtrij/onderwerp.|Count|Average|Aantal geplande berichten in een wachtrij/onderwerp.|EntityName|
|NamespaceCpuUsage|CPU|Procent|Maximum|Metrische gegevens voor CPU-gebruik van service bus Premium-naam ruimte.|Replica|
|NamespaceMemoryUsage|Geheugen gebruik|Procent|Maximum|Metrische geheugen gebruik van service bus Premium-naam ruimte.|Replica|
|CPUXNS|CPU (afgeschaft)|Procent|Maximum|Metrische gegevens voor CPU-gebruik van service bus Premium-naam ruimte. Deze metrische waarde is verouderd. Gebruik in plaats daarvan de CPU-metriek (NamespaceCpuUsage).|Replica|
|WSXNS|Geheugen gebruik (afgeschaft)|Procent|Maximum|Metrische geheugen gebruik van service bus Premium-naam ruimte. Deze metriek is afgeschaft. Gebruik in plaats daarvan de metriek geheugen gebruik (NamespaceMemoryUsage).|Replica|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Count|Average|CPU toegewezen aan deze container in milli-kernen|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Bytes|Average|Geheugen toegewezen aan deze container in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Count|Average|Werkelijk CPU-gebruik in milli-kernen|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Bytes|Average|Werkelijk geheugen gebruik in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Procent|Average|Gebruik van CPU voor deze container als percentage van AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Procent|Average|Gebruik van CPU voor deze container als percentage van AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Count|Average|Status van Service Fabric mesh-toepassing|ApplicationName, status|
|ServiceStatus|ServiceStatus|Count|Average|Integriteits status van een service in Service Fabric mesh-toepassing|ApplicationName, status, servicenaam|
|ServiceReplicaStatus|ServiceReplicaStatus|Count|Average|Integriteits status van een service replica in Service Fabric mesh-toepassing|ApplicationName, status, ServiceName, ServiceReplicaName|
|Container status|Container status|Count|Average|Status van de container in Service Fabric mesh-toepassing|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, status|
|RestartCount|RestartCount|Count|Average|Telling van een container in Service Fabric mesh-toepassing opnieuw starten|ApplicationName, status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ConnectionCount|Aantal verbindingen|Count|Maximum|De hoeveelheid gebruikers verbinding.|Eindpunt|
|MessageCount|Aantal berichten|Count|Totaal|De totale hoeveelheid berichten.|None|
|InboundTraffic|Binnenkomend verkeer|Bytes|Totaal|Het inkomende verkeer van de service|None|
|OutboundTraffic|Uitgaand verkeer|Bytes|Totaal|Het uitgaande verkeer van de service|None|
|UserErrors|Gebruikers fouten|Procent|Maximum|Het percentage gebruikers fouten|None|
|SystemErrors|Systeem fouten|Procent|Maximum|Het percentage systeem fouten|None|





## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Procent|Average|CPU-percentage|None|
|physical_data_read_percent|Gegevens-I/O-percentage|Procent|Average|Gegevens-I/O-percentage|None|
|log_write_percent|Logboek-IO-percentage|Procent|Average|Logboek-IO-percentage. Niet van toepassing op data warehouses.|None|
|dtu_consumption_percent|DTU-percentage|Procent|Average|DTU-percentage. Is van toepassing op DTU-gebaseerde data bases.|None|
|opslagpad|Gebruikte gegevens ruimte|Bytes|Maximum|Gebruikte gegevens ruimte. Niet van toepassing op data warehouses.|None|
|connection_successful|Geslaagde verbindingen|Count|Totaal|Geslaagde verbindingen|None|
|connection_failed|Mislukte verbindingen|Count|Totaal|Mislukte verbindingen|None|
|blocked_by_firewall|Geblokkeerd door de firewall|Count|Totaal|Geblokkeerd door de firewall|None|
|constateer|Deadlocks|Count|Totaal|Impassen. Niet van toepassing op data warehouses.|None|
|storage_percent|Percentage gebruikte gegevens ruimte|Procent|Maximum|Percentage gebruikte gegevens ruimte. Niet van toepassing op data warehouses of grootschalige-data bases.|None|
|xtp_storage_percent|Percentage OLTP-opslag in het geheugen|Procent|Average|Percentage OLTP-opslag in het geheugen. Niet van toepassing op data warehouses.|None|
|workers_percent|Percentage werk nemers|Procent|Average|Werknemers percentage. Niet van toepassing op data warehouses.|None|
|sessions_percent|Percentage sessies|Procent|Average|Percentage sessies. Niet van toepassing op data warehouses.|None|
|dtu_limit|DTU-limiet|Count|Average|DTU-limiet. Is van toepassing op DTU-gebaseerde data bases.|None|
|dtu_used|DTU gebruikt|Count|Average|DTU gebruikt. Is van toepassing op DTU-gebaseerde data bases.|None|
|cpu_limit|CPU-limiet|Count|Average|CPU-limiet. Is van toepassing op vCore-data bases.|None|
|cpu_used|CPU gebruikt|Count|Average|CPU gebruikt. Is van toepassing op vCore-data bases.|None|
|dwu_limit|Limiet voor DWU|Count|Maximum|DWU-limiet. Is alleen van toepassing op data warehouses.|None|
|dwu_consumption_percent|Percentage DWU|Procent|Maximum|DWU-percentage. Is alleen van toepassing op data warehouses.|None|
|dwu_used|DWU gebruikt|Count|Maximum|DWU gebruikt. Is alleen van toepassing op data warehouses.|None|
|cache_hit_percent|Percentage cache treffers|Procent|Maximum|Percentage van cache treffer. Is alleen van toepassing op data warehouses.|None|
|cache_used_percent|Percentage gebruikt cache|Procent|Maximum|Percentage gebruikt cache. Is alleen van toepassing op data warehouses.|None|
|sqlserver_process_core_percent|Kern percentage van SQL Server proces|Procent|Maximum|Het CPU-gebruiks percentage voor het SQL Server proces, zoals gemeten door het besturings systeem. Momenteel alleen beschikbaar voor serverloze data bases.|None|
|sqlserver_process_memory_percent|Percentage proces geheugen SQL Server|Procent|Maximum|Het percentage geheugen gebruik voor het SQL Server proces, zoals gemeten door het besturings systeem. Momenteel alleen beschikbaar voor serverloze data bases.|None|
|tempdb_data_size|Data File grootte van tempdb|Count|Maximum|Data File grootte van tempdb-gegevens bestanden. Niet van toepassing op data warehouses.|None|
|tempdb_log_size|Grootte van logboek bestanden tempdb|Count|Maximum|Grootte van KB-logboek bestanden. Niet van toepassing op data warehouses.|None|
|tempdb_log_used_percent|Percentage gebruikt TempDB-logboek|Procent|Maximum|Percentage gebruikt TempDB-logboek. Niet van toepassing op data warehouses.|None|
|local_tempdb_usage_percent|Lokaal TempDB-percentage|Procent|Average|Lokaal TempDB-percentage. Is alleen van toepassing op data warehouses.|None|
|app_cpu_billed|App CPU gefactureerd|Count|Totaal|App CPU gefactureerd. Is van toepassing op serverloze data bases.|None|
|app_cpu_percent|CPU-percentage van app|Procent|Average|CPU-percentage van de app. Is van toepassing op serverloze data bases.|None|
|app_memory_percent|Percentage app-geheugen|Procent|Average|Percentage app-geheugen. Is van toepassing op serverloze data bases.|None|
|allocated_data_storage|Toegewezen gegevens ruimte|Bytes|Average|Toegewezen gegevens opslag. Niet van toepassing op data warehouses.|None|
|memory_usage_percent|Geheugen percentage|Procent|Maximum|Geheugen percentage. Is alleen van toepassing op data warehouses.|None|
|full_backup_size_bytes|Opslag grootte voor volledige back-up|Bytes|Maximum|Cumulatieve opslag grootte voor volledige back-ups. Is van toepassing op vCore-data bases. Niet van toepassing op grootschalige-data bases.|None|
|diff_backup_size_bytes|Grootte van differentiële back-upopslag|Bytes|Maximum|Cumulatieve differentiële back-upopslag. Is van toepassing op vCore-data bases. Niet van toepassing op grootschalige-data bases.|None|
|log_backup_size_bytes|Opslag grootte van logboek back-up|Bytes|Maximum|Cumulatieve opslag grootte van logboek back-up. Is van toepassing op vCore-data bases. Niet van toepassing op grootschalige-data bases.|None|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Procent|Average|CPU-percentage|None|
|database_cpu_percent|CPU-percentage|Procent|Average|CPU-percentage|DatabaseResourceId|
|physical_data_read_percent|Gegevens-I/O-percentage|Procent|Average|Gegevens-I/O-percentage|None|
|database_physical_data_read_percent|Gegevens-I/O-percentage|Procent|Average|Gegevens-I/O-percentage|DatabaseResourceId|
|log_write_percent|Logboek-IO-percentage|Procent|Average|Logboek-IO-percentage|None|
|database_log_write_percent|Logboek-IO-percentage|Procent|Average|Logboek-IO-percentage|DatabaseResourceId|
|dtu_consumption_percent|DTU-percentage|Procent|Average|DTU-percentage. Is van toepassing op op DTU gebaseerde elastische Pools.|None|
|database_dtu_consumption_percent|DTU-percentage|Procent|Average|DTU-percentage|DatabaseResourceId|
|storage_percent|Percentage gebruikte gegevens ruimte|Procent|Average|Percentage gebruikte gegevens ruimte|None|
|workers_percent|Percentage werk nemers|Procent|Average|Percentage werk nemers|None|
|database_workers_percent|Percentage werk nemers|Procent|Average|Percentage werk nemers|DatabaseResourceId|
|sessions_percent|Percentage sessies|Procent|Average|Percentage sessies|None|
|database_sessions_percent|Percentage sessies|Procent|Average|Percentage sessies|DatabaseResourceId|
|eDTU_limit|eDTU-limiet|Count|Average|eDTU-limiet. Is van toepassing op op DTU gebaseerde elastische Pools.|None|
|storage_limit|Maximale grootte van gegevens|Bytes|Average|Maximale grootte van gegevens|None|
|eDTU_used|eDTU gebruikt|Count|Average|eDTU gebruikt. Is van toepassing op op DTU gebaseerde elastische Pools.|None|
|database_eDTU_used|eDTU gebruikt|Count|Average|eDTU gebruikt|DatabaseResourceId|
|storage_used|Gebruikte gegevens ruimte|Bytes|Average|Gebruikte gegevens ruimte|None|
|database_storage_used|Gebruikte gegevens ruimte|Bytes|Average|Gebruikte gegevens ruimte|DatabaseResourceId|
|xtp_storage_percent|Percentage OLTP-opslag in het geheugen|Procent|Average|Percentage OLTP-opslag in het geheugen|None|
|cpu_limit|CPU-limiet|Count|Average|CPU-limiet. Is van toepassing op op vCore gebaseerde elastische Pools.|None|
|database_cpu_limit|CPU-limiet|Count|Average|CPU-limiet|DatabaseResourceId|
|cpu_used|CPU gebruikt|Count|Average|CPU gebruikt. Is van toepassing op op vCore gebaseerde elastische Pools.|None|
|database_cpu_used|CPU gebruikt|Count|Average|CPU gebruikt|DatabaseResourceId|
|sqlserver_process_core_percent|Kern percentage van SQL Server proces|Procent|Maximum|CPU-gebruik als percentage van het SQL DB-proces. Is van toepassing op elastische Pools.|None|
|sqlserver_process_memory_percent|Percentage proces geheugen SQL Server|Procent|Maximum|Geheugen gebruik als percentage van het SQL DB-proces. Is van toepassing op elastische Pools.|None|
|tempdb_data_size|Data File grootte van tempdb|Count|Maximum|Data File grootte van tempdb|None|
|tempdb_log_size|Grootte van logboek bestanden tempdb|Count|Maximum|Grootte van logboek bestanden tempdb|None|
|tempdb_log_used_percent|Percentage gebruikt TempDB-logboek|Procent|Maximum|Percentage gebruikt TempDB-logboek|None|
|allocated_data_storage|Toegewezen gegevens ruimte|Bytes|Average|Toegewezen gegevens ruimte|None|
|database_allocated_data_storage|Toegewezen gegevens ruimte|Bytes|Average|Toegewezen gegevens ruimte|DatabaseResourceId|
|allocated_data_storage_percent|Percentage toegewezen gegevens ruimte|Procent|Maximum|Percentage toegewezen gegevens ruimte|None|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|dtu_consumption_percent|DTU-percentage|Procent|Average|DTU-percentage|ElasticPoolResourceId|
|database_dtu_consumption_percent|DTU-percentage|Procent|Average|DTU-percentage|DatabaseResourceId,ElasticPoolResourceId|
|storage_used|Gebruikte gegevens ruimte|Bytes|Average|Gebruikte gegevens ruimte|ElasticPoolResourceId|
|database_storage_used|Gebruikte gegevens ruimte|Bytes|Average|Gebruikte gegevens ruimte|DatabaseResourceId,ElasticPoolResourceId|
|dtu_used|DTU gebruikt|Count|Average|DTU gebruikt|DatabaseResourceId|

## <a name="microsoftsqlmanagedinstances"></a>Micro soft. SQL/managedInstances

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|virtual_core_count|Aantal virtuele kernen|Count|Average|Aantal virtuele kernen|None|
|avg_cpu_percent|Gemiddeld CPU-percentage|Procent|Average|Gemiddeld CPU-percentage|None|
|reserved_storage_mb|Gereserveerde opslag ruimte|Count|Average|Gereserveerde opslag ruimte|None|
|storage_space_used_mb|Gebruikte opslag ruimte|Count|Average|Gebruikte opslag ruimte|None|
|io_requests|Aantal i/o-aanvragen|Count|Average|Aantal i/o-aanvragen|None|
|io_bytes_read|Gelezen IO-bytes|Bytes|Average|Gelezen IO-bytes|None|
|io_bytes_written|Geschreven IO-bytes|Bytes|Average|Geschreven IO-bytes|None|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|UsedCapacity|Gebruikte capaciteit|Bytes|Average|Gebruikte capaciteit van account|None|
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
|BlobCount|Aantal blobs|Count|Average|Het aantal blobs in de Blob-service van het opslagaccount.|BlobType, Tier|
|ContainerCount|Aantal blobcontainers|Count|Average|Het aantal containers in de Blob-service van het opslagaccount.|None|
|IndexCapacity|Index capaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de index van de ADLS Gen2 (hiërarchisch) in bytes.|None|
|Transacties|Transacties|Count|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de ResponseType-dimensie voor het aantal verschillende type reactie.|ResponseType, geotype, ApiName, authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid inkomende gegevens in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgaande gegevens in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie|
|SuccessServerLatency|Geslaagde serverlatentie|Milliseconden|Average|De gemiddelde latentie die wordt gebruikt door Azure Storage voor het verwerken van een aanvraag is gelukt, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|Geotype, ApiName, authenticatie|
|SuccessE2ELatency|Geslaagde E2E-latentie|Milliseconden|Average|De gemiddelde end-to-end latentie van geslaagde aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|Het percentage van de beschikbaarheid van de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TableCapacity|Tabelcapaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de Table-service van het opslagaccount (in bytes).|None|
|TableCount|Aantal tabellen|Count|Average|Het aantal tabellen in de Table-service van het opslagaccount.|None|
|TableEntityCount|Aantal tabelentiteiten|Count|Average|Het aantal tabelentiteiten in de Table-service van het opslagaccount.|None|
|Transacties|Transacties|Count|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de ResponseType-dimensie voor het aantal verschillende type reactie.|ResponseType, geotype, ApiName, authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid inkomende gegevens in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgaande gegevens in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie|
|SuccessServerLatency|Geslaagde serverlatentie|Milliseconden|Average|De gemiddelde latentie die wordt gebruikt door Azure Storage voor het verwerken van een aanvraag is gelukt, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|Geotype, ApiName, authenticatie|
|SuccessE2ELatency|Geslaagde E2E-latentie|Milliseconden|Average|De gemiddelde end-to-end latentie van geslaagde aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|Het percentage van de beschikbaarheid van de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|FileCapacity|Bestands capaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de bestands service van het opslag account in bytes.|Bestands share|
|FileCount|Aantal bestanden|Count|Average|Het aantal bestanden in de file-service van het opslag account.|Bestands share|
|FileShareCount|Aantal bestands shares|Count|Average|Het aantal bestands shares in de file-service van het opslag account.|None|
|FileShareSnapshotCount|Aantal moment opnamen van bestands shares|Count|Average|Het aantal moment opnamen dat aanwezig is op de share in de bestanden service van het opslag account.|Bestands share|
|FileShareSnapshotSize|Grootte van moment opname van bestands share|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de moment opnamen in de bestands service van het opslag account in bytes.|Bestands share|
|FileShareQuota|Quota grootte van bestands share|Bytes|Average|De bovengrens voor de hoeveelheid opslag die kan worden gebruikt door Azure Files service in bytes.|Bestands share|
|Transacties|Transacties|Count|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de ResponseType-dimensie voor het aantal verschillende type reactie.|ResponseType, geotype, ApiName, Authentication, file share|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid inkomende gegevens in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie, bestands share|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgaande gegevens in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie, bestands share|
|SuccessServerLatency|Geslaagde serverlatentie|Milliseconden|Average|De gemiddelde latentie die wordt gebruikt door Azure Storage voor het verwerken van een aanvraag is gelukt, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|Geotype, ApiName, authenticatie, bestands share|
|SuccessE2ELatency|Geslaagde E2E-latentie|Milliseconden|Average|De gemiddelde end-to-end latentie van geslaagde aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie, bestands share|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|Het percentage van de beschikbaarheid van de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie, bestands share|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|QueueCapacity|Wachtrijcapaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de Queue-service van het opslagaccount (in bytes).|None|
|QueueCount|Aantal wachtrijen|Count|Average|Het aantal wachtrijen in de Queue-service van het opslagaccount.|None|
|QueueMessageCount|Aantal wachtrijberichten|Count|Average|Het geschatte aantal wachtrijberichten in de Queue-service van het opslagaccount.|None|
|Transacties|Transacties|Count|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de ResponseType-dimensie voor het aantal verschillende type reactie.|ResponseType, geotype, ApiName, authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid inkomende gegevens in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgaande gegevens in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie|
|SuccessServerLatency|Geslaagde serverlatentie|Milliseconden|Average|De gemiddelde latentie die wordt gebruikt door Azure Storage voor het verwerken van een aanvraag is gelukt, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|Geotype, ApiName, authenticatie|
|SuccessE2ELatency|Geslaagde E2E-latentie|Milliseconden|Average|De gemiddelde end-to-end latentie van geslaagde aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie|
|Beschikbaarheid|Beschikbaarheid|Procent|Average|Het percentage van de beschikbaarheid van de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie|





## <a name="microsoftstoragecachecaches"></a>Micro soft. StorageCache/caches

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ClientIOPS|Totaal aantal IOPS client|Count|Average|Het aantal client bestands bewerkingen dat is verwerkt door de cache.|None|
|ClientLatency|Gemiddelde client latentie|Milliseconden|Average|Gemiddelde latentie van client bestands bewerkingen naar de opslag cache.|None|
|ClientReadIOPS|Door client gelezen IOPS|CountPerSecond|Average|Lees bewerkingen van de client per seconde.|None|
|ClientReadThroughput|Gemiddelde doorvoer snelheid van cache Lees bewerking|BytesPerSecond|Average|Overdrachts frequentie van gelezen gegevens van de client.|None|
|ClientWriteIOPS|Client schrijf-IOPS|CountPerSecond|Average|Schrijf bewerkingen van de client per seconde.|None|
|ClientWriteThroughput|Gemiddelde doorvoer snelheid van cache schrijf bewerkingen|BytesPerSecond|Average|Overdrachts frequentie van de gegevens van de client.|None|
|ClientMetadataReadIOPS|IOPS voor lezen van meta gegevens van client|CountPerSecond|Average|Het aantal client bestands bewerkingen dat naar de cache wordt verzonden, exclusief gegevens Lees bewerkingen, waarbij de permanente status niet wordt gewijzigd.|None|
|ClientMetadataWriteIOPS|IOPS voor schrijven van meta gegevens van client|CountPerSecond|Average|Het aantal client bestands bewerkingen dat naar de cache wordt verzonden, met uitzonde ring van gegevens schrijf bewerkingen, waardoor de permanente status wordt gewijzigd.|None|
|ClientLockIOPS|IOPS-client vergrendeling|CountPerSecond|Average|Vergrendelings bewerkingen voor client bestanden per seconde.|None|
|StorageTargetHealth|Status van opslag doel|Count|Average|Booleaanse resultaten van connectiviteits test tussen de cache-en opslag doelen.|None|
|Bedrijfstijd|Bedrijfstijd|Count|Average|Booleaanse resultaten van connectiviteits test tussen het cache-en bewakings systeem.|None|
|StorageTargetIOPS|Totale aantal StorageTarget IOPS|Count|Average|De frequentie van alle bestands bewerkingen die de cache verzendt naar een bepaalde StorageTarget.|StorageTarget|
|StorageTargetWriteIOPS|StorageTarget write IOPS|Count|Average|Het aantal schrijf bewerkingen voor bestanden dat door de cache wordt verzonden naar een bepaalde StorageTarget.|StorageTarget|
|StorageTargetAsyncWriteThroughput|Asynchrone schrijf doorvoer StorageTarget|BytesPerSecond|Average|De frequentie waarmee de cache asynchroon gegevens naar een bepaalde StorageTarget schrijft. Dit zijn opportunistische schrijf bewerkingen die niet leiden dat clients worden geblokkeerd.|StorageTarget|
|StorageTargetSyncWriteThroughput|StorageTarget synchrone schrijf doorvoer|BytesPerSecond|Average|De frequentie waarmee de cache synchroon gegevens naar een bepaalde StorageTarget schrijft. Dit zijn schrijf bewerkingen die ervoor zorgen dat clients blok keren.|StorageTarget|
|StorageTargetTotalWriteThroughput|Totale schrijf doorvoer StorageTarget|BytesPerSecond|Average|De totale frequentie waarmee de cache gegevens naar een bepaalde StorageTarget schrijft.|StorageTarget|
|StorageTargetLatency|StorageTarget-latentie|Milliseconden|Average|De gemiddelde retour latentie van alle bestands bewerkingen die de cache verzendt naar een partricular-StorageTarget.|StorageTarget|
|StorageTargetMetadataReadIOPS|StorageTarget voor lezen van meta gegevens|CountPerSecond|Average|Het aantal bestands bewerkingen waarmee de permanente status niet wordt gewijzigd en de Lees bewerking wordt uitgesloten, waardoor de cache wordt verzonden naar een bepaalde StorageTarget.|StorageTarget|
|StorageTargetMetadataWriteIOPS|StorageTarget-schrijf-IOPS voor meta gegevens|CountPerSecond|Average|Het aantal bestands bewerkingen waarmee de permanente status wordt gewijzigd en de schrijf bewerking wordt uitgesloten, waardoor de cache wordt verzonden naar een bepaalde StorageTarget.|StorageTarget|
|StorageTargetReadIOPS|StorageTarget lezen IOPS|CountPerSecond|Average|Het aantal lees bewerkingen van bestanden dat door de cache wordt verzonden naar een bepaalde StorageTarget.|StorageTarget|
|StorageTargetReadAheadThroughput|StorageTarget door Voer lezen|BytesPerSecond|Average|De frequentie waarmee de cache gegevens uit de StorageTarget wordt gelezen.|StorageTarget|
|StorageTargetFillThroughput|Door Voer voor StorageTarget-vulling|BytesPerSecond|Average|De frequentie waarmee de cache gegevens leest uit de StorageTarget om een cache-Missing af te handelen.|StorageTarget|
|StorageTargetTotalReadThroughput|Totale Lees doorvoer StorageTarget|BytesPerSecond|Average|De totale frequentie waarmee de cache gegevens van een bepaalde StorageTarget leest.|StorageTarget|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Resultaat van synchronisatie sessie|Count|Average|Metriek die een waarde van 1 registreert telkens wanneer het server eindpunt een synchronisatie sessie met het Cloud eindpunt heeft voltooid|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Gesynchroniseerde bestanden|Count|Totaal|Aantal gesynchroniseerde bestanden|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Bestanden die niet worden gesynchroniseerd|Count|Totaal|Aantal bestanden dat niet kan worden gesynchroniseerd|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncBatchTransferredFileBytes|Gesynchroniseerde bytes|Bytes|Totaal|Totale bestands grootte die is overgedragen voor synchronisatie sessies|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncServerHeartbeat|Online status van de server|Count|Maximum|Metriek die een waarde van 1 registreert telkens wanneer de resigtered-server een heartbeat met het Cloud eindpunt heeft geregistreerd|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Cloud lagen intrekken|Bytes|Totaal|De totale grootte van de gegevens die door de server zijn ingetrokken|ServerName|
|StorageSyncRecalledTotalNetworkBytes|Grootte van intrekken Cloud lagen|Bytes|Totaal|Grootte van gegevens die zijn ingetrokken|SyncGroupName, servername|
|StorageSyncRecallThroughputBytesPerSecond|Door Voer van Cloud lagen intrekken|BytesPerSecond|Average|Grootte van gegevens intrekken door Voer|SyncGroupName, servername|
|StorageSyncRecalledNetworkBytesByApplication|Grootte van intrekken van Cloud lagen op toepassing|Bytes|Totaal|Grootte van gegevens die door de toepassing zijn ingetrokken|SyncGroupName, servername, ApplicationName|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>micro soft. storagesync/storageSyncServices/syncGroups

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|Gesynchroniseerde bestanden|Count|Totaal|Aantal gesynchroniseerde bestanden|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Bestanden die niet worden gesynchroniseerd|Count|Totaal|Aantal bestanden dat niet kan worden gesynchroniseerd|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupBatchTransferredFileBytes|Gesynchroniseerde bytes|Bytes|Totaal|Totale bestands grootte die is overgedragen voor synchronisatie sessies|SyncGroupName,ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>micro soft. storagesync/storageSyncServices/syncGroups/serverEndpoints

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|Gesynchroniseerde bestanden|Count|Totaal|Aantal gesynchroniseerde bestanden|ServerEndpointName,SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Bestanden die niet worden gesynchroniseerd|Count|Totaal|Aantal bestanden dat niet kan worden gesynchroniseerd|ServerEndpointName,SyncDirection|
|ServerEndpointBatchTransferredFileBytes|Gesynchroniseerde bytes|Bytes|Totaal|Totale bestands grootte die is overgedragen voor synchronisatie sessies|ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>micro soft. storagesync/storageSyncServices/registeredServer

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ServerHeartbeat|Online status van de server|Count|Maximum|Metriek die een waarde van 1 registreert telkens wanneer de resigtered-server een heartbeat met het Cloud eindpunt heeft geregistreerd|ServerResourceId, servername|
|ServerRecallIOTotalSizeBytes|Cloud lagen intrekken|Bytes|Totaal|De totale grootte van de gegevens die door de server zijn ingetrokken|ServerResourceId, servername|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ResourceUtilization|Gebruikspercentage voor Streaming-eenheden|Procent|Maximum|Gebruikspercentage voor Streaming-eenheden|Logischenaam, PartitionId|
|InputEvents|Invoergebeurtenissen|Count|Totaal|Invoergebeurtenissen|Logischenaam, PartitionId|
|InputEventBytes|Invoergebeurtenisbytes|Bytes|Totaal|Invoergebeurtenisbytes|Logischenaam, PartitionId|
|LateInputEvents|Late invoergebeurtenissen|Count|Totaal|Late invoergebeurtenissen|Logischenaam, PartitionId|
|OutputEvents|Uitvoergebeurtenis|Count|Totaal|Uitvoergebeurtenis|Logischenaam, PartitionId|
|ConversionErrors|Gegevensconversiefouten|Count|Totaal|Gegevensconversiefouten|Logischenaam, PartitionId|
|Fouten|Runtimefouten|Count|Totaal|Runtimefouten|Logischenaam, PartitionId|
|DroppedOrAdjustedEvents|Gebeurtenissen met een andere volg orde|Count|Totaal|Gebeurtenissen met een andere volg orde|Logischenaam, PartitionId|
|AMLCalloutRequests|Functieaanvragen|Count|Totaal|Functieaanvragen|Logischenaam, PartitionId|
|AMLCalloutFailedRequests|Mislukte functieaanvragen|Count|Totaal|Mislukte functieaanvragen|Logischenaam, PartitionId|
|AMLCalloutInputEvents|Functiegebeurtenissen|Count|Totaal|Functiegebeurtenissen|Logischenaam, PartitionId|
|DeserializationError|Fouten in invoerdeserialisatie|Count|Totaal|Fouten in invoerdeserialisatie|Logischenaam, PartitionId|
|EarlyInputEvents|Vroege-invoergebeurtenissen|Count|Totaal|Vroege-invoergebeurtenissen|Logischenaam, PartitionId|
|OutputWatermarkDelaySeconds|Watermerkvertraging|Waarna|Maximum|Watermerkvertraging|Logischenaam, PartitionId|
|InputEventsSourcesBacklogged|Invoervelden met achterstand|Count|Maximum|Invoervelden met achterstand|Logischenaam, PartitionId|
|InputEventsSourcesPerSecond|Ontvangen invoerbronnen|Count|Totaal|Ontvangen invoerbronnen|Logischenaam, PartitionId|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ontvangen berichten met ingang|Count|Totaal|Telling van berichten die zijn gelezen uit alle gebeurtenis bronnen van Event hub of IoT hub|None|
|IngressReceivedInvalidMessages|Ongeldige berichten ontvangen|Count|Totaal|Aantal ongeldige berichten gelezen uit alle gebeurtenis bronnen van Event hub of IoT hub|None|
|IngressReceivedBytes|Ontvangen bytes van binnenkomend verkeer|Bytes|Totaal|Het aantal gelezen bytes van alle gebeurtenis bronnen|None|
|IngressStoredBytes|In ingangs opgeslagen bytes|Bytes|Totaal|De totale grootte van de gebeurtenissen die zijn verwerkt en beschikbaar voor de query|None|
|IngressStoredEvents|Opgeslagen gebeurtenissen in ingangs|Count|Totaal|Aantal samengevoegde gebeurtenissen dat is verwerkt en beschikbaar is voor query|None|
|IngressReceivedMessagesTimeLag|Tijds vertraging ontvangen inkomende berichten|Waarna|Maximum|Verschil tussen het tijdstip waarop het bericht in de bron van de gebeurtenis in de wachtrij staat en de tijd die wordt verwerkt in ingangs|None|
|IngressReceivedMessagesCountLag|Vertraging ontvangen inkomende berichten van ingang|Count|Average|Verschil tussen het Volg nummer van de laatste bericht in de bron partitie en het Volg nummer van de berichten die worden verwerkt in ingangs gebeurtenissen|None|
|WarmStorageMaxProperties|Maximale eigenschappen van warme opslag|Count|Maximum|Maximum aantal eigenschappen dat is toegestaan door de omgeving voor de SKU van S1/S2 en het maximum aantal eigenschappen dat is toegestaan door de warme Store voor PAYG SKU|None|
|WarmStorageUsedProperties|Eigenschappen voor warme opslag gebruikt |Count|Maximum|Aantal eigenschappen dat wordt gebruikt door de omgeving voor de SKU van S1/S2 en het aantal eigenschappen dat door warme Store voor PAYG SKU wordt gebruikt|None|

## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ontvangen berichten met ingang|Count|Totaal|Aantal berichten dat uit de gebeurtenis bron is gelezen|None|
|IngressReceivedInvalidMessages|Ongeldige berichten ontvangen|Count|Totaal|Aantal ongeldige berichten gelezen uit de gebeurtenis bron|None|
|IngressReceivedBytes|Ontvangen bytes van binnenkomend verkeer|Bytes|Totaal|Aantal gelezen bytes van de gebeurtenis bron|None|
|IngressStoredBytes|In ingangs opgeslagen bytes|Bytes|Totaal|De totale grootte van de gebeurtenissen die zijn verwerkt en beschikbaar voor de query|None|
|IngressStoredEvents|Opgeslagen gebeurtenissen in ingangs|Count|Totaal|Aantal samengevoegde gebeurtenissen dat is verwerkt en beschikbaar is voor query|None|
|IngressReceivedMessagesTimeLag|Tijds vertraging ontvangen inkomende berichten|Waarna|Maximum|Verschil tussen het tijdstip waarop het bericht in de bron van de gebeurtenis in de wachtrij staat en de tijd die wordt verwerkt in ingangs|None|
|IngressReceivedMessagesCountLag|Vertraging ontvangen inkomende berichten van ingang|Count|Average|Verschil tussen het Volg nummer van de laatste bericht in de bron partitie en het Volg nummer van de berichten die worden verwerkt in ingangs gebeurtenissen|None|
|WarmStorageMaxProperties|Maximale eigenschappen van warme opslag|Count|Maximum|Maximum aantal eigenschappen dat is toegestaan door de omgeving voor de SKU van S1/S2 en het maximum aantal eigenschappen dat is toegestaan door de warme Store voor PAYG SKU|None|
|WarmStorageUsedProperties|Eigenschappen voor warme opslag gebruikt |Count|Maximum|Aantal eigenschappen dat wordt gebruikt door de omgeving voor de SKU van S1/S2 en het aantal eigenschappen dat door warme Store voor PAYG SKU wordt gebruikt|None|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Gelezen bytes per seconde|BytesPerSecond|Average|Gemiddelde door Voer van schijf vanwege Lees bewerkingen gedurende de voorbeeld periode.|None|
|DiskWriteBytesPerSecond|Geschreven bytes per seconde|BytesPerSecond|Average|Gemiddelde doorvoer snelheid van schijven vanwege schrijf bewerkingen gedurende de voorbeeld periode.|None|
|Gelezen bytes op de schijf|Gelezen bytes op de schijf|Bytes|Totaal|Totale schijf doorvoer vanwege Lees bewerkingen gedurende de voorbeeld periode.|None|
|Geschreven bytes op de schijf|Geschreven bytes op de schijf|Bytes|Totaal|Totale schijf doorvoer vanwege schrijf bewerkingen gedurende de voorbeeld periode.|None|
|DiskReadOperations|Lees bewerkingen op de schijf|Count|Totaal|Het aantal i/o-Lees bewerkingen in de vorige voorbeeld periode. Houd er rekening mee dat deze bewerkingen variabele grootte kunnen hebben.|None|
|DiskWriteOperations|Schrijf bewerkingen op de schijf|Count|Totaal|Het aantal i/o-schrijf bewerkingen in de vorige voorbeeld periode. Houd er rekening mee dat deze bewerkingen variabele grootte kunnen hebben.|None|
|Lees bewerkingen op de schijf per seconde|Lees bewerkingen op de schijf per seconde|CountPerSecond|Average|Het gemiddelde aantal i/o-Lees bewerkingen in de vorige voorbeeld periode. Houd er rekening mee dat deze bewerkingen variabele grootte kunnen hebben.|None|
|Schrijf bewerkingen op de schijf per seconde|Schrijf bewerkingen op de schijf per seconde|CountPerSecond|Average|Het gemiddelde aantal i/o-schrijf bewerkingen in de vorige voorbeeld periode. Houd er rekening mee dat deze bewerkingen variabele grootte kunnen hebben.|None|
|DiskReadLatency|Lees latentie van schijf|Milliseconden|Average|Totale lees latentie. De som van de lees latentie van het apparaat en de kernel.|None|
|DiskWriteLatency|Schrijf latentie schijf|Milliseconden|Average|Totale schrijf latentie. De som van de latentie voor het schrijven van apparaten en kernels.|None|
|NetworkInBytesPerSecond|Netwerk in bytes per seconde|BytesPerSecond|Average|Gemiddelde netwerk doorvoer voor ontvangen verkeer.|None|
|NetworkOutBytesPerSecond|Netwerk uitgaande bytes per seconde|BytesPerSecond|Average|Gemiddelde netwerk doorvoer voor verzonden verkeer.|None|
|Netwerk in|Netwerk in|Bytes|Totaal|Totale netwerk doorvoer voor ontvangen verkeer.|None|
|Netwerk uit|Netwerk uit|Bytes|Totaal|Totale netwerk doorvoer voor verzonden verkeer.|None|
|MemoryUsed|Gebruikt geheugen|Bytes|Average|De hoeveelheid machine geheugen die wordt gebruikt door de VM.|None|
|MemoryGranted|Toegewezen geheugen|Bytes|Average|De hoeveelheid geheugen die door de host is toegewezen aan de virtuele machine. Er wordt geen geheugen verleend aan de host totdat deze één keer wordt aangevallen en het toegewezen geheugen kan worden uitgewisseld of geballon weg als de VMkernel het geheugen nodig heeft.|None|
|MemoryActive|Actief geheugen|Bytes|Average|De hoeveelheid geheugen die wordt gebruikt door de virtuele machine in het verleden kleine tijd venster. Dit is het ' True ' nummer van de hoeveelheid geheugen die momenteel nodig is voor de VM. Extra, ongebruikt geheugen kan worden uitgewisseld of geballond zonder gevolgen voor de prestaties van de gast.|None|
|Percentage CPU|Percentage CPU|Procent|Average|Het CPU-gebruik. Deze waarde wordt gerapporteerd met 100% voor alle processor kernen op het systeem. Een voor beeld: een twee richtings-VM met 50% van een systeem met vier kern geheugens is volledig met twee kernen.|None|
|PercentageCpuReady|Percentage CPU gereed|Milliseconden|Totaal|Beschik bare tijd is de tijd die nodig is voor het wachten op CPU ('s) in het afgelopen update-interval.|None|










## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CpuPercentage|CPU-percentage|Procent|Average|CPU-percentage|Exemplaar|
|MemoryPercentage|Geheugen percentage|Procent|Average|Geheugen percentage|Exemplaar|
|DiskQueueLength|Wachtrij lengte voor schijf|Count|Average|Wachtrij lengte voor schijf|Exemplaar|
|HttpQueueLength|Lengte van http-wachtrij|Count|Average|Lengte van http-wachtrij|Exemplaar|
|BytesReceived|Gegevens in|Bytes|Totaal|Gegevens in|Exemplaar|
|BytesSent|Gegevens uit|Bytes|Totaal|Gegevens uit|Exemplaar|
|TcpSynSent|TCP SYN verzonden|Count|Average|TCP SYN verzonden|Exemplaar|
|TcpSynReceived|TCP SYN ontvangen|Count|Average|TCP SYN ontvangen|Exemplaar|
|TcpEstablished|TCP-verbinding|Count|Average|TCP-verbinding|Exemplaar|
|TcpFinWait1|TCP-FIN-wacht 1|Count|Average|TCP-FIN-wacht 1|Exemplaar|
|TcpFinWait2|TCP FIN WAIT 2|Count|Average|TCP FIN WAIT 2|Exemplaar|
|TcpClosing|TCP sluiten|Count|Average|TCP sluiten|Exemplaar|
|TcpCloseWait|TCP-wacht tijd voor sluiten|Count|Average|TCP-wacht tijd voor sluiten|Exemplaar|
|TcpLastAck|TCP laatste ACK|Count|Average|TCP laatste ACK|Exemplaar|
|TcpTimeWait|Wacht tijd voor TCP-bewerking|Count|Average|Wacht tijd voor TCP-bewerking|Exemplaar|

## <a name="microsoftwebsites-excluding-functions"></a>Micro soft. web/sites (met uitzonde ring van functies)

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CpuTime|CPU-tijd|Waarna|Totaal|CPU-tijd|Exemplaar|
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
|AverageResponseTime|Gemiddelde reactietijd|Waarna|Average|Gemiddelde reactietijd|Exemplaar|
|AppConnections|Verbindingen|Count|Average|Verbindingen|Exemplaar|
|Formuleer|Aantal ingangen|Count|Average|Aantal ingangen|Exemplaar|
|Lijnen|Aantal threads|Count|Average|Aantal threads|Exemplaar|
|PrivateBytes|Eigen Bytes|Bytes|Average|Eigen Bytes|Exemplaar|
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
|HealthCheckStatus|Status van de status controle|Count|Average|Status van de status controle|Exemplaar|
|FileSystemUsage|Gebruik van bestands systeem|Bytes|Average|Gebruik van bestands systeem|None|

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
|PrivateBytes|Eigen Bytes|Bytes|Average|Eigen Bytes|Exemplaar|
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
|HealthCheckStatus|Status van de status controle|Count|Average|Status van de status controle|Exemplaar|
|FileSystemUsage|Gebruik van bestands systeem|Bytes|Average|Gebruik van bestands systeem|None|


## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CpuTime|CPU-tijd|Waarna|Totaal|CPU-tijd|Exemplaar|
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
|AverageResponseTime|Gemiddelde reactietijd|Waarna|Average|Gemiddelde reactietijd|Exemplaar|
|HttpResponseTime|Reactie tijd|Waarna|Average|Reactie tijd|Exemplaar|
|FunctionExecutionUnits|Eenheden voor functie-uitvoering|Count|Totaal|Eenheden voor functie-uitvoering|Exemplaar|
|FunctionExecutionCount|Aantal functie-uitvoeringen|Count|Totaal|Aantal functie-uitvoeringen|Exemplaar|
|AppConnections|Verbindingen|Count|Average|Verbindingen|Exemplaar|
|Formuleer|Aantal ingangen|Count|Average|Aantal ingangen|Exemplaar|
|Lijnen|Aantal threads|Count|Average|Aantal threads|Exemplaar|
|PrivateBytes|Eigen Bytes|Bytes|Average|Eigen Bytes|Exemplaar|
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
|HealthCheckStatus|Status van de status controle|Count|Average|Status van de status controle|Exemplaar|
|FileSystemUsage|Gebruik van bestands systeem|Bytes|Average|Gebruik van bestands systeem|None|

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
|AverageResponseTime|Gemiddelde reactietijd|Waarna|Average|Gemiddelde reactietijd|Exemplaar|
|CpuPercentage|CPU-percentage|Procent|Average|CPU-percentage|Exemplaar|
|MemoryPercentage|Geheugen percentage|Procent|Average|Geheugen percentage|Exemplaar|
|DiskQueueLength|Wachtrij lengte voor schijf|Count|Average|Wachtrij lengte voor schijf|Exemplaar|
|HttpQueueLength|Lengte van http-wachtrij|Count|Average|Lengte van http-wachtrij|Exemplaar|
|ActiveRequests|Actieve aanvragen|Count|Totaal|Actieve aanvragen|Exemplaar|
|TotalFrontEnds|Totale front-ends|Count|Average|Totale front-ends|None|
|SmallAppServicePlanInstances|Werk rollen voor kleine App Service plannen|Count|Average|Werk rollen voor kleine App Service plannen|None|
|MediumAppServicePlanInstances|Werk nemers met gemiddeld App Service plannen|Count|Average|Werk nemers met gemiddeld App Service plannen|None|
|LargeAppServicePlanInstances|Werk rollen voor grote App Service plannen|Count|Average|Werk rollen voor grote App Service plannen|None|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Gegevens|De naam van de metrische gegevens weergeven|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|WorkersTotal|Totaal aantal werk rollen|Count|Average|Totaal aantal werk rollen|None|
|WorkersAvailable|Beschik bare werk nemers|Count|Average|Beschik bare werk nemers|None|
|WorkersUsed|Gebruikte werk rollen|Count|Average|Gebruikte werk rollen|None|
|CpuPercentage|CPU-percentage|Procent|Average|CPU-percentage|Exemplaar|
|MemoryPercentage|Geheugen percentage|Procent|Average|Geheugen percentage|Exemplaar|
## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over metrische gegevens in Azure Monitor](data-platform.md)
* [Waarschuwingen maken voor metrische gegevens](alerts-overview.md)
* [Metrische gegevens exporteren naar opslag, Event hub of Log Analytics](platform-logs-overview.md)
