---
title: Azure Monitor ondersteunde metrische gegevens per resource type
description: Een lijst met metrische gegevens die beschikbaar zijn voor elk resource type met Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 04/06/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 3d7ab9d4e7a7c560fa05bdc06c7d1c357a2c2767
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83196618"
---
# <a name="supported-metrics-with-azure-monitor"></a>Ondersteunde metrische gegevens met Azure Monitor

> [!NOTE]
> Deze lijst wordt grotendeels automatisch gegenereerd op basis van de Azure Monitor meet waarden REST API. Alle wijzigingen die in deze lijst via github zijn aangebracht, kunnen zonder waarschuwing worden geschreven. Neem contact op met de auteur van dit artikel voor meer informatie over het maken van permanente updates.

Azure Monitor biedt verschillende manieren om te communiceren met metrische gegevens, zoals het maken van grafieken in de portal, het openen ervan via de REST API of het opvragen van query's via Power shell of CLI. 

Dit artikel is een volledige lijst met alle platformen (dat wil zeggen, automatisch verzamelde) gegevens die momenteel beschikbaar zijn met de geconsolideerde metrische pijp lijn van Azure Monitor. De lijst is voor het laatst bijgewerkt op 27 maart 2020. De metrische gegevens die na deze datum zijn gewijzigd of toegevoegd, worden mogelijk niet hieronder weer gegeven. Als u de lijst met metrische gegevens programmatisch wilt opvragen en openen, gebruikt u de [2018-01-01 API-versie](https://docs.microsoft.com/rest/api/monitor/metricdefinitions). Andere metrische gegevens die niet in deze lijst staan, zijn mogelijk niet beschikbaar in de portal of met behulp van verouderde Api's.

De metrische gegevens zijn geordend op resource providers en resource type. Zie [resource providers voor Azure-Services](../../azure-resource-manager/management/azure-services-resource-providers.md)voor een lijst met Services en de resource providers die bij hen horen. 


## <a name="guest-os-metrics"></a>Metrische gegevens van het gast besturingssysteem

De metrische gegevens voor het gast besturingssysteem (gast besturingssysteem) die worden uitgevoerd in azure Virtual Machines, Service Fabric en Cloud Services, worden hier **niet** weer gegeven. In plaats daarvan moeten de metrische gegevens voor het gast besturingssysteem worden verzameld via de een of meer agents die worden uitgevoerd op of als onderdeel van het gast besturingssysteem.  De metrische gegevens voor het gast besturingssysteem zijn onder andere prestatie meter items die het CPU-percentage van de gast of het geheugen gebruik bijhouden, die vaak worden gebruikt voor automatisch schalen of waarschuwing.  Met de [uitbrei ding](diagnostics-extension-overview.md)voor de Azure Diagnostics kunt u metrische gegevens voor het gast besturingssysteem prestaties verzenden naar dezelfde data base waar de metrische gegevens van het platform worden opgeslagen. Hiermee worden metrische gegevens van het gast besturingssysteem gerouteerd via de API voor [aangepaste metrische gegevens](metrics-custom-overview.md) . Vervolgens kunt u een grafiek, waarschuwing en andere metrische gegevens van het gast besturingssysteem gebruiken, zoals de metrische gegevens van het platform. Zie [overzicht van bewakings agenten](agents-overview.md)voor meer informatie.    

## <a name="routing-platform-metrics-to-other-locations"></a>Gegevens van het routerings platform naar andere locaties

U kunt [Diagnostische instellingen](diagnostic-settings.md) gebruiken om platform metrieken te routeren naar Azure Storage, Azure monitor Logboeken (en dus log Analytics) en Event hubs.  

Er zijn enkele beperkingen in wat kan worden gerouteerd en het formulier waarin ze zijn opgeslagen. 
- Niet alle metrische gegevens kunnen worden geëxporteerd naar andere locaties. Raadpleeg [dit artikel](metrics-supported-export-diagnostic-settings.md)voor een lijst met platform metrische gegevens die kunnen worden geëxporteerd via Diagnostische instellingen.

- Het verzenden van multi-dimensionale metrische gegevens naar andere locaties via Diagnostische instellingen wordt momenteel niet ondersteund. Metrische gegevens met dimensies worden geëxporteerd als platte eendimensionale metrische gegevens, als totaal van alle dimensiewaarden.
*Een voorbeeld*: de meetwaarde 'Binnenkomende berichten' voor een Event Hub kan worden verkend en uitgezet op wachtrijniveau. Wanneer de waarde wordt geëxporteerd via diagnostische instellingen, wordt deze echter voorgesteld als alle binnenkomende berichten voor alle wachtrijen in de Event Hub.


## <a name="microsoftanalysisservicesservers"></a>Micro soft. AnalysisServices/servers

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|qpu_metric|QPU|Aantal|Average|QPU. Bereik 0-100 voor S1, 0-200 voor S2 en 0-400 voor S4|ServerResourceType|
|memory_metric|Geheugen|Bytes|Average|Geheugen. Bereik 0-25 GB voor S1, 0-50 GB voor S2 en 0-100 GB voor S4|ServerResourceType|
|private_bytes_metric|Privé-bytes|Bytes|Average|Privé-bytes.|ServerResourceType|
|virtual_bytes_metric|Virtuele bytes|Bytes|Average|Virtuele bytes.|ServerResourceType|
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
|QueryPoolJobQueueLength|Threads: lengte van van de taak wachtrij van de query pool|Aantal|Average|Aantal taken in de wachtrij van de query thread pool.|ServerResourceType|
|ShortParsingBusyThreads|Threads: bezette threads voor kort parseren|Aantal|Average|Het aantal actieve threads in de thread pool voor kort parseren.|ServerResourceType|
|ShortParsingIdleThreads|Threads: niet-actieve threads voor kort parseren|Aantal|Average|Het aantal niet-actieve threads in de thread pool voor kort parseren.|ServerResourceType|
|ShortParsingJobQueueLength|Threads: lengte van taak wachtrij voor kort parseren|Aantal|Average|Aantal taken in de wachtrij van de thread pool voor kort parseren.|ServerResourceType|
|memory_thrashing_metric|Geheugenthrashing|Percentage|Average|Gemiddeld geheugen overbelasting.|ServerResourceType|
|mashup_engine_qpu_metric|M-engine QPU|Aantal|Average|QPU-gebruik door mashup-engine processen|ServerResourceType|
|mashup_engine_memory_metric|M-engine geheugen|Bytes|Average|Geheugen gebruik door mashup-engine processen|ServerResourceType|
|mashup_engine_private_bytes_metric|M-engine-eigen bytes|Bytes|Average|Privé-bytes gebruik door mashup-engine processen.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|M-engine virtuele bytes|Bytes|Average|Gebruik van virtuele bytes door mashup-engine processen.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TotalRequests|Totaal aantal gateway aanvragen (afgeschaft)|Aantal|Totaal|Aantal gateway aanvragen-metrische aanvraag voor meerdere dimensies met GatewayResponseCodeCategory dimensie gebruiken|Locatie, hostnaam|
|SuccessfulRequests|Geslaagde gateway aanvragen (afgeschaft)|Aantal|Totaal|Aantal geslaagde gateway aanvragen-metrische aanvraag voor meerdere dimensies met GatewayResponseCodeCategory dimensie gebruiken|Locatie, hostnaam|
|UnauthorizedRequests|Niet-geautoriseerde gateway aanvragen (afgeschaft)|Aantal|Totaal|Aantal niet-geautoriseerde gateway aanvragen-metrische aanvraag voor meerdere dimensies met GatewayResponseCodeCategory dimensie gebruiken|Locatie, hostnaam|
|FailedRequests|Mislukte gateway aanvragen (afgeschaft)|Aantal|Totaal|Aantal fouten in gateway aanvragen-metrische aanvraag voor meerdere dimensies met GatewayResponseCodeCategory dimensie gebruiken|Locatie, hostnaam|
|OtherRequests|Andere gateway aanvragen (afgeschaft)|Aantal|Totaal|Aantal andere gateway aanvragen-metrische aanvraag voor meerdere dimensies met GatewayResponseCodeCategory dimensie gebruiken|Locatie, hostnaam|
|Duur|Totale duur van gateway aanvragen|Milliseconden|Average|Totale duur van gateway aanvragen in milliseconden|Locatie, hostnaam|
|BackendDuration|Duur van back-end-aanvragen|Milliseconden|Average|Duur van back-end-aanvragen in milliseconden|Locatie, hostnaam|
|Capaciteit|Capaciteit|Percentage|Average|De metrische gegevens over het gebruik van de ApiManagement-service|Locatie|
|EventHubTotalEvents|Totaal aantal EventHub-gebeurtenissen|Aantal|Totaal|Aantal gebeurtenissen dat naar EventHub is verzonden|Locatie|
|EventHubSuccessfulEvents|Geslaagde EventHub-gebeurtenissen|Aantal|Totaal|Aantal geslaagde EventHub-gebeurtenissen|Locatie|
|EventHubTotalFailedEvents|Mislukte EventHub-gebeurtenissen|Aantal|Totaal|Aantal mislukte EventHub-gebeurtenissen|Locatie|
|EventHubRejectedEvents|Geweigerde EventHub-gebeurtenissen|Aantal|Totaal|Aantal geweigerde EventHub-gebeurtenissen (onjuiste configuratie of niet-geautoriseerd)|Locatie|
|EventHubThrottledEvents|Vertraagde EventHub-gebeurtenissen|Aantal|Totaal|Aantal vertraagde EventHub-gebeurtenissen|Locatie|
|EventHubTimedoutEvents|Time-out EventHub-gebeurtenissen|Aantal|Totaal|Aantal time-out EventHub-gebeurtenissen|Locatie|
|EventHubDroppedEvents|Verwijderde EventHub-gebeurtenissen|Aantal|Totaal|Het aantal gebeurtenissen dat is overgeslagen omdat de maximale grootte van de wachtrij is bereikt|Locatie|
|EventHubTotalBytesSent|Grootte van EventHub-gebeurtenissen|Bytes|Totaal|Totale grootte van EventHub-gebeurtenissen in bytes|Locatie|
|Aanvragen|Aanvragen|Aantal|Totaal|Metrische gegevens van gateway aanvragen met meerdere dimensies|Location, hostname, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|NetworkConnectivity|Status van de netwerk verbinding van resources (preview-versie)|Aantal|Totaal|Status van de netwerk verbinding van de afhankelijke bron typen van de API Management-service|Locatie, resource type|


## <a name="microsoftappconfigurationconfigurationstores"></a>Micro soft. AppConfiguration/configurationStores

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Aantal|Aantal|Totaal aantal binnenkomende HTTP-aanvragen.|Status code|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|Aantal|Average|Latentie voor een HTTP-aanvraag.|Status code|


## <a name="microsoftappplatformspring"></a>Micro soft. AppPlatform/lente

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SystemCpuUsagePercentage|Percentage van het CPU-gebruik van het systeem|Percentage|Average|Het recente CPU-gebruik voor het hele systeem|AppName, pod|
|AppCpuUsagePercentage|CPU-gebruiks percentage van app|Percentage|Average|JVM CPU-gebruiks percentage van app|AppName, pod|
|AppMemoryCommitted|Toegewezen app-geheugen|Bytes|Average|Geheugen toegewezen aan JVM in bytes|AppName, pod|
|AppMemoryUsed|Gebruikt app-geheugen|Bytes|Average|Gebruikt app-geheugen in bytes|AppName, pod|
|AppMemoryMax|Maxi maal app-geheugen|Bytes|Maximum|De maximale hoeveelheid geheugen in bytes die kan worden gebruikt voor geheugen beheer|AppName, pod|
|MaxOldGenMemoryPoolBytes|Maxi maal beschik bare oude generatie gegevens grootte|Bytes|Average|Maximale grootte van de geheugen groep voor de oude generatie|AppName, pod|
|OldGenMemoryPoolBytes|Gegevens grootte van oude generatie|Bytes|Average|Grootte van de geheugen groep van de oude generatie na een volledige GC|AppName, pod|
|OldGenPromotedBytes|Promo veren tot oude generatie gegevens grootte|Bytes|Maximum|Aantal positieve toename van de grootte van de oude generatie geheugengroep vóór GC tot na GC|AppName, pod|
|YoungGenPromotedBytes|Promo veren tot jonge generatie gegevens grootte|Bytes|Maximum|Verhoogd voor een toename van de grootte van de Memory pool voor de jonge generatie na een GC tot de volgende|AppName, pod|
|GCPauseTotalCount|Aantal GC-onderbrekingen|Aantal|Totaal|Aantal GC-onderbrekingen|AppName, pod|
|GCPauseTotalTime|Totale tijd van de GC-onderbreking|Milliseconden|Totaal|Totale tijd van de GC-onderbreking|AppName, pod|
|TomcatSentBytes|Totaal aantal verzonden bytes in Tomcat|Bytes|Totaal|Totaal aantal verzonden bytes in Tomcat|AppName, pod|
|TomcatReceivedBytes|Totaal aantal bytes ontvangen Tomcat|Bytes|Totaal|Totaal aantal bytes ontvangen Tomcat|AppName, pod|
|TomcatRequestTotalTime|Totaal aantal keer Tomcat-aanvragen|Milliseconden|Totaal|Totaal aantal keer Tomcat-aanvragen|AppName, pod|
|TomcatRequestTotalCount|Totaal aantal Tomcat-aanvragen|Aantal|Totaal|Totaal aantal Tomcat-aanvragen|AppName, pod|
|TomcatResponseAvgTime|Gemiddelde tijd Tomcat-aanvraag|Milliseconden|Average|Gemiddelde tijd Tomcat-aanvraag|AppName, pod|
|TomcatRequestMaxTime|Maximale tijd voor tomcat-aanvraag|Milliseconden|Maximum|Maximale tijd voor tomcat-aanvraag|AppName, pod|
|TomcatErrorCount|Tomcat Global-fout|Aantal|Totaal|Tomcat Global-fout|AppName, pod|
|TomcatSessionActiveMaxCount|Aantal actieve Tomcat-sessies|Aantal|Totaal|Aantal actieve Tomcat-sessies|AppName, pod|
|TomcatSessionAliveMaxTime|Time-outperiode van Tomcat-sessie|Milliseconden|Maximum|Time-outperiode van Tomcat-sessie|AppName, pod|
|TomcatSessionActiveCurrentCount|Aantal Tomcat-sessies|Aantal|Totaal|Aantal Tomcat-sessies|AppName, pod|
|TomcatSessionCreatedCount|Aantal gemaakte Tomcat-sessies|Aantal|Totaal|Aantal gemaakte Tomcat-sessies|AppName, pod|
|TomcatSessionExpiredCount|Aantal verlopen Tomcat-sessies|Aantal|Totaal|Aantal verlopen Tomcat-sessies|AppName, pod|
|TomcatSessionRejectedCount|Aantal geweigerde Tomcat-sessies|Aantal|Totaal|Aantal geweigerde Tomcat-sessies|AppName, pod|


## <a name="microsoftautomationautomationaccounts"></a>Micro soft. Automation/automationAccounts

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TotalJob|Totaal aantal taken|Aantal|Totaal|Het totale aantal taken|Runbook, status|
|TotalUpdateDeploymentRuns|Totaal aantal uitvoeringen van update-implementaties|Aantal|Totaal|Totale aantal uitgevoerde software-update-implementaties|SoftwareUpdateConfigurationName, status|
|TotalUpdateDeploymentMachineRuns|Totaal aantal machine-uitvoeringen van update-implementaties|Aantal|Totaal|Het totale aantal uitgevoerde software-update-implementatie computers in een software-update-implementatie|SoftwareUpdateConfigurationName, status, target computer, SoftwareUpdateConfigurationRunId|


## <a name="microsoftbatchbatchaccounts"></a>Micro soft. batch/batchAccounts

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CoreCount|Aantal toegewezen kernen|Aantal|Totaal|Totaal aantal toegewezen kernen in het batch-account|Geen|
|TotalNodeCount|Aantal toegewezen knoop punten|Aantal|Totaal|Totaal aantal toegewezen knoop punten in het batch-account|Geen|
|LowPriorityCoreCount|Aantal LowPriority kernen|Aantal|Totaal|Totaal aantal kernen met lage prioriteit in het batch-account|Geen|
|TotalLowPriorityNodeCount|Aantal knoop punten met een lage prioriteit|Aantal|Totaal|Totaal aantal knoop punten met een lage prioriteit in het batch-account|Geen|
|CreatingNodeCount|Aantal knoop punten maken|Aantal|Totaal|Aantal knoop punten dat wordt gemaakt|Geen|
|StartingNodeCount|Begin aantal knoop punten|Aantal|Totaal|Aantal knoop punten dat begint|Geen|
|WaitingForStartTaskNodeCount|Wachten op aantal begin taak knooppunten|Aantal|Totaal|Aantal knoop punten dat wacht tot de begin taak is voltooid|Geen|
|StartTaskFailedNodeCount|Aantal mislukte knoop punten van begin taak|Aantal|Totaal|Aantal knoop punten waarop de begin taak is mislukt|Geen|
|IdleNodeCount|Aantal niet-actieve knoop punten|Aantal|Totaal|Aantal niet-actieve knoop punten|Geen|
|OfflineNodeCount|Aantal offline knooppunten|Aantal|Totaal|Aantal offline knooppunten|Geen|
|RebootingNodeCount|Aantal knoop punten opnieuw opstarten|Aantal|Totaal|Aantal knoop punten dat opnieuw wordt opgestart|Geen|
|ReimagingNodeCount|Telling van het aantal knoop punten|Aantal|Totaal|Aantal knoop punten van installatie kopieën|Geen|
|RunningNodeCount|Aantal actieve knoop punten|Aantal|Totaal|Aantal actieve knoop punten|Geen|
|LeavingPoolNodeCount|Aantal groeps knooppunten verlaten|Aantal|Totaal|Aantal knoop punten dat de pool verlaat|Geen|
|UnusableNodeCount|Aantal niet-bruikbare knoop punten|Aantal|Totaal|Aantal niet-bruikbare knoop punten|Geen|
|PreemptedNodeCount|Aantal knoop punten in herhaling|Aantal|Totaal|Aantal knoop punten dat is afgebroken|Geen|
|TaskStartEvent|Taak begin gebeurtenissen|Aantal|Totaal|Totaal aantal taken dat is gestart|poolId, jobId|
|TaskCompleteEvent|Taak voltooid gebeurtenissen|Aantal|Totaal|Totaal aantal taken dat is voltooid|poolId, jobId|
|TaskFailEvent|Taak fout gebeurtenissen|Aantal|Totaal|Totaal aantal taken dat is voltooid met de status mislukt|poolId, jobId|
|PoolCreateEvent|Groeps gebeurtenissen maken|Aantal|Totaal|Totaal aantal Pools dat is gemaakt|poolId|
|PoolResizeStartEvent|Begin gebeurtenissen van groeps grootte wijzigen|Aantal|Totaal|Totaal aantal hergroottes van de groep die zijn gestart|poolId|
|PoolResizeCompleteEvent|Volledige gebeurtenissen voor het wijzigen van de pool|Aantal|Totaal|Totaal aantal hergroottes van de groep die zijn voltooid|poolId|
|PoolDeleteStartEvent|Begin gebeurtenissen groep verwijderen|Aantal|Totaal|Totaal aantal verwijderde groepen dat is gestart|poolId|
|PoolDeleteCompleteEvent|Voltooide gebeurtenissen van groep verwijderen|Aantal|Totaal|Totaal aantal verwijderde groepen dat is voltooid|poolId|
|JobDeleteCompleteEvent|Voltooide gebeurtenissen van taak verwijderen|Aantal|Totaal|Het totale aantal taken dat is verwijderd.|jobId|
|JobDeleteStartEvent|Taak begin gebeurtenissen verwijderen|Aantal|Totaal|Het totale aantal taken dat is aangevraagd om te worden verwijderd.|jobId|
|JobDisableCompleteEvent|Voltooide gebeurtenissen voor taak uitschakelen|Aantal|Totaal|Het totale aantal taken dat is uitgeschakeld.|jobId|
|JobDisableStartEvent|Taak start gebeurtenissen uitschakelen|Aantal|Totaal|Het totale aantal taken dat is aangevraagd om te worden uitgeschakeld.|jobId|
|JobStartEvent|Taak begin gebeurtenissen|Aantal|Totaal|Het totale aantal taken dat is gestart.|jobId|
|JobTerminateCompleteEvent|Voltooide gebeurtenissen voor taak beëindigen|Aantal|Totaal|Het totale aantal taken dat is beëindigd.|jobId|
|JobTerminateStartEvent|Taak start gebeurtenissen beëindigen|Aantal|Totaal|Het totale aantal taken dat is aangevraagd om te worden beëindigd.|jobId|


## <a name="microsoftbatchaiworkspaces"></a>Micro soft. BatchAI/werk ruimten

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Taak verzonden|Taak verzonden|Aantal|Totaal|Aantal verzonden taken|Scenario, clustername|
|Taak is voltooid|Taak is voltooid|Aantal|Totaal|Aantal voltooide taken|Scenario, clustername, ResultType|
|Totaal aantal knoop punten|Totaal aantal knoop punten|Aantal|Average|Totaal aantal knoop punten|Scenario, clustername|
|Actieve knoop punten|Actieve knoop punten|Aantal|Average|Aantal actieve knoop punten|Scenario, clustername|
|Niet-actieve knoop punten|Niet-actieve knoop punten|Aantal|Average|Aantal niet-actieve knoop punten|Scenario, clustername|
|Niet-bruikbare knoop punten|Niet-bruikbare knoop punten|Aantal|Average|Aantal niet-bruikbare knoop punten|Scenario, clustername|
|Knoop punten die zijn afgebroken|Knoop punten die zijn afgebroken|Aantal|Average|Aantal knoop punten dat is afgebroken|Scenario, clustername|
|Knoop punten verlaten|Knoop punten verlaten|Aantal|Average|Aantal verlaatde knoop punten|Scenario, clustername|
|Totaal aantal kernen|Totaal aantal kernen|Aantal|Average|Aantal totale kernen|Scenario, clustername|
|Actieve kernen|Actieve kernen|Aantal|Average|Aantal actieve kernen|Scenario, clustername|
|Niet-actieve kernen|Niet-actieve kernen|Aantal|Average|Aantal niet-actieve kern geheugens|Scenario, clustername|
|Onbruikbaar aantal kern geheugens|Onbruikbaar aantal kern geheugens|Aantal|Average|Aantal niet-bruikbare kernen|Scenario, clustername|
|Afgebroken kernen|Afgebroken kernen|Aantal|Average|Aantal afgebroken kernen|Scenario, clustername|
|Kernen verlaten|Kernen verlaten|Aantal|Average|Aantal te verlaten kernen|Scenario, clustername|
|Percentage quotum gebruik|Percentage quotum gebruik|Aantal|Average|Percentage van gebruikte quota|Scenario, clustername, VmFamilyName, VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Micro soft. Block Chain/blockchainMembers

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|Percentage CPU-gebruik|Percentage|Maximum|Percentage CPU-gebruik|Knooppunt|
|MemoryUsage|Geheugengebruik|Bytes|Average|Geheugengebruik|Knooppunt|
|Memory limit|Geheugen limiet|Bytes|Average|Geheugen limiet|Knooppunt|
|MemoryUsagePercentageInDouble|Percentage geheugen gebruik|Percentage|Average|Percentage geheugen gebruik|Knooppunt|
|StorageUsage|Opslag gebruik|Bytes|Average|Opslag gebruik|Knooppunt|
|IOReadBytes|I/o gelezen bytes|Bytes|Totaal|I/o gelezen bytes|Knooppunt|
|IOWriteBytes|I/o-schrijf bytes|Bytes|Totaal|I/o-schrijf bytes|Knooppunt|
|ConnectionAccepted|Geaccepteerde verbindingen|Aantal|Totaal|Geaccepteerde verbindingen|Knooppunt|
|ConnectionHandled|Afgehandelde verbindingen|Aantal|Totaal|Afgehandelde verbindingen|Knooppunt|
|ConnectionActive|Actieve verbindingen|Aantal|Average|Actieve verbindingen|Knooppunt|
|RequestHandled|Verwerkte aanvragen|Aantal|Totaal|Verwerkte aanvragen|Knooppunt|
|ProcessedBlocks|Verwerkte blokken|Aantal|Totaal|Verwerkte blokken|Knooppunt|
|ProcessedTransactions|Verwerkte trans acties|Aantal|Totaal|Verwerkte trans acties|Knooppunt|
|PendingTransactions|Trans acties in behandeling|Aantal|Average|Trans acties in behandeling|Knooppunt|
|QueuedTransactions|Trans acties in de wachtrij|Aantal|Average|Trans acties in de wachtrij|Knooppunt|



## <a name="microsoftcacheredis"></a>Micro soft. cache/redis

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|connectedclients|Verbonden clients|Aantal|Maximum||ShardId|
|totalcommandsprocessed|Totaalaantal bewerkingen|Aantal|Totaal||ShardId|
|cachehits|Cachetreffers|Aantal|Totaal||ShardId|
|cachemisses|Cachemissers|Aantal|Totaal||ShardId|
|cachemissrate|Aantal missers in cache|Percentage|cachemissrate||ShardId|
|getcommands|Ophalingen|Aantal|Totaal||ShardId|
|setcommands|Instellingen|Aantal|Totaal||ShardId|
|operationsPerSecond|Bewerkingen per seconde|Aantal|Maximum||ShardId|
|evictedkeys|Verwijderde sleutels|Aantal|Totaal||ShardId|
|totalkeys|Totaal aantal sleutels|Aantal|Maximum||ShardId|
|expiredkeys|Verlopen sleutels|Aantal|Totaal||ShardId|
|usedmemory|Gebruikt geheugen|Bytes|Maximum||ShardId|
|usedmemorypercentage|Percentage gebruikt geheugen|Percentage|Maximum||ShardId|
|usedmemoryRss|Gebruikte geheugen-RSS|Bytes|Maximum||ShardId|
|serverLoad|Server belasting|Percentage|Maximum||ShardId|
|cacheWrite|Cache schrijven|BytesPerSecond|Maximum||ShardId|
|cacheRead|Lees bewerking in cache|BytesPerSecond|Maximum||ShardId|
|percentProcessorTime|CPU|Percentage|Maximum||ShardId|
|cacheLatency|Cache latentie micro seconden (preview-versie)|Aantal|Average||ShardId|
|fouten|Fouten|Aantal|Maximum||ShardId, error type|
|connectedclients0|Verbonden clients (Shard 0)|Aantal|Maximum||Geen|
|totalcommandsprocessed0|Totaal aantal bewerkingen (Shard 0)|Aantal|Totaal||Geen|
|cachehits0|Cache treffers (Shard 0)|Aantal|Totaal||Geen|
|cachemisses0|Cache missers (Shard 0)|Aantal|Totaal||Geen|
|getcommands0|Hiermee wordt opgehaald (Shard 0)|Aantal|Totaal||Geen|
|setcommands0|Sets (Shard 0)|Aantal|Totaal||Geen|
|operationsPerSecond0|Bewerkingen per seconde (Shard 0)|Aantal|Maximum||Geen|
|evictedkeys0|Verwijderde sleutels (Shard 0)|Aantal|Totaal||Geen|
|totalkeys0|Totaal aantal sleutels (Shard 0)|Aantal|Maximum||Geen|
|expiredkeys0|Verlopen sleutels (Shard 0)|Aantal|Totaal||Geen|
|usedmemory0|Gebruikt geheugen (Shard 0)|Bytes|Maximum||Geen|
|usedmemoryRss0|Gebruikt geheugen RSS (Shard 0)|Bytes|Maximum||Geen|
|serverLoad0|Server belasting (Shard 0)|Percentage|Maximum||Geen|
|cacheWrite0|Cache schrijven (Shard 0)|BytesPerSecond|Maximum||Geen|
|cacheRead0|Cache gelezen (Shard 0)|BytesPerSecond|Maximum||Geen|
|percentProcessorTime0|CPU (Shard 0)|Percentage|Maximum||Geen|
|connectedclients1|Verbonden clients (Shard 1)|Aantal|Maximum||Geen|
|totalcommandsprocessed1|Totaal aantal bewerkingen (Shard 1)|Aantal|Totaal||Geen|
|cachehits1|Cache treffers (Shard 1)|Aantal|Totaal||Geen|
|cachemisses1|Cache missers (Shard 1)|Aantal|Totaal||Geen|
|getcommands1|Hiermee wordt opgehaald (Shard 1)|Aantal|Totaal||Geen|
|setcommands1|Sets (Shard 1)|Aantal|Totaal||Geen|
|operationsPerSecond1|Bewerkingen per seconde (Shard 1)|Aantal|Maximum||Geen|
|evictedkeys1|Verwijderde sleutels (Shard 1)|Aantal|Totaal||Geen|
|totalkeys1|Totaal aantal sleutels (Shard 1)|Aantal|Maximum||Geen|
|expiredkeys1|Verlopen sleutels (Shard 1)|Aantal|Totaal||Geen|
|usedmemory1|Gebruikt geheugen (Shard 1)|Bytes|Maximum||Geen|
|usedmemoryRss1|Gebruikte geheugen RSS (Shard 1)|Bytes|Maximum||Geen|
|serverLoad1|Server belasting (Shard 1)|Percentage|Maximum||Geen|
|cacheWrite1|Cache schrijven (Shard 1)|BytesPerSecond|Maximum||Geen|
|cacheRead1|Lees bewerking in cache (Shard 1)|BytesPerSecond|Maximum||Geen|
|percentProcessorTime1|CPU (Shard 1)|Percentage|Maximum||Geen|
|connectedclients2|Verbonden clients (Shard 2)|Aantal|Maximum||Geen|
|totalcommandsprocessed2|Totaal aantal bewerkingen (Shard 2)|Aantal|Totaal||Geen|
|cachehits2|Cache treffers (Shard 2)|Aantal|Totaal||Geen|
|cachemisses2|Cache missers (Shard 2)|Aantal|Totaal||Geen|
|getcommands2|Hiermee wordt opgehaald (Shard 2)|Aantal|Totaal||Geen|
|setcommands2|Sets (Shard 2)|Aantal|Totaal||Geen|
|operationsPerSecond2|Bewerkingen per seconde (Shard 2)|Aantal|Maximum||Geen|
|evictedkeys2|Verwijderde sleutels (Shard 2)|Aantal|Totaal||Geen|
|totalkeys2|Totaal aantal sleutels (Shard 2)|Aantal|Maximum||Geen|
|expiredkeys2|Verlopen sleutels (Shard 2)|Aantal|Totaal||Geen|
|usedmemory2|Gebruikt geheugen (Shard 2)|Bytes|Maximum||Geen|
|usedmemoryRss2|Gebruikte geheugen RSS (Shard 2)|Bytes|Maximum||Geen|
|serverLoad2|Server belasting (Shard 2)|Percentage|Maximum||Geen|
|cacheWrite2|Cache schrijven (Shard 2)|BytesPerSecond|Maximum||Geen|
|cacheRead2|Lees bewerking in cache (Shard 2)|BytesPerSecond|Maximum||Geen|
|percentProcessorTime2|CPU (Shard 2)|Percentage|Maximum||Geen|
|connectedclients3|Verbonden clients (Shard 3)|Aantal|Maximum||Geen|
|totalcommandsprocessed3|Totaal aantal bewerkingen (Shard 3)|Aantal|Totaal||Geen|
|cachehits3|Cache treffers (Shard 3)|Aantal|Totaal||Geen|
|cachemisses3|Cache missers (Shard 3)|Aantal|Totaal||Geen|
|getcommands3|Hiermee wordt opgehaald (Shard 3)|Aantal|Totaal||Geen|
|setcommands3|Sets (Shard 3)|Aantal|Totaal||Geen|
|operationsPerSecond3|Bewerkingen per seconde (Shard 3)|Aantal|Maximum||Geen|
|evictedkeys3|Verwijderde sleutels (Shard 3)|Aantal|Totaal||Geen|
|totalkeys3|Totaal aantal sleutels (Shard 3)|Aantal|Maximum||Geen|
|expiredkeys3|Verlopen sleutels (Shard 3)|Aantal|Totaal||Geen|
|usedmemory3|Gebruikt geheugen (Shard 3)|Bytes|Maximum||Geen|
|usedmemoryRss3|Gebruikte geheugen RSS (Shard 3)|Bytes|Maximum||Geen|
|serverLoad3|Server belasting (Shard 3)|Percentage|Maximum||Geen|
|cacheWrite3|Cache schrijven (Shard 3)|BytesPerSecond|Maximum||Geen|
|cacheRead3|Lees bewerking in cache (Shard 3)|BytesPerSecond|Maximum||Geen|
|percentProcessorTime3|CPU (Shard 3)|Percentage|Maximum||Geen|
|connectedclients4|Verbonden clients (Shard 4)|Aantal|Maximum||Geen|
|totalcommandsprocessed4|Totaal aantal bewerkingen (Shard 4)|Aantal|Totaal||Geen|
|cachehits4|Cache treffers (Shard 4)|Aantal|Totaal||Geen|
|cachemisses4|Cache missers (Shard 4)|Aantal|Totaal||Geen|
|getcommands4|Hiermee wordt opgehaald (Shard 4)|Aantal|Totaal||Geen|
|setcommands4|Sets (Shard 4)|Aantal|Totaal||Geen|
|operationsPerSecond4|Bewerkingen per seconde (Shard 4)|Aantal|Maximum||Geen|
|evictedkeys4|Verwijderde sleutels (Shard 4)|Aantal|Totaal||Geen|
|totalkeys4|Totaal aantal sleutels (Shard 4)|Aantal|Maximum||Geen|
|expiredkeys4|Verlopen sleutels (Shard 4)|Aantal|Totaal||Geen|
|usedmemory4|Gebruikt geheugen (Shard 4)|Bytes|Maximum||Geen|
|usedmemoryRss4|Gebruikte geheugen RSS (Shard 4)|Bytes|Maximum||Geen|
|serverLoad4|Server belasting (Shard 4)|Percentage|Maximum||Geen|
|cacheWrite4|Cache schrijven (Shard 4)|BytesPerSecond|Maximum||Geen|
|cacheRead4|Lees bewerking in cache (Shard 4)|BytesPerSecond|Maximum||Geen|
|percentProcessorTime4|CPU (Shard 4)|Percentage|Maximum||Geen|
|connectedclients5|Verbonden clients (Shard 5)|Aantal|Maximum||Geen|
|totalcommandsprocessed5|Totaal aantal bewerkingen (Shard 5)|Aantal|Totaal||Geen|
|cachehits5|Cache treffers (Shard 5)|Aantal|Totaal||Geen|
|cachemisses5|Cache missers (Shard 5)|Aantal|Totaal||Geen|
|getcommands5|Hiermee wordt opgehaald (Shard 5)|Aantal|Totaal||Geen|
|setcommands5|Sets (Shard 5)|Aantal|Totaal||Geen|
|operationsPerSecond5|Bewerkingen per seconde (Shard 5)|Aantal|Maximum||Geen|
|evictedkeys5|Verwijderde sleutels (Shard 5)|Aantal|Totaal||Geen|
|totalkeys5|Totaal aantal sleutels (Shard 5)|Aantal|Maximum||Geen|
|expiredkeys5|Verlopen sleutels (Shard 5)|Aantal|Totaal||Geen|
|usedmemory5|Gebruikt geheugen (Shard 5)|Bytes|Maximum||Geen|
|usedmemoryRss5|Gebruikte geheugen RSS (Shard 5)|Bytes|Maximum||Geen|
|serverLoad5|Server belasting (Shard 5)|Percentage|Maximum||Geen|
|cacheWrite5|Cache schrijven (Shard 5)|BytesPerSecond|Maximum||Geen|
|cacheRead5|Lees bewerking in cache (Shard 5)|BytesPerSecond|Maximum||Geen|
|percentProcessorTime5|CPU (Shard 5)|Percentage|Maximum||Geen|
|connectedclients6|Verbonden clients (Shard 6)|Aantal|Maximum||Geen|
|totalcommandsprocessed6|Totaal aantal bewerkingen (Shard 6)|Aantal|Totaal||Geen|
|cachehits6|Cache treffers (Shard 6)|Aantal|Totaal||Geen|
|cachemisses6|Cache missers (Shard 6)|Aantal|Totaal||Geen|
|getcommands6|Hiermee wordt opgehaald (Shard 6)|Aantal|Totaal||Geen|
|setcommands6|Sets (Shard 6)|Aantal|Totaal||Geen|
|operationsPerSecond6|Bewerkingen per seconde (Shard 6)|Aantal|Maximum||Geen|
|evictedkeys6|Verwijderde sleutels (Shard 6)|Aantal|Totaal||Geen|
|totalkeys6|Totaal aantal sleutels (Shard 6)|Aantal|Maximum||Geen|
|expiredkeys6|Verlopen sleutels (Shard 6)|Aantal|Totaal||Geen|
|usedmemory6|Gebruikt geheugen (Shard 6)|Bytes|Maximum||Geen|
|usedmemoryRss6|Gebruikte geheugen RSS (Shard 6)|Bytes|Maximum||Geen|
|serverLoad6|Server belasting (Shard 6)|Percentage|Maximum||Geen|
|cacheWrite6|Cache schrijven (Shard 6)|BytesPerSecond|Maximum||Geen|
|cacheRead6|Lees bewerking in cache (Shard 6)|BytesPerSecond|Maximum||Geen|
|percentProcessorTime6|CPU (Shard 6)|Percentage|Maximum||Geen|
|connectedclients7|Verbonden clients (Shard 7)|Aantal|Maximum||Geen|
|totalcommandsprocessed7|Totaal aantal bewerkingen (Shard 7)|Aantal|Totaal||Geen|
|cachehits7|Cache treffers (Shard 7)|Aantal|Totaal||Geen|
|cachemisses7|Cache missers (Shard 7)|Aantal|Totaal||Geen|
|getcommands7|Hiermee wordt opgehaald (Shard 7)|Aantal|Totaal||Geen|
|setcommands7|Sets (Shard 7)|Aantal|Totaal||Geen|
|operationsPerSecond7|Bewerkingen per seconde (Shard 7)|Aantal|Maximum||Geen|
|evictedkeys7|Verwijderde sleutels (Shard 7)|Aantal|Totaal||Geen|
|totalkeys7|Totaal aantal sleutels (Shard 7)|Aantal|Maximum||Geen|
|expiredkeys7|Verlopen sleutels (Shard 7)|Aantal|Totaal||Geen|
|usedmemory7|Gebruikt geheugen (Shard 7)|Bytes|Maximum||Geen|
|usedmemoryRss7|Gebruikte geheugen RSS (Shard 7)|Bytes|Maximum||Geen|
|serverLoad7|Server belasting (Shard 7)|Percentage|Maximum||Geen|
|cacheWrite7|Cache schrijven (Shard 7)|BytesPerSecond|Maximum||Geen|
|cacheRead7|Lees bewerking in cache (Shard 7)|BytesPerSecond|Maximum||Geen|
|percentProcessorTime7|CPU (Shard 7)|Percentage|Maximum||Geen|
|connectedclients8|Verbonden clients (Shard 8)|Aantal|Maximum||Geen|
|totalcommandsprocessed8|Totaal aantal bewerkingen (Shard 8)|Aantal|Totaal||Geen|
|cachehits8|Cache treffers (Shard 8)|Aantal|Totaal||Geen|
|cachemisses8|Cache missers (Shard 8)|Aantal|Totaal||Geen|
|getcommands8|Hiermee wordt opgehaald (Shard 8)|Aantal|Totaal||Geen|
|setcommands8|Sets (Shard 8)|Aantal|Totaal||Geen|
|operationsPerSecond8|Bewerkingen per seconde (Shard 8)|Aantal|Maximum||Geen|
|evictedkeys8|Verwijderde sleutels (Shard 8)|Aantal|Totaal||Geen|
|totalkeys8|Totaal aantal sleutels (Shard 8)|Aantal|Maximum||Geen|
|expiredkeys8|Verlopen sleutels (Shard 8)|Aantal|Totaal||Geen|
|usedmemory8|Gebruikt geheugen (Shard 8)|Bytes|Maximum||Geen|
|usedmemoryRss8|Gebruikt geheugen RSS (Shard 8)|Bytes|Maximum||Geen|
|serverLoad8|Server belasting (Shard 8)|Percentage|Maximum||Geen|
|cacheWrite8|Cache schrijven (Shard 8)|BytesPerSecond|Maximum||Geen|
|cacheRead8|Lees bewerking in cache (Shard 8)|BytesPerSecond|Maximum||Geen|
|percentProcessorTime8|CPU (Shard 8)|Percentage|Maximum||Geen|
|connectedclients9|Verbonden clients (Shard 9)|Aantal|Maximum||Geen|
|totalcommandsprocessed9|Totaal aantal bewerkingen (Shard 9)|Aantal|Totaal||Geen|
|cachehits9|Cache treffers (Shard 9)|Aantal|Totaal||Geen|
|cachemisses9|Cache missers (Shard 9)|Aantal|Totaal||Geen|
|getcommands9|Hiermee wordt opgehaald (Shard 9)|Aantal|Totaal||Geen|
|setcommands9|Sets (Shard 9)|Aantal|Totaal||Geen|
|operationsPerSecond9|Bewerkingen per seconde (Shard 9)|Aantal|Maximum||Geen|
|evictedkeys9|Verwijderde sleutels (Shard 9)|Aantal|Totaal||Geen|
|totalkeys9|Totaal aantal sleutels (Shard 9)|Aantal|Maximum||Geen|
|expiredkeys9|Verlopen sleutels (Shard 9)|Aantal|Totaal||Geen|
|usedmemory9|Gebruikt geheugen (Shard 9)|Bytes|Maximum||Geen|
|usedmemoryRss9|Gebruikte geheugen RSS (Shard 9)|Bytes|Maximum||Geen|
|serverLoad9|Server belasting (Shard 9)|Percentage|Maximum||Geen|
|cacheWrite9|Cache schrijven (Shard 9)|BytesPerSecond|Maximum||Geen|
|cacheRead9|Lees bewerking in cache (Shard 9)|BytesPerSecond|Maximum||Geen|
|percentProcessorTime9|CPU (Shard 9)|Percentage|Maximum||Geen|




## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Micro soft. CDN/cdnwebapplicationfirewallpolicies

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Aantal aanvragen voor Web Application firewall|Aantal|Totaal|Het aantal client aanvragen dat is verwerkt door de Web Application firewall|Beleidsnaam, regelnaam, actie|


## <a name="microsoftclassiccomputevirtualmachines"></a>Micro soft. ClassicCompute/informatie

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Percentage|Average|Het percentage toegewezen reken eenheden dat momenteel wordt gebruikt door de virtuele machine (s).|Geen|
|Netwerk in|Netwerk in|Bytes|Totaal|Het aantal bytes dat is ontvangen op alle netwerk interfaces door de virtuele machine (s) (binnenkomend verkeer).|Geen|
|Netwerk uit|Netwerk uit|Bytes|Totaal|Het aantal uitgaande bytes op alle netwerk interfaces door de virtuele machine (s) (uitgaand verkeer).|Geen|
|Gelezen bytes per seconde|Schijf lezen|BytesPerSecond|Average|Gemiddeld aantal gelezen bytes van de schijf tijdens de controle periode.|Geen|
|Geschreven bytes per seconde|Schijf schrijven|BytesPerSecond|Average|Gemiddeld aantal bytes dat tijdens de controle periode naar de schijf wordt geschreven.|Geen|
|Lees bewerkingen op de schijf per seconde|Lees bewerkingen op de schijf per seconde|CountPerSecond|Average|Read-IOPS van schijf.|Geen|
|Schrijf bewerkingen op de schijf per seconde|Schrijf bewerkingen op de schijf per seconde|CountPerSecond|Average|Schijf schrijf-IOPS.|Geen|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Micro soft. ClassicCompute/domein naam/sleuven/rollen

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Percentage|Average|Het percentage toegewezen reken eenheden dat momenteel wordt gebruikt door de virtuele machine (s).|RoleInstanceId|
|Netwerk in|Netwerk in|Bytes|Totaal|Het aantal bytes dat is ontvangen op alle netwerk interfaces door de virtuele machine (s) (binnenkomend verkeer).|RoleInstanceId|
|Netwerk uit|Netwerk uit|Bytes|Totaal|Het aantal uitgaande bytes op alle netwerk interfaces door de virtuele machine (s) (uitgaand verkeer).|RoleInstanceId|
|Gelezen bytes per seconde|Schijf lezen|BytesPerSecond|Average|Gemiddeld aantal gelezen bytes van de schijf tijdens de controle periode.|RoleInstanceId|
|Geschreven bytes per seconde|Schijf schrijven|BytesPerSecond|Average|Gemiddeld aantal bytes dat tijdens de controle periode naar de schijf wordt geschreven.|RoleInstanceId|
|Lees bewerkingen op de schijf per seconde|Lees bewerkingen op de schijf per seconde|CountPerSecond|Average|Read-IOPS van schijf.|RoleInstanceId|
|Schrijf bewerkingen op de schijf per seconde|Schrijf bewerkingen op de schijf per seconde|CountPerSecond|Average|Schijf schrijf-IOPS.|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Micro soft. ClassicStorage/Storage accounts

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|UsedCapacity|Gebruikte capaciteit|Bytes|Average|Gebruikte capaciteit van account|Geen|
|Transacties|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de dimensie ResponseType voor het aantal verschillende typen reacties.|ResponseType, geotype, ApiName, authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid ingangs gegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgangs gegevens, in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie|
|SuccessServerLatency|Geslaagde serverlatentie|Milliseconden|Average|De latentie die door Azure Storage wordt gebruikt voor het verwerken van een geslaagde aanvraag, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency.|Geotype, ApiName, authenticatie|
|SuccessE2ELatency|Success E2E Latency|Milliseconden|Average|De end-to-end latentie van geslaagde aanvragen voor een opslag service of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie|
|Beschikbaarheid|Beschikbaarheid|Percentage|Average|Het percentage Beschik baarheid voor de opslag service of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Micro soft. ClassicStorage/Storage accounts/blobServices

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BlobCapacity|BLOB-capaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de Blob service van het opslag account in bytes.|BlobType, Tier|
|BlobCount|Aantal blobs|Aantal|Average|Het aantal blobs in de Blob service van het opslag account.|BlobType, Tier|
|ContainerCount|Aantal BLOB-containers|Aantal|Average|Het aantal containers in het Blob service van het opslag account.|Geen|
|IndexCapacity|Index capaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de index van de ADLS Gen2 (hiërarchisch) in bytes.|Geen|
|Transacties|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de dimensie ResponseType voor het aantal verschillende typen reacties.|ResponseType, geotype, ApiName, authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid ingangs gegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgangs gegevens, in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie|
|SuccessServerLatency|Geslaagde serverlatentie|Milliseconden|Average|De latentie die door Azure Storage wordt gebruikt voor het verwerken van een geslaagde aanvraag, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency.|Geotype, ApiName, authenticatie|
|SuccessE2ELatency|Success E2E Latency|Milliseconden|Average|De end-to-end latentie van geslaagde aanvragen voor een opslag service of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie|
|Beschikbaarheid|Beschikbaarheid|Percentage|Average|Het percentage Beschik baarheid voor de opslag service of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Micro soft. ClassicStorage/Storage accounts/tableServices

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TableCapacity|Tabel capaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de Table service van het opslag account in bytes.|Geen|
|TableCount|Aantal tabellen|Aantal|Average|Het aantal tabellen in de Table service van het opslag account.|Geen|
|TableEntityCount|Aantal tabel entiteiten|Aantal|Average|Het aantal tabel entiteiten in de Table service van het opslag account.|Geen|
|Transacties|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de dimensie ResponseType voor het aantal verschillende typen reacties.|ResponseType, geotype, ApiName, authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid ingangs gegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgangs gegevens, in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie|
|SuccessServerLatency|Geslaagde serverlatentie|Milliseconden|Average|De latentie die door Azure Storage wordt gebruikt voor het verwerken van een geslaagde aanvraag, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency.|Geotype, ApiName, authenticatie|
|SuccessE2ELatency|Success E2E Latency|Milliseconden|Average|De end-to-end latentie van geslaagde aanvragen voor een opslag service of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie|
|Beschikbaarheid|Beschikbaarheid|Percentage|Average|Het percentage Beschik baarheid voor de opslag service of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Micro soft. ClassicStorage/Storage accounts/fileServices

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|FileCapacity|Bestands capaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de bestands service van het opslag account in bytes.|Bestands share|
|FileCount|Aantal bestanden|Aantal|Average|Het aantal bestanden in de file-service van het opslag account.|Bestands share|
|FileShareCount|Aantal bestands shares|Aantal|Average|Het aantal bestands shares in de file-service van het opslag account.|Geen|
|FileShareSnapshotCount|Aantal moment opnamen van bestands shares|Aantal|Average|Het aantal moment opnamen dat aanwezig is op de share in de bestanden service van het opslag account.|Bestands share|
|FileShareSnapshotSize|Grootte van moment opname van bestands share|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de moment opnamen in de bestands service van het opslag account in bytes.|Bestands share|
|FileShareQuota|Quota grootte van bestands share|Bytes|Average|De bovengrens voor de hoeveelheid opslag die kan worden gebruikt door Azure Files service in bytes.|Bestands share|
|Transacties|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de dimensie ResponseType voor het aantal verschillende typen reacties.|ResponseType, geotype, ApiName, Authentication, file share|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid ingangs gegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie, bestands share|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgangs gegevens, in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie, bestands share|
|SuccessServerLatency|Geslaagde serverlatentie|Milliseconden|Average|De latentie die door Azure Storage wordt gebruikt voor het verwerken van een geslaagde aanvraag, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency.|Geotype, ApiName, authenticatie, bestands share|
|SuccessE2ELatency|Success E2E Latency|Milliseconden|Average|De end-to-end latentie van geslaagde aanvragen voor een opslag service of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie, bestands share|
|Beschikbaarheid|Beschikbaarheid|Percentage|Average|Het percentage Beschik baarheid voor de opslag service of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie, bestands share|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Micro soft. ClassicStorage/Storage accounts/queueServices

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|QueueCapacity|Wachtrij capaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de Queue-service van het opslag account in bytes.|Geen|
|QueueCount|Aantal wachtrijen|Aantal|Average|Het aantal wacht rijen in de Queue-service van het opslag account.|Geen|
|QueueMessageCount|Aantal wachtrij berichten|Aantal|Average|Het geschatte aantal wachtrij berichten in de Queue-service van het opslag account.|Geen|
|Transacties|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de dimensie ResponseType voor het aantal verschillende typen reacties.|ResponseType, geotype, ApiName, authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid ingangs gegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgangs gegevens, in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie|
|SuccessServerLatency|Geslaagde serverlatentie|Milliseconden|Average|De latentie die door Azure Storage wordt gebruikt voor het verwerken van een geslaagde aanvraag, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency.|Geotype, ApiName, authenticatie|
|SuccessE2ELatency|Success E2E Latency|Milliseconden|Average|De end-to-end latentie van geslaagde aanvragen voor een opslag service of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie|
|Beschikbaarheid|Beschikbaarheid|Percentage|Average|Het percentage Beschik baarheid voor de opslag service of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie|


## <a name="microsoftcognitiveservicesaccounts"></a>Micro soft. CognitiveServices/accounts

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
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
|TotalTokenCalls|Totaal aantal token aanroepen|Aantal|Totaal|Totaal aantal token aanroepen.|ApiName, Operationname, regio|
|CharactersTranslated|Geconverteerde tekens|Aantal|Totaal|Totaal aantal tekens in binnenkomende-tekst aanvraag.|ApiName, Operationname, regio|
|CharactersTrained|Getrainde tekens|Aantal|Totaal|Totaal aantal getrainde tekens.|ApiName, Operationname, regio|
|SpeechSessionDuration|Spraak sessie duur|Seconden|Totaal|Totale duur van de spraak sessie in seconden.|ApiName, Operationname, regio|
|TotalTransactions|Totaal aantal trans acties|Aantal|Totaal|Totaal aantal trans acties.|Geen|
|ProcessedImages|Verwerkte installatie kopieën|Aantal|Totaal| Aantal trans acties voor de verwerking van afbeeldingen.|ApiName, functie-, kanaal-, regio|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Percentage|Average|Het percentage toegewezen reken eenheden dat momenteel wordt gebruikt door de virtuele machine (s)|Geen|
|Netwerk in|Netwerk in Factureerbaar (afgeschaft)|Bytes|Totaal|Het aantal factureer bare bytes dat is ontvangen op alle netwerk interfaces door de virtuele machine (s) (binnenkomend verkeer) (afgeschaft)|Geen|
|Netwerk uit|Gefactureerd netwerk (afgeschaft)|Bytes|Totaal|Het aantal factureer bare bytes op alle netwerk interfaces door de virtuele machine (s) (uitgaand verkeer) (afgeschaft)|Geen|
|Gelezen bytes op de schijf|Gelezen bytes op de schijf|Bytes|Totaal|Gelezen bytes van de schijf tijdens de controle periode|Geen|
|Geschreven bytes op de schijf|Geschreven bytes op de schijf|Bytes|Totaal|Naar schijf geschreven bytes tijdens de controle periode|Geen|
|Lees bewerkingen op de schijf per seconde|Lees bewerkingen op de schijf per seconde|CountPerSecond|Average|Lees-IOPS schijf|Geen|
|Schrijf bewerkingen op de schijf per seconde|Schrijf bewerkingen op de schijf per seconde|CountPerSecond|Average|Schijf schrijf-IOPS|Geen|
|Resterende CPU-tegoeden|Resterende CPU-tegoeden|Aantal|Average|Totaal aantal beschik bare tegoeden voor burst|Geen|
|Verbruikte CPU-tegoeden|Verbruikte CPU-tegoeden|Aantal|Average|Totaal aantal tegoeden dat door de virtuele machine wordt verbruikt|Geen|
|Per schijf gelezen bytes/sec.|Gegevens schijf gelezen bytes per seconde [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode|SlotId|
|Schrijf bewerkingen per schijf/sec.|Gegevens schijf schrijf bewerkingen in bytes per seconde [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSecond|Average|Tijdens de controle periode naar één schijf geschreven bytes per seconde|SlotId|
|Lees bewerkingen per schijf/sec.|Lees bewerkingen op de gegevens schijf per seconde [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode|SlotId|
|Schrijf bewerkingen per schijf/SEC|Schrijf bewerkingen op de gegevens schijf per seconde [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSecond|Average|IOPS tijdens de controle periode van één schijf schrijven|SlotId|
|WACHTRIJ diepte per schijf|WACHTRIJ diepte van de gegevens schijf [(afgeschaft)](portal-disk-metrics-deprecation.md)] (Portal-Disk-Metrics-Deprecation.MD)|Aantal|Average|Wachtrij diepte van gegevens schijf (of wachtrij lengte)|SlotId|
|BESTURINGSSYSTEEM per schijf gelezen bytes per seconde|BESTURINGSSYSTEEM schijf gelezen bytes per seconde [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode voor de besturingssysteem schijf|Geen|
|Per schijf geschreven bytes/sec.|BESTURINGSSYSTEEM schijf schrijf bewerkingen in bytes per seconde [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSecond|Average|Geschreven bytes per seconde tijdens de controle periode voor de besturingssysteem schijf|Geen|
|Lees bewerkingen per schijf voor het besturings systeem/sec.|Lees bewerkingen op de besturingssysteem schijf per seconde [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode voor de besturingssysteem schijf|Geen|
|Schrijf bewerkingen per schijf van het besturings systeem/SEC|Schrijf bewerkingen op de besturingssysteem schijf per seconde [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSecond|Average|IOPS tijdens de controle periode voor de besturingssysteem schijf schrijven vanaf één schijf|Geen|
|WACHTRIJ diepte voor het besturings systeem per schijf|WACHTRIJ diepte van de besturingssysteem schijf [(afgeschaft)](portal-disk-metrics-deprecation.md)|Aantal|Average|Wachtrij diepte van het besturings systeem (of wachtrij lengte)|Geen|
|Gegevens schijf gelezen bytes per seconde|Gegevens schijf gelezen bytes per seconde (preview)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode|LUN|
|Geschreven bytes per seconde gegevens schijf|Geschreven bytes per seconde (preview) gegevens schijf|CountPerSecond|Average|Tijdens de controle periode naar één schijf geschreven bytes per seconde|LUN|
|Lees bewerkingen op de gegevens schijf per seconde|Lees bewerkingen op de gegevens schijf per seconde (preview)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode|LUN|
|Schrijf bewerkingen op de gegevens schijf per seconde|Schrijf bewerkingen op de gegevens schijf per seconde (preview)|CountPerSecond|Average|IOPS tijdens de controle periode van één schijf schrijven|LUN|
|Wachtrijlengte van gegevensschijf|Wachtrij diepte van gegevens schijf (preview-versie)|Aantal|Average|Wachtrij diepte van gegevens schijf (of wachtrij lengte)|LUN|
|BESTURINGSSYSTEEM schijf gelezen bytes per seconde|BESTURINGSSYSTEEM schijf gelezen bytes per seconde (preview)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode voor de besturingssysteem schijf|Geen|
|Schrijf bewerkingen op de besturingssysteem schijf per seconde|Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)|CountPerSecond|Average|Geschreven bytes per seconde tijdens de controle periode voor de besturingssysteem schijf|Geen|
|Lees bewerkingen van de besturingssysteem schijf per seconde|Lees bewerkingen op de besturingssysteem schijf per seconde (preview)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode voor de besturingssysteem schijf|Geen|
|Schrijf bewerkingen op de besturingssysteem schijf per seconde|Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)|CountPerSecond|Average|IOPS tijdens de controle periode voor de besturingssysteem schijf schrijven vanaf één schijf|Geen|
|Wachtrijlengte van besturingssysteemschijf|Wachtrij diepte van de besturingssysteem schijf (preview-versie)|Aantal|Average|Wachtrij diepte van het besturings systeem (of wachtrij lengte)|Geen|
|Binnenkomende stromen|Binnenkomende stromen|Aantal|Average|Inkomende stromen zijn het aantal huidige stromen in de binnenkomende richting (verkeer dat wordt verzonden naar de virtuele machine)|Geen|
|Uitgaande stromen|Uitgaande stromen|Aantal|Average|Uitgaande stromen zijn het aantal huidige stromen in de uitgaande richting (verkeer dat uit de virtuele machine wordt verzonden)|Geen|
|Maximum aanmaak frequentie inkomende stromen|Maximum aanmaak frequentie inkomende stromen|CountPerSecond|Average|Het maximale aantal inkomende stromen (verkeer dat wordt verzonden naar de virtuele machine)|Geen|
|Maximum aanmaak frequentie van uitgaande stromen|Maximum aanmaak frequentie van uitgaande stromen|CountPerSecond|Average|De maximale aanmaak frequentie van uitgaande stromen (verkeer dat uit de virtuele machine wordt verzonden)|Geen|
|Treffer voor Premium data-schijf cache lezen|Cache geheugen voor lezen van Premium-gegevens schijf (preview-versie)|Percentage|Average|Treffer voor Premium data-schijf cache lezen|LUN|
|Lees missers cache Premium-gegevens schijf|Cache voor lezen van Premium-gegevens schijf (preview)|Percentage|Average|Lees missers cache Premium-gegevens schijf|LUN|
|Treffer voor Premium-besturingssysteem schijf cache lezen|Treffer voor het lezen van een Premium-besturingssysteem schijf cache (preview-versie)|Percentage|Average|Treffer voor Premium-besturingssysteem schijf cache lezen|Geen|
|Leesmij voor Premium-besturingssysteem schijf cache lezen|Schijf cache voor Premium-Lees-missers (preview-versie)|Percentage|Average|Leesmij voor Premium-besturingssysteem schijf cache lezen|Geen|
|Totaal netwerk|Totaal netwerk|Bytes|Totaal|Het aantal ontvangen bytes op alle netwerk interfaces door de virtuele machine (s) (binnenkomend verkeer)|Geen|
|Totaal aantal netwerk|Totaal aantal netwerk|Bytes|Totaal|Het aantal uitgaande bytes op alle netwerk interfaces door de virtuele machine (s) (uitgaand verkeer)|Geen|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Percentage|Average|Het percentage toegewezen reken eenheden dat momenteel wordt gebruikt door de virtuele machine (s)|VMName|
|Netwerk in|Netwerk in Factureerbaar (afgeschaft)|Bytes|Totaal|Het aantal factureer bare bytes dat is ontvangen op alle netwerk interfaces door de virtuele machine (s) (binnenkomend verkeer) (afgeschaft)|VMName|
|Netwerk uit|Gefactureerd netwerk (afgeschaft)|Bytes|Totaal|Het aantal factureer bare bytes op alle netwerk interfaces door de virtuele machine (s) (uitgaand verkeer) (afgeschaft)|VMName|
|Gelezen bytes op de schijf|Gelezen bytes op de schijf|Bytes|Totaal|Gelezen bytes van de schijf tijdens de controle periode|VMName|
|Geschreven bytes op de schijf|Geschreven bytes op de schijf|Bytes|Totaal|Naar schijf geschreven bytes tijdens de controle periode|VMName|
|Lees bewerkingen op de schijf per seconde|Lees bewerkingen op de schijf per seconde|CountPerSecond|Average|Lees-IOPS schijf|VMName|
|Schrijf bewerkingen op de schijf per seconde|Schrijf bewerkingen op de schijf per seconde|CountPerSecond|Average|Schijf schrijf-IOPS|VMName|
|Resterende CPU-tegoeden|Resterende CPU-tegoeden|Aantal|Average|Totaal aantal beschik bare tegoeden voor burst|Geen|
|Verbruikte CPU-tegoeden|Verbruikte CPU-tegoeden|Aantal|Average|Totaal aantal tegoeden dat door de virtuele machine wordt verbruikt|Geen|
|Per schijf gelezen bytes/sec.|Gegevens schijf gelezen bytes per seconde [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode|SlotId|
|Schrijf bewerkingen per schijf/sec.|Gegevens schijf schrijf bewerkingen in bytes per seconde [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSecond|Average|Tijdens de controle periode naar één schijf geschreven bytes per seconde|SlotId|
|Lees bewerkingen per schijf/sec.|Lees bewerkingen op de gegevens schijf per seconde [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode|SlotId|
|Schrijf bewerkingen per schijf/SEC|Schrijf bewerkingen op de gegevens schijf per seconde [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSecond|Average|IOPS tijdens de controle periode van één schijf schrijven|SlotId|
|WACHTRIJ diepte per schijf|WACHTRIJ diepte van de gegevens schijf [(afgeschaft)](portal-disk-metrics-deprecation.md)|Aantal|Average|Wachtrij diepte van gegevens schijf (of wachtrij lengte)|SlotId|
|BESTURINGSSYSTEEM per schijf gelezen bytes per seconde|BESTURINGSSYSTEEM schijf gelezen bytes per seconde [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode voor de besturingssysteem schijf|Geen|
|Per schijf geschreven bytes/sec.|BESTURINGSSYSTEEM schijf schrijf bewerkingen in bytes per seconde [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSecond|Average|Geschreven bytes per seconde tijdens de controle periode voor de besturingssysteem schijf|Geen|
|Lees bewerkingen per schijf voor het besturings systeem/sec.|Lees bewerkingen op de besturingssysteem schijf per seconde [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode voor de besturingssysteem schijf|Geen|
|Schrijf bewerkingen per schijf van het besturings systeem/SEC|Schrijf bewerkingen op de besturingssysteem schijf per seconde [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSecond|Average|IOPS tijdens de controle periode voor de besturingssysteem schijf schrijven vanaf één schijf|Geen|
|WACHTRIJ diepte voor het besturings systeem per schijf|WACHTRIJ diepte van de besturingssysteem schijf [(afgeschaft)](portal-disk-metrics-deprecation.md)|Aantal|Average|Wachtrij diepte van het besturings systeem (of wachtrij lengte)|Geen|
|Gegevens schijf gelezen bytes per seconde|Gegevens schijf gelezen bytes per seconde (preview)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode|LUN, VMName|
|Geschreven bytes per seconde gegevens schijf|Geschreven bytes per seconde (preview) gegevens schijf|CountPerSecond|Average|Tijdens de controle periode naar één schijf geschreven bytes per seconde|LUN, VMName|
|Lees bewerkingen op de gegevens schijf per seconde|Lees bewerkingen op de gegevens schijf per seconde (preview)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode|LUN, VMName|
|Schrijf bewerkingen op de gegevens schijf per seconde|Schrijf bewerkingen op de gegevens schijf per seconde (preview)|CountPerSecond|Average|IOPS tijdens de controle periode van één schijf schrijven|LUN, VMName|
|Wachtrijlengte van gegevensschijf|Wachtrij diepte van gegevens schijf (preview-versie)|Aantal|Average|Wachtrij diepte van gegevens schijf (of wachtrij lengte)|LUN, VMName|
|BESTURINGSSYSTEEM schijf gelezen bytes per seconde|BESTURINGSSYSTEEM schijf gelezen bytes per seconde (preview)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode voor de besturingssysteem schijf|VMName|
|Schrijf bewerkingen op de besturingssysteem schijf per seconde|Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)|CountPerSecond|Average|Geschreven bytes per seconde tijdens de controle periode voor de besturingssysteem schijf|VMName|
|Lees bewerkingen van de besturingssysteem schijf per seconde|Lees bewerkingen op de besturingssysteem schijf per seconde (preview)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode voor de besturingssysteem schijf|VMName|
|Schrijf bewerkingen op de besturingssysteem schijf per seconde|Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)|CountPerSecond|Average|IOPS tijdens de controle periode voor de besturingssysteem schijf schrijven vanaf één schijf|VMName|
|Wachtrijlengte van besturingssysteemschijf|Wachtrij diepte van de besturingssysteem schijf (preview-versie)|Aantal|Average|Wachtrij diepte van het besturings systeem (of wachtrij lengte)|VMName|
|Binnenkomende stromen|Binnenkomende stromen|Aantal|Average|Inkomende stromen zijn het aantal huidige stromen in de binnenkomende richting (verkeer dat wordt verzonden naar de virtuele machine)|VMName|
|Uitgaande stromen|Uitgaande stromen|Aantal|Average|Uitgaande stromen zijn het aantal huidige stromen in de uitgaande richting (verkeer dat uit de virtuele machine wordt verzonden)|VMName|
|Maximum aanmaak frequentie inkomende stromen|Maximum aanmaak frequentie inkomende stromen|CountPerSecond|Average|Het maximale aantal inkomende stromen (verkeer dat wordt verzonden naar de virtuele machine)|VMName|
|Maximum aanmaak frequentie van uitgaande stromen|Maximum aanmaak frequentie van uitgaande stromen|CountPerSecond|Average|De maximale aanmaak frequentie van uitgaande stromen (verkeer dat uit de virtuele machine wordt verzonden)|VMName|
|Treffer voor Premium data-schijf cache lezen|Cache geheugen voor lezen van Premium-gegevens schijf (preview-versie)|Percentage|Average|Treffer voor Premium data-schijf cache lezen|LUN, VMName|
|Lees missers cache Premium-gegevens schijf|Cache voor lezen van Premium-gegevens schijf (preview)|Percentage|Average|Lees missers cache Premium-gegevens schijf|LUN, VMName|
|Treffer voor Premium-besturingssysteem schijf cache lezen|Treffer voor het lezen van een Premium-besturingssysteem schijf cache (preview-versie)|Percentage|Average|Treffer voor Premium-besturingssysteem schijf cache lezen|VMName|
|Leesmij voor Premium-besturingssysteem schijf cache lezen|Schijf cache voor Premium-Lees-missers (preview-versie)|Percentage|Average|Leesmij voor Premium-besturingssysteem schijf cache lezen|VMName|
|Totaal netwerk|Totaal netwerk|Bytes|Totaal|Het aantal ontvangen bytes op alle netwerk interfaces door de virtuele machine (s) (binnenkomend verkeer)|VMName|
|Totaal aantal netwerk|Totaal aantal netwerk|Bytes|Totaal|Het aantal uitgaande bytes op alle netwerk interfaces door de virtuele machine (s) (uitgaand verkeer)|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Micro soft. Compute/virtualMachineScaleSets/informatie

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Percentage|Average|Het percentage toegewezen reken eenheden dat momenteel wordt gebruikt door de virtuele machine (s)|Geen|
|Netwerk in|Netwerk in Factureerbaar (afgeschaft)|Bytes|Totaal|Het aantal factureer bare bytes dat is ontvangen op alle netwerk interfaces door de virtuele machine (s) (binnenkomend verkeer) (afgeschaft)|Geen|
|Netwerk uit|Gefactureerd netwerk (afgeschaft)|Bytes|Totaal|Het aantal factureer bare bytes op alle netwerk interfaces door de virtuele machine (s) (uitgaand verkeer) (afgeschaft)|Geen|
|Gelezen bytes op de schijf|Gelezen bytes op de schijf|Bytes|Totaal|Gelezen bytes van de schijf tijdens de controle periode|Geen|
|Geschreven bytes op de schijf|Geschreven bytes op de schijf|Bytes|Totaal|Naar schijf geschreven bytes tijdens de controle periode|Geen|
|Lees bewerkingen op de schijf per seconde|Lees bewerkingen op de schijf per seconde|CountPerSecond|Average|Lees-IOPS schijf|Geen|
|Schrijf bewerkingen op de schijf per seconde|Schrijf bewerkingen op de schijf per seconde|CountPerSecond|Average|Schijf schrijf-IOPS|Geen|
|Resterende CPU-tegoeden|Resterende CPU-tegoeden|Aantal|Average|Totaal aantal beschik bare tegoeden voor burst|Geen|
|Verbruikte CPU-tegoeden|Verbruikte CPU-tegoeden|Aantal|Average|Totaal aantal tegoeden dat door de virtuele machine wordt verbruikt|Geen|
|Per schijf gelezen bytes/sec.|Gegevens schijf gelezen bytes per seconde [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode|SlotId|
|Schrijf bewerkingen per schijf/sec.|Gegevens schijf schrijf bewerkingen in bytes per seconde [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSecond|Average|Tijdens de controle periode naar één schijf geschreven bytes per seconde|SlotId|
|Lees bewerkingen per schijf/sec.|Lees bewerkingen op de gegevens schijf per seconde [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode|SlotId|
|Schrijf bewerkingen per schijf/SEC|Schrijf bewerkingen op de gegevens schijf per seconde [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSecond|Average|IOPS tijdens de controle periode van één schijf schrijven|SlotId|
|WACHTRIJ diepte per schijf|WACHTRIJ diepte van de gegevens schijf [(afgeschaft)](portal-disk-metrics-deprecation.md)|Aantal|Average|Wachtrij diepte van gegevens schijf (of wachtrij lengte)|SlotId|
|BESTURINGSSYSTEEM per schijf gelezen bytes per seconde|BESTURINGSSYSTEEM schijf gelezen bytes per seconde [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode voor de besturingssysteem schijf|Geen|
|Per schijf geschreven bytes/sec.|BESTURINGSSYSTEEM schijf schrijf bewerkingen in bytes per seconde [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSecond|Average|Geschreven bytes per seconde tijdens de controle periode voor de besturingssysteem schijf|Geen|
|Lees bewerkingen per schijf voor het besturings systeem/sec.|Lees bewerkingen op de besturingssysteem schijf per seconde [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode voor de besturingssysteem schijf|Geen|
|Schrijf bewerkingen per schijf van het besturings systeem/SEC|Schrijf bewerkingen op de besturingssysteem schijf per seconde [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSecond|Average|IOPS tijdens de controle periode voor de besturingssysteem schijf schrijven vanaf één schijf|Geen|
|WACHTRIJ diepte voor het besturings systeem per schijf|WACHTRIJ diepte van de besturingssysteem schijf [(afgeschaft)](portal-disk-metrics-deprecation.md)|Aantal|Average|Wachtrij diepte van het besturings systeem (of wachtrij lengte)|Geen|
|Gegevens schijf gelezen bytes per seconde|Gegevens schijf gelezen bytes per seconde (preview)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode|LUN|
|Geschreven bytes per seconde gegevens schijf|Geschreven bytes per seconde (preview) gegevens schijf|CountPerSecond|Average|Tijdens de controle periode naar één schijf geschreven bytes per seconde|LUN|
|Lees bewerkingen op de gegevens schijf per seconde|Lees bewerkingen op de gegevens schijf per seconde (preview)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode|LUN|
|Schrijf bewerkingen op de gegevens schijf per seconde|Schrijf bewerkingen op de gegevens schijf per seconde (preview)|CountPerSecond|Average|IOPS tijdens de controle periode van één schijf schrijven|LUN|
|Wachtrijlengte van gegevensschijf|Wachtrij diepte van gegevens schijf (preview-versie)|Aantal|Average|Wachtrij diepte van gegevens schijf (of wachtrij lengte)|LUN|
|BESTURINGSSYSTEEM schijf gelezen bytes per seconde|BESTURINGSSYSTEEM schijf gelezen bytes per seconde (preview)|CountPerSecond|Average|Gelezen bytes per seconde van één schijf tijdens de controle periode voor de besturingssysteem schijf|Geen|
|Schrijf bewerkingen op de besturingssysteem schijf per seconde|Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)|CountPerSecond|Average|Geschreven bytes per seconde tijdens de controle periode voor de besturingssysteem schijf|Geen|
|Lees bewerkingen van de besturingssysteem schijf per seconde|Lees bewerkingen op de besturingssysteem schijf per seconde (preview)|CountPerSecond|Average|IOPS lezen vanaf één schijf tijdens de controle periode voor de besturingssysteem schijf|Geen|
|Schrijf bewerkingen op de besturingssysteem schijf per seconde|Schrijf bewerkingen op de besturingssysteem schijf per seconde (preview)|CountPerSecond|Average|IOPS tijdens de controle periode voor de besturingssysteem schijf schrijven vanaf één schijf|Geen|
|Wachtrijlengte van besturingssysteemschijf|Wachtrij diepte van de besturingssysteem schijf (preview-versie)|Aantal|Average|Wachtrij diepte van het besturings systeem (of wachtrij lengte)|Geen|
|Binnenkomende stromen|Binnenkomende stromen|Aantal|Average|Inkomende stromen zijn het aantal huidige stromen in de binnenkomende richting (verkeer dat wordt verzonden naar de virtuele machine)|Geen|
|Uitgaande stromen|Uitgaande stromen|Aantal|Average|Uitgaande stromen zijn het aantal huidige stromen in de uitgaande richting (verkeer dat uit de virtuele machine wordt verzonden)|Geen|
|Maximum aanmaak frequentie inkomende stromen|Maximum aanmaak frequentie inkomende stromen|CountPerSecond|Average|Het maximale aantal inkomende stromen (verkeer dat wordt verzonden naar de virtuele machine)|Geen|
|Maximum aanmaak frequentie van uitgaande stromen|Maximum aanmaak frequentie van uitgaande stromen|CountPerSecond|Average|De maximale aanmaak frequentie van uitgaande stromen (verkeer dat uit de virtuele machine wordt verzonden)|Geen|
|Treffer voor Premium data-schijf cache lezen|Cache geheugen voor lezen van Premium-gegevens schijf (preview-versie)|Percentage|Average|Treffer voor Premium data-schijf cache lezen|LUN|
|Lees missers cache Premium-gegevens schijf|Cache voor lezen van Premium-gegevens schijf (preview)|Percentage|Average|Lees missers cache Premium-gegevens schijf|LUN|
|Treffer voor Premium-besturingssysteem schijf cache lezen|Treffer voor het lezen van een Premium-besturingssysteem schijf cache (preview-versie)|Percentage|Average|Treffer voor Premium-besturingssysteem schijf cache lezen|Geen|
|Leesmij voor Premium-besturingssysteem schijf cache lezen|Schijf cache voor Premium-Lees-missers (preview-versie)|Percentage|Average|Leesmij voor Premium-besturingssysteem schijf cache lezen|Geen|
|Totaal netwerk|Totaal netwerk|Bytes|Totaal|Het aantal ontvangen bytes op alle netwerk interfaces door de virtuele machine (s) (binnenkomend verkeer)|Geen|
|Totaal aantal netwerk|Totaal aantal netwerk|Bytes|Totaal|Het aantal uitgaande bytes op alle netwerk interfaces door de virtuele machine (s) (uitgaand verkeer)|Geen|

## <a name="microsoftcontainerinstancecontainergroups"></a>Micro soft. ContainerInstance/containerGroups

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CpuUsage|CPU-gebruik|Aantal|Average|CPU-gebruik op alle kernen in millicores.|containerName|
|MemoryUsage|Geheugengebruik|Bytes|Average|Totaal geheugen gebruik in bytes.|containerName|
|NetworkBytesReceivedPerSecond|Ontvangen netwerk bytes per seconde|Bytes|Average|Het netwerk ontvangen bytes per seconde.|Geen|
|NetworkBytesTransmittedPerSecond|Verzonden netwerk bytes per seconde|Bytes|Average|Het netwerk aantal verzonden bytes per seconde.|Geen|

## <a name="microsoftcontainerregistryregistries"></a>Micro soft. ContainerRegistry/registers

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TotalPullCount|Totaal aantal pull-bewerkingen|Aantal|Average|Aantal opgehaalde afbeeldingen in totaal|Geen|
|SuccessfulPullCount|Aantal geslaagde pull-bewerkingen|Aantal|Average|Aantal geslaagde installatie kopieën|Geen|
|TotalPushCount|Totaal aantal push berichten|Aantal|Average|Aantal afbeeldings pushes in totaal|Geen|
|SuccessfulPushCount|Aantal geslaagde push berichten|Aantal|Average|Aantal geslaagde pushes voor installatie kopie|Geen|
|RunDuration|Uitvoerings duur|Milliseconden|Totaal|Uitvoerings duur in milliseconden|Geen|


## <a name="microsoftcontainerservicemanagedclusters"></a>Micro soft. container service/managedClusters

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Totaal aantal beschik bare CPU-kernen in een beheerd cluster|Aantal|Average|Totaal aantal beschik bare CPU-kernen in een beheerd cluster|Geen|
|kube_node_status_allocatable_memory_bytes|Totale hoeveelheid beschikbaar geheugen in een beheerd cluster|Bytes|Average|Totale hoeveelheid beschikbaar geheugen in een beheerd cluster|Geen|
|kube_pod_status_ready|Aantal in de status gereed|Aantal|Average|Aantal in de status gereed|naam ruimte, pod|
|kube_node_status_condition|Statussen voor de verschillende knooppunt voorwaarden|Aantal|Average|Statussen voor de verschillende knooppunt voorwaarden|voor waarde, status, status2, knoop punt|
|kube_pod_status_phase|Aantal per fase|Aantal|Average|Aantal per fase|fase, naam ruimte, pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Micro soft. CustomProviders/resourceproviders

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SuccessfullRequests|Geslaagde aanvragen|Aantal|Totaal|Geslaagde aanvragen van de aangepaste provider|HttpMethod, CallPath, status code|
|FailedRequests|Mislukte aanvragen|Aantal|Totaal|Hiermee worden de beschik bare logboeken voor aangepaste resource providers opgehaald|HttpMethod, CallPath, status code|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Micro soft. DataBoxEdge/dataBoxEdgeDevices

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|NICReadThroughput|Lees doorvoer (netwerk)|BytesPerSecond|Average|De Lees doorvoer van de netwerk interface op het apparaat in de rapportage periode voor alle volumes in de gateway.|InstanceName|
|NICWriteThroughput|Schrijf doorvoer (netwerk)|BytesPerSecond|Average|De schrijf doorvoer van de netwerk interface op het apparaat in de rapportage periode voor alle volumes in de gateway.|InstanceName|
|CloudReadThroughputPerShare|Door Voer van Cloud downloaden (delen)|BytesPerSecond|Average|De door Voer van downloaden naar Azure vanaf een share tijdens de rapportage periode.|Delen|
|CloudUploadThroughputPerShare|Upload doorvoer van Cloud (delen)|BytesPerSecond|Average|De upload doorvoer naar Azure vanaf een share tijdens de rapportage periode.|Delen|
|BytesUploadedToCloudPerShare|Geüploade Cloud bytes (delen)|Bytes|Average|Het totaal aantal bytes dat is geüpload naar Azure vanaf een share tijdens de rapportage periode.|Delen|
|TotalCapacity|Totale capaciteit|Bytes|Average|Totale capaciteit|Geen|
|Availablecapacity;)|Beschik bare capaciteit|Bytes|Average|De beschik bare capaciteit in bytes tijdens de rapportage periode.|Geen|
|CloudUploadThroughput|Upload doorvoer van Cloud|BytesPerSecond|Average|De upload doorvoer van de Cloud naar Azure tijdens de rapportage periode.|Geen|
|CloudReadThroughput|Door Voer van Cloud downloaden|BytesPerSecond|Average|De door Voer van de Cloud downloadt naar Azure tijdens de rapportage periode.|Geen|
|BytesUploadedToCloud|Geüploade Cloud bytes (apparaat)|Bytes|Average|Het totale aantal bytes dat tijdens de rapportage periode naar Azure wordt geüpload vanaf een apparaat.|Geen|
|HyperVVirtualProcessorUtilization|Edge Compute-percentage CPU|Percentage|Average|Percentage CPU-gebruik|InstanceName|
|HyperVMemoryUtilization|Edge Compute-geheugen gebruik|Percentage|Average|Hoeveelheid RAM-geheugen in gebruik|InstanceName|


## <a name="microsoftdatacatalogdatacatalogs"></a>Micro soft. DataCatalog/datacatalogs

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AssetDistributionByClassification|Activa distributie per classificatie|Aantal|Totaal|Geeft aan hoeveel activa met een bepaalde classificatie zijn toegewezen, dat wil zeggen dat ze zijn geclassificeerd met dat label.|Classificatie, bron|
|AssetDistributionByStorageType|Activa distributie per opslag type|Aantal|Totaal|Hiermee wordt het aantal assets van een bepaald opslag type aangegeven.|Para|
|NumberOfAssetsWithClassifications|Aantal activa met ten minste één classificatie|Aantal|Average|Hiermee wordt het aantal assets aangegeven met ten minste één label classificatie.|Geen|
|ScanCancelled|De scan is geannuleerd|Aantal|Totaal|Hiermee wordt het aantal geannuleerde scans aangegeven.|Geen|
|ScanCompleted|De scan is voltooid|Aantal|Totaal|Hiermee wordt het aantal scans aangegeven dat is voltooid.|Geen|
|ScanFailed|Kan niet scannen|Aantal|Totaal|Hiermee wordt aangegeven dat het aantal mislukte scans is mislukt.|Geen|
|ScanTimeTaken|Gebruikte scan tijd|Seconden|Totaal|Geeft de totale Scan tijd in seconden.|Geen|
|CatalogActiveUsers|Dagelijkse actieve gebruikers|Aantal|Totaal|Aantal actieve gebruikers per dag|Geen|
|CatalogUsage|Gebruiks distributie per bewerking|Aantal|Totaal|Geef aan hoeveel gebruiker van de bewerking de catalogus maakt, d.w.z. toegang, zoeken, woorden lijst.|Bewerking|


## <a name="microsoftdatafactorydatafactories"></a>Micro soft. DataFactory/datafactories

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|FailedRuns|Mislukte uitvoeringen|Aantal|Totaal||pipelineName, activiteitsnummer|
|SuccessfulRuns|Geslaagde uitvoeringen|Aantal|Totaal||pipelineName, activiteitsnummer|


## <a name="microsoftdatafactoryfactories"></a>Micro soft. DataFactory/fabrieken

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PipelineFailedRuns|Metrische gegevens van mislukte pijplijn uitvoeringen|Aantal|Totaal||FailureType, naam|
|PipelineSucceededRuns|Metrische uitvoerings metingen geslaagde pijp lijnen|Aantal|Totaal||FailureType, naam|
|PipelineCancelledRuns|Metrische gegevens van de pijplijn uitvoeringen geannuleerd|Aantal|Totaal||FailureType, naam|
|ActivityFailedRuns|Metrische gegevens mislukte uitvoering van activiteit|Aantal|Totaal||Activity type, PipelineName, FailureType, name|
|ActivitySucceededRuns|Metrische gegevens uitvoeringen uitgevoerde activiteit|Aantal|Totaal||Activity type, PipelineName, FailureType, name|
|ActivityCancelledRuns|Metrische gegevens voor geannuleerde activiteit uitgevoerd|Aantal|Totaal||Activity type, PipelineName, FailureType, name|
|TriggerFailedRuns|Meet waarden voor uitvoering van mislukte triggers|Aantal|Totaal||Naam, FailureType|
|TriggerSucceededRuns|Meet waarden voor uitvoering van geslaagde triggers|Aantal|Totaal||Naam, FailureType|
|TriggerCancelledRuns|Metrische gegevens over de trigger is geannuleerd|Aantal|Totaal||Naam, FailureType|
|IntegrationRuntimeCpuPercentage|CPU-gebruik van Integration runtime|Percentage|Average||IntegrationRuntimeName, knooppuntnaam|
|IntegrationRuntimeAvailableMemory|Beschik bare geheugen voor Integration runtime|Bytes|Average||IntegrationRuntimeName, knooppuntnaam|
|IntegrationRuntimeAverageTaskPickupDelay|Duur van de wachtrij voor Integration runtime|Seconden|Average||IntegrationRuntimeName|
|IntegrationRuntimeQueueLength|Lengte van de wachtrij voor Integration runtime|Aantal|Average||IntegrationRuntimeName|
|IntegrationRuntimeAvailableNodeNumber|Aantal beschik bare knoop punten voor Integration runtime|Aantal|Average||IntegrationRuntimeName|
|MaxAllowedResourceCount|Maximum aantal toegestane entiteiten|Aantal|Maximum||Geen|
|MaxAllowedFactorySizeInGbUnits|Maxi maal toegestane grootte van de fabriek (GB-eenheid)|Aantal|Maximum||Geen|
|ResourceCount|Totaal aantal entiteiten|Aantal|Maximum||Geen|
|FactorySizeInGbUnits|Totale grootte van de fabriek (GB-eenheid)|Aantal|Maximum||Geen|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Micro soft. DataLakeAnalytics/accounts

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|JobEndedSuccess|Geslaagde taken|Aantal|Totaal|Aantal geslaagde taken.|Geen|
|JobEndedFailure|Mislukte taken|Aantal|Totaal|Aantal mislukte taken.|Geen|
|JobEndedCancelled|Geannuleerde taken|Aantal|Totaal|Aantal geannuleerde taken.|Geen|
|JobAUEndedSuccess|Geslaagde AU-tijd|Seconden|Totaal|Totale AU-tijd voor voltooide taken.|Geen|
|JobAUEndedFailure|Mislukte AU-tijd|Seconden|Totaal|Totale AU-tijd voor mislukte taken.|Geen|
|JobAUEndedCancelled|Geannuleerde AU-tijd|Seconden|Totaal|Totale AU-tijd voor geannuleerde taken.|Geen|
|JobStage|Taken in fase|Aantal|Totaal|Aantal taken in elke fase.|Geen|


## <a name="microsoftdatalakestoreaccounts"></a>Micro soft. data Lake Store/accounts

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TotalStorage|Totale opslagruimte|Bytes|Maximum|De totale hoeveelheid gegevens die in het account is opgeslagen.|Geen|
|DataWritten|Gegevens geschreven|Bytes|Totaal|De totale hoeveelheid gegevens die naar het account wordt geschreven.|Geen|
|DataRead|Gegevens lezen|Bytes|Totaal|De totale hoeveelheid gegevens die uit het account is gelezen.|Geen|
|WriteRequests|Aanvragen schrijven|Aantal|Totaal|Het aantal aanvragen voor het schrijven van gegevens naar het account.|Geen|
|ReadRequests|Aanvragen lezen|Aantal|Totaal|Aantal aanvragen voor het lezen van gegevens voor het account.|Geen|


## <a name="microsoftdatashareaccounts"></a>Micro soft. DataShare/accounts

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ShareCount|Verzonden shares|Aantal|Maximum|Aantal verzonden shares in het account|ShareName|
|ShareSubscriptionCount|Ontvangen shares|Aantal|Maximum|Aantal ontvangen shares in het account|ShareSubscriptionName|
|SucceededShareSynchronizations|Moment opnamen van verzonden shares zijn voltooid|Aantal|Aantal|Aantal geslaagde moment opnamen van verzonden shares in het account|Geen|
|FailedShareSynchronizations|Mislukte moment opnamen van verzonden shares|Aantal|Aantal|Aantal mislukte moment opnamen van verzonden delen in het account|Geen|
|SucceededShareSubscriptionSynchronizations|Ontvangen moment opnamen van shares voltooid|Aantal|Aantal|Aantal ontvangen moment opnamen voor delen in het account|Geen|
|FailedShareSubscriptionSynchronizations|Mislukte moment opnamen van share ontvangen|Aantal|Aantal|Aantal mislukte moment opnamen van ontvangen shares in het account|Geen|


## <a name="microsoftdbformariadbservers"></a>Micro soft. DBforMariaDB/servers

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Percentage|Average|CPU-percentage|Geen|
|memory_percent|Geheugen percentage|Percentage|Average|Geheugen percentage|Geen|
|io_consumption_percent|IO-percentage|Percentage|Average|IO-percentage|Geen|
|storage_percent|Opslag percentage|Percentage|Average|Opslag percentage|Geen|
|storage_used|Gebruikte opslag|Bytes|Average|Gebruikte opslag|Geen|
|storage_limit|Opslag limiet|Bytes|Maximum|Opslag limiet|Geen|
|serverlog_storage_percent|Percentage server logboek opslag|Percentage|Average|Percentage server logboek opslag|Geen|
|serverlog_storage_usage|Gebruikte server logboek opslag|Bytes|Average|Gebruikte server logboek opslag|Geen|
|serverlog_storage_limit|Opslag limiet voor server logboek|Bytes|Average|Opslag limiet voor server logboek|Geen|
|active_connections|Actieve verbindingen|Aantal|Average|Actieve verbindingen|Geen|
|connections_failed|Mislukte verbindingen|Aantal|Totaal|Mislukte verbindingen|Geen|
|seconds_behind_master|Replicatie vertraging in seconden|Aantal|Maximum|Replicatie vertraging in seconden|Geen|
|backup_storage_used|Gebruikte back-upopslag|Bytes|Average|Gebruikte back-upopslag|Geen|
|network_bytes_egress|Netwerk uit|Bytes|Totaal|Netwerk uit over actieve verbindingen|Geen|
|network_bytes_ingress|Netwerk in|Bytes|Totaal|Netwerk in meerdere actieve verbindingen|Geen|


## <a name="microsoftdbformysqlservers"></a>Micro soft. DBforMySQL/servers

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Percentage|Average|CPU-percentage|Geen|
|memory_percent|Geheugen percentage|Percentage|Average|Geheugen percentage|Geen|
|io_consumption_percent|IO-percentage|Percentage|Average|IO-percentage|Geen|
|storage_percent|Opslag percentage|Percentage|Average|Opslag percentage|Geen|
|storage_used|Gebruikte opslag|Bytes|Average|Gebruikte opslag|Geen|
|storage_limit|Opslag limiet|Bytes|Maximum|Opslag limiet|Geen|
|serverlog_storage_percent|Percentage server logboek opslag|Percentage|Average|Percentage server logboek opslag|Geen|
|serverlog_storage_usage|Gebruikte server logboek opslag|Bytes|Average|Gebruikte server logboek opslag|Geen|
|serverlog_storage_limit|Opslag limiet voor server logboek|Bytes|Maximum|Opslag limiet voor server logboek|Geen|
|active_connections|Actieve verbindingen|Aantal|Average|Actieve verbindingen|Geen|
|connections_failed|Mislukte verbindingen|Aantal|Totaal|Mislukte verbindingen|Geen|
|seconds_behind_master|Replicatie vertraging in seconden|Aantal|Maximum|Replicatie vertraging in seconden|Geen|
|backup_storage_used|Gebruikte back-upopslag|Bytes|Average|Gebruikte back-upopslag|Geen|
|network_bytes_egress|Netwerk uit|Bytes|Totaal|Netwerk uit over actieve verbindingen|Geen|
|network_bytes_ingress|Netwerk in|Bytes|Totaal|Netwerk in meerdere actieve verbindingen|Geen|


## <a name="microsoftdbforpostgresqlservers"></a>Micro soft. DBforPostgreSQL/servers

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Percentage|Average|CPU-percentage|Geen|
|memory_percent|Geheugen percentage|Percentage|Average|Geheugen percentage|Geen|
|io_consumption_percent|IO-percentage|Percentage|Average|IO-percentage|Geen|
|storage_percent|Opslag percentage|Percentage|Average|Opslag percentage|Geen|
|storage_used|Gebruikte opslag|Bytes|Average|Gebruikte opslag|Geen|
|storage_limit|Opslag limiet|Bytes|Maximum|Opslag limiet|Geen|
|serverlog_storage_percent|Percentage server logboek opslag|Percentage|Average|Percentage server logboek opslag|Geen|
|serverlog_storage_usage|Gebruikte server logboek opslag|Bytes|Average|Gebruikte server logboek opslag|Geen|
|serverlog_storage_limit|Opslag limiet voor server logboek|Bytes|Maximum|Opslag limiet voor server logboek|Geen|
|active_connections|Actieve verbindingen|Aantal|Average|Actieve verbindingen|Geen|
|connections_failed|Mislukte verbindingen|Aantal|Totaal|Mislukte verbindingen|Geen|
|backup_storage_used|Gebruikte back-upopslag|Bytes|Average|Gebruikte back-upopslag|Geen|
|network_bytes_egress|Netwerk uit|Bytes|Totaal|Netwerk uit over actieve verbindingen|Geen|
|network_bytes_ingress|Netwerk in|Bytes|Totaal|Netwerk in meerdere actieve verbindingen|Geen|
|pg_replica_log_delay_in_seconds|Replica vertraging|Seconden|Maximum|Replica vertraging in seconden|Geen|
|pg_replica_log_delay_in_bytes|Maximale vertraging in Replica's|Bytes|Maximum|Vertraging in bytes van de meest vertraagde replica|Geen|


## <a name="microsoftdbforpostgresqlserversv2"></a>Micro soft. DBforPostgreSQL/serversv2

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Percentage|Average|CPU-percentage|Geen|
|memory_percent|Geheugen percentage|Percentage|Average|Geheugen percentage|Geen|
|IOPS|IOPS|Aantal|Average|I/o-bewerkingen per seconde|Geen|
|storage_percent|Opslag percentage|Percentage|Average|Opslag percentage|Geen|
|storage_used|Gebruikte opslag|Bytes|Average|Gebruikte opslag|Geen|
|active_connections|Actieve verbindingen|Aantal|Average|Actieve verbindingen|Geen|
|network_bytes_egress|Netwerk uit|Bytes|Totaal|Netwerk uit over actieve verbindingen|Geen|
|network_bytes_ingress|Netwerk in|Bytes|Totaal|Netwerk in meerdere actieve verbindingen|Geen|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Micro soft. DBforPostgreSQL/singleservers

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Percentage|Average|CPU-percentage|Geen|
|memory_percent|Geheugen percentage|Percentage|Average|Geheugen percentage|Geen|
|IOPS|IOPS|Aantal|Average|I/o-bewerkingen per seconde|Geen|
|storage_percent|Opslag percentage|Percentage|Average|Opslag percentage|Geen|
|storage_used|Gebruikte opslag|Bytes|Average|Gebruikte opslag|Geen|
|active_connections|Actieve verbindingen|Aantal|Average|Actieve verbindingen|Geen|
|network_bytes_egress|Netwerk uit|Bytes|Totaal|Netwerk uit over actieve verbindingen|Geen|
|network_bytes_ingress|Netwerk in|Bytes|Totaal|Netwerk in meerdere actieve verbindingen|Geen|
|connections_failed|Mislukte verbindingen|Aantal|Totaal|Mislukte verbindingen|Geen|
|connections_succeeded|Geslaagde verbindingen|Aantal|Totaal|Geslaagde verbindingen|Geen|
|maximum_used_transactionIDs|Maximum aantal gebruikte trans actie-Id's|Aantal|Average|Maximum aantal gebruikte trans actie-Id's|Geen|





## <a name="microsoftdevicesiothubs"></a>Micro soft. devices/IotHubs

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|D2C. telemetrie. ingress. allProtocol|Verzend pogingen voor telemetrie-berichten|Aantal|Totaal|Aantal pogingen voor het verzenden van apparaat-naar-Cloud-telemetrie naar uw IoT hub|Geen|
|D2C. telemetrie. ingress. geslaagd|Verzonden telemetriegegevens|Aantal|Totaal|Aantal te verzenden apparaat-naar-Cloud-telemetrie-berichten naar uw IoT-hub|Geen|
|C2D. commands. OUTuitgang. complete. geslaagd|C2D-bericht leveringen voltooid|Aantal|Totaal|Aantal bezorgingen van Cloud-naar-apparaat-berichten voltooid door het apparaat|Geen|
|C2D. commands. uitgangs. Abandon. geslaagd|C2D-berichten zijn afgebroken|Aantal|Totaal|Aantal Cloud-naar-apparaat-berichten die zijn afgebroken door het apparaat|Geen|
|C2D. commands. uitgangs. reject. geslaagd|Geweigerde C2D-berichten|Aantal|Totaal|Aantal Cloud-naar-apparaat-berichten dat door het apparaat is geweigerd|Geen|
|C2DMessagesExpired|C2D-berichten verlopen (preview-versie)|Aantal|Totaal|Aantal verlopen Cloud-naar-apparaat-berichten|Geen|
|apparaten. totalDevices|Totaal aantal apparaten (afgeschaft)|Aantal|Totaal|Aantal apparaten dat is geregistreerd bij uw IoT-hub|Geen|
|apparaten. connectedDevices. allProtocol|Verbonden apparaten (afgeschaft) |Aantal|Totaal|Aantal apparaten dat is verbonden met uw IoT-hub|Geen|
|D2C. telemetrie. uitgangs. geslaagd|Route ring: telemetrie-berichten|Aantal|Totaal|Het aantal keren dat berichten zijn bezorgd bij alle eind punten met behulp van IoT Hub route ring. Als een bericht wordt doorgestuurd naar meerdere eind punten, wordt deze waarde met één verhoogd voor elke geslaagde levering. Als een bericht meerdere keren op hetzelfde eind punt wordt bezorgd, wordt deze waarde met één verhoogd voor elke geslaagde levering.|Geen|
|D2C. telemetrie. uitgangs. verwijderd|Route ring: telemetrie-berichten verwijderd |Aantal|Totaal|Het aantal keren dat berichten zijn verwijderd door IoT Hub route ring vanwege Dead-eind punten. Deze waarde telt geen berichten die worden bezorgd als terugval route als berichten die worden verzonden, niet worden bezorgd.|Geen|
|D2C. telemetrie.. zwevend|Route ring: telemetriegegevens van zwevende berichten |Aantal|Totaal|Het aantal keren dat berichten zijn zwevend door IoT Hub route ring, omdat ze niet overeenkomen met de regels voor door sturen (inclusief de terugval regel). |Geen|
|D2C. telemetrie. uitgangs. ongeldig|Route ring: telemetrie-berichten incompatibel|Aantal|Totaal|Het aantal keren dat IoT Hub route ring geen berichten kan leveren als gevolg van incompatibiliteit met het eind punt. Deze waarde omvat geen nieuwe pogingen.|Geen|
|D2C. telemetrie.. fallback|Route ring: berichten worden bezorgd bij terugval|Aantal|Totaal|Het aantal keren dat de route ring van berichten IoT Hub verzonden naar het eind punt dat is gekoppeld aan de terugval route.|Geen|
|D2C. endpoints. uitgangs. Event hubs|Route ring: berichten worden bezorgd bij Event hub|Aantal|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan Event hub-eind punten.|Geen|
|D2C. endpoints. latentie. Event hubs|Route ring: bericht latentie voor Event hub|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten IoT Hub en het binnenkomen van berichten in een event hub-eind punt.|Geen|
|D2C. endpoints. uitgangs. serviceBusQueues|Route ring: berichten worden bezorgd bij Service Bus wachtrij|Aantal|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan Service Bus-wachtrij-eind punten.|Geen|
|D2C. endpoints. latentie. serviceBusQueues|Route ring: bericht latentie voor Service Bus wachtrij|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en telemetrie-berichten in een Service Bus wachtrij-eind punt.|Geen|
|D2C. endpoints. uitgangs. serviceBusTopics|Route ring: berichten die worden bezorgd bij Service Bus onderwerp|Aantal|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan Service Bus onderwerp-eind punten.|Geen|
|D2C. endpoints. latentie. serviceBusTopics|Route ring: bericht latentie voor Service Bus onderwerp|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en telemetrie-berichten in het eind punt van een Service Bus onderwerp.|Geen|
|D2C. endpoints. uitgangs punt. builtIn. Events|Route ring: berichten worden bezorgd bij berichten/gebeurtenissen|Aantal|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan het ingebouwde eind punt (berichten/gebeurtenissen).|Geen|
|D2C. endpoints. latentie. builtIn. Events|Route ring: bericht latentie voor berichten/gebeurtenissen|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en het inkomend telemetrie-bericht in het ingebouwde eind punt (berichten/gebeurtenissen).|Geen|
|D2C. endpoints. outwaarde. Storage|Route ring: berichten worden bezorgd bij de opslag|Aantal|Totaal|Het aantal keren dat IoT Hub route ring berichten heeft geleverd aan de opslag eindpunten.|Geen|
|D2C. endpoints. latentie. opslag|Route ring: bericht latentie voor opslag|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en telemetrie-berichten in een opslag eindpunt.|Geen|
|D2C. endpoints. out. storage. bytes|Route ring: gegevens worden geleverd aan de opslag|Bytes|Totaal|De hoeveelheid gegevens (bytes) IoT Hub route ring die aan de opslag eindpunten wordt geleverd.|Geen|
|D2C. endpoints. outwaar. storage. blobs|Route ring: blobs die aan de opslag worden geleverd|Aantal|Totaal|Het aantal keren dat IoT Hub route ring blobs naar opslag eindpunten heeft geleverd.|Geen|
|EventGridDeliveries|Event Grid leveringen (preview-versie)|Aantal|Totaal|Het aantal IoT Hub gebeurtenissen dat is gepubliceerd op Event Grid. Gebruik de dimensie resultaat voor het aantal geslaagde en mislukte aanvragen. De dimensie type-tekst geeft het soort gebeurtenis weer ( https://aka.ms/ioteventgrid) .|ResourceId, resultaat, type gebeurtenis|
|EventGridLatency|Event Grid latentie (preview-versie)|Milliseconden|Average|De gemiddelde latentie (in milliseconden) vanaf het moment waarop de IOT hub-gebeurtenis werd gegenereerd toen de gebeurtenis werd gepubliceerd in Event Grid. Dit getal is een gemiddelde tussen alle gebeurtenis typen. Gebruik de dimensie type type om de latentie van een specifiek soort gebeurtenis weer te geven.|ResourceId, Event type|
|RoutingDeliveries|Route ring van bezorgingen (preview-versie)|Milliseconden|Totaal|Het aantal keren dat IoT Hub probeerde berichten te leveren aan alle eind punten met behulp van route ring. Als u het aantal geslaagde of mislukte pogingen wilt zien, gebruikt u de dimensie resultaat. Gebruik de dimensie FailureReasonCategory om de reden van de fout te zien, zoals ongeldig, verwijderd of zwevend. U kunt ook de dimensies Endpointnaam en EndpointType gebruiken om te begrijpen hoeveel berichten er aan uw verschillende eind punten zijn geleverd. De waarde van de metriek neemt toe met één voor elke bezorgings poging, ook als het bericht wordt bezorgd bij meerdere eind punten of als het bericht meerdere keren wordt bezorgd bij hetzelfde eind punt.|ResourceId, EndpointType, eind punt, FailureReasonCategory, resultaat, RoutingSource|
|RoutingDeliveryLatency|Bezorg latentie van route ring (preview-versie)|Milliseconden|Average|De gemiddelde latentie (in milliseconden) tussen het binnenkomen van berichten naar IoT Hub en het inkomend telemetrie-bericht in een eind punt. U kunt de dimensies Endpointnaam en EndpointType gebruiken om inzicht te krijgen in de latentie van uw verschillende eind punten.|ResourceId, EndpointType, eind punt, RoutingSource|
|D2C. dubbele. lezen. geslaagd|Geslaagde dubbele Lees bewerkingen van apparaten|Aantal|Totaal|De telling van alle geslaagde apparaten met dubbele Lees bewerkingen.|Geen|
|D2C. dubbele. Read. failure|Mislukte dubbele Lees bewerkingen van apparaten|Aantal|Totaal|Het aantal apparaten dat niet kan worden gestart, dubbele Lees bewerkingen.|Geen|
|D2C. dubbele. Lees. grootte|Reactie grootte van dubbele Lees bewerkingen van apparaten|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde apparaten-geïnitieerde dubbele Lees bewerkingen.|Geen|
|D2C. dubbele. update. geslaagd|Geslaagde dubbele updates van apparaten|Aantal|Totaal|De telling van alle geslaagde, door het apparaat geïnitieerde dubbele updates.|Geen|
|D2C. dubbele. update. failure|Mislukte dubbele updates van apparaten|Aantal|Totaal|Het aantal apparaten dat door een apparaat is gestart en dubbele updates heeft uitgevoerd.|Geen|
|D2C. dubbele. update. grootte|Grootte van dubbele updates van apparaten|Bytes|Average|Het gemiddelde, het minimum en de maximale grootte van alle geslaagde, door het apparaat geïnitieerde dubbele updates.|Geen|
|C2D. methods. geslaagd|Geslaagde directe aanroepen van de methode|Aantal|Totaal|Het aantal voltooide direct-methode aanroepen.|Geen|
|C2D. methods. failure|Mislukte directe aanroepen van methode|Aantal|Totaal|Het aantal mislukte direct-methode aanroepen.|Geen|
|C2D. methods. requestSize|Aanvraag grootte van directe-methode aanroepen|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde direct-methode aanvragen.|Geen|
|C2D. methods. responseSize|Antwoord grootte van directe methode aanroepen|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde reacties van de methode direct.|Geen|
|C2D. dubbele. lezen. geslaagd|Geslaagde dubbele Lees bewerkingen van back-end|Aantal|Totaal|Het aantal geslaagde back-end-geïnitieerde dubbele Lees bewerkingen.|Geen|
|C2D. dubbele. Read. failure|Mislukte dubbele Lees bewerkingen van back-end|Aantal|Totaal|Het aantal mislukte back-end-geïnitieerde dubbele Lees bewerkingen.|Geen|
|C2D. dubbele. Lees. grootte|Reactie grootte van dubbele Lees bewerkingen van de back-end|Bytes|Average|Het gemiddelde, het minimum en het maximum van alle geslaagde back-end-geïnitieerde dubbele Lees bewerkingen.|Geen|
|C2D. dubbele. update. geslaagd|Geslaagde dubbele updates van back-end|Aantal|Totaal|Het aantal geslaagde, door de back-end gestarte dubbele updates.|Geen|
|C2D. dubbele. update. failure|Mislukte dubbele updates van back-end|Aantal|Totaal|Het aantal niet-geslaagde, door de back-end geïnitieerde dubbele updates.|Geen|
|C2D. dubbele. update. grootte|Grootte van dubbele updates van back-end|Bytes|Average|Het gemiddelde, het minimum en de maximale grootte van alle geslaagde back-end-geïnitieerde dubbele updates.|Geen|
|twinQueries. geslaagd|Geslaagde dubbele query's|Aantal|Totaal|Het aantal geslaagde dubbele query's.|Geen|
|twinQueries. failure|Mislukte dubbele query's|Aantal|Totaal|Het aantal mislukte dubbele query's.|Geen|
|twinQueries.resultSize|Resultaat grootte van dubbele query's|Bytes|Average|Het gemiddelde, het minimum en het maximum van de resultaat grootte van alle geslaagde dubbele query's.|Geen|
|Jobs. createTwinUpdateJob. geslaagd|Geslaagde creatie van dubbele update taken|Aantal|Totaal|Het aantal van alle geslaagde taken voor het maken van dubbele updates.|Geen|
|Jobs. createTwinUpdateJob. failure|Kan geen dubbele update taken uitvoeren|Aantal|Totaal|Het aantal mislukte het maken van dubbele update taken.|Geen|
|Jobs. createDirectMethodJob. geslaagd|Geslaagde creatie van methode aanroep taken|Aantal|Totaal|Het aantal van alle geslaagde aanroepen van directe methode aanroep taken.|Geen|
|Jobs. createDirectMethodJob. failure|Kan geen aanroepen van methode aanroep taken uitvoeren|Aantal|Totaal|Het aantal van alle mislukte aanroepen van directe methode aanroep taken.|Geen|
|Jobs. listJobs. geslaagd|Geslaagde aanroepen naar lijst taken|Aantal|Totaal|Het aantal geslaagde aanroepen naar lijst taken.|Geen|
|Jobs. listJobs. failure|Mislukte aanroepen naar lijst taken|Aantal|Totaal|Het aantal mislukte aanroepen naar lijst taken.|Geen|
|Jobs. cancelJob. geslaagd|Voltooide taak annuleringen|Aantal|Totaal|Het aantal geslaagde aanroepen om een taak te annuleren.|Geen|
|Jobs. cancelJob. failure|Mislukte taak annuleringen|Aantal|Totaal|Het aantal mislukte aanroepen om een taak te annuleren.|Geen|
|Jobs. queryJobs. geslaagd|Geslaagde taak query's|Aantal|Totaal|Het aantal geslaagde aanroepen naar query taken.|Geen|
|Jobs. queryJobs. failure|Mislukte taak query's|Aantal|Totaal|Het aantal mislukte aanroepen naar query taken.|Geen|
|Jobs. voltooid|Voltooide taken|Aantal|Totaal|Het aantal voltooide taken.|Geen|
|Jobs. mislukt|Mislukte taken|Aantal|Totaal|Het aantal mislukte taken.|Geen|
|D2C. telemetrie. ingress. sendThrottle|Aantal beperkings fouten|Aantal|Totaal|Aantal beperkings fouten door doorvoer vertraging van apparaat|Geen|
|dailyMessageQuotaUsed|Totaal aantal gebruikte berichten|Aantal|Average|Totaal aantal gebruikte berichten vandaag|Geen|
|deviceDataUsage|Totale hoeveelheid gegevens gebruik van apparaat|Bytes|Totaal|Verzonden bytes van en naar apparaten die zijn verbonden met IotHub|Geen|
|deviceDataUsageV2|Totaal gebruik van apparaatgegevens (preview-versie)|Bytes|Totaal|Verzonden bytes van en naar apparaten die zijn verbonden met IotHub|Geen|
|totalDeviceCount|Totaal aantal apparaten (preview-versie)|Aantal|Average|Aantal apparaten dat is geregistreerd bij uw IoT-hub|Geen|
|connectedDeviceCount|Verbonden apparaten (preview-versie)|Aantal|Average|Aantal apparaten dat is verbonden met uw IoT-hub|Geen|
|configuraties|Metrische configuratie gegevens|Aantal|Totaal|Metrische gegevens voor configuratie bewerkingen|Geen|


## <a name="microsoftdevicesprovisioningservices"></a>Micro soft. devices/provisioningServices

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|RegistrationAttempts|Registratie pogingen|Aantal|Totaal|Aantal pogingen voor apparaatregistratie|ProvisioningServiceName, IotHubName, status|
|DeviceAssignments|Apparaten toegewezen|Aantal|Totaal|Aantal apparaten dat is toegewezen aan een IoT-hub|ProvisioningServiceName,IotHubName|
|AttestationAttempts|Attestation-pogingen|Aantal|Totaal|Aantal pogingen voor het uitvoeren van een apparaat|ProvisioningServiceName, status, protocol|




## <a name="microsoftdocumentdbdatabaseaccounts"></a>Micro soft. DocumentDB/databaseAccounts

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AddRegion|Regio toegevoegd|Aantal|Aantal|Regio toegevoegd|Regio|
|AvailableStorage|Beschikbare opslag|Bytes|Totaal|Totale beschik bare opslag gerapporteerd bij een granulatie van 5 minuten|Verzamelingnaam, databasenaam, regio|
|CassandraConnectionClosures|Cassandra-verbinding sluiten|Aantal|Totaal|Aantal Cassandra-verbindingen dat is gesloten, gerapporteerd met een granulatie van 1 minuut|APIType, regio, ClosureReason|
|CassandraKeyspaceDelete|Cassandra-opslag ruimte verwijderd|Aantal|Aantal|Cassandra-opslag ruimte verwijderd|ResourceName, soort, ApiKindResourceType, OperationType|
|CassandraKeyspaceThroughputUpdate|Cassandra voor de door Voer van de opslag ruimte bijgewerkt|Aantal|Aantal|Cassandra voor de door Voer van de opslag ruimte bijgewerkt|ResourceName, soort, ApiKindResourceType, IsThroughputRequest|
|CassandraKeyspaceUpdate|Cassandra-opslag ruimte bijgewerkt|Aantal|Aantal|Cassandra-opslag ruimte bijgewerkt|ResourceName, soort, ApiKindResourceType, IsThroughputRequest|
|CassandraRequestCharges|Kosten voor Cassandra-aanvragen|Aantal|Totaal|RUs gebruikt voor Cassandra-aanvragen|APIType, DATABASENAME, verzamelingnaam, regio, OperationType, resource type|
|CassandraRequests|Cassandra aanvragen|Aantal|Aantal|Aantal gemaakte Cassandra-aanvragen|APIType, DATABASENAME, verzamelingnaam, regio, OperationType, resource type, error code|
|CassandraTableDelete|Cassandra-tabel verwijderd|Aantal|Aantal|Cassandra-tabel verwijderd|ResourceName, ChildResourceName, soort, ApiKindResourceType, OperationType|
|CassandraTableThroughputUpdate|Cassandra-tabel doorvoer bijgewerkt|Aantal|Aantal|Cassandra-tabel doorvoer bijgewerkt|ResourceName, ChildResourceName, soort, ApiKindResourceType, IsThroughputRequest|
|CassandraTableUpdate|Cassandra-tabel bijgewerkt|Aantal|Aantal|Cassandra-tabel bijgewerkt|ResourceName, ChildResourceName, soort, ApiKindResourceType, IsThroughputRequest|
|CreateAccount|Account gemaakt|Aantal|Aantal|Account gemaakt|Geen|
|DataUsage|Gegevensgebruik|Bytes|Totaal|Totaal gegevens gebruik gerapporteerd bij 5 minuten granulariteit|Verzamelingnaam, databasenaam, regio|
|DeleteAccount|Account is verwijderd|Aantal|Aantal|Account is verwijderd|Geen|
|DocumentCount|Aantal documenten|Aantal|Totaal|Totaal aantal documenten gemeld bij 5 minuten granulariteit|Verzamelingnaam, databasenaam, regio|
|DocumentQuota|Document quotum|Bytes|Totaal|Totale opslag quotum gerapporteerd bij een granulatie van 5 minuten|Verzamelingnaam, databasenaam, regio|
|GremlinDatabaseDelete|Gremlin-data base verwijderd|Aantal|Aantal|Gremlin-data base verwijderd|ResourceName, soort, ApiKindResourceType, OperationType|
|GremlinDatabaseThroughputUpdate|Gremlin-data base-door Voer bijgewerkt|Aantal|Aantal|Gremlin-data base-door Voer bijgewerkt|ResourceName, soort, ApiKindResourceType, IsThroughputRequest|
|GremlinDatabaseUpdate|Gremlin-data base bijgewerkt|Aantal|Aantal|Gremlin-data base bijgewerkt|ResourceName, soort, ApiKindResourceType, IsThroughputRequest|
|GremlinGraphDelete|Gremlin-grafiek verwijderd|Aantal|Aantal|Gremlin-grafiek verwijderd|ResourceName, ChildResourceName, soort, ApiKindResourceType, OperationType|
|GremlinGraphThroughputUpdate|Gremlin-grafiek doorvoer bijgewerkt|Aantal|Aantal|Gremlin-grafiek doorvoer bijgewerkt|ResourceName, ChildResourceName, soort, ApiKindResourceType, IsThroughputRequest|
|GremlinGraphUpdate|Gremlin-grafiek bijgewerkt|Aantal|Aantal|Gremlin-grafiek bijgewerkt|ResourceName, ChildResourceName, soort, ApiKindResourceType, IsThroughputRequest|
|IndexUsage|Indexgebruik|Bytes|Totaal|Totaal gebruik van index gerapporteerd bij een granulatie van 5 minuten|Verzamelingnaam, databasenaam, regio|
|MetadataRequests|Meta gegevens aanvragen|Aantal|Aantal|Aantal aanvragen voor meta gegevens. Cosmos DB onderhoudt de verzameling van meta gegevens van het systeem voor elk account, waarmee u verzamelingen, data bases, enzovoort en hun configuraties gratis kunt inventariseren.|DATABASENAME, verzamel-, regio, status code, rol|
|MongoCollectionDelete|Mongo-verzameling verwijderd|Aantal|Aantal|Mongo-verzameling verwijderd|ResourceName, ChildResourceName, soort, ApiKindResourceType, OperationType|
|MongoCollectionThroughputUpdate|Door Voer van Mongo-verzameling bijgewerkt|Aantal|Aantal|Door Voer van Mongo-verzameling bijgewerkt|ResourceName, ChildResourceName, soort, ApiKindResourceType, IsThroughputRequest|
|MongoCollectionUpdate|Mongo-verzameling bijgewerkt|Aantal|Aantal|Mongo-verzameling bijgewerkt|ResourceName, ChildResourceName, soort, ApiKindResourceType, IsThroughputRequest|
|MongoDBDatabaseUpdate|Mongo-data base bijgewerkt|Aantal|Aantal|Mongo-data base bijgewerkt|ResourceName, soort, ApiKindResourceType, IsThroughputRequest|
|MongoDatabaseDelete|Mongo-data base verwijderd|Aantal|Aantal|Mongo-data base verwijderd|ResourceName, soort, ApiKindResourceType, OperationType|
|MongoDatabaseThroughputUpdate|Mongo-data base-door Voer bijgewerkt|Aantal|Aantal|Mongo-data base-door Voer bijgewerkt|ResourceName, soort, ApiKindResourceType, IsThroughputRequest|
|MongoRequestCharge|Kosten voor Mongo-aanvragen|Aantal|Totaal|Verbruikte Mongo-aanvraag eenheden|DATABASENAME, verzamel-, regio, opdrachtnaam, error code, status|
|MongoRequests|Mongo aanvragen|Aantal|Aantal|Aantal gemaakte Mongo-aanvragen|DATABASENAME, verzamel-, regio, opdrachtnaam, error code, status|
|MongoRequestsCount|Frequentie van Mongo-aanvragen|CountPerSecond|Average|Aantal Mongo per seconde|DATABASENAME, verzamel-, regio, opdrachtnaam, error code|
|MongoRequestsDelete|Aantal Mongo-aanvragen voor verwijderen|CountPerSecond|Average|Mongo-aanvraag voor verwijderen per seconde|DATABASENAME, verzamel-, regio, opdrachtnaam, error code|
|MongoRequestsInsert|Aantal Mongo invoegen|CountPerSecond|Average|Aantal Mongo per seconde|DATABASENAME, verzamel-, regio, opdrachtnaam, error code|
|MongoRequestsQuery|Frequentie van Mongo-query aanvragen|CountPerSecond|Average|Mongo-query aanvraag per seconde|DATABASENAME, verzamel-, regio, opdrachtnaam, error code|
|MongoRequestsUpdate|Frequentie van Mongo-update aanvragen|CountPerSecond|Average|Aanvraag voor Mongo-update per seconde|DATABASENAME, verzamel-, regio, opdrachtnaam, error code|
|NormalizedRUConsumption|Genormaliseerd RU-verbruik|Percentage|Maximum|Maximum aantal van RU verbruik per minuut|Verzamelingnaam, databasenaam, regio|
|ProvisionedThroughput|Ingerichte doorvoer|Aantal|Maximum|Ingerichte doorvoer|DATABASENAME, verzamelnaam|
|RegionFailover|Er is een failover uitgevoerd voor de regio|Aantal|Aantal|Er is een failover uitgevoerd voor de regio|Geen|
|RemoveRegion|Regio is verwijderd|Aantal|Aantal|Regio is verwijderd|Regio|
|ReplicationLatency|P99-replicatie latentie|Milliseconden|Average|Replicatie latentie van P99 voor de bron-en doel regio's voor geografisch ingeschakelde account|SourceRegion,TargetRegion|
|ServerSideLatency|Latentie aan server zijde|Milliseconden|Average|Latentie aan server zijde|DATABASENAME, verzamel-, regio, ConnectionMode, OperationType, PublicAPIType|
|ServiceAvailability|Service beschikbaarheid|Percentage|Average|Beschik baarheid van account aanvragen op één uur, dag of maand granulatie|Geen|
|SqlContainerDelete|SQL-container verwijderd|Aantal|Aantal|SQL-container verwijderd|ResourceName, ChildResourceName, soort, ApiKindResourceType, OperationType|
|SqlContainerThroughputUpdate|SQL-container doorvoer bijgewerkt|Aantal|Aantal|SQL-container doorvoer bijgewerkt|ResourceName, ChildResourceName, soort, ApiKindResourceType, IsThroughputRequest|
|SqlContainerUpdate|SQL-container is bijgewerkt|Aantal|Aantal|SQL-container is bijgewerkt|ResourceName, ChildResourceName, soort, ApiKindResourceType, IsThroughputRequest|
|SqlDatabaseDelete|SQL-data base verwijderd|Aantal|Aantal|SQL-data base verwijderd|ResourceName, soort, ApiKindResourceType, OperationType|
|SqlDatabaseThroughputUpdate|SQL Data Base-door Voer bijgewerkt|Aantal|Aantal|SQL Data Base-door Voer bijgewerkt|ResourceName, soort, ApiKindResourceType, IsThroughputRequest|
|SqlDatabaseUpdate|SQL-data base bijgewerkt|Aantal|Aantal|SQL-data base bijgewerkt|ResourceName, soort, ApiKindResourceType, IsThroughputRequest|
|TableTableDelete|AzureTable-tabel verwijderd|Aantal|Aantal|AzureTable-tabel verwijderd|ResourceName, soort, ApiKindResourceType, OperationType|
|TableTableThroughputUpdate|AzureTable-tabel doorvoer bijgewerkt|Aantal|Aantal|AzureTable-tabel doorvoer bijgewerkt|ResourceName, soort, ApiKindResourceType, IsThroughputRequest|
|TableTableUpdate|AzureTable-tabel bijgewerkt|Aantal|Aantal|AzureTable-tabel bijgewerkt|ResourceName, soort, ApiKindResourceType, IsThroughputRequest|
|TotalRequestUnits|Totaal aantal aanvraag eenheden|Aantal|Totaal|Verbruikte aanvraag eenheden|DATABASENAME, verzamelings instelling, regio, status code, OperationType, status|
|TotalRequests|Totaal aantal aanvragen|Aantal|Aantal|Aantal aanvragen dat is gedaan|DATABASENAME, verzamelings instelling, regio, status code, OperationType, status|
|UpdateAccountKeys|Bijgewerkte account sleutels|Aantal|Aantal|Bijgewerkte account sleutels|KeyType|
|UpdateAccountNetworkSettings|De netwerk instellingen voor het account zijn bijgewerkt|Aantal|Aantal|De netwerk instellingen voor het account zijn bijgewerkt|Geen|
|UpdateAccountReplicationSettings|De replicatie-instellingen voor het account zijn bijgewerkt|Aantal|Aantal|De replicatie-instellingen voor het account zijn bijgewerkt|Geen|
|UpdateDiagnosticsSettings|De diagnostische instellingen voor het account zijn bijgewerkt|Aantal|Aantal|De diagnostische instellingen voor het account zijn bijgewerkt|DiagnosticSettingsName, ResourceGroupName|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Micro soft. EnterpriseKnowledgeGraph/Services

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TransactionCount|Aantal trans acties|Aantal|Aantal|Totaal aantal trans acties|TransactionCount|
|SuccessCount|Aantal geslaagd|Aantal|Aantal|Aantal geslaagde trans acties|SuccessCount|
|FailureCount|Aantal mislukt|Aantal|Aantal|Aantal mislukte trans acties|FailureCount|
|SuccessLatency|Geslaagde latentie|Milliseconden|Average|Latentie van geslaagde trans acties|SuccessCount|

## <a name="microsofteventgriddomains"></a>Micro soft. EventGrid/domeinen

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PublishSuccessCount|Gepubliceerde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat naar dit onderwerp is gepubliceerd|Onderwerp|
|PublishFailCount|Mislukte gebeurtenissen publiceren|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet naar dit onderwerp kan worden gepubliceerd|Onderwerp, error type, fout|
|PublishSuccessLatencyInMs|Latentie van publicatie geslaagd|Milliseconden|Totaal|Latentie van geslaagde publicatie in milliseconden|Geen|
|MatchedEventCount|Overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName|
|DeliveryAttemptFailCount|Mislukte leverings gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet aan dit gebeurtenis abonnement kan worden geleverd|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName, fout, error type|
|DeliverySuccessCount|Geleverde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat aan dit gebeurtenis abonnement is geleverd|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Doel verwerkings duur|Milliseconden|Average|Doel verwerkings duur in milliseconden|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Verwijderde gebeurtenissen|Aantal|Totaal|Totaal aantal verloren gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|DeadLetteredCount|Gebeurtenissen met onbestelbare berichten|Aantal|Totaal|Totaal aantal gebeurtenissen met onbestelbare berichten die overeenkomen met dit gebeurtenis abonnement|Onderwerp, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Micro soft. EventGrid/topics

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PublishSuccessCount|Gepubliceerde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat naar dit onderwerp is gepubliceerd|Geen|
|PublishFailCount|Mislukte gebeurtenissen publiceren|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet naar dit onderwerp kan worden gepubliceerd|Error type, fout|
|UnmatchedEventCount|Niet-overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet overeenkomt met een van de gebeurtenis abonnementen voor dit onderwerp|Geen|
|PublishSuccessLatencyInMs|Latentie van publicatie geslaagd|Milliseconden|Totaal|Latentie van geslaagde publicatie in milliseconden|Geen|
|MatchedEventCount|Overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|EventSubscriptionName|
|DeliveryAttemptFailCount|Mislukte leverings gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet aan dit gebeurtenis abonnement kan worden geleverd|Fout, error type, EventSubscriptionName|
|DeliverySuccessCount|Geleverde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat aan dit gebeurtenis abonnement is geleverd|EventSubscriptionName|
|DestinationProcessingDurationInMs|Doel verwerkings duur|Milliseconden|Average|Doel verwerkings duur in milliseconden|EventSubscriptionName|
|DroppedEventCount|Verwijderde gebeurtenissen|Aantal|Totaal|Totaal aantal verloren gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|DropReason,EventSubscriptionName|
|DeadLetteredCount|Gebeurtenissen met onbestelbare berichten|Aantal|Totaal|Totaal aantal gebeurtenissen met onbestelbare berichten die overeenkomen met dit gebeurtenis abonnement|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>Micro soft. EventGrid/systemTopics

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PublishSuccessCount|Gepubliceerde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat naar dit onderwerp is gepubliceerd|Geen|
|PublishFailCount|Mislukte gebeurtenissen publiceren|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet naar dit onderwerp kan worden gepubliceerd|Error type, fout|
|UnmatchedEventCount|Niet-overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet overeenkomt met een van de gebeurtenis abonnementen voor dit onderwerp|Geen|
|PublishSuccessLatencyInMs|Latentie van publicatie geslaagd|Milliseconden|Totaal|Latentie van geslaagde publicatie in milliseconden|Geen|
|MatchedEventCount|Overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|EventSubscriptionName|
|DeliveryAttemptFailCount|Mislukte leverings gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet aan dit gebeurtenis abonnement kan worden geleverd|Fout, error type, EventSubscriptionName|
|DeliverySuccessCount|Geleverde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat aan dit gebeurtenis abonnement is geleverd|EventSubscriptionName|
|DestinationProcessingDurationInMs|Doel verwerkings duur|Milliseconden|Average|Doel verwerkings duur in milliseconden|EventSubscriptionName|
|DroppedEventCount|Verwijderde gebeurtenissen|Aantal|Totaal|Totaal aantal verloren gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|DropReason,EventSubscriptionName|
|DeadLetteredCount|Gebeurtenissen met onbestelbare berichten|Aantal|Totaal|Totaal aantal gebeurtenissen met onbestelbare berichten die overeenkomen met dit gebeurtenis abonnement|DeadLetterReason,EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Micro soft. EventGrid/eventSubscriptions

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|MatchedEventCount|Overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|Geen|
|DeliveryAttemptFailCount|Mislukte leverings gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet aan dit gebeurtenis abonnement kan worden geleverd|Fout, error type|
|DeliverySuccessCount|Geleverde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat aan dit gebeurtenis abonnement is geleverd|Geen|
|DestinationProcessingDurationInMs|Doel verwerkings duur|Milliseconden|Average|Doel verwerkings duur in milliseconden|Geen|
|DroppedEventCount|Verwijderde gebeurtenissen|Aantal|Totaal|Totaal aantal verloren gebeurtenissen dat overeenkomt met dit gebeurtenis abonnement|DropReason|
|DeadLetteredCount|Gebeurtenissen met onbestelbare berichten|Aantal|Totaal|Totaal aantal gebeurtenissen met onbestelbare berichten die overeenkomen met dit gebeurtenis abonnement|DeadLetterReason|

## <a name="microsofteventgridextensiontopics"></a>Micro soft. EventGrid/extensionTopics

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PublishSuccessCount|Gepubliceerde gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat naar dit onderwerp is gepubliceerd|Geen|
|PublishFailCount|Mislukte gebeurtenissen publiceren|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet naar dit onderwerp kan worden gepubliceerd|Error type, fout|
|UnmatchedEventCount|Niet-overeenkomende gebeurtenissen|Aantal|Totaal|Totaal aantal gebeurtenissen dat niet overeenkomt met een van de gebeurtenis abonnementen voor dit onderwerp|Geen|
|PublishSuccessLatencyInMs|Latentie van publicatie geslaagd|Milliseconden|Totaal|Latentie van geslaagde publicatie in milliseconden|Geen|




## <a name="microsofteventhubnamespaces"></a>Micro soft. EventHub/naam ruimten

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SuccessfulRequests|Geslaagde aanvragen|Aantal|Totaal|Geslaagde aanvragen voor micro soft. EventHub.|EntityName, kan operationresult niet|
|ServerErrors|Server fouten.|Aantal|Totaal|Server fouten voor micro soft. EventHub.|EntityName, kan operationresult niet|
|UserErrors|Gebruikers fouten.|Aantal|Totaal|Gebruikers fouten voor micro soft. EventHub.|EntityName, kan operationresult niet|
|QuotaExceededErrors|Quota overschreden fouten.|Aantal|Totaal|Quotum overschrijdt fouten voor micro soft. EventHub.|EntityName, kan operationresult niet|
|ThrottledRequests|Vertraagde aanvragen.|Aantal|Totaal|Beperkte aanvragen voor micro soft. EventHub.|EntityName, kan operationresult niet|
|IncomingRequests|Binnenkomende aanvragen|Aantal|Totaal|Binnenkomende aanvragen voor micro soft. EventHub.|EntityName|
|IncomingMessages|Inkomende berichten|Aantal|Totaal|Binnenkomende berichten voor micro soft. EventHub.|EntityName|
|OutgoingMessages|Uitgaande berichten|Aantal|Totaal|Uitgaande berichten voor micro soft. EventHub.|EntityName|
|IncomingBytes|Binnenkomende bytes.|Bytes|Totaal|Binnenkomende bytes voor micro soft. EventHub.|EntityName|
|OutgoingBytes|Uitgaande bytes.|Bytes|Totaal|Uitgaande bytes voor micro soft. EventHub.|EntityName|
|ActiveConnections|ActiveConnections|Aantal|Average|Totaal aantal actieve verbindingen voor micro soft. EventHub.|Geen|
|ConnectionsOpened|Geopende verbindingen.|Aantal|Average|Geopende verbindingen voor micro soft. EventHub.|EntityName|
|ConnectionsClosed|Verbindingen gesloten.|Aantal|Average|Gesloten verbindingen voor micro soft. EventHub.|EntityName|
|CaptureBacklog|Achterstand vastleggen.|Aantal|Totaal|Achterstand vastleggen voor micro soft. EventHub.|EntityName|
|CapturedMessages|Vastgelegde berichten.|Aantal|Totaal|Vastgelegde berichten voor micro soft. EventHub.|EntityName|
|CapturedBytes|Vastgelegde bytes.|Bytes|Totaal|Vastgelegde bytes voor micro soft. EventHub.|EntityName|
|Grootte|Grootte|Bytes|Average|Grootte van een EventHub in bytes.|EntityName|
|INREQS|Binnenkomende aanvragen (afgeschaft)|Aantal|Totaal|Totaal aantal binnenkomende verzend aanvragen voor een naam ruimte (afgeschaft)|Geen|
|SUCCREQ|Geslaagde aanvragen (afgeschaft)|Aantal|Totaal|Totaal aantal geslaagde aanvragen voor een naam ruimte (afgeschaft)|Geen|
|FAILREQ|Mislukte aanvragen (afgeschaft)|Aantal|Totaal|Totaal aantal mislukte aanvragen voor een naam ruimte (afgeschaft)|Geen|
|SVRBSY|Fouten bij server bezet (afgeschaft)|Aantal|Totaal|Totaal aantal fouten bij server bezet voor een naam ruimte (afgeschaft)|Geen|
|INTERer|Interne server fouten (afgeschaft)|Aantal|Totaal|Totaal aantal interne server fouten voor een naam ruimte (afgeschaft)|Geen|
|MISCERR|Andere fouten (afgeschaft)|Aantal|Totaal|Totaal aantal mislukte aanvragen voor een naam ruimte (afgeschaft)|Geen|
|INMSGS|Binnenkomende berichten (verouderd) (afgeschaft)|Aantal|Totaal|Totaal aantal inkomende berichten voor een naam ruimte. Deze metriek is afgeschaft. Gebruik in plaats daarvan de metriek van binnenkomende berichten (afgeschaft)|Geen|
|EHINMSGS|Inkomende berichten (afgeschaft)|Aantal|Totaal|Totaal aantal inkomende berichten voor een naam ruimte (afgeschaft)|Geen|
|OUTMSGS|Uitgaande berichten (verouderd) (afgeschaft)|Aantal|Totaal|Totaal aantal uitgaande berichten voor een naam ruimte. Deze metriek is afgeschaft. Gebruik in plaats daarvan de metrische gegevens van uitgaande berichten (afgeschaft)|Geen|
|EHOUTMSGS|Uitgaande berichten (afgeschaft)|Aantal|Totaal|Totaal aantal uitgaande berichten voor een naam ruimte (afgeschaft)|Geen|
|EHINMBS|Binnenkomende bytes (verouderd) (afgeschaft)|Bytes|Totaal|Door Voer van binnenkomende berichten van Event hub voor een naam ruimte. Deze metriek is afgeschaft. Gebruik in plaats daarvan de metrische gegevens over binnenkomende bytes (afgeschaft)|Geen|
|EHINBYTES|Binnenkomende bytes (afgeschaft)|Bytes|Totaal|Door Voer van inkomende berichten van Event hub voor een naam ruimte (afgeschaft)|Geen|
|EHOUTMBS|Uitgaande bytes (verouderd) (afgeschaft)|Bytes|Totaal|Door Voer van Event hub voor een naam ruimte. Deze metriek is afgeschaft. Gebruik in plaats hiervan de metrische gegevens van uitgaande bytes (afgeschaft)|Geen|
|EHOUTBYTES|Uitgaande bytes (afgeschaft)|Bytes|Totaal|Door Voer van Event hub voor een naam ruimte (afgeschaft)|Geen|
|EHABL|Achterstallige berichten archiveren (afgeschaft)|Aantal|Totaal|Event hub-archief berichten in achterstand voor een naam ruimte (afgeschaft)|Geen|
|EHAMSGS|Berichten archiveren (afgeschaft)|Aantal|Totaal|Event hub heeft berichten gearchiveerd in een naam ruimte (afgeschaft)|Geen|
|EHAMBS|Berichten doorvoer archiveren (afgeschaft)|Bytes|Totaal|Door Voer van Event hub-bericht doorvoer in een naam ruimte (afgeschaft)|Geen|

## <a name="microsofteventhubclusters"></a>Micro soft. EventHub/clusters

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SuccessfulRequests|Geslaagde aanvragen|Aantal|Totaal|Geslaagde aanvragen voor micro soft. EventHub.|Kan operationresult niet|
|ServerErrors|Server fouten.|Aantal|Totaal|Server fouten voor micro soft. EventHub.|Kan operationresult niet|
|UserErrors|Gebruikers fouten.|Aantal|Totaal|Gebruikers fouten voor micro soft. EventHub.|Kan operationresult niet|
|QuotaExceededErrors|Quota overschreden fouten.|Aantal|Totaal|Quotum overschrijdt fouten voor micro soft. EventHub.|Kan operationresult niet|
|ThrottledRequests|Vertraagde aanvragen.|Aantal|Totaal|Beperkte aanvragen voor micro soft. EventHub.|Kan operationresult niet|
|IncomingRequests|Binnenkomende aanvragen|Aantal|Totaal|Binnenkomende aanvragen voor micro soft. EventHub.|Geen|
|IncomingMessages|Inkomende berichten|Aantal|Totaal|Binnenkomende berichten voor micro soft. EventHub.|Geen|
|OutgoingMessages|Uitgaande berichten|Aantal|Totaal|Uitgaande berichten voor micro soft. EventHub.|Geen|
|IncomingBytes|Binnenkomende bytes.|Bytes|Totaal|Binnenkomende bytes voor micro soft. EventHub.|Geen|
|OutgoingBytes|Uitgaande bytes.|Bytes|Totaal|Uitgaande bytes voor micro soft. EventHub.|Geen|
|ActiveConnections|ActiveConnections|Aantal|Average|Totaal aantal actieve verbindingen voor micro soft. EventHub.|Geen|
|ConnectionsOpened|Geopende verbindingen.|Aantal|Average|Geopende verbindingen voor micro soft. EventHub.|Geen|
|ConnectionsClosed|Verbindingen gesloten.|Aantal|Average|Gesloten verbindingen voor micro soft. EventHub.|Geen|
|CaptureBacklog|Achterstand vastleggen.|Aantal|Totaal|Achterstand vastleggen voor micro soft. EventHub.|Geen|
|CapturedMessages|Vastgelegde berichten.|Aantal|Totaal|Vastgelegde berichten voor micro soft. EventHub.|Geen|
|CapturedBytes|Vastgelegde bytes.|Bytes|Totaal|Vastgelegde bytes voor micro soft. EventHub.|Geen|
|CPU|CPU|Percentage|Maximum|CPU-gebruik voor het event hub-cluster als een percentage|Rol|
|AvailableMemory|Beschikbaar geheugen|Percentage|Maximum|Beschikbaar geheugen voor het event hub-cluster als percentage van het totale geheugen.|Rol|
|Grootte|Grootte van een EventHub in bytes.|Bytes|Average|Grootte van een EventHub in bytes.|Rol|


## <a name="microsofthdinsightclusters"></a>Micro soft. HDInsight/clusters

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|GatewayRequests|Gateway aanvragen|Aantal|Totaal|Aantal gateway-aanvragen|Http status|
|CategorizedGatewayRequests|Gecategoriseerde gateway aanvragen|Aantal|Totaal|Aantal gateway aanvragen per categorie (1xx/2xx/3xx/4xx/5xx)|Http status|
|NumActiveWorkers|Aantal actieve werk rollen|Aantal|Maximum|Aantal actieve werk rollen|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Micro soft. Insights/AutoscaleSettings

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ObservedMetricValue|Waargenomen metrische waarde|Aantal|Average|De waarde die wordt berekend door automatisch schalen wanneer deze wordt uitgevoerd|MetricTriggerSource|
|MetricThreshold|Drempel waarde voor metrische gegevens|Aantal|Average|De geconfigureerde drempel waarde voor automatisch schalen wanneer automatisch schalen is uitgevoerd.|MetricTriggerRule|
|ObservedCapacity|Waargenomen capaciteit|Aantal|Average|De capaciteit die is gerapporteerd voor automatisch schalen wanneer deze wordt uitgevoerd.|Geen|
|ScaleActionsInitiated|Schaal acties gestart|Aantal|Totaal|De richting van de schaal bewerking.|ScaleDirection|

## <a name="microsoftinsightscomponents"></a>Micro soft. Insights/onderdelen

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Beschikbaarheid|Percentage|Average|Percentage voltooide beschikbaarheids tests|availabilityResult/naam, availabilityResult/locatie|
|availabilityResults/aantal|Beschikbaarheidstests|Aantal|Aantal|Aantal beschikbaarheids tests|availabilityResult/naam, availabilityResult/locatie, availabilityResult/geslaagd|
|availabilityResults/duur|Duur beschikbaarheids test|Milliseconden|Average|Duur beschikbaarheids test|availabilityResult/naam, availabilityResult/locatie, availabilityResult/geslaagd|
|browserTimings/networkDuration|Netwerk verbindings tijd voor laden van pagina|Milliseconden|Average|Tijd tussen de gebruikers aanvraag en de netwerk verbinding. Inclusief DNS-Zoek-en transport verbinding.|Geen|
|browserTimings/processingDuration|Verwerkings tijd van client|Milliseconden|Average|Tijd tussen het ontvangen van de laatste byte van een document totdat de DOM is geladen. Asynchrone aanvragen kunnen nog steeds worden verwerkt.|Geen|
|browserTimings/receiveDuration|Reactie tijd van ontvangst|Milliseconden|Average|Tijd tussen de eerste en laatste bytes, of tot de verbinding wordt verbroken.|Geen|
|browserTimings/sendDuration|Aanvraag tijd verzenden|Milliseconden|Average|Tijd tussen netwerk verbinding en ontvangst van de eerste byte.|Geen|
|browserTimings/totalDuration|Laad tijd van browser pagina|Milliseconden|Average|Tijd van de gebruikers aanvraag totdat DOM, opmaak modellen, scripts en installatie kopieën worden geladen.|Geen|
|afhankelijkheden/aantal|Afhankelijkheids aanroepen|Aantal|Aantal|Aantal aanroepen van de toepassing naar externe bronnen.|afhankelijkheid/type, afhankelijkheid/requests, afhankelijkheid/geslaagd, afhankelijkheid/doel, afhankelijkheid/resultCode, bewerking/synthetisch, Cloud/roleInstance, Cloud/rolnaam|
|afhankelijkheden/duur|Duur van afhankelijkheid|Milliseconden|Average|Duur van de aanroepen van de toepassing naar externe bronnen.|afhankelijkheid/type, afhankelijkheid/requests, afhankelijkheid/geslaagd, afhankelijkheid/doel, afhankelijkheid/resultCode, bewerking/synthetisch, Cloud/roleInstance, Cloud/rolnaam|
|afhankelijkheden/mislukt|Mislukte afhankelijkheids aanroepen|Aantal|Aantal|Aantal mislukte afhankelijkheids aanroepen van de toepassing naar externe bronnen.|afhankelijkheid/type, afhankelijkheid/requests, afhankelijkheid/geslaagd, afhankelijkheid/doel, afhankelijkheid/resultCode, bewerking/synthetisch, Cloud/roleInstance, Cloud/rolnaam|
|Page views/aantal|Pagina weergaven|Aantal|Aantal|Aantal pagina weergaven.|bewerking/synthetisch, Cloud/rolnaam|
|Page views/duur|Laad tijd pagina weergave|Milliseconden|Average|Laad tijd pagina weergave|bewerking/synthetisch, Cloud/rolnaam|
|Performance Counters/requestExecutionTime|Uitvoerings tijd van de HTTP-aanvraag|Milliseconden|Average|Uitvoerings tijd van de meest recente aanvraag.|Cloud-roleInstance|
|Performance Counters/requestsInQueue|HTTP-aanvragen in de toepassings wachtrij|Aantal|Average|Lengte van de wachtrij voor toepassings aanvragen.|Cloud-roleInstance|
|Performance Counters/requestsPerSecond|Frequentie van HTTP-aanvragen|CountPerSecond|Average|Het aantal aanvragen voor de toepassing per seconde van ASP.NET.|Cloud-roleInstance|
|Performance Counters/exceptionsPerSecond|Uitzonderings frequentie|CountPerSecond|Average|Aantal verwerkte en onverwerkte uitzonde ringen die worden gerapporteerd aan Windows, inclusief .NET-uitzonde ringen en onbeheerde uitzonde ringen die worden geconverteerd naar .NET-uitzonde ringen.|Cloud-roleInstance|
|Performance Counters/processIOBytesPerSecond|I/o-frequentie van processen|BytesPerSecond|Average|Totaal aantal in bestanden, netwerk en apparaten gelezen en geschreven bytes per seconde.|Cloud-roleInstance|
|Performance Counters/processCpuPercentage|CPU verwerken|Percentage|Average|Het percentage van de verstreken tijd dat alle proces threads de processor hebben gebruikt om instructies uit te voeren. Dit kan variëren tussen 0 en 100. Deze metrische gegevens duiden de prestaties van alleen het W3wp-proces aan.|Cloud-roleInstance|
|Performance Counters/processorCpuPercentage|Processor tijd|Percentage|Average|Het percentage tijd dat de processor spendeert aan niet-inactieve threads.|Cloud-roleInstance|
|Performance Counters/memoryAvailableBytes|Beschikbaar geheugen|Bytes|Average|Fysiek geheugen dat direct beschikbaar is voor toewijzing aan een proces of voor systeem gebruik.|Cloud-roleInstance|
|Performance Counters/processPrivateBytes|Privé-bytes verwerken|Bytes|Average|Geheugen dat exclusief wordt toegewezen aan de processen van de bewaakte toepassing.|Cloud-roleInstance|
|aanvragen/duur|Server reactietijd|Milliseconden|Average|Tijd tussen het ontvangen van een HTTP-aanvraag en het volt ooien van het verzenden van het antwoord.|aanvraag-requests, aanvraag-resultCode, bewerking/synthetisch, Cloud/roleInstance, aanvraag/geslaagd, Cloud/rolnaam|
|aanvragen/aantal|Server aanvragen|Aantal|Aantal|Aantal voltooide HTTP-aanvragen.|aanvraag-requests, aanvraag-resultCode, bewerking/synthetisch, Cloud/roleInstance, aanvraag/geslaagd, Cloud/rolnaam|
|aanvragen/mislukt|Mislukte aanvragen|Aantal|Aantal|Het aantal HTTP-aanvragen dat is gemarkeerd als mislukt. In de meeste gevallen zijn dit aanvragen met een antwoord code >= 400 en niet gelijk is aan 401.|aanvraag-requests, aanvraag-resultCode, aanvraag/geslaagd, bewerking/synthetisch, Cloud/roleInstance, Cloud/rolnaam|
|aanvragen/frequentie|Aantal server aanvragen|CountPerSecond|Average|Aantal server aanvragen per seconde|aanvraag-requests, aanvraag-resultCode, bewerking/synthetisch, Cloud/roleInstance, aanvraag/geslaagd, Cloud/rolnaam|
|uitzonde ringen/aantal|Uitzonderingen|Aantal|Aantal|Totaal aantal niet-onderschepte uitzonde ringen.|Cloud/rolnaam, Cloud/roleInstance, client/type|
|uitzonde ringen/browser|Browseruitzonderingen|Aantal|Aantal|Aantal niet-onderschepte uitzonde ringen dat in de browser wordt gegenereerd.|client-isServer, Cloud/rolnaam|
|uitzonde ringen/server|Server uitzonderingen|Aantal|Aantal|Aantal niet-onderschepte uitzonde ringen dat is opgetreden in de server toepassing.|client-isServer, Cloud/rolnaam, Cloud/roleInstance|
|traceringen/aantal|Traceringen|Aantal|Aantal|Aantal tracerings documenten|Trace/severityLevel, Operation/synthetisch, Cloud/rolnaam, Cloud/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Micro soft. IoTCentral/IoTApps

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|connectedDeviceCount|Totaal aantal verbonden apparaten|Aantal|Average|Aantal apparaten dat is verbonden met IoT Central|Geen|
|C2D. Property. Read. geslaagd|Geslaagde apparaat-eigenschap leest van IoT Central|Aantal|Totaal|Het aantal geslaagde Lees bewerkingen van eigenschappen dat is gestart vanuit IoT Central|Geen|
|C2D. Property. Read. failure|Mislukte apparaat-eigenschap lezen van IoT Central|Aantal|Totaal|Het aantal mislukte eigenschaps Lees bewerkingen dat is gestart vanuit IoT Central|Geen|
|D2C. Property. Read. geslaagd|Geslaagde apparaat-eigenschap is van apparaten gelezen|Aantal|Totaal|Het aantal geslaagde, lees bewerkingen van apparaten|Geen|
|D2C. Property. Read. failure|Mislukte apparaat-eigenschap lezen van apparaten|Aantal|Totaal|Het aantal mislukte lees bewerkingen van een eigenschap die vanaf apparaten worden gestart|Geen|
|C2D. Property. update. geslaagd|Geslaagde updates van de apparaat-eigenschap van IoT Central|Aantal|Totaal|Het aantal geslaagde updates van alle eigenschappen dat vanaf IoT Central is gestart|Geen|
|C2D. Property. update. failure|Mislukte updates van Apparaatinstellingen van IoT Central|Aantal|Totaal|Het aantal mislukte updates van eigenschappen dat vanuit IoT Central is gestart|Geen|
|D2C. Property. update. geslaagd|Geslaagde updates van apparaat-eigenschappen van apparaten|Aantal|Totaal|Het aantal geslaagde updates van alle eigenschappen dat is gestart vanaf apparaten|Geen|
|D2C. Property. update. failure|Mislukte updates van Apparaatinstellingen van apparaten|Aantal|Totaal|Het aantal mislukte updates van eigenschappen dat is geïnitieerd vanaf apparaten|Geen|


## <a name="microsoftkeyvaultvaults"></a>Micro soft.-sleutel kluis/-kluizen

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ServiceApiHit|Totaal aantal treffers in de service-API|Aantal|Aantal|Totaal aantal treffers in de service-API|Activity type, Activiteitsnummer|
|ServiceApiLatency|Algehele latentie van Service-API|Milliseconden|Average|Algemene latentie van Service-API-aanvragen|Activity type, Activiteitsnummer, status code, StatusCodeClass|
|ServiceApiResult|Totale resultaten van Service-API|Aantal|Aantal|Totaal aantal resultaten van Service-API|Activity type, Activiteitsnummer, status code, StatusCodeClass|
|SaturationShoebox|Algehele intensiteit van de kluis|Percentage|Average|Gebruikte kluis capaciteit|Activity type, Activiteitsnummer, TransactionType|
|Beschikbaarheid|Algemene Beschik baarheid van kluis|Percentage|Average|Beschik baarheid van kluis aanvragen|Activity type, Activiteitsnummer, status code, StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Micro soft. Kusto/clusters

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CacheUtilization|Cache gebruik|Percentage|Average|Gebruiks niveau in het cluster bereik|Geen|
|QueryDuration|Query duur|Milliseconden|Average|De duur van query's in seconden|QueryStatus|
|IngestionUtilization|Opname gebruik|Percentage|Average|Verhouding van gebruikte opname sleuven in het cluster|Geen|
|KeepAlive|Actief houden|Aantal|Average|Sanity-controle geeft aan dat het cluster reageert op query's|Geen|
|IngestionVolumeInMB|Opname volume (in MB)|Aantal|Totaal|Totaal volume van opgenomen gegevens aan het cluster (in MB)|Database|
|IngestionLatencyInSeconds|Opname latentie (in seconden)|Seconden|Average|Opname tijd van de bron (bijvoorbeeld bericht is in EventHub) naar het cluster in enkele seconden|Geen|
|EventsProcessedForEventHubs|Verwerkte gebeurtenissen (voor gebeurtenis/IoT-hubs)|Aantal|Totaal|Aantal gebeurtenissen dat door het cluster wordt verwerkt bij het opnemen van gebeurtenis/IoT Hub|EventStatus|
|IngestionResult|Opname resultaat|Aantal|Aantal|Aantal opname bewerkingen|IngestionResultDetails|
|CPU|CPU|Percentage|Average|Niveau CPU-gebruik|Geen|
|ContinuousExportNumOfRecordsExported|Doorlopend exporteren: aantal geëxporteerde records|Aantal|Totaal|Het aantal geëxporteerde records dat wordt geactiveerd voor elk opslag artefact dat is geschreven tijdens de export bewerking|ContinuousExportName, data base|
|ExportUtilization|Gebruik exporteren|Percentage|Maximum|Gebruik exporteren|Geen|
|ContinuousExportPendingCount|Aantal doorlopend exporteren in behandeling|Aantal|Maximum|Het aantal in behandeling zijnde doorlopende export taken dat gereed is voor uitvoering|Geen|
|ContinuousExportMaxLatenessMinutes|Maximale achterstand voor continue export|Aantal|Maximum|De achterstand (in minuten) die is gerapporteerd door de doorlopende export taken in het cluster|Geen|
|ContinuousExportResult|Resultaat doorlopend exporteren|Aantal|Aantal|Hiermee wordt aangegeven of continue export is geslaagd of mislukt|ContinuousExportName, resultaat, data base|
|StreamingIngestDuration|Opname duur van streaming|Milliseconden|Average|De duur van het opnemen van gegevens stromen in milliseconden|Geen|
|StreamingIngestDataRate|Gegevens frequentie van streaming opname|Aantal|Average|Gegevens frequentie van streaming-opname (MB per seconde)|Geen|
|SteamingIngestRequestRate|Aanvraag frequentie voor streaming-opname|Aantal|RateRequestsPerSecond|Aanvraag frequentie voor streaming-opname (aanvragen per seconde)|Geen|
|StreamingIngestResults|Resultaat van streaming-opname|Aantal|Average|Resultaat van streaming-opname|Resultaat|
|TotalNumberOfConcurrentQueries|Totaal aantal gelijktijdige query's|Aantal|Totaal|Totaal aantal gelijktijdige query's|Geen|
|TotalNumberOfThrottledQueries|Totaal aantal vertraagde query's|Aantal|Totaal|Totaal aantal vertraagde query's|Geen|
|TotalNumberOfThrottledCommands|Totaal aantal vertraagde opdrachten|Aantal|Totaal|Totaal aantal vertraagde opdrachten|CommandType|
|TotalNumberOfExtents|Totaal aantal gebieden|Aantal|Totaal|Totaal aantal gegevens gebieden|Geen|
|InstanceCount|Aantal instanties|Aantal|Average|Totaal aantal instanties|Geen|


## <a name="microsoftlogicworkflows"></a>Micro soft. Logic/werk stromen

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|RunsStarted|Uitvoeringen gestart|Aantal|Totaal|Aantal uitvoeringen van werk stroom gestart.|Geen|
|RunsCompleted|Uitvoeringen voltooid|Aantal|Totaal|Aantal voltooide werk stroom uitvoeringen.|Geen|
|RunsSucceeded|Geslaagde uitvoeringen|Aantal|Totaal|Aantal geslaagde werk stroom uitvoeringen.|Geen|
|RunsFailed|Uitvoeringen mislukt|Aantal|Totaal|Het aantal uitvoeringen van de werk stroom is mislukt.|Geen|
|RunsCancelled|Geannuleerde uitvoeringen|Aantal|Totaal|Aantal uitgevoerde werk stroom uitvoeringen geannuleerd.|Geen|
|RunLatency|Uitvoerings latentie|Seconden|Average|Latentie van voltooide werk stroom uitvoeringen.|Geen|
|RunSuccessLatency|Latentie van geslaagde uitvoering|Seconden|Average|Latentie van geslaagde werk stroom uitvoeringen.|Geen|
|RunThrottledEvents|Vertraagde gebeurtenissen uitvoeren|Aantal|Totaal|Aantal werk stroom acties of trigger vertraagde gebeurtenissen.|Geen|
|RunStartThrottledEvents|Vertraagde gebeurtenissen uitvoeren|Aantal|Totaal|Aantal uitgevoerde vertraagde gebeurtenissen voor de werk stroom.|Geen|
|RunFailurePercentage|Percentage mislukte uitvoeringen|Percentage|Totaal|Percentage mislukte werk stroom uitvoeringen.|Geen|
|ActionsStarted|Gestarte acties |Aantal|Totaal|Aantal gestarte werk stroom acties.|Geen|
|ActionsCompleted|Acties voltooid |Aantal|Totaal|Aantal voltooide werk stroom acties.|Geen|
|ActionsSucceeded|Acties geslaagd |Aantal|Totaal|Aantal geslaagde werk stroom acties.|Geen|
|ActionsFailed|Mislukte acties |Aantal|Totaal|Aantal mislukte werk stroom acties.|Geen|
|ActionsSkipped|Overgeslagen acties |Aantal|Totaal|Aantal overgeslagen werk stroom acties.|Geen|
|ActionLatency|Actie latentie |Seconden|Average|Latentie van voltooide werk stroom acties.|Geen|
|ActionSuccessLatency|Latentie geslaagde acties |Seconden|Average|Latentie van geslaagde werk stroom acties.|Geen|
|ActionThrottledEvents|Door actie vertraagde gebeurtenissen|Aantal|Totaal|Aantal door werk stroom actie vertraagde gebeurtenissen..|Geen|
|TriggersStarted|Triggers gestart |Aantal|Totaal|Aantal gestarte werk stroom triggers.|Geen|
|TriggersCompleted|Triggers voltooid |Aantal|Totaal|Aantal voltooide werk stroom triggers.|Geen|
|TriggersSucceeded|Geslaagde triggers |Aantal|Totaal|Aantal geslaagde werk stroom triggers.|Geen|
|TriggersFailed|Mislukte triggers |Aantal|Totaal|Aantal mislukte werk stroom triggers.|Geen|
|TriggersSkipped|Triggers overgeslagen|Aantal|Totaal|Aantal overgeslagen werk stroom triggers.|Geen|
|TriggersFired|Geactiveerde triggers |Aantal|Totaal|Aantal geactiveerde werk stroom triggers.|Geen|
|TriggerLatency|Latentie van trigger |Seconden|Average|Latentie van voltooide werk stroom triggers.|Geen|
|TriggerFireLatency|Brand latentie activeren |Seconden|Average|Latentie van geactiveerde werk stroom triggers.|Geen|
|TriggerSuccessLatency|Latentie van trigger geslaagd |Seconden|Average|Latentie van geslaagde werk stroom triggers.|Geen|
|TriggerThrottledEvents|Trigger beperkings gebeurtenissen|Aantal|Totaal|Aantal door werk stroom trigger vertraagde gebeurtenissen.|Geen|
|BillableActionExecutions|Factureer bare actie-uitvoeringen|Aantal|Totaal|Aantal uitvoeringen van werk stroom acties dat wordt gefactureerd.|Geen|
|BillableTriggerExecutions|Factureer bare trigger uitvoeringen|Aantal|Totaal|Aantal uitvoeringen van werk stroom trigger dat wordt gefactureerd.|Geen|
|TotalBillableExecutions|Totaal aantal factureer bare uitvoeringen|Aantal|Totaal|Aantal uitgevoerde werk stroom uitvoeringen dat wordt gefactureerd.|Geen|
|BillingUsageNativeOperation|Facturerings gebruik voor uitvoering van systeem eigen bewerkingen|Aantal|Totaal|Aantal uitvoeringen van de native bewerking dat wordt gefactureerd.|Geen|
|BillingUsageStandardConnector|Facturerings gebruik voor het uitvoeren van standaard-connectors|Aantal|Totaal|Aantal uitvoeringen van Standard-connector dat wordt gefactureerd.|Geen|
|BillingUsageStorageConsumption|Facturerings gebruik voor uitvoeringen van opslag verbruik|Aantal|Totaal|Aantal uitvoeringen van opslag verbruik dat wordt gefactureerd.|Geen|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Micro soft. Logic/integrationServiceEnvironments

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|RunsStarted|Uitvoeringen gestart|Aantal|Totaal|Aantal uitvoeringen van werk stroom gestart.|Geen|
|RunsCompleted|Uitvoeringen voltooid|Aantal|Totaal|Aantal voltooide werk stroom uitvoeringen.|Geen|
|RunsSucceeded|Geslaagde uitvoeringen|Aantal|Totaal|Aantal geslaagde werk stroom uitvoeringen.|Geen|
|RunsFailed|Uitvoeringen mislukt|Aantal|Totaal|Het aantal uitvoeringen van de werk stroom is mislukt.|Geen|
|RunsCancelled|Geannuleerde uitvoeringen|Aantal|Totaal|Aantal uitgevoerde werk stroom uitvoeringen geannuleerd.|Geen|
|RunLatency|Uitvoerings latentie|Seconden|Average|Latentie van voltooide werk stroom uitvoeringen.|Geen|
|RunSuccessLatency|Latentie van geslaagde uitvoering|Seconden|Average|Latentie van geslaagde werk stroom uitvoeringen.|Geen|
|RunThrottledEvents|Vertraagde gebeurtenissen uitvoeren|Aantal|Totaal|Aantal werk stroom acties of trigger vertraagde gebeurtenissen.|Geen|
|RunStartThrottledEvents|Vertraagde gebeurtenissen uitvoeren|Aantal|Totaal|Aantal uitgevoerde vertraagde gebeurtenissen voor de werk stroom.|Geen|
|RunFailurePercentage|Percentage mislukte uitvoeringen|Percentage|Totaal|Percentage mislukte werk stroom uitvoeringen.|Geen|
|ActionsStarted|Gestarte acties |Aantal|Totaal|Aantal gestarte werk stroom acties.|Geen|
|ActionsCompleted|Acties voltooid |Aantal|Totaal|Aantal voltooide werk stroom acties.|Geen|
|ActionsSucceeded|Acties geslaagd |Aantal|Totaal|Aantal geslaagde werk stroom acties.|Geen|
|ActionsFailed|Mislukte acties |Aantal|Totaal|Aantal mislukte werk stroom acties.|Geen|
|ActionsSkipped|Overgeslagen acties |Aantal|Totaal|Aantal overgeslagen werk stroom acties.|Geen|
|ActionLatency|Actie latentie |Seconden|Average|Latentie van voltooide werk stroom acties.|Geen|
|ActionSuccessLatency|Latentie geslaagde acties |Seconden|Average|Latentie van geslaagde werk stroom acties.|Geen|
|ActionThrottledEvents|Door actie vertraagde gebeurtenissen|Aantal|Totaal|Aantal door werk stroom actie vertraagde gebeurtenissen..|Geen|
|TriggersStarted|Triggers gestart |Aantal|Totaal|Aantal gestarte werk stroom triggers.|Geen|
|TriggersCompleted|Triggers voltooid |Aantal|Totaal|Aantal voltooide werk stroom triggers.|Geen|
|TriggersSucceeded|Geslaagde triggers |Aantal|Totaal|Aantal geslaagde werk stroom triggers.|Geen|
|TriggersFailed|Mislukte triggers |Aantal|Totaal|Aantal mislukte werk stroom triggers.|Geen|
|TriggersSkipped|Triggers overgeslagen|Aantal|Totaal|Aantal overgeslagen werk stroom triggers.|Geen|
|TriggersFired|Geactiveerde triggers |Aantal|Totaal|Aantal geactiveerde werk stroom triggers.|Geen|
|TriggerLatency|Latentie van trigger |Seconden|Average|Latentie van voltooide werk stroom triggers.|Geen|
|TriggerFireLatency|Brand latentie activeren |Seconden|Average|Latentie van geactiveerde werk stroom triggers.|Geen|
|TriggerSuccessLatency|Latentie van trigger geslaagd |Seconden|Average|Latentie van geslaagde werk stroom triggers.|Geen|
|TriggerThrottledEvents|Trigger beperkings gebeurtenissen|Aantal|Totaal|Aantal door werk stroom trigger vertraagde gebeurtenissen.|Geen|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Gebruik van werk stroom processor voor Integratieserviceomgeving|Percentage|Average|Gebruik van werk stroom processoren voor de integratie service omgeving.|Geen|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Geheugen gebruik van werk stroom voor Integratieserviceomgeving|Percentage|Average|Geheugen gebruik van werk stroom voor de integratie service omgeving.|Geen|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Processor gebruik van connector voor Integratieserviceomgeving|Percentage|Average|Het processor gebruik van de connector voor de integratie service omgeving.|Geen|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Geheugen gebruik van connector voor Integratieserviceomgeving|Percentage|Average|Geheugen gebruik van connector voor de integratie service omgeving.|Geen|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Micro soft. MachineLearningServices/werk ruimten

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Geannuleerde uitvoeringen|Geannuleerde uitvoeringen|Aantal|Totaal|Aantal uitvoeringen geannuleerd voor deze werk ruimte|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Geannuleerde uitvoeringen annuleren|Geannuleerde uitvoeringen annuleren|Aantal|Totaal|Aantal uitvoeringen waarvoor annuleren is aangevraagd voor deze werk ruimte|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Voltooide uitvoeringen|Voltooide uitvoeringen|Aantal|Totaal|Het aantal uitvoeringen dat is voltooid voor deze werk ruimte|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Mislukte uitvoeringen|Mislukte uitvoeringen|Aantal|Totaal|Aantal uitvoeringen is mislukt voor deze werk ruimte|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Uitvoeringen volt ooien|Uitvoeringen volt ooien|Aantal|Totaal|Aantal uitvoeringen van voltooiings status voor deze werk ruimte|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Reageert niet|Reageert niet|Aantal|Totaal|Aantal uitvoeringen dat niet reageert voor deze werk ruimte|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Niet gestart uitvoeringen|Niet gestart uitvoeringen|Aantal|Totaal|Aantal uitvoeringen in de status niet gestart voor deze werk ruimte|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Uitvoering voorbereiden|Uitvoering voorbereiden|Aantal|Totaal|Aantal uitvoeringen dat wordt voor bereid voor deze werk ruimte|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Uitvoeringen van inrichting|Uitvoeringen van inrichting|Aantal|Totaal|Aantal uitvoeringen dat wordt ingericht voor deze werk ruimte|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Uitvoeringen in wachtrij|Uitvoeringen in wachtrij|Aantal|Totaal|Aantal uitvoeringen dat in de wachtrij is geplaatst voor deze werk ruimte|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Gestart uitvoeringen|Gestart uitvoeringen|Aantal|Totaal|Aantal uitvoeringen gestart voor deze werk ruimte|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Uitvoeringen starten|Uitvoeringen starten|Aantal|Totaal|Aantal uitvoeringen gestart voor deze werk ruimte|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType|
|Fouten|Fouten|Aantal|Totaal|Aantal uitvoerings fouten in deze werk ruimte|Scenario|
|Waarschuwingen|Waarschuwingen|Aantal|Totaal|Aantal uitvoerings waarschuwingen in deze werk ruimte|Scenario|
|Model registratie is voltooid|Model registratie is voltooid|Aantal|Totaal|Aantal model registraties dat is geslaagd in deze werk ruimte|Scenario|
|Model register mislukt|Model register mislukt|Aantal|Totaal|Aantal model registraties dat is mislukt in deze werk ruimte|Scenario, status code|
|Modelimplementatie gestart|Modelimplementatie gestart|Aantal|Totaal|Aantal model implementaties gestart in deze werk ruimte|Scenario|
|Modelimplementatie geslaagd|Modelimplementatie geslaagd|Aantal|Totaal|Aantal model implementaties dat is geslaagd in deze werk ruimte|Scenario|
|Modelimplementatie is mislukt|Modelimplementatie is mislukt|Aantal|Totaal|Aantal model implementaties die zijn mislukt in deze werk ruimte|Scenario, status code|
|Totaal aantal knoop punten|Totaal aantal knoop punten|Aantal|Average|Totaal aantal knoop punten. Dit totaal omvat enkele actieve knoop punten, niet-actieve knoop punten, onbruikbaare knoop punten, Premepted knoop punten, waardoor knoop punten|Scenario, clustername|
|Actieve knoop punten|Actieve knoop punten|Aantal|Average|Aantal Active-knoop punten. Dit zijn de knoop punten waarop een taak actief wordt uitgevoerd.|Scenario, clustername|
|Niet-actieve knoop punten|Niet-actieve knoop punten|Aantal|Average|Aantal niet-actieve knoop punten. Niet-actieve knoop punten zijn de knoop punten waarop geen taken worden uitgevoerd, maar u kunt wel een nieuwe taak accepteren, indien beschikbaar.|Scenario, clustername|
|Niet-bruikbare knoop punten|Niet-bruikbare knoop punten|Aantal|Average|Aantal niet-bruikbare knoop punten. Niet-bruikbare knoop punten zijn niet functioneel vanwege een probleem met onherleidbare. Deze knoop punten worden door Azure gerecycled.|Scenario, clustername|
|Knoop punten die zijn afgebroken|Knoop punten die zijn afgebroken|Aantal|Average|Aantal knoop punten dat is afgebroken. Deze knoop punten zijn de knoop punten met een lage prioriteit die worden verwijderd uit de beschik bare knooppunt groep.|Scenario, clustername|
|Knoop punten verlaten|Knoop punten verlaten|Aantal|Average|Aantal knoop punten dat de poort verlaat. Als u knoop punten verlaat, worden de knoop punten die zojuist de verwerking van een taak hebben voltooid, naar de niet-actieve status verzonden.|Scenario, clustername|
|Totaal aantal kernen|Totaal aantal kernen|Aantal|Average|Aantal totale kernen|Scenario, clustername|
|Actieve kernen|Actieve kernen|Aantal|Average|Aantal actieve kernen|Scenario, clustername|
|Niet-actieve kernen|Niet-actieve kernen|Aantal|Average|Aantal niet-actieve kern geheugens|Scenario, clustername|
|Onbruikbaar aantal kern geheugens|Onbruikbaar aantal kern geheugens|Aantal|Average|Aantal niet-bruikbare kernen|Scenario, clustername|
|Afgebroken kernen|Afgebroken kernen|Aantal|Average|Aantal afgebroken kernen|Scenario, clustername|
|Kernen verlaten|Kernen verlaten|Aantal|Average|Aantal te verlaten kernen|Scenario, clustername|
|Percentage quotum gebruik|Percentage quotum gebruik|Aantal|Average|Percentage van gebruikte quota|Scenario, clustername, VmFamilyName, VmPriority|
|CpuUtilization|CpuUtilization|Aantal|Average|CPU (preview-versie)|Scenario, runId, NodeId, CreatedTime|
|GpuUtilization|GpuUtilization|Aantal|Average|GPU (preview-versie)|Scenario, runId, NodeId, CreatedTime, DeviceId|


## <a name="microsoftmapsaccounts"></a>Micro soft. Maps/accounts

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Gebruik|Gebruik|Aantal|Aantal|Aantal API-aanroepen|ApiCategory, ApiName, ResultType, ResponseCode|
|Beschikbaarheid|Beschikbaarheid|Percentage|Average|Beschik baarheid van de Api's|ApiCategory, ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Micro soft. Media/Media Services/streamingEndpoints

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgangs gegevens, in bytes.|Output|
|SuccessE2ELatency|Geslaagde end-to-end-latentie|Milliseconden|Average|De gemiddelde latentie voor voltooide aanvragen in milliseconden.|Output|
|Aanvragen|Aanvragen|Aantal|Totaal|Aanvragen voor een streaming-eind punt.|Output Format, HTTP status code, error code|


## <a name="microsoftmediamediaservices"></a>Micro soft. Media/Media Services

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AssetQuota|Activa quotum|Aantal|Average|Hoeveel assets zijn toegestaan voor het huidige media service-account|Geen|
|AssetCount|Aantal assets|Aantal|Average|Hoeveel activa er al zijn gemaakt in het huidige media service-account|Geen|
|AssetQuotaUsedPercentage|Percentage gebruikt voor het activa quotum|Percentage|Average|Verbruikt percentage in huidige media service account|Geen|
|ContentKeyPolicyQuota|Quotum voor inhouds sleutel beleid|Aantal|Average|Hoeveel beleids regels voor inhouds sleutels zijn toegestaan voor het huidige media service-account|Geen|
|ContentKeyPolicyCount|Aantal beleids regels voor inhouds sleutels|Aantal|Average|Hoeveel beleids regels voor inhouds sleutels zijn al gemaakt in het huidige media service-account|Geen|
|ContentKeyPolicyQuotaUsedPercentage|Percentage gebruikt quotum voor inhouds sleutel beleid|Percentage|Average|Gebruikt percentage van beleid voor inhouds sleutels in het huidige media service-account|Geen|
|StreamingPolicyQuota|Quota voor streaming-beleid|Aantal|Average|Hoeveel streaming-beleids regels zijn toegestaan voor het huidige media service-account|Geen|
|StreamingPolicyCount|Aantal stroomsgewijze beleids regels|Aantal|Average|Hoeveel streaming-beleids regels zijn al gemaakt in het huidige media service-account|Geen|
|StreamingPolicyQuotaUsedPercentage|Percentage gebruikt quotum voor het streaming-beleid|Percentage|Average|Gebruikt beleid voor streamingbeleid in het huidige media service-account|Geen|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Micro soft. MixedReality/remoteRenderingAccounts

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AssetsConverted|Activa geconverteerd|Aantal|Totaal|Totaal aantal activa dat is geconverteerd|AppId, ResourceId, SDKVersion|
|ActiveRenderingSessions|Actieve rendering-sessies|Aantal|Totaal|Totaal aantal actieve rendering-sessies|AppId, ResourceId, SessionType, SDKVersion|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Micro soft. NetApp/netAppAccounts/capacityPools/volumes

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AverageReadLatency|Gemiddelde lees latentie|Milliseconden|Average|Gemiddelde lees latentie in milliseconden per bewerking|Geen|
|AverageWriteLatency|Gemiddelde schrijf latentie|Milliseconden|Average|Gemiddelde schrijf latentie in milliseconden per bewerking|Geen|
|VolumeLogicalSize|Grootte van gebruikt volume|Bytes|Average|Logische grootte van het volume (gebruikte bytes)|Geen|
|VolumeSnapshotSize|Grootte van moment opname van volume|Bytes|Average|Grootte van alle moment opnamen in volume|Geen|
|ReadIops|IOPS lezen|CountPerSecond|Average|In-en uitvoer bewerkingen per seconde|Geen|
|WriteIops|IOPS schrijven|CountPerSecond|Average|In-en uitvoer bewerkingen per seconde|Geen|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Micro soft. NetApp/netAppAccounts/capacityPools

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|VolumePoolAllocatedUsed|Groep toegewezen aan volume grootte|Bytes|Average|Gebruikte grootte van de pool is toegewezen|Geen|
|VolumePoolTotalLogicalSize|Verbruikte grootte van pool|Bytes|Average|Som van de logische grootte van alle volumes die deel uitmaken van de groep|Geen|

## <a name="microsoftnetworknetworkinterfaces"></a>Micro soft. Network/networkInterfaces

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BytesSentRate|Verzonden bytes|Bytes|Totaal|Het aantal bytes dat de netwerk interface heeft verzonden|Geen|
|BytesReceivedRate|Ontvangen bytes|Bytes|Totaal|Het aantal bytes dat de netwerk interface heeft ontvangen|Geen|
|PacketsSentRate|Verzonden pakketten|Aantal|Totaal|Aantal pakketten dat de netwerk interface heeft verzonden|Geen|
|PacketsReceivedRate|Ontvangen pakketten|Aantal|Totaal|Aantal pakketten dat de netwerk interface heeft ontvangen|Geen|

## <a name="microsoftnetworkloadbalancers"></a>Micro soft. Network/loadBalancers

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|VipAvailability|Beschik baarheid gegevenspad|Aantal|Average|Gemiddelde Beschik baarheid van gegevens paden per tijds duur van Load Balancer|FrontendIPAddress,FrontendPort|
|DipAvailability|Status van Health probe|Aantal|Average|Gemiddelde status van Load Balancer Health probe per tijds duur|Protocol type, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount|Aantal bytes|Aantal|Totaal|Totaal aantal verzonden bytes binnen tijds periode|FrontendIPAddress, FrontendPort, direction|
|PacketCount|Aantal pakketten|Aantal|Totaal|Totaal aantal verzonden pakketten binnen tijds periode|FrontendIPAddress, FrontendPort, direction|
|SYNCount|SYN-aantal|Aantal|Totaal|Totaal aantal SYN-pakketten verzonden binnen tijds periode|FrontendIPAddress, FrontendPort, direction|
|SnatConnectionCount|Aantal SNAT-verbindingen|Aantal|Totaal|Totaal aantal nieuwe SNAT-verbindingen dat binnen een tijds periode is gemaakt|FrontendIPAddress,BackendIPAddress,ConnectionState|
|AllocatedSnatPorts|Toegewezen SNAT-poorten (preview-versie)|Aantal|Totaal|Totaal aantal toegewezen SNAT-poorten binnen tijds periode|FrontendIPAddress, BackendIPAddress, protocol type, IsAwaitingRemoval|
|UsedSnatPorts|Gebruikte SNAT-poorten (preview-versie)|Aantal|Totaal|Totaal aantal SNAT-poorten gebruikt binnen tijds periode|FrontendIPAddress, BackendIPAddress, protocol type, IsAwaitingRemoval|

## <a name="microsoftnetworkdnszones"></a>Micro soft. Network/dnszones

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|QueryVolume|Query volume|Aantal|Totaal|Aantal verzonden query's voor een DNS-zone|Geen|
|RecordSetCount|Aantal record sets|Aantal|Maximum|Aantal record sets in een DNS-zone|Geen|
|RecordSetCapacityUtilization|Capaciteits gebruik van record sets|Percentage|Maximum|Percentage van de set-capaciteit van de record die wordt gebruikt door een DNS-zone|Geen|


## <a name="microsoftnetworkpublicipaddresses"></a>Micro soft. Network/publicIPAddresses

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PacketsInDDoS|DDoS inkomende pakketten|CountPerSecond|Maximum|DDoS inkomende pakketten|Geen|
|PacketsDroppedDDoS|DDoS inkomende pakketten verwijderd|CountPerSecond|Maximum|DDoS inkomende pakketten verwijderd|Geen|
|PacketsForwardedDDoS|DDoS inkomende pakketten doorgestuurd|CountPerSecond|Maximum|DDoS inkomende pakketten doorgestuurd|Geen|
|TCPPacketsInDDoS|Binnenkomende TCP-pakketten DDoS|CountPerSecond|Maximum|Binnenkomende TCP-pakketten DDoS|Geen|
|TCPPacketsDroppedDDoS|DDoS binnenkomende TCP-pakketten|CountPerSecond|Maximum|DDoS binnenkomende TCP-pakketten|Geen|
|TCPPacketsForwardedDDoS|Doorgestuurde binnenkomende TCP-pakketten DDoS|CountPerSecond|Maximum|Doorgestuurde binnenkomende TCP-pakketten DDoS|Geen|
|UDPPacketsInDDoS|Binnenkomende UDP-pakketten DDoS|CountPerSecond|Maximum|Binnenkomende UDP-pakketten DDoS|Geen|
|UDPPacketsDroppedDDoS|Verwijderde binnenkomende UDP-pakketten DDoS|CountPerSecond|Maximum|Verwijderde binnenkomende UDP-pakketten DDoS|Geen|
|UDPPacketsForwardedDDoS|Door inkomende UDP-pakketten DDoS doorgestuurd|CountPerSecond|Maximum|Door inkomende UDP-pakketten DDoS doorgestuurd|Geen|
|BytesInDDoS|Binnenkomende bytes DDoS|BytesPerSecond|Maximum|Binnenkomende bytes DDoS|Geen|
|BytesDroppedDDoS|Binnenkomende bytes verloren DDoS|BytesPerSecond|Maximum|Binnenkomende bytes verloren DDoS|Geen|
|BytesForwardedDDoS|Doorgestuurde binnenkomende bytes DDoS|BytesPerSecond|Maximum|Doorgestuurde binnenkomende bytes DDoS|Geen|
|TCPBytesInDDoS|DDoS binnenkomende TCP-bytes|BytesPerSecond|Maximum|DDoS binnenkomende TCP-bytes|Geen|
|TCPBytesDroppedDDoS|DDoS binnenkomende TCP-bytes|BytesPerSecond|Maximum|DDoS binnenkomende TCP-bytes|Geen|
|TCPBytesForwardedDDoS|DDoS doorgestuurde binnenkomende TCP-bytes|BytesPerSecond|Maximum|DDoS doorgestuurde binnenkomende TCP-bytes|Geen|
|UDPBytesInDDoS|Binnenkomende UDP-bytes DDoS|BytesPerSecond|Maximum|Binnenkomende UDP-bytes DDoS|Geen|
|UDPBytesDroppedDDoS|Binnenkomend UDP-bytes verloren DDoS|BytesPerSecond|Maximum|Binnenkomend UDP-bytes verloren DDoS|Geen|
|UDPBytesForwardedDDoS|Doorgestuurde binnenkomende UDP-bytes DDoS|BytesPerSecond|Maximum|Doorgestuurde binnenkomende UDP-bytes DDoS|Geen|
|IfUnderDDoSAttack|Onder DDoS-aanval of niet|Aantal|Maximum|Onder DDoS-aanval of niet|Geen|
|DDoSTriggerTCPPackets|Binnenkomende TCP-pakketten om DDoS-beperking te activeren|CountPerSecond|Maximum|Binnenkomende TCP-pakketten om DDoS-beperking te activeren|Geen|
|DDoSTriggerUDPPackets|Binnenkomende UDP-pakketten om DDoS-beperking te activeren|CountPerSecond|Maximum|Binnenkomende UDP-pakketten om DDoS-beperking te activeren|Geen|
|DDoSTriggerSYNPackets|Inkomende SYN-pakketten om DDoS-beperking te activeren|CountPerSecond|Maximum|Inkomende SYN-pakketten om DDoS-beperking te activeren|Geen|
|VipAvailability|Beschik baarheid gegevenspad|Aantal|Average|Gemiddelde Beschik baarheid van IP-adressen per tijds duur|Poort|
|ByteCount|Aantal bytes|Aantal|Totaal|Totaal aantal verzonden bytes binnen tijds periode|Poort, richting|
|PacketCount|Aantal pakketten|Aantal|Totaal|Totaal aantal verzonden pakketten binnen tijds periode|Poort, richting|
|SynCount|SYN-aantal|Aantal|Totaal|Totaal aantal SYN-pakketten verzonden binnen tijds periode|Poort, richting|



## <a name="microsoftnetworkvirtualnetworks"></a>Micro soft. Network/virtualNetworks

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Retour tijd voor pings naar een virtuele machine|Milliseconden|Average|Retour tijd voor pings die naar een bestemmings-VM worden verzonden|SourceCustomerAddress,DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Pingen naar een virtuele machine is mislukt|Percentage|Average|Percentage van het aantal mislukte pings naar totale aantal verzonden Pings van een doel-VM|SourceCustomerAddress,DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Micro soft. Network/azurefirewalls

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ApplicationRuleHit|Aantal treffers toepassings regels|Aantal|Totaal|Aantal keer dat toepassings regels zijn geraakt|Status, reden, protocol|
|NetworkRuleHit|Aantal treffers in netwerk regels|Aantal|Totaal|Aantal keren dat netwerk regels zijn geraakt|Status, reden, protocol|
|FirewallHealth|Status van Firewall|Percentage|Average|Status van Firewall|Status, reden|
|DataProcessed|Verwerkte gegevens|Bytes|Totaal|Totale hoeveelheid gegevens die door de firewall worden verwerkt|Geen|
|SNATPortUtilization|Gebruik van SNAT-poort|Percentage|Average|Gebruik van SNAT-poort|Geen|


## <a name="microsoftnetworkapplicationgateways"></a>Micro soft. Network/applicationGateways

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Doorvoer|Doorvoer|BytesPerSecond|Average|Aantal bytes per seconde dat de Application Gateway heeft bediend|Geen|
|UnhealthyHostCount|Aantal hosts met slechte status|Aantal|Average|Aantal beschadigde backend-hosts|BackendSettingsPool|
|HealthyHostCount|Aantal goede hosts|Aantal|Average|Aantal gezonde backend-hosts|BackendSettingsPool|
|TotalRequests|Totaal aantal aanvragen|Aantal|Totaal|Aantal geslaagde aanvragen dat Application Gateway heeft bediend|BackendSettingsPool|
|AvgRequestCountPerHealthyHost|Aanvragen per minuut per gegezonde host|Aantal|Average|Gemiddeld aantal aanvragen per minuut op een gezonde backend-host in een groep|BackendSettingsPool|
|FailedRequests|Mislukte aanvragen|Aantal|Totaal|Aantal mislukte aanvragen dat Application Gateway heeft bediend|BackendSettingsPool|
|ResponseStatus|Reactie status|Aantal|Totaal|Http-antwoord status geretourneerd door Application Gateway|HttpStatusGroup|
|CurrentConnections|Huidige verbindingen|Aantal|Totaal|Aantal actieve verbindingen dat tot stand is gebracht met Application Gateway|Geen|
|NewConnectionsPerSecond|Nieuwe verbindingen per seconde|CountPerSecond|Average|Nieuwe verbindingen per seconde die zijn gemaakt met Application Gateway|Geen|
|CpuUtilization|CPU-gebruik|Percentage|Average|Huidig CPU-gebruik van de Application Gateway|Geen|
|CapacityUnits|Huidige capaciteits eenheden|Aantal|Average|Verbruikte capaciteits eenheden|Geen|
|FixedBillableCapacityUnits|Vaste factureerbare capaciteitseenheden|Aantal|Average|Minimale capaciteits eenheden waarvoor kosten in rekening worden gebracht|Geen|
|EstimatedBilledCapacityUnits|Geschatte gefactureerde capaciteits eenheden|Aantal|Average|Geschatte capaciteits eenheden waarvoor kosten in rekening worden gebracht|Geen|
|ComputeUnits|Huidige reken eenheden|Aantal|Average|Verbruikte reken eenheden|Geen|
|BackendResponseStatus|Reactie status van back-end|Aantal|Totaal|Het aantal HTTP-antwoord codes dat door de back-end-leden is gegenereerd. Dit omvat geen antwoord codes die zijn gegenereerd door de Application Gateway.|BackendServer, hosts, BackendHttpSetting, HttpStatusGroup|
|TlsProtocol|TLS-protocol van client|Aantal|Totaal|Het aantal TLS-en niet-TLS-aanvragen dat door de client is gestart en die verbinding heeft gemaakt met de Application Gateway. Als u de TLS-protocol distributie wilt weer geven, filtert u op het TLS-protocol van de dimensie.|Listener, TlsProtocol|
|Bytes sent|Verzonden bytes|Bytes|Totaal|Het totale aantal bytes dat is verzonden door de Application Gateway naar de clients|Listener|
|BytesReceived|Ontvangen bytes|Bytes|Totaal|Het totale aantal bytes dat is ontvangen door de Application Gateway van de clients|Listener|
|ClientRtt|Client RTT|Milliseconden|Average|Gemiddelde Round-retour tijd tussen clients en Application Gateway. Met deze metriek wordt aangegeven hoe lang het duurt om verbindingen tot stand te brengen en bevestigingen te retour neren|Listener|
|ApplicationGatewayTotalTime|Totale tijd van Application Gateway|Milliseconden|Average|De gemiddelde tijd die nodig is voor het verwerken van een aanvraag en het antwoord op verzen ding. Dit wordt berekend als gemiddelde van het interval van de tijd dat Application Gateway de eerste byte van een HTTP-aanvraag ontvangt naar het tijdstip waarop de bewerking voor het verzenden van het antwoord is voltooid. Het is belang rijk te weten dat dit doorgaans de verwerkings tijd van Application Gateway, de tijd dat de aanvraag-en antwoord pakketten op het netwerk onderweg zijn en het tijdstip waarop de back-end-server heeft gereageerd.|Listener|
|BackendConnectTime|Moment back-end verbinding|Milliseconden|Average|Tijd die is besteed aan het tot stand brengen van een verbinding met een back-end-server|Listener, BackendServer, hosts, BackendHttpSetting|
|BackendFirstByteResponseTime|Reactie tijd eerste byte van back-end|Milliseconden|Average|Tijds interval tussen begin van het tot stand brengen van een verbinding met de back-end-server en het ontvangen van de eerste byte van de reactie header, geschatte verwerkings tijd van de back-endserver|Listener, BackendServer, hosts, BackendHttpSetting|
|BackendLastByteResponseTime|Reactie tijd laatste byte van back-end|Milliseconden|Average|Tijds interval tussen begin van het tot stand brengen van een verbinding met de back-endserver en het ontvangen van de laatste byte van de antwoord tekst|Listener, BackendServer, hosts, BackendHttpSetting|
|MatchedCount|Totale regel distributie Web Application firewall v1|Aantal|Totaal|Web Application firewall v1 totale regel distributie voor het binnenkomende verkeer|RuleGroup, RuleId|
|BlockedCount|Regel distributie voor door Web Application firewall v1 geblokkeerde aanvragen|Aantal|Totaal|Regel distributie voor door Web Application firewall v1 geblokkeerde aanvragen|RuleGroup, RuleId|
|BlockedReqCount|Aantal geblokkeerde aanvragen voor Web Application firewall v1|Aantal|Totaal|Aantal geblokkeerde aanvragen voor Web Application firewall v1|Geen|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AverageBandwidth|Gateway-S2S-band breedte|BytesPerSecond|Average|Gemiddeld aantal site-naar-site-band breedte van een gateway in bytes per seconde|Geen|
|P2SBandwidth|Gateway P2S-band breedte|BytesPerSecond|Average|Gemiddelde Point-to-site band breedte van een gateway in bytes per seconde|Geen|
|P2SConnectionCount|Aantal P2S-verbindingen|Aantal|Maximum|Aantal Point-to-site-verbindingen van een gateway|Protocol|
|TunnelAverageBandwidth|Tunnel bandbreedte|BytesPerSecond|Average|Gemiddelde band breedte van een tunnel in bytes per seconde|ConnectionName, RemoteIP|
|TunnelEgressBytes|Bytes voor uitgaand tunnels|Bytes|Totaal|Uitgaande bytes van een tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Bytes van de tunnel ingang|Bytes|Totaal|Binnenkomende bytes van een tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Tunnel-uituitgangs pakketten|Aantal|Totaal|Aantal uitgaande pakketten van een tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Tunnel ingangs pakketten|Aantal|Totaal|Binnenkomend pakket aantal van een tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Uitschakeling van niet-overeenkomende TS-pakketten door tunnel|Aantal|Totaal|Aantal uitgevallen uitgaande pakketten van de selectie van de verkeers kiezer komen niet overeen met een tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Verloren gegane pakketten door de tunnel ingang TS komen niet overeen|Aantal|Totaal|Aantal inkomende pakketten in de verkeers selectie niet overeenkomen met een tunnel|ConnectionName, RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Micro soft. Network/expressRoutePorts

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|RxLightLevel|RxLightLevel|Aantal|Average|RX licht niveau in dBm|Koppeling, Lane|
|TxLightLevel|TxLightLevel|Aantal|Average|TX licht niveau in dBm|Koppeling, Lane|
|AdminState|AdminState|Aantal|Average|Beheer status van de poort|Koppeling|
|LineProtocol|LineProtocol|Aantal|Average|Status van het regel Protocol van de poort|Koppeling|
|PortBitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Bits die Azure per seconde binnenkomen|Koppeling|
|PortBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Bits egressing Azure per seconde|Koppeling|



## <a name="microsoftnetworkexpressroutecircuits"></a>Micro soft. Network/expressRouteCircuits

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Bits die Azure per seconde binnenkomen|PeeringType|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Bits egressing Azure per seconde|PeeringType|
|GlobalReachBitsInPerSecond|GlobalReachBitsInPerSecond|CountPerSecond|Average|Bits die Azure per seconde binnenkomen|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|GlobalReachBitsOutPerSecond|CountPerSecond|Average|Bits egressing Azure per seconde|PeeredCircuitSKey|
|BgpAvailability|BGP-Beschik baarheid|Percentage|Average|BGP-Beschik baarheid van MSEE naar alle peers.|PeeringType, peer|
|ArpAvailability|ARP-Beschik baarheid|Percentage|Average|ARP-Beschik baarheid van MSEE naar alle peers.|PeeringType, peer|
|QosDropBitsInPerSecond|DroppedInBitsPerSecond|CountPerSecond|Average|Ingangs gegevens die per seconde worden verwijderd|Geen|
|QosDropBitsOutPerSecond|DroppedOutBitsPerSecond|CountPerSecond|Average|Uitgaande bits van gegevens die per seconde worden verwijderd|Geen|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Micro soft. Network/expressRouteCircuits/peerings

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Bits die Azure per seconde binnenkomen|Geen|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Bits egressing Azure per seconde|Geen|

## <a name="microsoftnetworkconnections"></a>Micro soft. Network/Connections

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Bits die Azure per seconde binnenkomen|Geen|
|BitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Bits egressing Azure per seconde|Geen|

## <a name="microsoftnetworkexpressroutegateways"></a>Micro soft. Network/expressRouteGateways

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|BitsInPerSecond|CountPerSecond|Average|Bits die Azure per seconde binnenkomen|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|BitsOutPerSecond|CountPerSecond|Average|Bits egressing Azure per seconde|ConnectionName|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Micro soft. Network/trafficManagerProfiles

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|QpsByEndpoint|Query's op eind punt geretourneerd|Aantal|Totaal|Aantal keren dat een Traffic Manager eind punt is geretourneerd in het opgegeven tijds bestek|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Eindpunt status op eind punt|Aantal|Maximum|1 als de test status van een eind punt is ingeschakeld, 0 anders.|EndpointName|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Micro soft. Network/networkWatchers/connectionMonitors

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Tests mislukt|Percentage|Average|% van de controles van connectiviteits controle is mislukt|Geen|
|AverageRoundtripMs|Gem. retour tijd (MS)|Milliseconden|Average|Gemiddelde Round-retour tijd van het netwerk (MS) voor connectiviteits controle tests die zijn verzonden tussen de bron en het doel|Geen|
|ChecksFailedPercent|Percentage mislukte controles (preview-versie)|Percentage|Average|% van het controleren van de connectiviteits controle is mislukt|SourceAddress, SourceName, SourceResourceId, Source type, protocol, DestinationAddress, doelhost, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|
|RoundTripTimeMs|Retour tijd (MS) (preview-versie)|Milliseconden|Average|Retour tijd in milliseconden voor het controleren van de connectiviteits controle|SourceAddress, SourceName, SourceResourceId, Source type, protocol, DestinationAddress, doelhost, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Micro soft. Network/frontdoors

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|RequestCount|Aantal aanvragen|Aantal|Totaal|Het aantal client aanvragen dat wordt geleverd door de HTTP/S-proxy|Http status, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Aanvraag grootte|Bytes|Totaal|Het aantal bytes dat is verzonden als aanvragen van clients naar de HTTP/S-proxy|Http status, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Grootte van antwoord|Bytes|Totaal|Het aantal bytes dat is verzonden als reacties van HTTP/S-proxy naar clients|Http status, HttpStatusGroup, ClientRegion, ClientCountry|
|BillableResponseSize|Grootte van factureer bare antwoorden|Bytes|Totaal|Het aantal factureer bare bytes (minimale 2KB per aanvraag) dat wordt verzonden als antwoorden van HTTP/S-proxy naar clients.|Http status, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendRequestCount|Aantal back-aanvragen|Aantal|Totaal|Het aantal aanvragen dat is verzonden vanaf de HTTP/S-proxy naar back-end|Http status, HttpStatusGroup, back-end|
|BackendRequestLatency|Latentie van back-upaanvraag|Milliseconden|Average|De tijd die wordt berekend vanaf het moment dat de aanvraag door de HTTP/S-proxy naar de back-end werd verzonden, totdat de HTTP/S-proxy de laatste antwoord byte van de back-end heeft ontvangen|Back-end|
|TotalLatency|Totale latentie|Milliseconden|Average|De tijd die wordt berekend vanaf het moment dat de client aanvraag door de HTTP/S-proxy werd ontvangen totdat de client de laatste antwoord byte van de HTTP/S-proxy heeft bevestigd|Http status, HttpStatusGroup, ClientRegion, ClientCountry|
|BackendHealthPercentage|Back-status percentage|Percentage|Average|Het percentage geslaagde status controles van de HTTP/S-proxy naar back-end|Back-end, hosts|
|WebApplicationFirewallRequestCount|Aantal aanvragen voor Web Application firewall|Aantal|Totaal|Het aantal client aanvragen dat is verwerkt door de Web Application firewall|Beleidsnaam, regelnaam, actie|


## <a name="microsoftnetworkprivatednszones"></a>Micro soft. Network/privateDnsZones

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|QueryVolume|Query volume|Aantal|Totaal|Aantal verzonden query's voor een Privé-DNS zone|Geen|
|RecordSetCount|Aantal record sets|Aantal|Maximum|Aantal record sets in een Privé-DNS zone|Geen|
|RecordSetCapacityUtilization|Capaciteits gebruik van record sets|Percentage|Maximum|Percentage van de record capaciteit die wordt gebruikt door een Privé-DNS zone|Geen|
|VirtualNetworkLinkCount|Aantal Virtual Network koppelingen|Aantal|Maximum|Aantal virtuele netwerken dat is gekoppeld aan een Privé-DNS zone|Geen|
|VirtualNetworkLinkCapacityUtilization|Capaciteits gebruik Virtual Network koppeling|Percentage|Maximum|Percentage van Virtual Network koppelings capaciteit die wordt gebruikt door een Privé-DNS zone|Geen|
|VirtualNetworkWithRegistrationLinkCount|Aantal registratie koppelingen Virtual Network|Aantal|Maximum|Aantal virtuele netwerken dat is gekoppeld aan een Privé-DNS zone waarvoor automatische registratie is ingeschakeld|Geen|
|VirtualNetworkWithRegistrationCapacityUtilization|Capaciteits gebruik van registratie koppeling Virtual Network|Percentage|Maximum|Percentage van Virtual Network koppeling met capaciteit voor automatische registratie die wordt gebruikt door een Privé-DNS zone|Geen|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Micro soft. notification hubs/naam ruimten/notification hubs

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|registratie. alle|Registratie bewerkingen|Aantal|Totaal|Het aantal voltooide registratie bewerkingen (gemaakte bijwerk query's en verwijderingen). |Geen|
|registratie. Create|Bewerkingen voor het maken van registratie|Aantal|Totaal|Het aantal gemaakte registraties.|Geen|
|registratie. update|Registratie-update bewerkingen|Aantal|Totaal|Het aantal voltooide registratie-updates.|Geen|
|registratie. ophalen|Lees bewerkingen voor registratie|Aantal|Totaal|Het aantal geslaagde registratie query's.|Geen|
|registratie. Delete|Verwijderings bewerkingen voor registratie|Aantal|Totaal|Het aantal voltooide registraties dat is verwijderd.|Geen|
|e-mail|Inkomende berichten|Aantal|Totaal|Het aantal geslaagde verzend-API-aanroepen. |Geen|
|inkomend. gepland|Geplande push meldingen verzonden|Aantal|Totaal|Geplande push meldingen geannuleerd|Geen|
|Binnenkomend. gepland. annuleren|Geplande push meldingen geannuleerd|Aantal|Totaal|Geplande push meldingen geannuleerd|Geen|
|gepland. in behandeling|Geplande meldingen in behandeling|Aantal|Totaal|Geplande meldingen in behandeling|Geen|
|installatie. alle|Bewerkingen voor installatie beheer|Aantal|Totaal|Bewerkingen voor installatie beheer|Geen|
|installatie. Get|Installatie bewerkingen ophalen|Aantal|Totaal|Installatie bewerkingen ophalen|Geen|
|installatie. upsert|Installatie bewerkingen maken of bijwerken|Aantal|Totaal|Installatie bewerkingen maken of bijwerken|Geen|
|installatie. patch|Patch-installatie bewerkingen|Aantal|Totaal|Patch-installatie bewerkingen|Geen|
|installatie. Delete|Installatie bewerkingen verwijderen|Aantal|Totaal|Installatie bewerkingen verwijderen|Geen|
|uitgaand. allpns. geslaagd|Geslaagde meldingen|Aantal|Totaal|Het aantal geslaagde meldingen.|Geen|
|uitgaande. allpns. invalidpayload|Payload-fouten|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de PNS een ongeldige Payload-fout heeft geretourneerd.|Geen|
|uitgaande. allpns. pnserror|Externe meldingen systeem fouten|Aantal|Totaal|Het aantal pushes dat is mislukt omdat er een probleem is opgetreden bij het communiceren met de PNS (er zijn verificatie problemen uitgesloten).|Geen|
|uitgaande. allpns. channelerror|Kanaal fouten|Aantal|Totaal|Het aantal pushes dat is mislukt omdat het kanaal ongeldig is en niet is gekoppeld aan de juiste app beperkt of verlopen.|Geen|
|uitgaande. allpns. badorexpiredchannel|Ongeldige of verlopen kanaal fouten|Aantal|Totaal|Het aantal pushes dat is mislukt omdat het kanaal/token-registratie in de registratie is verlopen of ongeldig is.|Geen|
|uitgaand. wns. geslaagd|Geslaagde meldingen WNS|Aantal|Totaal|Het aantal geslaagde meldingen.|Geen|
|uitgaande. wns. invalidcredentials|WNS-verificatie fouten (ongeldige referenties)|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd. (Windows Live herkent de referenties niet).|Geen|
|uitgaande. wns. badchannel|WNS ongeldige kanaal fout|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de kanaal in de registratie niet is herkend (WNS-status: 404 niet gevonden).|Geen|
|uitgaande. wns. expiredchannel|WNS-fout met verlopen kanaal|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de kanaal is verlopen (WNS-status: 410 verdwenen).|Geen|
|uitgaande. wns. throttled|WNS beperkte meldingen|Aantal|Totaal|Het aantal pushes dat is mislukt omdat WNS deze app beperkt (WNS-status: 406 niet toegestaan).|Geen|
|uitgaande. wns. tokenproviderunreachable|WNS-verificatie fouten (onbereikbaar)|Aantal|Totaal|Windows Live is niet bereikbaar.|Geen|
|uitgaande. wns. invalidtoken|WNS-verificatie fouten (ongeldig token)|Aantal|Totaal|Het token dat is gegeven aan WNS is niet geldig (WNS-status: 401 niet toegestaan).|Geen|
|uitgaande. wns. wrongtoken|WNS-autorisatie fouten (onjuist token)|Aantal|Totaal|Het token dat is gegeven aan WNS is geldig, maar voor een andere toepassing (WNS-status: 403 verboden). Dit kan gebeuren als de kanaal in de registratie is gekoppeld aan een andere app. Controleer of de client-app is gekoppeld aan dezelfde app waarvan de referenties zich in de notification hub bevinden.|Geen|
|uitgaande. wns. invalidnotificationformat|Ongeldige indeling van WNS-melding|Aantal|Totaal|De indeling van de melding is ongeldig (WNS-status: 400). Houd er rekening mee dat WNS niet alle ongeldige payloads weigert.|Geen|
|uitgaande. wns. invalidnotificationsize|Fout met ongeldige grootte van WNS-melding|Aantal|Totaal|De nettolading van de melding is te groot (WNS-status: 413).|Geen|
|uitgaande. wns. channelthrottled|WNS-kanaal beperkt|Aantal|Totaal|De melding is verwijderd omdat de kanaal in de registratie is beperkt (WNS-reactie header: X-WNS-notification status: channelThrottled).|Geen|
|uitgaande. wns. channeldisconnected|Verbinding met WNS-kanaal verbroken|Aantal|Totaal|De melding is verwijderd omdat de kanaal in de registratie is beperkt (WNS-reactie header: X-WNS-DeviceConnectionStatus: disconnected).|Geen|
|uitgaande. wns. dropd|WNS-verwijderde meldingen|Aantal|Totaal|De melding is verwijderd omdat de kanaal in de registratie is beperkt (X-WNS-notification status: verwijderd maar niet X-WNS-DeviceConnectionStatus: disconnected).|Geen|
|uitgaande. wns. pnserror|WNS-fouten|Aantal|Totaal|Er is geen melding bezorgd vanwege fouten in de communicatie met WNS.|Geen|
|uitgaande. wns. authenticationerror|WNS-verificatie fouten|Aantal|Totaal|Er is een melding niet bezorgd omdat er fouten zijn opgetreden tijdens de communicatie met Windows Live ongeldige referenties of een onjuist token.|Geen|
|uitgaand. apns. geslaagd|Geslaagde meldingen van APNS|Aantal|Totaal|Het aantal geslaagde meldingen.|Geen|
|uitgaand. apns. invalidcredentials|APNS-autorisatie fouten|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd.|Geen|
|uitgaand. apns. badchannel|Fout met ongeldige APNS-kanaal|Aantal|Totaal|Het aantal pushes dat is mislukt omdat het token ongeldig is (APNS-status code: 8).|Geen|
|uitgaand. apns. expiredchannel|Fout bij verlopen van APNS-kanaal|Aantal|Totaal|Het aantal tokens dat ongeldig is gemaakt door het APNS-feedback kanaal.|Geen|
|uitgaand. apns. invalidnotificationsize|Fout door ongeldige grootte van APNS-melding|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de payload te groot is (APNS-status code: 7).|Geen|
|uitgaand. apns. pnserror|APNS-fouten|Aantal|Totaal|Het aantal pushes dat is mislukt vanwege fouten in de communicatie met APNS.|Geen|
|uitgaand. GCM. geslaagd|Geslaagde meldingen GCM|Aantal|Totaal|Het aantal geslaagde meldingen.|Geen|
|uitgaande. GCM. invalidcredentials|GCM-verificatie fouten (ongeldige referenties)|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd.|Geen|
|uitgaande. GCM. badchannel|GCM ongeldige kanaal fout|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de registratie in de registratie niet is herkend (GCM-resultaat: ongeldige registratie).|Geen|
|uitgaande. GCM. expiredchannel|GCM-fout met verlopen kanaal|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de registratie in de registratie is verlopen (GCM resultaat: NotRegistered).|Geen|
|uitgaande. GCM. throttled|GCM beperkte meldingen|Aantal|Totaal|Het aantal pushes dat is mislukt omdat GCM deze app heeft beperkt (GCM-status code: 501-599 of resultaat: niet beschikbaar).|Geen|
|uitgaande. GCM. invalidnotificationformat|Ongeldige indeling van GCM-melding|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de payload niet juist is geformatteerd (GCM-resultaat: InvalidDataKey of InvalidTtl).|Geen|
|uitgaande. GCM. invalidnotificationsize|Fout met ongeldige grootte van GCM-melding|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de payload te groot is (GCM-resultaat: MessageTooBig).|Geen|
|uitgaande. GCM. wrongchannel|GCM onjuiste kanaal fout|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de registratie in de registratie niet is gekoppeld aan de huidige app (GCM-resultaat: InvalidPackageName).|Geen|
|uitgaande. GCM. pnserror|GCM-fouten|Aantal|Totaal|Het aantal pushes dat is mislukt vanwege fouten in de communicatie met GCM.|Geen|
|uitgaande. GCM. authenticationerror|GCM-verificatie fouten|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd, de referenties zijn geblokkeerd of de SenderId niet juist is geconfigureerd in de app (GCM resultaat: MismatchedSenderId).|Geen|
|uitgaand. mpns. geslaagd|Geslaagde meldingen MPNS|Aantal|Totaal|Het aantal geslaagde meldingen.|Geen|
|uitgaande. mpns. invalidcredentials|Ongeldige referenties MPNS|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd.|Geen|
|uitgaande. mpns. badchannel|MPNS ongeldige kanaal fout|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de kanaal in de registratie niet is herkend (MPNS-status: 404 niet gevonden).|Geen|
|uitgaande. mpns. throttled|MPNS beperkte meldingen|Aantal|Totaal|Het aantal pushes dat is mislukt omdat MPNS deze app beperkt (WNS MPNS: 406 niet toegestaan).|Geen|
|uitgaande. mpns. invalidnotificationformat|Ongeldige indeling van MPNS-melding|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de payload van de melding te groot is.|Geen|
|uitgaande. mpns. channeldisconnected|Verbinding met MPNS-kanaal verbroken|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de kanaal van de registratie is verbroken (MPNS-status: 412 niet gevonden).|Geen|
|uitgaande. mpns. dropd|MPNS-verwijderde meldingen|Aantal|Totaal|Het aantal pushes dat is verwijderd door MPNS (MPNS-reactie header: X-notification status: QueueFull of onderdrukt).|Geen|
|uitgaande. mpns. pnserror|MPNS-fouten|Aantal|Totaal|Het aantal pushes dat is mislukt vanwege fouten in de communicatie met MPNS.|Geen|
|uitgaande. mpns. authenticationerror|MPNS-verificatie fouten|Aantal|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd.|Geen|
|notificationhub. pushes|Alle uitgaande meldingen|Aantal|Totaal|Alle uitgaande meldingen van de notification hub|Geen|
|binnenkomende. alle. aanvragen|Alle inkomende aanvragen|Aantal|Totaal|Totaal aantal binnenkomende aanvragen voor een notification hub|Geen|
|inkomend. alle. failedrequests|Alle binnenkomende mislukte aanvragen|Aantal|Totaal|Totaal aantal binnenkomende mislukte aanvragen voor een notification hub|Geen|

## <a name="microsoftoperationalinsightsworkspaces"></a>Micro soft. OperationalInsights/werk ruimten

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage vrije inodes Average_|% Vrije inodes|Aantal|Average|Percentage vrije inodes Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|% Beschik bare ruimte Average_|Percentage beschik bare ruimte|Aantal|Average|% Beschik bare ruimte Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_% gebruikte inodes|% Gebruikte inodes|Aantal|Average|Average_% gebruikte inodes|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Percentage gebruikte ruimte Average_|Percentage gebruikte ruimte|Aantal|Average|Percentage gebruikte ruimte Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk Lees bewerkingen in bytes per seconde|Gelezen bytes per seconde|Aantal|Average|Average_Disk Lees bewerkingen in bytes per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk Lees bewerkingen per seconde|Lees bewerkingen per seconde|Aantal|Average|Average_Disk Lees bewerkingen per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk overdrachten per seconde|Schijf overdrachten per seconde|Aantal|Average|Average_Disk overdrachten per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk geschreven bytes per seconde|Geschreven bytes per seconde|Aantal|Average|Average_Disk geschreven bytes per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Disk schrijf bewerkingen per seconde|Schrijf bewerkingen per seconde|Aantal|Average|Average_Disk schrijf bewerkingen per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Free MB|Beschik bare mega bytes|Aantal|Average|Average_Free MB|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Logical schijf bytes per seconde|Bytes van logische schijf per seconde|Aantal|Average|Average_Logical schijf bytes per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Percentage beschik bare geheugen Average_|Percentage beschikbaar geheugen|Aantal|Average|Percentage beschik bare geheugen Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_ percentage beschik bare wissel ruimte|Percentage beschik bare wissel ruimte|Aantal|Average|Average_ percentage beschik bare wissel ruimte|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_ percentage gebruikt geheugen|Percentage gebruikt geheugen|Aantal|Average|Average_ percentage gebruikt geheugen|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_ percentage gebruikte wissel ruimte|Percentage gebruikte wissel ruimte|Aantal|Average|Average_ percentage gebruikte wissel ruimte|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Available MB geheugen|Beschikbaar geheugen in mega bytes|Aantal|Average|Average_Available MB geheugen|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Available MB wisselen|Beschik bare mega bytes wisselen|Aantal|Average|Average_Available MB wisselen|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Page Lees bewerkingen per seconde|Gelezen pagina's per seconde|Aantal|Average|Average_Page Lees bewerkingen per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Page schrijf bewerkingen per seconde|Geschreven pagina's per seconde|Aantal|Average|Average_Page schrijf bewerkingen per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Pages/sec.|Pagina's per seconde|Aantal|Average|Average_Pages/sec.|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Used MB wissel ruimte|Gebruikte MB wissel ruimte|Aantal|Average|Average_Used MB wissel ruimte|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Used geheugen MB|Gebruikt geheugen Mbytes|Aantal|Average|Average_Used geheugen MB|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Verzonden Average_Total bytes|Totaal aantal verzonden bytes|Aantal|Average|Verzonden Average_Total bytes|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Ontvangen Average_Total bytes|Totaal aantal ontvangen bytes|Aantal|Average|Ontvangen Average_Total bytes|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Total bytes|Totaal aantal bytes|Aantal|Average|Average_Total bytes|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Verzonden Average_Total pakketten|Totaal aantal verzonden pakketten|Aantal|Average|Verzonden Average_Total pakketten|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Ontvangen Average_Total pakketten|Totaal aantal ontvangen pakketten|Aantal|Average|Ontvangen Average_Total pakketten|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Total RX-fouten|Totaal aantal RX-fouten|Aantal|Average|Average_Total RX-fouten|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Total TX-fouten|Totaal aantal TX-fouten|Aantal|Average|Average_Total TX-fouten|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Total conflicten|Totaal aantal conflicten|Aantal|Average|Average_Total conflicten|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Avg. Gelezen bytes per seconde|Gemiddelde Lees tijd schijf|Aantal|Average|Average_Avg. Gelezen bytes per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Avg. Seconde/overdracht schijf|Gemiddelde tijd schijf overdracht|Aantal|Average|Average_Avg. Seconde/overdracht schijf|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Avg. voor de fysieke schijf|Gemiddelde schrijf tijd schijf|Aantal|Average|Average_Avg. voor de fysieke schijf|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Physical schijf bytes per seconde|Bytes van fysieke schijf per seconde|Aantal|Average|Average_Physical schijf bytes per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Beschermde tijd van Average_Pct|Pct-geprivilegieerde tijd|Aantal|Average|Beschermde tijd van Average_Pct|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Gebruikers tijd van Average_Pct|Pct-gebruikers tijd|Aantal|Average|Gebruikers tijd van Average_Pct|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Used geheugen KB|Gebruikte geheugen-kBytes|Aantal|Average|Average_Used geheugen KB|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Virtual gedeeld geheugen|Virtueel gedeeld geheugen|Aantal|Average|Average_Virtual gedeeld geheugen|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_ percentage DPC-tijd|Percentage DPC-tijd|Aantal|Average|Average_ percentage DPC-tijd|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|% Niet-actieve tijd Average_|Percentage niet-actieve tijd|Aantal|Average|% Niet-actieve tijd Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Percentage interrupt-tijd van Average_|Percentage interrupt-tijd|Aantal|Average|Percentage interrupt-tijd van Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_% i/o-wacht tijd|% I/o-wacht tijd|Aantal|Average|Average_% i/o-wacht tijd|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Tijd van Average_% leuk|Percentage tijd in Nice|Aantal|Average|Tijd van Average_% leuk|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Percentage tijd in beschermde modus Average_|Percentage tijd in beschermde modus|Aantal|Average|Percentage tijd in beschermde modus Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Percentage processor tijd van Average_|Percentage processortijd|Aantal|Average|Percentage processor tijd van Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Percentage gebruikers tijd van Average_|Percentage gebruikers tijd|Aantal|Average|Percentage gebruikers tijd van Average_|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Free fysiek geheugen|Vrij fysiek geheugen|Aantal|Average|Average_Free fysiek geheugen|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Free ruimte in wissel geheugen bestanden|Vrije ruimte in wissel geheugen bestanden|Aantal|Average|Average_Free ruimte in wissel geheugen bestanden|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Virtueel geheugen Average_Free|Vrij virtueel geheugen|Aantal|Average|Virtueel geheugen Average_Free|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Processes|Processen|Aantal|Average|Average_Processes|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Size opgeslagen in Wissel bestanden|Grootte opgeslagen in Wissel bestanden|Aantal|Average|Average_Size opgeslagen in Wissel bestanden|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Uptime|Systeem|Aantal|Average|Average_Uptime|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Users|Gebruikers|Aantal|Average|Average_Users|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Wachtrij lengte van Average_Current schijf|Huidige wachtrij lengte voor de schijf|Aantal|Average|Wachtrij lengte van Average_Current schijf|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Available MB|Beschik bare Mbytes|Aantal|Average|Average_Available MB|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_% toegewezen bytes in gebruik|% Toegewezen bytes in gebruik|Aantal|Average|Average_% toegewezen bytes in gebruik|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Ontvangen Average_Bytes per seconde|Ontvangen bytes per seconde|Aantal|Average|Ontvangen Average_Bytes per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Verzonden Average_Bytes per seconde|Verzonden bytes per seconde|Aantal|Average|Verzonden Average_Bytes per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Average_Bytes totaal per seconde|Totaal aantal bytes per seconde|Aantal|Average|Average_Bytes totaal per seconde|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Lengte van Average_Processor wachtrij|Lengte van de processor wachtrij|Aantal|Average|Lengte van Average_Processor wachtrij|Computer, ObjectName, INSTANCENAME, CounterPath, hebben|
|Hartslag|Hartslag|Aantal|Totaal|Hartslag|Computer, OSType, versie, SourceComputerId|
|Bijwerken|Bijwerken|Aantal|Average|Bijwerken|Computer, product, classificatie, update State, optioneel, goedgekeurd|
|Gebeurtenis|Gebeurtenis|Aantal|Average|Gebeurtenis|Source, EventLog, computer, EventCategory, EventLevel, EventLevelName, Event gebeurtenis|

## <a name="microsoftpeeringpeeringservices"></a>Micro soft. peering/peeringServices

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PrefixLatency|Voorvoegsel latentie|Milliseconden|Average|Latentie van mediaan voorvoegsel|Voorvoegselnaam|

## <a name="microsoftpeeringpeerings"></a>Micro soft. peering/peering

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SessionAvailabilityV4|Sessie beschikbaarheid v4|Percentage|Average|Beschik baarheid van de v4-sessie|ConnectionId|
|SessionAvailabilityV6|Sessie beschikbaarheid V6|Percentage|Average|Beschik baarheid van de V6-sessie|ConnectionId|
|IngressTrafficRate|Frequentie van ingangs verkeer|BitsPerSecond|Average|Ingangs verkeers frequentie in bits per seconde|ConnectionId|
|EgressTrafficRate|Frequentie van uitgangs verkeer|BitsPerSecond|Average|Frequentie van uitgaand verkeer in bits per seconde|ConnectionId|


## <a name="microsoftpowerbidedicatedcapacities"></a>Micro soft. PowerBIDedicated/capaciteiten

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|QueryDuration|Queryduur|Milliseconden|Average|DAX-query duur in laatste interval|Geen dimensies|
|QueryPoolJobQueueLength|Threads: lengte van taak wachtrij voor query pool|Aantal|Average|Aantal taken in de wachtrij van de query thread pool.|Geen dimensies|
|qpu_high_utilization_metric|Hoog QPU-gebruik|Aantal|Totaal|QPU hoog gebruik in de laatste minuut, 1 voor hoog QPU gebruik, anders 0|Geen dimensies|
|memory_metric|Geheugen|Bytes|Average|Geheugen. Bereik 0-3 GB voor a1, 0-5 GB voor a2, 0-10 GB voor a3, 0-25 GB voor A4, 0-50 GB voor A5 en 0-100 GB voor A6|Geen dimensies|
|memory_thrashing_metric|Geheugenthrashing|Percentage|Average|Gemiddeld geheugen overbelasting.|Geen dimensies|


## <a name="microsoftprojectbabylonaccounts"></a>Micro soft. ProjectBabylon/accounts

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AssetDistributionByClassification|Activa distributie per classificatie|Aantal|Totaal|Geeft aan hoeveel activa met een bepaalde classificatie zijn toegewezen, dat wil zeggen dat ze zijn geclassificeerd met dat label.|Classificatie, bron, ResourceId|
|AssetDistributionByStorageType|Activa distributie per opslag type|Aantal|Totaal|Hiermee wordt het aantal assets van een bepaald opslag type aangegeven.|Para, ResourceId|
|CatalogActiveUsers|Dagelijkse actieve gebruikers|Aantal|Totaal|Aantal actieve gebruikers per dag|ResourceId|
|CatalogUsage|Gebruiks distributie per bewerking|Aantal|Totaal|Geef aan hoeveel gebruiker van de bewerking de catalogus maakt, d.w.z. toegang, zoeken, woorden lijst.|Bewerking, ResourceId|
|NumberOfAssetsWithClassifications|Aantal activa met ten minste één classificatie|Aantal|Average|Hiermee wordt het aantal assets aangegeven met ten minste één label classificatie.|ResourceId|
|ScanCancelled|De scan is geannuleerd|Aantal|Totaal|Hiermee wordt het aantal geannuleerde scans aangegeven.|ResourceId|
|ScanCompleted|De scan is voltooid|Aantal|Totaal|Hiermee wordt het aantal scans aangegeven dat is voltooid.|ResourceId|
|ScanFailed|Kan niet scannen|Aantal|Totaal|Hiermee wordt aangegeven dat het aantal mislukte scans is mislukt.|ResourceId|
|ScanTimeTaken|Gebruikte scan tijd|Seconden|Totaal|Geeft de totale Scan tijd in seconden.|ResourceId|




## <a name="microsoftrelaynamespaces"></a>Micro soft. relay/naam ruimten

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ListenerConnections-geslaagd|ListenerConnections-geslaagd|Aantal|Totaal|Geslaagde ListenerConnections voor micro soft. relay.|EntityName, kan operationresult niet|
|ListenerConnections-client error|ListenerConnections-client error|Aantal|Totaal|Client error op ListenerConnections voor micro soft. relay.|EntityName, kan operationresult niet|
|ListenerConnections-server error|ListenerConnections-server error|Aantal|Totaal|Server error op ListenerConnections voor micro soft. relay.|EntityName, kan operationresult niet|
|SenderConnections-geslaagd|SenderConnections-geslaagd|Aantal|Totaal|Geslaagde SenderConnections voor micro soft. relay.|EntityName, kan operationresult niet|
|SenderConnections-client error|SenderConnections-client error|Aantal|Totaal|Client error op SenderConnections voor micro soft. relay.|EntityName, kan operationresult niet|
|SenderConnections-server error|SenderConnections-server error|Aantal|Totaal|Server error op SenderConnections voor micro soft. relay.|EntityName, kan operationresult niet|
|ListenerConnections-TotalRequests|ListenerConnections-TotalRequests|Aantal|Totaal|Totale ListenerConnections voor micro soft. relay.|EntityName|
|SenderConnections-TotalRequests|SenderConnections-TotalRequests|Aantal|Totaal|Totaal aantal SenderConnections-aanvragen voor micro soft. relay.|EntityName|
|ActiveConnections|ActiveConnections|Aantal|Totaal|Totaal aantal ActiveConnection voor micro soft. relay.|EntityName|
|ActiveListeners|ActiveListeners|Aantal|Totaal|Totale ActiveListeners voor micro soft. relay.|EntityName|
|BytesTransferred|BytesTransferred|Aantal|Totaal|Totale BytesTransferred voor micro soft. relay.|EntityName|
|ListenerDisconnects|ListenerDisconnects|Aantal|Totaal|Totale ListenerDisconnects voor micro soft. relay.|EntityName|
|SenderDisconnects|SenderDisconnects|Aantal|Totaal|Totale SenderDisconnects voor micro soft. relay.|EntityName|


## <a name="microsoftsearchsearchservices"></a>Micro soft. Search/searchServices

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SearchLatency|Zoek latentie|Seconden|Average|Gemiddelde Zoek latentie voor de zoek service|Geen|
|SearchQueriesPerSecond|Zoek query's per seconde|CountPerSecond|Average|Zoek query's per seconde voor de zoek service|Geen|
|ThrottledSearchQueriesPercentage|Percentage vertraagde Zoek query's|Percentage|Average|Percentage Zoek query's dat is beperkt tot de zoek service|Geen|


## <a name="microsoftservicebusnamespaces"></a>Micro soft. ServiceBus/naam ruimten

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SuccessfulRequests|Geslaagde aanvragen|Aantal|Totaal|Totaal aantal geslaagde aanvragen voor een naam ruimte|EntityName, kan operationresult niet|
|ServerErrors|Server fouten.|Aantal|Totaal|Server fouten voor micro soft. ServiceBus.|EntityName, kan operationresult niet|
|UserErrors|Gebruikers fouten.|Aantal|Totaal|Gebruikers fouten voor micro soft. ServiceBus.|EntityName, kan operationresult niet|
|ThrottledRequests|Vertraagde aanvragen.|Aantal|Totaal|Beperkte aanvragen voor micro soft. ServiceBus.|EntityName, kan operationresult niet|
|IncomingRequests|Binnenkomende aanvragen|Aantal|Totaal|Binnenkomende aanvragen voor micro soft. ServiceBus.|EntityName|
|IncomingMessages|Inkomende berichten|Aantal|Totaal|Binnenkomende berichten voor micro soft. ServiceBus.|EntityName|
|OutgoingMessages|Uitgaande berichten|Aantal|Totaal|Uitgaande berichten voor micro soft. ServiceBus.|EntityName|
|ActiveConnections|ActiveConnections|Aantal|Totaal|Totaal aantal actieve verbindingen voor micro soft. ServiceBus.|Geen|
|ConnectionsOpened|Geopende verbindingen.|Aantal|Average|Geopende verbindingen voor micro soft. ServiceBus.|EntityName|
|ConnectionsClosed|Verbindingen gesloten.|Aantal|Average|Gesloten verbindingen voor micro soft. ServiceBus.|EntityName|
|Grootte|Grootte|Bytes|Average|Grootte van een wachtrij/onderwerp in bytes.|EntityName|
|Berichten|Aantal berichten in een wachtrij/onderwerp.|Aantal|Average|Aantal berichten in een wachtrij/onderwerp.|EntityName|
|ActiveMessages|Aantal actieve berichten in een wachtrij/onderwerp.|Aantal|Average|Aantal actieve berichten in een wachtrij/onderwerp.|EntityName|
|DeadletteredMessages|Aantal onbestelbare berichten in een wachtrij/onderwerp.|Aantal|Average|Aantal onbestelbare berichten in een wachtrij/onderwerp.|EntityName|
|ScheduledMessages|Aantal geplande berichten in een wachtrij/onderwerp.|Aantal|Average|Aantal geplande berichten in een wachtrij/onderwerp.|EntityName|
|NamespaceCpuUsage|CPU|Percentage|Maximum|Metrische gegevens voor CPU-gebruik van service bus Premium-naam ruimte.|Replica|
|NamespaceMemoryUsage|Geheugengebruik|Percentage|Maximum|Metrische geheugen gebruik van service bus Premium-naam ruimte.|Replica|
|CPUXNS|CPU (afgeschaft)|Percentage|Maximum|Metrische gegevens voor CPU-gebruik van service bus Premium-naam ruimte. Deze metrische waarde is verouderd. Gebruik in plaats daarvan de CPU-metriek (NamespaceCpuUsage).|Replica|
|WSXNS|Geheugen gebruik (afgeschaft)|Percentage|Maximum|Metrische geheugen gebruik van service bus Premium-naam ruimte. Deze metriek is afgeschaft. Gebruik in plaats daarvan de metriek geheugen gebruik (NamespaceMemoryUsage).|Replica|


## <a name="microsoftservicefabricmeshapplications"></a>Micro soft. ServiceFabricMesh/toepassingen

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AllocatedCpu|AllocatedCpu|Aantal|Average|CPU toegewezen aan deze container in milli-kernen|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|AllocatedMemory|Bytes|Average|Geheugen toegewezen aan deze container in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualCpu|ActualCpu|Aantal|Average|Werkelijk CPU-gebruik in milli-kernen|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|ActualMemory|Bytes|Average|Werkelijk geheugen gebruik in MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|CpuUtilization|CpuUtilization|Percentage|Average|Gebruik van CPU voor deze container als percentage van AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|MemoryUtilization|Percentage|Average|Gebruik van CPU voor deze container als percentage van AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Aantal|Average|Status van Service Fabric mesh-toepassing|ApplicationName, status|
|ServiceStatus|ServiceStatus|Aantal|Average|Integriteits status van een service in Service Fabric mesh-toepassing|ApplicationName, status, servicenaam|
|ServiceReplicaStatus|ServiceReplicaStatus|Aantal|Average|Integriteits status van een service replica in Service Fabric mesh-toepassing|ApplicationName, status, ServiceName, ServiceReplicaName|
|Container status|Container status|Aantal|Average|Status van de container in Service Fabric mesh-toepassing|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, status|
|RestartCount|RestartCount|Aantal|Average|Telling van een container in Service Fabric mesh-toepassing opnieuw starten|ApplicationName, status, ServiceName, ServiceReplicaName, CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Micro soft. SignalRService/Signa lering

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ConnectionCount|Aantal verbindingen|Aantal|Maximum|De hoeveelheid gebruikers verbinding.|Eindpunt|
|MessageCount|Aantal berichten|Aantal|Totaal|De totale hoeveelheid berichten.|Geen|
|InboundTraffic|Binnenkomend verkeer|Bytes|Totaal|Het inkomende verkeer van de service|Geen|
|OutboundTraffic|Uitgaand verkeer|Bytes|Totaal|Het uitgaande verkeer van de service|Geen|
|UserErrors|Gebruikers fouten|Percentage|Maximum|Het percentage gebruikers fouten|Geen|
|SystemErrors|Systeem fouten|Percentage|Maximum|Het percentage systeem fouten|Geen|



## <a name="microsoftsqlserversdatabases"></a>Micro soft. SQL/servers/data bases

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Percentage|Average|CPU-percentage|Geen|
|physical_data_read_percent|Gegevens-I/O-percentage|Percentage|Average|Gegevens-I/O-percentage|Geen|
|log_write_percent|Logboek-IO-percentage|Percentage|Average|Logboek-IO-percentage. Niet van toepassing op data warehouses.|Geen|
|dtu_consumption_percent|DTU-percentage|Percentage|Average|DTU-percentage. Is van toepassing op DTU-gebaseerde data bases.|Geen|
|storage|Gebruikte gegevens ruimte|Bytes|Maximum|Gebruikte gegevens ruimte. Niet van toepassing op data warehouses.|Geen|
|connection_successful|Geslaagde verbindingen|Aantal|Totaal|Geslaagde verbindingen|Geen|
|connection_failed|Mislukte verbindingen|Aantal|Totaal|Mislukte verbindingen|Geen|
|blocked_by_firewall|Geblokkeerd door de firewall|Aantal|Totaal|Geblokkeerd door de firewall|Geen|
|constateer|Impassen|Aantal|Totaal|Impassen. Niet van toepassing op data warehouses.|Geen|
|storage_percent|Percentage gebruikte gegevens ruimte|Percentage|Maximum|Percentage gebruikte gegevens ruimte. Niet van toepassing op data warehouses of grootschalige-data bases.|Geen|
|xtp_storage_percent|Percentage OLTP-opslag in het geheugen|Percentage|Average|Percentage OLTP-opslag in het geheugen. Niet van toepassing op data warehouses.|Geen|
|workers_percent|Percentage werk nemers|Percentage|Average|Werknemers percentage. Niet van toepassing op data warehouses.|Geen|
|sessions_percent|Percentage sessies|Percentage|Average|Percentage sessies. Niet van toepassing op data warehouses.|Geen|
|dtu_limit|DTU-limiet|Aantal|Average|DTU-limiet. Is van toepassing op DTU-gebaseerde data bases.|Geen|
|dtu_used|DTU gebruikt|Aantal|Average|DTU gebruikt. Is van toepassing op DTU-gebaseerde data bases.|Geen|
|cpu_limit|CPU-limiet|Aantal|Average|CPU-limiet. Is van toepassing op vCore-data bases.|Geen|
|cpu_used|CPU gebruikt|Aantal|Average|CPU gebruikt. Is van toepassing op vCore-data bases.|Geen|
|dwu_limit|Limiet voor DWU|Aantal|Maximum|DWU-limiet. Is alleen van toepassing op data warehouses.|Geen|
|dwu_consumption_percent|Percentage DWU|Percentage|Maximum|DWU-percentage. Is alleen van toepassing op data warehouses.|Geen|
|dwu_used|DWU gebruikt|Aantal|Maximum|DWU gebruikt. Is alleen van toepassing op data warehouses.|Geen|
|cache_hit_percent|Percentage cache treffers|Percentage|Maximum|Percentage van cache treffer. Is alleen van toepassing op data warehouses.|Geen|
|cache_used_percent|Percentage gebruikt cache|Percentage|Maximum|Percentage gebruikt cache. Is alleen van toepassing op data warehouses.|Geen|
|sqlserver_process_core_percent<sup>1</sup> |Kern percentage van SQL Server proces|Percentage|Maximum|Het CPU-gebruiks percentage voor het SQL Server proces, zoals gemeten door het besturings systeem.|Geen|
|sqlserver_process_memory_percent<sup>1</sup> |Percentage proces geheugen SQL Server|Percentage|Maximum|Het percentage geheugen gebruik voor het SQL Server proces, zoals gemeten door het besturings systeem.|Geen|
|tempdb_data_size<sup>2</sup> |Data File grootte van tempdb|Aantal|Maximum|Data File grootte van tempdb-gegevens bestanden.|Geen|
|tempdb_log_size<sup>2</sup> |Grootte van logboek bestanden tempdb|Aantal|Maximum|Grootte van KB-logboek bestanden.|Geen|
|tempdb_log_used_percent<sup>2</sup> |Percentage gebruikt TempDB-logboek|Percentage|Maximum|Percentage gebruikt TempDB-logboek.|Geen|
|local_tempdb_usage_percent|Lokaal TempDB-percentage|Percentage|Average|Lokaal TempDB-percentage. Is alleen van toepassing op data warehouses.|Geen|
|app_cpu_billed|App CPU gefactureerd|Aantal|Totaal|App CPU gefactureerd. Is van toepassing op serverloze data bases.|Geen|
|app_cpu_percent|CPU-percentage van app|Percentage|Average|CPU-percentage van de app. Is van toepassing op serverloze data bases.|Geen|
|app_memory_percent|Percentage app-geheugen|Percentage|Average|Percentage app-geheugen. Is van toepassing op serverloze data bases.|Geen|
|allocated_data_storage|Toegewezen gegevens ruimte|Bytes|Average|Toegewezen gegevens opslag. Niet van toepassing op data warehouses.|Geen|
|memory_usage_percent|Geheugen percentage|Percentage|Maximum|Geheugen percentage. Is alleen van toepassing op data warehouses.|Geen|
|dw_backup_size_gb|Grootte van gegevens opslag|Aantal|Totaal|De grootte van de gegevens opslag bestaat uit de grootte van uw gegevens en het transactie logboek. De metrische gegevens worden geteld bij het gedeelte opslag van uw factuur. Is alleen van toepassing op data warehouses.|Geen|
|dw_snapshot_size_gb|Opslag grootte van moment opname|Aantal|Totaal|Opslag grootte van de moment opname is de grootte van de incrementele wijzigingen die door moment opnamen worden vastgelegd om door de gebruiker gedefinieerde en automatische herstel punten te maken. De metrische gegevens worden geteld bij het gedeelte opslag van uw factuur. Is alleen van toepassing op data warehouses.|Geen|
|dw_geosnapshot_size_gb|Opslag grootte voor nood herstel|Aantal|Totaal|Opslag voor herstel na nood gevallen wordt weer gegeven als ' nood herstel opslag ' in uw factuur. Is alleen van toepassing op data warehouses.|Geen|
|wlg_allocation_relative_to_system_percent|Toewijzing van werkbelasting groep per systeem percentage|Percentage|Maximum|Toegewezen percentage resources ten opzichte van het hele systeem per werkbelasting groep. Is alleen van toepassing op data warehouses.|WorkloadGroupName,IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Toewijzing van werkbelasting groep per cap-resource percentage|Percentage|Maximum|Toegewezen percentage resources ten opzichte van de opgegeven Cap-resources per werkbelasting groep. Is alleen van toepassing op data warehouses.|WorkloadGroupName,IsUserDefined|
|wlg_active_queries|Actieve query's voor werkbelasting groep|Aantal|Totaal|Actieve query's binnen de werkbelasting groep. Is alleen van toepassing op data warehouses.|WorkloadGroupName,IsUserDefined|
|wlg_queued_queries|Werkbelasting groep in wachtrij geplaatste query's|Aantal|Totaal|Query's in de wachtrij in de werkbelasting groep. Is alleen van toepassing op data warehouses.|WorkloadGroupName,IsUserDefined|
|active_queries|Actieve query's|Aantal|Totaal|Actieve query's over alle werkbelasting groepen. Is alleen van toepassing op data warehouses.|Geen|
|queued_queries|Query's in de wachtrij|Aantal|Totaal|Query's in de wachtrij voor alle werkbelasting groepen. Is alleen van toepassing op data warehouses.|Geen|
|wlg_active_queries_timeouts|Time-outs query werkbelasting groep|Aantal|Totaal|Query's waarvoor een time-out is opgetreden voor de werkbelasting groep. Is alleen van toepassing op data warehouses.|WorkloadGroupName,IsUserDefined|
|wlg_effective_min_resource_percent|Effectief min resource percentage|Percentage|Maximum|Mini maal percentage resources dat voor de werkbelasting groep is gereserveerd en geïsoleerd, rekening houdend met het minimum service niveau. Is alleen van toepassing op data warehouses.|WorkloadGroupName,IsUserDefined|
|wlg_effective_cap_resource_percent|Effectief cap-resource percentage|Percentage|Maximum|Een vaste limiet voor het percentage resources dat is toegestaan voor de werkbelasting groep, waarbij rekening wordt gehouden met een effectief min resource percentage dat is toegewezen aan andere werkbelasting groepen. Is alleen van toepassing op data warehouses.|WorkloadGroupName,IsUserDefined|
|full_backup_size_bytes|Opslag grootte voor volledige back-up|Bytes|Maximum|Cumulatieve opslag grootte voor volledige back-ups. Is van toepassing op vCore-data bases. Niet van toepassing op grootschalige-data bases.|Geen|
|diff_backup_size_bytes|Grootte van differentiële back-upopslag|Bytes|Maximum|Cumulatieve differentiële back-upopslag. Is van toepassing op vCore-data bases. Niet van toepassing op grootschalige-data bases.|Geen|
|log_backup_size_bytes|Opslag grootte van logboek back-up|Bytes|Maximum|Cumulatieve opslag grootte van logboek back-up. Is van toepassing op vCore-en grootschalige-data bases.|Geen|
|snapshot_backup_size_bytes|Grootte van back-upopslag voor moment opname|Bytes|Maximum|Grootte van cumulatieve back-upopslag voor moment opname. Is van toepassing op grootschalige-data bases.|Geen|
|base_blob_size_bytes|Basis grootte van Blob-opslag|Bytes|Maximum|Basis grootte van Blob-opslag. Is van toepassing op grootschalige-data bases.|Geen|

<sup>1</sup> deze metriek is beschikbaar voor data bases met behulp van het vCore-aankoop model met 2 vCores en hoger, of 200 DTU en hoger voor op DTU gebaseerde aankoop modellen. 

<sup>2</sup> deze metriek is beschikbaar voor data bases met behulp van het vCore-aankoop model met 2 vCores en hoger, of 200 DTU en hoger voor op DTU gebaseerde aankoop modellen. Deze metriek is momenteel niet beschikbaar voor grootschalige-data bases of data warehouses.

## <a name="microsoftsqlserverselasticpools"></a>Micro soft. SQL/servers/elasticPools

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Percentage|Average|CPU-percentage|Geen|
|database_cpu_percent|CPU-percentage|Percentage|Average|CPU-percentage|DatabaseResourceId|
|physical_data_read_percent|Gegevens-I/O-percentage|Percentage|Average|Gegevens-I/O-percentage|Geen|
|database_physical_data_read_percent|Gegevens-I/O-percentage|Percentage|Average|Gegevens-I/O-percentage|DatabaseResourceId|
|log_write_percent|Logboek-IO-percentage|Percentage|Average|Logboek-IO-percentage|Geen|
|database_log_write_percent|Logboek-IO-percentage|Percentage|Average|Logboek-IO-percentage|DatabaseResourceId|
|dtu_consumption_percent|DTU-percentage|Percentage|Average|DTU-percentage. Is van toepassing op op DTU gebaseerde elastische Pools.|Geen|
|database_dtu_consumption_percent|DTU-percentage|Percentage|Average|DTU-percentage|DatabaseResourceId|
|storage_percent|Percentage gebruikte gegevens ruimte|Percentage|Average|Percentage gebruikte gegevens ruimte|Geen|
|workers_percent|Percentage werk nemers|Percentage|Average|Percentage werk nemers|Geen|
|database_workers_percent|Percentage werk nemers|Percentage|Average|Percentage werk nemers|DatabaseResourceId|
|sessions_percent|Percentage sessies|Percentage|Average|Percentage sessies|Geen|
|database_sessions_percent|Percentage sessies|Percentage|Average|Percentage sessies|DatabaseResourceId|
|eDTU_limit|eDTU-limiet|Aantal|Average|eDTU-limiet. Is van toepassing op op DTU gebaseerde elastische Pools.|Geen|
|storage_limit|Maximale grootte van gegevens|Bytes|Average|Maximale grootte van gegevens|Geen|
|eDTU_used|eDTU gebruikt|Aantal|Average|eDTU gebruikt. Is van toepassing op op DTU gebaseerde elastische Pools.|Geen|
|database_eDTU_used|eDTU gebruikt|Aantal|Average|eDTU gebruikt|DatabaseResourceId|
|storage_used|Gebruikte gegevens ruimte|Bytes|Average|Gebruikte gegevens ruimte|Geen|
|database_storage_used|Gebruikte gegevens ruimte|Bytes|Average|Gebruikte gegevens ruimte|DatabaseResourceId|
|xtp_storage_percent|Percentage OLTP-opslag in het geheugen|Percentage|Average|Percentage OLTP-opslag in het geheugen|Geen|
|cpu_limit|CPU-limiet|Aantal|Average|CPU-limiet. Is van toepassing op op vCore gebaseerde elastische Pools.|Geen|
|database_cpu_limit|CPU-limiet|Aantal|Average|CPU-limiet|DatabaseResourceId|
|cpu_used|CPU gebruikt|Aantal|Average|CPU gebruikt. Is van toepassing op op vCore gebaseerde elastische Pools.|Geen|
|database_cpu_used|CPU gebruikt|Aantal|Average|CPU gebruikt|DatabaseResourceId|
|sqlserver_process_core_percent<sup>1</sup>|Kern percentage van SQL Server proces|Percentage|Maximum|Het CPU-gebruiks percentage voor het SQL Server proces, zoals gemeten door het besturings systeem. Is van toepassing op elastische Pools. |Geen|
|sqlserver_process_memory_percent<sup>1</sup>|Percentage proces geheugen SQL Server|Percentage|Maximum|Het percentage geheugen gebruik voor het SQL Server proces, zoals gemeten door het besturings systeem. Is van toepassing op elastische Pools. |Geen|
|tempdb_data_size<sup>2</sup>|Data File grootte van tempdb|Aantal|Maximum|Data File grootte van tempdb-gegevens bestanden.|Geen|
|tempdb_log_size<sup>2</sup>|Grootte van logboek bestanden tempdb|Aantal|Maximum|Grootte van KB-logboek bestanden. |Geen|
|tempdb_log_used_percent<sup>2</sup>|Percentage gebruikt TempDB-logboek|Percentage|Maximum|Percentage gebruikt TempDB-logboek.|Geen|
|allocated_data_storage|Toegewezen gegevens ruimte|Bytes|Average|Toegewezen gegevens ruimte|Geen|
|database_allocated_data_storage|Toegewezen gegevens ruimte|Bytes|Average|Toegewezen gegevens ruimte|DatabaseResourceId|
|allocated_data_storage_percent|Percentage toegewezen gegevens ruimte|Percentage|Maximum|Percentage toegewezen gegevens ruimte|Geen|

<sup>1</sup> deze metriek is beschikbaar voor data bases met behulp van het vCore-aankoop model met 2 vCores en hoger, of 200 DTU en hoger voor op DTU gebaseerde aankoop modellen. 

<sup>2</sup> deze metriek is beschikbaar voor data bases met behulp van het vCore-aankoop model met 2 vCores en hoger, of 200 DTU en hoger voor op DTU gebaseerde aankoop modellen. Deze metriek is momenteel niet beschikbaar voor grootschalige-data bases.


## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|dtu_consumption_percent|DTU-percentage|Percentage|Average|DTU-percentage|ElasticPoolResourceId|
|database_dtu_consumption_percent|DTU-percentage|Percentage|Average|DTU-percentage|DatabaseResourceId,ElasticPoolResourceId|
|storage_used|Gebruikte gegevens ruimte|Bytes|Average|Gebruikte gegevens ruimte|ElasticPoolResourceId|
|database_storage_used|Gebruikte gegevens ruimte|Bytes|Average|Gebruikte gegevens ruimte|DatabaseResourceId,ElasticPoolResourceId|
|dtu_used|DTU gebruikt|Aantal|Average|DTU gebruikt|DatabaseResourceId|

## <a name="microsoftsqlmanagedinstances"></a>Micro soft. SQL/managedInstances

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|virtual_core_count|Aantal virtuele kernen|Aantal|Average|Aantal virtuele kernen|Geen|
|avg_cpu_percent|Gemiddeld CPU-percentage|Percentage|Average|Gemiddeld CPU-percentage|Geen|
|reserved_storage_mb|Gereserveerde opslag ruimte|Aantal|Average|Gereserveerde opslag ruimte|Geen|
|storage_space_used_mb|Gebruikte opslag ruimte|Aantal|Average|Gebruikte opslag ruimte|Geen|
|io_requests|Aantal i/o-aanvragen|Aantal|Average|Aantal i/o-aanvragen|Geen|
|io_bytes_read|Gelezen IO-bytes|Bytes|Average|Gelezen IO-bytes|Geen|
|io_bytes_written|Geschreven IO-bytes|Bytes|Average|Geschreven IO-bytes|Geen|



## <a name="microsoftstoragestorageaccounts"></a>Micro soft. Storage/Storage accounts

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|UsedCapacity|Gebruikte capaciteit|Bytes|Average|Gebruikte capaciteit van account|Geen|
|Transacties|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de dimensie ResponseType voor het aantal verschillende typen reacties.|ResponseType, geotype, ApiName, authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid ingangs gegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgangs gegevens, in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie|
|SuccessServerLatency|Geslaagde serverlatentie|Milliseconden|Average|De gemiddelde latentie die door Azure Storage wordt gebruikt voor het verwerken van een geslaagde aanvraag, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|Geotype, ApiName, authenticatie|
|SuccessE2ELatency|Success E2E Latency|Milliseconden|Average|De gemiddelde end-to-end-latentie van geslaagde aanvragen voor een opslag service of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie|
|Beschikbaarheid|Beschikbaarheid|Percentage|Average|Het percentage Beschik baarheid voor de opslag service of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Micro soft. Storage/Storage accounts/blobServices

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BlobCapacity|BLOB-capaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de Blob service van het opslag account in bytes.|BlobType, Tier|
|BlobCount|Aantal blobs|Aantal|Average|Het aantal blobs in de Blob service van het opslag account.|BlobType, Tier|
|ContainerCount|Aantal BLOB-containers|Aantal|Average|Het aantal containers in het Blob service van het opslag account.|Geen|
|IndexCapacity|Index capaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de index van de ADLS Gen2 (hiërarchisch) in bytes.|Geen|
|Transacties|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de dimensie ResponseType voor het aantal verschillende typen reacties.|ResponseType, geotype, ApiName, authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid ingangs gegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgangs gegevens, in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie|
|SuccessServerLatency|Geslaagde serverlatentie|Milliseconden|Average|De gemiddelde latentie die door Azure Storage wordt gebruikt voor het verwerken van een geslaagde aanvraag, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|Geotype, ApiName, authenticatie|
|SuccessE2ELatency|Success E2E Latency|Milliseconden|Average|De gemiddelde end-to-end-latentie van geslaagde aanvragen voor een opslag service of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie|
|Beschikbaarheid|Beschikbaarheid|Percentage|Average|Het percentage Beschik baarheid voor de opslag service of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie|

## <a name="microsoftstoragestorageaccountstableservices"></a>Micro soft. Storage/Storage accounts/tableServices

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TableCapacity|Tabel capaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de Table service van het opslag account in bytes.|Geen|
|TableCount|Aantal tabellen|Aantal|Average|Het aantal tabellen in de Table service van het opslag account.|Geen|
|TableEntityCount|Aantal tabel entiteiten|Aantal|Average|Het aantal tabel entiteiten in de Table service van het opslag account.|Geen|
|Transacties|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de dimensie ResponseType voor het aantal verschillende typen reacties.|ResponseType, geotype, ApiName, authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid ingangs gegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgangs gegevens, in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie|
|SuccessServerLatency|Geslaagde serverlatentie|Milliseconden|Average|De gemiddelde latentie die door Azure Storage wordt gebruikt voor het verwerken van een geslaagde aanvraag, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|Geotype, ApiName, authenticatie|
|SuccessE2ELatency|Success E2E Latency|Milliseconden|Average|De gemiddelde end-to-end-latentie van geslaagde aanvragen voor een opslag service of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie|
|Beschikbaarheid|Beschikbaarheid|Percentage|Average|Het percentage Beschik baarheid voor de opslag service of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Micro soft. Storage/Storage accounts/fileServices

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|FileCapacity|Bestands capaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de bestands service van het opslag account in bytes.|Bestands share|
|FileCount|Aantal bestanden|Aantal|Average|Het aantal bestanden in de file-service van het opslag account.|Bestands share|
|FileShareCount|Aantal bestands shares|Aantal|Average|Het aantal bestands shares in de file-service van het opslag account.|Geen|
|FileShareSnapshotCount|Aantal moment opnamen van bestands shares|Aantal|Average|Het aantal moment opnamen dat aanwezig is op de share in de bestanden service van het opslag account.|Bestands share|
|FileShareSnapshotSize|Grootte van moment opname van bestands share|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de moment opnamen in de bestands service van het opslag account in bytes.|Bestands share|
|FileShareQuota|Quota grootte van bestands share|Bytes|Average|De bovengrens voor de hoeveelheid opslag die kan worden gebruikt door Azure Files service in bytes.|Bestands share|
|Transacties|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de dimensie ResponseType voor het aantal verschillende typen reacties.|ResponseType, geotype, ApiName, Authentication, file share|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid ingangs gegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie, bestands share|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgangs gegevens, in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie, bestands share|
|SuccessServerLatency|Geslaagde serverlatentie|Milliseconden|Average|De gemiddelde latentie die door Azure Storage wordt gebruikt voor het verwerken van een geslaagde aanvraag, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|Geotype, ApiName, authenticatie, bestands share|
|SuccessE2ELatency|Success E2E Latency|Milliseconden|Average|De gemiddelde end-to-end-latentie van geslaagde aanvragen voor een opslag service of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie, bestands share|
|Beschikbaarheid|Beschikbaarheid|Percentage|Average|Het percentage Beschik baarheid voor de opslag service of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie, bestands share|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Micro soft. Storage/Storage accounts/queueServices

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|QueueCapacity|Wachtrij capaciteit|Bytes|Average|De hoeveelheid opslag die wordt gebruikt door de Queue-service van het opslag account in bytes.|Geen|
|QueueCount|Aantal wachtrijen|Aantal|Average|Het aantal wacht rijen in de Queue-service van het opslag account.|Geen|
|QueueMessageCount|Aantal wachtrij berichten|Aantal|Average|Het geschatte aantal wachtrij berichten in de Queue-service van het opslag account.|Geen|
|Transacties|Transacties|Aantal|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. Gebruik de dimensie ResponseType voor het aantal verschillende typen reacties.|ResponseType, geotype, ApiName, authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid ingangs gegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype, ApiName, authenticatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgangs gegevens, in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype, ApiName, authenticatie|
|SuccessServerLatency|Geslaagde serverlatentie|Milliseconden|Average|De gemiddelde latentie die door Azure Storage wordt gebruikt voor het verwerken van een geslaagde aanvraag, in milliseconden. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|Geotype, ApiName, authenticatie|
|SuccessE2ELatency|Success E2E Latency|Milliseconden|Average|De gemiddelde end-to-end-latentie van geslaagde aanvragen voor een opslag service of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype, ApiName, authenticatie|
|Beschikbaarheid|Beschikbaarheid|Percentage|Average|Het percentage Beschik baarheid voor de opslag service of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype, ApiName, authenticatie|





## <a name="microsoftstoragecachecaches"></a>Micro soft. StorageCache/caches

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ClientIOPS|Totaal aantal IOPS client|Aantal|Average|Het aantal client bestands bewerkingen dat is verwerkt door de cache.|Geen|
|ClientLatency|Gemiddelde client latentie|Milliseconden|Average|Gemiddelde latentie van client bestands bewerkingen naar de opslag cache.|Geen|
|ClientReadIOPS|Door client gelezen IOPS|CountPerSecond|Average|Lees bewerkingen van de client per seconde.|Geen|
|ClientReadThroughput|Gemiddelde doorvoer snelheid van cache Lees bewerking|BytesPerSecond|Average|Overdrachts frequentie van gelezen gegevens van de client.|Geen|
|ClientWriteIOPS|Client schrijf-IOPS|CountPerSecond|Average|Schrijf bewerkingen van de client per seconde.|Geen|
|ClientWriteThroughput|Gemiddelde doorvoer snelheid van cache schrijf bewerkingen|BytesPerSecond|Average|Overdrachts frequentie van de gegevens van de client.|Geen|
|ClientMetadataReadIOPS|IOPS voor lezen van meta gegevens van client|CountPerSecond|Average|Het aantal client bestands bewerkingen dat naar de cache wordt verzonden, exclusief gegevens Lees bewerkingen, waarbij de permanente status niet wordt gewijzigd.|Geen|
|ClientMetadataWriteIOPS|IOPS voor schrijven van meta gegevens van client|CountPerSecond|Average|Het aantal client bestands bewerkingen dat naar de cache wordt verzonden, met uitzonde ring van gegevens schrijf bewerkingen, waardoor de permanente status wordt gewijzigd.|Geen|
|ClientLockIOPS|IOPS-client vergrendeling|CountPerSecond|Average|Vergrendelings bewerkingen voor client bestanden per seconde.|Geen|
|StorageTargetHealth|Status van opslag doel|Aantal|Average|Booleaanse resultaten van connectiviteits test tussen de cache-en opslag doelen.|Geen|
|Systeem|Systeem|Aantal|Average|Booleaanse resultaten van connectiviteits test tussen het cache-en bewakings systeem.|Geen|
|StorageTargetIOPS|Totale aantal StorageTarget IOPS|Aantal|Average|De frequentie van alle bestands bewerkingen die de cache verzendt naar een bepaalde StorageTarget.|StorageTarget|
|StorageTargetWriteIOPS|StorageTarget write IOPS|Aantal|Average|Het aantal schrijf bewerkingen voor bestanden dat door de cache wordt verzonden naar een bepaalde StorageTarget.|StorageTarget|
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

## <a name="microsoftstoragesyncstoragesyncservices"></a>micro soft. storagesync/storageSyncServices

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ServerSyncSessionResult|Resultaat van synchronisatie sessie|Aantal|Average|Metriek die een waarde van 1 registreert telkens wanneer het server eindpunt een synchronisatie sessie met het Cloud eindpunt heeft voltooid|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Gesynchroniseerde bestanden|Aantal|Totaal|Aantal gesynchroniseerde bestanden|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Bestanden die niet worden gesynchroniseerd|Aantal|Totaal|Aantal bestanden dat niet kan worden gesynchroniseerd|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncBatchTransferredFileBytes|Gesynchroniseerde bytes|Bytes|Totaal|Totale bestands grootte die is overgedragen voor synchronisatie sessies|SyncGroupName,ServerEndpointName,SyncDirection|
|StorageSyncServerHeartbeat|Online status van de server|Aantal|Maximum|Metriek die een waarde van 1 registreert telkens wanneer de resigtered-server een heartbeat met het Cloud eindpunt heeft geregistreerd|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Cloud lagen intrekken|Bytes|Totaal|De totale grootte van de gegevens die door de server zijn ingetrokken|ServerName|
|StorageSyncRecalledTotalNetworkBytes|Grootte van intrekken Cloud lagen|Bytes|Totaal|Grootte van gegevens die zijn ingetrokken|SyncGroupName, servername|
|StorageSyncRecallThroughputBytesPerSecond|Door Voer van Cloud lagen intrekken|BytesPerSecond|Average|Grootte van gegevens intrekken door Voer|SyncGroupName, servername|
|StorageSyncRecalledNetworkBytesByApplication|Grootte van intrekken van Cloud lagen op toepassing|Bytes|Totaal|Grootte van gegevens die door de toepassing zijn ingetrokken|SyncGroupName, servername, ApplicationName|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>micro soft. storagesync/storageSyncServices/syncGroups

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|Gesynchroniseerde bestanden|Aantal|Totaal|Aantal gesynchroniseerde bestanden|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Bestanden die niet worden gesynchroniseerd|Aantal|Totaal|Aantal bestanden dat niet kan worden gesynchroniseerd|SyncGroupName,ServerEndpointName,SyncDirection|
|SyncGroupBatchTransferredFileBytes|Gesynchroniseerde bytes|Bytes|Totaal|Totale bestands grootte die is overgedragen voor synchronisatie sessies|SyncGroupName,ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>micro soft. storagesync/storageSyncServices/syncGroups/serverEndpoints

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|Gesynchroniseerde bestanden|Aantal|Totaal|Aantal gesynchroniseerde bestanden|ServerEndpointName,SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Bestanden die niet worden gesynchroniseerd|Aantal|Totaal|Aantal bestanden dat niet kan worden gesynchroniseerd|ServerEndpointName,SyncDirection|
|ServerEndpointBatchTransferredFileBytes|Gesynchroniseerde bytes|Bytes|Totaal|Totale bestands grootte die is overgedragen voor synchronisatie sessies|ServerEndpointName,SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>micro soft. storagesync/storageSyncServices/registeredServer

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ServerHeartbeat|Online status van de server|Aantal|Maximum|Metriek die een waarde van 1 registreert telkens wanneer de resigtered-server een heartbeat met het Cloud eindpunt heeft geregistreerd|ServerResourceId, servername|
|ServerRecallIOTotalSizeBytes|Cloud lagen intrekken|Bytes|Totaal|De totale grootte van de gegevens die door de server zijn ingetrokken|ServerResourceId, servername|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Micro soft. StreamAnalytics/streamingjobs

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ResourceUtilization|% Gebruik|Percentage|Maximum|% Gebruik|Logischenaam, PartitionId|
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

## <a name="microsoftsynapseworkspaces"></a>Micro soft. Synapse/werk ruimten

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|OrchestrationPipelineRunsEnded|Beëindigde pijplijn uitvoeringen|Aantal|Totaal|Aantal uitvoeringen van de Orchestrator-pipeline die geslaagd, mislukt of geannuleerd zijn|Resultaat, FailureType, pijp lijn|
|OrchestrationActivityRunsEnded|Uitvoering van activiteit beëindigd|Aantal|Totaal|Aantal Orchestration-activiteiten dat is geslaagd, mislukt of geannuleerd|Resultaat, FailureType, activiteit, activity type, pijp lijn|
|OrchestrationTriggersEnded|Beëindigde triggers|Aantal|Totaal|Aantal Orchestrator-triggers dat is geslaagd, mislukt of geannuleerd|Resultaat, FailureType, trigger|
|SQLOnDemandLoginAttempts|Aanmeldings pogingen|Aantal|Totaal|Aantal aanmeldings pogingen dat is voltooid of mislukt|Resultaat|
|SQLOnDemandQueriesEnded|Beëindigde query's|Aantal|Totaal|Aantal query's dat is geslaagd, mislukt of geannuleerd|Resultaat|
|SQLOnDemandQueryProcessedBytes|Verwerkte gegevens|Bytes|Totaal|Hoeveelheid gegevens die wordt verwerkt door query's|Geen|

## <a name="microsoftsynapseworkspacesbigdatapools"></a>Micro soft. Synapse/werk ruimten/bigDataPools

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SparkJobsEnded|Beëindigde toepassingen|Aantal|Totaal|Aantal beëindigde toepassingen|Taak type, JobResult|
|CoresCapacity|Capaciteit van kern geheugens|Aantal|Maximum|Capaciteit van kern geheugens|Geen|
|MemoryCapacityGB|Geheugen capaciteit (GB)|Aantal|Maximum|Geheugen capaciteit (GB)|Geen|

## <a name="microsoftsynapseworkspacessqlpools"></a>Micro soft. Synapse/werk ruimten/sqlPools

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|DWULimit|Limiet voor DWU|Aantal|Maximum|Serviceniveau doelstelling van de SQL-groep|Geen|
|DWUUsed|DWU gebruikt|Aantal|Maximum|Vertegenwoordigt een weer gave op hoog niveau van het gebruik in de SQL-groep. Gemeten met DWU-limiet * DWU percentage|Geen|
|DWUUsedPercent|Percentage gebruikt DWU|Percentage|Maximum|Vertegenwoordigt een weer gave op hoog niveau van het gebruik in de SQL-groep. Gemeten door het maximum te nemen tussen het CPU-percentage en het IO-percentage voor gegevens|Geen|
|ConnectionsBlockedByFirewall|Verbindingen geblokkeerd door Firewall|Aantal|Totaal|Het aantal verbindingen dat wordt geblokkeerd door firewall regels. Ga naar het toegangs beheer beleid voor uw SQL-groep en controleer deze verbindingen als het aantal hoog is|Geen|
|AdaptiveCacheHitPercent|Treffer percentage van adaptieve cache|Percentage|Maximum|Meet hoe goed werk belastingen gebruikmaken van de adaptieve cache. Gebruik deze metriek met de metrische gegevens in het cache geheugen om te bepalen of u een extra capaciteit wilt schalen of de werk belasting opnieuw wilt uitvoeren om de cache te hydraten|Geen|
|AdaptiveCacheUsedPercent|Percentage van adaptief cache gebruik|Percentage|Maximum|Meet hoe goed werk belastingen gebruikmaken van de adaptieve cache. Gebruik deze metriek met de waarde voor het gebruikte percentage van het cache geheugen om te bepalen of u wilt schalen op extra capaciteit of werk belastingen opnieuw wilt uitvoeren voor de cache|Geen|
|LocalTempDBUsedPercent|Percentage lokaal gebruikte tempdb|Percentage|Maximum|Lokaal TempDB-gebruik voor alle reken knooppunten: de waarden worden elke vijf minuten verzonden|Geen|
|MemoryUsedPercent|Percentage gebruikt geheugen|Percentage|Maximum|Geheugen gebruik op alle knoop punten in de SQL-groep|Geen|
|Verbindingen|Verbindingen|Aantal|Totaal|Totaal aantal aanmeldingen bij de SQL-groep|Resultaat|
|WLGActiveQueries|Actieve query's voor werkbelasting groep|Aantal|Totaal|De actieve query's binnen de werkbelasting groep. Als u deze metrische waarde onfilterd en ongesplitst gebruikt, worden alle actieve query's weer gegeven die worden uitgevoerd op het systeem|IsUserDefined,WorkloadGroup|
|WLGActiveQueriesTimeouts|Time-outs query werkbelasting groep|Aantal|Totaal|Query's voor de werkbelasting groep waarvoor een time-out is opgetreden. Querytime-time-outs die door deze metrische gegevens worden gerapporteerd, worden slechts eenmaal gestart nadat de query is uitgevoerd (de wacht tijd is niet opgenomen als gevolg van vergren deling of wachten op een resource)|IsUserDefined,WorkloadGroup|
|WLGAllocationBySystemPercent|Toewijzing van werkbelasting groep per systeem percentage|Percentage|Maximum|Het percentage toewijzing van resources ten opzichte van het hele systeem|IsUserDefined,WorkloadGroup|
|WLGAllocationByMaxResourcePercent|Toewijzing van werkbelasting groep op Maxi maal resource percentage|Percentage|Maximum|Geeft de percentage toewijzing van resources ten opzichte van het resource percentage van de effectief cap per werkbelasting groep. Deze metrische gegevens bieden het effectief gebruik van de werkbelasting groep|IsUserDefined,WorkloadGroup|
|WLGEffectiveCapResourcePercent|Effectief cap-resource percentage|Percentage|Maximum|Het werkelijke Cap-resource percentage voor de werkbelasting groep. Als er andere werkbelasting groepen zijn met min_percentage_resource > 0, wordt de effective_cap_percentage_resource proportioneel verlaagd|IsUserDefined,WorkloadGroup|
|wlg_effective_min_resource_percent|Effectief min resource percentage|Percentage|Minimum|De instelling van het werkelijke minimale resource percentage dat is toegestaan voor het beschouwen van het service niveau en de instellingen van de werkbelasting groep. De effectief min_percentage_resource kan hoger worden aangepast op lagere service niveaus|IsUserDefined,WorkloadGroup|
|WLGQueuedQueries|Werkbelasting groep in wachtrij geplaatste query's|Aantal|Totaal|Cumulatief aantal aanvragen dat in de wachtrij is geplaatst nadat de limiet voor het maximum aantal gelijktijdig is bereikt|IsUserDefined,WorkloadGroup|

## <a name="microsofttimeseriesinsightsenvironments"></a>Micro soft. TimeSeriesInsights/omgevingen

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ontvangen berichten met ingang|Aantal|Totaal|Telling van berichten die zijn gelezen uit alle gebeurtenis bronnen van Event hub of IoT hub|Geen|
|IngressReceivedInvalidMessages|Ongeldige berichten ontvangen|Aantal|Totaal|Aantal ongeldige berichten gelezen uit alle gebeurtenis bronnen van Event hub of IoT hub|Geen|
|IngressReceivedBytes|Ontvangen bytes van binnenkomend verkeer|Bytes|Totaal|Het aantal gelezen bytes van alle gebeurtenis bronnen|Geen|
|IngressStoredBytes|In ingangs opgeslagen bytes|Bytes|Totaal|De totale grootte van de gebeurtenissen die zijn verwerkt en beschikbaar voor de query|Geen|
|IngressStoredEvents|Opgeslagen gebeurtenissen in ingangs|Aantal|Totaal|Aantal samengevoegde gebeurtenissen dat is verwerkt en beschikbaar is voor query|Geen|
|IngressReceivedMessagesTimeLag|Tijds vertraging ontvangen inkomende berichten|Seconden|Maximum|Verschil tussen het tijdstip waarop het bericht in de bron van de gebeurtenis in de wachtrij staat en de tijd die wordt verwerkt in ingangs|Geen|
|IngressReceivedMessagesCountLag|Vertraging ontvangen inkomende berichten van ingang|Aantal|Average|Verschil tussen het Volg nummer van de laatste bericht in de bron partitie en het Volg nummer van de berichten die worden verwerkt in ingangs gebeurtenissen|Geen|
|WarmStorageMaxProperties|Maximale eigenschappen van warme opslag|Aantal|Maximum|Maximum aantal eigenschappen dat is toegestaan door de omgeving voor de SKU van S1/S2 en het maximum aantal eigenschappen dat is toegestaan door de warme Store voor PAYG SKU|Geen|
|WarmStorageUsedProperties|Eigenschappen voor warme opslag gebruikt |Aantal|Maximum|Aantal eigenschappen dat wordt gebruikt door de omgeving voor de SKU van S1/S2 en het aantal eigenschappen dat door warme Store voor PAYG SKU wordt gebruikt|Geen|



## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Micro soft. TimeSeriesInsights/omgevingen/eventsources

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ontvangen berichten met ingang|Aantal|Totaal|Aantal berichten dat uit de gebeurtenis bron is gelezen|Geen|
|IngressReceivedInvalidMessages|Ongeldige berichten ontvangen|Aantal|Totaal|Aantal ongeldige berichten gelezen uit de gebeurtenis bron|Geen|
|IngressReceivedBytes|Ontvangen bytes van binnenkomend verkeer|Bytes|Totaal|Aantal gelezen bytes van de gebeurtenis bron|Geen|
|IngressStoredBytes|In ingangs opgeslagen bytes|Bytes|Totaal|De totale grootte van de gebeurtenissen die zijn verwerkt en beschikbaar voor de query|Geen|
|IngressStoredEvents|Opgeslagen gebeurtenissen in ingangs|Aantal|Totaal|Aantal samengevoegde gebeurtenissen dat is verwerkt en beschikbaar is voor query|Geen|
|IngressReceivedMessagesTimeLag|Tijds vertraging ontvangen inkomende berichten|Seconden|Maximum|Verschil tussen het tijdstip waarop het bericht in de bron van de gebeurtenis in de wachtrij staat en de tijd die wordt verwerkt in ingangs|Geen|
|IngressReceivedMessagesCountLag|Vertraging ontvangen inkomende berichten van ingang|Aantal|Average|Verschil tussen het Volg nummer van de laatste bericht in de bron partitie en het Volg nummer van de berichten die worden verwerkt in ingangs gebeurtenissen|Geen|
|WarmStorageMaxProperties|Maximale eigenschappen van warme opslag|Aantal|Maximum|Maximum aantal eigenschappen dat is toegestaan door de omgeving voor de SKU van S1/S2 en het maximum aantal eigenschappen dat is toegestaan door de warme Store voor PAYG SKU|Geen|
|WarmStorageUsedProperties|Eigenschappen voor warme opslag gebruikt |Aantal|Maximum|Aantal eigenschappen dat wordt gebruikt door de omgeving voor de SKU van S1/S2 en het aantal eigenschappen dat door warme Store voor PAYG SKU wordt gebruikt|Geen|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Micro soft. VMwareCloudSimple/informatie

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|DiskReadBytesPerSecond|Gelezen bytes per seconde|BytesPerSecond|Average|Gemiddelde door Voer van schijf vanwege Lees bewerkingen gedurende de voorbeeld periode.|Geen|
|DiskWriteBytesPerSecond|Geschreven bytes per seconde|BytesPerSecond|Average|Gemiddelde doorvoer snelheid van schijven vanwege schrijf bewerkingen gedurende de voorbeeld periode.|Geen|
|Gelezen bytes op de schijf|Gelezen bytes op de schijf|Bytes|Totaal|Totale schijf doorvoer vanwege Lees bewerkingen gedurende de voorbeeld periode.|Geen|
|Geschreven bytes op de schijf|Geschreven bytes op de schijf|Bytes|Totaal|Totale schijf doorvoer vanwege schrijf bewerkingen gedurende de voorbeeld periode.|Geen|
|DiskReadOperations|Lees bewerkingen op de schijf|Aantal|Totaal|Het aantal i/o-Lees bewerkingen in de vorige voorbeeld periode. Houd er rekening mee dat deze bewerkingen variabele grootte kunnen hebben.|Geen|
|DiskWriteOperations|Schrijf bewerkingen op de schijf|Aantal|Totaal|Het aantal i/o-schrijf bewerkingen in de vorige voorbeeld periode. Houd er rekening mee dat deze bewerkingen variabele grootte kunnen hebben.|Geen|
|Lees bewerkingen op de schijf per seconde|Lees bewerkingen op de schijf per seconde|CountPerSecond|Average|Het gemiddelde aantal i/o-Lees bewerkingen in de vorige voorbeeld periode. Houd er rekening mee dat deze bewerkingen variabele grootte kunnen hebben.|Geen|
|Schrijf bewerkingen op de schijf per seconde|Schrijf bewerkingen op de schijf per seconde|CountPerSecond|Average|Het gemiddelde aantal i/o-schrijf bewerkingen in de vorige voorbeeld periode. Houd er rekening mee dat deze bewerkingen variabele grootte kunnen hebben.|Geen|
|DiskReadLatency|Lees latentie van schijf|Milliseconden|Average|Totale lees latentie. De som van de lees latentie van het apparaat en de kernel.|Geen|
|DiskWriteLatency|Schrijf latentie schijf|Milliseconden|Average|Totale schrijf latentie. De som van de latentie voor het schrijven van apparaten en kernels.|Geen|
|NetworkInBytesPerSecond|Netwerk in bytes per seconde|BytesPerSecond|Average|Gemiddelde netwerk doorvoer voor ontvangen verkeer.|Geen|
|NetworkOutBytesPerSecond|Netwerk uitgaande bytes per seconde|BytesPerSecond|Average|Gemiddelde netwerk doorvoer voor verzonden verkeer.|Geen|
|Netwerk in|Netwerk in|Bytes|Totaal|Totale netwerk doorvoer voor ontvangen verkeer.|Geen|
|Netwerk uit|Netwerk uit|Bytes|Totaal|Totale netwerk doorvoer voor verzonden verkeer.|Geen|
|MemoryUsed|Gebruikt geheugen|Bytes|Average|De hoeveelheid machine geheugen die wordt gebruikt door de VM.|Geen|
|MemoryGranted|Toegewezen geheugen|Bytes|Average|De hoeveelheid geheugen die door de host is toegewezen aan de virtuele machine. Er wordt geen geheugen verleend aan de host totdat deze één keer wordt aangevallen en het toegewezen geheugen kan worden uitgewisseld of geballon weg als de VMkernel het geheugen nodig heeft.|Geen|
|MemoryActive|Actief geheugen|Bytes|Average|De hoeveelheid geheugen die wordt gebruikt door de virtuele machine in het verleden kleine tijd venster. Dit is het ' True ' nummer van de hoeveelheid geheugen die momenteel nodig is voor de VM. Extra, ongebruikt geheugen kan worden uitgewisseld of geballond zonder gevolgen voor de prestaties van de gast.|Geen|
|Percentage CPU|Percentage CPU|Percentage|Average|Het CPU-gebruik. Deze waarde wordt gerapporteerd met 100% voor alle processor kernen op het systeem. Een voor beeld: een twee richtings-VM met 50% van een systeem met vier kern geheugens is volledig met twee kernen.|Geen|
|PercentageCpuReady|Percentage CPU gereed|Milliseconden|Totaal|Beschik bare tijd is de tijd die nodig is voor het wachten op CPU ('s) in het afgelopen update-interval.|Geen|

## <a name="microsoftwebserverfarms"></a>Micro soft. web/server farms

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CpuPercentage|CPU-percentage|Percentage|Average|CPU-percentage|Exemplaar|
|MemoryPercentage|Geheugen percentage|Percentage|Average|Geheugen percentage|Exemplaar|
|DiskQueueLength|Wachtrij lengte voor schijf|Aantal|Average|Wachtrij lengte voor schijf|Exemplaar|
|HttpQueueLength|Lengte van http-wachtrij|Aantal|Average|Lengte van http-wachtrij|Exemplaar|
|BytesReceived|Gegevens in|Bytes|Totaal|Gegevens in|Exemplaar|
|Bytes sent|Gegevens uit|Bytes|Totaal|Gegevens uit|Exemplaar|
|TcpSynSent|TCP SYN verzonden|Aantal|Average|TCP SYN verzonden|Exemplaar|
|TcpSynReceived|TCP SYN ontvangen|Aantal|Average|TCP SYN ontvangen|Exemplaar|
|TcpEstablished|TCP-verbinding|Aantal|Average|TCP-verbinding|Exemplaar|
|TcpFinWait1|TCP-FIN-wacht 1|Aantal|Average|TCP-FIN-wacht 1|Exemplaar|
|TcpFinWait2|TCP FIN WAIT 2|Aantal|Average|TCP FIN WAIT 2|Exemplaar|
|TcpClosing|TCP sluiten|Aantal|Average|TCP sluiten|Exemplaar|
|TcpCloseWait|TCP-wacht tijd voor sluiten|Aantal|Average|TCP-wacht tijd voor sluiten|Exemplaar|
|TcpLastAck|TCP laatste ACK|Aantal|Average|TCP laatste ACK|Exemplaar|
|TcpTimeWait|Wacht tijd voor TCP-bewerking|Aantal|Average|Wacht tijd voor TCP-bewerking|Exemplaar|

## <a name="microsoftwebsites-excluding-functions"></a>Micro soft. web/sites (met uitzonde ring van functies) 

> [!NOTE]
> Het gebruik van het **Bestands systeem** is een nieuwe waarde die wereld wijd wordt getotaliseerd, er worden geen gegevens verwacht, tenzij u White List hebt voor een persoonlijke preview.

> [!IMPORTANT]
> De **gemiddelde reactie tijd** wordt afgeschaft om Verwar ring met metrische aggregaties te voor komen. Gebruik de **reactie tijd** als vervanging.

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CpuTime|CPU-tijd|Seconden|Totaal|CPU-tijd|Exemplaar|
|Aanvragen|Aanvragen|Aantal|Totaal|Aanvragen|Exemplaar|
|BytesReceived|Gegevens in|Bytes|Totaal|Gegevens in|Exemplaar|
|Bytes sent|Gegevens uit|Bytes|Totaal|Gegevens uit|Exemplaar|
|Http101|Http 101|Aantal|Totaal|Http 101|Exemplaar|
|Http2xx|Http-2xx|Aantal|Totaal|Http-2xx|Exemplaar|
|Http3xx|HTTP-3xx|Aantal|Totaal|HTTP-3xx|Exemplaar|
|Http401|HTTP 401|Aantal|Totaal|HTTP 401|Exemplaar|
|Http403|HTTP 403|Aantal|Totaal|HTTP 403|Exemplaar|
|Http404|Http 404|Aantal|Totaal|Http 404|Exemplaar|
|Http406|Http 406|Aantal|Totaal|Http 406|Exemplaar|
|Http4xx|Http-4xx|Aantal|Totaal|Http-4xx|Exemplaar|
|Http5xx|Http-server fouten|Aantal|Totaal|Http-server fouten|Exemplaar|
|MemoryWorkingSet|Werkset geheugen|Bytes|Average|Werkset geheugen|Exemplaar|
|AverageMemoryWorkingSet|Gemiddelde werkset geheugen|Bytes|Average|Gemiddelde werkset geheugen|Exemplaar|
|HttpResponseTime|Reactie tijd|Seconden|Totaal|Reactie tijd|Exemplaar|
|AverageResponseTime|Gemiddelde reactie tijd (afgeschaft)|Seconden|Average|Gemiddelde reactie tijd|Exemplaar|
|AppConnections|Verbindingen|Aantal|Average|Verbindingen|Exemplaar|
|Formuleer|Aantal ingangen|Aantal|Average|Aantal ingangen|Exemplaar|
|Lijnen|Aantal threads|Aantal|Average|Aantal threads|Exemplaar|
|PrivateBytes|Privé-bytes|Bytes|Average|Privé-bytes|Exemplaar|
|IoReadBytesPerSecond|I/o gelezen bytes per seconde|BytesPerSecond|Totaal|I/o gelezen bytes per seconde|Exemplaar|
|IoWriteBytesPerSecond|I/o-schrijf bewerkingen in bytes per seconde|BytesPerSecond|Totaal|I/o-schrijf bewerkingen in bytes per seconde|Exemplaar|
|IoOtherBytesPerSecond|Andere i/o-bytes per seconde|BytesPerSecond|Totaal|Andere i/o-bytes per seconde|Exemplaar|
|IoReadOperationsPerSecond|I/o-Lees bewerkingen per seconde|BytesPerSecond|Totaal|I/o-Lees bewerkingen per seconde|Exemplaar|
|IoWriteOperationsPerSecond|I/o-schrijf bewerkingen per seconde|BytesPerSecond|Totaal|I/o-schrijf bewerkingen per seconde|Exemplaar|
|IoOtherOperationsPerSecond|Andere i/o-bewerkingen per seconde|BytesPerSecond|Totaal|Andere i/o-bewerkingen per seconde|Exemplaar|
|RequestsInApplicationQueue|Aanvragen in de wachtrij van de toepassing|Aantal|Average|Aanvragen in de wachtrij van de toepassing|Exemplaar|
|CurrentAssemblies|Huidige Assembly's|Aantal|Average|Huidige Assembly's|Exemplaar|
|TotalAppDomains|Totaal aantal app-domeinen|Aantal|Average|Totaal aantal app-domeinen|Exemplaar|
|TotalAppDomainsUnloaded|Totaal aantal verwijderde app-domeinen|Aantal|Average|Totaal aantal verwijderde app-domeinen|Exemplaar|
|Gen0Collections|Schone verzamelingen van 0 gen|Aantal|Totaal|Schone verzamelingen van 0 gen|Exemplaar|
|Gen1Collections|1 garbagecollection-verzamelingen|Aantal|Totaal|1 garbagecollection-verzamelingen|Exemplaar|
|Gen2Collections|Opschoon verzamelingen van generatie 2|Aantal|Totaal|Opschoon verzamelingen van generatie 2|Exemplaar|
|HealthCheckStatus|Status van de status controle|Aantal|Average|Status van de status controle|Exemplaar|
|FileSystemUsage|Gebruik van bestands systeem|Bytes|Average|Gebruik van bestands systeem|Geen|

## <a name="microsoftwebsites-functions"></a>Micro soft. web/sites (functies)

> [!NOTE]
> Het gebruik van het **Bestands systeem** is een nieuwe waarde die wereld wijd wordt getotaliseerd, er worden geen gegevens verwacht, tenzij u White List hebt voor een persoonlijke preview.

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BytesReceived|Gegevens in|Bytes|Totaal|Gegevens in|Exemplaar|
|Bytes sent|Gegevens uit|Bytes|Totaal|Gegevens uit|Exemplaar|
|Http5xx|Http-server fouten|Aantal|Totaal|Http-server fouten|Exemplaar|
|MemoryWorkingSet|Werkset geheugen|Bytes|Average|Werkset geheugen|Exemplaar|
|AverageMemoryWorkingSet|Gemiddelde werkset geheugen|Bytes|Average|Gemiddelde werkset geheugen|Exemplaar|
|FunctionExecutionUnits|Eenheden voor functie-uitvoering|MB/milliseconden|Totaal|[Eenheden voor functie-uitvoering](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Exemplaar|
|FunctionExecutionCount|Aantal functie-uitvoeringen|Aantal|Totaal|Aantal functie-uitvoeringen|Exemplaar|
|PrivateBytes|Privé-bytes|Bytes|Average|Privé-bytes|Exemplaar|
|IoReadBytesPerSecond|I/o gelezen bytes per seconde|BytesPerSecond|Totaal|I/o gelezen bytes per seconde|Exemplaar|
|IoWriteBytesPerSecond|I/o-schrijf bewerkingen in bytes per seconde|BytesPerSecond|Totaal|I/o-schrijf bewerkingen in bytes per seconde|Exemplaar|
|IoOtherBytesPerSecond|Andere i/o-bytes per seconde|BytesPerSecond|Totaal|Andere i/o-bytes per seconde|Exemplaar|
|IoReadOperationsPerSecond|I/o-Lees bewerkingen per seconde|BytesPerSecond|Totaal|I/o-Lees bewerkingen per seconde|Exemplaar|
|IoWriteOperationsPerSecond|I/o-schrijf bewerkingen per seconde|BytesPerSecond|Totaal|I/o-schrijf bewerkingen per seconde|Exemplaar|
|IoOtherOperationsPerSecond|Andere i/o-bewerkingen per seconde|BytesPerSecond|Totaal|Andere i/o-bewerkingen per seconde|Exemplaar|
|RequestsInApplicationQueue|Aanvragen in de wachtrij van de toepassing|Aantal|Average|Aanvragen in de wachtrij van de toepassing|Exemplaar|
|CurrentAssemblies|Huidige Assembly's|Aantal|Average|Huidige Assembly's|Exemplaar|
|TotalAppDomains|Totaal aantal app-domeinen|Aantal|Average|Totaal aantal app-domeinen|Exemplaar|
|TotalAppDomainsUnloaded|Totaal aantal verwijderde app-domeinen|Aantal|Average|Totaal aantal verwijderde app-domeinen|Exemplaar|
|Gen0Collections|Schone verzamelingen van 0 gen|Aantal|Totaal|Schone verzamelingen van 0 gen|Exemplaar|
|Gen1Collections|1 garbagecollection-verzamelingen|Aantal|Totaal|1 garbagecollection-verzamelingen|Exemplaar|
|Gen2Collections|Opschoon verzamelingen van generatie 2|Aantal|Totaal|Opschoon verzamelingen van generatie 2|Exemplaar|
|HealthCheckStatus|Status van de status controle|Aantal|Average|Status van de status controle|Exemplaar|
|FileSystemUsage|Gebruik van bestands systeem|Bytes|Average|Gebruik van bestands systeem|Geen|

## <a name="microsoftwebsitesslots"></a>Micro soft. web/sites/sleuven

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CpuTime|CPU-tijd|Seconden|Totaal|CPU-tijd|Exemplaar|
|Aanvragen|Aanvragen|Aantal|Totaal|Aanvragen|Exemplaar|
|BytesReceived|Gegevens in|Bytes|Totaal|Gegevens in|Exemplaar|
|Bytes sent|Gegevens uit|Bytes|Totaal|Gegevens uit|Exemplaar|
|Http101|Http 101|Aantal|Totaal|Http 101|Exemplaar|
|Http2xx|Http-2xx|Aantal|Totaal|Http-2xx|Exemplaar|
|Http3xx|HTTP-3xx|Aantal|Totaal|HTTP-3xx|Exemplaar|
|Http401|HTTP 401|Aantal|Totaal|HTTP 401|Exemplaar|
|Http403|HTTP 403|Aantal|Totaal|HTTP 403|Exemplaar|
|Http404|Http 404|Aantal|Totaal|Http 404|Exemplaar|
|Http406|Http 406|Aantal|Totaal|Http 406|Exemplaar|
|Http4xx|Http-4xx|Aantal|Totaal|Http-4xx|Exemplaar|
|Http5xx|Http-server fouten|Aantal|Totaal|Http-server fouten|Exemplaar|
|MemoryWorkingSet|Werkset geheugen|Bytes|Average|Werkset geheugen|Exemplaar|
|AverageMemoryWorkingSet|Gemiddelde werkset geheugen|Bytes|Average|Gemiddelde werkset geheugen|Exemplaar|
|AverageResponseTime|Gemiddelde reactie tijd|Seconden|Average|Gemiddelde reactie tijd|Exemplaar|
|HttpResponseTime|Reactie tijd|Seconden|Average|Reactie tijd|Exemplaar|
|FunctionExecutionUnits|Eenheden voor functie-uitvoering|Aantal|Totaal|Eenheden voor functie-uitvoering|Exemplaar|
|FunctionExecutionCount|Aantal functie-uitvoeringen|Aantal|Totaal|Aantal functie-uitvoeringen|Exemplaar|
|AppConnections|Verbindingen|Aantal|Average|Verbindingen|Exemplaar|
|Formuleer|Aantal ingangen|Aantal|Average|Aantal ingangen|Exemplaar|
|Lijnen|Aantal threads|Aantal|Average|Aantal threads|Exemplaar|
|PrivateBytes|Privé-bytes|Bytes|Average|Privé-bytes|Exemplaar|
|IoReadBytesPerSecond|I/o gelezen bytes per seconde|BytesPerSecond|Totaal|I/o gelezen bytes per seconde|Exemplaar|
|IoWriteBytesPerSecond|I/o-schrijf bewerkingen in bytes per seconde|BytesPerSecond|Totaal|I/o-schrijf bewerkingen in bytes per seconde|Exemplaar|
|IoOtherBytesPerSecond|Andere i/o-bytes per seconde|BytesPerSecond|Totaal|Andere i/o-bytes per seconde|Exemplaar|
|IoReadOperationsPerSecond|I/o-Lees bewerkingen per seconde|BytesPerSecond|Totaal|I/o-Lees bewerkingen per seconde|Exemplaar|
|IoWriteOperationsPerSecond|I/o-schrijf bewerkingen per seconde|BytesPerSecond|Totaal|I/o-schrijf bewerkingen per seconde|Exemplaar|
|IoOtherOperationsPerSecond|Andere i/o-bewerkingen per seconde|BytesPerSecond|Totaal|Andere i/o-bewerkingen per seconde|Exemplaar|
|RequestsInApplicationQueue|Aanvragen in de wachtrij van de toepassing|Aantal|Average|Aanvragen in de wachtrij van de toepassing|Exemplaar|
|CurrentAssemblies|Huidige Assembly's|Aantal|Average|Huidige Assembly's|Exemplaar|
|TotalAppDomains|Totaal aantal app-domeinen|Aantal|Average|Totaal aantal app-domeinen|Exemplaar|
|TotalAppDomainsUnloaded|Totaal aantal verwijderde app-domeinen|Aantal|Average|Totaal aantal verwijderde app-domeinen|Exemplaar|
|Gen0Collections|Schone verzamelingen van 0 gen|Aantal|Totaal|Schone verzamelingen van 0 gen|Exemplaar|
|Gen1Collections|1 garbagecollection-verzamelingen|Aantal|Totaal|1 garbagecollection-verzamelingen|Exemplaar|
|Gen2Collections|Opschoon verzamelingen van generatie 2|Aantal|Totaal|Opschoon verzamelingen van generatie 2|Exemplaar|
|HealthCheckStatus|Status van de status controle|Aantal|Average|Status van de status controle|Exemplaar|
|FileSystemUsage|Gebruik van bestands systeem|Bytes|Average|Gebruik van bestands systeem|Geen|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Micro soft. Web/hostingEnvironments/multiRolePools

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Aanvragen|Aanvragen|Aantal|Totaal|Aanvragen|Exemplaar|
|BytesReceived|Gegevens in|Bytes|Totaal|Gegevens in|Exemplaar|
|Bytes sent|Gegevens uit|Bytes|Totaal|Gegevens uit|Exemplaar|
|Http101|Http 101|Aantal|Totaal|Http 101|Exemplaar|
|Http2xx|Http-2xx|Aantal|Totaal|Http-2xx|Exemplaar|
|Http3xx|HTTP-3xx|Aantal|Totaal|HTTP-3xx|Exemplaar|
|Http401|HTTP 401|Aantal|Totaal|HTTP 401|Exemplaar|
|Http403|HTTP 403|Aantal|Totaal|HTTP 403|Exemplaar|
|Http404|Http 404|Aantal|Totaal|Http 404|Exemplaar|
|Http406|Http 406|Aantal|Totaal|Http 406|Exemplaar|
|Http4xx|Http-4xx|Aantal|Totaal|Http-4xx|Exemplaar|
|Http5xx|Http-server fouten|Aantal|Totaal|Http-server fouten|Exemplaar|
|AverageResponseTime|Gemiddelde reactie tijd|Seconden|Average|Gemiddelde reactie tijd|Exemplaar|
|CpuPercentage|CPU-percentage|Percentage|Average|CPU-percentage|Exemplaar|
|MemoryPercentage|Geheugen percentage|Percentage|Average|Geheugen percentage|Exemplaar|
|DiskQueueLength|Wachtrij lengte voor schijf|Aantal|Average|Wachtrij lengte voor schijf|Exemplaar|
|HttpQueueLength|Lengte van http-wachtrij|Aantal|Average|Lengte van http-wachtrij|Exemplaar|
|ActiveRequests|Actieve aanvragen|Aantal|Totaal|Actieve aanvragen|Exemplaar|
|TotalFrontEnds|Totale front-ends|Aantal|Average|Totale front-ends|Geen|
|SmallAppServicePlanInstances|Werk rollen voor kleine App Service plannen|Aantal|Average|Werk rollen voor kleine App Service plannen|Geen|
|MediumAppServicePlanInstances|Werk nemers met gemiddeld App Service plannen|Aantal|Average|Werk nemers met gemiddeld App Service plannen|Geen|
|LargeAppServicePlanInstances|Werk rollen voor grote App Service plannen|Aantal|Average|Werk rollen voor grote App Service plannen|Geen|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Micro soft. Web/hostingEnvironments/workerPools

|Gegevens|Weergave naam voor metrische gegevens|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|WorkersTotal|Totaal aantal werk rollen|Aantal|Average|Totaal aantal werk rollen|Geen|
|WorkersAvailable|Beschik bare werk nemers|Aantal|Average|Beschik bare werk nemers|Geen|
|WorkersUsed|Gebruikte werk rollen|Aantal|Average|Gebruikte werk rollen|Geen|
|CpuPercentage|CPU-percentage|Percentage|Average|CPU-percentage|Exemplaar|
|MemoryPercentage|Geheugen percentage|Percentage|Average|Geheugen percentage|Exemplaar|
## <a name="next-steps"></a>Volgende stappen
* [Meer informatie over metrische gegevens in Azure Monitor](data-platform.md)
* [Waarschuwingen maken op basis van metrische gegevens](alerts-overview.md)
* [Metrische gegevens exporteren naar opslag, Event hub of Log Analytics](platform-logs-overview.md)

