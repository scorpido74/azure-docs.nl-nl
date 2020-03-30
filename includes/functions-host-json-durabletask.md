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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80117020"
---
Configuratie-instellingen voor [duurzame functies](../articles/azure-functions/durable-functions-overview.md).

### <a name="durable-functions-1x"></a>Duurzame functies 1,x

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

### <a name="durable-functions-2x"></a><a name="durable-functions-2-0-host-json"></a>Duurzame functies 2.x

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

Namen van taakhubs moeten beginnen met een letter en bestaan alleen uit letters en cijfers. Als dit niet is opgegeven, is de standaardtaakhubnaam voor een functie-app **DurableFunctionsHub.** Zie [Taakhubs voor](../articles/azure-functions/durable-functions-task-hubs.md)meer informatie.

|Eigenschap  |Standaard | Beschrijving |
|---------|---------|---------|
|hubNaam|Duurzame FunctionsHub|Alternatieve [taakhubnamen](../articles/azure-functions/durable-functions-task-hubs.md) kunnen worden gebruikt om meerdere toepassingen voor duurzame functies van elkaar te isoleren, zelfs als ze dezelfde back-end voor opslag gebruiken.|
|controlQueueBatchSize|32|Het aantal berichten dat tegelijk uit de besturingselementwachtrij moet worden opgehaald.|
|controlQueueBufferThreshold|256|Het aantal controlewachtrijberichten dat tegelijkertijd in het geheugen kan worden gebufferd, waarna de dispatcher wacht voordat de extra berichten worden gedequeuerd.|
|partitionCount |4|Het aantal partities voor de besturingselementwachtrij. Kan een positief geheel getal tussen 1 en 16 zijn.|
|controlQueueVisibilityTimeout |5 minuten|De time-out van de zichtbaarheid van de wachtrijberichten in de wachtrij in de wachtrij.|
|workItemQueueVisibilityTimeout |5 minuten|De time-out van de wachtrijberichten voor werkitems in de wachtrij.|
|maxConcurrentActiviteitsfuncties |10x het aantal processors op de huidige machine|Het maximum aantal activiteitsfuncties dat gelijktijdig kan worden verwerkt op één hostexemplaar.|
|maxConcurrentOrchestratorFuncties |10x het aantal processors op de huidige machine|Het maximum aantal orchestrator-functies dat gelijktijdig kan worden verwerkt op één hostinstantie.|
|maxQueuePollingInterval|30 seconden|Het maximale controle- en werkitemwachtrijpolling-interval in de *indeling hh:mm:ss.* Hogere waarden kunnen leiden tot hogere latencies voor het verwerken van berichten. Lagere waarden kunnen leiden tot hogere opslagkosten als gevolg van hogere opslagtransacties.|
|azureStorageConnectionStringName |AzureWebJobsStorage|De naam van de app-instelling met de tekenreeks Azure Storage-verbinding die wordt gebruikt om de onderliggende Azure Storage-resources te beheren.|
|trackingStoreConnectionStringName||De naam van een verbindingstekenreeks die u wilt gebruiken voor de tabellen Geschiedenis en Instanties. Als dit niet `azureStorageConnectionStringName` is opgegeven, wordt de verbinding gebruikt.|
|trackingStoreNamePrefix||Het voorvoegsel dat moet worden `trackingStoreConnectionStringName` gebruikt voor de tabellen Geschiedenis en Instanties wanneer dit is opgegeven. Als dit niet is ingesteld, `DurableTask`is de standaardvoorvoegwaarde . Als `trackingStoreConnectionStringName` dit niet is opgegeven, gebruiken de `hubName` tabellen Geschiedenis en Instanties de `trackingStoreNamePrefix` waarde als voorvoegsel en worden elke instelling voor genegeerd.|
|traceInputsAndOutputs |false|Een waarde die aangeeft of de ingangen en uitvoer van functieaanroepen moeten worden getraceerd. Het standaardgedrag bij het traceren van functieuitvoeringsgebeurtenissen is het opnemen van het aantal bytes in de geserialiseerde ingangen en uitvoer voor functieaanroepen. Dit gedrag biedt minimale informatie over hoe de ingangen en uitgangen eruit zien zonder de logboeken op te zwellen of per ongeluk gevoelige informatie bloot te stellen. Als u deze eigenschap op true instelt, wordt de standaardfunctielogboekregistratie de volledige inhoud van functie-ingangen en uitvoerlogboeken.|
|logReplayEvents logReplayEvents logReplayEvents logReplay|false|Een waarde die aangeeft of orchestration replay-gebeurtenissen moeten worden geschreven naar Application Insights.|
|eventGridTopicEndpoint ||De URL van een aangepast eindpunt van een aangepast onderwerp van Azure Event Grid. Wanneer deze eigenschap is ingesteld, worden meldingen van de levenscyclus van orkestratie gepubliceerd naar dit eindpunt. Deze eigenschap ondersteunt de resolutie App-instellingen.|
|eventGridKeySettingName ||De naam van de app-instelling met de sleutel die wordt gebruikt `EventGridTopicEndpoint`voor het verifiëren met het aangepaste azure-gebeurtenisrasteronderwerp op .|
|eventGridPublishRetryCount|0|Het aantal keren dat u opnieuw moet proberen als publiceren in het gebeurtenisrasteronderwerp mislukt.|
|eventGridPublishRetryInterval|5 minuten|Het gebeurtenisraster publiceert het interval voor nieuwe try's in de indeling *hh:mm:ss.*|
|eventGridPublishEventTypes||Een lijst met gebeurtenistypen die u wilt publiceren in Gebeurtenisraster. Als dit niet is opgegeven, worden alle gebeurtenistypen gepubliceerd. Toegestane waarden `Started`zijn `Completed` `Failed`, `Terminated`, , .|
|gebruikGracefulShutdown|false|(Preview) Schakel het op een elegante manier afsluiten in om de kans te verkleinen dat de uitvoeringen van de host-uitschakelingen in het proces in het proces mislukken.|

Veel van deze instellingen zijn voor het optimaliseren van de prestaties. Zie [Prestaties en schaal voor](../articles/azure-functions/durable-functions-perf-and-scale.md)meer informatie.
