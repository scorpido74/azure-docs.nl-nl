---
title: Timers in Durable Functions-Azure
description: Meer informatie over het implementeren van duurzame timers in de Durable Functions-extensie voor Azure Functions.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: c477a81801c1345d5be5f5f45419bb4776c875e0
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74231273"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Timers in Durable Functions (Azure Functions)

[Durable functions](durable-functions-overview.md) biedt *duurzame timers* voor gebruik in Orchestrator-functies voor het implementeren van vertragingen of het instellen van time-outs voor asynchrone acties. Duurzame timers moeten worden gebruikt in Orchestrator-functies in plaats van `Thread.Sleep` en `Task.Delay`C#(), of `setTimeout()` en `setInterval()` (Java script).

U maakt een duurzame timer door de methode van de `CreateTimer` (.NET) of de methode `createTimer` (Java script) van de [Orchestration-trigger binding](durable-functions-bindings.md#orchestration-trigger)aan te roepen. De methode retourneert een taak die op een opgegeven datum en tijd wordt voltooid.

## <a name="timer-limitations"></a>Beperkingen van de timer

Wanneer u een timer maakt die om 4:30 uur verloopt, in het onderliggende duurzame taak raamwerk een bericht dat alleen op 4:30 uur zichtbaar wordt. Bij het uitvoeren van het Azure Functions verbruiks abonnement zorgt het nieuw zicht bare timer bericht ervoor dat de functie-app wordt geactiveerd op een geschikte VM.

> [!NOTE]
> * Duurzame timers zijn momenteel beperkt tot 7 dagen. Als er langere vertragingen nodig zijn, kunnen ze worden gesimuleerd met behulp van de timer-Api's in een `while`-lus.
> * Gebruik altijd `CurrentUtcDateTime` in plaats van `DateTime.UtcNow` in .NET of `currentUtcDateTime` in plaats van `Date.now` of `Date.UTC` in Java script bij het berekenen van de brand tijd voor duurzame timers. Zie het artikel [Orchestrator functie code constraints](durable-functions-code-constraints.md) voor meer informatie.

## <a name="usage-for-delay"></a>Gebruik voor vertraging

In het volgende voor beeld ziet u hoe u duurzame timers gebruikt voor het vertragen van de uitvoering. In het voor beeld wordt elke dag 10 dagen een facturerings melding uitgegeven.

### <a name="c"></a>C#

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

> [!NOTE]
> Het vorige C# voor beeld is gericht Durable functions 2. x. Voor Durable Functions 1. x moet u `DurableOrchestrationContext` gebruiken in plaats van `IDurableOrchestrationContext`. Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

### <a name="javascript-functions-20-only"></a>Java script (alleen voor de functies 2,0)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const dayOfMonth = context.df.currentUtcDateTime.getDate();
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```

> [!WARNING]
> Vermijd oneindige lussen in Orchestrator-functies. Zie [eeuwige Orchestrations](durable-functions-eternal-orchestrations.md)(Engelstalig) voor informatie over het veilig en efficiënt implementeren van oneindige lussen scenario's.

## <a name="usage-for-timeout"></a>Gebruik voor time-out

In dit voor beeld ziet u hoe u met duurzame timers time-outs implementeert.

### <a name="c"></a>C#

```csharp
[FunctionName("TryGetQuote")]
public static async Task<bool> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("GetQuote");
        Task timeoutTask = context.CreateTimer(deadline, cts.Token);

        Task winner = await Task.WhenAny(activityTask, timeoutTask);
        if (winner == activityTask)
        {
            // success case
            cts.Cancel();
            return true;
        }
        else
        {
            // timeout case
            return false;
        }
    }
}
```

> [!NOTE]
> Het vorige C# voor beeld is gericht Durable functions 2. x. Voor Durable Functions 1. x moet u `DurableOrchestrationContext` gebruiken in plaats van `IDurableOrchestrationContext`. Zie het artikel [Durable functions versies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

### <a name="javascript-functions-20-only"></a>Java script (alleen voor de functies 2,0)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("GetQuote");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    }
    else
    {
        // timeout case
        return false;
    }
});
```

> [!WARNING]
> Gebruik een `CancellationTokenSource` om een duurzame timer (.NET) te annuleren of `cancel()` aan te roepen op de geretourneerde `TimerTask` (Java script) als uw code niet wacht totdat de bewerking is voltooid. Het duurzame taak raamwerk wijzigt de status van de Orchestrator naar voltooid totdat alle openstaande taken zijn voltooid of geannuleerd.

Met dit annulerings mechanisme wordt de functie voor het uitvoeren van activiteiten of suborchestration-uitvoeringen niet beëindigd. In plaats daarvan kan de Orchestrator-functie het resultaat negeren en verplaatsen. Als uw functie-app gebruikmaakt van het verbruiks abonnement, worden er nog steeds kosten in rekening gebracht voor elk tijdstip en geheugen dat door de functie voor afgebroken activiteit wordt verbruikt. Functies die in het verbruiks abonnement worden uitgevoerd, hebben standaard een time-out van vijf minuten. Als deze limiet wordt overschreden, wordt de Azure Functions-host gerecycled om alle bewerkingen te stoppen en te voor komen dat er een overmatige facturerings situatie is. De [time-out van de functie kan worden geconfigureerd](../functions-host-json.md#functiontimeout).

Voor een uitgebreidere voor beeld van het implementeren van time-outs in Orchestrator-functies raadpleegt u het artikel [Human interactie & time-outs-telefoon verificatie](durable-functions-phone-verification.md) .

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het verhogen en verwerken van externe gebeurtenissen](durable-functions-external-events.md)
