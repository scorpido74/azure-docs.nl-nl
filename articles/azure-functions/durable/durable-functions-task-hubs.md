---
title: Taakhubs in duurzame functies - Azure
description: Ontdek wat een taakhub is in de extensie Duurzame functies voor Azure-functies. Meer informatie over het configureren van taakhubs.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 427ab6c4e0e769ab881af0af3023d514c1b092c6
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2020
ms.locfileid: "81604615"
---
# <a name="task-hubs-in-durable-functions-azure-functions"></a>Taakhubs in duurzame functies (Azure-functies)

Een *taakhub* in [Duurzame functies](durable-functions-overview.md) is een logische container voor Azure Storage-bronnen die worden gebruikt voor orkestraties. Orchestrator- en activiteitsfuncties kunnen alleen met elkaar communiceren als ze tot dezelfde taakhub behoren.

Als meerdere functie-apps een opslagaccount delen, *moet* elke functie-app zijn geconfigureerd met een afzonderlijke taakhubnaam. Een opslagaccount kan meerdere taakhubs bevatten. In het volgende diagram wordt één taakhub per functie-app in gedeelde en toegewezen opslagaccounts geïllustreerd.

![Diagram met gedeelde en toegewezen opslagaccounts.](./media/durable-functions-task-hubs/task-hubs-storage.png)

## <a name="azure-storage-resources"></a>Azure Storage-bronnen

Een taakhub bestaat uit de volgende opslagbronnen:

* Een of meer controlewachtrijen.
* Eén wachtrij voor werkitems.
* Eén geschiedenistafel.
* Een instanties tabel.
* Eén opslagcontainer met een of meer leaseblobs.
* Een opslagcontainer met grote berichtenladingen, indien van toepassing.

Al deze resources worden automatisch gemaakt in het standaard Azure Storage-account wanneer orchestrator-, entiteits- of activiteitsfuncties worden uitgevoerd of gepland worden uitgevoerd. In het artikel [Prestaties en schaal](durable-functions-perf-and-scale.md) wordt uitgelegd hoe deze resources worden gebruikt.

## <a name="task-hub-names"></a>Namen van taakhubs

Taakhubs worden geïdentificeerd aan de andere kant van de regels:

* Bevat alleen alfanumerieke tekens
* Begint met een brief
* Heeft een minimale lengte van 3 tekens, maximale lengte van 45 tekens

De naam van de taakhub wordt gedeclareerd in het bestand *host.json,* zoals in het volgende voorbeeld wordt weergegeven:

### <a name="hostjson-functions-20"></a>host.json (functies 2.0)

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

### <a name="hostjson-functions-1x"></a>host.json (functies 1.x)

```json
{
  "durableTask": {
    "hubName": "MyTaskHub"
  }
}
```

Taakhubs kunnen ook worden geconfigureerd met app-instellingen, zoals in het volgende `host.json` voorbeeldbestand wordt weergegeven:

### <a name="hostjson-functions-10"></a>host.json (functies 1.0)

```json
{
  "durableTask": {
    "hubName": "%MyTaskHub%"
  }
}
```

### <a name="hostjson-functions-20"></a>host.json (functies 2.0)

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

De naam van de taakhub wordt `MyTaskHub` ingesteld op de waarde van de app-instelling. Het `local.settings.json` volgende laat zien `MyTaskHub` hoe `samplehubname`de instelling moet worden gedefinieerd als :

```json
{
  "IsEncrypted": false,
  "Values": {
    "MyTaskHub" : "samplehubname"
  }
}
```

In de volgende code wordt uitgelegd hoe u een functie schrijft die de [orchestration-clientbinding](durable-functions-bindings.md#orchestration-client) gebruikt om te werken met een taakhub die is geconfigureerd als app-instelling:

# <a name="c"></a>[C #](#tab/csharp)

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
> Het vorige C#-voorbeeld is voor Duurzame functies 2.x. Voor duurzame functies 1.x `DurableOrchestrationContext` moet `IDurableOrchestrationContext`u in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[Javascript](#tab/javascript)

De eigenschap taakhub `function.json` in het bestand wordt ingesteld via app-instelling:

```json
{
    "name": "input",
    "taskHub": "%MyTaskHub%",
    "type": "orchestrationClient",
    "direction": "in"
}
```

---

Namen van taakhubs moeten beginnen met een letter en bestaan alleen uit letters en cijfers. Als dit niet is opgegeven, wordt een standaardtaakhubnaam gebruikt zoals in de volgende tabel wordt weergegeven:

| Duurzame extensieversie | Standaardnaam taakhub |
| - | - |
| 2.x | Wanneer de naam van de taakhub wordt geïmplementeerd in Azure, wordt deze afgeleid van de naam van de _functie-app_. Wanneer u buiten Azure wordt uitgevoerd, `TestHubName`is de standaardnaam van de taakhub . |
| 1.x | De standaardnaam van de `DurableFunctionsHub`taakhub voor alle omgevingen is . |

Zie het artikel [Duurzame functies versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen extensieversies.

> [!NOTE]
> De naam is wat de ene taakhub onderscheidt van de andere wanneer er meerdere taakhubs in een gedeelde opslagaccount zijn. Als u meerdere functie-apps hebt die een gedeeld opslagaccount delen, moet u expliciet verschillende namen configureren voor elke taakhub in de *bestanden host.json.* Anders zullen de meerdere functie-apps met elkaar concurreren om berichten, wat kan leiden tot ongedefinieerd gedrag, waaronder orkestraties die onverwacht "vastlopen" in de `Pending` of-status. `Running`

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het verwerken van orchestration-versies](durable-functions-versioning.md)
