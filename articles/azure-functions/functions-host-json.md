---
title: host.json-referentie voor Azure-functies 2.x
description: Referentiedocumentatie voor het Azure Functions host.json-bestand met de v2-runtime.
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 7967cdc7f5f7cbb92c12de15d31471fda8aa6569
ms.sourcegitcommit: 31e9f369e5ff4dd4dda6cf05edf71046b33164d3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81758848"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>host.json-verwijzing voor Azure-functies 2.x en hoger 

> [!div class="op_single_selector" title1="Selecteer de versie van de runtime van Azure-functies die u gebruikt: "]
> * [Versie 1](functions-host-json-v1.md)
> * [Versie 2+](functions-host-json.md)

Het bestand *host.json* metagegevens bevat algemene configuratieopties die van invloed zijn op alle functies voor een functie-app. In dit artikel worden de instellingen weergegeven die beschikbaar zijn vanaf versie 2.x van de runtime van Azure-functies.  

> [!NOTE]
> Dit artikel is voor Azure Functions 2.x en latere versies.  Zie [host.json-verwijzing naar Azure-functies 1.x voor](functions-host-json-v1.md)een verwijzing naar host.json in Functies 1.x .

Andere configuratieopties voor functie-apps worden beheerd in uw [app-instellingen](functions-app-settings.md) (voor geïmplementeerde apps) of in het bestand [local.settings.json](functions-run-local.md#local-settings-file) (voor lokale ontwikkeling).

Configuraties in host.json met betrekking tot bindingen worden op elke functie in de functie-app ook toegepast. 

## <a name="sample-hostjson-file"></a>Voorbeeld van host.json-bestand

Het volgende *voorbeeldhost.json-bestand* voor versie 2.x+ heeft alle mogelijke opties opgegeven (met uitzondering van alle opties die alleen voor intern gebruik zijn).

```json
{
    "version": "2.0",
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "extensions": {
        "cosmosDb": {},
        "durableTask": {},
        "eventHubs": {},
        "http": {},
        "queues": {},
        "sendGrid": {},
        "serviceBus": {}
    },
    "extensionBundle": {
        "id": "Microsoft.Azure.Functions.ExtensionBundle",
        "version": "[1.*, 2.0.0)"
    },
    "functions": [ "QueueProcessor", "GitHubWebHook" ],
    "functionTimeout": "00:05:00",
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    },
    "logging": {
        "fileLoggingMode": "debugOnly",
        "logLevel": {
          "Function.MyFunction": "Information",
          "default": "None"
        },
        "applicationInsights": {
            "samplingSettings": {
              "isEnabled": true,
              "maxTelemetryItemsPerSecond" : 20,
              "evaluationInterval": "01:00:00",
              "initialSamplingPercentage": 100.0, 
              "samplingPercentageIncreaseTimeout" : "00:00:01",
              "samplingPercentageDecreaseTimeout" : "00:00:01",
              "minSamplingPercentage": 0.1,
              "maxSamplingPercentage": 100.0,
              "movingAverageRatio": 1.0,
              "excludedTypes" : "Dependency;Event",
              "includedTypes" : "PageView;Trace"
            },
            "enableLiveMetrics": true,
            "enableDependencyTracking": true,
            "enablePerformanceCountersCollection": true,            
            "httpAutoCollectionOptions": {
                "enableHttpTriggerExtendedInfoCollection": true,
                "enableW3CDistributedTracing": true,
                "enableResponseHeaderInjection": true
            },
            "snapshotConfiguration": {
                "agentEndpoint": null,
                "captureSnapshotMemoryWeight": 0.5,
                "failedRequestLimit": 3,
                "handleUntrackedExceptions": true,
                "isEnabled": true,
                "isEnabledInDeveloperMode": false,
                "isEnabledWhenProfiling": true,
                "isExceptionSnappointsEnabled": false,
                "isLowPrioritySnapshotUploader": true,
                "maximumCollectionPlanSize": 50,
                "maximumSnapshotsRequired": 3,
                "problemCounterResetInterval": "24:00:00",
                "provideAnonymousTelemetry": true,
                "reconnectInterval": "00:15:00",
                "shadowCopyFolder": null,
                "shareUploaderProcess": true,
                "snapshotInLowPriorityThread": true,
                "snapshotsPerDayLimit": 30,
                "snapshotsPerTenMinutesLimit": 1,
                "tempFolder": null,
                "thresholdForSnapshotting": 1,
                "uploaderProxy": null
            }
        }
    },
    "managedDependency": {
        "enabled": true
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ]
}
```

In de volgende secties van dit artikel wordt elke eigenschap op het hoogste niveau uitgelegd. Alle zijn optioneel, tenzij anders aangegeven.

## <a name="aggregator"></a>Aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

Deze instelling is een kind van [de houtkap](#logging).

Besturingselementen voor Toepassingsinzichten, inclusief [bemonsteringsopties.](./functions-monitoring.md#configure-sampling)

Zie het eerdere [voorbeeldhost.json-bestand](#sample-hostjson-file)voor de volledige JSON-structuur .

> [!NOTE]
> Logboekbemonstering kan ertoe leiden dat sommige uitvoeringen niet worden weergegeven in het monitorblad Van Application Insights. Om logboekbemonstering te `excludedTypes: "Request"` voorkomen, voegt u de `samplingSettings` waarde toe.

| Eigenschap | Standaard | Beschrijving |
| --------- | --------- | --------- | 
| samplingInstellingen | N.v.t. | Zie [applicationInsights.samplingSettings](#applicationinsightssamplingsettings). |
| enableLiveMetrics | waar | Maakt het verzamelen van live statistieken mogelijk. |
| inschakelenDependencyTracking | waar | Hiermee u afhankelijkheidstracking bijhouden. |
| performancecountersverzameling inschakelen | waar | Hiermee maakt kudu prestatietellers verzamelen. |
| liveMetricsInitializationDelay | 00:00:15 | Alleen voor intern gebruik. |
| httpAutoCollectionOptions | N.v.t. | Zie [applicationInsights.httpAutoCollectionOptions](#applicationinsightshttpautocollectionoptions). |
| momentopnameConfiguratie | N.v.t. | Zie [applicationInsights.snapshotConfiguration](#applicationinsightssnapshotconfiguration). |

### <a name="applicationinsightssamplingsettings"></a>applicationInsights.samplingInstellingen

|Eigenschap | Standaard | Beschrijving |
| --------- | --------- | --------- | 
| Isenabled | waar | Hiermee schakelt u sampling in of uit. | 
| maxTelemetryItemsPerSecond | 20 | Het doelaantal telemetrie-items dat per seconde wordt geregistreerd op elke serverhost. Als uw app op veel hosts wordt uitgevoerd, verlaagt u deze waarde om binnen uw totale doelsnelheid van het verkeer te blijven. | 
| evaluatieInterval | 01:00:00 | Het interval waarmee de huidige telemetriesnelheid opnieuw wordt geëvalueerd. Evaluatie wordt uitgevoerd als een voortschrijdend gemiddelde. U dit interval verkorten als uw telemetrie onderhevig is aan plotselinge uitbarstingen. |
| eersteSamplingPercentage| 1.0 | Het eerste bemonsteringspercentage dat aan het begin van het bemonsteringsproces wordt toegepast om het percentage dynamisch te variëren. Verminder de waarde niet tijdens het debuggen. |
| samplingPercentageIncreaseTimeout | 00:00:01 | Wanneer de waarde van het bemonsteringspercentage verandert, bepaalt deze eigenschap hoe snel daarna Application Insights het bemonsteringspercentage opnieuw mag verhogen om meer gegevens vast te leggen. |
| samplingPercentageDecreaseTimeout | 00:00:01 | Wanneer de waarde van het bemonsteringspercentage verandert, bepaalt deze eigenschap hoe snel daarna Application Insights het bemonsteringspercentage opnieuw mag verlagen om minder gegevens vast te leggen. |
| minSamplingPercentage | 0.1 | Aangezien het bemonsteringspercentage varieert, bepaalt deze eigenschap het minimaal toegestane bemonsteringspercentage. |
| maxSamplingPercentage | 0.1 | Aangezien het bemonsteringspercentage varieert, bepaalt deze eigenschap het maximaal toegestane bemonsteringspercentage. |
| movingAverageRatio | 1.0 | Bij de berekening van het voortschrijdend gemiddelde wordt het gewicht toegewezen aan de meest recente waarde. Gebruik een waarde die gelijk is aan of lager is dan 1. Kleinere waarden maken het algoritme minder reactief voor plotselinge veranderingen. |
| uitgeslotenTypen | null | Een lijst met typen met een dubbele punt die u niet wilt bemonsteren. Erkende `Dependency`typen zijn: `Event`, `PageView` `Request`, `Exception` `Trace`, , en . Alle exemplaren van de opgegeven typen worden verzonden; de typen die niet zijn opgegeven, worden bemonsterd. |
| includedTypes | null | Een lijst met typen met een dubbele punt die u wilt bemonsteren. een lege lijst impliceert alle soorten. Type dat `excludedTypes` wordt vermeld in de typen overschrijven die hier worden vermeld. Erkende `Dependency`typen zijn: `Event`, `PageView` `Request`, `Exception` `Trace`, , en . Exemplaren van de opgegeven typen worden bemonsterd; de typen die niet zijn opgegeven of geïmpliceerd, worden zonder bemonstering verzonden. |

### <a name="applicationinsightshttpautocollectionoptions"></a>applicationInsights.httpAutoCollectionOptions

|Eigenschap | Standaard | Beschrijving |
| --------- | --------- | --------- | 
| httpTriggerExtendedInfoCollection inschakelen | waar | Hiermee kunnen of worden uitgebreide HTTP-aanvraaggegevens voor HTTP-triggers in- of uitgeschakeld: inkomende foutteksten, ondersteuning voor meervoudige instrumentatiesleutels, HTTP-methode, pad en respons. |
| enableW3CDistributedTracing | waar | Hiermee schakelt u ondersteuning van het W3C distributed tracing-protocol in of uit (en schakelt het verouderde correlatieschema in). Standaard ingeschakeld als `enableHttpTriggerExtendedInfoCollection` dit waar is. Als `enableHttpTriggerExtendedInfoCollection` deze vlag niet waar is, is deze vlag alleen van toepassing op uitgaande aanvragen, niet op binnenkomende aanvragen. |
| inschakelenResponseHeaderInjection | waar | Hiermee of wordt de injectie van correlatiekoppen met meerdere componenten in reacties in- of uitgeschakeld. Met het inschakelen van injectie kan Application Insights een toepassingskaart samenstellen voor wanneer verschillende instrumentatietoetsen worden gebruikt. Standaard ingeschakeld als `enableHttpTriggerExtendedInfoCollection` dit waar is. Deze instelling is niet `enableHttpTriggerExtendedInfoCollection` van toepassing als deze niet is. |

### <a name="applicationinsightssnapshotconfiguration"></a>applicationInsights.snapshotConfiguration

Zie [Momentopnamen voor foutopsporing in .NET-apps](/azure/azure-monitor/app/snapshot-debugger) en [Problemen oplossen waarmee Debugger van Snapshots van Application Insights of het bekijken van momentopnamen](/azure/azure-monitor/app/snapshot-debugger-troubleshoot)worden opgelost voor meer informatie over momentopnamen.

|Eigenschap | Standaard | Beschrijving |
| --------- | --------- | --------- | 
| agentEndpoint | null | Het eindpunt dat wordt gebruikt om verbinding te maken met de foutopsporingsservice Van Application Insights.The endpoint used to the Application Insights Snapshot Debugger service. Als null, wordt een standaardeindpunt gebruikt. |
| captureSnapshotMemoryWeight | 0.5 | Het gewicht dat wordt gegeven aan de huidige geheugengrootte bij het controleren of er voldoende geheugen is om een momentopname te maken. De verwachte waarde is een fractie groter dan 0 (0 < CaptureSnapshotMemoryWeight < 1). |
| failedRequestLimit | 3 | De limiet voor het aantal mislukte aanvragen om momentopnamen op te vragen voordat de telemetrieprocessor is uitgeschakeld.|
| handleUntrackedExceptions | waar | Hiermee of wordt het bijhouden van uitzonderingen die niet worden bijgehouden door telemetrie van Application Insights in- of uitgeschakeld. |
| Isenabled | waar | Het in- of uitschakelen van momentopnameverzameling | 
| isEnabledInDeveloperMode | false | Het in- of uitschakelen van momentopnameverzameling is ingeschakeld in de ontwikkelaarsmodus. |
| isEnabledWhenProfiling | waar | Hiermee u het maken van momentopnamen in- of uitschakelen, zelfs als de Application Insights Profiler een gedetailleerde profileringssessie verzamelt. |
| isExceptionSnappointsIngeschakeld | false | Hiermee of wordt het filteren van uitzonderingen uitgeschakeld. |
| isLowPrioritySnapshotUploader | waar | Hiermee bepaalt u of het snapshotuploaderproces onder de normale prioriteit moet worden uitgevoerd. |
| maximaalCollectionPlanSize | 50 | Het maximum aantal problemen dat we op elk moment kunnen volgen in een bereik van één tot 9999. |
| maximumSnapshotsVereist | 3 | Het maximum aantal momentopnamen dat voor één probleem wordt verzameld, in een bereik van één tot 999. Een probleem kan worden gezien als een individuele gooi verklaring in uw aanvraag. Zodra het aantal momentopnamen dat voor een probleem is verzameld deze waarde bereikt, worden er `problemCounterResetInterval`geen `thresholdForSnapshotting` momentopnamen meer verzameld voor dat probleem totdat probleemtellers zijn gereset (zie) en de limiet opnieuw wordt bereikt. |
| probleemCounterResetInterval | 24:00:00 | Hoe vaak de probleemtellers in een bereik van één minuut tot zeven dagen moeten worden gereset. Wanneer dit interval is bereikt, worden alle probleemtellingen op nul gezet. Bestaande problemen die de drempel waarde voor het maken van momentopnamen al `maximumSnapshotsRequired`hebben bereikt, maar het aantal momentopnamen nog niet hebben gegenereerd, blijven actief. |
| biedenAnoniemeTelemetry | waar | Hiermee bepaalt u of u anoniem gebruik en fouttelemetrie naar Microsoft wilt verzenden. Deze telemetrie kan worden gebruikt als u contact opneemt met Microsoft om problemen met de momentopnamefoutopsporing op te lossen. Het wordt ook gebruikt om gebruikspatronen te controleren. |
| reconnectInterval | 00:15:00 | Hoe vaak maken we opnieuw verbinding met het eindpunt van momentopnamefoutopsporing. Toegestane bereik is een minuut tot een dag. |
| shadowCopyFolder | null | Hiermee geeft u de map op die moet worden gebruikt voor schaduwkopiërende binaire bestanden. Als deze niet zijn ingesteld, worden de mappen die zijn opgegeven door de volgende omgevingsvariabelen in volgorde geprobeerd: Fabric_Folder_App_Temp, LOCALAPPDATA, APPDATA, TEMP. |
| shareUploaderProcess | waar | Als dit waar is, verzamelt en uploadt slechts één exemplaar van SnapshotUploader momentopnamen voor meerdere apps die de InstrumentationKey delen. Als deze is ingesteld op false, is de SnapshotUploader uniek voor elke tuple (ProcessName, InstrumentationKey). |
| snapshotInLowPriorityThread | waar | Hiermee bepaalt u of momentopnamen al dan niet moeten worden verwerkt in een thread met lage IO-prioriteit. Het maken van een momentopname is een snelle bewerking, maar om een momentopname te uploaden naar de Snapshot Debugger-service, moet deze eerst als een minidump naar de schijf worden geschreven. Dat gebeurt in het SnapshotUploader-proces. Als u deze waarde instelt op true, wordt io met een lage prioriteit gebruikt om de minidump te schrijven, die niet zal concurreren met uw toepassing voor resources. Het instellen van deze waarde op valse snelheden minidump creatie ten koste van het vertragen van uw toepassing. |
| momentopnamenPerDayLimit | 30 | Het maximum aantal momentopnamen dat in één dag (24 uur) is toegestaan. Deze limiet wordt ook afgedwongen aan de servicekant van Application Insights. Uploads zijn tarief beperkt tot 50 per dag per toepassing (dat wil zeggen, per instrumentatie sleutel). Deze waarde helpt voorkomen dat er extra momentopnamen worden gemaakt die uiteindelijk tijdens het uploaden worden afgewezen. Met een waarde van nul wordt de limiet volledig verwijderd, wat niet wordt aanbevolen. |
| momentopnamenPerTenMinutesLimit | 1 | Het maximum aantal momentopnamen dat in 10 minuten is toegestaan. Hoewel er geen bovengrens op deze waarde, wees voorzichtig verhogen op de productie workloads, omdat het van invloed kan zijn op de prestaties van uw toepassing. Het maken van een momentopname is snel, maar het maken van een minidump van de momentopname en het uploaden naar de Snapshot Debugger-service is een veel tragere bewerking die zal concurreren met uw toepassing voor resources (zowel CPU als I/O). |
| tempFolder | null | Hiermee geeft u de map op om minidumps en uploaderlogbestanden te schrijven. Als dit niet is ingesteld, wordt *%TEMP%\Dumps* gebruikt. |
| drempelwaardenForSnapshotting | 1 | Hoe vaak Application Insights een uitzondering moet zien voordat het om momentopnamen vraagt. |
| uploaderProxy | null | Overschrijft de proxyserver die wordt gebruikt in het snapshot-uploaderproces. Mogelijk moet u deze instelling gebruiken als uw toepassing verbinding maakt met internet via een proxyserver. Het momentopnamecollectorwordt uitgevoerd binnen het proces van uw toepassing en gebruikt dezelfde proxy-instellingen. De Momentopnameuploader wordt echter als een afzonderlijk proces uitgevoerd en het kan nodig zijn om de proxyserver handmatig te configureren. Als deze waarde null is, probeert Momentopnameverzamelaar het adres van de proxy automatisch op te sporen door System.Net.WebRequest.DefaultWebProxy te onderzoeken en de waarde door te geven aan de Momentopnameuploader. Als deze waarde niet null is, wordt autodetectie niet gebruikt en wordt de proxyserver die hier is opgegeven, gebruikt in de momentopnameuploader. |

## <a name="cosmosdb"></a>cosmosDb

Configuratie-instelling is te vinden in [Cosmos DB triggers en bindingen.](functions-bindings-cosmosdb-v2-output.md#host-json)

## <a name="durabletask"></a>duurzaamTask

Configuratie-instelling is te vinden in [bindingen voor duurzame functies.](durable/durable-functions-bindings.md#host-json)

## <a name="eventhub"></a>eventHub

Configuratie-instellingen zijn te vinden in [gebeurtenishubtriggers en -bindingen.](functions-bindings-event-hubs-trigger.md#host-json) 

## <a name="extensions"></a>Extensies

Eigenschap die een object retourneert dat alle bindingsspecifieke instellingen bevat, zoals [http](#http) en [eventHub.](#eventhub)

## <a name="extensionbundle"></a>extensieBundel 

Met extensiebundels u een compatibele set extensies voor het binden van functies toevoegen aan uw functie-app. Zie [Uitbreidingsbundels voor lokale ontwikkeling voor](functions-bindings-register.md#extension-bundles)meer informatie.

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>Functies

Een lijst met functies die de taakhost uitvoert. Een lege array betekent dat alle functies worden uitgevoerd. Alleen bedoeld voor gebruik bij [lokaal hardlopen.](functions-run-local.md) In functie-apps in Azure moet u in plaats daarvan de stappen volgen in [Hoe u functies in Azure-functies uitschakelt](disable-function.md) om specifieke functies uit te schakelen in plaats van deze instelling te gebruiken.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functieTime-out

Geeft de time-outduur voor alle functies aan. Het volgt de tijdspanne string formaat. In een serverloos verbruiksplan is het geldige bereik 1 seconde tot 10 minuten en de standaardwaarde 5 minuten.  

In het Premium-abonnement is het geldige bereik 1 seconde tot 60 minuten en de standaardwaarde 30 minuten.

In een Dedicated (App Service) plan is er geen algemene limiet en de standaardwaarde is 30 minuten. Een waarde `-1` van geeft onbegrensde uitvoering aan, maar het wordt aanbevolen om een vaste bovengrens te behouden.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Configuratie-instellingen voor [hoststatusmonitor](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

```
{
    "healthMonitor": {
        "enabled": true,
        "healthCheckInterval": "00:00:10",
        "healthCheckWindow": "00:02:00",
        "healthCheckThreshold": 6,
        "counterThreshold": 0.80
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|enabled|waar|Hiermee geeft u op of de functie is ingeschakeld. | 
|healthCheckInterval|10 seconden|Het tijdsinterval tussen de periodieke achtergrondstatuscontroles. | 
|healthCheckWindow|2 minuten|Een schuiftijdvenster dat wordt `healthCheckThreshold` gebruikt in combinatie met de instelling.| 
|healthCheckThreshold|6|Maximaal aantal keren dat de statuscontrole kan mislukken voordat een hostwordt gestart.| 
|tegenDrempel|0,80|De drempel waarop een prestatiemeter als ongezond wordt beschouwd.| 

## <a name="http"></a>http

Configuratie-instellingen zijn te vinden in [http triggers en bindingen.](functions-bindings-http-webhook-output.md#hostjson-settings)

## <a name="logging"></a>logboekregistratie

Hiermee bepaalt u het logginggedrag van de functie-app, inclusief Application Insights.

```json
"logging": {
    "fileLoggingMode": "debugOnly",
    "logLevel": {
      "Function.MyFunction": "Information",
      "default": "None"
    },
    "console": {
        ...
    },
    "applicationInsights": {
        ...
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------|
|fileLoggingMode|foutopsporingAlleen|Hiermee bepaalt u welk niveau van bestandslogboekregistratie is ingeschakeld.  Opties `never`zijn `always` `debugOnly`, , . |
|Loglevel|N.v.t.|Object dat de filtering van de logboekcategorie definieert voor functies in de app. Versies 2.x en later volgen de ASP.NET Core-indeling voor het filteren van logboekcategorieën. Met deze instelling u logboekregistratie filteren voor specifieke functies. Zie [Logboekfiltering](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) in de ASP.NET Core-documentatie voor meer informatie. |
|console|N.v.t.| De instelling voor het registreren van de [console.](#console) |
|applicationInsights|N.v.t.| De [instelling applicationInsights.](#applicationinsights) |

## <a name="console"></a>console

Deze instelling is een kind van [de houtkap](#logging). Het regelt de console logboekregistratie wanneer u niet in de foutopsporingsmodus staat.

```json
{
    "logging": {
    ...
        "console": {
          "isEnabled": "false"
        },
    ...
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|Isenabled|false|Hiermee schakelt u consolelogboekregistratie in of uit.| 

## <a name="manageddependency"></a>managedDependency

Beheerde afhankelijkheid is een functie die momenteel alleen wordt ondersteund met PowerShell-gebaseerde functies. Hiermee kunnen afhankelijkheden automatisch door de service worden beheerd. Wanneer `enabled` de eigenschap `true`is `requirements.psd1` ingesteld op , wordt het bestand verwerkt. Afhankelijkheden worden bijgewerkt wanneer er secundaire versies worden uitgebracht. Zie [Beheerafhankelijkheid](functions-reference-powershell.md#dependency-management) in het PowerShell-artikel voor meer informatie.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>Wachtrijen

Configuratie-instellingen zijn te vinden in [storage queue triggers en bindingen.](functions-bindings-storage-queue-output.md#host-json)  

## <a name="sendgrid"></a>sendGrid

Configuratie-instelling is te vinden in [SendGrid-triggers en bindingen.](functions-bindings-sendgrid.md#host-json)

## <a name="servicebus"></a>serviceBus

Configuratie-instelling is te vinden in [servicebustriggers en -bindingen.](functions-bindings-service-bus-output.md#host-json)

## <a name="singleton"></a>Singleton

Configuratie-instellingen voor singleton-vergrendelingsgedrag. Zie [GitHub-probleem over singleton-ondersteuning](https://github.com/Azure/azure-webjobs-sdk-script/issues/912)voor meer informatie.

```json
{
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|lockPeriode|00:00:15|De periode waarvoor functieniveauvergrendelingen worden genomen. De sloten automatisch vernieuwen.| 
|listenerLockPeriod|00:01:00|De periode waarvoor luisteraar vergrendelt, wordt genomen.| 
|listenerLockRecoveryPollingInterval|00:01:00|Het tijdsinterval dat wordt gebruikt voor het herstel van listenervergrendeling als een listenerslot niet kan worden verkregen bij het opstarten.| 
|lockAcquisitionTimeout|00:01:00|De maximale tijd van de runtime zal proberen om een slot te verwerven.| 
|lockAcquisitionPollingInterval|N.v.t.|Het interval tussen pogingen voor het verkrijgen van het slot.| 

## <a name="version"></a>versie

Deze waarde geeft de schemaversie van host.json aan. De versietekenreeks `"version": "2.0"` is vereist voor een functie-app die zich richt op de v2-runtime of een latere versie. Er zijn geen host.json schema wijzigingen tussen v2 en v3.

## <a name="watchdirectories"></a>horlogeDirectories

Een set [gedeelde codemappen](functions-reference-csharp.md#watched-directories) die moeten worden gecontroleerd op wijzigingen.  Zorgt ervoor dat wanneer de code in deze mappen wordt gewijzigd, de wijzigingen worden opgepikt door uw functies.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het bijwerken van het host.json-bestand](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Bekijk globale instellingen in omgevingsvariabelen](functions-app-settings.md)
