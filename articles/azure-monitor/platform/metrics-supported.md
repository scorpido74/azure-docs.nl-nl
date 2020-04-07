---
title: Ondersteunde metrische gegevens azure monitor op resourcetype
description: Lijst met statistieken die beschikbaar zijn voor elk resourcetype met Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 04/06/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 846e8da889e3913c4a8eaab7808495bbd8afad29
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80754659"
---
# <a name="supported-metrics-with-azure-monitor"></a>Ondersteunde statistieken met Azure Monitor

> [!NOTE]
> Deze lijst wordt grotendeels automatisch gegenereerd uit de AZURE Monitor Metrics REST API. Elke wijziging in deze lijst via Github kan zonder waarschuwing worden overgeschreven. Neem contact op met de auteur van dit artikel voor meer informatie over het maken van permanente updates.

Azure Monitor biedt verschillende manieren om te communiceren met statistieken, waaronder het in kaart brengen ervan in de portal, toegang tot deze gegevens via de REST API of het opvragen ervan met PowerShell of CLI. Hieronder vindt u een volledige lijst met alle statistieken die momenteel beschikbaar zijn met de metrische pijplijn van Azure Monitor. Andere statistieken kunnen beschikbaar zijn in de portal of met behulp van oudere API's. Deze lijst hieronder bevat alleen statistieken die beschikbaar zijn via de geconsolideerde Azure Monitor-metrische pijplijn. De statistieken worden georganiseerd op naamruimte. Zie [Resourceproviders voor Azure-services voor](../../azure-resource-manager/management/azure-services-resource-providers.md)een lijst met services en de naamruimten die ertoe behoren. Gebruik de [api-versie 2018-01-01](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) voor en toegang tot deze statistieken om deze statistieken programmatisch te zoeken en te openen

> [!NOTE]
> Het verzenden van multidimensionale metrische gegevens via diagnostische instellingen wordt momenteel niet ondersteund. Metrische gegevens met dimensies worden geëxporteerd als platte eendimensionale metrische gegevens, als totaal van alle dimensiewaarden.
>
> *Een voorbeeld*: de meetwaarde 'Binnenkomende berichten' voor een Event Hub kan worden verkend en uitgezet op wachtrijniveau. Wanneer de waarde wordt geëxporteerd via diagnostische instellingen, wordt deze echter voorgesteld als alle binnenkomende berichten voor alle wachtrijen in de Event Hub.
>
> Zie [dit artikel](metrics-supported-export-diagnostic-settings.md)voor een lijst met platformstatistieken die via diagnostische instellingen kunnen worden geëxporteerd.


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|qpu_metric|QPU (QPU)|Count|Average|QPU. Bereik 0-100 voor S1, 0-200 voor S2 en 0-400 voor S4|ServerResourceType|
|memory_metric|Geheugen|Bytes|Average|Geheugen. Bereik 0-25 GB voor S1, 0-50 GB voor S2 en 0-100 GB voor S4|ServerResourceType|
|private_bytes_metric|Privébytes|Bytes|Average|Privébytes.|ServerResourceType|
|virtual_bytes_metric|Virtuele bytes|Bytes|Average|Virtuele bytes.|ServerResourceType|
|TotalConnectionRequests|Totaal aantal verbindingsaanvragen|Count|Average|Totaal aantal verbindingsaanvragen. Dit zijn aankomsten.|ServerResourceType|
|Succesvolle verbindingenPerSec|Succesvolle verbindingen per seconde|CountPerSeconde|Average|Snelheid van succesvolle verbindingsvoltooiingen.|ServerResourceType|
|TotalConnection-fouten|Totale verbindingsfouten|Count|Average|Totaal mislukte verbindingspogingen.|ServerResourceType|
|CurrentUserSessions|Huidige gebruikerssessies|Count|Average|Huidig aantal gebruikerssessies ingesteld.|ServerResourceType|
|QuerypoolBusyThreads|Drukke threads voor querygroep|Count|Average|Aantal drukke threads in de querythreadgroep.|ServerResourceType|
|CommandPoolJobQueueLengte|Wachtrijlengte van taakgroep|Count|Average|Aantal taken in de wachtrij van de command thread pool.|ServerResourceType|
|ProcessingpoolJobQueueLength|Wachtrijlengte van de groeptaak verwerken|Count|Average|Aantal niet-I/O-taken in de wachtrij van de verwerkingsthreadgroep.|ServerResourceType|
|Huidige verbindingen|Verbinding: huidige verbindingen|Count|Average|Huidig aantal clientverbindingen tot stand gebracht.|ServerResourceType|
|Schonerestroomprijs|Geheugen: Schonere huidige prijs|Count|Average|Huidige prijs van het geheugen, $/byte/time, genormaliseerd tot 1000.|ServerResourceType|
|CleanerMemoryKrimpbaar|Geheugen: Schoner geheugen krimpbaar|Bytes|Average|Hoeveelheid geheugen, in bytes, onder voorbehoud van zuivering door de achtergrond reiniger.|ServerResourceType|
|CleanerMemoryNonshrinkable CleanerMemory|Geheugen: Schoner geheugen niet krimpend|Bytes|Average|Hoeveelheid geheugen, in bytes, niet onderhevig aan zuivering door de achtergrond reiniger.|ServerResourceType|
|Geheugengebruik|Geheugen: geheugengebruik|Bytes|Average|Geheugengebruik van het serverproces zoals gebruikt bij het berekenen van een schonere geheugenprijs. Gelijk aan tegen proces\PrivateBytes plus de grootte van geheugentoegewezen gegevens, waarbij elk geheugen wordt genegeerd dat in kaart is gebracht of toegewezen is toegewezen door de xVelocity in-memory analytics engine (VertiPaq) die meer dan de xVelocity-engine Memory Limit bevat.|ServerResourceType|
|MemoryLimitHard|Geheugen: Geheugenlimiet hard|Bytes|Average|Limiet voor hard geheugen, uit configuratiebestand.|ServerResourceType|
|MemoryLimitHigh|Geheugen: geheugenlimiet hoog|Bytes|Average|Hoge geheugenlimiet, van configuratiebestand.|ServerResourceType|
|MemoryLimitLow|Geheugen: geheugenlimiet laag|Bytes|Average|Lage geheugenlimiet, van configuratiebestand.|ServerResourceType|
|MemoryLimitVertiPaq|Geheugen: Geheugenlimiet VertiPaq|Bytes|Average|In-memory limit, van configuratiebestand.|ServerResourceType|
|Quota|Geheugen: Quotum|Bytes|Average|Huidig geheugenquotum, in bytes. Geheugenquotum wordt ook wel geheugensubsidie of geheugenreservering genoemd.|ServerResourceType|
|Quotageblokkeerd|Geheugen: Quotum geblokkeerd|Count|Average|Huidig aantal quotaaanvragen dat wordt geblokkeerd totdat andere geheugenquota zijn vrijgemaakt.|ServerResourceType|
|VertiPaqNonpaged|Geheugen: VertiPaq Nonpaged|Bytes|Average|Bytes geheugen die zijn vergrendeld in de werkset voor gebruik door de geheugenengine.|ServerResourceType|
|VertiPaqPaged|Geheugen: VertiPaq Paged|Bytes|Average|Bytes van het opgepiepte geheugen dat wordt gebruikt voor gegevens in het geheugen.|ServerResourceType|
|RowsReadPersec|Verwerking: Rijen per seconde gelezen|CountPerSeconde|Average|Snelheid van rijen die worden gelezen uit alle relationele databases.|ServerResourceType|
|Rijenomgebouwde persec|Verwerking: rijen die per seconde worden geconverteerd|CountPerSeconde|Average|Snelheid van rijen die tijdens de verwerking worden omgezet.|ServerResourceType|
|RijengeschrevenPersec|Verwerking: rijen geschreven per seconde|CountPerSeconde|Average|Snelheid van rijen geschreven tijdens de verwerking.|ServerResourceType|
|CommandPoolBusyThreads|Threads: Drukke threads voor het uitvoeren van opdrachten|Count|Average|Aantal drukke threads in de command thread pool.|ServerResourceType|
|CommandPoolIdleThreads|Threads: Niet-actieve threads van de opdrachtgroep|Count|Average|Aantal niet-actieve threads in de command thread pool.|ServerResourceType|
|LongParsingBusyThreads|Threads: Lange ontwering drukke threads|Count|Average|Aantal drukke threads in de lange parsing thread pool.|ServerResourceType|
|LongParsingIdleThreads|Threads: Lange ontwering idle threads|Count|Average|Aantal niet-actieve threads in de lange parsing thread pool.|ServerResourceType|
|LongParsingJobQueueLength|Threads: Lange ontwering taakwachtrijlengte|Count|Average|Aantal taken in de wachtrij van de lange parsingthreadpool.|ServerResourceType|
|ProcessingpoolBusyIOJobThreads|Threads: Verwerking van de gedrukke I/O-taakthreads|Count|Average|Aantal threads met I/O-taken in de verwerkingsthreadgroep.|ServerResourceType|
|ProcessingpoolBusyNonIOThreads|Threads: Verwerking van druk niet-I/O-threads|Count|Average|Aantal threads waarop niet-I/O-taken worden uitgevoerd in de verwerkingsthreadgroep.|ServerResourceType|
|ProcessingpoolIOJobQueueLength|Threads: De wachtrijlengte van de groep I/O verwerken|Count|Average|Aantal I/O-taken in de wachtrij van de verwerkingsthreadgroep.|ServerResourceType|
|VerwerkingPoolIdleIOJobThreads|Threads: I/O-taakthreads verwerken|Count|Average|Aantal niet-actieve threads voor I/O-taken in de verwerkingsthreadgroep.|ServerResourceType|
|VerwerkingPoolIdleNonIOThreads|Threads: Niet-I/O-threads verwerken|Count|Average|Aantal niet-actieve threads in de verwerkingsthreadpool die is gewijd aan niet-I/O-taken.|ServerResourceType|
|QueryPoolIdleThreads|Threads: Niet-actieve threads van de querygroep|Count|Average|Aantal niet-actieve threads voor I/O-taken in de verwerkingsthreadgroep.|ServerResourceType|
|QuerypoolJobQueueLengte|Threads: Query pool job queue lengt|Count|Average|Aantal taken in de wachtrij van de querythreadgroep.|ServerResourceType|
|ShortParsingBusyThreads|Threads: Korte parsing drukke threads|Count|Average|Aantal drukke threads in de korte parsing thread pool.|ServerResourceType|
|ShortParsingIdleThreads|Threads: Korte ontwering idle threads|Count|Average|Aantal niet-actieve threads in de korte parsing thread pool.|ServerResourceType|
|ShortParsingJobQueueLength ShortParsingJobQueueLength ShortParsingJobQueueLength ShortPar|Threads: Korte afstand van de taakwachtrijlengte|Count|Average|Aantal taken in de wachtrij van de korte parsing thread pool.|ServerResourceType|
|memory_thrashing_metric|Geheugenthrashing|Percentage|Average|Gemiddelde geheugen geseling.|ServerResourceType|
|mashup_engine_qpu_metric|M Engine QPU|Count|Average|QPU-gebruik door mashup-motorprocessen|ServerResourceType|
|mashup_engine_memory_metric|M-enginegeheugen|Bytes|Average|Geheugengebruik door mashup-engineprocessen|ServerResourceType|
|mashup_engine_private_bytes_metric|M Engine Private Bytes|Bytes|Average|Privé bytes gebruik door mashup engine processen.|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Virtuele bytes van M Engine|Bytes|Average|Virtuele bytes gebruik door mashup engine processen.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TotalRequests|Totaalgatewayaanvragen (afgeschaft)|Count|Totaal|Aantal gatewayaanvragen - In plaats daarvan multidimensionele aanvraagmetriek gebruiken met gatewayresponsecodecategoriedimensie|Locatie, Hostname|
|Succesvolle verzoeken|Succesvolle gatewayaanvragen (afgeschaft)|Count|Totaal|Aantal geslaagde gatewayaanvragen - In plaats daarvan multidimensionele aanvraagmetriek gebruiken met gatewayresponsecodecategoriedimensie|Locatie, Hostname|
|Ongeautoriseerde verzoeken|Ongeautoriseerde gatewayverzoeken (afgeschaft)|Count|Totaal|Aantal ongeautoriseerde gatewayaanvragen - Gebruik in plaats daarvan multi-dimensionele aanvraagmetriek met gatewayresponsecodecategoriedimensie|Locatie, Hostname|
|Mislukte aanvragen|Mislukte gatewayaanvragen (afgeschaft)|Count|Totaal|Aantal fouten in gatewayaanvragen - In plaats daarvan multidimensionaataanvragen gebruiken met de dimensie GatewayResponseCodeCategory|Locatie, Hostname|
|Overige verzoeken|Andere gatewayaanvragen (afgeschaft)|Count|Totaal|Aantal andere gatewayaanvragen - In plaats daarvan met een multidimensionaat aanvraagmetalmeting gebruiken met gatewayresponsecodecategoriedimensie|Locatie, Hostname|
|Duur|Totale duur van gatewayaanvragen|Milliseconden|Average|Algemene duur van gatewayaanvragen in milliseconden|Locatie, Hostname|
|Back-endDuur|Duur van backendaanvragen|Milliseconden|Average|Duur van backendaanvragen in milliseconden|Locatie, Hostname|
|Capaciteit|Capaciteit|Percentage|Average|Functiemetriek voor ApiManagement-service|Locatie|
|EventHubTotalEvents|Totaal EventHub-gebeurtenissen|Count|Totaal|Aantal gebeurtenissen dat naar EventHub wordt verzonden|Locatie|
|EventHubSuccessfulEvents|Succesvolle EventHub-evenementen|Count|Totaal|Aantal succesvolle EventHub-evenementen|Locatie|
|GebeurtenisHubTotalFailedgebeurtenissen|Mislukte EventHub-gebeurtenissen|Count|Totaal|Aantal mislukte EventHub-gebeurtenissen|Locatie|
|EventHubRejectedEvents|Afgewezen EventHub-gebeurtenissen|Count|Totaal|Aantal afgewezen EventHub-gebeurtenissen (verkeerde configuratie of ongeautoriseerd)|Locatie|
|EventHubThrottledEvents|Throttled EventHub-gebeurtenissen|Count|Totaal|Aantal gethrottled EventHub-gebeurtenissen|Locatie|
|EventHubTimedoutGebeurtenissen|Time-out EventHub-evenementen|Count|Totaal|Aantal time-out-EventHub-gebeurtenissen|Locatie|
|EventHubDroppedGebeurtenissen|Gebeurtenisgebeurtenissen van EventHub laten vallen|Count|Totaal|Aantal overgeslagen gebeurtenissen vanwege limiet voor wachtrijgrootte bereikt|Locatie|
|EventHubTotalBytesSent|Grootte van EventHub-gebeurtenissen|Bytes|Totaal|Totale grootte van EventHub-gebeurtenissen in bytes|Locatie|
|Aanvragen|Aanvragen|Count|Totaal|Gatewayaanvraagstatistieken met meerdere dimensies|Locatie,Hostname,LastErrorReason,BackendResponseCode,GatewayResponseCode,BackendResponseCodeCategory,GatewayResponseCodeCategory|
|Netwerkconnectiviteit|Status van bronnen voor netwerkconnectiviteit (voorbeeld)|Count|Totaal|Netwerkconnectiviteitsstatus van afhankelijke resourcetypen van de API-beheerservice|Locatie, ResourceType|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|HttpIncomingRequestCount|HttpIncomingRequestCount|Count|Count|Totaal aantal binnenkomende http-aanvragen.|Statuscode|
|HttpIncomingRequestDuration|HttpIncomingRequestDuration|Count|Average|Latentie op een http-aanvraag.|Statuscode|


## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SystemCpuUsagePercentage SystemCpuUsagePercentage SystemCpuUsagePercentage SystemCpu|Gebruikspercentage systeem-CPU-gebruik|Percentage|Average|De recente cpu-gebruik voor het hele systeem|AppName,Pod|
|AppCpuUsagePercentage|Gebruikspercentage app-cpu's|Percentage|Average|Gebruikspercentage cpu-gebruik van app JVM|AppName,Pod|
|AppMemoryCommitted|App-geheugen toegewezen|Bytes|Average|Geheugen toegewezen aan JVM in bytes|AppName,Pod|
|AppMemoryGebruikt|App-geheugen gebruikt|Bytes|Average|App-geheugen dat wordt gebruikt in bytes|AppName,Pod|
|AppMemoryMax AppMemoryMax AppMemoryMax|App-geheugen max.|Bytes|Maximum|De maximale hoeveelheid geheugen in bytes die kan worden gebruikt voor geheugenbeheer|AppName,Pod|
|MaxOldGenMemoryPoolBytes|Max beschikbare oude generatie gegevensgrootte|Bytes|Average|Maximale grootte van oude generatie geheugen pool|AppName,Pod|
|OldGenMemoryPoolBytes|Gegevensgrootte van oude generatie|Bytes|Average|Grootte van oude generatie geheugen pool na een volledige GC|AppName,Pod|
|OldGenPromotedBytes|Bevorderen tot gegevensgrootte van oude generatie|Bytes|Maximum|Aantal positieve verhogingen in de grootte van de oude generatie geheugenpool vóór GC aan na GC|AppName,Pod|
|YoungGenPromotedBytes YoungGenPromotedBytes YoungGenPromotedBytes YoungGen|Bevorderen tot gegevensgrootte van jonge generaties|Bytes|Maximum|Verhoogd voor een toename van de grootte van de jonge generatie geheugen pool na een GC tot vóór de volgende|AppName,Pod|
|GCPauseTotalCount|GC-pauzetelling|Count|Totaal|GC-pauzetelling|AppName,Pod|
|GCPauseTotalTime|Totale tijd van GC-pauze|Milliseconden|Totaal|Totale tijd van GC-pauze|AppName,Pod|
|TomcatSentBytes|Tomcat Totaal verzonden bytes|Bytes|Totaal|Tomcat Totaal verzonden bytes|AppName,Pod|
|TomcatReceivedBytes|Tomcat Totaal ontvangen bytes|Bytes|Totaal|Tomcat Totaal ontvangen bytes|AppName,Pod|
|TomcatRequestTotalTime|Totaalaantal aanvragen tomcat-aanvraag|Milliseconden|Totaal|Totaalaantal aanvragen tomcat-aanvraag|AppName,Pod|
|TomcatRequestTotalCount|Totaal aantal aanvragen voor Tomcat-aanvragen|Count|Totaal|Totaal aantal aanvragen voor Tomcat-aanvragen|AppName,Pod|
|TomcatResponseAvgTime|Gemiddelde tijd voor Tomcat-aanvraag|Milliseconden|Average|Gemiddelde tijd voor Tomcat-aanvraag|AppName,Pod|
|TomcatRequestMaxTime|Tomcat Request Max Time|Milliseconden|Maximum|Tomcat Request Max Time|AppName,Pod|
|TomcatErrorCount|Tomcat-globale fout|Count|Totaal|Tomcat-globale fout|AppName,Pod|
|TomcatSessionActiveMaxCount|Tomcat Session Max Active Count|Count|Totaal|Tomcat Session Max Active Count|AppName,Pod|
|TomcatSessionAliveMaxTijd|Tomcat Sessie Max Alive Time|Milliseconden|Maximum|Tomcat Sessie Max Alive Time|AppName,Pod|
|TomcatSessionActiveCurrentCount|Tomcat Sessie Alive Count|Count|Totaal|Tomcat Sessie Alive Count|AppName,Pod|
|TomcatSessionCreatedCount|Aantal gemaakt Tomcat-sessie|Count|Totaal|Aantal gemaakt Tomcat-sessie|AppName,Pod|
|TomcatSessionExpiredCount|Aantal verlopen tomcat-sessies|Count|Totaal|Aantal verlopen tomcat-sessies|AppName,Pod|
|TomcatSessionRejectedCount|Afgewezen aantal Tomcat-sessie|Count|Totaal|Afgewezen aantal Tomcat-sessie|AppName,Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TotalJob|Totaal aantal taken|Count|Totaal|Het totale aantal banen|Runbook, Status|
|TotalupdateDeploymentRuns|Totaal aantal uitvoeringen van update-implementaties|Count|Totaal|Totale implementatie van software-updates wordt uitgevoerd|SoftwareUpdateConfigurationName, Status|
|TotalupdateDeploymentmachineRuns TotalupdateDeploymentmachineRuns TotalupdateDeploymentmachineRuns Totalupdate|Totaal aantal machine-uitvoeringen van update-implementaties|Count|Totaal|Total software update deployment machine draait in een implementatie van software-updates|SoftwareUpdateConfigurationName,Status,TargetComputer,SoftwareUpdateConfigurationRunid|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Aantal cores|Speciale kerntelling|Count|Totaal|Totaal aantal toegewezen kernen in de batchrekening|Geen|
|TotalNodeCount TotalNodeCount|Speciale nodetelling|Count|Totaal|Totaal aantal toegewezen knooppunten in de batchrekening|Geen|
|LowPriorityCoreCount LowPriorityCoreCount LowPriorityCoreCount LowPriority|LowPriority-kerntelling|Count|Totaal|Totaal aantal kernen met lage prioriteit in de batchrekening|Geen|
|TotalLowPriorityNodeCount TotalLowPriorityNodeCount TotalLowPriorityNodeCount TotalLow|Aantal knooppunts met lage prioriteit|Count|Totaal|Totaal aantal knooppunten met lage prioriteit in de batchrekening|Geen|
|CreatingNodeCount|Aantal knooppunt's maken|Count|Totaal|Aantal knooppunten dat wordt gemaakt|Geen|
|StartingNodeCount|Aantal knooppunten starten|Count|Totaal|Aantal knooppunten dat begint|Geen|
|WachtenForStartTaskNodeCount|Wachten op het aantal takenvan taak|Count|Totaal|Aantal knooppunten dat wacht tot de taak Start is voltooid|Geen|
|BegintaakMisluktNodeCount|Aantal mislukte knooppunttaken starten|Count|Totaal|Aantal knooppunten waarvan de taak Start is mislukt|Geen|
|IdleNodeCount|Aantal niet-actieve knooppunten|Count|Totaal|Aantal niet-actieve knooppunten|Geen|
|OfflineNodeCount|Aantal offline knooppunten|Count|Totaal|Aantal offline knooppunten|Geen|
|RebootingNodeCount|Aantal knooppunten opnieuw opstarten|Count|Totaal|Aantal rebootknooppunten|Geen|
|ReimagingNodeCount|Reimaging Node Count|Count|Totaal|Aantal reimagingknooppunten|Geen|
|RunningNodeCount|Aantal knooppunten uitvoeren|Count|Totaal|Aantal lopende knooppunten|Geen|
|LeavingPoolNodeCount|Poolknooppunt tellen verlaten|Count|Totaal|Aantal knooppunten dat de pool verlaat|Geen|
|UnusableNodeCount|Aantal onbruikbare knooppunt|Count|Totaal|Aantal onbruikbare knooppunten|Geen|
|VoorrangNodeCount|Voorrang Node Count|Count|Totaal|Aantal vooraf gelopen knooppunten|Geen|
|TaakstartGebeurtenis|Gebeurtenissen voor het starten van taken|Count|Totaal|Totaal aantal taken dat is gestart|poolId, jobId|
|TaakCompleteEvent|Gebeurtenissen voltooien van taken|Count|Totaal|Totaal aantal taken dat is voltooid|poolId, jobId|
|Taakfailgebeurtenis|Gebeurtenissen als taakmislukt|Count|Totaal|Totaal aantal taken dat in een mislukte status is voltooid|poolId, jobId|
|PoolCreateEvent|Gebeurtenissen maken|Count|Totaal|Totaal aantal groepen dat is gemaakt|poolId (poolId)|
|PoolResizeStartEvent|Begingebeurtenissen voor het aanpassen van het formaat wijzigen|Count|Totaal|Totaal aantal zwembadgroottes dat is gestart|poolId (poolId)|
|PoolResizeCompleteEvent|Het formaat complete gebeurtenissen van de groep wijzigen|Count|Totaal|Totaal aantal groottes van de groep dat is voltooid|poolId (poolId)|
|PooldeleteStartEvent|Startgebeurtenissen verwijderen verwijderen groep|Count|Totaal|Totaal aantal groepverwijdert dat is gestart|poolId (poolId)|
|PooldeleteCompleteEvent|Complete gebeurtenissen verwijderen verwijderen|Count|Totaal|Totaal aantal groepverwijdert dat is voltooid|poolId (poolId)|
|JobDeleteCompleteEvent|Complete gebeurtenissen verwijderen van taak|Count|Totaal|Totaal aantal taken dat met succes is verwijderd.|jobId|
|JobdeleteStartEvent|Startgebeurtenissen voor taak verwijderen|Count|Totaal|Totaal aantal taken waarvan is gevraagd om te worden verwijderd.|jobId|
|JobDisableCompleteEvent|Complete gebeurtenissen uitschakelen|Count|Totaal|Totaal aantal taken dat met succes is uitgeschakeld.|jobId|
|JobDisableStartEvent|Startgebeurtenissen uitschakelen|Count|Totaal|Totaal aantal banen dat is aangevraagd om te worden uitgeschakeld.|jobId|
|JobStartEvent|Gebeurtenissen voor het starten van vacatures|Count|Totaal|Totaal aantal banen dat met succes is gestart.|jobId|
|Gebeurtenis TaakterminateComplete|Volledige gebeurtenissen beëindigen van taak|Count|Totaal|Totaal aantal taken dat is beëindigd.|jobId|
|JobterminateStartEvent|Begingebeurtenissen voor het beëindigen van taken beëindigen|Count|Totaal|Totaal aantal taken die zijn aangevraagd om te worden beëindigd.|jobId|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/werkruimten

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Vacature ingediend|Vacature ingediend|Count|Totaal|Aantal ingediende vacatures|Scenario,ClusterNaam|
|Taak voltooid|Taak voltooid|Count|Totaal|Aantal voltooide taken|Scenario, clusternaam, resultaattype|
|Totaal aantal knooppunten|Totaal aantal knooppunten|Count|Average|Aantal totale knooppunten|Scenario,ClusterNaam|
|Actieve knooppunten|Actieve knooppunten|Count|Average|Aantal lopende knooppunten|Scenario,ClusterNaam|
|Inactieve knooppunten|Inactieve knooppunten|Count|Average|Aantal niet-actieve knooppunten|Scenario,ClusterNaam|
|Onbruikbare knooppunten|Onbruikbare knooppunten|Count|Average|Aantal onbruikbare knooppunten|Scenario,ClusterNaam|
|Voorkoming van knooppunten|Voorkoming van knooppunten|Count|Average|Aantal vooraf gelopen knooppunten|Scenario,ClusterNaam|
|Knooppunten verlaten|Knooppunten verlaten|Count|Average|Aantal verlatenknooppunten|Scenario,ClusterNaam|
|Totaal aantal kernen|Totaal aantal kernen|Count|Average|Aantal totale kernen|Scenario,ClusterNaam|
|Actieve kernen|Actieve kernen|Count|Average|Aantal actieve kernen|Scenario,ClusterNaam|
|Niet-actieve kernen|Niet-actieve kernen|Count|Average|Aantal niet-actieve kernen|Scenario,ClusterNaam|
|Onbruikbare kernen|Onbruikbare kernen|Count|Average|Aantal onbruikbare kernen|Scenario,ClusterNaam|
|Voorrang kernen|Voorrang kernen|Count|Average|Aantal vooraf gekomen kernen|Scenario,ClusterNaam|
|Kernen verlaten|Kernen verlaten|Count|Average|Aantal verlatenkernen|Scenario,ClusterNaam|
|Percentage quotumgebruik|Percentage quotumgebruik|Count|Average|Percentage van het gebruikte quotum|Scenario, ClusterNaam, VmFamilyName, VmPriority|

## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainLeden

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CpuUsagePercentageInDouble|CPU-gebruikspercentage|Percentage|Maximum|CPU-gebruikspercentage|Knooppunt|
|Geheugengebruik|Geheugengebruik|Bytes|Average|Geheugengebruik|Knooppunt|
|MemoryLimit (MemoryLimit)|Geheugenlimiet|Bytes|Average|Geheugenlimiet|Knooppunt|
|Geheugenusagepercentageindouble|Percentage geheugengebruik|Percentage|Average|Percentage geheugengebruik|Knooppunt|
|OpslagGebruik|Opslaggebruik|Bytes|Average|Opslaggebruik|Knooppunt|
|IOReadBytes|IO Lees bytes|Bytes|Totaal|IO Lees bytes|Knooppunt|
|IOWriteBytes|IO-schrijfbytes|Bytes|Totaal|IO-schrijfbytes|Knooppunt|
|Verbindinggeaccepteerd|Geaccepteerde verbindingen|Count|Totaal|Geaccepteerde verbindingen|Knooppunt|
|Verbinding verwerkt|Verwerkte verbindingen|Count|Totaal|Verwerkte verbindingen|Knooppunt|
|ConnectionActive|Actieve verbindingen|Count|Average|Actieve verbindingen|Knooppunt|
|Aanvraag behandeld|Afgehandelde aanvragen|Count|Totaal|Afgehandelde aanvragen|Knooppunt|
|Verwerkte blokken|Verwerkte blokken|Count|Totaal|Verwerkte blokken|Knooppunt|
|Verwerkte transacties|Verwerkte transacties|Count|Totaal|Verwerkte transacties|Knooppunt|
|Transacties in behandeling|Transacties in behandeling|Count|Average|Transacties in behandeling|Knooppunt|
|Transacties in de wachtrij|Transacties in wachtrijen|Count|Average|Transacties in wachtrijen|Knooppunt|



## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|verbonden clients|Verbonden clients|Count|Maximum||ShardId ShardId|
|totalcommandsprocessed|Totaal bewerkingen|Count|Totaal||ShardId ShardId|
|cachehits|Cache Hits|Count|Totaal||ShardId ShardId|
|cachemisses|Cache-missers|Count|Totaal||ShardId ShardId|
|cachemissrate|Cache Miss Rate|Percentage|cachemissrate||ShardId ShardId|
|getcommands|Krijgt|Count|Totaal||ShardId ShardId|
|setopdrachten instellen|Sets|Count|Totaal||ShardId ShardId|
|operationsPerSecond|Bewerkingen per seconde|Count|Maximum||ShardId ShardId|
|uitgezette sleutels|Uitgezette sleutels|Count|Totaal||ShardId ShardId|
|totalkeys|Totaal aantal toetsen|Count|Maximum||ShardId ShardId|
|verlopen sleutels|Verlopen sleutels|Count|Totaal||ShardId ShardId|
|gebruikt geheugen|Gebruikt geheugen|Bytes|Maximum||ShardId ShardId|
|gebruiktgeheugenpercentage|Percentage gebruikt geheugen|Percentage|Maximum||ShardId ShardId|
|gebruikte memoryRss|RSS gebruikt geheugen|Bytes|Maximum||ShardId ShardId|
|serverLaden|Serverbelasting|Percentage|Maximum||ShardId ShardId|
|cacheWrite|Cache schrijven|BytesPerSeconde|Maximum||ShardId ShardId|
|cacheLezen|Cache lezen|BytesPerSeconde|Maximum||ShardId ShardId|
|percentProcessorTime|CPU|Percentage|Maximum||ShardId ShardId|
|cacheLatentie|Cachelatentie Microseconden (Preview)|Count|Average||ShardId ShardId|
|fouten|Fouten|Count|Maximum||ShardId, ErrorType|
|connectedclients0|Verbonden clients (Shard 0)|Count|Maximum||Geen|
|totalcommandsprocessed0|Totale bewerkingen (shard 0)|Count|Totaal||Geen|
|cachehits0|Cache Hits (Shard 0)|Count|Totaal||Geen|
|cachemisses0|Cache-missers (Shard 0)|Count|Totaal||Geen|
|getcommands0|Krijgt (Shard 0)|Count|Totaal||Geen|
|setcommands0|Sets (Shard 0)|Count|Totaal||Geen|
|operationsPerSeconde0|Bewerkingen per seconde (shard 0)|Count|Maximum||Geen|
|uitgezetsleutels0|Uitgezette sleutels (Shard 0)|Count|Totaal||Geen|
|totalkeys0|Totaal aantal toetsen (shard 0)|Count|Maximum||Geen|
|verlopensleutels00|Verlopen sleutels (shard 0)|Count|Totaal||Geen|
|usedmemory0|Gebruikt geheugen (shard 0)|Bytes|Maximum||Geen|
|usedmemoryRss0|Gebruikte geheugen RSS (Shard 0)|Bytes|Maximum||Geen|
|serverLoad0|Serverbelasting (shard 0)|Percentage|Maximum||Geen|
|cacheWrite0|Cache schrijven (shard 0)|BytesPerSeconde|Maximum||Geen|
|cacheRead0|Cache lezen (shard 0)|BytesPerSeconde|Maximum||Geen|
|percentProcessorTime0|CPU (Shard 0)|Percentage|Maximum||Geen|
|verbonden clients1|Verbonden clients (Shard 1)|Count|Maximum||Geen|
|totalcommandsprocessed1|Totale bewerkingen (shard 1)|Count|Totaal||Geen|
|cachehits1|Cache Hits (Shard 1)|Count|Totaal||Geen|
|cachemisses1|Cache Missers (Shard 1)|Count|Totaal||Geen|
|getcommands1|Gets (Shard 1)|Count|Totaal||Geen|
|setcommands1|Sets (Shard 1)|Count|Totaal||Geen|
|operationsPerSeconde1|Bewerkingen per seconde (shard 1)|Count|Maximum||Geen|
|uitgezette sleutels1|Uitgezette sleutels (Shard 1)|Count|Totaal||Geen|
|totalkeys1|Totaal aantal toetsen (shard 1)|Count|Maximum||Geen|
|verlopen sleutels1|Verlopen sleutels (shard 1)|Count|Totaal||Geen|
|gebruikt geheugen1|Gebruikt geheugen (shard 1)|Bytes|Maximum||Geen|
|gebruikte memoryRss1|Gebruikte geheugen RSS (Shard 1)|Bytes|Maximum||Geen|
|serverLoad1|Serverbelasting (shard 1)|Percentage|Maximum||Geen|
|cacheWrite1|Cache schrijven (shard 1)|BytesPerSeconde|Maximum||Geen|
|cacheRead1|Cache lezen (shard 1)|BytesPerSeconde|Maximum||Geen|
|percentProcessorTime1|CPU (Shard 1)|Percentage|Maximum||Geen|
|verbonden clients2|Verbonden clients (Shard 2)|Count|Maximum||Geen|
|totalcommandsprocessed2|Totale bewerkingen (shard 2)|Count|Totaal||Geen|
|cachehits2|Cache Hits (Shard 2)|Count|Totaal||Geen|
|cachemisses2|Cache Missers (Shard 2)|Count|Totaal||Geen|
|getcommands2|Krijgt (Shard 2)|Count|Totaal||Geen|
|setcommands2|Sets (Shard 2)|Count|Totaal||Geen|
|operationsPerSeconde2|Bewerkingen per seconde (Shard 2)|Count|Maximum||Geen|
|uitgezette sleutels2|Uitgezette sleutels (Shard 2)|Count|Totaal||Geen|
|totalkeys2|Totaal aantal toetsen (shard 2)|Count|Maximum||Geen|
|verlopen sleutels2|Verlopen sleutels (Shard 2)|Count|Totaal||Geen|
|gebruikt geheugen2|Gebruikt geheugen (shard 2)|Bytes|Maximum||Geen|
|usedmemoryRss2|Gebruikte geheugen RSS (Shard 2)|Bytes|Maximum||Geen|
|serverLoad2|Serverbelasting (Shard 2)|Percentage|Maximum||Geen|
|cacheWrite2|Cache schrijven (Shard 2)|BytesPerSeconde|Maximum||Geen|
|cacheRead2|Cache lezen (Shard 2)|BytesPerSeconde|Maximum||Geen|
|percentProcessorTime2|CPU (Shard 2)|Percentage|Maximum||Geen|
|verbonden clients3|Verbonden clients (Shard 3)|Count|Maximum||Geen|
|totalcommandsprocessed3|Totale bewerkingen (shard 3)|Count|Totaal||Geen|
|cachehits3|Cache Hits (Shard 3)|Count|Totaal||Geen|
|cachemisses3|Cache Missers (Shard 3)|Count|Totaal||Geen|
|getcommands3|Krijgt (Shard 3)|Count|Totaal||Geen|
|setcommands3|Sets (Shard 3)|Count|Totaal||Geen|
|operationsPerSeconde3|Bewerkingen per seconde (Shard 3)|Count|Maximum||Geen|
|uitgezette sleutels3|Uitgezette sleutels (Shard 3)|Count|Totaal||Geen|
|totalkeys3|Totaal aantal toetsen (shard 3)|Count|Maximum||Geen|
|verlopen sleutels3|Verlopen sleutels (Shard 3)|Count|Totaal||Geen|
|gebruikt geheugen3|Gebruikt geheugen (shard 3)|Bytes|Maximum||Geen|
|usedmemoryRss3|Gebruikte geheugen RSS (Shard 3)|Bytes|Maximum||Geen|
|serverLoad3|Serverbelasting (Shard 3)|Percentage|Maximum||Geen|
|cacheWrite3|Cache schrijven (Shard 3)|BytesPerSeconde|Maximum||Geen|
|cacheLees3|Cache lezen (Shard 3)|BytesPerSeconde|Maximum||Geen|
|percentProcessorTime3|CPU (Shard 3)|Percentage|Maximum||Geen|
|connectedclients4|Verbonden clients (Shard 4)|Count|Maximum||Geen|
|totalcommandsprocessed4|Totale bewerkingen (shard 4)|Count|Totaal||Geen|
|cachehits4|Cache Hits (Shard 4)|Count|Totaal||Geen|
|cachemisses4|Cache Missers (Shard 4)|Count|Totaal||Geen|
|getcommands4|Krijgt (Shard 4)|Count|Totaal||Geen|
|setcommands4|Sets (Shard 4)|Count|Totaal||Geen|
|operationsPerSeconde4|Bewerkingen per seconde (Shard 4)|Count|Maximum||Geen|
|uitgezette sleutels4|Uitgezette sleutels (Shard 4)|Count|Totaal||Geen|
|totalkeys4|Totaal aantal toetsen (shard 4)|Count|Maximum||Geen|
|verlopen sleutels4|Verlopen sleutels (Shard 4)|Count|Totaal||Geen|
|gebruiktgeheugen4|Gebruikt geheugen (shard 4)|Bytes|Maximum||Geen|
|usedmemoryRss4|Gebruikte geheugen RSS (Shard 4)|Bytes|Maximum||Geen|
|serverLoad4|Serverbelasting (Shard 4)|Percentage|Maximum||Geen|
|cacheWrite4|Cache schrijven (Shard 4)|BytesPerSeconde|Maximum||Geen|
|cacheLees4|Cache lezen (Shard 4)|BytesPerSeconde|Maximum||Geen|
|percentProcessorTime4|CPU (Shard 4)|Percentage|Maximum||Geen|
|verbonden clients5|Verbonden clients (Shard 5)|Count|Maximum||Geen|
|totalcommandsprocessed5|Totale bewerkingen (shard 5)|Count|Totaal||Geen|
|cachehits5|Cache Hits (Shard 5)|Count|Totaal||Geen|
|cachemisses5|Cache Missers (Shard 5)|Count|Totaal||Geen|
|getcommands5|Krijgt (Shard 5)|Count|Totaal||Geen|
|setcommands5|Sets (Shard 5)|Count|Totaal||Geen|
|operationsPerSeconde5|Bewerkingen per seconde (Shard 5)|Count|Maximum||Geen|
|uitgezette sleutels5|Uitgezette sleutels (Shard 5)|Count|Totaal||Geen|
|totalkeys5|Totaal aantal toetsen (shard 5)|Count|Maximum||Geen|
|verlopen sleutels5|Verlopen sleutels (Shard 5)|Count|Totaal||Geen|
|gebruikt geheugen5|Gebruikt geheugen (shard 5)|Bytes|Maximum||Geen|
|usedmemoryRss5|Gebruikte geheugen RSS (Shard 5)|Bytes|Maximum||Geen|
|serverLoad5|Serverbelasting (Shard 5)|Percentage|Maximum||Geen|
|cacheWrite5|Cache schrijven (Shard 5)|BytesPerSeconde|Maximum||Geen|
|cacheLees5|Cache lezen (Shard 5)|BytesPerSeconde|Maximum||Geen|
|percentProcessorTime5|CPU (Shard 5)|Percentage|Maximum||Geen|
|verbonden clients6|Verbonden clients (Shard 6)|Count|Maximum||Geen|
|totalcommandsprocessed6|Totale bewerkingen (shard 6)|Count|Totaal||Geen|
|cachehits6|Cache Hits (Shard 6)|Count|Totaal||Geen|
|cachemisses6|Cache Missers (Shard 6)|Count|Totaal||Geen|
|getcommands6|Krijgt (Shard 6)|Count|Totaal||Geen|
|setcommands6|Sets (Shard 6)|Count|Totaal||Geen|
|operationsPerSeconde6|Bewerkingen per seconde (Shard 6)|Count|Maximum||Geen|
|uitgezette sleutels6|Uitgezette sleutels (Shard 6)|Count|Totaal||Geen|
|totalkeys6|Totaal aantal toetsen (shard 6)|Count|Maximum||Geen|
|verlopen sleutels6|Verlopen sleutels (Shard 6)|Count|Totaal||Geen|
|gebruikt geheugen6|Gebruikt geheugen (shard 6)|Bytes|Maximum||Geen|
|usedmemoryRss6|Gebruikte geheugen RSS (Shard 6)|Bytes|Maximum||Geen|
|serverLoad6|Serverbelasting (shard 6)|Percentage|Maximum||Geen|
|cacheWrite6|Cache schrijven (Shard 6)|BytesPerSeconde|Maximum||Geen|
|cacheRead6|Cache lezen (Shard 6)|BytesPerSeconde|Maximum||Geen|
|percentProcessorTime6|CPU (Shard 6)|Percentage|Maximum||Geen|
|verbondenclients7|Verbonden clients (Shard 7)|Count|Maximum||Geen|
|totalcommandsprocessed7|Totale bewerkingen (shard 7)|Count|Totaal||Geen|
|cachehits7|Cache Hits (Shard 7)|Count|Totaal||Geen|
|cachemisses7|Cache Missers (Shard 7)|Count|Totaal||Geen|
|getcommands7|Krijgt (Shard 7)|Count|Totaal||Geen|
|setcommands7|Sets (Shard 7)|Count|Totaal||Geen|
|operationsPerSeconde7|Bewerkingen per seconde (Shard 7)|Count|Maximum||Geen|
|uitgezette sleutels7|Uitgezette sleutels (Shard 7)|Count|Totaal||Geen|
|totalkeys7|Totaal aantal toetsen (shard 7)|Count|Maximum||Geen|
|verlopen sleutels7|Verlopen sleutels (Shard 7)|Count|Totaal||Geen|
|gebruikt geheugen7|Gebruikt geheugen (shard 7)|Bytes|Maximum||Geen|
|usedmemoryRss7|Gebruikte geheugen RSS (Shard 7)|Bytes|Maximum||Geen|
|serverLoad7|Serverbelasting (shard 7)|Percentage|Maximum||Geen|
|cacheWrite7|Cache schrijven (Shard 7)|BytesPerSeconde|Maximum||Geen|
|cacheLees7|Cache lezen (Shard 7)|BytesPerSeconde|Maximum||Geen|
|percentProcessorTime7|CPU (Shard 7)|Percentage|Maximum||Geen|
|verbondenclients8|Verbonden clients (Shard 8)|Count|Maximum||Geen|
|totalcommandsprocessed8|Totale bewerkingen (shard 8)|Count|Totaal||Geen|
|cachehits8|Cache Hits (Shard 8)|Count|Totaal||Geen|
|cachemisses8|Cache Missers (Shard 8)|Count|Totaal||Geen|
|getcommands8|Krijgt (Shard 8)|Count|Totaal||Geen|
|setcommands8|Sets (Shard 8)|Count|Totaal||Geen|
|operationsPerSeconde8|Bewerkingen per seconde (Shard 8)|Count|Maximum||Geen|
|uitgezette sleutels8|Uitgezette sleutels (Shard 8)|Count|Totaal||Geen|
|totalkeys8|Totaal aantal toetsen (shard 8)|Count|Maximum||Geen|
|verlopen sleutels8|Verlopen sleutels (Shard 8)|Count|Totaal||Geen|
|gebruikt geheugen8|Gebruikt geheugen (shard 8)|Bytes|Maximum||Geen|
|usedmemoryRss8|Gebruikte geheugen RSS (Shard 8)|Bytes|Maximum||Geen|
|serverLoad8|Serverbelasting (shard 8)|Percentage|Maximum||Geen|
|cacheWrite8|Cache schrijven (Shard 8)|BytesPerSeconde|Maximum||Geen|
|cacheRead8|Cache lezen (Shard 8)|BytesPerSeconde|Maximum||Geen|
|percentProcessorTime8|CPU (Shard 8)|Percentage|Maximum||Geen|
|verbonden clients9|Verbonden clients (Shard 9)|Count|Maximum||Geen|
|totalcommandsprocessed9|Totale bewerkingen (shard 9)|Count|Totaal||Geen|
|cachehits9|Cache Hits (Shard 9)|Count|Totaal||Geen|
|cachemisses9|Cache Missers (Shard 9)|Count|Totaal||Geen|
|getcommands9|Krijgt (Shard 9)|Count|Totaal||Geen|
|setcommands9|Sets (Shard 9)|Count|Totaal||Geen|
|operationsPerSeconde9|Bewerkingen per seconde (Shard 9)|Count|Maximum||Geen|
|uitgezette sleutels9|Uitgezette sleutels (Scherf 9)|Count|Totaal||Geen|
|totalkeys9|Totaal aantal toetsen (shard 9)|Count|Maximum||Geen|
|verlopen sleutels9|Verlopen sleutels (Shard 9)|Count|Totaal||Geen|
|usedmemory9|Gebruikt geheugen (shard 9)|Bytes|Maximum||Geen|
|usedmemoryRss9|Gebruikte geheugen RSS (Shard 9)|Bytes|Maximum||Geen|
|serverLoad9|Serverbelasting (shard 9)|Percentage|Maximum||Geen|
|cacheWrite9|Cache schrijven (shard 9)|BytesPerSeconde|Maximum||Geen|
|cacheRead9|Cache lezen (shard 9)|BytesPerSeconde|Maximum||Geen|
|percentProcessorTime9|CPU (Shard 9)|Percentage|Maximum||Geen|




## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallbeleid

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Aantal firewallaanvragen voor webtoepassingen|Count|Totaal|Het aantal clientaanvragen dat door de webtoepassingsfirewall wordt verwerkt|PolicyName, RuleName, Actie|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Percentage|Average|Het percentage toegewezen compute units dat momenteel door de virtuele machine(s) wordt gebruikt.|Geen|
|Netwerk in|Netwerk in|Bytes|Totaal|Het aantal bytes dat door de Virtuele Machine(s) (Binnenkomend verkeer) op alle netwerkinterfaces is ontvangen.|Geen|
|Netwerk uit|Netwerk uit|Bytes|Totaal|Het aantal bytes op alle netwerkinterfaces door de Virtuele Machine(s) (Uitgaand Verkeer).|Geen|
|Bytes/Seconde schijfgelezen|Schijf lezen|BytesPerSeconde|Average|Gemiddelde bytes die van schijf tijdens controleperiode worden gelezen.|Geen|
|Bytes voor schijfschrijfbytes per seconde|Schijfschrijven|BytesPerSeconde|Average|Gemiddelde bytes die tijdens de controleperiode naar de schijf zijn geschreven.|Geen|
|Bewerkingen voor schijflezen/sec|Bewerkingen voor schijflezen/sec|CountPerSeconde|Average|Schijf lees IOPS.|Geen|
|Bewerkingen voor schijfschrijfbewerkingen/sec|Bewerkingen voor schijfschrijfbewerkingen/sec|CountPerSeconde|Average|Schijf schrijf IOPS.|Geen|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/rollen

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Percentage|Average|Het percentage toegewezen compute units dat momenteel door de virtuele machine(s) wordt gebruikt.|RoleInstanceId|
|Netwerk in|Netwerk in|Bytes|Totaal|Het aantal bytes dat door de Virtuele Machine(s) (Binnenkomend verkeer) op alle netwerkinterfaces is ontvangen.|RoleInstanceId|
|Netwerk uit|Netwerk uit|Bytes|Totaal|Het aantal bytes op alle netwerkinterfaces door de Virtuele Machine(s) (Uitgaand Verkeer).|RoleInstanceId|
|Bytes/Seconde schijfgelezen|Schijf lezen|BytesPerSeconde|Average|Gemiddelde bytes die van schijf tijdens controleperiode worden gelezen.|RoleInstanceId|
|Bytes voor schijfschrijfbytes per seconde|Schijfschrijven|BytesPerSeconde|Average|Gemiddelde bytes die tijdens de controleperiode naar de schijf zijn geschreven.|RoleInstanceId|
|Bewerkingen voor schijflezen/sec|Bewerkingen voor schijflezen/sec|CountPerSeconde|Average|Schijf lees IOPS.|RoleInstanceId|
|Bewerkingen voor schijfschrijfbewerkingen/sec|Bewerkingen voor schijfschrijfbewerkingen/sec|CountPerSeconde|Average|Schijf schrijf IOPS.|RoleInstanceId|



## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Gebruikte capaciteit|Gebruikte capaciteit|Bytes|Average|Gebruikte capaciteit account|Geen|
|Transacties|Transacties|Count|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. De dimensie ResponseType gebruiken voor het aantal verschillende typen reacties.|ResponseType,GeoType,ApiName,Authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid binnenkomende gegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|GeoType, ApiName, Verificatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgangsgegevens in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|GeoType, ApiName, Verificatie|
|SuccessServerLatentie|Geslaagde serverlatentie|Milliseconden|Average|De latentie die azure-opslag gebruikt om een succesvolle aanvraag in milliseconden te verwerken. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency.|GeoType, ApiName, Verificatie|
|SuccesE2ELatentie|Success E2E Latency|Milliseconden|Average|De end-to-end latentie van succesvolle aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|GeoType, ApiName, Verificatie|
|Beschikbaarheid|Beschikbaarheid|Percentage|Average|Het percentage beschikbaarheid voor de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|GeoType, ApiName, Verificatie|

## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BlobCapaciteit|Blob-capaciteit|Bytes|Average|De hoeveelheid opslagruimte die wordt gebruikt door de Blob-service van het opslagaccount in bytes.|BlobType, Laag|
|BlobCount|Blobtelling|Count|Average|Het aantal Blob in de Blob-service van het opslagaccount.|BlobType, Laag|
|ContainerCount ContainerCount|Aantal Blob-containers|Count|Average|Het aantal containers in de Blob-service van het opslagaccount.|Geen|
|Indexcapaciteit|Indexcapaciteit|Bytes|Average|De hoeveelheid opslagruimte die wordt gebruikt door de ADLS Gen2 -index (Hiërarchische) index in bytes.|Geen|
|Transacties|Transacties|Count|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. De dimensie ResponseType gebruiken voor het aantal verschillende typen reacties.|ResponseType,GeoType,ApiName,Authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid binnenkomende gegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|GeoType, ApiName, Verificatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgangsgegevens in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|GeoType, ApiName, Verificatie|
|SuccessServerLatentie|Geslaagde serverlatentie|Milliseconden|Average|De latentie die azure-opslag gebruikt om een succesvolle aanvraag in milliseconden te verwerken. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency.|GeoType, ApiName, Verificatie|
|SuccesE2ELatentie|Success E2E Latency|Milliseconden|Average|De end-to-end latentie van succesvolle aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|GeoType, ApiName, Verificatie|
|Beschikbaarheid|Beschikbaarheid|Percentage|Average|Het percentage beschikbaarheid voor de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|GeoType, ApiName, Verificatie|

## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Tabelcapaciteit|Tabelcapaciteit|Bytes|Average|De hoeveelheid opslagruimte die wordt gebruikt door de tabelservice van het opslagaccount in bytes.|Geen|
|Tabeltelling|Tabelaantal|Count|Average|Het aantal tabel in de tabelservice van het opslagaccount.|Geen|
|Tabelentiteittelling|Aantal tabelentiteiten|Count|Average|Het aantal tabelentiteiten in de tabelservice van het opslagaccount.|Geen|
|Transacties|Transacties|Count|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. De dimensie ResponseType gebruiken voor het aantal verschillende typen reacties.|ResponseType,GeoType,ApiName,Authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid binnenkomende gegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|GeoType, ApiName, Verificatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgangsgegevens in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|GeoType, ApiName, Verificatie|
|SuccessServerLatentie|Geslaagde serverlatentie|Milliseconden|Average|De latentie die azure-opslag gebruikt om een succesvolle aanvraag in milliseconden te verwerken. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency.|GeoType, ApiName, Verificatie|
|SuccesE2ELatentie|Success E2E Latency|Milliseconden|Average|De end-to-end latentie van succesvolle aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|GeoType, ApiName, Verificatie|
|Beschikbaarheid|Beschikbaarheid|Percentage|Average|Het percentage beschikbaarheid voor de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|GeoType, ApiName, Verificatie|

## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Bestandscapaciteit|Bestandscapaciteit|Bytes|Average|De hoeveelheid opslagruimte die wordt gebruikt door de bestandsservice van het opslagaccount in bytes.|FileShare|
|FileCount|Aantal bestanden|Count|Average|Het aantal bestanden in de bestandsservice van het opslagaccount.|FileShare|
|FileShareCount|Aantal bestandsdelen|Count|Average|Het aantal bestandsshares in de bestandsservice van de opslagrekening.|Geen|
|FileShareSnapshotCount|Momentopnameaantal momentopnamen voor bestandsshare|Count|Average|Het aantal momentopnamen dat aanwezig is op het aandeel in de Bestandenservice van het opslagaccount.|FileShare|
|FileShareSnapshotSize|Momentopnamemomentopname voor bestandsshare|Bytes|Average|De hoeveelheid opslagruimte die wordt gebruikt door de momentopnamen in de bestandsservice van het opslagaccount in bytes.|FileShare|
|FileShareQuota|Quotumgrootte voor bestandsshare|Bytes|Average|De bovengrens voor de hoeveelheid opslagruimte die door Azure Files Service in bytes kan worden gebruikt.|FileShare|
|Transacties|Transacties|Count|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. De dimensie ResponseType gebruiken voor het aantal verschillende typen reacties.|ResponseType,GeoType,ApiName,Authentication,FileShare|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid binnenkomende gegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype,ApiName, Authenticatie,FileShare|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgangsgegevens in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype,ApiName, Authenticatie,FileShare|
|SuccessServerLatentie|Geslaagde serverlatentie|Milliseconden|Average|De latentie die azure-opslag gebruikt om een succesvolle aanvraag in milliseconden te verwerken. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency.|Geotype,ApiName, Authenticatie,FileShare|
|SuccesE2ELatentie|Success E2E Latency|Milliseconden|Average|De end-to-end latentie van succesvolle aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype,ApiName, Authenticatie,FileShare|
|Beschikbaarheid|Beschikbaarheid|Percentage|Average|Het percentage beschikbaarheid voor de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype,ApiName, Authenticatie,FileShare|

## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Wachtrijcapaciteit|Wachtrijcapaciteit|Bytes|Average|De hoeveelheid opslagruimte die wordt gebruikt door de wachtrijservice van het opslagaccount in bytes.|Geen|
|Aantal wachtrijen|Aantal wachtrijen|Count|Average|Het aantal wachtrijen in de wachtrijservice van het opslagaccount.|Geen|
|Aantal queuemessage's|Aantal wachtrijberichten|Count|Average|Het geschatte aantal wachtrijberichten in de wachtrijservice van het opslagaccount.|Geen|
|Transacties|Transacties|Count|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. De dimensie ResponseType gebruiken voor het aantal verschillende typen reacties.|ResponseType,GeoType,ApiName,Authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid binnenkomende gegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|GeoType, ApiName, Verificatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgangsgegevens in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|GeoType, ApiName, Verificatie|
|SuccessServerLatentie|Geslaagde serverlatentie|Milliseconden|Average|De latentie die azure-opslag gebruikt om een succesvolle aanvraag in milliseconden te verwerken. Deze waarde bevat niet de netwerklatentie die is opgegeven in SuccessE2ELatency.|GeoType, ApiName, Verificatie|
|SuccesE2ELatentie|Success E2E Latency|Milliseconden|Average|De end-to-end latentie van succesvolle aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|GeoType, ApiName, Verificatie|
|Beschikbaarheid|Beschikbaarheid|Percentage|Average|Het percentage beschikbaarheid voor de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|GeoType, ApiName, Verificatie|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Totaal Aantal oproepen|Totaal aantal oproepen|Count|Totaal|Totaal aantal oproepen.|ApiName, OperationName,Regio|
|Succesvol bellen|Succesvolle gesprekken|Count|Totaal|Aantal succesvolle gesprekken.|ApiName, OperationName,Regio|
|Totaalfouten|Totaal aantal fouten|Count|Totaal|Totaal aantal oproepen met foutrespons (HTTP-antwoordcode 4xx of 5xx).|ApiName, OperationName,Regio|
|Geblokkeerde oproepen|Geblokkeerde oproepen|Count|Totaal|Aantal oproepen dat de limiet voor het tarief of het quotum heeft overschreden.|ApiName, OperationName,Regio|
|Serverfouten|Serverfouten|Count|Totaal|Aantal oproepen met interne servicefout (HTTP-antwoordcode 5xx).|ApiName, OperationName,Regio|
|ClientFouten|Clientfouten|Count|Totaal|Aantal oproepen met clientside error (HTTP response code 4xx).|ApiName, OperationName,Regio|
|DataIn|Gegevens in|Bytes|Totaal|Grootte van binnenkomende gegevens in bytes.|ApiName, OperationName,Regio|
|Gegevensout|Gegevens uit|Bytes|Totaal|Grootte van uitgaande gegevens in bytes.|ApiName, OperationName,Regio|
|Latentie|Latentie|Milliseconden|Average|Latentie in milliseconden.|ApiName, OperationName,Regio|
|TotalTokencalls|Totaal aantal tokenoproepen|Count|Totaal|Totaal aantal tokenoproepen.|ApiName, OperationName,Regio|
|Tekensvertaald|Tekens vertaald|Count|Totaal|Totaal aantal tekens in binnenkomende tekstaanvraag.|ApiName, OperationName,Regio|
|TekensGetraind|Personages getraind|Count|Totaal|Totaal aantal getrainde tekens.|ApiName, OperationName,Regio|
|SessiesessieDuur|Sessieduur van spraaksessies|Seconden|Totaal|Totale duur van de spraaksessie in seconden.|ApiName, OperationName,Regio|
|TotaalTransacties|Totaal aantal transacties|Count|Totaal|Totaal aantal transacties.|Geen|
|Verwerkte afbeeldingen|Verwerkte afbeeldingen|Count|Totaal| Aantal transacties voor beeldverwerking.|ApiName, FeatureName, Kanaal, Regio|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Percentage|Average|Het percentage toegewezen compute units dat momenteel wordt gebruikt door de virtuele machine(s)|Geen|
|Netwerk in|Netwerk in factureerbaar (afgeschaft)|Bytes|Totaal|Het aantal factureerbare bytes dat op alle netwerkinterfaces is ontvangen door de Virtuele Machine(s) (Binnenkomend verkeer) (afgeschaft)|Geen|
|Netwerk uit|Network Out Billable (Afgeschaft)|Bytes|Totaal|Het aantal factureerbare bytes op alle netwerkinterfaces door de Virtuele Machine(s) (Uitgaand Verkeer) (Afgeschaft)|Geen|
|Bytes voor schijflezen|Bytes voor schijflezen|Bytes|Totaal|Bytes die van schijf tijdens controleperiode worden gelezen|Geen|
|Schrijfbytes voor schijf|Schrijfbytes voor schijf|Bytes|Totaal|Bytes die tijdens de controleperiode naar schijf zijn geschreven|Geen|
|Bewerkingen voor schijflezen/sec|Bewerkingen voor schijflezen/sec|CountPerSeconde|Average|Schijf lees IOPS|Geen|
|Bewerkingen voor schijfschrijfbewerkingen/sec|Bewerkingen voor schijfschrijfbewerkingen/sec|CountPerSeconde|Average|SchijfschrijfiOPS|Geen|
|Resterende CPU-credits|Resterende CPU-credits|Count|Average|Totaal aantal credits beschikbaar om te barsten|Geen|
|CPU-credits verbruikt|CPU-credits verbruikt|Count|Average|Totaal aantal credits verbruikt door de virtuele machine|Geen|
|Bytes per seconde per schijf gelezen|Bytes/Sec gegevensschijf lezen [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSeconde|Average|Bytes/seconde die tijdens de controleperiode vanaf één schijf wordt gelezen|SlotId (SlotId)|
|Schrijfbytes per schijf per seconde|Bytes/Sec voor gegevensschijfschrijven [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSeconde|Average|Bytes/sec die tijdens de controleperiode naar één schijf is geschreven|SlotId (SlotId)|
|Leesbewerkingen per schijf per seconde|Gegevensschijfleesbewerkingen/Sec [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSeconde|Average|IOPS lezen vanaf één schijf tijdens de controleperiode|SlotId (SlotId)|
|Schrijfbewerkingen per schijf per seconde|Gegevensschijfschrijfbewerkingen/Sec [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSeconde|Average|IOPS schrijven vanaf één schijf tijdens de controleperiode|SlotId (SlotId)|
|QD per schijf|Gegevensschijf QD [(afgeschaft)](portal-disk-metrics-deprecation.md)](portal-disk-metrics-deprecation.md)|Count|Average|Gegevensschijfwachtrijdiepte(of wachtrijlengte)|SlotId (SlotId)|
|Bytes per seconde voor gelezen besturingssysteem per schijf|Bytes/Sec voor gelezen SCHIJVEN [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSeconde|Average|Bytes/seconde die van één schijf tijdens controleperiode voor OS-schijf worden gelezen|Geen|
|Schrijven bytes per schijf per seconde per schijf|Bytes/Sec voor schrijven in de schijf van HET BE [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSeconde|Average|Bytes/sec die naar één schijf is geschreven tijdens de controleperiode voor de os-schijf|Geen|
|Leesbewerkingen van het besturingssysteem per schijf per seconde|Bewerkingen voor lezen van schijven/sec van het besturingssysteem [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSeconde|Average|IOPS lezen vanaf één schijf tijdens de controleperiode voor de OS-schijf|Geen|
|Schrijfbewerkingen per schijf per seconde|Bewerkingen voor schrijven van OS-schijven/Sec [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSeconde|Average|IOPS schrijven vanaf één schijf tijdens de controleperiode voor de OS-schijf|Geen|
|OS Per schijf QD|OS Disk QD [(afgeschaft)](portal-disk-metrics-deprecation.md)|Count|Average|Diepte van de schijfwachtrij(of wachtrijlengte)|Geen|
|Bytes/seconde gegevensschijf gelezen|Bytes/Seconde voor gegevensschijf lezen (voorbeeld)|CountPerSeconde|Average|Bytes/seconde die tijdens de controleperiode vanaf één schijf wordt gelezen|Lun|
|Schrijfbytes voor gegevensschijf per seconde|Bytes/sec voor gegevensschijf schrijven (voorbeeld)|CountPerSeconde|Average|Bytes/sec die tijdens de controleperiode naar één schijf is geschreven|Lun|
|Leesbewerkingen gegevensschijf/sec|Gegevensschijfleesbewerkingen/Sec (voorbeeld)|CountPerSeconde|Average|IOPS lezen vanaf één schijf tijdens de controleperiode|Lun|
|Schrijfbewerkingen voor gegevensschijf/sec|Schrijfbewerkingen voor gegevensschijf/seconde (voorbeeld)|CountPerSeconde|Average|IOPS schrijven vanaf één schijf tijdens de controleperiode|Lun|
|Wachtrijlengte van gegevensschijf|Gegevensschijfwachtrijdiepte (voorbeeld)|Count|Average|Gegevensschijfwachtrijdiepte(of wachtrijlengte)|Lun|
|Bytes/seconde voor gelezen osschijflezen|Bytes/Sec voor gelezen OS-schijf (voorbeeld)|CountPerSeconde|Average|Bytes/seconde die van één schijf tijdens controleperiode voor OS-schijf worden gelezen|Geen|
|Bytes voor schrijven van os-schijven per seconde|Bytes/sec voor schrijven in de os-schijf (voorbeeld)|CountPerSeconde|Average|Bytes/sec die naar één schijf is geschreven tijdens de controleperiode voor de os-schijf|Geen|
|Leesbewerkingen voor schijven van BE/Sec|Bewerkingen voor lezen van de schijf van BE/Sec (voorbeeld)|CountPerSeconde|Average|IOPS lezen vanaf één schijf tijdens de controleperiode voor de OS-schijf|Geen|
|Schrijfbewerkingen voor osschijfschrijfbewerkingen/sec|Be-disk schrijfbewerkingen/sec (voorbeeld)|CountPerSeconde|Average|IOPS schrijven vanaf één schijf tijdens de controleperiode voor de OS-schijf|Geen|
|Wachtrijlengte van besturingssysteemschijf|Diepte van de schijfwachtrij van HET BE (voorbeeld)|Count|Average|Diepte van de schijfwachtrij(of wachtrijlengte)|Geen|
|Binnenkomende stromen|Binnenkomende stromen|Count|Average|Binnenkomende stromen zijn het aantal stroomstromen in de inkomende richting (verkeer dat naar de VM gaat)|Geen|
|Uitgaande stromen|Uitgaande stromen|Count|Average|Uitgaande stromen zijn het aantal stroomstromen in de uitgaande richting (verkeer dat uit de VM gaat)|Geen|
|Maximale creatiesnelheid inkomende stromen|Maximale creatiesnelheid inkomende stromen|CountPerSeconde|Average|Het maximale creatiepercentage van inkomende stromen (verkeer dat naar de VM gaat)|Geen|
|Maximale creatiesnelheid voor uitgaande stromen|Maximale creatiesnelheid voor uitgaande stromen|CountPerSeconde|Average|Het maximale creatiepercentage van uitgaande stromen (verkeer dat uit de VM gaat)|Geen|
|Premium Data Disk Cache Read Hit|Gelezen hit in de schijfcache van Premium-gegevens (voorbeeld)|Percentage|Average|Premium Data Disk Cache Read Hit|Lun|
|Premium Data Disk Cache Lees Miss|Leesmisser van de schijfcache van Premium-gegevens (voorbeeld)|Percentage|Average|Premium Data Disk Cache Lees Miss|Lun|
|Premium OS Disk Cache Read Hit|Premium OS Disk Cache Read Hit (Preview)|Percentage|Average|Premium OS Disk Cache Read Hit|Geen|
|Premium OS Disk Cache Lees Miss|Premium OS Disk Cache Lees Miss (Preview)|Percentage|Average|Premium OS Disk Cache Lees Miss|Geen|
|Netwerk in totaal|Netwerk in totaal|Bytes|Totaal|Het aantal bytes dat wordt ontvangen op alle netwerkinterfaces door de virtuele machine(s) (Binnenkomend verkeer)|Geen|
|Netwerk totaal|Netwerk totaal|Bytes|Totaal|Het aantal bytes op alle netwerkinterfaces door de virtuele machine(s) (Uitgaand Verkeer)|Geen|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Percentage|Average|Het percentage toegewezen compute units dat momenteel wordt gebruikt door de virtuele machine(s)|VMName|
|Netwerk in|Netwerk in factureerbaar (afgeschaft)|Bytes|Totaal|Het aantal factureerbare bytes dat op alle netwerkinterfaces is ontvangen door de Virtuele Machine(s) (Binnenkomend verkeer) (afgeschaft)|VMName|
|Netwerk uit|Network Out Billable (Afgeschaft)|Bytes|Totaal|Het aantal factureerbare bytes op alle netwerkinterfaces door de Virtuele Machine(s) (Uitgaand Verkeer) (Afgeschaft)|VMName|
|Bytes voor schijflezen|Bytes voor schijflezen|Bytes|Totaal|Bytes die van schijf tijdens controleperiode worden gelezen|VMName|
|Schrijfbytes voor schijf|Schrijfbytes voor schijf|Bytes|Totaal|Bytes die tijdens de controleperiode naar schijf zijn geschreven|VMName|
|Bewerkingen voor schijflezen/sec|Bewerkingen voor schijflezen/sec|CountPerSeconde|Average|Schijf lees IOPS|VMName|
|Bewerkingen voor schijfschrijfbewerkingen/sec|Bewerkingen voor schijfschrijfbewerkingen/sec|CountPerSeconde|Average|SchijfschrijfiOPS|VMName|
|Resterende CPU-credits|Resterende CPU-credits|Count|Average|Totaal aantal credits beschikbaar om te barsten|Geen|
|CPU-credits verbruikt|CPU-credits verbruikt|Count|Average|Totaal aantal credits verbruikt door de virtuele machine|Geen|
|Bytes per seconde per schijf gelezen|Bytes/Sec gegevensschijf lezen [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSeconde|Average|Bytes/seconde die tijdens de controleperiode vanaf één schijf wordt gelezen|SlotId (SlotId)|
|Schrijfbytes per schijf per seconde|Bytes/Sec voor gegevensschijfschrijven [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSeconde|Average|Bytes/sec die tijdens de controleperiode naar één schijf is geschreven|SlotId (SlotId)|
|Leesbewerkingen per schijf per seconde|Gegevensschijfleesbewerkingen/Sec [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSeconde|Average|IOPS lezen vanaf één schijf tijdens de controleperiode|SlotId (SlotId)|
|Schrijfbewerkingen per schijf per seconde|Gegevensschijfschrijfbewerkingen/Sec [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSeconde|Average|IOPS schrijven vanaf één schijf tijdens de controleperiode|SlotId (SlotId)|
|QD per schijf|Gegevensschijf QD [(afgeschaft)](portal-disk-metrics-deprecation.md)|Count|Average|Gegevensschijfwachtrijdiepte(of wachtrijlengte)|SlotId (SlotId)|
|Bytes per seconde voor gelezen besturingssysteem per schijf|Bytes/Sec voor gelezen SCHIJVEN [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSeconde|Average|Bytes/seconde die van één schijf tijdens controleperiode voor OS-schijf worden gelezen|Geen|
|Schrijven bytes per schijf per seconde per schijf|Bytes/Sec voor schrijven in de schijf van HET BE [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSeconde|Average|Bytes/sec die naar één schijf is geschreven tijdens de controleperiode voor de os-schijf|Geen|
|Leesbewerkingen van het besturingssysteem per schijf per seconde|Bewerkingen voor lezen van schijven/sec van het besturingssysteem [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSeconde|Average|IOPS lezen vanaf één schijf tijdens de controleperiode voor de OS-schijf|Geen|
|Schrijfbewerkingen per schijf per seconde|Bewerkingen voor schrijven van OS-schijven/Sec [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSeconde|Average|IOPS schrijven vanaf één schijf tijdens de controleperiode voor de OS-schijf|Geen|
|OS Per schijf QD|OS Disk QD [(afgeschaft)](portal-disk-metrics-deprecation.md)|Count|Average|Diepte van de schijfwachtrij(of wachtrijlengte)|Geen|
|Bytes/seconde gegevensschijf gelezen|Bytes/Seconde voor gegevensschijf lezen (voorbeeld)|CountPerSeconde|Average|Bytes/seconde die tijdens de controleperiode vanaf één schijf wordt gelezen|LUN, VMName|
|Schrijfbytes voor gegevensschijf per seconde|Bytes/sec voor gegevensschijf schrijven (voorbeeld)|CountPerSeconde|Average|Bytes/sec die tijdens de controleperiode naar één schijf is geschreven|LUN, VMName|
|Leesbewerkingen gegevensschijf/sec|Gegevensschijfleesbewerkingen/Sec (voorbeeld)|CountPerSeconde|Average|IOPS lezen vanaf één schijf tijdens de controleperiode|LUN, VMName|
|Schrijfbewerkingen voor gegevensschijf/sec|Schrijfbewerkingen voor gegevensschijf/seconde (voorbeeld)|CountPerSeconde|Average|IOPS schrijven vanaf één schijf tijdens de controleperiode|LUN, VMName|
|Wachtrijlengte van gegevensschijf|Gegevensschijfwachtrijdiepte (voorbeeld)|Count|Average|Gegevensschijfwachtrijdiepte(of wachtrijlengte)|LUN, VMName|
|Bytes/seconde voor gelezen osschijflezen|Bytes/Sec voor gelezen OS-schijf (voorbeeld)|CountPerSeconde|Average|Bytes/seconde die van één schijf tijdens controleperiode voor OS-schijf worden gelezen|VMName|
|Bytes voor schrijven van os-schijven per seconde|Bytes/sec voor schrijven in de os-schijf (voorbeeld)|CountPerSeconde|Average|Bytes/sec die naar één schijf is geschreven tijdens de controleperiode voor de os-schijf|VMName|
|Leesbewerkingen voor schijven van BE/Sec|Bewerkingen voor lezen van de schijf van BE/Sec (voorbeeld)|CountPerSeconde|Average|IOPS lezen vanaf één schijf tijdens de controleperiode voor de OS-schijf|VMName|
|Schrijfbewerkingen voor osschijfschrijfbewerkingen/sec|Be-disk schrijfbewerkingen/sec (voorbeeld)|CountPerSeconde|Average|IOPS schrijven vanaf één schijf tijdens de controleperiode voor de OS-schijf|VMName|
|Wachtrijlengte van besturingssysteemschijf|Diepte van de schijfwachtrij van HET BE (voorbeeld)|Count|Average|Diepte van de schijfwachtrij(of wachtrijlengte)|VMName|
|Binnenkomende stromen|Binnenkomende stromen|Count|Average|Binnenkomende stromen zijn het aantal stroomstromen in de inkomende richting (verkeer dat naar de VM gaat)|VMName|
|Uitgaande stromen|Uitgaande stromen|Count|Average|Uitgaande stromen zijn het aantal stroomstromen in de uitgaande richting (verkeer dat uit de VM gaat)|VMName|
|Maximale creatiesnelheid inkomende stromen|Maximale creatiesnelheid inkomende stromen|CountPerSeconde|Average|Het maximale creatiepercentage van inkomende stromen (verkeer dat naar de VM gaat)|VMName|
|Maximale creatiesnelheid voor uitgaande stromen|Maximale creatiesnelheid voor uitgaande stromen|CountPerSeconde|Average|Het maximale creatiepercentage van uitgaande stromen (verkeer dat uit de VM gaat)|VMName|
|Premium Data Disk Cache Read Hit|Gelezen hit in de schijfcache van Premium-gegevens (voorbeeld)|Percentage|Average|Premium Data Disk Cache Read Hit|LUN, VMName|
|Premium Data Disk Cache Lees Miss|Leesmisser van de schijfcache van Premium-gegevens (voorbeeld)|Percentage|Average|Premium Data Disk Cache Lees Miss|LUN, VMName|
|Premium OS Disk Cache Read Hit|Premium OS Disk Cache Read Hit (Preview)|Percentage|Average|Premium OS Disk Cache Read Hit|VMName|
|Premium OS Disk Cache Lees Miss|Premium OS Disk Cache Lees Miss (Preview)|Percentage|Average|Premium OS Disk Cache Lees Miss|VMName|
|Netwerk in totaal|Netwerk in totaal|Bytes|Totaal|Het aantal bytes dat wordt ontvangen op alle netwerkinterfaces door de virtuele machine(s) (Binnenkomend verkeer)|VMName|
|Netwerk totaal|Netwerk totaal|Bytes|Totaal|Het aantal bytes op alle netwerkinterfaces door de virtuele machine(s) (Uitgaand Verkeer)|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Percentage|Average|Het percentage toegewezen compute units dat momenteel wordt gebruikt door de virtuele machine(s)|Geen|
|Netwerk in|Netwerk in factureerbaar (afgeschaft)|Bytes|Totaal|Het aantal factureerbare bytes dat op alle netwerkinterfaces is ontvangen door de Virtuele Machine(s) (Binnenkomend verkeer) (afgeschaft)|Geen|
|Netwerk uit|Network Out Billable (Afgeschaft)|Bytes|Totaal|Het aantal factureerbare bytes op alle netwerkinterfaces door de Virtuele Machine(s) (Uitgaand Verkeer) (Afgeschaft)|Geen|
|Bytes voor schijflezen|Bytes voor schijflezen|Bytes|Totaal|Bytes die van schijf tijdens controleperiode worden gelezen|Geen|
|Schrijfbytes voor schijf|Schrijfbytes voor schijf|Bytes|Totaal|Bytes die tijdens de controleperiode naar schijf zijn geschreven|Geen|
|Bewerkingen voor schijflezen/sec|Bewerkingen voor schijflezen/sec|CountPerSeconde|Average|Schijf lees IOPS|Geen|
|Bewerkingen voor schijfschrijfbewerkingen/sec|Bewerkingen voor schijfschrijfbewerkingen/sec|CountPerSeconde|Average|SchijfschrijfiOPS|Geen|
|Resterende CPU-credits|Resterende CPU-credits|Count|Average|Totaal aantal credits beschikbaar om te barsten|Geen|
|CPU-credits verbruikt|CPU-credits verbruikt|Count|Average|Totaal aantal credits verbruikt door de virtuele machine|Geen|
|Bytes per seconde per schijf gelezen|Bytes/Sec gegevensschijf lezen [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSeconde|Average|Bytes/seconde die tijdens de controleperiode vanaf één schijf wordt gelezen|SlotId (SlotId)|
|Schrijfbytes per schijf per seconde|Bytes/Sec voor gegevensschijfschrijven [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSeconde|Average|Bytes/sec die tijdens de controleperiode naar één schijf is geschreven|SlotId (SlotId)|
|Leesbewerkingen per schijf per seconde|Gegevensschijfleesbewerkingen/Sec [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSeconde|Average|IOPS lezen vanaf één schijf tijdens de controleperiode|SlotId (SlotId)|
|Schrijfbewerkingen per schijf per seconde|Gegevensschijfschrijfbewerkingen/Sec [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSeconde|Average|IOPS schrijven vanaf één schijf tijdens de controleperiode|SlotId (SlotId)|
|QD per schijf|Gegevensschijf QD [(afgeschaft)](portal-disk-metrics-deprecation.md)|Count|Average|Gegevensschijfwachtrijdiepte(of wachtrijlengte)|SlotId (SlotId)|
|Bytes per seconde voor gelezen besturingssysteem per schijf|Bytes/Sec voor gelezen SCHIJVEN [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSeconde|Average|Bytes/seconde die van één schijf tijdens controleperiode voor OS-schijf worden gelezen|Geen|
|Schrijven bytes per schijf per seconde per schijf|Bytes/Sec voor schrijven in de schijf van HET BE [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSeconde|Average|Bytes/sec die naar één schijf is geschreven tijdens de controleperiode voor de os-schijf|Geen|
|Leesbewerkingen van het besturingssysteem per schijf per seconde|Bewerkingen voor lezen van schijven/sec van het besturingssysteem [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSeconde|Average|IOPS lezen vanaf één schijf tijdens de controleperiode voor de OS-schijf|Geen|
|Schrijfbewerkingen per schijf per seconde|Bewerkingen voor schrijven van OS-schijven/Sec [(afgeschaft)](portal-disk-metrics-deprecation.md)|CountPerSeconde|Average|IOPS schrijven vanaf één schijf tijdens de controleperiode voor de OS-schijf|Geen|
|OS Per schijf QD|OS Disk QD [(afgeschaft)](portal-disk-metrics-deprecation.md)|Count|Average|Diepte van de schijfwachtrij(of wachtrijlengte)|Geen|
|Bytes/seconde gegevensschijf gelezen|Bytes/Seconde voor gegevensschijf lezen (voorbeeld)|CountPerSeconde|Average|Bytes/seconde die tijdens de controleperiode vanaf één schijf wordt gelezen|Lun|
|Schrijfbytes voor gegevensschijf per seconde|Bytes/sec voor gegevensschijf schrijven (voorbeeld)|CountPerSeconde|Average|Bytes/sec die tijdens de controleperiode naar één schijf is geschreven|Lun|
|Leesbewerkingen gegevensschijf/sec|Gegevensschijfleesbewerkingen/Sec (voorbeeld)|CountPerSeconde|Average|IOPS lezen vanaf één schijf tijdens de controleperiode|Lun|
|Schrijfbewerkingen voor gegevensschijf/sec|Schrijfbewerkingen voor gegevensschijf/seconde (voorbeeld)|CountPerSeconde|Average|IOPS schrijven vanaf één schijf tijdens de controleperiode|Lun|
|Wachtrijlengte van gegevensschijf|Gegevensschijfwachtrijdiepte (voorbeeld)|Count|Average|Gegevensschijfwachtrijdiepte(of wachtrijlengte)|Lun|
|Bytes/seconde voor gelezen osschijflezen|Bytes/Sec voor gelezen OS-schijf (voorbeeld)|CountPerSeconde|Average|Bytes/seconde die van één schijf tijdens controleperiode voor OS-schijf worden gelezen|Geen|
|Bytes voor schrijven van os-schijven per seconde|Bytes/sec voor schrijven in de os-schijf (voorbeeld)|CountPerSeconde|Average|Bytes/sec die naar één schijf is geschreven tijdens de controleperiode voor de os-schijf|Geen|
|Leesbewerkingen voor schijven van BE/Sec|Bewerkingen voor lezen van de schijf van BE/Sec (voorbeeld)|CountPerSeconde|Average|IOPS lezen vanaf één schijf tijdens de controleperiode voor de OS-schijf|Geen|
|Schrijfbewerkingen voor osschijfschrijfbewerkingen/sec|Be-disk schrijfbewerkingen/sec (voorbeeld)|CountPerSeconde|Average|IOPS schrijven vanaf één schijf tijdens de controleperiode voor de OS-schijf|Geen|
|Wachtrijlengte van besturingssysteemschijf|Diepte van de schijfwachtrij van HET BE (voorbeeld)|Count|Average|Diepte van de schijfwachtrij(of wachtrijlengte)|Geen|
|Binnenkomende stromen|Binnenkomende stromen|Count|Average|Binnenkomende stromen zijn het aantal stroomstromen in de inkomende richting (verkeer dat naar de VM gaat)|Geen|
|Uitgaande stromen|Uitgaande stromen|Count|Average|Uitgaande stromen zijn het aantal stroomstromen in de uitgaande richting (verkeer dat uit de VM gaat)|Geen|
|Maximale creatiesnelheid inkomende stromen|Maximale creatiesnelheid inkomende stromen|CountPerSeconde|Average|Het maximale creatiepercentage van inkomende stromen (verkeer dat naar de VM gaat)|Geen|
|Maximale creatiesnelheid voor uitgaande stromen|Maximale creatiesnelheid voor uitgaande stromen|CountPerSeconde|Average|Het maximale creatiepercentage van uitgaande stromen (verkeer dat uit de VM gaat)|Geen|
|Premium Data Disk Cache Read Hit|Gelezen hit in de schijfcache van Premium-gegevens (voorbeeld)|Percentage|Average|Premium Data Disk Cache Read Hit|Lun|
|Premium Data Disk Cache Lees Miss|Leesmisser van de schijfcache van Premium-gegevens (voorbeeld)|Percentage|Average|Premium Data Disk Cache Lees Miss|Lun|
|Premium OS Disk Cache Read Hit|Premium OS Disk Cache Read Hit (Preview)|Percentage|Average|Premium OS Disk Cache Read Hit|Geen|
|Premium OS Disk Cache Lees Miss|Premium OS Disk Cache Lees Miss (Preview)|Percentage|Average|Premium OS Disk Cache Lees Miss|Geen|
|Netwerk in totaal|Netwerk in totaal|Bytes|Totaal|Het aantal bytes dat wordt ontvangen op alle netwerkinterfaces door de virtuele machine(s) (Binnenkomend verkeer)|Geen|
|Netwerk totaal|Netwerk totaal|Bytes|Totaal|Het aantal bytes op alle netwerkinterfaces door de virtuele machine(s) (Uitgaand Verkeer)|Geen|

## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroepen

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CpuUsage CpuUsage|CPU-gebruik|Count|Average|CPU-gebruik op alle cores in millicores.|containerNaam|
|Geheugengebruik|Geheugengebruik|Bytes|Average|Totaal geheugengebruik in byte.|containerNaam|
|NetworkBytesReceivedPerSeconde|Netwerkbytes ontvangen per seconde|Bytes|Average|De netwerkbytes die per seconde worden ontvangen.|Geen|
|NetworkBytesTransmittedPerSeconde|Netwerkbytes per seconde verzonden|Bytes|Average|De netwerkbytes die per seconde worden verzonden.|Geen|

## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registers

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Totaal Aantal pullen|Totaal aantal pullen|Count|Average|Aantal beeldtrekt in totaal|Geen|
|Geslaagdpullcount|Geslaagd aantal pullen|Count|Average|Aantal succesvolle afbeeldingtrekt|Geen|
|Totaal aantal push-tellingen|Totaal aantal push's|Count|Average|Aantal beeldpushes in totaal|Geen|
|Succesvol PushCount|Succesvol aantal push's|Count|Average|Aantal geslaagde beeldpushes|Geen|
|RunDuration|Duur uitvoeren|Milliseconden|Totaal|Duur uitvoeren in milliseconden|Geen|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Totaal aantal beschikbare cpu-cores in een beheerd cluster|Count|Average|Totaal aantal beschikbare cpu-cores in een beheerd cluster|Geen|
|kube_node_status_allocatable_memory_bytes|Totale hoeveelheid beschikbaar geheugen in een beheerd cluster|Bytes|Average|Totale hoeveelheid beschikbaar geheugen in een beheerd cluster|Geen|
|kube_pod_status_ready|Aantal pods in de status Gereed|Count|Average|Aantal pods in de status Gereed|naamruimte, pod|
|kube_node_status_condition|Statussen voor verschillende knooppuntvoorwaarden|Count|Average|Statussen voor verschillende knooppuntvoorwaarden|voorwaarde, status, status2, knooppunt|
|kube_pod_status_phase|Aantal peulen per fase|Count|Average|Aantal peulen per fase|fase, naamruimte, pod|



## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Succesvolle lRequests|Succesvolle aanvragen|Count|Totaal|Succesvolle verzoeken van de aangepaste provider|HttpMethod, Callpath, Statuscode|
|Mislukte aanvragen|Mislukte aanvragen|Count|Totaal|Haalt de beschikbare logboeken voor aangepaste resourceproviders op|HttpMethod, Callpath, Statuscode|

## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|NICRead-doorvoer|Doorvoer lezen (netwerk)|BytesPerSeconde|Average|De leesdoorvoer van de netwerkinterface op het apparaat in de rapportageperiode voor alle volumes in de gateway.|Instancename|
|NICWrite-doorvoer|Doorvoer schrijven (netwerk)|BytesPerSeconde|Average|De schrijfdoorvoer van de netwerkinterface op het apparaat in de rapportageperiode voor alle volumes in de gateway.|Instancename|
|CloudReadThroughputPerShare|Clouddownloaddoorvoer (delen)|BytesPerSeconde|Average|De downloaddoorvoer naar Azure vanuit een aandeel tijdens de rapportageperiode.|Delen|
|CloudUploadThroughputPerShare|Doorvoer van cloudupload (delen)|BytesPerSeconde|Average|De uploaddoorvoer naar Azure vanuit een aandeel tijdens de rapportageperiode.|Delen|
|BytesGeüploadToCloudPerShare|Cloud bytes geüpload (Delen)|Bytes|Average|Het totale aantal bytes dat tijdens de rapportageperiode vanuit een aandeel naar Azure wordt geüpload.|Delen|
|TotaalCapaciteit|Totale capaciteit|Bytes|Average|Totale capaciteit|Geen|
|Beschikbare capaciteit|Beschikbare capaciteit|Bytes|Average|De beschikbare capaciteit in bytes tijdens de verslagperiode.|Geen|
|CloudUploadDoorvoer|Doorvoer van cloudupload|BytesPerSeconde|Average|De doorvoer van de cloud uploadt naar Azure tijdens de rapportageperiode.|Geen|
|CloudRead-doorvoer|Clouddownloaddoorvoer|BytesPerSeconde|Average|De clouddownloaddoorvoer naar Azure tijdens de rapportageperiode.|Geen|
|BytesGeuploadedToCloud|Cloud bytes geüpload (apparaat)|Bytes|Average|Het totale aantal bytes dat tijdens de rapportageperiode vanaf een apparaat naar Azure wordt geüpload.|Geen|
|HypervVirtualProcessorGebruik|Edge Compute - Percentage CPU|Percentage|Average|Percentage CPU-gebruik|Instancename|
|HypervMemoryUsage HyperVMemoryUsage HyperVMemoryUsage HyperV|Edge Compute - Geheugengebruik|Percentage|Average|Hoeveelheid RAM in gebruik|Instancename|


## <a name="microsoftdatacatalogdatacatalogs"></a>Microsoft.DataCatalog/datacatalogi

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AssetDistributionbyclassification|Asset distributie per classificatie|Count|Totaal|Geeft het aantal activa aan waarbij een bepaalde classificatie is toegewezen, d.w.z. ze zijn ingedeeld met dat label.|Classificatie, Bron|
|AssetDistributionByStorageType|Distributie van activa op opslagtype|Count|Totaal|Geeft het aantal elementen aan met een bepaald opslagtype.|StorageType|
|NumberOfAssetsWithClassificaties|Aantal activa met ten minste één classificatie|Count|Average|Geeft het aantal elementen aan met ten minste één tagclassificatie.|Geen|
|ScanCancelled|Scan geannuleerd|Count|Totaal|Geeft het aantal geannuleerde scans aan.|Geen|
|Scannen voltooid|Scan voltooid|Count|Totaal|Geeft het aantal scans aan dat is voltooid.|Geen|
|Scannen mislukt|Scannen is mislukt|Count|Totaal|Geeft aan dat het aantal scans is mislukt.|Geen|
|ScanTimeTaken|Scan tijd genomen|Seconden|Totaal|Geeft de totale scantijd in seconden aan.|Geen|
|CatalogusActieveGebruikers|Dagelijkse actieve gebruikers|Count|Totaal|Aantal actieve gebruikers dagelijks|Geen|
|Catalogusgebruik|Gebruiksdistributie per bewerking|Count|Totaal|Geef het aantal bewerkingen aan dat de gebruiker aan de catalogus maakt, d.w.z. Access, Search, Glossary.|Bewerking|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafabrieken

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Mislukte runs|Mislukte uitvoeringen|Count|Totaal||pipelineName, activityName|
|Geslaagde runs|Succesvolle runs|Count|Totaal||pipelineName, activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/fabrieken

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Pijplijnmisluktloopt|Mislukte pijplijn voert statistieken uit|Count|Totaal||FailureType, Naam|
|PipelineSucceededRuns|Opgevolgd pijplijnvoert statistieken uit|Count|Totaal||FailureType, Naam|
|Pijplijncancelledruns|Geannuleerde pijplijn voert statistieken uit|Count|Totaal||FailureType, Naam|
|Activiteitmisluktloopt|Mislukte activiteit voert statistieken uit|Count|Totaal||ActivityType, PipelineName, FailureType, Naam|
|ActivitySucceededRuns ActivitySucceededRuns ActivitySucceededRuns ActivitySucceed|Op geslaagde activiteit voert statistieken uit|Count|Totaal||ActivityType, PipelineName, FailureType, Naam|
|ActivityCancelledRuns|Geannuleerde activiteit voert statistieken uit|Count|Totaal||ActivityType, PipelineName, FailureType, Naam|
|Triggerfailedruns|Mislukte trigger voert statistieken uit|Count|Totaal||Naam, FailureType|
|TriggerSucceededRuns|Geslaagde trigger voert statistieken uit|Count|Totaal||Naam, FailureType|
|TriggerCancelledRuns|Geannuleerde trigger voert statistieken uit|Count|Totaal||Naam, FailureType|
|IntegratieRuntimeCpuPercentage|Integratie runtime CPU-gebruik|Percentage|Average||IntegrationRuntimeName, NodeName|
|IntegratieRuntimeBeschikbaarGeheugen|Beschikbare geheugen voor integratieruntime|Bytes|Average||IntegrationRuntimeName, NodeName|
|IntegratieRuntimeAverageTaskPickupDelay|Wachtrijduur voor integratie|Seconden|Average||IntegratieRuntimeName|
|IntegratieRuntimeQueueLengte|Wachtrijlengte van de in-integratie-runtime|Count|Average||IntegratieRuntimeName|
|IntegratieRuntimeAvailableNodeNummer|Aantal beschikbare nodegegevens voor integratie|Count|Average||IntegratieRuntimeName|
|MaxAllowedResourceCount|Maximaal toegestane entiteiten tellen|Count|Maximum||Geen|
|MaxAllowedFactorySizeInGbUnits|Maximaal toegestane fabrieksgrootte (GB-eenheid)|Count|Maximum||Geen|
|Resourceaantal|Aantal entiteiten in totaal|Count|Maximum||Geen|
|Factorysizeingbunits|Totale fabrieksgrootte (GB-eenheid)|Count|Maximum||Geen|

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|JobEndedSucces|Succesvolle vacatures|Count|Totaal|Aantal succesvolle banen.|Geen|
|JobEndedFailure|Mislukte taken|Count|Totaal|Aantal mislukte taken.|Geen|
|JobEndedCancelled|Geannuleerde taken|Count|Totaal|Aantal geannuleerde taken.|Geen|
|Jobauendedsucces|Succesvolle AU-tijd|Seconden|Totaal|Totale AU tijd voor succesvolle banen.|Geen|
|JobauendedFailure|Mislukte AU-tijd|Seconden|Totaal|Totale AU-tijd voor mislukte taken.|Geen|
|Jobaucancelled|Geannuleerde AU-tijd|Seconden|Totaal|Totale AU-tijd voor geannuleerde taken.|Geen|
|JobStage|Vacatures in fase|Count|Totaal|Aantal vacatures in elke fase.|Geen|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|TotaalOpslag|Totale opslagruimte|Bytes|Maximum|Totale hoeveelheid gegevens die op de rekening is opgeslagen.|Geen|
|DataGeschreven|Gegevens geschreven|Bytes|Totaal|Totale hoeveelheid gegevens die naar de rekening is geschreven.|Geen|
|Gegevenslezen|Gegevens lezen|Bytes|Totaal|Totale hoeveelheid gegevens die van de rekening wordt gelezen.|Geen|
|Schrijfverzoeken|Schrijfverzoeken|Count|Totaal|Aantal gegevens dat aanvragen naar het account schrijft.|Geen|
|Leesverzoeken|Leesverzoeken|Count|Totaal|Aantal gegevensleesverzoeken naar het account.|Geen|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Aantal delen|Verzonden aandelen|Count|Maximum|Aantal verzonden aandelen op de rekening|Sharenaam|
|Aantal aandelenabonnementen|Ontvangen aandelen|Count|Maximum|Aantal ontvangen aandelen op de rekening|ShareSubscriptionName|
|SucceededShareSynchronizations|Geslaagde momentopnamen voor verzonden delen|Count|Count|Aantal verzonden delen geslaagd momentopnamen in het account|Geen|
|FailedShareSynchronizations|Mislukte momentopnamen voor verzonden delen|Count|Count|Aantal verzonden share mislukte momentopnamen in het account|Geen|
|SucceededShareSubscriptionSynchronizations|Geslaagde momentopnamen voor ontvangen delen|Count|Count|Aantal ontvangen delen geslaagd momentopnamen in de account|Geen|
|FailedShareSubscriptionSynchronizations|Mislukte momentopnamen voor ontvangen delen|Count|Count|Aantal mislukte momentopnamen van ontvangen delen in het account|Geen|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforMariaDB/servers

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Percentage|Average|CPU-percentage|Geen|
|memory_percent|Geheugenpercentage|Percentage|Average|Geheugenpercentage|Geen|
|io_consumption_percent|IO procent|Percentage|Average|IO procent|Geen|
|storage_percent|Opslagpercentage|Percentage|Average|Opslagpercentage|Geen|
|storage_used|Gebruikte opslag|Bytes|Average|Gebruikte opslag|Geen|
|storage_limit|Opslaglimiet|Bytes|Maximum|Opslaglimiet|Geen|
|serverlog_storage_percent|Opslagpercentage serverlogboeken|Percentage|Average|Opslagpercentage serverlogboeken|Geen|
|serverlog_storage_usage|Serverlogboekopslag gebruikt|Bytes|Average|Serverlogboekopslag gebruikt|Geen|
|serverlog_storage_limit|Opslaglimiet voor serverlogboeken|Bytes|Average|Opslaglimiet voor serverlogboeken|Geen|
|active_connections|Actieve verbindingen|Count|Average|Actieve verbindingen|Geen|
|connections_failed|Mislukte verbindingen|Count|Totaal|Mislukte verbindingen|Geen|
|seconds_behind_master|Replicatievertraging in seconden|Count|Maximum|Replicatievertraging in seconden|Geen|
|backup_storage_used|Gebruikte back-upopslag|Bytes|Average|Gebruikte back-upopslag|Geen|
|network_bytes_egress|Netwerk uit|Bytes|Totaal|Netwerk uit over actieve verbindingen|Geen|
|network_bytes_ingress|Netwerk in|Bytes|Totaal|Netwerk In over actieve verbindingen|Geen|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Percentage|Average|CPU-percentage|Geen|
|memory_percent|Geheugenpercentage|Percentage|Average|Geheugenpercentage|Geen|
|io_consumption_percent|IO procent|Percentage|Average|IO procent|Geen|
|storage_percent|Opslagpercentage|Percentage|Average|Opslagpercentage|Geen|
|storage_used|Gebruikte opslag|Bytes|Average|Gebruikte opslag|Geen|
|storage_limit|Opslaglimiet|Bytes|Maximum|Opslaglimiet|Geen|
|serverlog_storage_percent|Opslagpercentage serverlogboeken|Percentage|Average|Opslagpercentage serverlogboeken|Geen|
|serverlog_storage_usage|Serverlogboekopslag gebruikt|Bytes|Average|Serverlogboekopslag gebruikt|Geen|
|serverlog_storage_limit|Opslaglimiet voor serverlogboeken|Bytes|Maximum|Opslaglimiet voor serverlogboeken|Geen|
|active_connections|Actieve verbindingen|Count|Average|Actieve verbindingen|Geen|
|connections_failed|Mislukte verbindingen|Count|Totaal|Mislukte verbindingen|Geen|
|seconds_behind_master|Replicatievertraging in seconden|Count|Maximum|Replicatievertraging in seconden|Geen|
|backup_storage_used|Gebruikte back-upopslag|Bytes|Average|Gebruikte back-upopslag|Geen|
|network_bytes_egress|Netwerk uit|Bytes|Totaal|Netwerk uit over actieve verbindingen|Geen|
|network_bytes_ingress|Netwerk in|Bytes|Totaal|Netwerk In over actieve verbindingen|Geen|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Percentage|Average|CPU-percentage|Geen|
|memory_percent|Geheugenpercentage|Percentage|Average|Geheugenpercentage|Geen|
|io_consumption_percent|IO procent|Percentage|Average|IO procent|Geen|
|storage_percent|Opslagpercentage|Percentage|Average|Opslagpercentage|Geen|
|storage_used|Gebruikte opslag|Bytes|Average|Gebruikte opslag|Geen|
|storage_limit|Opslaglimiet|Bytes|Maximum|Opslaglimiet|Geen|
|serverlog_storage_percent|Opslagpercentage serverlogboeken|Percentage|Average|Opslagpercentage serverlogboeken|Geen|
|serverlog_storage_usage|Serverlogboekopslag gebruikt|Bytes|Average|Serverlogboekopslag gebruikt|Geen|
|serverlog_storage_limit|Opslaglimiet voor serverlogboeken|Bytes|Maximum|Opslaglimiet voor serverlogboeken|Geen|
|active_connections|Actieve verbindingen|Count|Average|Actieve verbindingen|Geen|
|connections_failed|Mislukte verbindingen|Count|Totaal|Mislukte verbindingen|Geen|
|backup_storage_used|Gebruikte back-upopslag|Bytes|Average|Gebruikte back-upopslag|Geen|
|network_bytes_egress|Netwerk uit|Bytes|Totaal|Netwerk uit over actieve verbindingen|Geen|
|network_bytes_ingress|Netwerk in|Bytes|Totaal|Netwerk In over actieve verbindingen|Geen|
|pg_replica_log_delay_in_seconds|Replica Lag|Seconden|Maximum|Replica lag in seconden|Geen|
|pg_replica_log_delay_in_bytes|Max Lag Over Replica's|Bytes|Maximum|Vertraging in bytes van de meest achterblijvende replica|Geen|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Percentage|Average|CPU-percentage|Geen|
|memory_percent|Geheugenpercentage|Percentage|Average|Geheugenpercentage|Geen|
|iops iops|IOPS|Count|Average|IO-bewerkingen per seconde|Geen|
|storage_percent|Opslagpercentage|Percentage|Average|Opslagpercentage|Geen|
|storage_used|Gebruikte opslag|Bytes|Average|Gebruikte opslag|Geen|
|active_connections|Actieve verbindingen|Count|Average|Actieve verbindingen|Geen|
|network_bytes_egress|Netwerk uit|Bytes|Totaal|Netwerk uit over actieve verbindingen|Geen|
|network_bytes_ingress|Netwerk in|Bytes|Totaal|Netwerk In over actieve verbindingen|Geen|


## <a name="microsoftdbforpostgresqlsingleservers"></a>Microsoft.DBforPostgreSQL/singleservers

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Percentage|Average|CPU-percentage|Geen|
|memory_percent|Geheugenpercentage|Percentage|Average|Geheugenpercentage|Geen|
|iops iops|IOPS|Count|Average|IO-bewerkingen per seconde|Geen|
|storage_percent|Opslagpercentage|Percentage|Average|Opslagpercentage|Geen|
|storage_used|Gebruikte opslag|Bytes|Average|Gebruikte opslag|Geen|
|active_connections|Actieve verbindingen|Count|Average|Actieve verbindingen|Geen|
|network_bytes_egress|Netwerk uit|Bytes|Totaal|Netwerk uit over actieve verbindingen|Geen|
|network_bytes_ingress|Netwerk in|Bytes|Totaal|Netwerk In over actieve verbindingen|Geen|
|connections_failed|Mislukte verbindingen|Count|Totaal|Mislukte verbindingen|Geen|
|connections_succeeded|Geslaagde verbindingen|Count|Totaal|Geslaagde verbindingen|Geen|
|maximum_used_transactionIDs|Maximale gebruikte transactie-i-d's|Count|Average|Maximale gebruikte transactie-i-d's|Geen|





## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|d2c.telemetrie.ingress.allProtocol|Telemetriebericht verzendt pogingen|Count|Totaal|Aantal telemetrieberichten van apparaat tot cloud die naar uw IoT-hub zijn verzonden|Geen|
|d2c.telemetrie.ingress.succes|Verzonden telemetrieberichten|Count|Totaal|Aantal telemetrieberichten van apparaat tot cloud die naar uw IoT-hub zijn verzonden|Geen|
|c2d.commands.egress.complete.success|C2D-berichtleveringen voltooid|Count|Totaal|Aantal leveringen van berichten in de cloud naar het apparaat die door het apparaat zijn voltooid|Geen|
|c2d.commands.egress.abandon.success|C2D-berichten verlaten|Count|Totaal|Aantal cloud-to-device-berichten dat door het apparaat wordt opgegeven|Geen|
|c2d.commands.egress.reject.success|C2D-berichten afgewezen|Count|Totaal|Aantal cloud-to-device-berichten dat door het apparaat wordt geweigerd|Geen|
|C2D-berichten verlopen|Verlopen C2D-berichten (voorbeeld)|Count|Totaal|Aantal verlopen cloud-to-device-berichten|Geen|
|devices.totalDevices|Totaal aantal apparaten (afgeschaft)|Count|Totaal|Aantal apparaten dat is geregistreerd op uw IoT-hub|Geen|
|devices.connectedDevices.allProtocol|Verbonden apparaten (afgeschaft) |Count|Totaal|Aantal apparaten dat is verbonden met uw IoT-hub|Geen|
|d2c.telemetry.egress.success|Routering: telemetrieberichten geleverd|Count|Totaal|Het aantal keren dat berichten met succes naar alle eindpunten zijn geleverd met behulp van IoT Hub-routering. Als een bericht naar meerdere eindpunten wordt doorgestuurd, neemt deze waarde met één toe voor elke succesvolle levering. Als een bericht meerdere keren op hetzelfde eindpunt wordt bezorgd, wordt deze waarde voor elke succesvolle levering met één verhoogd.|Geen|
|d2c.telemetry.egress.dropped|Routering: telemetrieberichten verwijderd |Count|Totaal|Het aantal keren dat berichten zijn verwijderd door IoT Hub-routering vanwege doodlopende eindpunten. Deze waarde telt geen berichten die worden afgeleverd op fallback-route, omdat gedropte berichten daar niet worden bezorgd.|Geen|
|d2c.telemetry.egress.orphaned|Routering: telemetrieberichten verweesd |Count|Totaal|Het aantal keren dat berichten zijn verweesd door de routering van IoT-hub omdat ze niet overeenkomen met routeringsregels (inclusief de terugvalregel). |Geen|
|d2c.telemetry.egress.invalid|Routering: telemetrieberichten onverenigbaar|Count|Totaal|Het aantal keren dat IoT Hub-routering geen berichten kan verzenden vanwege een onverenigbaarheid met het eindpunt. Deze waarde omvat geen nieuwe pogingen.|Geen|
|d2c.telemetry.egress.fallback|Routering: berichten geleverd aan fallback|Count|Totaal|Het aantal keren dat IoT Hub-routering berichten heeft afgeleverd naar het eindpunt dat is gekoppeld aan de terugvalroute.|Geen|
|d2c.endpoints.egress.eventHubs|Routering: berichten geleverd aan Event Hub|Count|Totaal|Het aantal keren dat IoT Hub-routering met succes berichten heeft afgeleverd aan eindpunten van Event Hub.|Geen|
|d2c.endpoints.latency.eventHubs|Routering: berichtlatentie voor gebeurtenishub|Milliseconden|Average|De gemiddelde latentie (milliseconden) tussen het binnendringen van berichten naar IoT Hub en het binnendringen van berichten in een eindpunt van Event Hub.|Geen|
|d2c.endpoints.egress.serviceBusQueues|Routering: berichten die worden bezorgd in de wachtrij voor servicebussen|Count|Totaal|Het aantal keren dat IoT Hub-routering met succes berichten heeft afgeleverd bij eindpunten van wachtrijeindpunten van servicebus.|Geen|
|d2c.endpoints.latency.serviceBusQueues|Routering: berichtlatentie voor servicebuswachtrij|Milliseconden|Average|De gemiddelde latentie (milliseconden) tussen het binnendringen van berichten naar IoT Hub en telemetriebericht is in een eindpunt van de servicebuswachtrij.|Geen|
|d2c.endpoints.egress.serviceBusTopics|Routering: berichten geleverd aan Service Bus Topic|Count|Totaal|Het aantal keren dat IoT Hub-routering met succes berichten heeft afgeleverd bij eindpunten van onderwerpen in het onderwerp ServiceBus.|Geen|
|d2c.endpoints.latency.serviceBusTopics|Routering: berichtlatentie voor servicebusonderwerp|Milliseconden|Average|De gemiddelde latentie (milliseconden) tussen berichtbinnendringen naar IoT Hub en telemetriebericht binnenvallen in een eindpunt van een servicebusonderwerp.|Geen|
|d2c.endpoints.egress.builtIn.events|Routering: berichten die worden bezorgd aan berichten/gebeurtenissen|Count|Totaal|Het aantal keren dat IoT Hub-routering met succes berichten heeft afgeleverd bij het ingebouwde eindpunt (berichten/gebeurtenissen).|Geen|
|d2c.endpoints.latency.builtIn.events|Routering: berichtlatentie voor berichten/gebeurtenissen|Milliseconden|Average|De gemiddelde latentie (milliseconden) tussen berichtbinnendringen naar IoT Hub en telemetriebericht binnenvallen in het ingebouwde eindpunt (berichten/gebeurtenissen).|Geen|
|d2c.endpoints.egress.storage|Routering: berichten die in de opslag worden bezorgd|Count|Totaal|Het aantal keren dat IoT Hub-routering met succes berichten naar opslageindpunten heeft geleverd.|Geen|
|d2c.endpoints.latency.storage|Routering: berichtlatentie voor opslag|Milliseconden|Average|De gemiddelde latentie (milliseconden) tussen berichtbinnendringen naar IoT Hub en telemetrie bericht binnendringen in een opslag eindpunt.|Geen|
|d2c.endpoints.egress.storage.bytes|Routering: gegevens die aan opslag worden geleverd|Bytes|Totaal|De hoeveelheid gegevens (bytes) IoT Hub-routering die wordt geleverd aan opslageindpunten.|Geen|
|d2c.endpoints.egress.storage.blobs|Routering: blobs geleverd aan opslag|Count|Totaal|Het aantal keren dat IoT Hub-routering blobs naar opslageindpunten heeft geleverd.|Geen|
|EventGridDeliveries EventGridDeliveries EventGridDeliveries EventGrid|Gebeurtenisgridleveringen (voorbeeld)|Count|Totaal|Het aantal IoT Hub-gebeurtenissen dat is gepubliceerd in Event Grid. Gebruik de dimensie Resultaat voor het aantal geslaagde en mislukte aanvragen. Met de dimensie EventTypehttps://aka.ms/ioteventgrid)wordt het type gebeurtenis weergegeven ( .|Resourceid, Resultaat, Eventtype|
|EventGridLatentie|Latentie van gebeurtenisraster (voorbeeld)|Milliseconden|Average|De gemiddelde latentie (milliseconden) vanaf het moment dat de gebeurtenis Iot Hub werd gegenereerd tot het moment waarop de gebeurtenis werd gepubliceerd in Gebeurtenisraster. Dit getal is een gemiddelde tussen alle gebeurtenistypen. Gebruik de dimensie EventType om de latentie van een specifiek type gebeurtenis te zien.|ResourceId, EventType|
|RouteringLeveringen|Routeringsleveringen (voorbeeld)|Milliseconden|Totaal|Het aantal keren dat IoT Hub heeft geprobeerd berichten aan alle eindpunten te leveren via routering. Als u het aantal geslaagde of mislukte pogingen wilt zien, gebruikt u de dimensie Resultaat. Als u de reden van het mislukken wilt zien, zoals ongeldig, laten vallen of verweesd, gebruikt u de dimensie FailureReasonCategory. U ook de dimensies EndpointName en EndpointType gebruiken om te begrijpen hoeveel berichten op uw verschillende eindpunten zijn afgeleverd. De metrische waarde wordt met één verhoogd voor elke afleverpoging, inclusief als het bericht op meerdere eindpunten wordt bezorgd of als het bericht meerdere keren op hetzelfde eindpunt wordt bezorgd.|ResourceId, EndpointType, EndpointName, FailureReasonCategory,Resultaat,RoutingSource|
|Routeringslatentie|Latentie voor routeringsweergave (voorbeeld)|Milliseconden|Average|De gemiddelde latentie (milliseconden) tussen bericht binnendringen naar IoT Hub en telemetrie bericht binnendringen in een eindpunt. U de afmetingen EndpointName en EndpointType gebruiken om inzicht te krijgen in de latentie voor uw verschillende eindpunten.|ResourceId, EndpointType, EndpointName, RoutingSource|
|d2c.twin.read.success|Succesvolle tweeling leest van apparaten|Count|Totaal|De telling van alle succesvolle apparaat-geïnitieerde tweeling leest.|Geen|
|d2c.twin.read.failure|Mislukte tweeling leest van apparaten|Count|Totaal|De telling van alle mislukte apparaat-geïnitieerde tweeling leest.|Geen|
|d2c.twin.read.size|Reactiegrootte van dubbele leest van apparaten|Bytes|Average|Het gemiddelde, min, en max van alle succesvolle apparaat geïnitieerde tweeling leest.|Geen|
|d2c.twin.update.success|Succesvolle dubbele updates van apparaten|Count|Totaal|De telling van alle succesvolle apparaat-geïnitieerde dubbele updates.|Geen|
|d2c.twin.update.failure|Mislukte dubbele updates van apparaten|Count|Totaal|De telling van alle mislukte apparaat-geïnitieerde dubbele updates.|Geen|
|d2c.twin.update.size|Grootte van dubbele updates van apparaten|Bytes|Average|De gemiddelde, min, en maximale grootte van alle succesvolle apparaat geïnitieerde dubbele updates.|Geen|
|c2d.methods.success|Succesvolle directe methode aanroepingen|Count|Totaal|De telling van alle succesvolle directe methodevraag.|Geen|
|c2d.methods.failure|Mislukte directe aanroepen van methoden|Count|Totaal|De telling van alle mislukte directe methodeaanroepen.|Geen|
|c2d.methods.requestSize|Aanvraaggrootte van directe methode-aanroepingen|Bytes|Average|Het gemiddelde, min en max van alle succesvolle directe methodeaanvragen.|Geen|
|c2d.methods.responseSize|Reactiegrootte van directe aanroepingen van methoden|Bytes|Average|Het gemiddelde, min, en max van alle succesvolle directe methode reacties.|Geen|
|c2d.twin.read.success|Succesvolle tweeling leest van back-end|Count|Totaal|De telling van alle succesvolle back-end-geïnitieerde tweeling leest.|Geen|
|c2d.twin.read.failure|Mislukte tweeling leest van back-end|Count|Totaal|De telling van alle ontbroken back-end-geïnitieerde tweeling leest.|Geen|
|c2d.twin.read.size|Reactiegrootte van tweeling leest van back-end|Bytes|Average|De gemiddelde, min, en max van alle succesvolle back-end-geïnitieerde tweeling leest.|Geen|
|c2d.twin.update.success|Succesvolle dubbele updates van back-end|Count|Totaal|De telling van alle succesvolle back-end-geïnitieerde dubbele updates.|Geen|
|c2d.twin.update.failure|Mislukte dubbele updates van back-end|Count|Totaal|De telling van alle mislukte back-end-geïnitieerde dubbele updates.|Geen|
|c2d.twin.update.size|Grootte van dubbele updates van back-end|Bytes|Average|De gemiddelde, min, en maximale grootte van alle succesvolle back-end-geïnitieerde dubbele updates.|Geen|
|twinQueries.success|Succesvolle dubbele query's|Count|Totaal|De telling van alle succesvolle dubbele query's.|Geen|
|twinQueries.failure|Mislukte dubbele query's|Count|Totaal|De telling van alle mislukte dubbele query's.|Geen|
|twinQueries.resultSize|De resultaatgrootte van twee query's|Bytes|Average|De gemiddelde, min, en max van het resultaat grootte van alle succesvolle dubbele query's.|Geen|
|jobs.createTwinUpdateJob.success|Succesvolle creaties van twin update jobs|Count|Totaal|De telling van alle succesvolle creatie van twin update banen.|Geen|
|jobs.createTwinUpdateJob.failure|Mislukte creaties van twin update-taken|Count|Totaal|De telling van alle mislukte creatie van dubbele update taken.|Geen|
|jobs.createDirectMethodJob.success|Succesvolle creaties van methode aanroepbanen|Count|Totaal|De telling van alle succesvolle creatie van directe methode aanroepbanen.|Geen|
|jobs.createDirectMethodJob.failure|Mislukte creaties van aanroeptaken voor methoden|Count|Totaal|De telling van alle mislukte creatie van directe methode aanroeptaken.|Geen|
|jobs.listJobs.success|Succesvolle oproepen om vacatures op te noemen|Count|Totaal|De telling van alle succesvolle oproepen om taken op te sommen.|Geen|
|jobs.listJobs.failure|Mislukte oproepen om taken weer te geven|Count|Totaal|De telling van alle mislukte oproepen om taken op te sommen.|Geen|
|jobs.cancelJob.success|Succesvolle annuleringen van vacatures|Count|Totaal|De telling van alle succesvolle oproepen om een taak te annuleren.|Geen|
|jobs.cancelJob.failure|Mislukte annuleringen van werk|Count|Totaal|De telling van alle mislukte oproepen om een taak te annuleren.|Geen|
|jobs.queryJobs.success|Succesvolle zoekopdrachten|Count|Totaal|De telling van alle geslaagde oproepen naar querytaken.|Geen|
|jobs.queryJobs.failure|Mislukte query's|Count|Totaal|Het aantal mislukte oproepen naar querytaken.|Geen|
|vacatures.voltooid|Voltooide taken|Count|Totaal|De telling van alle voltooide taken.|Geen|
|jobs.failed|Mislukte taken|Count|Totaal|De telling van alle mislukte taken.|Geen|
|d2c.telemetry.ingress.sendThrottle|Aantal beperkingsfouten|Count|Totaal|Aantal beperkingsfouten als gevolg van gashendels voor de doorvoer van het apparaat|Geen|
|dailyMessageQuotaGebruikt|Totaal aantal gebruikte berichten|Count|Average|Aantal totale berichten dat vandaag wordt gebruikt|Geen|
|deviceDataUsage deviceDataUsage|Totaal gebruik van apparaatgegevens|Bytes|Totaal|Bytes die worden overgedragen van en naar alle apparaten die zijn aangesloten op IotHub|Geen|
|deviceDataUsageV2|Totaal apparaatgegevensgebruik (voorbeeld)|Bytes|Totaal|Bytes die worden overgedragen van en naar alle apparaten die zijn aangesloten op IotHub|Geen|
|totalDeviceCount totalDeviceCount|Totaal aantal apparaten (voorbeeld)|Count|Average|Aantal apparaten dat is geregistreerd op uw IoT-hub|Geen|
|connectedDeviceCount|Verbonden apparaten (voorbeeld)|Count|Average|Aantal apparaten dat is verbonden met uw IoT-hub|Geen|
|Configuraties|Configuratiestatistieken|Count|Totaal|Statistieken voor configuratiebewerkingen|Geen|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Registratiepogingen|Registratiepogingen|Count|Totaal|Aantal apparaatregistraties geprobeerd|ProvisioningServiceName, iotHubName, Status|
|Apparaattoewijzingen|Toegewezen apparaten|Count|Totaal|Aantal apparaten dat is toegewezen aan een IoT-hub|ProvisioningServiceName, iotHubName|
|AttestationPogingen|Attestpogingen|Count|Totaal|Aantal apparaatverklaringen geprobeerd|ProvisioningServiceName, Status,Protocol|




## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Regio toevoegen|Regio toegevoegd|Count|Count|Regio toegevoegd|Regio|
|Beschikbare opslagruimte|Beschikbare opslagruimte|Bytes|Totaal|Totaal beschikbare opslag gerapporteerd bij 5 minuten granulariteit|CollectionName, DatabaseName,Regio|
|CassandraConnectionSluitingen|Cassandra Connection Sluitingen|Count|Totaal|Aantal Cassandra-verbindingen dat gesloten is, gerapporteerd op een granulariteit van 1 minuut|APIType, Regio, ClosureReason|
|CassandraKeyspaceDelete CassandraKeyspaceDelete|Cassandra Keyspace verwijderd|Count|Count|Cassandra Keyspace verwijderd|ResourceName, apikind, apikindResourcetype, operationtype|
|CassandraKeyspaceThroughputUpdate|Cassandra Keyspace-doorvoer bijgewerkt|Count|Count|Cassandra Keyspace-doorvoer bijgewerkt|ResourceName, apikind, apikindResourcetype, IsThroughputRequest|
|CassandraKeyspaceUpdate|Cassandra Keyspace bijgewerkt|Count|Count|Cassandra Keyspace bijgewerkt|ResourceName, apikind, apikindResourcetype, IsThroughputRequest|
|CassandraRequestCharges|Cassandra Verzoek Kosten|Count|Totaal|RU's verbruikt voor Cassandra verzoeken gedaan|APIType,DatabaseName,CollectionName,Region,OperationType,ResourceType|
|CassandraRequests|Cassandra Verzoeken|Count|Count|Aantal Cassandra verzoeken gedaan|APIType,DatabaseName,CollectionName,Region,OperationType,ResourceType,ErrorCode|
|CassandraTableDelete|Cassandra Tafel verwijderd|Count|Count|Cassandra Tafel verwijderd|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|CassandraTableThroughputUpdate|Cassandra-tabeldoorvoer bijgewerkt|Count|Count|Cassandra-tabeldoorvoer bijgewerkt|Resourcename, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|CassandraTableUpdate|Cassandra Tafel bijgewerkt|Count|Count|Cassandra Tafel bijgewerkt|Resourcename, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|Account maken|Account gemaakt|Count|Count|Account gemaakt|Geen|
|DataGebruik|Gegevensgebruik|Bytes|Totaal|Totaal gegevensgebruik gerapporteerd bij granulariteit van 5 minuten|CollectionName, DatabaseName,Regio|
|Account verwijderen|Account verwijderd|Count|Count|Account verwijderd|Geen|
|Documenttelling|Documentaantal|Count|Totaal|Totaal aantal documenten gerapporteerd bij granulariteit van 5 minuten|CollectionName, DatabaseName,Regio|
|Documentquota|Documentquota|Bytes|Totaal|Totaal opslagquotum gerapporteerd bij granulariteit van 5 minuten|CollectionName, DatabaseName,Regio|
|GremlinDatabaseDelete GremlinDatabaseDelete|Gremlin-database verwijderd|Count|Count|Gremlin-database verwijderd|ResourceName, apikind, apikindResourcetype, operationtype|
|GremlinDatabaseThroughputUpdate|Gremlin Database Doorvoer bijgewerkt|Count|Count|Gremlin Database Doorvoer bijgewerkt|ResourceName, apikind, apikindResourcetype, IsThroughputRequest|
|GremlinDatabaseUpdate|Gremlin-database bijgewerkt|Count|Count|Gremlin-database bijgewerkt|ResourceName, apikind, apikindResourcetype, IsThroughputRequest|
|GremlinGraphDelete GremlinGraphDelete GremlinGraphDelete Greml|Gremlin-grafiek verwijderd|Count|Count|Gremlin-grafiek verwijderd|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|GremlinGraphThroughputUpdate|Gremlin-grafiekdoorvoer bijgewerkt|Count|Count|Gremlin-grafiekdoorvoer bijgewerkt|Resourcename, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|GremlinGraphUpdate|Gremlin Grafiek bijgewerkt|Count|Count|Gremlin Grafiek bijgewerkt|Resourcename, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|IndexGebruik|Indexgebruik|Bytes|Totaal|Totaal indexgebruik gerapporteerd op 5 minuten granulariteit|CollectionName, DatabaseName,Regio|
|MetagegevensVerzoeken|Metagegevensaanvragen|Count|Count|Aantal metagegevensaanvragen. Cosmos DB onderhoudt systeemmetadata verzameling voor elk account, waarmee u collecties, databases, etc, en hun configuraties gratis opsommen.|Databasenaam, CollectionName,Regio,Statuscode, Rol|
|MongoCollectionDelete|Mongo-verzameling verwijderd|Count|Count|Mongo-verzameling verwijderd|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|MongoCollectionThroughputUpdate|Mongo-collectiedoorvoer bijgewerkt|Count|Count|Mongo-collectiedoorvoer bijgewerkt|Resourcename, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoCollectionUpdate|Mongo-collectie bijgewerkt|Count|Count|Mongo-collectie bijgewerkt|Resourcename, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|MongoDBDatabaseUpdate|Mongo-database bijgewerkt|Count|Count|Mongo-database bijgewerkt|ResourceName, apikind, apikindResourcetype, IsThroughputRequest|
|MongoDatabaseDelete MongoDatabaseDelete|Mongo-database verwijderd|Count|Count|Mongo-database verwijderd|ResourceName, apikind, apikindResourcetype, operationtype|
|MongoDatabaseThroughputUpdate|Mongo-databasedoorvoer bijgewerkt|Count|Count|Mongo-databasedoorvoer bijgewerkt|ResourceName, apikind, apikindResourcetype, IsThroughputRequest|
|MongoRequest|Mongo Aanvraag Kosten|Count|Totaal|Mongo Aanvraag Eenheden verbruikt|Databasenaam, CollectionName,Regio,CommandName, ErrorCode, Status|
|MongoRequests|Mongo-verzoeken|Count|Count|Aantal Mongo-verzoeken|Databasenaam, CollectionName,Regio,CommandName, ErrorCode, Status|
|Aantal mongoverzoeken|Mongo-aanvraagtarief|CountPerSeconde|Average|Mongo aanvraag Aantal per seconde|Databasenaam, CollectionName,Regio,CommandName, ErrorCode|
|MongoRequestsDelete|Aanvraagtarief voor mongo verwijderen|CountPerSeconde|Average|Mongo Delete-aanvraag per seconde|Databasenaam, CollectionName,Regio,CommandName, ErrorCode|
|MongoRequestsInsert|Aanvraagpercentage voor invoegen van Mongo|CountPerSeconde|Average|Aantal invoegplaten per seconde|Databasenaam, CollectionName,Regio,CommandName, ErrorCode|
|MongoRequestsQuery|Mongo-aanvraagpercentage|CountPerSeconde|Average|Mongo Query-aanvraag per seconde|Databasenaam, CollectionName,Regio,CommandName, ErrorCode|
|MongoRequestsUpdate|Mongo-updateaanvraagpercentage|CountPerSeconde|Average|Aanvraag voor Mongo-update per seconde|Databasenaam, CollectionName,Regio,CommandName, ErrorCode|
|Genormaliseerd RUConsumption|Genormaliseerd RU-verbruik|Percentage|Maximum|Maximaal RU-verbruikspercentage per minuut|CollectionName, DatabaseName,Regio|
|Voorzieningen|Ingerichte doorvoer|Count|Maximum|Ingerichte doorvoer|DatabaseNaam, CollectionName|
|RegioFailover|Regio mislukt voorbij|Count|Count|Regio mislukt voorbij|Geen|
|Regio verwijderen|Regio verwijderd|Count|Count|Regio verwijderd|Regio|
|Replicatielatentie|Laattie voor P99-replicatie|Milliseconden|Average|P99-replicatielatentie in bron- en doelregio's voor een geo-enabled account|Bronregio, Doelregio|
|ServerSideLatentie|Latentie aan serverzijde|Milliseconden|Average|Latentie aan serverzijde|DatabaseNaam, CollectionName,Regio,ConnectionMode, OperationType, PublicAPIType|
|Beschikbaarheid van service|Beschikbaarheid van service|Percentage|Average|Beschikbaarheid van accountaanvragen op een uur, dag of maand granulariteit|Geen|
|SqlContainerDelete SqlContainerDelete|Sql-container verwijderd|Count|Count|Sql-container verwijderd|ResourceName, ChildResourceName, ApiKind, ApiKindResourceType, OperationType|
|SqlContainerThroughputUpdate SqlContainerThroughputUpdate|Sql-containerdoorvoer bijgewerkt|Count|Count|Sql-containerdoorvoer bijgewerkt|Resourcename, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|SqlContainerUpdate SqlContainerUpdate|Sql-container bijgewerkt|Count|Count|Sql-container bijgewerkt|Resourcename, ChildResourceName, ApiKind, ApiKindResourceType, IsThroughputRequest|
|SqlDatabaseDelete SqlDatabaseDelete|Sql-database verwijderd|Count|Count|Sql-database verwijderd|ResourceName, apikind, apikindResourcetype, operationtype|
|SqlDatabaseThroughputUpdate SqlDatabaseThroughputUpdate|Sql-databasedoorvoer bijgewerkt|Count|Count|Sql-databasedoorvoer bijgewerkt|ResourceName, apikind, apikindResourcetype, IsThroughputRequest|
|SqlDatabaseUpdate SqlDatabaseUpdate|Sql-database bijgewerkt|Count|Count|Sql-database bijgewerkt|ResourceName, apikind, apikindResourcetype, IsThroughputRequest|
|Tabeltabelverwijderen|AzureTable-tabel verwijderd|Count|Count|AzureTable-tabel verwijderd|ResourceName, apikind, apikindResourcetype, operationtype|
|TabeltabeldoorvoerUpdate|AzureTable-tabeldoorvoer bijgewerkt|Count|Count|AzureTable-tabeldoorvoer bijgewerkt|ResourceName, apikind, apikindResourcetype, IsThroughputRequest|
|TabeltabelUpdate|AzureTable-tabel bijgewerkt|Count|Count|AzureTable-tabel bijgewerkt|ResourceName, apikind, apikindResourcetype, IsThroughputRequest|
|TotalRequestUnits|Eenheden voor totale aanvragen|Count|Totaal|Eenheden aanvragen verbruikt|Databasenaam, CollectionName,Regio,StatusCode, OperationType, Status|
|TotalRequests|Totaal aantal aanvragen|Count|Count|Aantal gedaane verzoeken|Databasenaam, CollectionName,Regio,StatusCode, OperationType, Status|
|Accountkeys bijwerken|Accountsleutels bijgewerkt|Count|Count|Accountsleutels bijgewerkt|Keytype|
|Instellingen voor UpdateAccountNetwork|Accountnetwerkinstellingen bijgewerkt|Count|Count|Accountnetwerkinstellingen bijgewerkt|Geen|
|Instellingen voor updateAccountReplicatie|Accountreplicatie-instellingen bijgewerkt|Count|Count|Accountreplicatie-instellingen bijgewerkt|Geen|
|Instellingen voor bijwerken|Diagnostische instellingen voor account bijgewerkt|Count|Count|Diagnostische instellingen voor account bijgewerkt|DiagnosticSettingsName,ResourceGroupName|



## <a name="microsoftenterpriseknowledgegraphservices"></a>Microsoft.EnterpriseKnowledgeGraph/services

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Aantal transacties|Aantal transacties|Count|Count|Totaal aantal transacties|Aantal transacties|
|SuccesTelling|Aantal geslaagd|Count|Count|Aantal geslaagde transacties|SuccesTelling|
|Aantal fouten|Aantal mislukt|Count|Count|Aantal mislukte transacties|Aantal fouten|
|SuccesLatentie|Succes latentie|Milliseconden|Average|Latentie van succesvolle transacties|SuccesTelling|

## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domeinen

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SuccessCount publiceren|Gepubliceerde evenementen|Count|Totaal|Totaal aantal gebeurtenissen gepubliceerd in dit onderwerp|Onderwerp|
|Aantal publishfailfail|Mislukte gebeurtenissen publiceren|Count|Totaal|Totaal aantal gebeurtenissen kan niet worden gepubliceerd in dit onderwerp|Onderwerp, ErrorType, Fout|
|PublicerenSuccessLatencyInMs|Succeslatentie publiceren|Milliseconden|Totaal|Succeslatentie publiceren in milliseconden|Geen|
|MatchedEventCount|Overeenkomende gebeurtenissen|Count|Totaal|Totaal aantal evenementen dat is gekoppeld aan dit evenementabonnement|Onderwerp,EventSubscriptionName,DomainEventSubscriptionName|
|DeliveryAttemptFailCount|Mislukte gebeurtenissen voor bezorging|Count|Totaal|Totaal aantal gebeurtenissen kan niet worden geleverd aan dit gebeurtenisabonnement|Onderwerp,EventSubscriptionName,DomainEventSubscriptionName,Fout,ErrorType|
|DeliverySuccessCount|Geleverde evenementen|Count|Totaal|Totaal aantal evenementen dat is geleverd aan dit evenementabonnement|Onderwerp,EventSubscriptionName,DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Duur bestemmingsverwerking|Milliseconden|Average|Duur van bestemmingsverwerking in milliseconden|Onderwerp,EventSubscriptionName,DomainEventSubscriptionName|
|Aantal gevallengebeurtenissen|Gebeurtenissen laten vallen|Count|Totaal|Totaal aantal gevallen gebeurtenissen die overeenkomen met dit gebeurtenisabonnement|Onderwerp,EventSubscriptionName,DomainEventSubscriptionName,DropReason|
|DeadLetteredCount DeadLetteredCount DeadLetteredCount DeadLetter|Dode letterde gebeurtenissen|Count|Totaal|Totaal aantal dode-geletterde evenementen dat overeenkomt met dit evenementabonnement|Onderwerp,EventSubscriptionName,DomainEventSubscriptionName,DeadLetterReason|

## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/onderwerpen

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SuccessCount publiceren|Gepubliceerde evenementen|Count|Totaal|Totaal aantal gebeurtenissen gepubliceerd in dit onderwerp|Geen|
|Aantal publishfailfail|Mislukte gebeurtenissen publiceren|Count|Totaal|Totaal aantal gebeurtenissen kan niet worden gepubliceerd in dit onderwerp|ErrorType,Fout|
|Ongeëvenaardaantal gebeurtenissen|Ongeëvenaarde evenementen|Count|Totaal|Totaal aantal evenementen komt niet overeen met een van de gebeurtenisabonnementen voor dit onderwerp|Geen|
|PublicerenSuccessLatencyInMs|Succeslatentie publiceren|Milliseconden|Totaal|Succeslatentie publiceren in milliseconden|Geen|
|MatchedEventCount|Overeenkomende gebeurtenissen|Count|Totaal|Totaal aantal evenementen dat is gekoppeld aan dit evenementabonnement|EventSubscriptionName|
|DeliveryAttemptFailCount|Mislukte gebeurtenissen voor bezorging|Count|Totaal|Totaal aantal gebeurtenissen kan niet worden geleverd aan dit gebeurtenisabonnement|Fout,ErrorType,EventSubscriptionName|
|DeliverySuccessCount|Geleverde evenementen|Count|Totaal|Totaal aantal evenementen dat is geleverd aan dit evenementabonnement|EventSubscriptionName|
|DestinationProcessingDurationInMs|Duur bestemmingsverwerking|Milliseconden|Average|Duur van bestemmingsverwerking in milliseconden|EventSubscriptionName|
|Aantal gevallengebeurtenissen|Gebeurtenissen laten vallen|Count|Totaal|Totaal aantal gevallen gebeurtenissen die overeenkomen met dit gebeurtenisabonnement|DropReason,EventSubscriptionName|
|DeadLetteredCount DeadLetteredCount DeadLetteredCount DeadLetter|Dode letterde gebeurtenissen|Count|Totaal|Totaal aantal dode-geletterde evenementen dat overeenkomt met dit evenementabonnement|DeadletterReason, EventSubscriptionName|

## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SuccessCount publiceren|Gepubliceerde evenementen|Count|Totaal|Totaal aantal gebeurtenissen gepubliceerd in dit onderwerp|Geen|
|Aantal publishfailfail|Mislukte gebeurtenissen publiceren|Count|Totaal|Totaal aantal gebeurtenissen kan niet worden gepubliceerd in dit onderwerp|ErrorType,Fout|
|Ongeëvenaardaantal gebeurtenissen|Ongeëvenaarde evenementen|Count|Totaal|Totaal aantal evenementen komt niet overeen met een van de gebeurtenisabonnementen voor dit onderwerp|Geen|
|PublicerenSuccessLatencyInMs|Succeslatentie publiceren|Milliseconden|Totaal|Succeslatentie publiceren in milliseconden|Geen|
|MatchedEventCount|Overeenkomende gebeurtenissen|Count|Totaal|Totaal aantal evenementen dat is gekoppeld aan dit evenementabonnement|EventSubscriptionName|
|DeliveryAttemptFailCount|Mislukte gebeurtenissen voor bezorging|Count|Totaal|Totaal aantal gebeurtenissen kan niet worden geleverd aan dit gebeurtenisabonnement|Fout,ErrorType,EventSubscriptionName|
|DeliverySuccessCount|Geleverde evenementen|Count|Totaal|Totaal aantal evenementen dat is geleverd aan dit evenementabonnement|EventSubscriptionName|
|DestinationProcessingDurationInMs|Duur bestemmingsverwerking|Milliseconden|Average|Duur van bestemmingsverwerking in milliseconden|EventSubscriptionName|
|Aantal gevallengebeurtenissen|Gebeurtenissen laten vallen|Count|Totaal|Totaal aantal gevallen gebeurtenissen die overeenkomen met dit gebeurtenisabonnement|DropReason,EventSubscriptionName|
|DeadLetteredCount DeadLetteredCount DeadLetteredCount DeadLetter|Dode letterde gebeurtenissen|Count|Totaal|Totaal aantal dode-geletterde evenementen dat overeenkomt met dit evenementabonnement|DeadletterReason, EventSubscriptionName|

## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventAbonnementen

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|MatchedEventCount|Overeenkomende gebeurtenissen|Count|Totaal|Totaal aantal evenementen dat is gekoppeld aan dit evenementabonnement|Geen|
|DeliveryAttemptFailCount|Mislukte gebeurtenissen voor bezorging|Count|Totaal|Totaal aantal gebeurtenissen kan niet worden geleverd aan dit gebeurtenisabonnement|Fout,ErrorType|
|DeliverySuccessCount|Geleverde evenementen|Count|Totaal|Totaal aantal evenementen dat is geleverd aan dit evenementabonnement|Geen|
|DestinationProcessingDurationInMs|Duur bestemmingsverwerking|Milliseconden|Average|Duur van bestemmingsverwerking in milliseconden|Geen|
|Aantal gevallengebeurtenissen|Gebeurtenissen laten vallen|Count|Totaal|Totaal aantal gevallen gebeurtenissen die overeenkomen met dit gebeurtenisabonnement|DropReden|
|DeadLetteredCount DeadLetteredCount DeadLetteredCount DeadLetter|Dode letterde gebeurtenissen|Count|Totaal|Totaal aantal dode-geletterde evenementen dat overeenkomt met dit evenementabonnement|DeadLetterReason DeadLetterReason DeadLetterReason DeadLetter|

## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionOnderwerpen

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SuccessCount publiceren|Gepubliceerde evenementen|Count|Totaal|Totaal aantal gebeurtenissen gepubliceerd in dit onderwerp|Geen|
|Aantal publishfailfail|Mislukte gebeurtenissen publiceren|Count|Totaal|Totaal aantal gebeurtenissen kan niet worden gepubliceerd in dit onderwerp|ErrorType,Fout|
|Ongeëvenaardaantal gebeurtenissen|Ongeëvenaarde evenementen|Count|Totaal|Totaal aantal evenementen komt niet overeen met een van de gebeurtenisabonnementen voor dit onderwerp|Geen|
|PublicerenSuccessLatencyInMs|Succeslatentie publiceren|Milliseconden|Totaal|Succeslatentie publiceren in milliseconden|Geen|




## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/naamruimten

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Succesvolle verzoeken|Succesvolle aanvragen|Count|Totaal|Succesvolle aanvragen voor Microsoft.EventHub.|EntityName, OperationResult|
|Serverfouten|Serverfouten.|Count|Totaal|Serverfouten voor Microsoft.EventHub.|EntityName, OperationResult|
|Gebruikersfouten|Gebruikersfouten.|Count|Totaal|Gebruikersfouten voor Microsoft.EventHub.|EntityName, OperationResult|
|Quotaoverschre's|Quotum overschreden fouten.|Count|Totaal|Quota overschreden fouten voor Microsoft.EventHub.|EntityName, OperationResult|
|ThrottledRequests|Throttled Requests.|Count|Totaal|Throttled Requests for Microsoft.EventHub.|EntityName, OperationResult|
|Binnenkomende aanvragen|Binnenkomende aanvragen|Count|Totaal|Binnenkomende aanvragen voor Microsoft.EventHub.|Entiteitsnaam|
|Binnenkomende berichten|Binnenkomende berichten|Count|Totaal|Binnenkomende berichten voor Microsoft.EventHub.|Entiteitsnaam|
|Uitgaande Berichten|Uitgaande berichten|Count|Totaal|Uitgaande berichten voor Microsoft.EventHub.|Entiteitsnaam|
|Inkomende Bytes|Binnenkomende bytes.|Bytes|Totaal|Binnenkomende bytes voor Microsoft.EventHub.|Entiteitsnaam|
|Uitgaande bytes|Uitgaande bytes.|Bytes|Totaal|Uitgaande bytes voor Microsoft.EventHub.|Entiteitsnaam|
|Actieve verbindingen|Actieve verbindingen|Count|Average|Totale actieve verbindingen voor Microsoft.EventHub.|Geen|
|Verbindingengeopend|Verbindingen geopend.|Count|Average|Verbindingen geopend voor Microsoft.EventHub.|Entiteitsnaam|
|Verbindingengesloten|Verbindingen gesloten.|Count|Average|Verbindingen gesloten voor Microsoft.EventHub.|Entiteitsnaam|
|VastleggenBacklog|Leg Backlog vast.|Count|Totaal|Leg achterstand vast voor Microsoft.EventHub.|Entiteitsnaam|
|CapturedMessages|Vastgelegde berichten.|Count|Totaal|Vastgelegde berichten voor Microsoft.EventHub.|Entiteitsnaam|
|CapturedBytes|Vastgelegde bytes.|Bytes|Totaal|Vastgelegde bytes voor Microsoft.EventHub.|Entiteitsnaam|
|Grootte|Grootte|Bytes|Average|Grootte van een EventHub in bytes.|Entiteitsnaam|
|INREQS|Binnenkomende aanvragen (afgeschaft)|Count|Totaal|Totaal aantal aanvragen voor binnenkomend verzenden voor een naamruimte (afgeschaft)|Geen|
|SUCCREQ SUCCREQ|Succesvolle aanvragen (afgeschaft)|Count|Totaal|Totaal aantal geslaagde aanvragen voor een naamruimte (afgeschaft)|Geen|
|FAILREQ FAILREQ|Mislukte aanvragen (afgeschaft)|Count|Totaal|Totaal mislukte aanvragen voor een naamruimte (afgeschaft)|Geen|
|SVRBSY (SVRBSY)|Serverdrukfouten (afgeschaft)|Count|Totaal|Totaal aantal serverdrukfouten voor een naamruimte (afgeschaft)|Geen|
|INTERR (INTERR)|Interne serverfouten (afgeschaft)|Count|Totaal|Totaal aantal interne serverfouten voor een naamruimte (afgeschaft)|Geen|
|MISCERR (MISCERR)|Andere fouten (afgeschaft)|Count|Totaal|Totaal mislukte aanvragen voor een naamruimte (afgeschaft)|Geen|
|INMSGS INMSGS|Binnenkomende berichten (verouderd) (afgeschaft)|Count|Totaal|Totaal binnenkomende berichten voor een naamruimte. Deze statistiek is afgeschaft. Gebruik in plaats daarvan inkomende berichten (Afgeschaft)|Geen|
|EHINMSGS|Binnenkomende berichten (afgeschaft)|Count|Totaal|Totaal aantal binnenkomende berichten voor een naamruimte (afgeschaft)|Geen|
|OUTMSGS OUTMSGS|Uitgaande berichten (verouderd) (afgeschaft)|Count|Totaal|Totaal uitgaande berichten voor een naamruimte. Deze statistiek is afgeschaft. Gebruik in plaats daarvan de statistiek Uitgaande berichten (Afgeschaft)|Geen|
|EHOUTMSGS|Uitgaande berichten (afgeschaft)|Count|Totaal|Totaal aantal uitgaande berichten voor een naamruimte (afgeschaft)|Geen|
|EHINMBS|Binnenkomende bytes (verouderd) (afgeschaft)|Bytes|Totaal|Gebeurtenishub inkomende berichtdoorvoer voor een naamruimte. Deze statistiek is afgeschaft. Gebruik in plaats daarvan inkomende bytesstatistiek (Afgeschaft)|Geen|
|EHINBYTES|Binnenkomende bytes (afgeschaft)|Bytes|Totaal|Gebeurtenishub inkomende berichtdoorvoer voor een naamruimte (afgeschaft)|Geen|
|EHOUTMBS|Uitgaande bytes (verouderd) (afgeschaft)|Bytes|Totaal|Uitgaande berichtdoorvoer van gebeurtenishub voor een naamruimte. Deze statistiek is afgeschaft. Gebruik in plaats daarvan uitgaande bytes (Afgeschaft)|Geen|
|EHOUTBYTES|Uitgaande bytes (afgeschaft)|Bytes|Totaal|Uitgaande berichtdoorvoer van gebeurtenishub voor een naamruimte (afgeschaft)|Geen|
|EHABL EHABL|Archiefbacklogberichten (afgeschaft)|Count|Totaal|Archiefberichten van Gebeurtenishub in achterstand voor een naamruimte (afgeschaft)|Geen|
|EHAMSGS|Archiefberichten (afgeschaft)|Count|Totaal|Event Hub gearchiveerde berichten in een naamruimte (afgeschaft)|Geen|
|EHAMBS|Archiefberichtdoorvoer (afgeschaft)|Bytes|Totaal|Gebeurtenishub gearchiveerde berichtdoorvoer in een naamruimte (afgeschaft)|Geen|

## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Succesvolle verzoeken|Succesvolle aanvragen|Count|Totaal|Succesvolle aanvragen voor Microsoft.EventHub.|OperationResult (OperationResult)|
|Serverfouten|Serverfouten.|Count|Totaal|Serverfouten voor Microsoft.EventHub.|OperationResult (OperationResult)|
|Gebruikersfouten|Gebruikersfouten.|Count|Totaal|Gebruikersfouten voor Microsoft.EventHub.|OperationResult (OperationResult)|
|Quotaoverschre's|Quotum overschreden fouten.|Count|Totaal|Quota overschreden fouten voor Microsoft.EventHub.|OperationResult (OperationResult)|
|ThrottledRequests|Throttled Requests.|Count|Totaal|Throttled Requests for Microsoft.EventHub.|OperationResult (OperationResult)|
|Binnenkomende aanvragen|Binnenkomende aanvragen|Count|Totaal|Binnenkomende aanvragen voor Microsoft.EventHub.|Geen|
|Binnenkomende berichten|Binnenkomende berichten|Count|Totaal|Binnenkomende berichten voor Microsoft.EventHub.|Geen|
|Uitgaande Berichten|Uitgaande berichten|Count|Totaal|Uitgaande berichten voor Microsoft.EventHub.|Geen|
|Inkomende Bytes|Binnenkomende bytes.|Bytes|Totaal|Binnenkomende bytes voor Microsoft.EventHub.|Geen|
|Uitgaande bytes|Uitgaande bytes.|Bytes|Totaal|Uitgaande bytes voor Microsoft.EventHub.|Geen|
|Actieve verbindingen|Actieve verbindingen|Count|Average|Totale actieve verbindingen voor Microsoft.EventHub.|Geen|
|Verbindingengeopend|Verbindingen geopend.|Count|Average|Verbindingen geopend voor Microsoft.EventHub.|Geen|
|Verbindingengesloten|Verbindingen gesloten.|Count|Average|Verbindingen gesloten voor Microsoft.EventHub.|Geen|
|VastleggenBacklog|Leg Backlog vast.|Count|Totaal|Leg achterstand vast voor Microsoft.EventHub.|Geen|
|CapturedMessages|Vastgelegde berichten.|Count|Totaal|Vastgelegde berichten voor Microsoft.EventHub.|Geen|
|CapturedBytes|Vastgelegde bytes.|Bytes|Totaal|Vastgelegde bytes voor Microsoft.EventHub.|Geen|
|CPU|CPU|Percentage|Maximum|CPU-gebruik voor het cluster Gebeurtenishub als percentage|Rol|
|BeschikbaarGeheugen|Beschikbaar geheugen|Percentage|Maximum|Beschikbaar geheugen voor het cluster van de gebeurtenishub als percentage van het totale geheugen.|Rol|
|Grootte|Grootte van een EventHub in bytes.|Bytes|Average|Grootte van een EventHub in bytes.|Rol|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Gatewayrequests|Gatewayaanvragen|Count|Totaal|Aantal gatewayaanvragen|HttpStatus|
|Gecategoriseerde gatewayaanvragen|Gecategoriseerde gatewayaanvragen|Count|Totaal|Aantal gatewayaanvragen per categorie (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|NumActiveWorkers|Aantal actieve werknemers|Count|Maximum|Aantal actieve werknemers|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft.Insights/Instellingen voor automatisch schalen

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|WaargenomenMetrische Waarde|Waargenomen metrische waarde|Count|Average|De waarde berekend op basis van automatisch schalen wanneer deze wordt uitgevoerd|MetricTriggerBron|
|Metrische drempelwaarde|Metrische drempelwaarde|Count|Average|De geconfigureerde drempelwaarde voor automatisch schalen wanneer de automatische schaal is uitgevoerd.|Metrische triggerregel|
|WaargenomenCapaciteit|Waargenomen capaciteit|Count|Average|De capaciteit die wordt gerapporteerd aan autoscale wanneer deze werd uitgevoerd.|Geen|
|ScaleActionsInitiated|Schaalacties gestart|Count|Totaal|De richting van de schaalbewerking.|Schaalrichting|

## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Componenten

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|beschikbaarheidResultaten/beschikbaarheidPercentage|Beschikbaarheid|Percentage|Average|Percentage met succes voltooide beschikbaarheidstests|beschikbaarheidResultaat/naam, beschikbaarheidResultaat/locatie|
|beschikbaarheidResultaten/aantal|Beschikbaarheidstests|Count|Count|Aantal beschikbaarheidstests|beschikbaarheidResultaat/naam, beschikbaarheidResultaat/locatie, beschikbaarheidResultaat/succes|
|beschikbaarheidResultaten/duur|Duur beschikbaarheidstest|Milliseconden|Average|Duur beschikbaarheidstest|beschikbaarheidResultaat/naam, beschikbaarheidResultaat/locatie, beschikbaarheidResultaat/succes|
|browserTimings/netwerkDuur|Verbindingstijd van het laden van pagina's|Milliseconden|Average|Tijd tussen gebruikersaanvraag en netwerkverbinding. Inclusief DNS-lookup en transportverbinding.|Geen|
|browserTimings/verwerkingDuur|Verwerkingstijd van de client|Milliseconden|Average|Tijd tussen het ontvangen van de laatste byte van een document totdat de DOM is geladen. Async-aanvragen worden mogelijk nog steeds verwerkt.|Geen|
|browserTimings/receiveDuration|Ontvangstvan de reactietijd|Milliseconden|Average|Tijd tussen de eerste en laatste bytes, of tot de verbinding.|Geen|
|browserTimings/sendDuration|Aanvraagtijd verzenden|Milliseconden|Average|Tijd tussen netwerkverbinding en ontvangst van de eerste byte.|Geen|
|browserTimings/totalDuration|Laadtijd van browserpagina's|Milliseconden|Average|Tijd vanaf gebruikersaanvraag tot DOM, stylesheets, scripts en afbeeldingen worden geladen.|Geen|
|afhankelijkheden/aantal|Afhankelijkheidsoproepen|Count|Count|Aantal oproepen die door de toepassing naar externe bronnen worden gedaan.|afhankelijkheid/type,afhankelijkheid/performanceBucket, afhankelijkheid/succes,afhankelijkheid/doel,afhankelijkheid/resultaatCode,bewerking/synthetisch,cloud/roleInstance,cloud/roleName|
|afhankelijkheden/duur|Afhankelijkheidsduur|Milliseconden|Average|Duur van de oproepen die door de toepassing naar externe bronnen worden gedaan.|afhankelijkheid/type,afhankelijkheid/performanceBucket, afhankelijkheid/succes,afhankelijkheid/doel,afhankelijkheid/resultaatCode,bewerking/synthetisch,cloud/roleInstance,cloud/roleName|
|afhankelijkheden/mislukt|Fouten in afhankelijkheidsoproepen|Count|Count|Aantal mislukte afhankelijkheidsoproepen die door de toepassing naar externe bronnen worden uitgevoerd.|afhankelijkheid/type,afhankelijkheid/performanceBucket, afhankelijkheid/succes,afhankelijkheid/doel,afhankelijkheid/resultaatCode,bewerking/synthetisch,cloud/roleInstance,cloud/roleName|
|pageViews/count pageViews/count pageViews/count pageViews|Paginaweergaven|Count|Count|Aantal paginaweergaven.|operation/synthetic,cloud/roleName|
|pageViews/duur|Laadtijd van de paginaweergave|Milliseconden|Average|Laadtijd van de paginaweergave|operation/synthetic,cloud/roleName|
|performanceCounters/requestExecutionTime|HTTP-aanvraaguitvoeringstijd|Milliseconden|Average|Uitvoeringstijd van het meest recente verzoek.|cloud/rolInstantie|
|performanceCounters/requestsInQueue|HTTP-aanvragen in toepassingswachtrij|Count|Average|Lengte van de wachtrij voor toepassingsaanvragen.|cloud/rolInstantie|
|prestatietellers/aanvragenPerSeconde|HTTP-aanvraagpercentage|CountPerSeconde|Average|Snelheid van alle aanvragen voor de toepassing per seconde vanaf ASP.NET.|cloud/rolInstantie|
|prestatietellers/exceptionsPerSeconde|Uitzonderingstarief|CountPerSeconde|Average|Aantal afgehandelde en niet-afgehandelde uitzonderingen die zijn gerapporteerd aan vensters, waaronder .NET-uitzonderingen en onbeheerde uitzonderingen die worden omgezet in .NET-uitzonderingen.|cloud/rolInstantie|
|performanceCounters/processIOBytesPerSeconde|Io-snelheid verwerken|BytesPerSeconde|Average|Totaal aantal bytes per seconde gelezen en geschreven naar bestanden, netwerk en apparaten.|cloud/rolInstantie|
|performanceCounters/processCpuPercentage|CPU verwerken|Percentage|Average|Het percentage verstreken tijd dat alle procesthreads de processor gebruikten om instructies uit te voeren. Dit kan variëren tussen 0 tot 100. Deze statistiek geeft alleen de prestaties van het W3WP-proces aan.|cloud/rolInstantie|
|performanceCounters/processorCpuPercentage|Processortijd|Percentage|Average|Het percentage tijd dat de processor doorbrengt in niet-niet-actieve threads.|cloud/rolInstantie|
|performanceCounters/geheugenAvailableBytes|Beschikbaar geheugen|Bytes|Average|Fysiek geheugen onmiddellijk beschikbaar voor toewijzing aan een proces of voor systeemgebruik.|cloud/rolInstantie|
|performanceCounters/processPrivateBytes|Privébytes verwerken|Bytes|Average|Geheugen uitsluitend toegewezen aan de processen van de bewaakte toepassing.|cloud/rolInstantie|
|verzoeken/duur|Serverresponstijd|Milliseconden|Average|Tijd tussen het ontvangen van een HTTP-aanvraag en het voltooien van het verzenden van het antwoord.|request/performanceBucket,request/resultCode,operation/synthetic,cloud/roleInstance,request/success,cloud/roleName|
|aanvragen/tellen|Serveraanvragen|Count|Count|Aantal afgeronde HTTP-aanvragen.|request/performanceBucket,request/resultCode,operation/synthetic,cloud/roleInstance,request/success,cloud/roleName|
|aanvragen/mislukt|Mislukte aanvragen|Count|Count|Aantal HTTP-aanvragen dat is gemarkeerd als mislukt. In de meeste gevallen gaat het om aanvragen met een antwoordcode >= 400 en niet gelijk aan 401.|request/performanceBucket,request/resultCode,request/success,operation/synthetic,cloud/roleInstance,cloud/roleName|
|verzoeken/tarief|Serveraanvraagsnelheid|CountPerSeconde|Average|Snelheid van serveraanvragen per seconde|request/performanceBucket,request/resultCode,operation/synthetic,cloud/roleInstance,request/success,cloud/roleName|
|uitzonderingen/telling|Uitzonderingen|Count|Count|Gecombineerd aantal van alle niet-gevangen uitzonderingen.|cloud/roleName,cloud/roleInstance,client/type|
|uitzonderingen/browser|Browseruitzonderingen|Count|Count|Aantal niet-gevangen uitzonderingen die in de browser worden gegooid.|client/isServer,cloud/roleName|
|uitzonderingen/server|Serveruitzonderingen|Count|Count|Aantal niet-gevangen uitzonderingen die in de servertoepassing zijn gegooid.|client/isServer,cloud/roleName,cloud/roleInstance|
|sporen/telling|Traceringen|Count|Count|Aantal traceerdocumenten|trace/severityLevel,operation/synthetic,cloud/roleName,cloud/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft.IoTCentral/IoTApps

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|connectedDeviceCount|Totaal verbonden apparaten|Count|Average|Aantal apparaten dat is aangesloten op IoT Central|Geen|
|c2d.property.read.success|Succesvolle eigenschap apparaat leest van IoT Central|Count|Totaal|De telling van alle succesvolle eigenschappen leest geïnitieerd van IoT Central|Geen|
|c2d.property.read.failure|De eigenschap Failed Device leest van IoT Central|Count|Totaal|De telling van alle mislukte eigenschap leest geïnitieerd van IoT Central|Geen|
|d2c.property.read.success|Succesvolle eigenschap apparaat leest van apparaten|Count|Totaal|De telling van alle succesvolle eigenschap leest geïnitieerd van apparaten|Geen|
|d2c.property.read.failure|De eigenschap Failed Device leest van apparaten|Count|Totaal|De telling van alle mislukte eigenschap leest geïnitieerd van apparaten|Geen|
|c2d.property.update.success|Succesvolle updates van apparaateigendom van IoT Central|Count|Totaal|De telling van alle succesvolle vastgoedupdates die zijn gestart vanuit IoT Central|Geen|
|c2d.property.update.failure|Updates van de eigenschap Failed Device van IoT Central|Count|Totaal|De telling van alle mislukte eigenschapsupdates die zijn gestart vanuit IoT Central|Geen|
|d2c.property.update.success|Updates voor de eigenschap van een succesvol apparaat vanaf apparaten|Count|Totaal|De telling van alle succesvolle eigenschapsupdates die vanaf apparaten zijn gestart|Geen|
|d2c.property.update.failure|Updates van de eigenschap failed Device van apparaten|Count|Totaal|Het aantal mislukte eigenschapsupdates dat vanaf apparaten is gestart|Geen|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/kluizen

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Serviceapihit (ServiceApiHit)|Total Service Api Hits|Count|Count|Aantal totale service-api-hits|ActivityType, ActivityName|
|ServiceApiLatentie|Algemene latentie van service-api's|Milliseconden|Average|Algehele latentie van service api-aanvragen|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Resultaten van de Total Service Api|Count|Count|Aantal resultaten van de totale service-api|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|VerzadigingShoebox|Algehele gevolmetattijd|Percentage|Average|Gebruikte kluiscapaciteit|ActivityType,ActivityName, TransactionType|
|Beschikbaarheid|Algemene beschikbaarheid van kluizen|Percentage|Average|Beschikbaarheid van Vault-aanvragen|ActivityType, ActivityName, StatusCode, StatusCodeClass|

## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CacheGebruik|Cachegebruik|Percentage|Average|Gebruiksniveau in het clusterbereik|Geen|
|Queryduur|Queryduur|Milliseconden|Average|De duur van query's in enkele seconden|Querystatus|
|Opnamegebruik|Innamegebruik|Percentage|Average|Verhouding van gebruikte innameslots in het cluster|Geen|
|Keepalive|In leven blijven|Count|Average|Sanity-controle geeft aan dat het cluster reageert op query's|Geen|
|InnameVolumeInMB|Innamevolume (in MB)|Count|Totaal|Totaal volume van ingenomen gegevens naar het cluster (in MB)|Database|
|IngestionLatencyInSeconden|Innamelatentie (in seconden)|Seconden|Average|Innametijd van de bron (bijvoorbeeld bericht is in EventHub) in seconden naar het cluster|Geen|
|EventsProcessedForEventHubs|Gebeurtenissen verwerkt (voor gebeurtenis/IoT-hubs)|Count|Totaal|Aantal gebeurtenissen dat door het cluster wordt verwerkt wanneer het wordt ingenomen vanuit gebeurtenis/IoT-hub|EventStatus|
|OpnameResultaat|Innameresultaat|Count|Count|Aantal innamebewerkingen|IngestionResultDetails|
|CPU|CPU|Percentage|Average|CPU-gebruiksniveau|Geen|
|ContinuexportNumOfRecordsExported|Continue export – aantal geëxporteerde records|Count|Totaal|Aantal records geëxporteerd, afgevuurd voor elk opslagartefact geschreven tijdens de exportbewerking|ContinueexportName, Database|
|ExportGebruik|Gebruik exporteren|Percentage|Maximum|Exportgebruik|Geen|
|ContinuExportPendingCount|Aantal continue export in behandeling|Count|Maximum|Het aantal lopende continue exporttaken klaar voor uitvoering|Geen|
|ContinuExportMaxLatenessMinutes|Continue export Max Lateness|Count|Maximum|De te late (in minuten) gerapporteerd door de continue exporttaken in het cluster|Geen|
|ContinuExportResultaat|Continu exportresultaat|Count|Count|Geeft aan of Continue export is geslaagd of mislukt|ContinueexportName, Resultaat, Database|
|StreamingIngestDuration|Streaming Ingest Duur|Milliseconden|Average|Streaming duurt in milliseconden|Geen|
|StreamingIngestDataRate|Gegevenssnelheid streamen|Count|Average|Streaming gegevenssnelheid (MB per seconde)|Geen|
|SteamingingestRequestRate (SteamingingestRequestRate)|Percentage streamingaanvragen|Count|RateRequestsPerSeconde|Streaming ingest aanvraagtarief (aanvragen per seconde)|Geen|
|StreamingIngestResults|Streaming Ingest Resultaat|Count|Average|Streaming ingest resultaat|Resultaat|
|TotaalAantalOfConcurrentQueries|Totaal aantal gelijktijdige query's|Count|Totaal|Totaal aantal gelijktijdige query's|Geen|
|TotalNumberNumberOfThrottledQueries|Totaal aantal aanzet tot beperkingen|Count|Totaal|Totaal aantal aanzet tot beperkingen|Geen|
|TotalNumberNumberOfThrottledCommands TotalNumberOfThrottledCommands TotalNumber|Totaal aantal opdrachten met een beperkt bedrag|Count|Totaal|Totaal aantal opdrachten met een beperkt bedrag|Commandtype|
|TotalNumberOfExtents TotalNumberExtents TotalNumberOfExtents TotalNumberOf|Totaal aantal inomvang|Count|Totaal|Totaal aantal gegevensomvang|Geen|
|InstanceCount|Aantal instanties|Count|Average|Totaal aantal instance's|Geen|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/werkstromen

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|RunsStarted|Runs gestart|Count|Totaal|Aantal werkstroomuitvoeringen gestart.|Geen|
|RunsVoltooid|Voltooide runs|Count|Totaal|Aantal werkstroomuitvoeringen voltooid.|Geen|
|RunsGeslaagd|Runs geslaagd|Count|Totaal|Aantal werkstroombewerkingen geslaagd.|Geen|
|RunsFailed|Runs zijn mislukt|Count|Totaal|Het aantal werkstroomuitvoeringen is mislukt.|Geen|
|RunsCancelled|Runs geannuleerd|Count|Totaal|Aantal werkstroomwordt geannuleerd.|Geen|
|RunLatentie|Latentie uitvoeren|Seconden|Average|Latentie van voltooide werkstroomuitvoeringen.|Geen|
|RunSuccessLatency RunSuccessLatency|Succeslatentie uitvoeren|Seconden|Average|Latentie van geslaagde werkstroom wordt uitgevoerd.|Geen|
|RunThrottledEvents|Loopthrottled gebeurtenissen uit|Count|Totaal|Aantal werkstroomactie of trigger throttled events.|Geen|
|RunStartThrottledEvents|Gebeurtenissen met throttled starten uitvoeren|Count|Totaal|Aantal werkstroomrunstarts met throttled-gebeurtenissen.|Geen|
|RunFailurePercentage|Percentage mislukte uitvoeren|Percentage|Totaal|Percentage werkstroom wordt uitgevoerd, is mislukt.|Geen|
|ActiesGestart|Acties gestart |Count|Totaal|Aantal gestarte werkstroomacties.|Geen|
|ActiesVoltooid|Acties voltooid |Count|Totaal|Aantal werkstroomacties voltooid.|Geen|
|ActiesGeslaagd|Acties geslaagd |Count|Totaal|Aantal werkstroomacties geslaagd.|Geen|
|Actiesmislukt|Acties zijn mislukt |Count|Totaal|Aantal werkstroomacties is mislukt.|Geen|
|ActiesOvergeslagen|Acties overgeslagen |Count|Totaal|Aantal overgeslagen werkstroomacties.|Geen|
|Actielatentie|Actielatentie |Seconden|Average|Latentie van voltooide werkstroomacties.|Geen|
|ActionSuccessLatentie|Latentie voor actiesucces |Seconden|Average|Latentie van geslaagde werkstroomacties.|Geen|
|ActionThrottledEvents|Actie-aan-eengasste gebeurtenissen|Count|Totaal|Aantal gebeurtenissen met een gasstroomactie beperkt..|Geen|
|TriggersStarted|Triggers gestart |Count|Totaal|Aantal gestarte werkstroomtriggers.|Geen|
|TriggersVoltooid|Triggers voltooid |Count|Totaal|Aantal werkstroomtriggers voltooid.|Geen|
|TriggersGeslaagd|Triggers geslaagd |Count|Totaal|Aantal werkstroomtriggers is geslaagd.|Geen|
|Triggers mislukt|Triggers zijn mislukt |Count|Totaal|Het aantal werkstroomtriggers is mislukt.|Geen|
|TriggersOvergeslagen|Triggers overgeslagen|Count|Totaal|Aantal werkstroomtriggers dat is overgeslagen.|Geen|
|TriggersFired|Triggers afgevuurd |Count|Totaal|Aantal ontslagen werkstroomtriggers.|Geen|
|TriggerLatentie|Triggerlatentie |Seconden|Average|Latentie van voltooide werkstroomtriggers.|Geen|
|TriggerFireLatentie|Vuurlatentie activeren |Seconden|Average|Latentie van ontslagen workflowtriggers.|Geen|
|TriggerSuccessLatency|Latentie van succes activeren |Seconden|Average|Latentie van geslaagde werkstroomtriggers.|Geen|
|TriggerThrottledGebeurtenissen|Gebeurtenissen met gaspedaal activeren|Count|Totaal|Aantal werkstroomtriggeren voor gethrottled gebeurtenissen.|Geen|
|FactureerbareActie-executies|Factureerbare actie-uitvoeringen|Count|Totaal|Aantal uitvoeringen voor werkstroomactie die worden gefactureerd.|Geen|
|FactureerbareTriggerExecutions|Factureerbare triggeruitvoeringen|Count|Totaal|Aantal werkstroomtriggeruitvoeringen die worden gefactureerd.|Geen|
|TotalBillableExecutions TotalBillableExecutions TotalBillableExecutions TotalBill|Totale factureerbare uitvoeringen|Count|Totaal|Aantal werkstroomuitvoeringen dat wordt gefactureerd.|Geen|
|BillingUsageNativeOperation|Factureringsgebruik voor uitvoeringen van native bewerkingen|Count|Totaal|Aantal native operatie-uitvoeringen dat wordt gefactureerd.|Geen|
|FactureringGebruikSstandaardconnector|Factureringsgebruik voor standaardconnectoruitvoeringen|Count|Totaal|Aantal standaardconnectoruitvoeringen die worden gefactureerd.|Geen|
|FactureringUsageStorageVerbruik|Factureringsgebruik voor uitvoer van opslagverbruik|Count|Totaal|Aantal uitvoeringen van opslagverbruik die worden gefactureerd.|Geen|

## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|RunsStarted|Runs gestart|Count|Totaal|Aantal werkstroomuitvoeringen gestart.|Geen|
|RunsVoltooid|Voltooide runs|Count|Totaal|Aantal werkstroomuitvoeringen voltooid.|Geen|
|RunsGeslaagd|Runs geslaagd|Count|Totaal|Aantal werkstroombewerkingen geslaagd.|Geen|
|RunsFailed|Runs zijn mislukt|Count|Totaal|Het aantal werkstroomuitvoeringen is mislukt.|Geen|
|RunsCancelled|Runs geannuleerd|Count|Totaal|Aantal werkstroomwordt geannuleerd.|Geen|
|RunLatentie|Latentie uitvoeren|Seconden|Average|Latentie van voltooide werkstroomuitvoeringen.|Geen|
|RunSuccessLatency RunSuccessLatency|Succeslatentie uitvoeren|Seconden|Average|Latentie van geslaagde werkstroom wordt uitgevoerd.|Geen|
|RunThrottledEvents|Loopthrottled gebeurtenissen uit|Count|Totaal|Aantal werkstroomactie of trigger throttled events.|Geen|
|RunStartThrottledEvents|Gebeurtenissen met throttled starten uitvoeren|Count|Totaal|Aantal werkstroomrunstarts met throttled-gebeurtenissen.|Geen|
|RunFailurePercentage|Percentage mislukte uitvoeren|Percentage|Totaal|Percentage werkstroom wordt uitgevoerd, is mislukt.|Geen|
|ActiesGestart|Acties gestart |Count|Totaal|Aantal gestarte werkstroomacties.|Geen|
|ActiesVoltooid|Acties voltooid |Count|Totaal|Aantal werkstroomacties voltooid.|Geen|
|ActiesGeslaagd|Acties geslaagd |Count|Totaal|Aantal werkstroomacties geslaagd.|Geen|
|Actiesmislukt|Acties zijn mislukt |Count|Totaal|Aantal werkstroomacties is mislukt.|Geen|
|ActiesOvergeslagen|Acties overgeslagen |Count|Totaal|Aantal overgeslagen werkstroomacties.|Geen|
|Actielatentie|Actielatentie |Seconden|Average|Latentie van voltooide werkstroomacties.|Geen|
|ActionSuccessLatentie|Latentie voor actiesucces |Seconden|Average|Latentie van geslaagde werkstroomacties.|Geen|
|ActionThrottledEvents|Actie-aan-eengasste gebeurtenissen|Count|Totaal|Aantal gebeurtenissen met een gasstroomactie beperkt..|Geen|
|TriggersStarted|Triggers gestart |Count|Totaal|Aantal gestarte werkstroomtriggers.|Geen|
|TriggersVoltooid|Triggers voltooid |Count|Totaal|Aantal werkstroomtriggers voltooid.|Geen|
|TriggersGeslaagd|Triggers geslaagd |Count|Totaal|Aantal werkstroomtriggers is geslaagd.|Geen|
|Triggers mislukt|Triggers zijn mislukt |Count|Totaal|Het aantal werkstroomtriggers is mislukt.|Geen|
|TriggersOvergeslagen|Triggers overgeslagen|Count|Totaal|Aantal werkstroomtriggers dat is overgeslagen.|Geen|
|TriggersFired|Triggers afgevuurd |Count|Totaal|Aantal ontslagen werkstroomtriggers.|Geen|
|TriggerLatentie|Triggerlatentie |Seconden|Average|Latentie van voltooide werkstroomtriggers.|Geen|
|TriggerFireLatentie|Vuurlatentie activeren |Seconden|Average|Latentie van ontslagen workflowtriggers.|Geen|
|TriggerSuccessLatency|Latentie van succes activeren |Seconden|Average|Latentie van geslaagde werkstroomtriggers.|Geen|
|TriggerThrottledGebeurtenissen|Gebeurtenissen met gaspedaal activeren|Count|Totaal|Aantal werkstroomtriggeren voor gethrottled gebeurtenissen.|Geen|
|IntegratieServiceEnvironmentWorkflowProcessorUsage|Gebruik van werkstroomprocessor voor integratieserviceomgeving|Percentage|Average|Gebruik van werkstroomprocessor voor integratieserviceomgeving.|Geen|
|IntegratieServiceEnvironmentWorkflowMemoryUsage|Gebruik van werkstroomgeheugen voor integratieserviceomgeving|Percentage|Average|Gebruik van werkstroomgeheugen voor de integratieserviceomgeving.|Geen|
|IntegratieServiceEnvironmentConnectorProcessorUsage|Gebruik van connectorprocessor voor integratieserviceomgeving|Percentage|Average|Gebruik van connectorprocessor voor integratieserviceomgeving.|Geen|
|IntegratieserviceEnvironmentConnectorMemoryUsage|Geheugengebruik connector voor integratieserviceomgeving|Percentage|Average|Gebruik van connectorgeheugen voor integratieserviceomgeving.|Geen|

## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/werkruimten

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Geannuleerde runs|Geannuleerde runs|Count|Totaal|Aantal geannuleerde runs voor deze werkruimte|Scenario, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Aangevraagde runs annuleren|Aangevraagde runs annuleren|Count|Totaal|Aantal runs waarbij annuleren is aangevraagd voor deze werkruimte|Scenario, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Voltooide uitvoeringen|Voltooide uitvoeringen|Count|Totaal|Aantal voltooide uitvoeringen voor deze werkruimte|Scenario, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Mislukte uitvoeringen|Mislukte uitvoeringen|Count|Totaal|Aantal runs dat is mislukt voor deze werkruimte|Scenario, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Het afronden van runs|Het afronden van runs|Count|Totaal|Aantal ingevoerde actieve runs voor deze werkruimte|Scenario, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Niet reageren wordt uitgevoerd|Niet reageren wordt uitgevoerd|Count|Totaal|Aantal runs dat niet reageert voor deze werkruimte|Scenario, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Niet gestarte runs|Niet gestarte runs|Count|Totaal|Aantal runs in niet gestarte status voor deze werkruimte|Scenario, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Runs voorbereiden|Runs voorbereiden|Count|Totaal|Aantal runs dat zich voorbereidt op deze werkruimte|Scenario, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Inrichten van uitvoeringen|Inrichten van uitvoeringen|Count|Totaal|Aantal uitvoeringen dat voor deze werkruimte is ingericht|Scenario, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|In de wachtrij staande runs|In de wachtrij staande runs|Count|Totaal|Aantal uitvoeringen dat in de wachtrij staat voor deze werkruimte|Scenario, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Gestarte runs|Gestarte runs|Count|Totaal|Aantal gestarte runs voor deze werkruimte|Scenario, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Startruns|Startruns|Count|Totaal|Aantal gestarte runs voor deze werkruimte|Scenario, RunType, PublishedPipelineId, Computetype, PipelineStepType|
|Fouten|Fouten|Count|Totaal|Aantal runfouten in deze werkruimte|Scenario|
|Waarschuwingen|Waarschuwingen|Count|Totaal|Aantal waarschuwingen voor uitvoeren in deze werkruimte|Scenario|
|Modelregister geslaagd|Modelregister geslaagd|Count|Totaal|Aantal modelregistraties dat in deze werkruimte is geslaagd|Scenario|
|Modelregister is mislukt|Modelregister is mislukt|Count|Totaal|Aantal modelregistraties dat in deze werkruimte is mislukt|Scenario, Statuscode|
|Modeldeploy gestart|Modeldeploy gestart|Count|Totaal|Aantal modelimplementaties dat in deze werkruimte is gestart|Scenario|
|Modeldeploy geslaagd|Modeldeploy geslaagd|Count|Totaal|Aantal modelimplementaties dat is geslaagd in deze werkruimte|Scenario|
|Modeldeploy mislukt|Modeldeploy mislukt|Count|Totaal|Aantal modelimplementaties dat is mislukt in deze werkruimte|Scenario, Statuscode|
|Totaal aantal knooppunten|Totaal aantal knooppunten|Count|Average|Aantal totale knooppunten. Dit totaal omvat enkele actieve knooppunten, niet-actieve knooppunten, onbruikbare knooppunten, vooraf gemepted knooppunten, knooppunten verlaten|Scenario,ClusterNaam|
|Actieve knooppunten|Actieve knooppunten|Count|Average|Aantal Acitve-knooppunten. Dit zijn de knooppunten die actief een taak uitvoeren.|Scenario,ClusterNaam|
|Inactieve knooppunten|Inactieve knooppunten|Count|Average|Aantal niet-actieve knooppunten. Niet-actieve knooppunten zijn de knooppunten die geen taken uitvoeren, maar die een nieuwe taak kunnen accepteren indien beschikbaar.|Scenario,ClusterNaam|
|Onbruikbare knooppunten|Onbruikbare knooppunten|Count|Average|Aantal onbruikbare knooppunten. Onbruikbare knooppunten zijn niet functioneel vanwege een onoplosbaar probleem. Azure recyclet deze knooppunten.|Scenario,ClusterNaam|
|Voorkoming van knooppunten|Voorkoming van knooppunten|Count|Average|Aantal vooraf gelopen knooppunten. Deze knooppunten zijn de knooppunten met lage prioriteit die worden verwijderd uit de beschikbare knooppuntgroep.|Scenario,ClusterNaam|
|Knooppunten verlaten|Knooppunten verlaten|Count|Average|Aantal verlaten knooppunten. Het verlaten van knooppunten zijn de knooppunten die net klaar zijn met het verwerken van een taak en gaan naar De status Niet actief.|Scenario,ClusterNaam|
|Totaal aantal kernen|Totaal aantal kernen|Count|Average|Aantal totale kernen|Scenario,ClusterNaam|
|Actieve kernen|Actieve kernen|Count|Average|Aantal actieve kernen|Scenario,ClusterNaam|
|Niet-actieve kernen|Niet-actieve kernen|Count|Average|Aantal niet-actieve kernen|Scenario,ClusterNaam|
|Onbruikbare kernen|Onbruikbare kernen|Count|Average|Aantal onbruikbare kernen|Scenario,ClusterNaam|
|Voorrang kernen|Voorrang kernen|Count|Average|Aantal vooraf gekomen kernen|Scenario,ClusterNaam|
|Kernen verlaten|Kernen verlaten|Count|Average|Aantal verlatenkernen|Scenario,ClusterNaam|
|Percentage quotumgebruik|Percentage quotumgebruik|Count|Average|Percentage van het gebruikte quotum|Scenario, ClusterNaam, VmFamilyName, VmPriority|
|Cpu-gebruik|Cpu-gebruik|Count|Average|CPU (voorbeeld)|Scenario, runId, NodeId, CreatedTime|
|Gpu's|Gpu's|Count|Average|GPU (voorbeeld)|Scenario, runId, NodeId, CreatedTime, DeviceId|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Gebruik|Gebruik|Count|Count|Aantal API-aanroepen|ApiCategory,apiname, resulttype,responsecode|
|Beschikbaarheid|Beschikbaarheid|Percentage|Average|Beschikbaarheid van de API's|ApiCategory, ApiName|

## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEindpunten

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid Egress-gegevens in bytes.|Outputformat|
|SuccesE2ELatentie|Succes end-to-end Latency|Milliseconden|Average|De gemiddelde latentie voor succesvolle aanvragen in milliseconden.|Outputformat|
|Aanvragen|Aanvragen|Count|Totaal|Verzoeken aan een streaming eindpunt.|OutputFormat,httpstatuscode, errorcode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AssetQuota|Activaquotum|Count|Average|Hoeveel activa zijn toegestaan voor het huidige mediaserviceaccount|Geen|
|AssetCount (AssetCount)|Aantal activa|Count|Average|Hoeveel activa zijn er al gemaakt in het huidige mediaserviceaccount|Geen|
|AssetQuotaUsedPercentage|Gebruikt actiefquotum|Percentage|Average|Actief gebruikt percentage in huidige mediaserviceaccount|Geen|
|ContentKeyPolicyQuota|Inhoudssleutelbeleidsquotum|Count|Average|Hoeveel content key polices zijn toegestaan voor de huidige media service account|Geen|
|ContentKeyPolicyCount|Aantal inhoudssleutelbeleidsfactoren|Count|Average|Hoeveel inhoudssleutelbeleid is al gemaakt in het huidige mediaserviceaccount|Geen|
|ContentKeyPolicyQuotaUsedPercentage|Gebruikt gebruikt quotum voor inhoudssleutelbeleid|Percentage|Average|Content Key Policy gebruikt percentage in huidige media service account|Geen|
|StreamingPolicyQuota|Quotum streamingbeleid|Count|Average|Hoeveel streamingbeleid is toegestaan voor het huidige mediaserviceaccount|Geen|
|Aantal streamingbeleid|Aantal streamingbeleid|Count|Average|Hoeveel streamingbeleid er al zijn gemaakt in het huidige mediaserviceaccount|Geen|
|StreamingPolicyQuotaUsedPercentage|Gebruikt percentage streamingbeleid|Percentage|Average|Streamingbeleid gebruikt percentage in huidige mediaserviceaccount|Geen|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft.MixedReality/remoteRenderingAccounts

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Activa geconverteerd|Geconverteerde activa|Count|Totaal|Totaal aantal geconverteerde activa|AppId, Resourceid, SDKVersion|
|ActiveRenderingSessies|Actieve renderingsessies|Count|Totaal|Totaal aantal actieve renderingsessies|Appid, Resourceid, SessionType, SDKVersion|

## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Gemiddelde leeslatentie|Gemiddelde leeslatentie|Milliseconden|Average|Gemiddelde leeslatentie in milliseconden per bewerking|Geen|
|Gemiddelde writelatentie|Gemiddelde schrijflatentie|Milliseconden|Average|Gemiddelde schrijflatentie in milliseconden per bewerking|Geen|
|VolumeLogische grootte|Volume verbruikt grootte|Bytes|Average|Logische grootte van het volume (gebruikte bytes)|Geen|
|VolumeSnapshotSize|Grootte van volumemomentopnamen|Bytes|Average|Grootte van alle momentopnamen in volume|Geen|
|ReadIops|Iops lezen|CountPerSeconde|Average|In/uit-bewerkingen per seconde lezen|Geen|
|Schrijfiops|Iops schrijven|CountPerSeconde|Average|In/out-bewerkingen per seconde schrijven|Geen|

## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|VolumePoolToegewezengebruikt|Pool toegewezen aan volumegrootte|Bytes|Average|Toegewezen gebruikte grootte van het zwembad|Geen|
|VolumepooltotallogicalSize|Geconsumed Size|Bytes|Average|Som van de logische omvang van alle volumes die behoren tot de pool|Geen|

## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BytesSentRate|Verzonden bytes|Bytes|Totaal|Aantal bytes dat de netwerkinterface heeft verzonden|Geen|
|BytesReceivedRate|Ontvangen bytes|Bytes|Totaal|Aantal bytes dat de netwerkinterface heeft ontvangen|Geen|
|PakkettenSentRate|Verzonden pakketten|Count|Totaal|Aantal pakketten dat de netwerkinterface heeft verzonden|Geen|
|PakkettenReceivedRate|Ontvangen pakketten|Count|Totaal|Aantal pakketten dat de netwerkinterface heeft ontvangen|Geen|

## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Beschikbaarheid van vips|Beschikbaarheid van gegevenspad|Count|Average|Beschikbaarheid van gegevenspad van gemiddelde load balancer per tijdsduur|FrontendIPAddress, FrontendPort|
|DipBeschikbaarheid|Status van status status status van status van status|Count|Average|Status van de status van de status van de gemiddelde load balancer-status per tijdsduur|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|ByteCount ByteCount|Byte Telling|Count|Totaal|Totaal aantal bytes dat binnen de periode wordt verzonden|FrontendIPAddress, FrontendPort, Richting|
|Aantal pakketten|Aantal pakketten|Count|Totaal|Totaal aantal pakketten dat binnen de periode wordt verzonden|FrontendIPAddress, FrontendPort, Richting|
|SYNCount|SYN-telling|Count|Totaal|Totaal aantal SYN-pakketten dat binnen de periode wordt verzonden|FrontendIPAddress, FrontendPort, Richting|
|Aantal snat-verbindingen|Aantal SNAT-verbindingen|Count|Totaal|Totaal aantal nieuwe SNAT-verbindingen dat binnen de periode is gemaakt|FrontendIPAddress, BackendIPAddress, ConnectionState|
|ToegewezenSnatPorts|Toegewezen SNAT-poorten (voorbeeld)|Count|Totaal|Totaal aantal SNAT-poorten toegewezen binnen de periode|FrontendIPAddress, BackendIPAddress,ProtocolType, IsAwaitingRemoval|
|UsedSnatPorts|Gebruikte SNAT-poorten (voorbeeld)|Count|Totaal|Totaal aantal SNAT-poorten dat binnen de periode wordt gebruikt|FrontendIPAddress, BackendIPAddress,ProtocolType, IsAwaitingRemoval|

## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|QueryVolume|Queryvolume|Count|Totaal|Aantal query's dat wordt weergegeven voor een DNS-zone|Geen|
|RecordSetCount RecordSetCount RecordSetCount RecordSet|Recordsetaantal|Count|Maximum|Aantal recordsets in een DNS-zone|Geen|
|RecordSetCapacityUsage RecordSetCapacityUsage RecordSetCapacityUsage RecordSet|Capaciteitsbenutting recordset|Percentage|Maximum|Percentage recordsetcapaciteit dat wordt gebruikt door een DNS-zone|Geen|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PacketsindDos|Binnenkomende pakketten DDoS|CountPerSeconde|Maximum|Binnenkomende pakketten DDoS|Geen|
|PakkettenDroppedDDoS|Binnenkomende pakketten dropten DDoS|CountPerSeconde|Maximum|Binnenkomende pakketten dropten DDoS|Geen|
|PakkettenForwardedDDoS|Binnenkomende pakketten doorgestuurde DDoS|CountPerSeconde|Maximum|Binnenkomende pakketten doorgestuurde DDoS|Geen|
|TCPpacketsinddos|Binnenkomende TCP-pakketten DDoS|CountPerSeconde|Maximum|Binnenkomende TCP-pakketten DDoS|Geen|
|TCPPacketsDroppedDDoS|Binnenkomende TCP-pakketten hebben DDoS-pakketten laten vallen|CountPerSeconde|Maximum|Binnenkomende TCP-pakketten hebben DDoS-pakketten laten vallen|Geen|
|TCPPacketsForwardedDDoS|Binnenkomende TCP-pakketten doorgestuurdd DDoS|CountPerSeconde|Maximum|Binnenkomende TCP-pakketten doorgestuurdd DDoS|Geen|
|UDPPacketsInDDoS|Binnenkomende UDP-pakketten DDoS|CountPerSeconde|Maximum|Binnenkomende UDP-pakketten DDoS|Geen|
|UDPPacketsDroppedDDoS|Binnenkomende UDP-pakketten droppen DDoS|CountPerSeconde|Maximum|Binnenkomende UDP-pakketten droppen DDoS|Geen|
|UDPPacketsForwardedDDoS|Binnenkomende UDP-pakketten doorgestuurde DDoS|CountPerSeconde|Maximum|Binnenkomende UDP-pakketten doorgestuurde DDoS|Geen|
|BytesInddos|DDoS-binnenkomende bytes|BytesPerSeconde|Maximum|DDoS-binnenkomende bytes|Geen|
|BytesDroppedDDoS|Inkomende bytes dropd DDoS|BytesPerSeconde|Maximum|Inkomende bytes dropd DDoS|Geen|
|BytesForwardedDDoS|Inkomende bytes doorgestuurdd DDoS|BytesPerSeconde|Maximum|Inkomende bytes doorgestuurdd DDoS|Geen|
|TCPBytesInddos|Inkomende TCP-bytes DDoS|BytesPerSeconde|Maximum|Inkomende TCP-bytes DDoS|Geen|
|TCPBytesDroppedDDoS|Binnenkomende TCP-bytes ddos-bytes laten vallen|BytesPerSeconde|Maximum|Binnenkomende TCP-bytes ddos-bytes laten vallen|Geen|
|TCPBytesForwardedDDoS|Inkomende TCP-bytes doorgestuurde DDoS|BytesPerSeconde|Maximum|Inkomende TCP-bytes doorgestuurde DDoS|Geen|
|UDPBytesIndDoS|Binnenkomende UDP-bytes DDoS|BytesPerSeconde|Maximum|Binnenkomende UDP-bytes DDoS|Geen|
|UDPBytesDroppedDDoS|Binnenkomende UDP-bytes hebben DDoS-bytes laten vallen|BytesPerSeconde|Maximum|Binnenkomende UDP-bytes hebben DDoS-bytes laten vallen|Geen|
|UDPBytesForwardedDDoS|Inkomende UDP-bytes doorgestuurde DDoS|BytesPerSeconde|Maximum|Inkomende UDP-bytes doorgestuurde DDoS|Geen|
|IfunderddosAttack IfUnderDDoSAttack|Onder DDoS-aanval of niet|Count|Maximum|Onder DDoS-aanval of niet|Geen|
|DDoStriggerTCPPackets|Binnenkomende TCP-pakketten om DDoS-mitigatie te activeren|CountPerSeconde|Maximum|Binnenkomende TCP-pakketten om DDoS-mitigatie te activeren|Geen|
|DDoSTriggerUDPPakketten|Binnenkomende UDP-pakketten om DDoS-mitigatie te activeren|CountPerSeconde|Maximum|Binnenkomende UDP-pakketten om DDoS-mitigatie te activeren|Geen|
|DDoSTriggerSYNPackets DDoSTriggerSYNPackets|Binnenkomende SYN-pakketten om DDoS-mitigatie te activeren|CountPerSeconde|Maximum|Binnenkomende SYN-pakketten om DDoS-mitigatie te activeren|Geen|
|Beschikbaarheid van vips|Beschikbaarheid van gegevenspad|Count|Average|Gemiddelde beschikbaarheid VAN IP-adres per tijdsduur|Poort|
|ByteCount ByteCount|Byte Telling|Count|Totaal|Totaal aantal bytes dat binnen de periode wordt verzonden|Poort, Richting|
|Aantal pakketten|Aantal pakketten|Count|Totaal|Totaal aantal pakketten dat binnen de periode wordt verzonden|Poort, Richting|
|SynCount (SynCount)|SYN-telling|Count|Totaal|Totaal aantal SYN-pakketten dat binnen de periode wordt verzonden|Poort, Richting|



## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PingmeshAverageRoundtripMs|Retourtijd voor pings naar een vm|Milliseconden|Average|Retourtijd voor pings die naar een doel-VM worden verzonden|SourceCustomerAddress, DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Mislukte pings naar een vm|Percentage|Average|Percentage aantal mislukte pings naar totaal verzonden pings van een doel-VM|SourceCustomerAddress, DestinationCustomerAddress|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ApplicationRuleHit|Toepassingsregels raken het aantal|Count|Totaal|Aantal keren Dat toepassingsregels zijn getroffen|Status, Reden, Protocol|
|NetworkRuleHit (NetworkRuleHit)|Netwerkregels raken aantal|Count|Totaal|Aantal keren dat netwerkregels zijn getroffen|Status, Reden, Protocol|
|FirewallHealth|Firewallstatus|Percentage|Average|Firewallstatus|Status, Reden|
|Gegevensverwerkt|Gegevens verwerkt|Bytes|Totaal|Totale hoeveelheid gegevens die door Firewall wordt verwerkt|Geen|
|SNATPortUsage|SNAT-poortgebruik|Percentage|Average|SNAT-poortgebruik|Geen|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Doorvoer|Doorvoer|BytesPerSeconde|Average|Aantal bytes per seconde dat de Application Gateway heeft betekend|Geen|
|OngezondeHostCount|Aantal ongezonde hosten|Count|Average|Aantal ongezonde backendhosts|BackendInstellingenPool|
|HealthyHostCount HealthyHostCount HealthyHostCount HealthyHost|Aantal gezonde gasten|Count|Average|Aantal gezonde backendhosts|BackendInstellingenPool|
|TotalRequests|Totaal aantal aanvragen|Count|Totaal|Aantal geslaagde aanvragen dat Application Gateway heeft betekend|BackendInstellingenPool|
|AvgrequestCountPerHealthyHost|Aanvragen per minuut per gezonde verhuurder|Count|Average|Gemiddeld aantal aanvragen per minuut per gezonde backendhost in een groep|BackendInstellingenPool|
|Mislukte aanvragen|Mislukte aanvragen|Count|Totaal|Aantal mislukte aanvragen dat Application Gateway heeft betekend|BackendInstellingenPool|
|Reactiestatus|Reactiestatus|Count|Totaal|Reactiestatus http-antwoord geretourneerd door Application Gateway|HttpStatusgroep|
|Huidige verbindingen|Huidige verbindingen|Count|Totaal|Aantal huidige verbindingen met Application Gateway|Geen|
|NewConnectionsPerSeconde|Nieuwe verbindingen per seconde|CountPerSeconde|Average|Nieuwe verbindingen per seconde met Application Gateway|Geen|
|Cpu-gebruik|CPU-gebruik|Percentage|Average|Huidig CPU-gebruik van de toepassingsgateway|Geen|
|Capaciteitseenheden|Huidige capaciteitseenheden|Count|Average|Capaciteitseenheden verbruikt|Geen|
|FixedBillableCapacityUnits FixedBillableCapacityUnits|Vaste factureerbare capaciteitseenheden|Count|Average|Minimale capaciteitseenheden die in rekening worden gebracht|Geen|
|Geschatte gefactureerde capaciteitseenheden|Geschatte gefactureerde capaciteitseenheden|Count|Average|Geschatte capaciteitseenheden die in rekening worden gebracht|Geen|
|ComputeUnits|Huidige compute-eenheden|Count|Average|Rekeneenheden verbruikt|Geen|
|BackendResponseStatus|Status backend-antwoord|Count|Totaal|Het aantal HTTP-antwoordcodes dat door de backend-leden wordt gegenereerd. Dit omvat geen antwoordcodes die door de Application Gateway worden gegenereerd.|BackendServer, BackendPool, BackendhttpSetting, HttpStatusGroep|
|TlsProtocol TlsProtocol|TLS-protocol voor client|Count|Totaal|Het aantal TLS- en niet-TLS-aanvragen dat is geïnitieerd door de client die verbinding heeft gemaakt met de Application Gateway. Als u tls-protocoldistributie wilt weergeven, filtert u op het dimension TLS-protocol.|Listener, TlsProtocol|
|BytesSent|Verzonden bytes|Bytes|Totaal|Het totale aantal bytes dat door de toepassingsgateway naar de clients wordt verzonden|Listener|
|Ontvangen bytes|Ontvangen bytes|Bytes|Totaal|Het totale aantal bytes dat door de toepassingsgateway van de clients is ontvangen|Listener|
|ClientRtt|Client RTT|Milliseconden|Average|Gemiddelde retourtijd tussen clients en Application Gateway. Deze statistiek geeft aan hoe lang het duurt om verbindingen tot stand te brengen en bevestigingen terug te sturen|Listener|
|ApplicationGatewayTotalTime|Totale tijd van toepassingsgateway|Milliseconden|Average|Gemiddelde tijd die nodig is om een aanvraag te verwerken en het antwoord ervan te worden verzonden. Dit wordt berekend als het gemiddelde van het interval vanaf het moment waarop Application Gateway de eerste byte van een HTTP-aanvraag ontvangt tot het moment waarop de bewerking voor antwoordverzenden is voltooid. Het is belangrijk op te merken dat dit meestal de verwerkingstijd van de Application Gateway omvat, de tijd dat de aanvraag- en antwoordpakketten over het netwerk reizen en de tijd die de backendserver heeft genomen om te reageren.|Listener|
|Back-endConnectTime|Back-end verbindingstijd|Milliseconden|Average|Tijd besteed aan het tot stand brengen van een verbinding met een backendserver|Listener,BackendServer, BackendPool, BackendHttpSetting|
|BackendFirstByteResponseTime|Backend Eerste byte-reactietijd|Milliseconden|Average|Tijdsinterval tussen het begin van het tot stand brengen van een verbinding met backendserver en het ontvangen van de eerste byte van de antwoordkop, waarbij de verwerkingstijd van de backendserver wordt beantwoord|Listener,BackendServer, BackendPool, BackendHttpSetting|
|BackendLastByteResponseTime|Laatste reactietijd van back-end|Milliseconden|Average|Tijdsinterval tussen het begin van het tot stand brengen van een verbinding met backendserver en het ontvangen van de laatste byte van de antwoordbody|Listener,BackendServer, BackendPool, BackendHttpSetting|
|MatchedCount|Web Application Firewall v1 Totale regelverdeling|Count|Totaal|Web Application Firewall v1 Totale regelverdeling voor het binnenkomende verkeer|Regelgroep, RuleId|
|Geblokkeerdaantal|Regelverdeling van webtoepassingsfirewall v1 geblokkeerd|Count|Totaal|Web Application Firewall v1 geblokkeerde aanvragen regeldistributie|Regelgroep, RuleId|
|GeblokkeerdEReqCount|Aantal geblokkeerde aanvragen voor webtoepassingsfirewall v1|Count|Totaal|Aantal geblokkeerde aanvragen voor Web Application Firewall v1|Geen|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Gemiddelde bandbreedte|Gateway S2S-bandbreedte|BytesPerSeconde|Average|Gemiddelde site-to-site bandbreedte van een gateway in bytes per seconde|Geen|
|P2SBandbreedte|Gateway P2S-bandbreedte|BytesPerSeconde|Average|Gemiddelde point-to-site bandbreedte van een gateway in bytes per seconde|Geen|
|Aantal P2SConnection-verbindingen|Aantal P2S-verbindingen|Count|Maximum|Aantal van een gateway tussen punt en site|Protocol|
|TunnelGemiddeldebandbreedte|Tunnelbandbreedte|BytesPerSeconde|Average|Gemiddelde bandbreedte van een tunnel in bytes per seconde|ConnectionName, RemoteIP|
|TunnelEgressBytes|Bytes tunnel uitgang|Bytes|Totaal|Uitgaande bytes van een tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Tunnel Ingress Bytes|Bytes|Totaal|Binnenkomende bytes van een tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Tunnel Egress Pakketten|Count|Totaal|Uitgaande pakkettelling van een tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Tunnel Ingress Pakketten|Count|Totaal|Inkomende pakkettelling van een tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Tunnel Egress TS Mismatch Packet Drop|Count|Totaal|Aantal uitgaande pakketdalingen van de verkeerskiezer mismatch van een tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Tunnel Ingress TS Mismatch Packet Drop|Count|Totaal|Inkomende pakketdaling saantal van traffic selector mismatch van een tunnel|ConnectionName, RemoteIP|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|RxLightLevel RxLightLevel|RxLightLevel RxLightLevel|Count|Average|Rx Licht niveau in dBm|Link, Rijstrook|
|TxLightLevel TxLightLevel|TxLightLevel TxLightLevel|Count|Average|Tx lichtniveau in dBm|Link, Rijstrook|
|AdminState|AdminState|Count|Average|Beheerdersstatus van de poort|Koppeling|
|LineProtocol|LineProtocol|Count|Average|De status van het lijnprotocol van de poort|Koppeling|
|PortBitsInPerSeconde|BitsinperSeconde|CountPerSeconde|Average|Bits die Azure per seconde binnendringen|Koppeling|
|PortBitsOutPerSeconde|BitsOutperSeconde|CountPerSeconde|Average|Bits die Azure per seconde uitvallen|Koppeling|



## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BitsinperSeconde|BitsinperSeconde|CountPerSeconde|Average|Bits die Azure per seconde binnendringen|PeeringType|
|BitsOutperSeconde|BitsOutperSeconde|CountPerSeconde|Average|Bits die Azure per seconde uitvallen|PeeringType|
|GlobalReachBitsInPerSeconde|GlobalReachBitsInPerSeconde|CountPerSeconde|Average|Bits die Azure per seconde binnendringen|PeeredCircuitSKey|
|GlobalReachBitsOutPerSeconde|GlobalReachBitsOutPerSeconde|CountPerSeconde|Average|Bits die Azure per seconde uitvallen|PeeredCircuitSKey|
|Beschikbaarheid van bgp|Beschikbaarheid van Bgp|Percentage|Average|BGP Beschikbaarheid van MSEE naar alle peers.|PeeringType, Peer|
|ArpBeschikbaarheid|Arp beschikbaarheid|Percentage|Average|ARP Beschikbaarheid van MSEE naar alle peers.|PeeringType, Peer|
|QosDropBitsInPerSeconde|DroppedinBitsPerSeconde|CountPerSeconde|Average|Invallende stukjes gegevens die per seconde zijn gedropt|Geen|
|QosDropBitsOutPerSeconde|DroppedOutBitsPerSeconde|CountPerSeconde|Average|Egress bits van gegevens gedaald per seconde|Geen|

## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BitsinperSeconde|BitsinperSeconde|CountPerSeconde|Average|Bits die Azure per seconde binnendringen|Geen|
|BitsOutperSeconde|BitsOutperSeconde|CountPerSeconde|Average|Bits die Azure per seconde uitvallen|Geen|

## <a name="microsoftnetworkconnections"></a>Microsoft.Network/verbindingen

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BitsinperSeconde|BitsinperSeconde|CountPerSeconde|Average|Bits die Azure per seconde binnendringen|Geen|
|BitsOutperSeconde|BitsOutperSeconde|CountPerSeconde|Average|Bits die Azure per seconde uitvallen|Geen|

## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ErgatewayConnectionBitsInPerSeconde|BitsinperSeconde|CountPerSeconde|Average|Bits die Azure per seconde binnendringen|VerbindingNaam|
|ErgatewayConnectionBitsOutSeconde|BitsOutperSeconde|CountPerSeconde|Average|Bits die Azure per seconde uitvallen|VerbindingNaam|

## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|QpsByEndpoint|Query's op endpoint geretourneerd|Count|Totaal|Het aantal keren dat een eindpunt van een traffic manager in het gegeven tijdsbestek is geretourneerd|EndpointName|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Eindpuntstatus op eindpunt|Count|Maximum|1 als de sondestatus van een eindpunt 'Ingeschakeld' is, 0 anders.|EndpointName|



## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ProbesFailedPercent|% Sondes mislukt|Percentage|Average|% van de connectiviteitsbewakingssondes is mislukt|Geen|
|Gemiddelde RoundtripMs|Avg. Retourtijd (ms)|Milliseconden|Average|Gemiddelde netwerkretourtijd (ms) voor connectiviteitsbewaking sondes verzonden tussen bron en bestemming|Geen|
|Controlesmisluktprocent|Mislukt percentage (voorbeeld)|Percentage|Average|% van de controles op connectiviteitsbewaking is mislukt|SourceAddress,SourceName,SourceResourceid,SourceType,Protocol,DestinationAddress,DestinationName,DestinationResourceid,DestinationType,DestinationPort,TestGroupName,TestConfigurationName|
|RoundTripTimeMs|Retourtijd (ms) (preview)|Milliseconden|Average|Retourtijd in milliseconden voor de controle op connectiviteitsbewaking|SourceAddress,SourceName,SourceResourceid,SourceType,Protocol,DestinationAddress,DestinationName,DestinationResourceid,DestinationType,DestinationPort,TestGroupName,TestConfigurationName|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/voordeuren

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Aantal aanvragen|Aantal aanvragen|Count|Totaal|Het aantal clientaanvragen dat wordt bediend door de HTTP/S-proxy|httpstatus,httpstatusgroep, clientregio, clientland|
|RequestSize|Aanvraaggrootte|Bytes|Totaal|Het aantal bytes dat wordt verzonden als aanvragen van clients naar de HTTP/S-proxy|httpstatus,httpstatusgroep, clientregio, clientland|
|ResponseSize|Reactiegrootte|Bytes|Totaal|Het aantal bytes dat wordt verzonden als antwoorden van HTTP/S-proxy naar clients|httpstatus,httpstatusgroep, clientregio, clientland|
|BillableResponseSize|Factuurbare reactiegrootte|Bytes|Totaal|Het aantal factureerbare bytes (minimaal 2 KB per aanvraag) dat als antwoorden van HTTP/S proxy naar clients wordt verzonden.|httpstatus,httpstatusgroep, clientregio, clientland|
|BackendRequestCount|Aantal back-endaanvragen|Count|Totaal|Het aantal aanvragen dat vanuit de HTTP/S-proxy naar backends wordt verzonden|HttpStatus,httpStatusGroup,Backend|
|BackendRequestLatentie|Latentie voor back-endaanvragen|Milliseconden|Average|De tijd die is berekend vanaf het moment dat de aanvraag door de HTTP/S-proxy naar de backend is verzonden totdat de HTTP/S-proxy de laatste reactiebyte van de backend heeft ontvangen|Back-end|
|TotalLatency TotalLatency|Totale latentie|Milliseconden|Average|De tijd die is berekend vanaf het moment dat de clientaanvraag werd ontvangen door de HTTP/S-proxy totdat de client de laatste reactiebyte van de HTTP/S-proxy heeft erkend|httpstatus,httpstatusgroep, clientregio, clientland|
|Back-endHealthPercentage|Backend-statuspercentage|Percentage|Average|Het percentage succesvolle statussondes van de HTTP/S-proxy naar backends|Backend, BackendPool|
|WebApplicationFirewallRequestCount|Aantal firewallaanvragen voor webtoepassingen|Count|Totaal|Het aantal clientaanvragen dat door de webtoepassingsfirewall wordt verwerkt|PolicyName, RuleName, Actie|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft.Network/privateDnsZones

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|QueryVolume|Queryvolume|Count|Totaal|Aantal query's dat wordt weergegeven voor een privé-DNS-zone|Geen|
|RecordSetCount RecordSetCount RecordSetCount RecordSet|Recordsetaantal|Count|Maximum|Aantal recordsets in een privé-DNS-zone|Geen|
|RecordSetCapacityUsage RecordSetCapacityUsage RecordSetCapacityUsage RecordSet|Capaciteitsbenutting recordset|Percentage|Maximum|Percentage recordsetcapaciteit dat wordt gebruikt door een privé-DNS-zone|Geen|
|VirtualNetworkLinkCount VirtualNetworkLinkCount VirtualNetworkLinkCount VirtualNetwork|Aantal virtuele netwerkkoppelingen|Count|Maximum|Aantal virtuele netwerken dat is gekoppeld aan een privé-DNS-zone|Geen|
|VirtualNetworkLinkCapacityBenut|Capaciteitsbenutting van virtuele netwerkkoppelingen|Percentage|Maximum|Percentage van de capaciteit van virtual network link die wordt gebruikt door een privé-DNS-zone|Geen|
|VirtualNetworkWithRegistrationLinkCount VirtualNetworkWithRegistrationLinkCount VirtualNetworkWithRegistrationLinkCount VirtualNetwork|Aantal koppelingskoppelingen voor virtuele netwerken|Count|Maximum|Aantal virtuele netwerken dat is gekoppeld aan een privé-DNS-zone waarbij automatische registratie is ingeschakeld|Geen|
|VirtualNetworkWithRegistrationCapacityUtilization VirtualNetworkWithRegistrationCapacityUtilization VirtualNetworkWithRegistrationCapacityUtilization VirtualNetwork|Capaciteitsbenutting van de koppeling voor virtuele netwerk|Percentage|Maximum|Percentage van virtual network link met automatische registratiecapaciteit die wordt gebruikt door een private DNS-zone|Geen|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|registratie.alle|Registratiebewerkingen|Count|Totaal|De telling van alle succesvolle registratiebewerkingen (creaties worden query's en verwijderingen bijgewerkt). |Geen|
|registration.create|Registratie Bewerkingen maken|Count|Totaal|De telling van alle succesvolle registratie creaties.|Geen|
|registration.update|Registratieupdatebewerkingen|Count|Totaal|De telling van alle succesvolle registratie-updates.|Geen|
|registration.get|Registratie Leesbewerkingen|Count|Totaal|De telling van alle succesvolle registratiequery's.|Geen|
|registratie.verwijderen|Registratiebewerkingen verwijderen|Count|Totaal|De telling van alle succesvolle registratieverwijderingen.|Geen|
|Inkomende|Binnenkomende berichten|Count|Totaal|De telling van alle succesvolle API-aanroepen verzenden. |Geen|
|inkomend.gepland|Geplande pushmeldingen verzonden|Count|Totaal|Geplande pushmeldingen geannuleerd|Geen|
|inkomende.gepland.annuleren|Geplande pushmeldingen geannuleerd|Count|Totaal|Geplande pushmeldingen geannuleerd|Geen|
|gepland.in afwachting van|Geplande meldingen in behandeling|Count|Totaal|Geplande meldingen in behandeling|Geen|
|installation.all|Installatiebeheerbewerkingen|Count|Totaal|Installatiebeheerbewerkingen|Geen|
|installation.get|Installatiebewerkingen uitvoeren|Count|Totaal|Installatiebewerkingen uitvoeren|Geen|
|installation.upsert|Installatiebewerkingen maken of bijwerken|Count|Totaal|Installatiebewerkingen maken of bijwerken|Geen|
|installation.patch|Installatiebewerkingen voor patch|Count|Totaal|Installatiebewerkingen voor patch|Geen|
|installation.delete|Installatiebewerkingen verwijderen|Count|Totaal|Installatiebewerkingen verwijderen|Geen|
|uitgaande.allpns.succes|Succesvolle meldingen|Count|Totaal|De telling van alle succesvolle meldingen.|Geen|
|outgoing.allpns.invalidpayload|Payload-fouten|Count|Totaal|De telling van duwt die mislukt omdat de PNS een slechte payload fout terug.|Geen|
|uitgaande.allpns.pnsfout|Fouten in extern meldingssysteem|Count|Totaal|Het aantal pushes dat is mislukt omdat er een probleem was met het communiceren met de PNS (exclusief verificatieproblemen).|Geen|
|uitgaande.allpns.channelerror|Kanaalfouten|Count|Totaal|Het aantal pushes dat is mislukt omdat het kanaal ongeldig is, is niet gekoppeld aan de juiste app die is beperkt of verlopen.|Geen|
|uitgaande.allpns.badorexpiredchannel|Foute of verlopen kanaalfouten|Count|Totaal|Het aantal pushes dat is mislukt omdat het kanaal/token/registratieId in de registratie is verlopen of ongeldig is.|Geen|
|outgoing.wns.success|WNS-succesvolle meldingen|Count|Totaal|De telling van alle succesvolle meldingen.|Geen|
|uitgaande.wns.ongeldige referenties|WNS-autorisatiefouten (ongeldige referenties)|Count|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd. (Windows Live herkent de referenties niet).|Geen|
|outgoing.wns.badchannel|WNS Fout slecht kanaal|Count|Totaal|De telling van pushes die mislukt omdat de ChannelURI in de registratie niet werd erkend (WNS-status: 404 niet gevonden).|Geen|
|outgoing.wns.expiredchannel|WNS-verlopen kanaalfout|Count|Totaal|Het aantal pushes dat is mislukt omdat de ChannelURI is verlopen (WNS-status: 410 Gone).|Geen|
|outgoing.wns.throttled|WNS-meldingen voor throttled|Count|Totaal|Het aantal pushes dat is mislukt omdat WNS deze app in de weg staat (WNS-status: 406 Niet acceptabel).|Geen|
|outgoing.wns.tokenprovideronbereikbaar|WNS-autorisatiefouten (onbereikbaar)|Count|Totaal|Windows Live is niet bereikbaar.|Geen|
|outgoing.wns.invalidtoken|WNS-autorisatiefouten (ongeldig token)|Count|Totaal|Het token dat aan WNS wordt verstrekt, is niet geldig (WNS-status: 401 Ongeautoriseerd).|Geen|
|outgoing.wns.wrongtoken|WNS-autorisatiefouten (verkeerde token)|Count|Totaal|Het token dat aan WNS wordt verstrekt is geldig, maar voor een andere toepassing (WNS-status: 403 Verboden). Dit kan gebeuren als de ChannelURI in de registratie is gekoppeld aan een andere app. Controleer of de client-app is gekoppeld aan dezelfde app waarvan de referenties zich in de meldingshub bevinden.|Geen|
|uitgaande.wns.invalidenotatie|WNS ongeldige meldingsnotatie|Count|Totaal|De notatie van de melding is ongeldig (WNS-status: 400). Houd er rekening mee dat WNS niet alle ongeldige payloads afwijst.|Geen|
|outgoing.wns.invalidnotificationsize|FOUT WNS Ongeldige meldingsgrootte|Count|Totaal|De meldingspayload is te groot (WNS-status: 413).|Geen|
|outgoing.wns.channelthrottled|WNS-kanaal beperkt|Count|Totaal|De melding is verwijderd omdat de ChannelURI in de registratie is beperkt (WNS response header: X-WNS-NotificationStatus:channelThrottled).|Geen|
|outgoing.wns.channeldisconnected|WNS-kanaal verbroken|Count|Totaal|De melding is verwijderd omdat de ChannelURI in de registratie is beperkt (WNS response header: X-WNS-DeviceConnectionStatus: disconnected).|Geen|
|outgoing.wns.dropped|WNS-meldingen laten vallen|Count|Totaal|De melding is verwijderd omdat de ChannelURI in de registratie is beperkt (X-WNS-NotificationStatus: gedaald, maar niet X-WNS-DeviceConnectionStatus: losgekoppeld).|Geen|
|uitgaande.wns.pnsfout|WNS-fouten|Count|Totaal|Melding niet geleverd vanwege fouten die communiceren met WNS.|Geen|
|extravert.wns.authenticationfout|WNS-verificatiefouten|Count|Totaal|Melding niet geleverd vanwege fouten die communiceren met ongeldige referenties van Windows Live of verkeerde token.|Geen|
|outgoing.apns.success|APNS-succesvolle meldingen|Count|Totaal|De telling van alle succesvolle meldingen.|Geen|
|uitgaande.apns.invalidereferenties|Apns-autorisatiefouten|Count|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd.|Geen|
|uitgaande.apns.badchannel|Apns Slechte Kanaalfout|Count|Totaal|Het aantal pushes dat is mislukt omdat het token ongeldig is (APNS-statuscode: 8).|Geen|
|uitgaande.apns.verlopenkanaal|Apns-verlopen kanaalfout|Count|Totaal|Het aantal tokendat ongeldig is gemaakt door het APNS-feedbackkanaal.|Geen|
|outgoing.apns.invalidnotificationsize|Apns ongeldige meldingsgrootte fout|Count|Totaal|Het aantal pushes dat is mislukt omdat de payload te groot was (APNS-statuscode: 7).|Geen|
|uitgaande.apns.pnsfout|APNS-fouten|Count|Totaal|Het aantal pushes dat is mislukt vanwege fouten die met APNS communiceren.|Geen|
|outgoing.gcm.success|GCM-succesvolle meldingen|Count|Totaal|De telling van alle succesvolle meldingen.|Geen|
|uitgaande.gcm.ongeldige referenties|GCM-autorisatiefouten (ongeldige referenties)|Count|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd.|Geen|
|outgoing.gcm.badchannel|GCM Bad Channel-fout|Count|Totaal|Het aantal pushes dat is mislukt omdat de registratieId in de registratie niet is erkend (GCM-resultaat: ongeldige registratie).|Geen|
|uitgaande.gcm.verlopenkanaal|GCM-fout verlopen kanaal|Count|Totaal|De telling van pushes die is mislukt omdat de registratieId in de registratie is verlopen (GCM resultaat: Niet Geregistreerd).|Geen|
|outgoing.gcm.throttled|GCM-meldingen voor throttled|Count|Totaal|Het aantal pushes dat is mislukt omdat GCM deze app heeft beperkt (GCM-statuscode: 501-599 of resultaat:Niet beschikbaar).|Geen|
|uitgaande.gcm.invalidenotatie|GCM Ongeldige notificaties|Count|Totaal|Het aantal pushes dat is mislukt omdat de payload niet correct is opgemaakt (GCM-resultaat: InvalidDataKey of InvalidTtl).|Geen|
|uitgaande.gcm.invalidemeldingsgrootte|Fout bij gcm-fout ongeldige meldingsgrootte|Count|Totaal|De telling van pushes die mislukt omdat de payload te groot was (GCM resultaat: MessageTooBig).|Geen|
|uitgaande.gcm.wrongchannel|GCM Verkeerde Kanaalfout|Count|Totaal|Het aantal pushes dat is mislukt omdat de registratieId in de registratie niet is gekoppeld aan de huidige app (GCM-resultaat: InvalidPackageName).|Geen|
|uitgaande.gcm.pnsfout|GCM-fouten|Count|Totaal|Het aantal pushes dat is mislukt vanwege fouten die met GCM communiceren.|Geen|
|uitgaande.gcm.authenticationfout|GCM-verificatiefouten|Count|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd, de referenties worden geblokkeerd of de SenderId is niet correct geconfigureerd in de app (GCM-resultaat: MismatchedSenderId).|Geen|
|outgoing.mpns.success|MPNS-succesvolle meldingen|Count|Totaal|De telling van alle succesvolle meldingen.|Geen|
|uitgaande.mpns.invalidcredentials|MPNS-ongeldige referenties|Count|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd.|Geen|
|uitgaande.mpns.badchannel|MPNS Fout slecht kanaal|Count|Totaal|De telling van pushes die is mislukt omdat de ChannelURI in de registratie niet werd herkend (MPNS-status: 404 niet gevonden).|Geen|
|outgoing.mpns.throttled|MPNS-meldingen voor throttled|Count|Totaal|Het aantal pushes dat is mislukt omdat MPNS deze app throttling (WNS MPNS: 406 Niet acceptabel).|Geen|
|uitgaande.mpns.invalidenotatie|MPNS Ongeldige notificaties|Count|Totaal|De telling van duwt die mislukt omdat de payload van de melding was te groot.|Geen|
|outgoing.mpns.channeldisconnected|MPNS-kanaal verbroken|Count|Totaal|Het aantal pushes dat is mislukt omdat de ChannelURI in de registratie is verbroken (MPNS-status: 412 niet gevonden).|Geen|
|outgoing.mpns.dropped|MPNS-meldingen laten vallen|Count|Totaal|Het aantal pushes dat is gedropt door MPNS (MPNS-antwoordkop: X-NotificationStatus: QueueFull of Suppressed).|Geen|
|uitgaande.mpns.pnsfout|MPNS-fouten|Count|Totaal|Het aantal pushes dat is mislukt vanwege fouten die communiceren met MPNS.|Geen|
|uitgaande.mpns.authenticationfout|MPNS-verificatiefouten|Count|Totaal|Het aantal pushes dat is mislukt omdat de PNS de opgegeven referenties niet heeft geaccepteerd of de referenties zijn geblokkeerd.|Geen|
|notificationhub.pushes|Alle uitgaande meldingen|Count|Totaal|Alle uitgaande meldingen van de meldingshub|Geen|
|inkomende.all.requests|Alle binnenkomende aanvragen|Count|Totaal|Totaal aantal binnenkomende aanvragen voor een meldingshub|Geen|
|inkomende.alle.mislukte aanvragen|Alle binnenkomende mislukte aanvragen|Count|Totaal|Totaal aantal inkomende mislukte aanvragen voor een meldingshub|Geen|

## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/werkruimten

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Average_% Gratis Inodes|% Gratis Inodes|Count|Average|Average_% Gratis Inodes|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_% vrije ruimte|% vrije ruimte|Count|Average|Average_% vrije ruimte|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_% gebruikte inodes|% gebruikte inodes|Count|Average|Average_% gebruikte inodes|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_% gebruikte ruimte|% gebruikte ruimte|Count|Average|Average_% gebruikte ruimte|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Disk Bytes per seconde lezen|Bytes voor schijflezen per seconde|Count|Average|Average_Disk Bytes per seconde lezen|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Disk leest/sec|Schijfleest/sec|Count|Average|Average_Disk leest/sec|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Disk Transfers/sec|Schijfoverdrachten per seconde|Count|Average|Average_Disk Transfers/sec|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Disk Schrijfbytes per seconde|Bytes voor schijfschrijfschrijfberichten per seconde|Count|Average|Average_Disk Schrijfbytes per seconde|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Disk schrijft/sec|Schijfschrijft/sec|Count|Average|Average_Disk schrijft/sec|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Free Megabytes|Gratis Megabytes|Count|Average|Average_Free Megabytes|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Logical schijfbytes per seconde|Logische schijfbytes per seconde|Count|Average|Average_Logical schijfbytes per seconde|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_% beschikbaar geheugen|% beschikbaar geheugen|Count|Average|Average_% beschikbaar geheugen|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_% beschikbare swapruimte|% beschikbare ruilruimte|Count|Average|Average_% beschikbare swapruimte|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_% gebruikt geheugen|% gebruikt geheugen|Count|Average|Average_% gebruikt geheugen|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_% gebruikte swapruimte|% gebruikte swapruimte|Count|Average|Average_% gebruikte swapruimte|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Available MBytes-geheugen|Beschikbaar MBytes-geheugen|Count|Average|Average_Available MBytes-geheugen|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Available MBytes-swap|Beschikbare MBytes-swap|Count|Average|Average_Available MBytes-swap|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Page leest/sec|Pagina leest/sec|Count|Average|Average_Page leest/sec|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Page schrijft/sec|Paginaschrijft/sec|Count|Average|Average_Page schrijft/sec|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Pages/sec|Pagina's/sec|Count|Average|Average_Pages/sec|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Used MBytes-wisselruimte|Gebruikte MBytes-wisselruimte|Count|Average|Average_Used MBytes-wisselruimte|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Used-geheugenmbytes|Bytes voor gebruikt geheugen|Count|Average|Average_Used-geheugenmbytes|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Total verzonden bytes|Totaal aantal verzonden bytes|Count|Average|Average_Total verzonden bytes|Computer, objectnaam, instancename, contrapath, sourcesystem|
|ontvangen Average_Total ontvangen bytes|Totaal aantal ontvangen bytes|Count|Average|ontvangen Average_Total ontvangen bytes|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Total Bytes|Totaal aantal bytes|Count|Average|Average_Total Bytes|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Total verzonden pakketten|Totaal verzonden pakketten|Count|Average|Average_Total verzonden pakketten|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Total ontvangen pakketten|Totaal ontvangen pakketten|Count|Average|Average_Total ontvangen pakketten|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Total Rx-fouten|Totaal aantal rx-fouten|Count|Average|Average_Total Rx-fouten|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Total Tx-fouten|Totaal aantal Tx-fouten|Count|Average|Average_Total Tx-fouten|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Total botsingen|Totale botsingen|Count|Average|Average_Total botsingen|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Avg. Gelezen bytes per seconde|Avg. Schijf sec/read|Count|Average|Average_Avg. Gelezen bytes per seconde|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Avg. Schijfsec/overdracht|Avg. Schijfsec/Overdracht|Count|Average|Average_Avg. Schijfsec/overdracht|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Avg. voor de fysieke schijf|Avg. Schijf sec/write|Count|Average|Average_Avg. voor de fysieke schijf|Computer, objectnaam, instancename, contrapath, sourcesystem|
|schijfbytes per seconde Average_Physical|Bytes van fysieke schijf per seconde|Count|Average|schijfbytes per seconde Average_Physical|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Pct bevoorrechte tijd|Pct bevoorrechte tijd|Count|Average|Average_Pct bevoorrechte tijd|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Pct gebruikerstijd|Pct-gebruikerstijd|Count|Average|Average_Pct gebruikerstijd|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Used geheugenkbytes|KBytes voor gebruikt geheugen|Count|Average|Average_Used geheugenkbytes|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Virtual gedeeld geheugen|Virtueel gedeeld geheugen|Count|Average|Average_Virtual gedeeld geheugen|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_% DPC-tijd|% DPC-tijd|Count|Average|Average_% DPC-tijd|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_% idle tijd|Percentage niet-actieve tijd|Count|Average|Average_% idle tijd|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_% Onderbrekingstijd|% Onderbrekingstijd|Count|Average|Average_% Onderbrekingstijd|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_% IO wachttijd|% IO wachttijd|Count|Average|Average_% IO wachttijd|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_% Leuke tijd|% Mooie tijd|Count|Average|Average_% Leuke tijd|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_% bevoorrechte tijd|% bevoorrechte tijd|Count|Average|Average_% bevoorrechte tijd|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_% processortijd|Percentage processortijd|Count|Average|Average_% processortijd|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_% gebruikerstijd|% gebruikerstijd|Count|Average|Average_% gebruikerstijd|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Free fysiek geheugen|Gratis fysiek geheugen|Count|Average|Average_Free fysiek geheugen|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Free ruimte in pagingbestanden|Vrije ruimte in Paging-bestanden|Count|Average|Average_Free ruimte in pagingbestanden|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Free virtueel geheugen|Gratis virtueel geheugen|Count|Average|Average_Free virtueel geheugen|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Processes|Processen|Count|Average|Average_Processes|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Size opgeslagen in pagingbestanden|Grootte opgeslagen in paging-bestanden|Count|Average|Average_Size opgeslagen in pagingbestanden|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Uptime|Uptime|Count|Average|Average_Uptime|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Users|Gebruikers|Count|Average|Average_Users|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Current-schijfwachtrijlengte|Huidige schijfwachtrijlengte|Count|Average|Average_Current-schijfwachtrijlengte|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Available MBytes|Beschikbare mbytes|Count|Average|Average_Available MBytes|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_% vastgelegde bytes in gebruik|% vastgelegde bytes in gebruik|Count|Average|Average_% vastgelegde bytes in gebruik|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Bytes ontvangen/sec|Ontvangen bytes per seconde|Count|Average|Average_Bytes ontvangen/sec|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Bytes verzonden/sec|Verzonden bytes per seconde|Count|Average|Average_Bytes verzonden/sec|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Bytes Totaal/sec|Totaal aantal bytes per seconde|Count|Average|Average_Bytes Totaal/sec|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Average_Processor wachtrijlengte|Processorwachtrijlengte|Count|Average|Average_Processor wachtrijlengte|Computer, objectnaam, instancename, contrapath, sourcesystem|
|Hartslag|Hartslag|Count|Totaal|Hartslag|Computer, OSType, Versie, SourceComputerId|
|Update|Update|Count|Average|Update|Computer, Product, Classificatie, UpdateState, optioneel, goedgekeurd|
|Gebeurtenis|Gebeurtenis|Count|Average|Gebeurtenis|Bron,EventLog,Computer,EventCategory,EventLevel,EventLevelName, EventID|

## <a name="microsoftpeeringpeeringservices"></a>Microsoft.Peering/peeringServices

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|PrefixLatentie|Latentie voor voorvoegsel|Milliseconden|Average|Mediane voorvoegsellatentie|VoorvoegselNaam|

## <a name="microsoftpeeringpeerings"></a>Microsoft.Peering/peerings

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Beschikbaarheid van sessiesV4|Beschikbaarheid v4 van sessie|Percentage|Average|Beschikbaarheid van de V4-sessie|ConnectionId|
|Beschikbaarheid van sessiesV6|Beschikbaarheid v6 van sessie|Percentage|Average|Beschikbaarheid van de V6-sessie|ConnectionId|
|IngressTrafficRate|Invallen verkeerstarief|BitsPerSeconde|Average|Invallen de verkeerssnelheid in bits per seconde|ConnectionId|
|UitgaandeTrafficRate|Verkeer segress|BitsPerSeconde|Average|Uitgaande verkeerssnelheid in bits per seconde|ConnectionId|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capaciteiten

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Queryduur|Queryduur|Milliseconden|Average|DAX Query duur in laatste interval|Geen dimensies|
|QuerypoolJobQueueLengte|Threads: wachtrijlengte van de querygroeptaak|Count|Average|Aantal taken in de wachtrij van de querythreadgroep.|Geen dimensies|
|qpu_high_utilization_metric|Hoog QPU-gebruik|Count|Totaal|QPU hoog gebruik in de laatste minuut, 1 voor een hoge QPU-gebruik, anders 0|Geen dimensies|
|memory_metric|Geheugen|Bytes|Average|Geheugen. Bereik 0-3 GB voor A1, 0-5 GB voor A2, 0-10 GB voor A3, 0-25 GB voor A4, 0-50 GB voor A5 en 0-100 GB voor A6|Geen dimensies|
|memory_thrashing_metric|Geheugenthrashing|Percentage|Average|Gemiddelde geheugen geseling.|Geen dimensies|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft.ProjectBabylon/accounts

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|AssetDistributionbyclassification|Asset distributie per classificatie|Count|Totaal|Geeft het aantal activa aan waarbij een bepaalde classificatie is toegewezen, d.w.z. ze zijn ingedeeld met dat label.|Classificatie, Bron,ResourceId|
|AssetDistributionByStorageType|Distributie van activa op opslagtype|Count|Totaal|Geeft het aantal elementen aan met een bepaald opslagtype.|StorageType, ResourceId|
|CatalogusActieveGebruikers|Dagelijkse actieve gebruikers|Count|Totaal|Aantal actieve gebruikers dagelijks|ResourceId|
|Catalogusgebruik|Gebruiksdistributie per bewerking|Count|Totaal|Geef het aantal bewerkingen aan dat de gebruiker aan de catalogus maakt, d.w.z. Access, Search, Glossary.|Bewerking, ResourceId|
|NumberOfAssetsWithClassificaties|Aantal activa met ten minste één classificatie|Count|Average|Geeft het aantal elementen aan met ten minste één tagclassificatie.|ResourceId|
|ScanCancelled|Scan geannuleerd|Count|Totaal|Geeft het aantal geannuleerde scans aan.|ResourceId|
|Scannen voltooid|Scan voltooid|Count|Totaal|Geeft het aantal scans aan dat is voltooid.|ResourceId|
|Scannen mislukt|Scannen is mislukt|Count|Totaal|Geeft aan dat het aantal scans is mislukt.|ResourceId|
|ScanTimeTaken|Scan tijd genomen|Seconden|Totaal|Geeft de totale scantijd in seconden aan.|ResourceId|




## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/naamruimten

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ListenerConnections-Succes|ListenerConnections-Succes|Count|Totaal|Succesvolle listenerverbindingen voor Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-ClientError|ListenerConnections-ClientError|Count|Totaal|ClientError op ListenerConnections voor Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-ServerFout|ListenerConnections-ServerFout|Count|Totaal|ServerError op ListenerConnections voor Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-Succes|SenderConnections-Succes|Count|Totaal|Succesvolle senderconnections voor Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-ClientError|SenderConnections-ClientError|Count|Totaal|ClientError op SenderConnections voor Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-ServerD|SenderConnections-ServerD|Count|Totaal|ServerError op SenderConnections voor Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-TotaalAanvragen|ListenerConnections-TotaalAanvragen|Count|Totaal|Totale listenerverbindingen voor Microsoft.Relay.|Entiteitsnaam|
|SenderConnections-TotaalAanvragen|SenderConnections-TotaalAanvragen|Count|Totaal|Total SenderConnections-aanvragen voor Microsoft.Relay.|Entiteitsnaam|
|Actieve verbindingen|Actieve verbindingen|Count|Totaal|Totale ActiveConnections voor Microsoft.Relay.|Entiteitsnaam|
|Actieveluisteraars|Actieveluisteraars|Count|Totaal|Totaal aantal actieve listeners voor Microsoft.Relay.|Entiteitsnaam|
|Bytes overgedragen|Bytes overgedragen|Count|Totaal|Totaal aantal overgedragen bytes voor Microsoft.Relay.|Entiteitsnaam|
|ListenerKoppelt de verbinding|ListenerKoppelt de verbinding|Count|Totaal|Totale listenerKoppelt zich af voor Microsoft.Relay.|Entiteitsnaam|
|Afzenderkoppelt|Afzenderkoppelt|Count|Totaal|Total SenderDisconnects for Microsoft.Relay.|Entiteitsnaam|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Zoeklatentie|Zoeklatentie|Seconden|Average|Gemiddelde zoeklatentie voor de zoekservice|Geen|
|ZoekopdrachtenPerSeconde|Zoekopdrachten per seconde|CountPerSeconde|Average|Zoekopdrachten per seconde voor de zoekservice|Geen|
|ThrottledSearchQueriesPercentage|Percentage gewurgde zoekopdrachten|Percentage|Average|Percentage zoekopdrachten dat is beperkt voor de zoekservice|Geen|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/naamruimten

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Succesvolle verzoeken|Succesvolle aanvragen|Count|Totaal|Totaal aantal geslaagde aanvragen voor een naamruimte|EntityName, OperationResult|
|Serverfouten|Serverfouten.|Count|Totaal|Serverfouten voor Microsoft.ServiceBus.|EntityName, OperationResult|
|Gebruikersfouten|Gebruikersfouten.|Count|Totaal|Gebruikersfouten voor Microsoft.ServiceBus.|EntityName, OperationResult|
|ThrottledRequests|Throttled Requests.|Count|Totaal|Throttled Requests for Microsoft.ServiceBus.|EntityName, OperationResult|
|Binnenkomende aanvragen|Binnenkomende aanvragen|Count|Totaal|Binnenkomende aanvragen voor Microsoft.ServiceBus.|Entiteitsnaam|
|Binnenkomende berichten|Binnenkomende berichten|Count|Totaal|Binnenkomende berichten voor Microsoft.ServiceBus.|Entiteitsnaam|
|Uitgaande Berichten|Uitgaande berichten|Count|Totaal|Uitgaande berichten voor Microsoft.ServiceBus.|Entiteitsnaam|
|Actieve verbindingen|Actieve verbindingen|Count|Totaal|Totale actieve verbindingen voor Microsoft.ServiceBus.|Geen|
|Verbindingengeopend|Verbindingen geopend.|Count|Average|Verbindingen geopend voor Microsoft.ServiceBus.|Entiteitsnaam|
|Verbindingengesloten|Verbindingen gesloten.|Count|Average|Verbindingen gesloten voor Microsoft.ServiceBus.|Entiteitsnaam|
|Grootte|Grootte|Bytes|Average|Grootte van een wachtrij/onderwerp in bytes.|Entiteitsnaam|
|Berichten|Aantal berichten in een wachtrij/onderwerp.|Count|Average|Aantal berichten in een wachtrij/onderwerp.|Entiteitsnaam|
|ActiveMessages|Aantal actieve berichten in een wachtrij/onderwerp.|Count|Average|Aantal actieve berichten in een wachtrij/onderwerp.|Entiteitsnaam|
|DeadletteredMessages|Aantal dode-geletterde berichten in een wachtrij/onderwerp.|Count|Average|Aantal dode-geletterde berichten in een wachtrij/onderwerp.|Entiteitsnaam|
|Geplande berichten|Aantal geplande berichten in een wachtrij/onderwerp.|Count|Average|Aantal geplande berichten in een wachtrij/onderwerp.|Entiteitsnaam|
|NamespaceCpuUsage|CPU|Percentage|Maximum|Service bus premium namespace CPU gebruik metrische.|Replica|
|NamespaceMemoryUsage|Geheugengebruik|Percentage|Maximum|Service bus premium naamruimte geheugen gebruik metrische.|Replica|
|CPUXNS CPUXNS|CPU (afgeschaft)|Percentage|Maximum|Service bus premium namespace CPU gebruik metrische. Deze statistiek wordt onteigend. Gebruik in plaats daarvan de CPU-statistiek (NamespaceCpuUsage).|Replica|
|WSXNS|Geheugengebruik (afgeschaft)|Percentage|Maximum|Service bus premium naamruimte geheugen gebruik metrische. Deze statistiek is afgeschaft. Gebruik in plaats daarvan de statistiek Geheugengebruik (NamespaceMemoryUsage).|Replica|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/toepassingen

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ToegewezenCpu|ToegewezenCpu|Count|Average|Cpu toegewezen aan deze container in milli-cores|ApplicationName, ServiceName,CodePackageName,ServiceReplicaName|
|ToegewezenGeheugen|ToegewezenGeheugen|Bytes|Average|Geheugen toegewezen aan deze container in MB|ApplicationName, ServiceName,CodePackageName,ServiceReplicaName|
|Werkelijke Cpu|Werkelijke Cpu|Count|Average|Werkelijke CPU-gebruik in milli-cores|ApplicationName, ServiceName,CodePackageName,ServiceReplicaName|
|Werkelijkememory|Werkelijkememory|Bytes|Average|Werkelijke geheugengebruik in MB|ApplicationName, ServiceName,CodePackageName,ServiceReplicaName|
|Cpu-gebruik|Cpu-gebruik|Percentage|Average|Gebruik van CPU voor deze container als percentage van ToegewezenCpu|ApplicationName, ServiceName,CodePackageName,ServiceReplicaName|
|Geheugengebruik|Geheugengebruik|Percentage|Average|Gebruik van CPU voor deze container als percentage van ToegewezenCpu|ApplicationName, ServiceName,CodePackageName,ServiceReplicaName|
|ApplicationStatus|ApplicationStatus|Count|Average|Status van de toepassing Mesh-structuur van servicefabric|ApplicationName, Status|
|ServiceStatus|ServiceStatus|Count|Average|Status van een service in servicefabric mesh-toepassing|ApplicationName, Status,ServiceName|
|ServiceReplicaStatus|ServiceReplicaStatus|Count|Average|Status van een servicereplica in de toepassing Service Fabric Mesh|ApplicationName, Status,ServiceName, ServiceReplicaName|
|ContainerStatus|ContainerStatus|Count|Average|Status van de container in servicefabric mesh-toepassing|ApplicationName, ServiceName,CodePackageName,ServiceReplicaName,Status|
|Aantal opnieuw starten|Aantal opnieuw starten|Count|Average|Het aantal containers opnieuw starten in de toepassing Service Fabric Mesh|ApplicationName, Status,ServiceName,ServiceReplicaName,CodePackageName|

## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Aantal verbindingen|Aantal verbindingen|Count|Maximum|De hoeveelheid gebruikersverbinding.|Eindpunt|
|Aantal berichten|Aantal berichten|Count|Totaal|Het totale aantal berichten.|Geen|
|Binnenkomend verkeer|Binnenkomend verkeer|Bytes|Totaal|Het inkomende verkeer van de dienst|Geen|
|UitgaandVerkeer|Uitgaand verkeer|Bytes|Totaal|Het uitgaande serviceverkeer|Geen|
|Gebruikersfouten|Gebruikersfouten|Percentage|Maximum|Het percentage gebruikersfouten|Geen|
|Systeemfouten|Systeemfouten|Percentage|Maximum|Het percentage systeemfouten|Geen|



## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Percentage|Average|CPU-percentage|Geen|
|physical_data_read_percent|Gegevens-I/O-percentage|Percentage|Average|Gegevens-I/O-percentage|Geen|
|log_write_percent|Io-percentage logboek|Percentage|Average|Log IO-percentage. Niet van toepassing op datawarehouses.|Geen|
|dtu_consumption_percent|DTU-percentage|Percentage|Average|DTU-percentage. Is van toepassing op DTU-gebaseerde databases.|Geen|
|storage|Gebruikte gegevensruimte|Bytes|Maximum|Gebruikte gegevensruimte. Niet van toepassing op datawarehouses.|Geen|
|connection_successful|Succesvolle verbindingen|Count|Totaal|Succesvolle verbindingen|Geen|
|connection_failed|Mislukte verbindingen|Count|Totaal|Mislukte verbindingen|Geen|
|blocked_by_firewall|Geblokkeerd door firewall|Count|Totaal|Geblokkeerd door firewall|Geen|
|Impasse|Deadlocks|Count|Totaal|Deadlocks. Niet van toepassing op datawarehouses.|Geen|
|storage_percent|Gebruikte gegevensruimte procenten|Percentage|Maximum|Dataruimte gebruikt procent. Niet van toepassing op datawarehouses of hyperscale databases.|Geen|
|xtp_storage_percent|In-Memory OLTP-opslagpercentage|Percentage|Average|In-Memory OLTP-opslagpercentage. Niet van toepassing op datawarehouses.|Geen|
|workers_percent|Percentage werknemers|Percentage|Average|Percentage werknemers. Niet van toepassing op datawarehouses.|Geen|
|sessions_percent|Percentage Sessies|Percentage|Average|Percentage sessies. Niet van toepassing op datawarehouses.|Geen|
|dtu_limit|DTU-limiet|Count|Average|DTU-limiet. Is van toepassing op DTU-gebaseerde databases.|Geen|
|dtu_used|DTU gebruikt|Count|Average|DTU gebruikt. Is van toepassing op DTU-gebaseerde databases.|Geen|
|cpu_limit|CPU-limiet|Count|Average|CPU-limiet. Is van toepassing op vCore-gebaseerde databases.|Geen|
|cpu_used|CPU gebruikt|Count|Average|CPU gebruikt. Is van toepassing op vCore-gebaseerde databases.|Geen|
|dwu_limit|DWU-limiet|Count|Maximum|DWU-limiet. Geldt alleen voor datawarehouses.|Geen|
|dwu_consumption_percent|DWU-percentage|Percentage|Maximum|DWU-percentage. Geldt alleen voor datawarehouses.|Geen|
|dwu_used|Gebruikte DWU|Count|Maximum|DWU gebruikt. Geldt alleen voor datawarehouses.|Geen|
|cache_hit_percent|Percentage cachehit|Percentage|Maximum|Cache hit percentage. Geldt alleen voor datawarehouses.|Geen|
|cache_used_percent|Door cache gebruikt percentage|Percentage|Maximum|Cache gebruikt percentage. Geldt alleen voor datawarehouses.|Geen|
|sqlserver_process_core_percent<sup>1.</sup> |SQL Server-proceskernpercentage|Percentage|Maximum|CPU-gebruikspercentage voor het SQL Server-proces, gemeten door het besturingssysteem.|Geen|
|sqlserver_process_memory_percent<sup>1.</sup> |SQL Server-procesgeheugenpercentage|Percentage|Maximum|Percentage geheugengebruik voor het SQL Server-proces, gemeten door het besturingssysteem.|Geen|
|tempdb_data_size<sup>2.</sup> |Tempdb-gegevensbestandsgrootte Kilobytes|Count|Maximum|Tempdb Data File Size Kilobytes.|Geen|
|tempdb_log_size<sup>2.</sup> |Tempdb Log File Size Kilobytes|Count|Maximum|Tempdb Log File Size Kilobytes.|Geen|
|tempdb_log_used_percent<sup>2.</sup> |Tempdb Procent Log gebruikt|Percentage|Maximum|Tempdb Procent Log gebruikt.|Geen|
|local_tempdb_usage_percent|Lokaal tempdb percentage|Percentage|Average|Lokaal tempdb percentage. Geldt alleen voor datawarehouses.|Geen|
|app_cpu_billed|App CPU gefactureerd|Count|Totaal|App CPU gefactureerd. Van toepassing op serverloze databases.|Geen|
|app_cpu_percent|Cpu-percentage voor apps|Percentage|Average|App CPU-percentage. Van toepassing op serverloze databases.|Geen|
|app_memory_percent|Percentage app-geheugen|Percentage|Average|App-geheugenpercentage. Van toepassing op serverloze databases.|Geen|
|allocated_data_storage|Toegewezen gegevensruimte|Bytes|Average|Toegewezen gegevensopslag. Niet van toepassing op datawarehouses.|Geen|
|memory_usage_percent|Geheugenpercentage|Percentage|Maximum|Geheugenpercentage. Geldt alleen voor datawarehouses.|Geen|
|dw_backup_size_gb|Grootte van gegevensopslag|Count|Totaal|De grootte van de gegevensopslag bestaat uit de grootte van uw gegevens en het transactielogboek. De statistiek wordt meegeteld voor het gedeelte 'Opslag' van uw factuur. Geldt alleen voor datawarehouses.|Geen|
|dw_snapshot_size_gb|Momentopnameopslaggrootte|Count|Totaal|Momentopnameopslaggrootte is de grootte van de incrementele wijzigingen die zijn vastgelegd door momentopnamen om door de gebruiker gedefinieerde en automatische herstelpunten te maken. De statistiek wordt meegeteld voor het gedeelte 'Opslag' van uw factuur. Geldt alleen voor datawarehouses.|Geen|
|dw_geosnapshot_size_gb|Opslaggrootte na herstel na noodgevallen|Count|Totaal|Storage size disaster recovery wordt weerspiegeld als 'Disaster Recovery Storage' in uw factuur. Geldt alleen voor datawarehouses.|Geen|
|wlg_allocation_relative_to_system_percent|Toewijzing workloadgroep per systeempercentage|Percentage|Maximum|Toegewezen percentage resources ten opzichte van het hele systeem per werkbelastinggroep. Geldt alleen voor datawarehouses.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Toewijzing workloadgroep op cap resourcepercentage|Percentage|Maximum|Toegewezen percentage resources ten opzichte van de opgegeven limietresources per werkbelastinggroep. Geldt alleen voor datawarehouses.|WorkloadGroupName, IsUserDefined|
|wlg_active_queries|Actieve query's voor workloadgroep|Count|Totaal|Actieve query's binnen de werkbelastinggroep. Geldt alleen voor datawarehouses.|WorkloadGroupName, IsUserDefined|
|wlg_queued_queries|Query's in de groep Werkbelasting in de wachtrij|Count|Totaal|Query's in de wachtrij binnen de werkbelastinggroep. Geldt alleen voor datawarehouses.|WorkloadGroupName, IsUserDefined|
|active_queries|Actieve query's|Count|Totaal|Actieve query's voor alle werkbelastinggroepen. Geldt alleen voor datawarehouses.|Geen|
|queued_queries|Query's in de wachtrij|Count|Totaal|Query's in de wachtrij voor alle werkbelastinggroepen. Geldt alleen voor datawarehouses.|Geen|
|wlg_active_queries_timeouts|Time-outs van werkgroepquery's|Count|Totaal|Query's die een time-out hebben voor de werkbelastinggroep. Geldt alleen voor datawarehouses.|WorkloadGroupName, IsUserDefined|
|wlg_effective_min_resource_percent|Effectief min resource percentage|Percentage|Maximum|Minimumpercentage van de gereserveerde en geïsoleerde resources voor de werkbelastinggroep, rekening houdend met het minimumniveau van de service. Geldt alleen voor datawarehouses.|WorkloadGroupName, IsUserDefined|
|wlg_effective_cap_resource_percent|Percentage effectieve cap-resources|Percentage|Maximum|Een harde limiet voor het percentage resources dat is toegestaan voor de werkbelastinggroep, rekening houdend met het effectieve Min Resource Percentage dat is toegewezen voor andere werkbelastinggroepen. Geldt alleen voor datawarehouses.|WorkloadGroupName, IsUserDefined|
|full_backup_size_bytes|Volledige grootte van de back-upopslag|Bytes|Maximum|Cumulatieve volledige back-upopslaggrootte. Is van toepassing op vCore-gebaseerde databases. Niet van toepassing op Hyperscale-databases.|Geen|
|diff_backup_size_bytes|Differentiële grootte van de back-upopslag|Bytes|Maximum|Cumulatieve differentiële opslaggrootte. Is van toepassing op vCore-gebaseerde databases. Niet van toepassing op Hyperscale-databases.|Geen|
|log_backup_size_bytes|Grootte van de opslagvan logboekback-ups|Bytes|Maximum|Cumulatieve opslaggrootte van logboekback-ups. Is van toepassing op vCore-gebaseerde en Hyperscale-databases.|Geen|
|snapshot_backup_size_bytes|Momentopname back-upopslaggrootte|Bytes|Maximum|Cumulatieve momentopname-opslaggrootte. Is van toepassing op Hyperscale-databases.|Geen|
|base_blob_size_bytes|Opslaggrootte van basisblob|Bytes|Maximum|De opslaggrootte van de basisblob. Is van toepassing op Hyperscale-databases.|Geen|

<sup>1</sup> Deze statistiek is beschikbaar voor databases met behulp van het vCore-inkoopmodel met 2 vCores en hoger, of 200 DTU en hoger voor DTU-gebaseerde inkoopmodellen. 

<sup>2</sup> Deze statistiek is beschikbaar voor databases met behulp van het vCore-inkoopmodel met 2 vCores en hoger, of 200 DTU en hoger voor DTU-gebaseerde inkoopmodellen. Deze statistiek is momenteel niet beschikbaar voor Hyperscale-databases of gegevensmagazijnen.

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|cpu_percent|CPU-percentage|Percentage|Average|CPU-percentage|Geen|
|database_cpu_percent|CPU-percentage|Percentage|Average|CPU-percentage|DatabaseResourceId|
|physical_data_read_percent|Gegevens-I/O-percentage|Percentage|Average|Gegevens-I/O-percentage|Geen|
|database_physical_data_read_percent|Gegevens-I/O-percentage|Percentage|Average|Gegevens-I/O-percentage|DatabaseResourceId|
|log_write_percent|Io-percentage logboek|Percentage|Average|Io-percentage logboek|Geen|
|database_log_write_percent|Io-percentage logboek|Percentage|Average|Io-percentage logboek|DatabaseResourceId|
|dtu_consumption_percent|DTU-percentage|Percentage|Average|DTU-percentage. Geldt voor elastische pools op basis van DTU.|Geen|
|database_dtu_consumption_percent|DTU-percentage|Percentage|Average|DTU-percentage|DatabaseResourceId|
|storage_percent|Gebruikte gegevensruimte procenten|Percentage|Average|Gebruikte gegevensruimte procenten|Geen|
|workers_percent|Percentage werknemers|Percentage|Average|Percentage werknemers|Geen|
|database_workers_percent|Percentage werknemers|Percentage|Average|Percentage werknemers|DatabaseResourceId|
|sessions_percent|Percentage Sessies|Percentage|Average|Percentage Sessies|Geen|
|database_sessions_percent|Percentage Sessies|Percentage|Average|Percentage Sessies|DatabaseResourceId|
|eDTU_limit|eDTU-limiet|Count|Average|eDTU-limiet. Geldt voor elastische pools op basis van DTU.|Geen|
|storage_limit|Maximale grootte van gegevens|Bytes|Average|Maximale grootte van gegevens|Geen|
|eDTU_used|eDTU gebruikt|Count|Average|eDTU gebruikt. Geldt voor elastische pools op basis van DTU.|Geen|
|database_eDTU_used|eDTU gebruikt|Count|Average|eDTU gebruikt|DatabaseResourceId|
|storage_used|Gebruikte gegevensruimte|Bytes|Average|Gebruikte gegevensruimte|Geen|
|database_storage_used|Gebruikte gegevensruimte|Bytes|Average|Gebruikte gegevensruimte|DatabaseResourceId|
|xtp_storage_percent|In-Memory OLTP-opslagpercentage|Percentage|Average|In-Memory OLTP-opslagpercentage|Geen|
|cpu_limit|CPU-limiet|Count|Average|CPU-limiet. Van toepassing op vCore-gebaseerde elastische pools.|Geen|
|database_cpu_limit|CPU-limiet|Count|Average|CPU-limiet|DatabaseResourceId|
|cpu_used|CPU gebruikt|Count|Average|CPU gebruikt. Van toepassing op vCore-gebaseerde elastische pools.|Geen|
|database_cpu_used|CPU gebruikt|Count|Average|CPU gebruikt|DatabaseResourceId|
|sqlserver_process_core_percent<sup>1.</sup>|SQL Server-proceskernpercentage|Percentage|Maximum|CPU-gebruikspercentage voor het SQL Server-proces, gemeten door het besturingssysteem. Geldt voor elastische zwembaden. |Geen|
|sqlserver_process_memory_percent<sup>1.</sup>|SQL Server-procesgeheugenpercentage|Percentage|Maximum|Percentage geheugengebruik voor het SQL Server-proces, gemeten door het besturingssysteem. Geldt voor elastische zwembaden. |Geen|
|tempdb_data_size<sup>2.</sup>|Tempdb-gegevensbestandsgrootte Kilobytes|Count|Maximum|Tempdb Data File Size Kilobytes.|Geen|
|tempdb_log_size<sup>2.</sup>|Tempdb Log File Size Kilobytes|Count|Maximum|Tempdb Log File Size Kilobytes. |Geen|
|tempdb_log_used_percent<sup>2.</sup>|Tempdb Procent Log gebruikt|Percentage|Maximum|Tempdb Procent Log gebruikt.|Geen|
|allocated_data_storage|Toegewezen gegevensruimte|Bytes|Average|Toegewezen gegevensruimte|Geen|
|database_allocated_data_storage|Toegewezen gegevensruimte|Bytes|Average|Toegewezen gegevensruimte|DatabaseResourceId|
|allocated_data_storage_percent|Toegewezen gegevensruimte percentage|Percentage|Maximum|Toegewezen gegevensruimte percentage|Geen|

<sup>1</sup> Deze statistiek is beschikbaar voor databases met behulp van het vCore-inkoopmodel met 2 vCores en hoger, of 200 DTU en hoger voor DTU-gebaseerde inkoopmodellen. 

<sup>2</sup> Deze statistiek is beschikbaar voor databases met behulp van het vCore-inkoopmodel met 2 vCores en hoger, of 200 DTU en hoger voor DTU-gebaseerde inkoopmodellen. Deze statistiek is momenteel niet beschikbaar voor Hyperscale-databases.


## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|dtu_consumption_percent|DTU-percentage|Percentage|Average|DTU-percentage|ElasticPoolResourceid|
|database_dtu_consumption_percent|DTU-percentage|Percentage|Average|DTU-percentage|DatabaseResourceid, ElasticPoolResourceid|
|storage_used|Gebruikte gegevensruimte|Bytes|Average|Gebruikte gegevensruimte|ElasticPoolResourceid|
|database_storage_used|Gebruikte gegevensruimte|Bytes|Average|Gebruikte gegevensruimte|DatabaseResourceid, ElasticPoolResourceid|
|dtu_used|DTU gebruikt|Count|Average|DTU gebruikt|DatabaseResourceId|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|virtual_core_count|Aantal virtuele kernen|Count|Average|Aantal virtuele kernen|Geen|
|avg_cpu_percent|Gemiddeld CPU-percentage|Percentage|Average|Gemiddeld CPU-percentage|Geen|
|reserved_storage_mb|Opslagruimte gereserveerd|Count|Average|Opslagruimte gereserveerd|Geen|
|storage_space_used_mb|Gebruikte opslagruimte|Count|Average|Gebruikte opslagruimte|Geen|
|io_requests|Aantal IO-aanvragen|Count|Average|Aantal IO-aanvragen|Geen|
|io_bytes_read|GELEZEN IO-bytes|Bytes|Average|GELEZEN IO-bytes|Geen|
|io_bytes_written|IO-bytes geschreven|Bytes|Average|IO-bytes geschreven|Geen|



## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Gebruikte capaciteit|Gebruikte capaciteit|Bytes|Average|Gebruikte capaciteit account|Geen|
|Transacties|Transacties|Count|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. De dimensie ResponseType gebruiken voor het aantal verschillende typen reacties.|ResponseType,GeoType,ApiName,Authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid binnenkomende gegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|GeoType, ApiName, Verificatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgangsgegevens in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|GeoType, ApiName, Verificatie|
|SuccessServerLatentie|Geslaagde serverlatentie|Milliseconden|Average|De gemiddelde latentie die Azure Storage gebruikt om een succesvolle aanvraag in milliseconden te verwerken. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|GeoType, ApiName, Verificatie|
|SuccesE2ELatentie|Success E2E Latency|Milliseconden|Average|De gemiddelde end-to-end latentie van succesvolle aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|GeoType, ApiName, Verificatie|
|Beschikbaarheid|Beschikbaarheid|Percentage|Average|Het percentage beschikbaarheid voor de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|GeoType, ApiName, Verificatie|

## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|BlobCapaciteit|Blob-capaciteit|Bytes|Average|De hoeveelheid opslagruimte die wordt gebruikt door de Blob-service van het opslagaccount in bytes.|BlobType, Laag|
|BlobCount|Blobtelling|Count|Average|Het aantal Blob in de Blob-service van het opslagaccount.|BlobType, Laag|
|ContainerCount ContainerCount|Aantal Blob-containers|Count|Average|Het aantal containers in de Blob-service van het opslagaccount.|Geen|
|Indexcapaciteit|Indexcapaciteit|Bytes|Average|De hoeveelheid opslagruimte die wordt gebruikt door de ADLS Gen2 -index (Hiërarchische) index in bytes.|Geen|
|Transacties|Transacties|Count|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. De dimensie ResponseType gebruiken voor het aantal verschillende typen reacties.|ResponseType,GeoType,ApiName,Authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid binnenkomende gegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|GeoType, ApiName, Verificatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgangsgegevens in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|GeoType, ApiName, Verificatie|
|SuccessServerLatentie|Geslaagde serverlatentie|Milliseconden|Average|De gemiddelde latentie die Azure Storage gebruikt om een succesvolle aanvraag in milliseconden te verwerken. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|GeoType, ApiName, Verificatie|
|SuccesE2ELatentie|Success E2E Latency|Milliseconden|Average|De gemiddelde end-to-end latentie van succesvolle aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|GeoType, ApiName, Verificatie|
|Beschikbaarheid|Beschikbaarheid|Percentage|Average|Het percentage beschikbaarheid voor de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|GeoType, ApiName, Verificatie|

## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Tabelcapaciteit|Tabelcapaciteit|Bytes|Average|De hoeveelheid opslagruimte die wordt gebruikt door de tabelservice van het opslagaccount in bytes.|Geen|
|Tabeltelling|Tabelaantal|Count|Average|Het aantal tabel in de tabelservice van het opslagaccount.|Geen|
|Tabelentiteittelling|Aantal tabelentiteiten|Count|Average|Het aantal tabelentiteiten in de tabelservice van het opslagaccount.|Geen|
|Transacties|Transacties|Count|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. De dimensie ResponseType gebruiken voor het aantal verschillende typen reacties.|ResponseType,GeoType,ApiName,Authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid binnenkomende gegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|GeoType, ApiName, Verificatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgangsgegevens in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|GeoType, ApiName, Verificatie|
|SuccessServerLatentie|Geslaagde serverlatentie|Milliseconden|Average|De gemiddelde latentie die Azure Storage gebruikt om een succesvolle aanvraag in milliseconden te verwerken. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|GeoType, ApiName, Verificatie|
|SuccesE2ELatentie|Success E2E Latency|Milliseconden|Average|De gemiddelde end-to-end latentie van succesvolle aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|GeoType, ApiName, Verificatie|
|Beschikbaarheid|Beschikbaarheid|Percentage|Average|Het percentage beschikbaarheid voor de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|GeoType, ApiName, Verificatie|

## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Bestandscapaciteit|Bestandscapaciteit|Bytes|Average|De hoeveelheid opslagruimte die wordt gebruikt door de bestandsservice van het opslagaccount in bytes.|FileShare|
|FileCount|Aantal bestanden|Count|Average|Het aantal bestanden in de bestandsservice van het opslagaccount.|FileShare|
|FileShareCount|Aantal bestandsdelen|Count|Average|Het aantal bestandsshares in de bestandsservice van de opslagrekening.|Geen|
|FileShareSnapshotCount|Momentopnameaantal momentopnamen voor bestandsshare|Count|Average|Het aantal momentopnamen dat aanwezig is op het aandeel in de Bestandenservice van het opslagaccount.|FileShare|
|FileShareSnapshotSize|Momentopname van bestandsshare|Bytes|Average|De hoeveelheid opslagruimte die wordt gebruikt door de momentopnamen in de bestandsservice van het opslagaccount in bytes.|FileShare|
|FileShareQuota|Quotumgrootte voor bestandsshare|Bytes|Average|De bovengrens voor de hoeveelheid opslagruimte die door Azure Files Service in bytes kan worden gebruikt.|FileShare|
|Transacties|Transacties|Count|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. De dimensie ResponseType gebruiken voor het aantal verschillende typen reacties.|ResponseType,GeoType,ApiName,Authentication,FileShare|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid binnenkomende gegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|Geotype,ApiName, Authenticatie,FileShare|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgangsgegevens in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|Geotype,ApiName, Authenticatie,FileShare|
|SuccessServerLatentie|Geslaagde serverlatentie|Milliseconden|Average|De gemiddelde latentie die Azure Storage gebruikt om een succesvolle aanvraag in milliseconden te verwerken. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|Geotype,ApiName, Authenticatie,FileShare|
|SuccesE2ELatentie|Success E2E Latency|Milliseconden|Average|De gemiddelde end-to-end latentie van succesvolle aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|Geotype,ApiName, Authenticatie,FileShare|
|Beschikbaarheid|Beschikbaarheid|Percentage|Average|Het percentage beschikbaarheid voor de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|Geotype,ApiName, Authenticatie,FileShare|

## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Wachtrijcapaciteit|Wachtrijcapaciteit|Bytes|Average|De hoeveelheid opslagruimte die wordt gebruikt door de wachtrijservice van het opslagaccount in bytes.|Geen|
|Aantal wachtrijen|Aantal wachtrijen|Count|Average|Het aantal wachtrijen in de wachtrijservice van het opslagaccount.|Geen|
|Aantal queuemessage's|Aantal wachtrijberichten|Count|Average|Het geschatte aantal wachtrijberichten in de wachtrijservice van het opslagaccount.|Geen|
|Transacties|Transacties|Count|Totaal|Het aantal aanvragen voor een opslagservice of de opgegeven API-bewerking. Dit is inclusief geslaagde en mislukte aanvragen, evenals aanvragen waarbij fouten zijn opgetreden. De dimensie ResponseType gebruiken voor het aantal verschillende typen reacties.|ResponseType,GeoType,ApiName,Authenticatie|
|Inkomend verkeer|Inkomend verkeer|Bytes|Totaal|De hoeveelheid binnenkomende gegevens, in bytes. Hieronder vallen de inkomende gegevens van een externe client in Azure Storage evenals de inkomende gegevens binnen Azure.|GeoType, ApiName, Verificatie|
|Uitgaand verkeer|Uitgaand verkeer|Bytes|Totaal|De hoeveelheid uitgangsgegevens in bytes. Hieronder vallen de uitgaande gegevens van een externe client in Azure Storage evenals de uitgaande gegevens binnen Azure. Daarom geeft deze hoeveelheid niet de factureerbare uitgaande gegevens weer.|GeoType, ApiName, Verificatie|
|SuccessServerLatentie|Geslaagde serverlatentie|Milliseconden|Average|De gemiddelde latentie die Azure Storage gebruikt om een succesvolle aanvraag in milliseconden te verwerken. Deze waarde bevat niet de netwerklatentie die is opgegeven in AverageE2ELatency.|GeoType, ApiName, Verificatie|
|SuccesE2ELatentie|Success E2E Latency|Milliseconden|Average|De gemiddelde end-to-end latentie van succesvolle aanvragen voor een opslagservice of de opgegeven API-bewerking, in milliseconden. Deze waarde bevat de vereiste verwerkingstijd in Azure Storage die nodig is om de aanvraag te lezen, het antwoord te verzenden en bevestiging van het antwoord te ontvangen.|GeoType, ApiName, Verificatie|
|Beschikbaarheid|Beschikbaarheid|Percentage|Average|Het percentage beschikbaarheid voor de opslagservice of de opgegeven API-bewerking. De beschikbaarheid wordt berekend door de waarde TotalBillableRequests te delen door het aantal van toepassing zijnde aanvragen, inclusief de aanvragen die onverwachte fouten produceren. Alle onverwachte fouten leiden tot een afgenomen beschikbaarheid voor de opslagservice of de opgegeven API-bewerking.|GeoType, ApiName, Verificatie|





## <a name="microsoftstoragecachecaches"></a>Microsoft.StorageCache/caches

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ClientIOPS|Totaal aantal client-IOPS|Count|Average|De snelheid van clientbestandenbewerkingen die door de cache worden verwerkt.|Geen|
|ClientLatentie|Gemiddelde klantlatentie|Milliseconden|Average|Gemiddelde latentie van clientbestandsbewerkingen naar de opslagcache.|Geen|
|ClientReadIOPS|Client Lees IOPS|CountPerSeconde|Average|Client leesbewerkingen per seconde.|Geen|
|ClientRead-doorvoer|Gemiddelde cacheleesdoorvoer|BytesPerSeconde|Average|Client lees gegevensoverdracht snelheid.|Geen|
|ClientWriteIOPS|Client Schrijf IOPS|CountPerSeconde|Average|Client schrijfbewerkingen per seconde.|Geen|
|ClientWrite-doorvoer|Gemiddelde cacheschrijfdoorvoer|BytesPerSeconde|Average|Client schrijf gegevensoverdracht snelheid.|Geen|
|ClientMetadataReadIOPS|Clientmetagegevens lezen IOPS|CountPerSeconde|Average|De snelheid van clientbestandsbewerkingen die naar de cache worden verzonden, met uitzondering van gegevensreads, die de permanente status niet wijzigen.|Geen|
|ClientMetadataWriteIOPS|Clientmetagegevens schrijven IOPS|CountPerSeconde|Average|De snelheid van clientbestandsbewerkingen die naar de cache worden verzonden, met uitzondering van gegevensschrijfbewerkingen, die de permanente status wijzigen.|Geen|
|ClientlockiOPS|Client Lock IOPS|CountPerSeconde|Average|Clientbestandsvergrendeling per seconde.|Geen|
|StorageTargetHealth|Status van opslagdoel|Count|Average|Booleaanse resultaten van connectiviteitstest tussen de cache- en opslagdoelen.|Geen|
|Uptime|Uptime|Count|Average|Booleaanse resultaten van connectiviteitstest tussen de cache en het bewakingssysteem.|Geen|
|StorageTargetIOPS|IOPS voor totale opslagdoel|Count|Average|De snelheid van alle bestandsbewerkingen die de cache naar een bepaalde StorageTarget verzendt.|StorageTarget|
|StorageTargetWriteIOPS|StorageTarget Schrijven IOPS|Count|Average|De snelheid van de bestandsschrijfbewerkingen die de cache naar een bepaalde StorageTarget verzendt.|StorageTarget|
|StorageTargetAsyncWriteThroughput|StorageTarget Asynchrone schrijfdoorvoer|BytesPerSeconde|Average|De snelheid waarmee de cache asynchroon gegevens naar een bepaalde StorageTarget schrijft. Dit zijn opportunistische schrijft die niet leiden tot klanten te blokkeren.|StorageTarget|
|StorageTargetSyncWriteThroughput|StorageTarget synchrone schrijfdoorvoer|BytesPerSeconde|Average|De snelheid waarmee de cache synchroon gegevens naar een bepaalde StorageTarget schrijft. Dit zijn schrijft dat doen veroorzaken klanten te blokkeren.|StorageTarget|
|StorageTargetTotalWrite-doorvoer|Totale schrijfdoorvoer van StorageTarget|BytesPerSeconde|Average|De totale snelheid waarmee de cache gegevens naar een bepaalde StorageTarget schrijft.|StorageTarget|
|StorageTargetLatentie|Latentie van StorageTarget|Milliseconden|Average|De gemiddelde latentie rond reis van alle bestandsbewerkingen die de cache naar een partricular StorageTarget stuurt.|StorageTarget|
|StorageTargetMetadataReadIOPS|StorageTarget-metagegevens lezen IOPS|CountPerSeconde|Average|De snelheid van bestandsbewerkingen die de permanente status niet wijzigen en de leesbewerking uitsluiten, die de cache naar een bepaalde StorageTarget verzendt.|StorageTarget|
|StorageTargetMetadataWriteIOPS|StorageTarget-metagegevens schrijven IOPS|CountPerSeconde|Average|De snelheid van bestandsbewerkingen die de permanente status wijzigen en de schrijfbewerking uitsluiten, die de cache naar een bepaalde StorageTarget verzendt.|StorageTarget|
|StorageTargetReadIOPS|StorageTarget lees IOPS|CountPerSeconde|Average|De snelheid van bestandsleesbewerkingen die de cache naar een bepaalde StorageTarget verzendt.|StorageTarget|
|StorageTargetReadAheadDoorvoer|StorageTarget Lees vooruit doorvoer|BytesPerSeconde|Average|De snelheid waarmee de cache opportunistisch gegevens van de StorageTarget leest.|StorageTarget|
|StorageTargetFillThroughput|Opslagdoelvuldoorvoer|BytesPerSeconde|Average|De snelheid waarmee de cache gegevens van storagetarget leest om een cachemis te verwerken.|StorageTarget|
|StorageTargetTotalRead-doorvoer|Totale leesdoorvoer van StorageTarget|BytesPerSeconde|Average|De totale snelheid waarmee de cache gegevens van een bepaalde StorageTarget leest.|StorageTarget|

## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ServerSyncSessionResultaat|Sessieresultaat synchroniseren|Count|Average|Metric die een waarde van 1 registreert telkens wanneer het Servereindpunt een synchronisatiesessie met het Cloud-eindpunt voltooit|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionAppliedFilesCount|Bestanden gesynchroniseerd|Count|Totaal|Aantal gesynchroniseerde bestanden|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Bestanden die niet worden gesynchroniseerd|Count|Totaal|Aantal bestanden kan niet worden gesynchroniseerd|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncbatchtransferredFileBytes|Bytes gesynchroniseerd|Bytes|Totaal|Totale bestandsgrootte overgedragen voor Synchronisatiesessies|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncServerHeartbeat|Server onlinestatus|Count|Maximum|Metric die een waarde van 1 registreert elke keer dat de opnieuw gebonden server een heartbeat registreert met het Cloud Endpoint|ServerName|
|StorageSyncRecallIOTotalSizeBytes|Terugroepactie voor cloudtiering|Bytes|Totaal|Totale grootte van gegevens die door de server worden teruggeroepen|ServerName|
|StorageSyncRecalledTotalNetworkBytes|Cloud tiering recall grootte|Bytes|Totaal|Omvang van de aangeroepen gegevens|SyncGroupName, servernaam|
|StorageSyncRecallThroughputBytesPerSeconde|Cloud tiering recall throughput Cloud tiering recall throughput Cloud tiering recall throughput Cloud tier|BytesPerSeconde|Average|Omvang van de doorvoer van gegevensterugroepen|SyncGroupName, servernaam|
|StorageSyncRecalledNetworkBytesByApplication|Cloud tiering recall grootte per toepassing|Bytes|Totaal|Omvang van de gegevens die door toepassing worden teruggeroepen|SyncGroupName, ServerName, ApplicationName|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SyncGroupSyncSessionAppliedFilesCount|Bestanden gesynchroniseerd|Count|Totaal|Aantal gesynchroniseerde bestanden|SyncGroupName, ServerEndpointName, SyncDirection|
|Aantal syncgroupsync-peritemfouten|Bestanden die niet worden gesynchroniseerd|Count|Totaal|Aantal bestanden kan niet worden gesynchroniseerd|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupBatchTransferFileBytes|Bytes gesynchroniseerd|Bytes|Totaal|Totale bestandsgrootte overgedragen voor Synchronisatiesessies|SyncGroupName, ServerEndpointName, SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ServerEndpointSyncSessionAppliedFilesCount|Bestanden gesynchroniseerd|Count|Totaal|Aantal gesynchroniseerde bestanden|ServerEndpointName, SyncDirection|
|Aantal serverendpointsync-peritemfouten|Bestanden die niet worden gesynchroniseerd|Count|Totaal|Aantal bestanden kan niet worden gesynchroniseerd|ServerEndpointName, SyncDirection|
|ServerEndpointBatchTransferFileBytes|Bytes gesynchroniseerd|Bytes|Totaal|Totale bestandsgrootte overgedragen voor Synchronisatiesessies|ServerEndpointName, SyncDirection|

## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|ServerHeartbeat ServerHeartbeat|Server onlinestatus|Count|Maximum|Metric die een waarde van 1 registreert elke keer dat de opnieuw gebonden server een heartbeat registreert met het Cloud Endpoint|ServerResourceid, servernaam|
|ServerRecallIOTotalSizeBytes|Terugroepactie voor cloudtiering|Bytes|Totaal|Totale grootte van gegevens die door de server worden teruggeroepen|ServerResourceid, servernaam|



## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Resourcegebruik|SU % Gebruik|Percentage|Maximum|SU % Gebruik|Logische naam, partitionid|
|Invoergebeurtenissen|Invoergebeurtenissen|Count|Totaal|Invoergebeurtenissen|Logische naam, partitionid|
|InputEventBytes|Gebeurtenisbytes voor invoer|Bytes|Totaal|Gebeurtenisbytes voor invoer|Logische naam, partitionid|
|LateInputGebeurtenissen|Gebeurtenissen voor late invoer|Count|Totaal|Gebeurtenissen voor late invoer|Logische naam, partitionid|
|Uitvoergebeurtenissen|Uitvoergebeurtenissen|Count|Totaal|Uitvoergebeurtenissen|Logische naam, partitionid|
|Conversiefouten|Fouten in gegevensconversie|Count|Totaal|Fouten in gegevensconversie|Logische naam, partitionid|
|Fouten|Runtime-fouten|Count|Totaal|Runtime-fouten|Logische naam, partitionid|
|DroppedoradjustedEvents|Niet op volgorde evenementen|Count|Totaal|Niet op volgorde evenementen|Logische naam, partitionid|
|AMLCalloutRequests|Functieaanvragen|Count|Totaal|Functieaanvragen|Logische naam, partitionid|
|AMLCalloutFailedRequests|Mislukte functieaanvragen|Count|Totaal|Mislukte functieaanvragen|Logische naam, partitionid|
|AMLCalloutInputEvents|Functiegebeurtenissen|Count|Totaal|Functiegebeurtenissen|Logische naam, partitionid|
|Fout bij deserialisatie|Fouten in de deserialisatie van invoer|Count|Totaal|Fouten in de deserialisatie van invoer|Logische naam, partitionid|
|EarlyInputEvents|Gebeurtenissen voor vroege invoer|Count|Totaal|Gebeurtenissen voor vroege invoer|Logische naam, partitionid|
|UitvoerWatermarkDelaySeconden|Watermerk vertraging|Seconden|Maximum|Watermerk vertraging|Logische naam, partitionid|
|InputEventsSourcesBacklogged|Backlogged Invoergebeurtenissen|Count|Maximum|Backlogged Invoergebeurtenissen|Logische naam, partitionid|
|InputEventsSourcesPerSeconde|Ontvangen invoerbronnen|Count|Totaal|Ontvangen invoerbronnen|Logische naam, partitionid|

## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/werkruimten

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|OrchestrationPipelineRunsEnded OrchestrationPipelineRunsEnded OrchestrationPipelineRunsEnded Orchestration|Pijplijnruns beëindigd|Count|Totaal|Aantal orchestration pipeline runs die zijn geslaagd, mislukt of geannuleerd|Resultaat, FailureType, Pipeline|
|OrchestrationActivityRunsEnded OrchestrationActivityRunsEnded OrchestrationActivityRunsEnded Orchestration|Activiteitsruns beëindigd|Count|Totaal|Aantal orkestratieactiviteiten die zijn geslaagd, mislukt of geannuleerd|Resultaat, FailureType, Activiteit,ActivityType, Pijplijn|
|OrchestrationTriggersEnded|Triggers beëindigd|Count|Totaal|Aantal orchestration-triggers die zijn geslaagd, mislukt of geannuleerd|Resultaat, FailureType, Trigger|
|SQLOnDemandLoginAttempts|Inlogpogingen|Count|Totaal|Aantal inlogpogingen die zijn mislukt of mislukt|Resultaat|
|SQLOnDemandQueriesEnded|Query's beëindigd|Count|Totaal|Aantal query's dat is geslaagd, mislukt of is geannuleerd|Resultaat|
|SQLOnDemandQueryProcessedBytes|Gegevens verwerkt|Bytes|Totaal|Hoeveelheid gegevens die door query's worden verwerkt|Geen|

## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/workspaces/bigDataPools

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|SparkJobsEnded|Beëindigde toepassingen|Count|Totaal|Aantal beëindigde aanvragen|JobType, JobResult|
|CoresCapaciteit|Capaciteit van kernen|Count|Maximum|Capaciteit van kernen|Geen|
|GeheugencapaciteitGB|Geheugencapaciteit (GB)|Count|Maximum|Geheugencapaciteit (GB)|Geen|

## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/workspaces/sqlPools

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|DWULimit|DWU-limiet|Count|Maximum|Doelstelling op serviceniveau van de SQL-pool|Geen|
|DWUGebruikt|Gebruikte DWU|Count|Maximum|Vertegenwoordigt een weergave op hoog niveau van het gebruik in de SQL-groep. Gemeten naar DWU-limiet * DWU-percentage|Geen|
|DWUUsedProcent|DWU gebruikt percentage|Percentage|Maximum|Vertegenwoordigt een weergave op hoog niveau van het gebruik in de SQL-groep. Gemeten door het maximum te nemen tussen CPU-percentage en Data IO-percentage|Geen|
|ConnectionsBlockedByFirewall|Verbindingen geblokkeerd door firewall|Count|Totaal|Aantal verbindingen geblokkeerd door firewallregels. Opnieuw toegangsbeheerbeleid voor uw SQL-groep en deze verbindingen controleren als het aantal hoog is|Geen|
|AdaptiveCacheHitPercent|Percentage adaptieve cachehit|Percentage|Maximum|Meet hoe goed workloads zijn met behulp van de adaptieve cache. Gebruik deze statistiek met de statistiek cachehitpercentage om te bepalen of u moet schalen voor extra capaciteit of om workloads opnieuw uit te voeren om de cache te hydrateren|Geen|
|AdaptiveCacheUsedPercent|Gebruikt percentage adaptieve cache|Percentage|Maximum|Meet hoe goed workloads zijn met behulp van de adaptieve cache. Gebruik deze statistiek met de cache die percentagestatistiek wordt gebruikt om te bepalen of u moet schalen voor extra capaciteit of om workloads opnieuw uit te voeren om de cache te hydrateren|Geen|
|LocalTempDBUsedPercent|Lokaal tempdb gebruikt percentage|Percentage|Maximum|Lokaal tempdb-gebruik voor alle compute nodes - waarden worden elke vijf minuten uitgestoten|Geen|
|MemoryUsedPercent|Geheugengebruikt percentage|Percentage|Maximum|Geheugengebruik voor alle knooppunten in de SQL-groep|Geen|
|Verbindingen|Verbindingen|Count|Totaal|Aantal aanmeldingen van totaal in de SQL-groep|Resultaat|
|WLGActiveQueries|Actieve query's voor workloadgroep|Count|Totaal|De actieve query's binnen de werkbelastinggroep. Met deze ongefilterde en ongesplitste statistiek worden alle actieve query's weergegeven die op het systeem worden uitgevoerd|IsUserDefined,WorkloadGroup|
|WLGActiveQueriesTime-outs|Time-outs van werkgroepquery's|Count|Totaal|Query's voor de werkbelastinggroep die een time-out heeft. Query-time-outs die door deze statistiek worden gerapporteerd, zijn pas nadat de query is gestart met uitvoeren (er is geen wachttijd opgenomen vanwege vergrendeling of resourcewachttijden)|IsUserDefined,WorkloadGroup|
|WLGAllocationBySystemPercent|Toewijzing workloadgroep per systeempercentage|Percentage|Maximum|Het percentage toewijzing van middelen ten opzichte van het gehele systeem|IsUserDefined,WorkloadGroup|
|WLGAllocationByMaxResourceProcent|Toewijzing workloadgroep op basis van maximaal resourcepercentage|Percentage|Maximum|Hiermee geeft u het percentage toewijzing van resources weer ten opzichte van het percentage effectieve limietresources per werkbelastinggroep. Deze statistiek biedt het effectieve gebruik van de werkbelastinggroep|IsUserDefined,WorkloadGroup|
|WLGEffectiveCapResourcePercent|Percentage effectieve cap-resources|Percentage|Maximum|Het effectieve cap resource percentage voor de workloadgroep. Als er andere werkbelastinggroepen met min_percentage_resource > 0 zijn, wordt de effective_cap_percentage_resource proportioneel verlaagd|IsUserDefined,WorkloadGroup|
|wlg_effective_min_resource_percent|Effectief min resource percentage|Percentage|Minimum|De instelling voor het effectieve min-resourcepercentage is toegestaan, rekening houdend met het serviceniveau en de instellingen voor de werkbelastinggroep. De effectieve min_percentage_resource kan hoger worden aangepast op lagere serviceniveaus|IsUserDefined,WorkloadGroup|
|WLGQueuedQueries|Query's in de groep Werkbelasting in de wachtrij|Count|Totaal|Cumulatief aantal aanvragen in de wachtrij nadat de maximale gelijktijdigheidslimiet is bereikt|IsUserDefined,WorkloadGroup|

## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/omgevingen

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ontvangen berichten binnendringen|Count|Totaal|Count of messages read from all Event hub or IoT hub event sources|Geen|
|IngressReceivedInvalidMessages|Ongeldige berichten die niet worden ontvangen|Count|Totaal|Count of invalid messages read from all Event hub or IoT hub event sources|Geen|
|IngressReceivedBytes|Ontvangen bytes binnenvallen|Bytes|Totaal|Aantal gelezen bytes uit alle gebeurtenisbronnen|Geen|
|IngressStoredBytes|Opgeslagen bytes binnenlaten|Bytes|Totaal|Totale grootte van gebeurtenissen die met succes zijn verwerkt en beschikbaar zijn voor query|Geen|
|IngressStoredEvents|Opgeslagen gebeurtenissen binnendringen|Count|Totaal|Aantal afgevlakte gebeurtenissen die zijn verwerkt en beschikbaar zijn voor query|Geen|
|IngressReceivedMessagesTimeLag|Binnenvallen de ontvangen berichten vertraging|Seconden|Maximum|Verschil tussen het tijdstip waarop het bericht in de gebeurtenisbron is geplaatst en de tijd dat het wordt verwerkt in Ingress|Geen|
|IngressReceivedMessagesCountLag|Aantal meldingen van ontvangen berichten lag|Count|Average|Verschil tussen het volgordenummer van het laatst in de wachtrij staande bericht in de gebeurtenisbronpartitie en het volgordenummer van berichten die worden verwerkt in Ingress|Geen|
|WarmStorageMaxProperties WarmStorageMaxProperties|Eigenschappen voor warme opslag max.|Count|Maximum|Maximaal aantal eigenschappen die door de omgeving zijn toegestaan voor S1/S2 SKU en maximaal aantal eigenschappen die door Warm Store zijn toegestaan voor PAYG SKU|Geen|
|WarmStorageUsedEigenschappen|Gebruikte eigenschappen voor warme opslag |Count|Maximum|Aantal eigenschappen dat door de omgeving wordt gebruikt voor S1/S2 SKU en aantal eigenschappen die door Warm Store worden gebruikt voor PAYG SKU|Geen|



## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/omgevingen/eventsources

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|IngressReceivedMessages|Ontvangen berichten binnendringen|Count|Totaal|Aantal berichten dat is gelezen vanuit de gebeurtenisbron|Geen|
|IngressReceivedInvalidMessages|Ongeldige berichten die niet worden ontvangen|Count|Totaal|Aantal ongeldige berichten dat is gelezen vanuit de gebeurtenisbron|Geen|
|IngressReceivedBytes|Ontvangen bytes binnenvallen|Bytes|Totaal|Aantal gelezen bytes uit de gebeurtenisbron|Geen|
|IngressStoredBytes|Opgeslagen bytes binnenlaten|Bytes|Totaal|Totale grootte van gebeurtenissen die met succes zijn verwerkt en beschikbaar zijn voor query|Geen|
|IngressStoredEvents|Opgeslagen gebeurtenissen binnendringen|Count|Totaal|Aantal afgevlakte gebeurtenissen die zijn verwerkt en beschikbaar zijn voor query|Geen|
|IngressReceivedMessagesTimeLag|Binnenvallen de ontvangen berichten vertraging|Seconden|Maximum|Verschil tussen het tijdstip waarop het bericht in de gebeurtenisbron is geplaatst en de tijd dat het wordt verwerkt in Ingress|Geen|
|IngressReceivedMessagesCountLag|Aantal meldingen van ontvangen berichten lag|Count|Average|Verschil tussen het volgordenummer van het laatst in de wachtrij staande bericht in de gebeurtenisbronpartitie en het volgordenummer van berichten die worden verwerkt in Ingress|Geen|
|WarmStorageMaxProperties WarmStorageMaxProperties|Eigenschappen voor warme opslag max.|Count|Maximum|Maximaal aantal eigenschappen die door de omgeving zijn toegestaan voor S1/S2 SKU en maximaal aantal eigenschappen die door Warm Store zijn toegestaan voor PAYG SKU|Geen|
|WarmStorageUsedEigenschappen|Gebruikte eigenschappen voor warme opslag |Count|Maximum|Aantal eigenschappen dat door de omgeving wordt gebruikt voor S1/S2 SKU en aantal eigenschappen die door Warm Store worden gebruikt voor PAYG SKU|Geen|

## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|DiskReadBytesPerSeconde|Bytes/Seconde schijfgelezen|BytesPerSeconde|Average|Gemiddelde schijfdoorvoer als gevolg van leesbewerkingen gedurende de voorbeeldperiode.|Geen|
|DiskwritebytesPerSeconde|Bytes voor schijfschrijfbytes per seconde|BytesPerSeconde|Average|Gemiddelde schijfdoorvoer als gevolg van schrijfbewerkingen gedurende de voorbeeldperiode.|Geen|
|Bytes voor schijflezen|Bytes voor schijflezen|Bytes|Totaal|Totale schijfdoorvoer als gevolg van leesbewerkingen gedurende de voorbeeldperiode.|Geen|
|Schrijfbytes voor schijf|Schrijfbytes voor schijf|Bytes|Totaal|Totale schijfdoorvoer als gevolg van schrijfbewerkingen gedurende de voorbeeldperiode.|Geen|
|DiskReadOperations|Schijfleesbewerkingen|Count|Totaal|Het aantal IO-leesbewerkingen in de vorige voorbeeldperiode. Houd er rekening mee dat deze bewerkingen van variabele grootte kunnen zijn.|Geen|
|DiskWriteOperations|Schijfschrijfbewerkingen|Count|Totaal|Het aantal IO-schrijfbewerkingen in de vorige voorbeeldperiode. Houd er rekening mee dat deze bewerkingen van variabele grootte kunnen zijn.|Geen|
|Bewerkingen voor schijflezen/sec|Bewerkingen voor schijflezen/sec|CountPerSeconde|Average|Het gemiddelde aantal IO-leesbewerkingen in de vorige voorbeeldperiode. Houd er rekening mee dat deze bewerkingen van variabele grootte kunnen zijn.|Geen|
|Bewerkingen voor schijfschrijfbewerkingen/sec|Bewerkingen voor schijfschrijfbewerkingen/sec|CountPerSeconde|Average|Het gemiddelde aantal IO-schrijfbewerkingen in de vorige voorbeeldperiode. Houd er rekening mee dat deze bewerkingen van variabele grootte kunnen zijn.|Geen|
|DiskReadLatentie|Latentie voor schijflezen|Milliseconden|Average|Totale leeslatentie. De som van het apparaat en kernel read latencies.|Geen|
|DiskWriteLatentie|Latentie voor schijfschrijfschrijven|Milliseconden|Average|Totale schrijflatentie. De som van het apparaat en kernel schrijf latencies.|Geen|
|NetworkinbytesPerSeconde|Netwerk in bytes/sec|BytesPerSeconde|Average|Gemiddelde netwerkdoorvoer voor ontvangen verkeer.|Geen|
|NetworkOutBytesPerSeconde|Bytes/Seconde voor netwerkuit|BytesPerSeconde|Average|Gemiddelde netwerkdoorvoer voor verzonden verkeer.|Geen|
|Netwerk in|Netwerk in|Bytes|Totaal|Totale netwerkdoorvoer voor ontvangen verkeer.|Geen|
|Netwerk uit|Netwerk uit|Bytes|Totaal|Totale netwerkdoorvoer voor verzonden verkeer.|Geen|
|Geheugengebruikt|Geheugen gebruikt|Bytes|Average|De hoeveelheid machinegeheugen die door de VM wordt gebruikt.|Geen|
|Geheugenverleend|Geheugen verleend|Bytes|Average|De hoeveelheid geheugen die door de host aan de VM is toegekend. Geheugen wordt niet verleend aan de host totdat het een keer wordt aangeraakt en toegekend geheugen kan worden verwisseld of weggeballongeld als de VMkernel het geheugen nodig heeft.|Geen|
|MemoryActive (MemoryActive)|Geheugenactief|Bytes|Average|De hoeveelheid geheugen die de VM in het afgelopen kleine tijdsvenster heeft gebruikt. Dit is het "ware" aantal van hoeveel geheugen de VM momenteel nodig heeft. Extra, ongebruikt geheugen kan worden verwisseld of opballonnet zonder invloed op de prestaties van de gast.|Geen|
|Percentage CPU|Percentage CPU|Percentage|Average|Het CPU-gebruik. Deze waarde wordt gerapporteerd met 100% die alle processorkernen op het systeem vertegenwoordigt. Een 2-weg VM met 50% van een viercore-systeem gebruikt bijvoorbeeld volledig twee cores.|Geen|
|PercentageCpuReady|Percentage CPU-ready|Milliseconden|Totaal|Klaar tijd is de tijd besteden aan wachten tot CPU(s) beschikbaar komen in het afgelopen update-interval.|Geen|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CpuPercentage|CPU-percentage|Percentage|Average|CPU-percentage|Exemplaar|
|GeheugenPercentage|Geheugenpercentage|Percentage|Average|Geheugenpercentage|Exemplaar|
|DiskQueueLengte|Lengte van de schijfwachtrij|Count|Average|Lengte van de schijfwachtrij|Exemplaar|
|HttpQueuelength|Http-wachtrijlengte|Count|Average|Http-wachtrijlengte|Exemplaar|
|Ontvangen bytes|Gegevens in|Bytes|Totaal|Gegevens in|Exemplaar|
|BytesSent|Gegevens uit|Bytes|Totaal|Gegevens uit|Exemplaar|
|TcpSynsent|TCP Syn verzonden|Count|Average|TCP Syn verzonden|Exemplaar|
|TcpSynReceived|TCP Syn ontvangen|Count|Average|TCP Syn ontvangen|Exemplaar|
|TcpEstablished|TCP opgericht|Count|Average|TCP opgericht|Exemplaar|
|TcpFinWait1|TCP Fin Wacht 1|Count|Average|TCP Fin Wacht 1|Exemplaar|
|TcpFinWait2|TCP Fin Wacht 2|Count|Average|TCP Fin Wacht 2|Exemplaar|
|TcpClosing|TCP-afsluiting|Count|Average|TCP-afsluiting|Exemplaar|
|TcpCloseWait|TCP Sluiten Wachten|Count|Average|TCP Sluiten Wachten|Exemplaar|
|TcpLastAck|TCP Last Ack|Count|Average|TCP Last Ack|Exemplaar|
|TcpTimeWait|TCP-tijd wachten|Count|Average|TCP-tijd wachten|Exemplaar|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (met uitzondering van functies) 

> [!NOTE]
> **Bestandssysteemgebruik** is een nieuwe statistiek die wereldwijd wordt uitgerold, er worden geen gegevens verwacht, tenzij u op de witte lijst staat voor een privévoorbeeld.

> [!IMPORTANT]
> **De gemiddelde responstijd** wordt afgeschaft om verwarring met metrische aggregaties te voorkomen. Gebruik **de responstijd** als vervanging.

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CpuTime (CpuTime)|CPU-tijd|Seconden|Totaal|CPU-tijd|Exemplaar|
|Aanvragen|Aanvragen|Count|Totaal|Aanvragen|Exemplaar|
|Ontvangen bytes|Gegevens in|Bytes|Totaal|Gegevens in|Exemplaar|
|BytesSent|Gegevens uit|Bytes|Totaal|Gegevens uit|Exemplaar|
|Http101|Http 101|Count|Totaal|Http 101|Exemplaar|
|Http2xx (Http2xx)|Http 2xx|Count|Totaal|Http 2xx|Exemplaar|
|Http3xx (Http3xx)|Http 3xx|Count|Totaal|Http 3xx|Exemplaar|
|Http401|Http 401|Count|Totaal|Http 401|Exemplaar|
|Http403|Http 403|Count|Totaal|Http 403|Exemplaar|
|Http404|Http 404|Count|Totaal|Http 404|Exemplaar|
|Http406|Http 406|Count|Totaal|Http 406|Exemplaar|
|Http4xx (Http4xx)|Http 4xx|Count|Totaal|Http 4xx|Exemplaar|
|Http5xx (Http5xx)|Http-serverfouten|Count|Totaal|Http-serverfouten|Exemplaar|
|MemoryWorkingSet (MemoryWorkingSet)|Geheugenwerkset|Bytes|Average|Geheugenwerkset|Exemplaar|
|Gemiddelde MemoryWorkingSet|Gemiddelde geheugenwerkset|Bytes|Average|Gemiddelde geheugenwerkset|Exemplaar|
|Reactietijd|Reactietijd|Seconden|Totaal|Reactietijd|Exemplaar|
|Gemiddelde responstijd|Gemiddelde responstijd (afgeschaft)|Seconden|Average|Gemiddelde responstijd|Exemplaar|
|AppVerbindingen|Verbindingen|Count|Average|Verbindingen|Exemplaar|
|Behandelt|Aantal ingangen|Count|Average|Aantal ingangen|Exemplaar|
|Threads|Aantal draad|Count|Average|Aantal draad|Exemplaar|
|PrivateBytes|Privébytes|Bytes|Average|Privébytes|Exemplaar|
|IoReadBytesPerSeconde|IO Leesbytes per seconde|BytesPerSeconde|Totaal|IO Leesbytes per seconde|Exemplaar|
|IoWriteBytesPerSeconde|IO-schrijfbytes per seconde|BytesPerSeconde|Totaal|IO-schrijfbytes per seconde|Exemplaar|
|IootherbytesPerSeconde|IO Andere bytes per seconde|BytesPerSeconde|Totaal|IO Andere bytes per seconde|Exemplaar|
|IoReadOperationsPerSeconde|IO-leesbewerkingen per seconde|BytesPerSeconde|Totaal|IO-leesbewerkingen per seconde|Exemplaar|
|IoWriteOperationsPerSeconde|IO-schrijfbewerkingen per seconde|BytesPerSeconde|Totaal|IO-schrijfbewerkingen per seconde|Exemplaar|
|IootherOperationsperSeconde|IO Andere bewerkingen per seconde|BytesPerSeconde|Totaal|IO Andere bewerkingen per seconde|Exemplaar|
|RequestsinApplicationQueue|Aanvragen in toepassingswachtrij|Count|Average|Aanvragen in toepassingswachtrij|Exemplaar|
|Huidige vergaderingen|Huidige vergaderingen|Count|Average|Huidige vergaderingen|Exemplaar|
|TotalAppDomains TotalAppDomains|Totaal aantal app-domeinen|Count|Average|Totaal aantal app-domeinen|Exemplaar|
|TotalAppDomainsUnloaded TotalAppDomainsUnloaded TotalAppDomainsUnloaded TotalApp|Totaal aantal app-domeinen gelost|Count|Average|Totaal aantal app-domeinen gelost|Exemplaar|
|Gen0Collecties|Gen 0 Garbage Collections|Count|Totaal|Gen 0 Garbage Collections|Exemplaar|
|Gen1Collecties|Gen 1 Garbage Collections|Count|Totaal|Gen 1 Garbage Collections|Exemplaar|
|Gen2Collecties|Gen 2 Garbage Collections|Count|Totaal|Gen 2 Garbage Collections|Exemplaar|
|Status van HealthCheck|Status van status van status van status van status|Count|Average|Status van status van status van status van status|Exemplaar|
|FileSystemUsage FileSystemUsage|Gebruik van bestandssysteem|Bytes|Average|Gebruik van bestandssysteem|Geen|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (functies)

> [!NOTE]
> **Bestandssysteemgebruik** is een nieuwe statistiek die wereldwijd wordt uitgerold, er worden geen gegevens verwacht, tenzij u op de witte lijst staat voor een privévoorbeeld.

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Ontvangen bytes|Gegevens in|Bytes|Totaal|Gegevens in|Exemplaar|
|BytesSent|Gegevens uit|Bytes|Totaal|Gegevens uit|Exemplaar|
|Http5xx (Http5xx)|Http-serverfouten|Count|Totaal|Http-serverfouten|Exemplaar|
|MemoryWorkingSet (MemoryWorkingSet)|Geheugenwerkset|Bytes|Average|Geheugenwerkset|Exemplaar|
|Gemiddelde MemoryWorkingSet|Gemiddelde geheugenwerkset|Bytes|Average|Gemiddelde geheugenwerkset|Exemplaar|
|FunctionExecutionUnits|Functieuitvoeringseenheden|MB / Milliseconden|Totaal|[Functieuitvoeringseenheden](https://github.com/Azure/Azure-Functions/wiki/Consumption-Plan-Cost-Billing-FAQ#how-can-i-view-graphs-of-execution-count-and-gb-seconds)|Exemplaar|
|Aantal functies|Aantal functieuitvoeringen|Count|Totaal|Aantal functieuitvoeringen|Exemplaar|
|PrivateBytes|Privébytes|Bytes|Average|Privébytes|Exemplaar|
|IoReadBytesPerSeconde|IO Leesbytes per seconde|BytesPerSeconde|Totaal|IO Leesbytes per seconde|Exemplaar|
|IoWriteBytesPerSeconde|IO-schrijfbytes per seconde|BytesPerSeconde|Totaal|IO-schrijfbytes per seconde|Exemplaar|
|IootherbytesPerSeconde|IO Andere bytes per seconde|BytesPerSeconde|Totaal|IO Andere bytes per seconde|Exemplaar|
|IoReadOperationsPerSeconde|IO-leesbewerkingen per seconde|BytesPerSeconde|Totaal|IO-leesbewerkingen per seconde|Exemplaar|
|IoWriteOperationsPerSeconde|IO-schrijfbewerkingen per seconde|BytesPerSeconde|Totaal|IO-schrijfbewerkingen per seconde|Exemplaar|
|IootherOperationsperSeconde|IO Andere bewerkingen per seconde|BytesPerSeconde|Totaal|IO Andere bewerkingen per seconde|Exemplaar|
|RequestsinApplicationQueue|Aanvragen in toepassingswachtrij|Count|Average|Aanvragen in toepassingswachtrij|Exemplaar|
|Huidige vergaderingen|Huidige vergaderingen|Count|Average|Huidige vergaderingen|Exemplaar|
|TotalAppDomains TotalAppDomains|Totaal aantal app-domeinen|Count|Average|Totaal aantal app-domeinen|Exemplaar|
|TotalAppDomainsUnloaded TotalAppDomainsUnloaded TotalAppDomainsUnloaded TotalApp|Totaal aantal app-domeinen gelost|Count|Average|Totaal aantal app-domeinen gelost|Exemplaar|
|Gen0Collecties|Gen 0 Garbage Collections|Count|Totaal|Gen 0 Garbage Collections|Exemplaar|
|Gen1Collecties|Gen 1 Garbage Collections|Count|Totaal|Gen 1 Garbage Collections|Exemplaar|
|Gen2Collecties|Gen 2 Garbage Collections|Count|Totaal|Gen 2 Garbage Collections|Exemplaar|
|Status van HealthCheck|Status van status van status van status van status|Count|Average|Status van status van status van status van status|Exemplaar|
|FileSystemUsage FileSystemUsage|Gebruik van bestandssysteem|Bytes|Average|Gebruik van bestandssysteem|Geen|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|CpuTime (CpuTime)|CPU-tijd|Seconden|Totaal|CPU-tijd|Exemplaar|
|Aanvragen|Aanvragen|Count|Totaal|Aanvragen|Exemplaar|
|Ontvangen bytes|Gegevens in|Bytes|Totaal|Gegevens in|Exemplaar|
|BytesSent|Gegevens uit|Bytes|Totaal|Gegevens uit|Exemplaar|
|Http101|Http 101|Count|Totaal|Http 101|Exemplaar|
|Http2xx (Http2xx)|Http 2xx|Count|Totaal|Http 2xx|Exemplaar|
|Http3xx (Http3xx)|Http 3xx|Count|Totaal|Http 3xx|Exemplaar|
|Http401|Http 401|Count|Totaal|Http 401|Exemplaar|
|Http403|Http 403|Count|Totaal|Http 403|Exemplaar|
|Http404|Http 404|Count|Totaal|Http 404|Exemplaar|
|Http406|Http 406|Count|Totaal|Http 406|Exemplaar|
|Http4xx (Http4xx)|Http 4xx|Count|Totaal|Http 4xx|Exemplaar|
|Http5xx (Http5xx)|Http-serverfouten|Count|Totaal|Http-serverfouten|Exemplaar|
|MemoryWorkingSet (MemoryWorkingSet)|Geheugenwerkset|Bytes|Average|Geheugenwerkset|Exemplaar|
|Gemiddelde MemoryWorkingSet|Gemiddelde geheugenwerkset|Bytes|Average|Gemiddelde geheugenwerkset|Exemplaar|
|Gemiddelde responstijd|Gemiddelde responstijd|Seconden|Average|Gemiddelde responstijd|Exemplaar|
|HttpResponseTime|Reactietijd|Seconden|Average|Reactietijd|Exemplaar|
|FunctionExecutionUnits|Functieuitvoeringseenheden|Count|Totaal|Functieuitvoeringseenheden|Exemplaar|
|Aantal functies|Aantal functieuitvoeringen|Count|Totaal|Aantal functieuitvoeringen|Exemplaar|
|AppVerbindingen|Verbindingen|Count|Average|Verbindingen|Exemplaar|
|Behandelt|Aantal ingangen|Count|Average|Aantal ingangen|Exemplaar|
|Threads|Aantal draad|Count|Average|Aantal draad|Exemplaar|
|PrivateBytes|Privébytes|Bytes|Average|Privébytes|Exemplaar|
|IoReadBytesPerSeconde|IO Leesbytes per seconde|BytesPerSeconde|Totaal|IO Leesbytes per seconde|Exemplaar|
|IoWriteBytesPerSeconde|IO-schrijfbytes per seconde|BytesPerSeconde|Totaal|IO-schrijfbytes per seconde|Exemplaar|
|IootherbytesPerSeconde|IO Andere bytes per seconde|BytesPerSeconde|Totaal|IO Andere bytes per seconde|Exemplaar|
|IoReadOperationsPerSeconde|IO-leesbewerkingen per seconde|BytesPerSeconde|Totaal|IO-leesbewerkingen per seconde|Exemplaar|
|IoWriteOperationsPerSeconde|IO-schrijfbewerkingen per seconde|BytesPerSeconde|Totaal|IO-schrijfbewerkingen per seconde|Exemplaar|
|IootherOperationsperSeconde|IO Andere bewerkingen per seconde|BytesPerSeconde|Totaal|IO Andere bewerkingen per seconde|Exemplaar|
|RequestsinApplicationQueue|Aanvragen in toepassingswachtrij|Count|Average|Aanvragen in toepassingswachtrij|Exemplaar|
|Huidige vergaderingen|Huidige vergaderingen|Count|Average|Huidige vergaderingen|Exemplaar|
|TotalAppDomains TotalAppDomains|Totaal aantal app-domeinen|Count|Average|Totaal aantal app-domeinen|Exemplaar|
|TotalAppDomainsUnloaded TotalAppDomainsUnloaded TotalAppDomainsUnloaded TotalApp|Totaal aantal app-domeinen gelost|Count|Average|Totaal aantal app-domeinen gelost|Exemplaar|
|Gen0Collecties|Gen 0 Garbage Collections|Count|Totaal|Gen 0 Garbage Collections|Exemplaar|
|Gen1Collecties|Gen 1 Garbage Collections|Count|Totaal|Gen 1 Garbage Collections|Exemplaar|
|Gen2Collecties|Gen 2 Garbage Collections|Count|Totaal|Gen 2 Garbage Collections|Exemplaar|
|Status van HealthCheck|Status van status van status van status van status|Count|Average|Status van status van status van status van status|Exemplaar|
|FileSystemUsage FileSystemUsage|Gebruik van bestandssysteem|Bytes|Average|Gebruik van bestandssysteem|Geen|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingOmgevingen/multiRolePools

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|Aanvragen|Aanvragen|Count|Totaal|Aanvragen|Exemplaar|
|Ontvangen bytes|Gegevens in|Bytes|Totaal|Gegevens in|Exemplaar|
|BytesSent|Gegevens uit|Bytes|Totaal|Gegevens uit|Exemplaar|
|Http101|Http 101|Count|Totaal|Http 101|Exemplaar|
|Http2xx (Http2xx)|Http 2xx|Count|Totaal|Http 2xx|Exemplaar|
|Http3xx (Http3xx)|Http 3xx|Count|Totaal|Http 3xx|Exemplaar|
|Http401|Http 401|Count|Totaal|Http 401|Exemplaar|
|Http403|Http 403|Count|Totaal|Http 403|Exemplaar|
|Http404|Http 404|Count|Totaal|Http 404|Exemplaar|
|Http406|Http 406|Count|Totaal|Http 406|Exemplaar|
|Http4xx (Http4xx)|Http 4xx|Count|Totaal|Http 4xx|Exemplaar|
|Http5xx (Http5xx)|Http-serverfouten|Count|Totaal|Http-serverfouten|Exemplaar|
|Gemiddelde responstijd|Gemiddelde responstijd|Seconden|Average|Gemiddelde responstijd|Exemplaar|
|CpuPercentage|CPU-percentage|Percentage|Average|CPU-percentage|Exemplaar|
|GeheugenPercentage|Geheugenpercentage|Percentage|Average|Geheugenpercentage|Exemplaar|
|DiskQueueLengte|Lengte van de schijfwachtrij|Count|Average|Lengte van de schijfwachtrij|Exemplaar|
|HttpQueuelength|Http-wachtrijlengte|Count|Average|Http-wachtrijlengte|Exemplaar|
|ActiveRequests|Actieve aanvragen|Count|Totaal|Actieve aanvragen|Exemplaar|
|TotalFrontEnds TotalFrontEnds|Totaal aantal fronteinden|Count|Average|Totaal aantal fronteinden|Geen|
|SmallAppServicePlanInstances|Werknemers met een kleine app-serviceplan|Count|Average|Werknemers met een kleine app-serviceplan|Geen|
|MediumAppServicePlanInstances|Werknemers met een gemiddelde app-serviceplan|Count|Average|Werknemers met een gemiddelde app-serviceplan|Geen|
|LargeAppServicePlanInstances|Werknemers met een groot app-serviceplan|Count|Average|Werknemers met een groot app-serviceplan|Geen|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingOmgevingen/workerPools

|Gegevens|Metrische weergavenaam|Eenheid|Aggregatietype|Beschrijving|Dimensies|
|---|---|---|---|---|---|
|WerknemersTotaal|Totaal aantal werknemers|Count|Average|Totaal aantal werknemers|Geen|
|WerknemersBeschikbaar|Beschikbare werknemers|Count|Average|Beschikbare werknemers|Geen|
|WerknemersGebruikt|Gebruikte werknemers|Count|Average|Gebruikte werknemers|Geen|
|CpuPercentage|CPU-percentage|Percentage|Average|CPU-percentage|Exemplaar|
|GeheugenPercentage|Geheugenpercentage|Percentage|Average|Geheugenpercentage|Exemplaar|
## <a name="next-steps"></a>Volgende stappen
* [Lees meer over statistieken in Azure Monitor](data-platform.md)
* [Waarschuwingen maken op basis van metrische gegevens](alerts-overview.md)
* [Statistieken exporteren naar opslag, gebeurtenishub of logboekanalyse](platform-logs-overview.md)

