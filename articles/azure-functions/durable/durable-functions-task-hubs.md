---
title: Taak hubs in Durable Functions-Azure
description: Meer informatie over wat een task hub is in de Durable Functions-extensie voor Azure Functions. Meer informatie over het configureren van taak hubs.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: b42294fdcf60add8496116bd1f83bf64f54a5f63
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614720"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Taak hubs in Durable Functions (Azure Functions)

Een *Task hub* in [Durable functions](durable-functions-overview.md) is een logische container voor Azure storage resources die worden gebruikt voor Orchestrations. Orchestrator-en activiteit functies kunnen alleen communiceren wanneer ze tot dezelfde taak hub behoren.

Als meerdere functie-apps een opslag account delen, *moet* elke functie-app worden geconfigureerd met de naam van een afzonderlijke taak hub. Een opslag account kan meerdere taak hubs bevatten. In het volgende diagram ziet u één taak-hub per functie-app in gedeelde en toegewezen opslag accounts.

![Diagram met gedeelde en speciale opslag accounts.](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure Storage resources

Een taak-hub bestaat uit de volgende opslag resources:

* Een of meer controle wachtrijen.
* Eén wachtrij voor werk items.
* Eén geschiedenis tabel.
* Tabel met één exemplaar.
* Eén opslag container met een of meer lease-blobs.
* Een opslag container met grote bericht payloads, indien van toepassing.

Al deze resources worden automatisch gemaakt in de standaard Azure Storage-account wanneer orchestrator-, entity-of activity-functies worden uitgevoerd of zijn gepland om te worden uitgevoerd. In het artikel [prestaties en schalen](durable-functions-perf-and-scale.md) wordt uitgelegd hoe deze bronnen worden gebruikt.

## <a name="task-hub-names"></a>Namen van taak hubs

Taak hubs worden geïdentificeerd aan de hand van een naam die is gedeclareerd in het *host. json* -bestand, zoals wordt weer gegeven in het volgende voor beeld:

### <a name="hostjson-functions-20"></a>host. json (functions 2,0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "MyTaskHub"
    }
  }
}
```

### <a name="hostjson-functions-1x"></a>host. json (functions 1. x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

Taak hubs kunnen ook worden geconfigureerd met behulp van app-instellingen, zoals wordt weer gegeven in het volgende `host.json` voorbeeld bestand:

### <a name="hostjson-functions-10"></a>host. json (functions 1,0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host. json (functions 2,0)

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": "%MyTaskHub%"
    }
  }
}
```

De naam van de taak-hub wordt ingesteld op de waarde van de app-instelling `MyTaskHub`. In de volgende `local.settings.json` ziet u hoe u de instelling `MyTaskHub` kunt definiëren als `samplehubname`:

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

De volgende code is een vooraf gecompileerd C# voor beeld van het schrijven van een functie die gebruikmaakt van de [Orchestration-client binding](durable-functions-bindings.md#orchestration-client) om te werken met een task hub die is geconfigureerd als een app-instelling:

### <a name="c"></a>C#

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [OrchestrationClient(TaskHub = "%MyTaskHub%")] IDurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // Function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

> [!NOTE]
> Het vorige C# voor beeld is voor Durable functions 2. x. Voor Durable Functions 1. x moet u `DurableOrchestrationContext` gebruiken in plaats van `IDurableOrchestrationContext`. Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

### <a name="javascript"></a>Javascript

De eigenschap task hub in het `function.json`-bestand is ingesteld via de app-instelling:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

Namen van taak hubs moeten beginnen met een letter en mogen alleen letters en cijfers bevatten. Als u niets opgeeft, wordt de naam van een standaard taak-hub gebruikt, zoals wordt weer gegeven in de volgende tabel:

| Duurzame extensie versie | Naam van de standaard taak-hub |
| - | - |
| 2.x | Bij implementatie in azure wordt de naam van de taak-hub afgeleid van de naam van de _functie-app_. Bij uitvoering buiten Azure wordt de standaard naam van de taak hub `TestHubName`. |
| 1.x | De standaard naam van de taak hub voor alle omgevingen is `DurableFunctionsHub`. |

Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen extensies.

> [!NOTE]
> De naam is wat een taak hub onderscheidt van een andere als er meerdere taak hubs in een gedeeld opslag account zijn. Als u meerdere functie-apps hebt die een gedeeld opslag account delen, moet u expliciet verschillende namen configureren voor elke Task hub in de *host. json* -bestanden. Anders kunnen de meerdere functie-apps met elkaar concurreren voor berichten. Dit kan leiden tot ongedefinieerd gedrag, met inbegrip van indelingen die onverwacht zijn vastgelopen in de `Pending` of `Running` status.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het afhandelen van Orchestrator versie beheer](durable-functions-versioning.md)
