---
title: host. json-verwijzing voor Azure Functions 2. x
description: Referentie documentatie voor het Azure Functions host. JSON-bestand met v2 runtime.
ms.topic: conceptual
ms.date: 09/08/2018
ms.openlocfilehash: 08d772fc9b2871262b449a017f8be59a344576b2
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975445"
---
# <a name="hostjson-reference-for-azure-functions-2x-and-later"></a>host. json-verwijzing voor Azure Functions 2. x en hoger 

> [!div class="op_single_selector" title1="Selecteer de versie van de Azure Functions runtime die u gebruikt: "]
> * [Versie 1](functions-host-json-v1.md)
> * [Versie 2](functions-host-json.md)

Het meta gegevensbestand van de *host. json* bevat globale configuratie opties die van invloed zijn op alle functies voor een functie-app. In dit artikel vindt u een lijst met de instellingen die vanaf versie 2. x van de Azure Functions runtime beschikbaar zijn.  

> [!NOTE]
> Dit artikel is voor Azure Functions 2. x en latere versies.  Voor een verwijzing van host.json in functies 1.x, Zie [naslaginformatie over host.json voor Azure Functions 1.x](functions-host-json-v1.md).

Andere opties voor de configuratie van de functie-app worden beheerd in de [app-instellingen](functions-app-settings.md).

