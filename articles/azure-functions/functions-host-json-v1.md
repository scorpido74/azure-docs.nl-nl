---
title: host. json-verwijzing voor Azure Functions 1. x
description: Referentie documentatie voor het Azure Functions host. JSON-bestand met de V1-runtime.
author: ggailey777
manager: gwallace
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/19/2018
ms.author: glenga
ms.openlocfilehash: 3d6a28c8cdcf13dc805d70832ed65732911138cd
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263348"
---
# <a name="hostjson-reference-for-azure-functions-1x"></a>host. json-verwijzing voor Azure Functions 1. x

> [!div class="op_single_selector" title1="Selecteer de versie van de Azure Functions runtime die u gebruikt: "]
> * [Versie 1](functions-host-json-v1.md)
> * [Versie 2](functions-host-json.md)

Het meta gegevensbestand van de *host. json* bevat globale configuratie opties die van invloed zijn op alle functies voor een functie-app. In dit artikel vindt u de instellingen die beschikbaar zijn voor de V1-runtime. Het JSON-schema is http://json.schemastore.org/host.

> [!NOTE]
> Dit artikel is voor Azure Functions 1. x.  Zie [host. json Reference voor Azure functions 2. x](functions-host-json.md)voor een verwijzing naar de host. json in functions 2. x.

Andere opties voor de configuratie van de functie-app worden beheerd in de [app-instellingen](functions-app-settings.md).

