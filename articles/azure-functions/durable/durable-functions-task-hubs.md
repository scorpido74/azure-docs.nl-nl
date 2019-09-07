---
title: Taak hubs in Durable Functions-Azure
description: Meer informatie over wat een task hub is in de Durable Functions-extensie voor Azure Functions. Meer informatie over het configureren van taak hubs configureren.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2017
ms.author: azfuncdf
ms.openlocfilehash: b0a58251530467d788710b0584b15715a207e20f
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734329"
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

Al deze resources worden automatisch gemaakt in het standaard Azure Storage-account wanneer Orchestrator-of activity-functies worden uitgevoerd of zijn gepland om te worden uitgevoerd. In het artikel [prestaties en schalen](durable-functions-perf-and-scale.md) wordt uitgelegd hoe deze bronnen worden gebruikt.

## <a name="task-hub-names"></a>Namen van taak hubs

Taak hubs worden geïdentificeerd aan de hand van een naam die is gedeclareerd in het *host. json* -bestand, zoals wordt weer gegeven in het volgende voor beeld:

### <a name="hostjson-functions-1x"></a>host. json (functions 1. x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

### <a name="hostjson-functions-2x"></a>host. json (functions 2. x)

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

Taak hubs kunnen ook worden geconfigureerd met behulp van app-instellingen, zoals wordt weer gegeven in het volgende *host. json* -voorbeeld bestand:

### <a name="hostjson-functions-1x"></a>host. json (functions 1. x)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-2x"></a>host. json (functions 2. x)

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

De naam van de taak-hub wordt ingesteld op de waarde `MyTaskHub` van de app-instelling. In het `local.settings.json` volgende voor beeld ziet u hoe `MyTaskHub` u de `samplehubname`instelling definieert als:

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

Hier volgt een vooraf gecompileerd C# voor beeld van hoe u een functie schrijft die gebruikmaakt van een [OrchestrationClientBinding](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) om te werken met een task hub die is geconfigureerd als een app-instelling:

### <a name="c"></a>C#

```csharp
[FunctionName("HttpStart")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}")] HttpRequestMessage req,
    [OrchestrationClient(TaskHub = "%MyTaskHub%")] DurableOrchestrationClientBase starter,
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

### <a name="javascript"></a>JavaScript

De eigenschap task hub in het `function.json` bestand is ingesteld via de app-instelling:
```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

Namen van taak hubs moeten beginnen met een letter en mogen alleen letters en cijfers bevatten. Als niet wordt opgegeven, is de standaard naam **DurableFunctionsHub**.

> [!NOTE]
> De naam is wat een taak hub onderscheidt van een andere als er meerdere taak hubs in een gedeeld opslag account zijn. Als u meerdere functie-apps hebt die een gedeeld opslag account delen, moet u expliciet verschillende namen configureren voor elke Task hub in de *host. json* -bestanden. Anders kunnen de meerdere functie-apps met elkaar concurreren voor berichten. Dit kan leiden tot ongedefinieerd gedrag.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het verwerken van versie beheer](durable-functions-versioning.md)
