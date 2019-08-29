---
title: Timers in Durable Functions-Azure
description: Meer informatie over het implementeren van duurzame timers in de Durable Functions-extensie voor Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/08/2018
ms.author: azfuncdf
ms.openlocfilehash: 11edfc11fc1e54684a99774c21517d4c322348b1
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70087046"
---
# <a name="timers-in-durable-functions-azure-functions"></a>Timers in Durable Functions (Azure Functions)

[Durable functions](durable-functions-overview.md) biedt *duurzame timers* voor gebruik in Orchestrator-functies voor het implementeren van vertragingen of het instellen van time-outs voor asynchrone acties. Duurzame timers moeten worden gebruikt in Orchestrator-functies in plaats `Thread.Sleep` van `Task.Delay` enC#(), `setTimeout()` of `setInterval()` en (Java script).

U maakt een duurzame timer door de methode [CreateTimer](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CreateTimer_) van [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) in .net of de `createTimer` methode van `DurableOrchestrationContext` in Java script aan te roepen. De methode retourneert een taak die op een opgegeven datum en tijd wordt hervat.

## <a name="timer-limitations"></a>Beperkingen van de timer

Wanneer u een timer maakt die om 4:30 uur verloopt, in het onderliggende duurzame taak raamwerk een bericht dat alleen op 4:30 uur zichtbaar wordt. Bij het uitvoeren van het Azure Functions verbruiks abonnement zorgt het nieuw zicht bare timer bericht ervoor dat de functie-app wordt geactiveerd op een geschikte VM.

> [!NOTE]
> * Duurzame timers kunnen niet langer duren dan zeven dagen vanwege beperkingen in Azure Storage. We werken aan een [functie aanvraag om timers langer dan zeven dagen uit te breiden](https://github.com/Azure/azure-functions-durable-extension/issues/14).
> * Gebruik altijd [CurrentUtcDateTime](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_CurrentUtcDateTime) in plaats `DateTime.UtcNow` van in .net `currentUtcDateTime` en in `Date.now` plaats `Date.UTC` van of in Java script, zoals wordt weer gegeven in de onderstaande voor beelden bij het berekenen van een relatieve deadline van een duurzame timer.

## <a name="usage-for-delay"></a>Gebruik voor vertraging

In het volgende voor beeld ziet u hoe u duurzame timers gebruikt voor het vertragen van de uitvoering. In het voor beeld wordt elke dag gedurende tien dagen een facturerings melding uitgegeven.

### <a name="c"></a>C#

```csharp
[FunctionName("BillingIssuer")]
public static async Task Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    for (int i = 0; i < 10; i++)
    {
        DateTime deadline = context.CurrentUtcDateTime.Add(TimeSpan.FromDays(1));
        await context.CreateTimer(deadline, CancellationToken.None);
        await context.CallActivityAsync("SendBillingEvent");
    }
}
```

### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

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
    [OrchestrationTrigger] DurableOrchestrationContext context)
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

### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

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
> Gebruik a `CancellationTokenSource` om een duurzame timer (C#) te annuleren of `cancel()` een aanroep uit `TimerTask` te voeren op de geretourneerde (Java script) als uw code niet wacht totdat de bewerking is voltooid. Het duurzame taak raamwerk wijzigt de status van de Orchestrator naar voltooid totdat alle openstaande taken zijn voltooid of geannuleerd.

Met dit mechanisme wordt de uitvoering van de uitgevoerde activiteiten functie niet daad werkelijk afgesloten. In plaats daarvan kan de Orchestrator-functie het resultaat negeren en verplaatsen. Als uw functie-app gebruikmaakt van het verbruiks abonnement, worden er nog steeds kosten in rekening gebracht voor elk tijdstip en geheugen dat door de functie voor afgebroken activiteit wordt verbruikt. Functies die in het verbruiks abonnement worden uitgevoerd, hebben standaard een time-out van vijf minuten. Als deze limiet wordt overschreden, wordt de Azure Functions-host gerecycled om alle bewerkingen te stoppen en te voor komen dat er een overmatige facturerings situatie is. De [time-out van de functie kan worden geconfigureerd](../functions-host-json.md#functiontimeout).

Voor een uitgebreidere voor beeld van hoe u time-outs implementeert in Orchestrator-functies, raadpleegt u het scenario voor de [verificatie van Human Interacties &-telefonische authenticatie](durable-functions-phone-verification.md) .

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het verhogen en verwerken van externe gebeurtenissen](durable-functions-external-events.md)