Sommige host. json-instellingen worden alleen gebruikt wanneer lokaal wordt uitgevoerd in het bestand [Local. settings. json](functions-run-local.md#local-settings-file) .

## <a name="sample-hostjson-file"></a>Voor beeld van host. JSON-bestand

Voor de volgende voor beeld- *JSON* -bestanden zijn alle mogelijke opties opgegeven.


```json
{
    "aggregator": {
        "batchSize": 1000,
        "flushTimeout": "00:00:30"
    },
    "applicationInsights": {
        "sampling": {
          "isEnabled": true,
          "maxTelemetryItemsPerSecond" : 5
        }
    },
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix"
        }
    },
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
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
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 20,
        "maxConcurrentRequests": 10,
        "dynamicThrottlesEnabled": false
    },
    "id": "9f4ea53c5136457d883d685e57164f08",
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    },
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    },
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    },
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    },
    "singleton": {
      "lockPeriod": "00:00:15",
      "listenerLockPeriod": "00:01:00",
      "listenerLockRecoveryPollingInterval": "00:01:00",
      "lockAcquisitionTimeout": "00:01:00",
      "lockAcquisitionPollingInterval": "00:00:03"
    },
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    },
    "watchDirectories": [ "Shared" ],
}
```

In de volgende secties van dit artikel wordt elke eigenschap op het hoogste niveau uitgelegd. Alle zijn optioneel, tenzij anders aangegeven.

## <a name="aggregator"></a>aggregator

[!INCLUDE [aggregator](../../includes/functions-host-json-aggregator.md)]

## <a name="applicationinsights"></a>applicationInsights

[!INCLUDE [applicationInsights](../../includes/functions-host-json-applicationinsights.md)]

## <a name="documentdb"></a>DocumentDB

Configuratie-instellingen voor de [Azure Cosmos DB trigger en bindingen](functions-bindings-cosmosdb.md).

```json
{
    "documentDB": {
        "connectionMode": "Gateway",
        "protocol": "Https",
        "leaseOptions": {
            "leasePrefix": "prefix1"
        }
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------|
|GatewayMode|Gateway|De verbindings modus die wordt gebruikt door de functie bij het maken van verbinding met de Azure Cosmos DB-service. Opties zijn `Direct` en `Gateway`|
|Protocol|https|Het verbindings protocol dat door de functie wordt gebruikt bij het verbinden met de Azure Cosmos DB-service.  Lees [hier voor een uitleg van beide modi](../cosmos-db/performance-tips.md#networking)|
|leasePrefix|n.v.t.|Het lease voorvoegsel dat moet worden gebruikt voor alle functies in een app.|

## <a name="durabletask"></a>durableTask

[!INCLUDE [durabletask](../../includes/functions-host-json-durabletask.md)]

## <a name="eventhub"></a>eventHub

Configuratie-instellingen voor [Event hub-triggers en-bindingen](functions-bindings-event-hubs.md).

[!INCLUDE [functions-host-json-event-hubs](../../includes/functions-host-json-event-hubs.md)]

## <a name="functions"></a>functions

Een lijst met functies die de taak host uitvoert. Een lege matrix houdt in dat alle functies worden uitgevoerd. Alleen bedoeld voor gebruik bij [lokaal uitvoeren](functions-run-local.md). In functie-apps in azure moet u in plaats daarvan de stappen volgen in [het uitschakelen van functies in azure functions](disable-function.md) om specifieke functies uit te scha kelen in plaats van deze instelling te gebruiken.

```json
{
    "functions": [ "QueueProcessor", "GitHubWebHook" ]
}
```

## <a name="functiontimeout"></a>functionTimeout

Hiermee wordt de duur van de time-out voor alle functies aangegeven. In een serverloze verbruiks abonnement is het geldige bereik van 1 seconde tot 10 minuten en de standaard waarde is 5 minuten. In een App Service plan geldt geen algemene limiet en de standaard waarde is afhankelijk van de runtime versie.

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
|ingeschakeld|waar|Hiermee wordt aangegeven of de functie is ingeschakeld. | 
|healthCheckInterval|10 seconden|Het tijds interval tussen de periodieke status controles voor de achtergrond. | 
|healthCheckWindow|2 minuten|Een schuif tijd venster dat wordt gebruikt in combi natie met de instelling `healthCheckThreshold`.| 
|healthCheckThreshold|6|Maximum aantal keer dat de status controle kan mislukken voordat een host recyclen wordt gestart.| 
|counterThreshold|0,80|De drempel waarde waarbij een prestatie meter item wordt beschouwd als een slechte status.| 

## <a name="http"></a>http

Configuratie-instellingen voor [http-triggers en-bindingen](functions-bindings-http-webhook.md).

```json
{
    "http": {
        "routePrefix": "api",
        "maxOutstandingRequests": 200,
        "maxConcurrentRequests": 100,
        "dynamicThrottlesEnabled": true
    }
}
```

[!INCLUDE [functions-host-json-http](../../includes/functions-host-json-http.md)]

## <a name="id"></a>id

*Alleen versie 1. x.*

De unieke ID voor een beveiligingshost. Kan een kleine letter-GUID zijn waarbij streepjes worden verwijderd. Vereist wanneer lokaal wordt uitgevoerd. Bij het uitvoeren in azure wordt u aangeraden geen ID-waarde in te stellen. Een ID wordt automatisch gegenereerd in azure wanneer `id` wordt wegge laten. 

Als u een opslag account deelt in meerdere functie-apps, moet u ervoor zorgen dat elke functie-app een andere `id` heeft. U kunt de eigenschap `id` weglaten of de `id` van elke functie-app hand matig instellen op een andere waarde. De timer trigger gebruikt een opslag vergrendeling om ervoor te zorgen dat er slechts één timer exemplaar is wanneer een functie-app wordt geschaald naar meerdere exemplaren. Als twee functie-apps hetzelfde `id` delen en elk een timer trigger gebruikt, wordt er slechts één timer uitgevoerd.

```json
{
    "id": "9f4ea53c5136457d883d685e57164f08"
}
```

## <a name="logger"></a>Logger

Hiermee wordt gefilterd op Logboeken die zijn geschreven door een [ILogger-object](functions-monitoring.md#write-logs-in-c-functions) of [context. log](functions-monitoring.md#write-logs-in-javascript-functions).

```json
{
    "logger": {
        "categoryFilter": {
            "defaultLevel": "Information",
            "categoryLevels": {
                "Host": "Error",
                "Function": "Error",
                "Host.Aggregator": "Information"
            }
        }
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|categoryFilter|n.v.t.|Hiermee wordt gefilterd op categorie opgegeven| 
|defaultLevel|Informatie|Voor alle categorieën die niet zijn opgegeven in de matrix `categoryLevels`, verzendt u logboeken op dit niveau en hierboven naar Application Insights.| 
|categoryLevels|n.v.t.|Een matrix met categorieën die het minimale logboek niveau opgeven dat moet worden verzonden naar Application Insights voor elke categorie. De categorie die hier is opgegeven, bepaalt alle categorieën die beginnen met dezelfde waarde, en de langere waarden hebben prioriteit. In het voor gaande voor beeld van een *host. json* -bestand worden alle categorieën die beginnen met ' host. aggregator ' geregistreerd op `Information`-niveau. Alle andere categorieën die beginnen met ' host ', zoals ' host. uitvoerder ', melden zich op het niveau @no__t 0.| 

## <a name="queues"></a>Bestel

Configuratie-instellingen voor [opslag wachtrij-Triggers en-bindingen](functions-bindings-storage-queue.md).

```json
{
    "queues": {
      "maxPollingInterval": 2000,
      "visibilityTimeout" : "00:00:30",
      "batchSize": 16,
      "maxDequeueCount": 5,
      "newBatchThreshold": 8
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|maxPollingInterval|60000|Het maximum interval in milliseconden tussen de wachtrij polls.| 
|visibilityTimeout|0|Het tijds interval tussen nieuwe pogingen wanneer het verwerken van een bericht mislukt.| 
|batchSize|16|Het aantal wachtrij berichten dat door de functions-runtime gelijktijdig wordt opgehaald en processen parallel. Wanneer het aantal dat wordt verwerkt, wordt teruggestuurd naar de `newBatchThreshold`, haalt de runtime een nieuwe batch op en begint deze berichten te verwerken. Het maximum aantal gelijktijdige berichten dat per functie wordt verwerkt, is dus `batchSize` plus `newBatchThreshold`. Deze limiet geldt afzonderlijk voor elke door de wachtrij geactiveerde functie. <br><br>Als u wilt voor komen dat een parallelle uitvoering wordt uitgevoerd op berichten die worden ontvangen op één wachtrij, kunt u `batchSize` instellen op 1. Met deze instelling elimineert u echter alleen gelijktijdigheid, zolang uw functie-app wordt uitgevoerd op één virtuele machine (VM). Als de functie-app wordt geschaald naar meerdere Vm's, kan elke virtuele machine één exemplaar van elke door de wachtrij geactiveerde functie uitvoeren.<br><br>De maximale `batchSize` is 32. | 
|maxDequeueCount|5|Het aantal keren dat een bericht moet worden verwerkt voordat het naar de verontreinigde wachtrij wordt verplaatst.| 
|newBatchThreshold|batchSize/2|Telkens wanneer het aantal berichten dat gelijktijdig wordt verwerkt, naar dit nummer wordt opgehaald, haalt de runtime een andere batch op.| 

## <a name="sendgrid"></a>SendGrid

Configuratie-instelling voor de [SendGrind-uitvoer binding](functions-bindings-sendgrid.md)

```json
{
    "sendGrid": {
        "from": "Contoso Group <admin@contoso.com>"
    }
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|Van|n.v.t.|Het e-mail adres van de afzender over alle functies.| 

## <a name="servicebus"></a>serviceBus

Configuratie-instelling voor [Service Bus triggers en bindingen](functions-bindings-service-bus.md).

```json
{
    "serviceBus": {
      "maxConcurrentCalls": 16,
      "prefetchCount": 100,
      "autoRenewTimeout": "00:05:00"
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|maxConcurrentCalls|16|Het maximum aantal gelijktijdige aanroepen naar de retour aanroep dat de bericht pomp moet initiëren. De functie runtime verwerkt standaard meerdere berichten tegelijk. Stel `maxConcurrentCalls` in op 1 om de runtime te vragen om slechts één wachtrij of onderwerp bericht tegelijk te verwerken. | 
|prefetchCount|n.v.t.|De standaard PrefetchCount die wordt gebruikt door de onderliggende MessageReceiver.| 
|autoRenewTimeout|00:05:00|De maximale duur waarbinnen de bericht vergrendeling automatisch wordt vernieuwd.| 

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

## <a name="tracing"></a>tracering

*Versie 1. x*

Configuratie-instellingen voor logboeken die u maakt met behulp van een `TraceWriter`-object. Zie [ C# logboek registratie en](functions-reference-csharp.md#logging) [node. js-logboek registratie](functions-reference-node.md#writing-trace-output-to-the-console).

```json
{
    "tracing": {
      "consoleLevel": "verbose",
      "fileLoggingMode": "debugOnly"
    }
}
```

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------| 
|consoleLevel|valuta|Het tracerings niveau voor console logboek registratie. Opties zijn: `off`, `error`, `warning`, `info` en `verbose`.|
|fileLoggingMode|debugOnly|Het tracerings niveau voor logboek registratie van bestanden. Opties zijn `never`, `always`, `debugOnly`.| 

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
