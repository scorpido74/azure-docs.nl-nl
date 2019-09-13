---
title: Preview-functies Durable Functions-Azure Functions
description: Meer informatie over preview-functies voor Durable Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: article
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: 8f2560141eac4e7d9fed267d347990e65bfe9c26
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933249"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Durable Functions 2,0 Preview (Azure Functions)

*Durable functions* is een uitbrei ding van [Azure functions](../functions-overview.md) en [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) waarmee u stateful functies in een serverloze omgeving kunt schrijven. Met de extensie worden status, controlepunten en het opnieuw opstarten voor u beheerd. Als u nog niet bekend bent met Durable Functions, raadpleegt u de documentatie van het [overzicht](durable-functions-overview.md).

Durable Functions 1. x is een functie voor het maken van een GA (algemeen beschikbaar) van Azure Functions, maar bevat ook verschillende subonderdelen die momenteel zijn opgenomen in de open bare preview. In dit artikel worden nieuwe uitgebrachte preview-functies beschreven en wordt uitgelegd hoe ze werken en hoe u ze kunt gaan gebruiken.

> [!NOTE]
> Deze preview-functies maken deel uit van een Durable Functions 2,0-release, die momenteel een **Preview-kwaliteits release** is met verschillende belang rijke wijzigingen. De Azure Functions duurzame uitbreidings pakket builds kunt u vinden op nuget.org met versies in de vorm van **2.0.0-betax**. Deze builds zijn niet bedoeld voor productie werkbelastingen en volgende releases kunnen extra belang rijke wijzigingen bevatten.

## <a name="breaking-changes"></a>Wijzigingen die fouten veroorzaken

Verschillende belang rijke wijzigingen worden geïntroduceerd in Durable Functions 2,0. Bestaande toepassingen zijn niet naar verwachting compatibel met Durable Functions 2,0 zonder code wijzigingen. In deze sectie vindt u enkele van de wijzigingen:

### <a name="hostjson-schema"></a>Host. json-schema

Het volgende code fragment toont het nieuwe schema voor host. json. De belangrijkste wijzigingen die u moet herkennen, zijn de nieuwe subsecties:

