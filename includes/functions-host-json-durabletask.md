---
title: bestand opnemen
description: bestand opnemen
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 03/14/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 6bb59db4c1b31033b1e116742dedc94621b1c60d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80117020"
---
Configuratie-instellingen voor [Durable functions](../articles/azure-functions/durable-functions-overview.md).

### <a name="durable-functions-1x"></a>Durable Functions 1. x

```json
{
  "durableTask": {
    "hubName": "MyTaskHub",
    "controlQueueBatchSize": 32,
    "partitionCount": 4,
    "controlQueueVisibilityTimeout": "00:05:00",
    "workItemQueueVisibilityTimeout": "00:05:00",
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "maxQueuePollingInterval": "00:00:30",
    "azureStorageConnectionStringName": "AzureWebJobsStorage",
    "trackingStoreConnectionStringName": "TrackingStorage",
    "trackingStoreNamePrefix": "DurableTask",
    "traceInputsAndOutputs": false,
    "logReplayEvents": false,
    "eventGridTopicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
    "eventGridKeySettingName":  "EventGridKey",
    "eventGridPublishRetryCount": 3,
    "eventGridPublishRetryInterval": "00:00:30",
    "eventGridPublishEventTypes": ["Started", "Completed", "Failed", "Terminated"]
  }
}
```

### <a name="durable-functions-2x"></a><a name="durable-functions-2-0-host-json"></a>Durable Functions 2. x

```json
{
 "extensions": {
  "durableTask": {
    "hubName": "MyTaskHub",
    "storageProvider": {
      "connectionStringName": "AzureWebJobsStorage",
      "controlQueueBatchSize": 32,
      "controlQueueBufferThreshold": 256,
      "controlQueueVisibilityTimeout": "00:05:00",
      "maxQueuePollingInterval": "00:00:30",
      "partitionCount": 4,
      "trackingStoreConnectionStringName": "TrackingStorage",
      "trackingStoreNamePrefix": "DurableTask",
      "workItemQueueVisibilityTimeout": "00:05:00",
    },
    "tracing": {
      "traceInputsAndOutputs": false,
      "traceReplayEvents": false,
    },
    "notifications": {
      "eventGrid": {
        "topicEndpoint": "https://topic_name.westus2-1.eventgrid.azure.net/api/events",
        "keySettingName": "EventGridKey",
        "publishRetryCount": 3,
        "publishRetryInterval": "00:00:30",
        "publishEventTypes": [
          "Started",
          "Pending",
          "Failed",
          "Terminated"
        ]
      }
    },
    "maxConcurrentActivityFunctions": 10,
    "maxConcurrentOrchestratorFunctions": 10,
    "extendedSessionsEnabled": false,
    "extendedSessionIdleTimeoutInSeconds": 30,
    "useGracefulShutdown": false
  }
  }
}

```

Namen van taak hubs moeten beginnen met een letter en mogen alleen letters en cijfers bevatten. Als deze niet wordt opgegeven, is de standaard naam van de taak-hub voor een functie-app **DurableFunctionsHub**. Zie [taak hubs](../articles/azure-functions/durable-functions-task-hubs.md)voor meer informatie.

