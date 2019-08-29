---
title: Singleton voor Durable Functions-Azure
description: Het gebruik van Singleton in de Durable Functions extensie voor Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: d9bf9687f60e649fee98869ef263117177ad5efd
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097934"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Singleton-Orchestrator in Durable Functions (Azure Functions)

Voor achtergrond taken moet u er vaak voor zorgen dat slechts één exemplaar van een bepaalde Orchestrator tegelijk wordt uitgevoerd. Dit kan in [Durable functions](durable-functions-overview.md) worden gedaan door een specifieke exemplaar-id toe te wijzen aan een Orchestrator tijdens het maken.

## <a name="singleton-example"></a>Voor beeld van Singleton

In de C# volgende en Java script-voor beelden ziet u een http-trigger functie waarmee een singleton achtergrond taak indeling wordt gemaakt. De code zorgt ervoor dat er slechts één exemplaar bestaat voor een opgegeven exemplaar-ID.

### <a name="c"></a>C#

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    string instanceId,
    ILogger log)
{
    // Check if an instance with the specified ID already exists.
    var existingInstance = await starter.GetStatusAsync(instanceId);
    if (existingInstance == null)
    {
        // An instance with the specified ID doesn't exist, create one.
        dynamic eventData = await req.Content.ReadAsAsync<object>();
        await starter.StartNewAsync(functionName, instanceId, eventData);
        log.LogInformation($"Started orchestration with ID = '{instanceId}'.");
        return starter.CreateCheckStatusResponse(req, instanceId);
    }
    else
    {
        // An instance with the specified ID exists, don't create one.
        return req.CreateErrorResponse(
            HttpStatusCode.Conflict,
            $"An instance with ID '{instanceId}' already exists.");
    }
}
```

### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

Dit is het bestand function.json:
```json
{
  "bindings": [
    {
      "authLevel": "function",
      "name": "req",
      "type": "httpTrigger",
      "direction": "in",
      "route": "orchestrators/{functionName}/{instanceId}",
      "methods": ["post"]
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    },
    {
      "name": "$return",
      "type": "http",
      "direction": "out"
    }
  ]
}
```

Dit is de JavaScript-code:
```javascript
const df = require("durable-functions");

module.exports = async function(context, req) {
    const client = df.getClient(context);

    const instanceId = req.params.instanceId;
    const functionName = req.params.functionName;

    // Check if an instance with the specified ID already exists.
    const existingInstance = await client.getStatus(instanceId);
    if (!existingInstance) {
        // An instance with the specified ID doesn't exist, create one.
        const eventData = req.body;
        await client.startNew(functionName, instanceId, eventData);
        context.log(`Started orchestration with ID = '${instanceId}'.`);
        return client.createCheckStatusResponse(req, instanceId);
    } else {
        // An instance with the specified ID exists, don't create one.
        return {
            status: 409,
            body: `An instance with ID '${instanceId}' already exists.`,
        };
    }
};
```

Exemplaar-Id's zijn standaard wille keurig gegenereerde GUID'S. Maar in dit geval wordt de exemplaar-ID door gegeven in route gegevens van de URL. Met de code [](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetStatusAsync_) wordt GetStatusAsyncC#() `getStatus` of (Java script) aangeroepen om te controleren of een exemplaar met de opgegeven id al wordt uitgevoerd. Als dat niet het geval is, wordt er een exemplaar gemaakt met die ID.

> [!WARNING]
> Bij het lokaal ontwikkelen in Java script moet u de omgevings variabele `WEBSITE_HOSTNAME` instellen op `localhost:<port>`, bijvoorbeeld. `localhost:7071`om methoden te gebruiken `DurableOrchestrationClient`in. Zie het [github-probleem](https://github.com/Azure/azure-functions-durable-js/issues/28)voor meer informatie over deze vereiste.

> [!NOTE]
> Dit voor beeld bevat een mogelijke race voorwaarde. Als twee instanties van **HttpStartSingle** gelijktijdig worden uitgevoerd, wordt door beide functie aanroepen geslaagd, maar er wordt slechts één Orchestration-exemplaar gestart. Afhankelijk van uw vereisten kan dit ongewenste neven effecten hebben. Daarom is het belang rijk om ervoor te zorgen dat twee aanvragen deze trigger gelijktijdig kunnen uitvoeren.

De implementatie details van de Orchestrator-functie zijn niet werkelijk van belang. Dit kan een normale Orchestrator-functie zijn die wordt gestart en voltooid, of kan een van de functies zijn die permanent worden uitgevoerd (dat wil zeggen, een [eeuwige](durable-functions-eternal-orchestrations.md)-indeling). Het belang rijk punt is dat er maar één instantie tegelijk wordt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het aanroepen van sub-indelingen](durable-functions-sub-orchestrations.md)
