---
title: host. json-verwijzing voor Azure Functions 2. x
description: Referentie documentatie voor het Azure Functions host. JSON-bestand met v2 runtime.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/08/2018
ms.author: glenga
ms.openlocfilehash: 5a4bc05e0a0b0b6a2c1b859caea2aadc12b8e0e0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70096401"
---
# <a name="hostjson-reference-for-azure-functions-2x"></a>host. json-verwijzing voor Azure Functions 2. x  

> [!div class="op_single_selector" title1="Selecteer de versie van de Azure Functions runtime die u gebruikt: "]
> * [Versie 1:](functions-host-json-v1.md)
> * [Versie 2](functions-host-json.md)

Het meta gegevensbestand van de *host. json* bevat globale configuratie opties die van invloed zijn op alle functies voor een functie-app. In dit artikel vindt u de instellingen die beschikbaar zijn voor de v2-runtime.  

> [!NOTE]
> Dit artikel is voor Azure Functions 2. x.  Voor een verwijzing van host.json in functies 1.x, Zie [naslaginformatie over host.json voor Azure Functions 1.x](functions-host-json-v1.md).

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
              "maxTelemetryItemsPerSecond" : 5
            }
        }
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "watchDirectories": [ "Shared", "Test" ],
    "managedDependency": {
        "enabled": true
    }
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
          "maxTelemetryItemsPerSecond" : 5
        }
    }
}
```

> [!NOTE]
> Het vastleggen van logboeken kan ertoe leiden dat sommige uitvoeringen niet worden weer gegeven op de Blade Application Insights monitor.

|Eigenschap  |Standaard | Description |
|---------|---------|---------| 
|isEnabled|true|Hiermee worden steek proeven in-of uitgeschakeld.| 
|maxTelemetryItemsPerSecond|5|De drempel waarde waarmee steek proeven worden gestart.| 

## <a name="cosmosdb"></a>cosmosDb

De configuratie-instelling vindt u in [Cosmos DB triggers en bindingen](functions-bindings-cosmosdb-v2.md#host-json).

## <a name="durabletask"></a>durableTask

De configuratie-instelling kan worden gevonden in [bindingen voor Durable functions](durable/durable-functions-bindings.md#host-json).

## <a name="eventhub"></a>eventHub

U kunt configuratie-instellingen vinden in [Event hub-triggers en](functions-bindings-event-hubs.md#host-json)-bindingen. 

## <a name="extensions"></a>Extensions

Eigenschap die een object retourneert dat alle binding-specifieke instellingen bevat, zoals [http](#http) en [eventHub](#eventhub).

## <a name="functions"></a>functies

Een lijst met functies die de taak host uitvoert. Een lege matrix houdt in dat alle functies worden uitgevoerd. Alleen bedoeld voor gebruik bij [lokaal uitvoeren](functions-run-local.md). In functie-apps in azure moet u in plaats daarvan de stappen volgen in [het uitschakelen van functies in azure functions](disable-function.md) om specifieke functies uit te scha kelen in plaats van deze instelling te gebruiken.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Hiermee wordt de duur van de time-out voor alle functies aangegeven. Het volgt de teken reeks notatie time span. In een serverloze verbruiks abonnement is het geldige bereik van 1 seconde tot 10 minuten en de standaard waarde is 5 minuten.  
In een speciaal (App Service)-abonnement is er geen algemene limiet en de standaard waarde is afhankelijk van de runtime versie: 
+ Versie 1. x: de standaard waarde is *Null*, wat geen time-out aangeeft.   
+ Versie 2. x: de standaard waarde is 30 minuten. Een waarde van `-1` geeft aan dat de uitvoering is beperkt.

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

|Eigenschap  |Standaard | Description |
|---------|---------|---------| 
|enabled|true|Hiermee wordt aangegeven of de functie is ingeschakeld. | 
|healthCheckInterval|10 seconden|Het tijds interval tussen de periodieke status controles voor de achtergrond. | 
|healthCheckWindow|2 minuten|Een schuif tijd venster dat wordt gebruikt in combi natie `healthCheckThreshold` met de instelling.| 
|healthCheckThreshold|6|Maximum aantal keer dat de status controle kan mislukken voordat een host recyclen wordt gestart.| 
|counterThreshold|0,80|De drempel waarde waarbij een prestatie meter item wordt beschouwd als een slechte status.| 

## <a name="http"></a>http

Configuratie-instellingen vindt u in [http-triggers en-bindingen](functions-bindings-http-webhook.md).

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="logging"></a>Userenv

Hiermee bepaalt u het gedrag van logboek registratie van de functie-app, met inbegrip van Application Insights.

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

|Eigenschap  |Standaard | Description |
|---------|---------|---------|
|fileLoggingMode|debugOnly|Hiermee wordt gedefinieerd welk niveau van bestands logboek registratie is ingeschakeld.  Opties zijn `never`, `always`, `debugOnly`. |
|logLevel|N.v.t.|Object dat de logboek categorie filtering definieert voor functies in de app. Versie 2. x volgt de ASP.NET Core indeling voor het filteren van de logboek categorie. Hiermee kunt u logboek registratie voor specifieke functies filteren. Zie [logboek filtering](https://docs.microsoft.com/aspnet/core/fundamentals/logging/?view=aspnetcore-2.1#log-filtering) in de ASP.net core-documentatie voor meer informatie. |
|console|N.v.t.| De instelling voor de logboek registratie van de [console](#console) . |
|applicationInsights|N.v.t.| De instelling [applicationInsights](#applicationinsights) . |

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

|Eigenschap  |Standaard | Description |
|---------|---------|---------| 
|isEnabled|false|Hiermee wordt de logboek registratie van de console in-of uitgeschakeld.| 

## <a name="queues"></a>Bestel

Configuratie-instellingen vindt u in de [opslag wachtrij Triggers en bindingen](functions-bindings-storage-queue.md#host-json).  

## <a name="sendgrid"></a>sendGrid

Configuratie-instelling vindt u in [SendGrid-triggers en](functions-bindings-sendgrid.md#host-json)-bindingen.

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

|Eigenschap  |Standaard | Description |
|---------|---------|---------| 
|lockPeriod|00:00:15|De periode waarin vergrendelingen op functie niveau worden uitgevoerd. De vergren delingen automatisch verlengen.| 
|listenerLockPeriod|00:01:00|De periode waarin de luister vergrendelingen worden uitgevoerd.| 
|listenerLockRecoveryPollingInterval|00:01:00|Het tijds interval dat wordt gebruikt voor het herstel van de listener-vergren deling als tijdens het opstarten geen listener-vergrendeling kan worden verkregen.| 
|lockAcquisitionTimeout|00:01:00|De maximale hoeveelheid tijd die de runtime probeert een vergren deling te verkrijgen.| 
|lockAcquisitionPollingInterval|N.v.t.|Het interval tussen overname pogingen voor vergren delen.| 

## <a name="version"></a>version

De versie teken `"version": "2.0"` reeks is vereist voor een functie-app die de v2-runtime bedoelt.

## <a name="watchdirectories"></a>watchDirectories

Een set [gedeelde code mappen](functions-reference-csharp.md#watched-directories) die moeten worden gecontroleerd op wijzigingen.  Zorgt ervoor dat wanneer de code in deze directory's wordt gewijzigd, de wijzigingen worden opgehaald door uw functies.

```json
{
    "watchDirectories": [ "Shared" ]
}
```

## <a name="manageddependency"></a>managedDependency

Managed dependency is een preview-functie die momenteel alleen wordt ondersteund met Power shell-functies. Hierdoor kunnen afhankelijkheden automatisch worden beheerd door de service. Wanneer de ingeschakelde eigenschap is ingesteld op True, wordt het bestand [Requirements. psd1](functions-reference-powershell.md#dependency-management) verwerkt. Afhankelijkheden worden bijgewerkt wanneer er secundaire versies worden vrijgegeven.

```json
{
    "managedDependency": {
        "enabled": true
    }
}
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het bijwerken van het bestand host. json](functions-reference.md#fileupdate)

> [!div class="nextstepaction"]
> [Algemene instellingen in omgevings variabelen weer geven](functions-app-settings.md)
