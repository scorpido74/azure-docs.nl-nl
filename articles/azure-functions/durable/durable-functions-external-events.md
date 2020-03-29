---
title: Externe gebeurtenissen afhandelen in duurzame functies - Azure
description: Meer informatie over het afhandelen van externe gebeurtenissen in de extensie Duurzame functies voor Azure-functies.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 0877161f8d668141c8efb7c06b10643bf209341f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76262959"
---
# <a name="handling-external-events-in-durable-functions-azure-functions"></a>Externe gebeurtenissen verwerken in duurzame functies (Azure-functies)

Orchestrator functies hebben de mogelijkheid om te wachten en te luisteren naar externe gebeurtenissen. Deze functie [van duurzame functies](durable-functions-overview.md) is vaak handig voor het hanteren van menselijke interactie of andere externe triggers.

> [!NOTE]
> Externe gebeurtenissen zijn eenrichtingsasynchrone bewerkingen. Ze zijn niet geschikt voor situaties waarin de client die de gebeurtenis verzendt een synchrone reactie van de orchestrator-functie nodig heeft.

## <a name="wait-for-events"></a>Wachten op evenementen

Met `WaitForExternalEvent` de (.NET) en `waitForExternalEvent` (JavaScript) methoden van de [orchestration trigger binding](durable-functions-bindings.md#orchestration-trigger) kan een orchestrator functie asynchroon wachten en luisteren naar een externe gebeurtenis. De luisterorchestrator-functie verklaart de *naam* van de gebeurtenis en de *vorm van de gegevens* die zij verwacht te ontvangen.

# <a name="c"></a>[C #](#tab/csharp)

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
> De vorige C#-code is voor duurzame functies 2.x. Voor duurzame functies 1.x `DurableOrchestrationContext` moet `IDurableOrchestrationContext`u in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

Het voorgaande voorbeeld luistert naar een specifieke gebeurtenis en onderneemt actie wanneer deze wordt ontvangen.

U tegelijkertijd naar meerdere gebeurtenissen luisteren, zoals in het volgende voorbeeld, die wachten op een van de drie mogelijke gebeurtenismeldingen.

# <a name="c"></a>[C #](#tab/csharp)

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
> De vorige C#-code is voor duurzame functies 2.x. Voor duurzame functies 1.x `DurableOrchestrationContext` moet `IDurableOrchestrationContext`u in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

Het vorige voorbeeld luistert naar *een* van de meerdere gebeurtenissen. Het is ook mogelijk om te wachten op *alle* evenementen.

# <a name="c"></a>[C #](#tab/csharp)

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
> De vorige code is voor duurzame functies 2.x. Voor duurzame functies 1.x `DurableOrchestrationContext` moet `IDurableOrchestrationContext`u in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

Als het laadvermogen van de gebeurtenis in `T`.NET niet kan worden omgezet in het verwachte type, wordt een uitzondering gemaakt.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

`WaitForExternalEvent`wacht voor onbepaalde tijd op wat input.  De functie-app kan veilig worden gelost tijdens het wachten. Als en wanneer een gebeurtenis aankomt voor deze orchestration-instantie, wordt deze automatisch gewekt en verwerkt deze onmiddellijk de gebeurtenis.

> [!NOTE]
> Als uw functie-app het verbruiksplan gebruikt, worden er geen factureringskosten `WaitForExternalEvent` gemaakt terwijl een `waitForExternalEvent` orchestrator-functie wacht op een taak van (.NET) of (JavaScript), ongeacht hoe lang deze wacht.

## <a name="send-events"></a>Gebeurtenissen verzenden

De `RaiseEventAsync` (.NET) `raiseEvent` of (JavaScript)-methode van de [orchestration clientbinding](durable-functions-bindings.md#orchestration-client) verzendt de gebeurtenissen waarop `WaitForExternalEvent` (.NET) of `waitForExternalEvent` (JavaScript) wacht.  De `RaiseEventAsync` methode neemt *eventName* en *eventData* als parameters. De gebeurtenisgegevens moeten JSON-serializable zijn.

Hieronder vindt u een voorbeeldfunctie die door wachtrijen wordt geactiveerd en die een gebeurtenis 'Goedkeuring' naar een instantie voor de orchestrator-functie verzendt. De orchestration instance ID komt uit de hoofdtekst van het wachtrijbericht.

# <a name="c"></a>[C #](#tab/csharp)

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
> De vorige C#-code is voor duurzame functies 2.x. Voor duurzame functies 1.x `OrchestrationClient` moet u `DurableClient` attribuut gebruiken in plaats `DurableOrchestrationClient` van het `IDurableOrchestrationClient`kenmerk en moet u het parametertype gebruiken in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function(context, instanceId) {
    const client = df.getClient(context);
    await client.raiseEvent(instanceId, "Approval", true);
};
```

---

Intern worden `RaiseEventAsync` in (.NET) of `raiseEvent` (JavaScript) een bericht indewachtrijen dat wordt opgepikt door de wachtorchestrator-functie. Als de instantie niet wacht op de opgegeven *gebeurtenisnaam,* wordt het gebeurtenisbericht toegevoegd aan een wachtrij in het geheugen. Als de instantie orchestration later naar die gebeurtenisnaam begint te *luisteren,* wordt de wachtrij gecontroleerd op gebeurtenisberichten.

> [!NOTE]
> Als er geen orchestration-instantie is met de opgegeven *instantie-id,* wordt het gebeurtenisbericht verwijderd.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het implementeren van foutafhandeling](durable-functions-error-handling.md)

> [!div class="nextstepaction"]
> [Een voorbeeld uitvoeren dat wacht op menselijke interactie](durable-functions-phone-verification.md)