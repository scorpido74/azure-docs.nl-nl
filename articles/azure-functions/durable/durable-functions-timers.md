---
title: Timers in duurzame functies - Azure
description: Meer informatie over het implementeren van duurzame timers in de extensie Duurzame functies voor Azure-functies.
ms.topic: conceptual
ms.date: 11/03/2019
ms.author: azfuncdf
ms.openlocfilehash: 0565cc149a36baf31d8516fffcf48b194c465760
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76261480"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Timers in duurzame functies (Azure-functies)

[Duurzame functies](durable-functions-overview.md) bieden *duurzame timers* voor gebruik in orchestrator-functies om vertragingen te implementeren of time-outs in te stellen voor async-acties. Duurzame timers moeten worden gebruikt in `Thread.Sleep` orchestrator-functies in plaats van en `Task.Delay` (C#), of `setTimeout()` (JavaScript). `setInterval()`

U maakt een duurzame `CreateTimer` timer door de methode `createTimer` (.NET) of de (JavaScript)-methode van de [orchestration trigger binding aan te roepen.](durable-functions-bindings.md#orchestration-trigger) De methode retourneert een taak die is voltooid op een bepaalde datum en tijd.

## <a name="timer-limitations"></a>Timerbeperkingen

Wanneer u een timer maakt die om 16:30 uur afloopt, wordt in het onderliggende kader voor duurzame taken een bericht indekt dat pas om 16:30 uur zichtbaar wordt. Wanneer u het Azure Functions Consumption-abonnement uitvoert, zorgt het nieuw zichtbare timerbericht ervoor dat de functie-app wordt geactiveerd op een geschikte vm.

> [!NOTE]
> * Duurzame timers zijn momenteel beperkt tot 7 dagen. Als er langere vertragingen nodig zijn, kunnen ze worden `while` gesimuleerd met behulp van de timer API's in een lus.
> * Gebruik `CurrentUtcDateTime` altijd `DateTime.UtcNow` in plaats `currentUtcDateTime` van `Date.now` in `Date.UTC` .NET of in plaats van of in JavaScript bij het berekenen van de brandtijd voor duurzame timers. Zie het artikel voor beperkingen van de [orchestrator-functiecode voor](durable-functions-code-constraints.md) meer informatie.

## <a name="usage-for-delay"></a>Gebruik voor vertraging

In het volgende voorbeeld wordt uitgelegd hoe u duurzame timers gebruiken voor het uitstellen van de uitvoering. Het voorbeeld geeft elke dag gedurende 10 dagen een factureringsmelding uit.

# <a name="c"></a>[C #](#tab/csharp)

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
> Het vorige C#-voorbeeld is gericht op duurzame functies 2.x. Voor duurzame functies 1.x `DurableOrchestrationContext` moet `IDurableOrchestrationContext`u in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```js
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    for (let i = 0; i < 10; i++) {
        const deadline = moment.utc(context.df.currentUtcDateTime).add(1, 'd');
        yield context.df.createTimer(deadline.toDate());
        yield context.df.callActivity("SendBillingEvent");
    }
});
```

---

> [!WARNING]
> Vermijd oneindige lussen in orchestrator-functies. Zie [Eternal Orchestrations](durable-functions-eternal-orchestrations.md)voor informatie over het veilig en efficiënt implementeren van oneindige lusscenario's.

## <a name="usage-for-timeout"></a>Gebruik voor time-out

In dit voorbeeld wordt uitgelegd hoe u duurzame timers gebruiken om time-outs te implementeren.

# <a name="c"></a>[C #](#tab/csharp)

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
> Het vorige C#-voorbeeld is gericht op duurzame functies 2.x. Voor duurzame functies 1.x `DurableOrchestrationContext` moet `IDurableOrchestrationContext`u in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

> [!WARNING]
> Gebruik `CancellationTokenSource` een (.NET) `cancel()` of `TimerTask` roep de geretourneerde (JavaScript) aan om een duurzame timer te annuleren als uw code niet wacht tot deze is voltooid. Het framework Duurzame taak wijzigt de status van een orkestratie niet in 'voltooid' totdat alle openstaande taken zijn voltooid of geannuleerd.

Dit annuleringsmechanisme beëindigt de activiteitsfunctie of suborchestration-uitvoeringen niet in uitvoering. Integendeel, het laat gewoon de orchestrator functie om het resultaat te negeren en verder te gaan. Als uw functie-app het verbruiksplan gebruikt, wordt u nog steeds gefactureerd voor alle tijd en geheugen die worden verbruikt door de verlaten activiteitsfunctie. Functies die in het verbruiksplan worden uitgevoerd, hebben standaard een time-out van vijf minuten. Als deze limiet wordt overschreden, wordt de Azure Functions-host gerecycled om alle uitvoering te stoppen en een op hol geslagen factureringssituatie te voorkomen. De [functie-time-out is configureerbaar](../functions-host-json.md#functiontimeout).

Zie het artikel [Human Interaction & Timeouts - Telefoonverificatie](durable-functions-phone-verification.md) voor een diepgaander voorbeeld van het implementeren van time-outs in orchestrator-functies.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het verhogen en verwerken van externe gebeurtenissen](durable-functions-external-events.md)