|Eigenschap  |Standaard | Description |
|---------|---------|---------|
|hubName|DurableFunctionsHub|Alternatieve namen van [taak hubs](../articles/azure-functions/durable-functions-task-hubs.md) kunnen worden gebruikt om meerdere Durable functions toepassingen van elkaar te isoleren, zelfs als ze dezelfde opslag back-end gebruiken.|
|controlQueueBatchSize|32|Het aantal berichten dat tegelijkertijd moet worden opgehaald uit de controle wachtrij.|
|controlQueueBufferThreshold|256|Het aantal controle wachtrij berichten dat in het geheugen tegelijk kan worden gebufferd, op het moment dat de dispatcher wacht voordat extra berichten worden verwijderd.|
|partitionCount |4|Het aantal partities voor de controle wachtrij. Dit kan een positief geheel getal zijn tussen 1 en 16.|
|controlQueueVisibilityTimeout |5 minuten|De time-out voor de zicht baarheid van berichten in de wachtrij voor controle wachtrijen.|
|workItemQueueVisibilityTimeout |5 minuten|De time-out van de zicht baarheid van berichten in de wachtrij voor werk items in de wachtrij.|
|maxConcurrentActivityFunctions |10X het aantal processors op de huidige computer|Het maximum aantal activiteiten functies dat gelijktijdig kan worden verwerkt op één exemplaar van een host.|
|maxConcurrentOrchestratorFunctions |10X het aantal processors op de huidige computer|Het maximum aantal Orchestrator-functies dat gelijktijdig kan worden verwerkt op één exemplaar van een host.|
|maxQueuePollingInterval|30 seconden|Het maximale polling interval voor het beheer en de werk items in de indeling *uu: mm: SS* . Hogere waarden kunnen resulteren in hogere latenties voor bericht verwerking. Lagere waarden kunnen leiden tot hogere opslag kosten vanwege verhoogde opslag transacties.|
|azureStorageConnectionStringName |AzureWebJobsStorage|De naam van de app-instelling met de Azure Storage connection string gebruikt voor het beheren van de onderliggende Azure Storage resources.|
|trackingStoreConnectionStringName||De naam van een connection string die moet worden gebruikt voor de tabellen geschiedenis en exemplaren. Als u niets opgeeft, `azureStorageConnectionStringName` wordt de verbinding gebruikt.|
|trackingStoreNamePrefix||Het voor voegsel dat moet worden gebruikt voor de tabellen geschiedenis en instanties wanneer `trackingStoreConnectionStringName` is opgegeven. Als deze niet is ingesteld, is de standaard waarde voor het voor voegsel `DurableTask` . Als `trackingStoreConnectionStringName` niet is opgegeven, gebruiken de tabellen geschiedenis en exemplaren de `hubName` waarde als voor voegsel en wordt een instelling voor `trackingStoreNamePrefix` genegeerd.|
|traceInputsAndOutputs |false|Een waarde die aangeeft of de invoer en uitvoer van functie aanroepen moeten worden getraceerd. Het standaard gedrag bij het traceren van functie-uitvoerings gebeurtenissen omvat het aantal bytes in de geserialiseerde invoer en uitvoer voor functie aanroepen. Dit gedrag biedt minimale informatie over hoe de invoer en uitvoer eruitzien zonder de logboeken te vertonen of per ongeluk gevoelige informatie weer te geven. Als u deze eigenschap instelt op True, wordt de volledige inhoud van functie-invoer en-uitvoer in de standaard functie vastgelegd.|
|logReplayEvents|false|Een waarde die aangeeft of het schrijven van Orchestrator replay-gebeurtenissen naar Application Insights.|
|eventGridTopicEndpoint ||De URL van een Azure Event Grid aangepast onderwerp-eind punt. Wanneer deze eigenschap is ingesteld, worden meldings gebeurtenissen van de Orchestration-cyclus naar dit eind punt gepubliceerd. Deze eigenschap ondersteunt de resolutie van de app-instellingen.|
|eventGridKeySettingName ||De naam van de app-instelling met de sleutel die wordt gebruikt voor de verificatie met het aangepaste onderwerp Azure Event Grid op `EventGridTopicEndpoint` .|
|eventGridPublishRetryCount|0|Het aantal keren dat het opnieuw moet worden uitgevoerd als het publiceren naar het Event Grid-onderwerp mislukt.|
|eventGridPublishRetryInterval|5 minuten|Het interval voor nieuwe pogingen Event Grid wordt gepubliceerd in de notatie *uu: mm: SS* .|
|eventGridPublishEventTypes||Een lijst met gebeurtenis typen die moeten worden gepubliceerd naar Event Grid. Als u niets opgeeft, worden alle gebeurtenis typen gepubliceerd. Toegestane waarden zijn onder andere `Started` , `Completed` , `Failed` , `Terminated` .|
|useGracefulShutdown|false|Evaluatie Schakel probleemloos afsluiten in om de kans op het afsluiten van de host te verminderen tijdens het uitvoeren van de uitvoering van de functie.|

Veel van deze instellingen zijn voor het optimaliseren van de prestaties. Zie [prestaties en schalen](../articles/azure-functions/durable-functions-perf-and-scale.md)voor meer informatie.