Sommige host. json-instellingen worden alleen gebruikt wanneer lokaal wordt uitgevoerd in het bestand [Local. settings. json](functions-run-local.md#local-settings-file) .

## <a name="sample-hostjson-file"></a>Voor beeld van host. JSON-bestand

Voor de volgende voor beeld- *JSON* -bestanden zijn alle mogelijke opties opgegeven.

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
              "maxTelemetryItemsPerSecond" : 20
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

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

Deze instelling is een onderliggend item van [logboek registratie](#logging).

Hiermee bepaalt u de [sampling functie in Application Insights](./functions-monitoring.md#configure-sampling).

```json
{
    "applicationInsights": {
        "samplingSettings": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 20
        }
    }
}
```

> [!NOTE]
> Het vastleggen van logboeken kan ertoe leiden dat sommige uitvoeringen niet worden weer gegeven op de Blade Application Insights monitor.

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|isEnabled|waar|Hiermee worden steek proeven in-of uitgeschakeld.| 
|maxTelemetryItemsPerSecond|20|De drempel waarde waarmee steek proeven worden gestart.| 
|EnableLiveMetrics |waar|Hiermee wordt de verzameling Live Metrics ingeschakeld.|
|EnableDependencyTracking|waar|Hiermee schakelt u het bijhouden van afhankelijkheden in.|
|EnablePerformanceCountersCollection|waar|Hiermee schakelt u de verzameling kudu-prestatie meter items.|

## <a name="cosmosdb"></a>cosmosDb

De configuratie-instelling vindt u in [Cosmos DB triggers en bindingen](functions-bindings-cosmosdb-v2.md#host-json).

## <a name="durabletask"></a>durableTask

De configuratie-instelling kan worden gevonden in [bindingen voor Durable functions](durable/durable-functions-bindings.md#host-json).

## <a name="eventhub"></a>eventHub

U kunt configuratie-instellingen vinden in [Event hub-triggers en-bindingen](functions-bindings-event-hubs.md#host-json). 

## <a name="extensions"></a>Extensions

Eigenschap die een object retourneert dat alle binding-specifieke instellingen bevat, zoals [http](#http) en [eventHub](#eventhub).

## <a name="extensionbundle"></a>extensionBundle 

Met uitbreidings bundels kunt u een compatibele set van functies bindings extensies toevoegen aan uw functie-app. Zie [uitbreidings bundels voor lokale ontwikkeling voor](functions-bindings-register.md#extension-bundles)meer informatie.

[!INCLUDE [functions-extension-bundles-json](../../includes/functions-extension-bundles-json.md)]

## <a name="functions"></a>functions

Een lijst met functies die de taak host uitvoert. Een lege matrix houdt in dat alle functies worden uitgevoerd. Alleen bedoeld voor gebruik bij [lokaal uitvoeren](functions-run-local.md). In functie-apps in azure moet u in plaats daarvan de stappen volgen in [het uitschakelen van functies in azure functions](disable-function.md) om specifieke functies uit te scha kelen in plaats van deze instelling te gebruiken.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Hiermee wordt de duur van de time-out voor alle functies aangegeven. Het volgt de teken reeks notatie time span. In een serverloze verbruiks abonnement is het geldige bereik van 1 seconde tot 10 minuten en de standaard waarde is 5 minuten.  

In het Premium-abonnement ligt het geldige bereik van 1 seconde tot 60 minuten en is de standaard waarde 30 minuten.

In een speciaal (App Service)-abonnement is er geen algemene limiet, en de standaard waarde is 30 minuten. Een waarde van `-1` geeft een onbegrensde uitvoering aan, maar het behouden van een vaste bovengrens wordt aanbevolen.

```json
{
    "functionTimeout": "00:05:00"
}
```

## <a name="healthmonitor"></a>healthMonitor

Configuratie-instellingen voor de [host Health Monitor](https://github.com/Azure/azure-webjobs-sdk-script/wiki/Host-Health-Monitor).

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
|enabled|waar|Hiermee wordt aangegeven of de functie is ingeschakeld. | 
|healthCheckInterval|10 seconden|Het tijds interval tussen de periodieke status controles voor de achtergrond. | 
|healthCheckWindow|2 minuten|Een schuif tijd venster dat wordt gebruikt in combi natie met de instelling `healthCheckThreshold`.| 
|healthCheckThreshold|6|Maximum aantal keer dat de status controle kan mislukken voordat een host recyclen wordt gestart.| 
|counterThreshold|0,80|De drempel waarde waarbij een prestatie meter item wordt beschouwd als een slechte status.| 

## <a name="http"></a>http

Configuratie-instellingen vindt u in [http-triggers en-bindingen](functions-bindings-http-webhook.md#hostjson-settings).

## <a name="logging"></a>logboekregistratie

Hiermee bepaalt u het gedrag van logboek registratie van de functie-app, met inbegrip van Application Insights.

```json
"logging": {
    "fileLoggingMode": "debugOnly"
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
|fileLoggingMode|debugOnly|Hiermee wordt gedefinieerd welk niveau van bestands logboek registratie is ingeschakeld.  Opties zijn `never`, `always``debugOnly`. |
|logLevel|n.v.t.|Object dat de logboek categorie filtering definieert voor functies in de app. Versie 2. x en hoger volgen de ASP.NET Core indeling voor het filteren van de logboek categorie. Hiermee kunt u logboek registratie voor specifieke functies filteren. Zie [logboek filtering](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) in de ASP.net core-documentatie voor meer informatie. |
|console|n.v.t.| De instelling voor de logboek registratie van de [console](#console) . |
|applicationInsights|n.v.t.| De instelling [applicationInsights](#applicationinsights) . |

## <a name="console"></a>console

Deze instelling is een onderliggend item van [logboek registratie](#logging). Het beheert de logboek registratie van de console als dit niet in de foutopsporingsmodus is.

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
|isEnabled|onwaar|Hiermee wordt de logboek registratie van de console in-of uitgeschakeld.| 

## <a name="manageddependency"></a>managedDependency

Managed dependency is een functie die momenteel alleen wordt ondersteund met Power shell-functies. Hierdoor kunnen afhankelijkheden automatisch worden beheerd door de service. Wanneer de eigenschap `enabled` is ingesteld op `true`, wordt het `requirements.psd1` bestand verwerkt. Afhankelijkheden worden bijgewerkt wanneer er secundaire versies worden vrijgegeven. Zie [beheerde afhankelijkheden](functions-reference-powershell.md#dependency-management) in het Power shell-artikel voor meer informatie.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="queues"></a>Bestel

Configuratie-instellingen vindt u in de [opslag wachtrij Triggers en bindingen](functions-bindings-storage-queue.md#host-json).  

## <a name="sendgrid"></a>sendGrid

Configuratie-instelling vindt u in [SendGrid-triggers en-bindingen](functions-bindings-sendgrid.md#host-json).

## <a name="servicebus"></a>serviceBus

De configuratie-instelling vindt u in [Service Bus triggers en bindingen](functions-bindings-service-bus.md#host-json).

## <a name="singleton"></a>Singleton

Configuratie-instellingen voor het gedrag van Singleton-vergren deling. Zie [github-probleem over Singleton-ondersteuning](https://github.com/Azure/azure-webjobs-sdk-script/issues/912)voor meer informatie.

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
|lockPeriod|00:00:15|De periode waarin vergrendelingen op functie niveau worden uitgevoerd. De vergren delingen automatisch verlengen.| 
|listenerLockPeriod|00:01:00|De periode waarin de luister vergrendelingen worden uitgevoerd.| 
|listenerLockRecoveryPollingInterval|00:01:00|Het tijds interval dat wordt gebruikt voor het herstel van de listener-vergren deling als tijdens het opstarten geen listener-vergrendeling kan worden verkregen.| 
|lockAcquisitionTimeout|00:01:00|De maximale hoeveelheid tijd die de runtime probeert een vergren deling te verkrijgen.| 
|lockAcquisitionPollingInterval|n.v.t.|Het interval tussen overname pogingen voor vergren delen.| 

## <a name="version"></a>versie

De versie teken reeks `"version": "2.0"` is vereist voor een functie-app die de v2-runtime bedoelt.

## <a name="watchdirectories"></a>watchDirectories

Een set [gedeelde code mappen](functions-reference-csharp.md#watched-directories) die moeten worden gecontroleerd op wijzigingen.  Zorgt ervoor dat wanneer de code in deze directory's wordt gewijzigd, de wijzigingen worden opgehaald door uw functies.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het bijwerken van het bestand host. json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Algemene instellingen in omgevings variabelen weer geven](functions-app-settings.md)
