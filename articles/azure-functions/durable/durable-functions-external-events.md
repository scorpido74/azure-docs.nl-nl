---
title: Externe gebeurtenissen in Durable Functions-Azure verwerken
description: Meer informatie over het afhandelen van externe gebeurtenissen in de extensie Durable Functions voor Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 387b5d920de4a295366cc7e948862a12cea901d3
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86165546"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Externe gebeurtenissen in Durable Functions verwerken (Azure Functions)

Orchestrator-functies hebben de mogelijkheid om te wachten op externe gebeurtenissen en te Luis teren. Deze functie van [Durable functions](durable-functions-overview.md) is vaak handig voor het afhandelen van menselijke interactie of andere externe triggers.

> [!NOTE]
> Externe gebeurtenissen zijn asynchrone bewerkingen in één richting. Deze zijn niet geschikt voor situaties waarin de client die de gebeurtenis verzendt, een synchrone reactie van de Orchestrator-functie nodig heeft.

## <a name="wait-for-events"></a>Wachten op gebeurtenissen

De methoden [WaitForExternalEvent](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_WaitForExternalEvent_) (.net) en `waitForExternalEvent` (Java script) van de Orchestration- [trigger binding](durable-functions-bindings.md#orchestration-trigger) zorgen ervoor dat een Orchestrator-functie asynchroon wacht en luistert naar een externe gebeurtenis. De functie voor het Luis teren van Orchestrator declareert de *naam* van de gebeurtenis en de *vorm van de gegevens* die ze verwacht te ontvangen.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("BudgetApproval")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

> [!NOTE]
> De vorige C#-code is voor Durable Functions 2. x. Voor Durable Functions 1. x moet u `DurableOrchestrationContext` in plaats van gebruiken `IDurableOrchestrationContext` . Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

---

In het voor gaande voor beeld wordt geluisterd naar een specifieke gebeurtenis en wordt er actie ondernomen wanneer het wordt ontvangen.

U kunt gelijktijdig naar meerdere gebeurtenissen Luis teren, zoals in het volgende voor beeld, waarin wordt gewacht op een van de drie mogelijke gebeurtenis meldingen.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("Select")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

> [!NOTE]
> De vorige C#-code is voor Durable Functions 2. x. Voor Durable Functions 1. x moet u `DurableOrchestrationContext` in plaats van gebruiken `IDurableOrchestrationContext` . Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

---

In het vorige voor beeld wordt geluisterd naar *een* of meer gebeurtenissen. Het is ook mogelijk te wachten op *alle* gebeurtenissen.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("NewBuildingPermit")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
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

> [!NOTE]
> De vorige code is voor Durable Functions 2. x. Voor Durable Functions 1. x moet u `DurableOrchestrationContext` in plaats van gebruiken `IDurableOrchestrationContext` . Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

Als de nettolading van de gebeurtenis in .NET niet kan worden geconverteerd naar het verwachte type `T` , wordt er een uitzonde ring gegenereerd.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

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

---

`WaitForExternalEvent`voor sommige invoer wordt oneindig gewacht.  De functie-app kan tijdens het wachten veilig worden verwijderd. Als en wanneer een gebeurtenis arriveert voor dit Orchestrator-exemplaar, wordt deze automatisch geactiveerd en wordt de gebeurtenis onmiddellijk verwerkt.

> [!NOTE]
> Als uw functie-app gebruikmaakt van het verbruiks abonnement, worden er geen facturerings kosten in rekening gebracht terwijl een Orchestrator-functie wacht op een taak van `WaitForExternalEvent` (.net) of `waitForExternalEvent` (Java script), ongeacht hoe lang het wacht.

## <a name="send-events"></a>Gebeurtenissen verzenden

U kunt de methoden [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_) (.net) of `raiseEventAsync` (Java script) gebruiken om een externe gebeurtenis naar een indeling te verzenden. Deze methoden worden weer gegeven door de [Orchestration-client](durable-functions-bindings.md#orchestration-client) binding. U kunt ook de ingebouwde [HTTP-API](durable-functions-http-api.md#raise-event) voor het activeren van gebeurtenissen gebruiken om een externe gebeurtenis naar een indeling te verzenden.

Een verhoogde gebeurtenis omvat een *exemplaar-id*, een *eventname*en *Event Data* als para meters. Orchestrator-functies verwerken deze gebeurtenissen met behulp van de `WaitForExternalEvent` api's (.net) of `waitForExternalEvent` (Java script). De *eventname* moet overeenkomen met de verzend-en ontvangst eindigt om de gebeurtenis te verwerken. De gebeurtenis gegevens moeten ook JSON-serialiseerbaar zijn.

Intern worden de mechanismen ' gebeurtenis verhogen ' gebruikt om een bericht in de wachtrij te plaatsen dat wordt opgehaald door de functie voor wachtende functies. Als het exemplaar niet op de opgegeven *gebeurtenis naam wacht,* wordt het gebeurtenis bericht toegevoegd aan een in-Memory wachtrij. Als het Orchestrator-exemplaar later begint met Luis teren naar die *gebeurtenis naam,* wordt de wachtrij gecontroleerd op gebeurtenis berichten.

> [!NOTE]
> Als er geen Orchestrator-exemplaar is met de opgegeven *exemplaar-id*, wordt het gebeurtenis bericht verwijderd.

Hieronder ziet u een voor beeld van een door de wachtrij geactiveerde functie die een ' goed keuring ' gebeurtenis naar een Orchestrator-functie exemplaar verzendt. De indelings exemplaar-ID is afkomstig van de hoofd tekst van het wachtrij bericht.

# <a name="c"></a>[C#](#tab/csharp)

```csharp
[FunctionName("ApprovalQueueProcessor")]
public static async Task Run(
    [QueueTrigger("approval-queue")] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    await client.RaiseEventAsync(instanceId, "Approval", true);
}
```

> [!NOTE]
> De vorige C#-code is voor Durable Functions 2. x. Voor Durable Functions 1. x, moet u `OrchestrationClient` kenmerk gebruiken in plaats van het `DurableClient` kenmerk, en moet u het `DurableOrchestrationClient` parameter type gebruiken in plaats van `IDurableOrchestrationClient` . Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

---

Intern, `RaiseEventAsync` (.net) of `raiseEvent` (Java script) in een bericht dat wordt opgehaald door de functie voor wachtende functies. Als het exemplaar niet op de opgegeven *gebeurtenis naam wacht,* wordt het gebeurtenis bericht toegevoegd aan een in-Memory wachtrij. Als het Orchestrator-exemplaar later begint met Luis teren naar die *gebeurtenis naam,* wordt de wachtrij gecontroleerd op gebeurtenis berichten.

> [!NOTE]
> Als er geen Orchestrator-exemplaar is met de opgegeven *exemplaar-id*, wordt het gebeurtenis bericht verwijderd.

### <a name="http"></a>HTTP

Hier volgt een voor beeld van een HTTP-aanvraag waarmee een ' goed keuring '-gebeurtenis wordt gegenereerd aan een Orchestration-exemplaar. 

```http
POST /runtime/webhooks/durabletask/instances/MyInstanceId/raiseEvent/Approval&code=XXX
Content-Type: application/json

"true"
```

In dit geval is de exemplaar-ID hardcoded als *MyInstanceId*.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het implementeren van fout afhandeling](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [Een voor beeld uitvoeren dat wacht op menselijke interactie](durable-functions-phone-verification.md)