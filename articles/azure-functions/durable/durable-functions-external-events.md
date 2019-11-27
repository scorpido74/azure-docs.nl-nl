---
title: Externe gebeurtenissen in Durable Functions-Azure verwerken
description: Meer informatie over het afhandelen van externe gebeurtenissen in de extensie Durable Functions voor Azure Functions.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 7d7fcc725d78b24a93b09cb9c76cf7dc0231cac2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232883"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Externe gebeurtenissen in Durable Functions verwerken (Azure Functions)

Orchestrator-functies hebben de mogelijkheid om te wachten op externe gebeurtenissen en te Luis teren. Deze functie van [Durable functions](durable-functions-overview.md) is vaak handig voor het afhandelen van menselijke interactie of andere externe triggers.

> [!NOTE]
> Externe gebeurtenissen zijn asynchrone bewerkingen in één richting. Deze zijn niet geschikt voor situaties waarin de client die de gebeurtenis verzendt, een synchrone reactie van de Orchestrator-functie nodig heeft.

## <a name="wait-for-events"></a>Wachten op gebeurtenissen

Met de methoden `WaitForExternalEvent` (.NET) en `waitForExternalEvent` (Java script) van de [Orchestration-trigger binding](durable-functions-bindings.md#orchestration-trigger) kan een Orchestrator-functie asynchroon worden gewacht en geluisterd op een externe gebeurtenis. De functie voor het Luis teren van Orchestrator declareert de *naam* van de gebeurtenis en de *vorm van de gegevens* die ze verwacht te ontvangen.

### <a name="c"></a>C#

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
> De vorige C# code is voor Durable functions 2. x. Voor Durable Functions 1. x moet u `DurableOrchestrationContext` gebruiken in plaats van `IDurableOrchestrationContext`. Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

### <a name="javascript-functions-20-only"></a>Java script (alleen voor de functies 2,0)

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
> De vorige C# code is voor Durable functions 2. x. Voor Durable Functions 1. x moet u `DurableOrchestrationContext` gebruiken in plaats van `IDurableOrchestrationContext`. Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

#### <a name="javascript-functions-20-only"></a>Java script (alleen voor de functies 2,0)

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
> De vorige code is voor Durable Functions 2. x. Voor Durable Functions 1. x moet u `DurableOrchestrationContext` gebruiken in plaats van `IDurableOrchestrationContext`. Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

#### <a name="javascript-functions-20-only"></a>Java script (alleen voor de functies 2,0)

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

`WaitForExternalEvent` voor sommige invoer oneindig geduld.  De functie-app kan tijdens het wachten veilig worden verwijderd. Als en wanneer een gebeurtenis arriveert voor dit Orchestrator-exemplaar, wordt deze automatisch geactiveerd en wordt de gebeurtenis onmiddellijk verwerkt.

> [!NOTE]
> Als uw functie-app gebruikmaakt van het verbruiks abonnement, worden er geen facturerings kosten in rekening gebracht terwijl een Orchestrator-functie wacht op een taak van `WaitForExternalEvent` (.NET) of `waitForExternalEvent` (Java script), ongeacht hoe lang het wacht.

Als de nettolading van de gebeurtenis in .NET niet kan worden geconverteerd naar het verwachte type `T`, wordt er een uitzonde ring gegenereerd.

## <a name="send-events"></a>Gebeurtenissen verzenden

De `RaiseEventAsync` (.NET) of `raiseEvent` (Java script)-methode van de [Orchestrator client binding](durable-functions-bindings.md#orchestration-client) verzendt de gebeurtenissen die `WaitForExternalEvent` (.net) of `waitForExternalEvent` (Java script) wacht op.  Voor de methode `RaiseEventAsync` worden *eventname* en *Event Data* als para meters gebruikt. De gebeurtenis gegevens moeten JSON-serialiseerbaar zijn.

Hieronder ziet u een voor beeld van een door de wachtrij geactiveerde functie die een ' goed keuring ' gebeurtenis naar een Orchestrator-functie exemplaar verzendt. De indelings exemplaar-ID is afkomstig van de hoofd tekst van het wachtrij bericht.

### <a name="c"></a>C#

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
> De vorige C# code is voor Durable functions 2. x. Voor Durable Functions 1. x moet u `OrchestrationClient` kenmerk gebruiken in plaats van het kenmerk `DurableClient`, en moet u het `DurableOrchestrationClient` parameter type gebruiken in plaats van `IDurableOrchestrationClient`. Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

### <a name="javascript-functions-20-only"></a>Java script (alleen voor de functies 2,0)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

Intern, `RaiseEventAsync` (.NET) of `raiseEvent` (Java script) in een bericht dat wordt opgehaald door de functie voor wachtende Orchestrator. Als het exemplaar niet op de opgegeven *gebeurtenis naam wacht,* wordt het gebeurtenis bericht toegevoegd aan een in-Memory wachtrij. Als het Orchestrator-exemplaar later begint met Luis teren naar die *gebeurtenis naam,* wordt de wachtrij gecontroleerd op gebeurtenis berichten.

> [!NOTE]
> Als er geen Orchestrator-exemplaar is met de opgegeven *exemplaar-id*, wordt het gebeurtenis bericht verwijderd.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het implementeren van fout afhandeling](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [Een voor beeld uitvoeren dat wacht op menselijke interactie](durable-functions-phone-verification.md)