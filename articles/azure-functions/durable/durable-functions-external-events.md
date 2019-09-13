---
title: Externe gebeurtenissen in Durable Functions-Azure verwerken
description: Meer informatie over het afhandelen van externe gebeurtenissen in de extensie Durable Functions voor Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: e38f118e10c9d0e2347edb7cbaa5d7b68a0e63f2
ms.sourcegitcommit: f3f4ec75b74124c2b4e827c29b49ae6b94adbbb7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/12/2019
ms.locfileid: "70933400"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Externe gebeurtenissen in Durable Functions verwerken (Azure Functions)

Orchestrator-functies hebben de mogelijkheid om te wachten op externe gebeurtenissen en te Luis teren. Deze functie van [Durable functions](durable-functions-overview.md) is vaak handig voor het afhandelen van menselijke interactie of andere externe triggers.

> [!NOTE]
> Externe gebeurtenissen zijn asynchrone bewerkingen in één richting. Deze zijn niet geschikt voor situaties waarin de client die de gebeurtenis verzendt, een synchrone reactie van de Orchestrator-functie nodig heeft.

## <a name="wait-for-events"></a>Wachten op gebeurtenissen

Met de methode [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) kan een Orchestrator-functie asynchroon worden gewacht en geluisterd op een externe gebeurtenis. De functie voor het Luis teren van Orchestrator declareert de *naam* van de gebeurtenis en de *vorm van de gegevens* die ze verwacht te ontvangen.

### <a name="c"></a>C#

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    bool approved = await context.WaitForExternalEvent<bool>("Approval");
    if (approved)
    {
        // approval granted - do the approved action
    }
    else
    {
        // approval denied - send a notification
    }
}
```

### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const approved = yield context.df.waitForExternalEvent("Approval");
    if (approved) {
        // approval granted - do the approved action
    } else {
        // approval denied - send a notification
    }
});
```

In het voor gaande voor beeld wordt geluisterd naar een specifieke gebeurtenis en wordt er actie ondernomen wanneer het wordt ontvangen.

U kunt gelijktijdig naar meerdere gebeurtenissen Luis teren, zoals in het volgende voor beeld, waarin wordt gewacht op een van de drie mogelijke gebeurtenis meldingen.

#### <a name="c"></a>C#

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    var event1 = context.WaitForExternalEvent<float>("Event1");
    var event2 = context.WaitForExternalEvent<bool>("Event2");
    var event3 = context.WaitForExternalEvent<int>("Event3");

    var winner = await Task.WhenAny(event1, event2, event3);
    if (winner == event1)
    {
        // ...
    }
    else if (winner == event2)
    {
        // ...
    }
    else if (winner == event3)
    {
        // ...
    }
}
```

#### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const event1 = context.df.waitForExternalEvent("Event1");
    const event2 = context.df.waitForExternalEvent("Event2");
    const event3 = context.df.waitForExternalEvent("Event3");

    const winner = yield context.df.Task.any([event1, event2, event3]);
    if (winner === event1) {
        // ...
    } else if (winner === event2) {
        // ...
    } else if (winner === event3) {
        // ...
    }
});
```

In het vorige voor beeld wordt geluisterd naar *een* of meer gebeurtenissen. Het is ook mogelijk te wachten op *alle* gebeurtenissen.

#### <a name="c"></a>C#

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string applicationId = context.GetInput<string>();

    var gate1 = context.WaitForExternalEvent("CityPlanningApproval");
    var gate2 = context.WaitForExternalEvent("FireDeptApproval");
    var gate3 = context.WaitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    await Task.WhenAll(gate1, gate2, gate3);

    await context.CallActivityAsync("IssueBuildingPermit", applicationId);
}
```

#### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const applicationId = context.df.getInput();

    const gate1 = context.df.waitForExternalEvent("CityPlanningApproval");
    const gate2 = context.df.waitForExternalEvent("FireDeptApproval");
    const gate3 = context.df.waitForExternalEvent("BuildingDeptApproval");

    // all three departments must grant approval before a permit can be issued
    yield context.df.Task.all([gate1, gate2, gate3]);

    yield context.df.callActivity("IssueBuildingPermit", applicationId);
});
```

[WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) wacht oneindig voor sommige invoer.  De functie-app kan tijdens het wachten veilig worden verwijderd. Als en wanneer een gebeurtenis arriveert voor dit Orchestrator-exemplaar, wordt deze automatisch geactiveerd en wordt de gebeurtenis onmiddellijk verwerkt.

> [!NOTE]
> Als uw functie-app gebruikmaakt van het verbruiks abonnement, worden er geen facturerings kosten in rekening gebracht terwijl een Orchestrator-functie wacht `WaitForExternalEvent` op een taak van `waitForExternalEvent` (.net) of (Java script), ongeacht hoe lang het wacht.

Als de nettolading van de gebeurtenis in .net niet kan worden geconverteerd naar het `T`verwachte type, wordt er een uitzonde ring gegenereerd.

## <a name="send-events"></a>Gebeurtenissen verzenden

De methode [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) van de klasse [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) verzendt de gebeurtenissen die `WaitForExternalEvent` (.net) of `waitForExternalEvent` (Java script) wachten op.  Voor `RaiseEventAsync` de-methode worden *eventname* en *Event Data* als para meters gebruikt. De gebeurtenis gegevens moeten JSON-serialiseerbaar zijn.

Hieronder ziet u een voor beeld van een door de wachtrij geactiveerde functie die een ' goed keuring ' gebeurtenis naar een Orchestrator-functie exemplaar verzendt. De indelings exemplaar-ID is afkomstig van de hoofd tekst van het wachtrij bericht.

### <a name="c"></a>C#

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [OrchestrationClient] DurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

Intern, `RaiseEventAsync` (.net) of `raiseEvent` (Java script) in een bericht dat wordt opgehaald door de functie voor wachtende functies. Als het exemplaar niet op de opgegeven *gebeurtenis naam wacht,* wordt het gebeurtenis bericht toegevoegd aan een in-Memory wachtrij. Als het Orchestrator-exemplaar later begint met Luis teren naar die *gebeurtenis naam,* wordt de wachtrij gecontroleerd op gebeurtenis berichten.

> [!NOTE]
> Als er geen Orchestrator-exemplaar is met de opgegeven *exemplaar-id*, wordt het gebeurtenis bericht verwijderd. Zie het [github-probleem](https://github.com/Azure/azure-functions-durable-extension/issues/29)voor meer informatie over dit gedrag. 

> [!WARNING]
> Bij het lokaal ontwikkelen in Java script moet u de omgevings variabele `WEBSITE_HOSTNAME` instellen op `localhost:<port>`, bijvoorbeeld. `localhost:7071`om methoden te gebruiken `DurableOrchestrationClient`in. Zie het [github-probleem](https://github.com/Azure/azure-functions-durable-js/issues/28)voor meer informatie over deze vereiste.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het implementeren van fout afhandeling](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [Een voor beeld uitvoeren dat wacht op menselijke interactie](durable-functions-phone-verification.md)