* `"storageProvider"`(en de `"azureStorage"` Subsectie) voor Storage-specifieke configuratie
* `"tracking"`voor het bijhouden en registreren van configuratie
* `"notifications"`(en de `"eventGrid"` Subsectie) voor configuratie van gebeurtenis raster meldingen

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "azureStorage": {
          "connectionStringName": <string>,
          "controlQueueBatchSize": <int?>,
          "partitionCount": <int?>,
          "controlQueueVisibilityTimeout": <hh:mm:ss?>,
          "workItemQueueVisibilityTimeout": <hh:mm:ss?>,
          "trackingStoreConnectionStringName": <string?>,
          "trackingStoreNamePrefix": <string?>,
          "maxQueuePollingInterval": <hh:mm:ss?>
        }
      },
      "tracking": {
        "traceInputsAndOutputs": <bool?>,
        "traceReplayEvents": <bool?>,
      },
      "notifications": {
        "eventGrid": {
          "topicEndpoint": <string?>,
          "keySettingName": <string?>,
          "publishRetryCount": <string?>,
          "publishRetryInterval": <hh:mm:ss?>,
          "publishRetryHttpStatus": <int[]?>,
          "publishEventTypes": <string[]?>,
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "customLifeCycleNotificationHelperType": <string?>
  }
}
```

Als Durable functions 2,0 blijft stabiliseren, worden er meer wijzigingen aangebracht in de `durableTask` sectie host. json. Zie [Dit github-probleem](https://github.com/Azure/azure-functions-durable-extension/issues/641)voor meer informatie over deze wijzigingen.

### <a name="public-interface-changes"></a>Wijzigingen in de open bare interface

De verschillende ' context ' objecten die door Durable Functions worden ondersteund, bevatten abstracte basis klassen die zijn bedoeld voor gebruik in eenheids testen. Als onderdeel van Durable Functions 2,0 zijn deze abstracte basis klassen vervangen door interfaces. Functie code die rechtstreeks gebruikmaakt van de concrete typen, wordt niet beïnvloed.

De volgende tabel bevat de belangrijkste wijzigingen:

| Oud type | Nieuw type |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |
| OrchestrationClientAttribute | DurableClientAttribute |

Als een abstracte basis klasse virtuele methoden bevatte, zijn deze virtuele methoden vervangen door extensie methoden die zijn gedefinieerd in `DurableContextExtensions`.

## <a name="entity-functions"></a>Entiteit functies

Vanaf Durable Functions v 2.0.0-alpha hebben we een nieuw concept voor [entiteits functies](durable-functions-entities.md) geïntroduceerd.

Met entiteits functies worden bewerkingen gedefinieerd voor het lezen en bijwerken van kleine stukjes status, ook wel *duurzame entiteiten*genoemd. Net als Orchestrator functions zijn entiteits functies functies met een speciaal trigger type, *entiteits trigger*. In tegens telling tot Orchestrator-functies hebben entiteits functies geen specifieke code beperkingen. Met entiteits functies wordt ook de status expliciet beheerd in plaats van impliciet de status via de controle stroom te vertegenwoordigen.

Op basis van de eerste feedback van gebruikers hebben we later ondersteuning toegevoegd voor een op klassen gebaseerd programmeer model voor entiteiten in Durable Functions v 2.0.0-beta1.

Zie het artikel [entiteits functies](durable-functions-entities.md) voor meer informatie.

## <a name="durable-http"></a>Duurzame HTTP

Vanaf Durable Functions v 2.0.0-beta2 hebben we een nieuwe [duurzame http](durable-functions-http-features.md) -functie geïntroduceerd waarmee u het volgende kunt doen:

* HTTP-Api's rechtstreeks aanroepen vanuit Orchestration-functies (met enkele gedocumenteerde beperkingen)
* Implementeert automatische polling van HTTP 202-status van client zijde
* Ingebouwde ondersteuning voor door [Azure beheerde identiteiten](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Zie het artikel [http-functies](durable-functions-http-features.md#consuming-http-apis) voor meer informatie.

## <a name="alternate-storage-providers"></a>Alternatieve opslag providers

Het duurzame taak raamwerk ondersteunt nu meerdere opslag providers, waaronder [Azure Storage](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage), [Azure service bus](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), een [in-Memory Emulator](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)en een experimentele [redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) -provider. Tot nu toe daarentegen is de duurzame taak extensie voor Azure Functions alleen de Azure Storage provider ondersteund. Vanaf Durable Functions 2,0 wordt de ondersteuning voor alternatieve opslag providers toegevoegd, te beginnen met de redis-provider.

> [!NOTE]
> Durable Functions 2,0 ondersteunt alleen .NET Standard 2,0-compatibele providers.

### <a name="emulator"></a>Emulator

De [DurableTask. emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) -provider is een lokaal geheugen, een niet-duurzame opslag provider, die geschikt is voor lokale test scenario's. Het kan worden geconfigureerd met behulp van het volgende minimale **host. json** -schema:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { "enabled": true }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis (experimenteel)

De provider [DurableTask. redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) houdt alle indelings status van een geconfigureerd redis-cluster.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "redis": {
          "connectionStringName": <string>,
        }
      }
    }
  }
}
```

De `connectionStringName` moet verwijzen naar de naam van een app-instelling of omgevings variabele. Deze app-instelling of omgevings variabele moet een redis connection string-waarde bevatten in de vorm *Server: poort*. Bijvoorbeeld `localhost:6379` om verbinding te maken met een lokale redis-cluster.

> [!NOTE]
> De redis-provider is momenteel experimentele en ondersteunt alleen functie-apps die worden uitgevoerd op één knoop punt. Het is niet gegarandeerd dat de redis-provider ooit algemeen beschikbaar wordt gemaakt en kan worden verwijderd in een toekomstige versie.
