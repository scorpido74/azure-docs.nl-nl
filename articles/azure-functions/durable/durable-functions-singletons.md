---
title: Singletons voor duurzame functies - Azure
description: Singletons gebruiken in de extensie Duurzame functies voor Azure-functies.
author: cgillum
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 4eff7c4c91ed664fcf1f4fc7a8be2d43d24e5c6b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76262806"
---
# <a name="singleton-orchestrators-in-durable-functions-azure-functions"></a>Singleton-orchestrators in duurzame functies (Azure-functies)

Voor achtergrondtaken moet u er vaak voor zorgen dat slechts één exemplaar van een bepaalde orchestrator tegelijk wordt uitgevoerd. U dit soort singletongedrag in [duurzame functies](durable-functions-overview.md) garanderen door een specifieke instantie-ID toe te wijs aan een orchestrator toe te staan bij het maken ervan.

## <a name="singleton-example"></a>Voorbeeld van Singleton

In het volgende voorbeeld wordt een HTTP-triggerfunctie weergegeven die een singleton-taakorkestratie op de achtergrond maakt. De code zorgt ervoor dat er slechts één instantie bestaat voor een opgegeven instantie-id.

# <a name="c"></a>[C #](#tab/csharp)

```cs
[FunctionName("HttpStartSingle")]
public static async Task<HttpResponseMessage> RunSingle(
    [HttpTrigger(AuthorizationLevel.Function, methods: "post", Route = "orchestrators/{functionName}/{instanceId}")] HttpRequestMessage req,
    [DurableClient] IDurableOrchestrationClient starter,
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

> [!NOTE]
> De vorige C#-code is voor duurzame functies 2.x. Voor duurzame functies 1.x `OrchestrationClient` moet u `DurableClient` attribuut gebruiken in plaats `DurableOrchestrationClient` van het `IDurableOrchestrationClient`kenmerk en moet u het parametertype gebruiken in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[Javascript](#tab/javascript)

**function.json**

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

**index.js**

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

---

Standaard zijn instantie-id's willekeurig gegenereerde GUIDs. In het vorige voorbeeld wordt de instantie-id echter doorgegeven in routegegevens van de URL. De codeaanroepen `GetStatusAsync`(C#) of `getStatus` (JavaScript) om te controleren of een instantie met de opgegeven id al wordt uitgevoerd. Als een dergelijke instantie niet wordt uitgevoerd, wordt een nieuwe instantie gemaakt met die ID.

> [!NOTE]
> Er is een potentiële race conditie in dit monster. Als twee exemplaren van **HttpStartSingle** gelijktijdig worden uitgevoerd, worden beide functieoproepen gemeld, maar wordt er slechts één orchestration-instantie daadwerkelijk gestart. Afhankelijk van uw eisen, Dit kan ongewenste bijwerkingen hebben. Daarom is het belangrijk om ervoor te zorgen dat geen twee aanvragen deze triggerfunctie gelijktijdig kunnen uitvoeren.

De implementatiedetails van de orchestrator functie doen er eigenlijk niet toe. Het kan een gewone orchestrator functie die begint en voltooit, of het kan een die voor altijd loopt (dat wil zeggen, een [Eeuwige Orchestration](durable-functions-eternal-orchestrations.md)). Het belangrijkste punt is dat er slechts één instantie tegelijk wordt uitgevoerd.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over de native HTTP-functies van orkestraties](durable-functions-http-features.md)
