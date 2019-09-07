---
title: Fouten afhandelen in Durable Functions-Azure
description: Meer informatie over het afhandelen van fouten in de Durable Functions extensie voor Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 7b357189a9ce67f27952985b78dd3134517ffba5
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734308"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Het afhandelen van fouten in Durable Functions (Azure Functions)

Duurzame functie-indelingen worden in code geïmplementeerd en kunnen de functies voor fout afhandeling van de programmeer taal gebruiken. Daarom zijn er echt geen nieuwe concepten die u nodig hebt om meer te weten te komen over het opnemen van fout afhandeling en compensatie in uw integraties. Er zijn echter enkele gedragingen waarmee u rekening moet houden.

## <a name="errors-in-activity-functions"></a>Fouten in de activiteit functies

Uitzonde ringen die worden gegenereerd in een functie van een activiteit, worden teruggeleid naar de Orchestrator-functie en `FunctionFailedException`gegenereerd als een. U kunt fout afhandeling en compensatie code schrijven die aan uw behoeften voldoen in de Orchestrator-functie.

Denk bijvoorbeeld aan de volgende Orchestrator-functie waarmee u fondsen van het ene naar het andere account overbrengt:

### <a name="precompiled-c"></a>Vooraf gecompileerdeC#

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    var transferDetails = ctx.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

### <a name="c-script"></a>C# Script

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static async Task Run(DurableOrchestrationContext context)
{
    var transferDetails = ctx.GetInput<TransferOperation>();

    await context.CallActivityAsync("DebitAccount",
        new
        {
            Account = transferDetails.SourceAccount,
            Amount = transferDetails.Amount
        });

    try
    {
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.DestinationAccount,
                Amount = transferDetails.Amount
            });
    }
    catch (Exception)
    {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        await context.CallActivityAsync("CreditAccount",
            new
            {
                Account = transferDetails.SourceAccount,
                Amount = transferDetails.Amount
            });
    }
}
```

### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const transferDetails = context.df.getInput();

    yield context.df.callActivity("DebitAccount",
        {
            account = transferDetails.sourceAccount,
            amount = transferDetails.amount,
        }
    );

    try {
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.destinationAccount,
                amount = transferDetails.amount,
            }
        );
    }
    catch (error) {
        // Refund the source account.
        // Another try/catch could be used here based on the needs of the application.
        yield context.df.callActivity("CreditAccount",
            {
                account = transferDetails.sourceAccount,
                amount = transferDetails.amount,
            }
        );
    }
});
```

Als de aanroep van de functie **CreditAccount** mislukt voor het doel account, compenseert de Orchestrator-functie dit door de tegoeden terug te sturen naar het bron account.

## <a name="automatic-retry-on-failure"></a>Automatische nieuwe poging bij fout

Wanneer u activiteit functies of suborchestration-functies aanroept, kunt u een beleid voor automatische nieuwe pogingen opgeven. In het volgende voor beeld wordt geprobeerd een functie Maxi maal drie keer aan te roepen en wordt vijf seconden gewacht tussen elke nieuwe poging:

### <a name="precompiled-c"></a>Vooraf gecompileerdeC#

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

### <a name="c-script"></a>C# Script

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await ctx.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const retryOptions = new df.RetryOptions(5000, 3);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

De `CallActivityWithRetryAsync` API (.net) `callActivityWithRetry` of (Java script) gebruikt `RetryOptions` een para meter. Suborchestrator-aanroepen met `CallSubOrchestratorWithRetryAsync` behulp van de `callSubOrchestratorWithRetry` API (.net) of (Java script) kunnen gebruikmaken van dezelfde beleids regels voor opnieuw proberen.

Er zijn verschillende opties voor het aanpassen van het beleid voor automatische opnieuw proberen. Hieronder vallen de volgende landen/regio's:

* **Maximum aantal pogingen**: Het maximum aantal nieuwe pogingen.
* **Interval voor eerste poging**: De hoeveelheid tijd die moet worden gewacht voordat de eerste nieuwe poging wordt gedaan.
* **Uitstel coëfficiënt**: De coëfficiënt die wordt gebruikt om de frequentie van de toename van uitstel te bepalen. De standaard waarde is 1.
* **Maximum interval voor opnieuw proberen**: De maximale wacht tijd tussen nieuwe pogingen.
* **Time-out voor opnieuw proberen**: De maximale hoeveelheid tijd die nodig is om nieuwe pogingen uit te voeren. Het standaard gedrag is om voor onbepaalde tijd opnieuw te proberen.
* **Greep**: Een door de gebruiker gedefinieerde call back kan worden opgegeven, waarmee wordt bepaald of een functie aanroep opnieuw moet worden geprobeerd.

## <a name="function-timeouts"></a>Time-outs van functies

Het kan zijn dat u een functie aanroep binnen een Orchestrator-functie wilt verlaten als het te lang duurt om te volt ooien. De juiste manier om dit te doen is door een [duurzame timer](durable-functions-timers.md) te maken `context.CreateTimer` met (.net) `context.df.createTimer` of (Java script) in `Task.WhenAny` combi natie met ( `context.df.Task.any` .net) of (Java script), zoals in het volgende voor beeld:

### <a name="precompiled-c"></a>Vooraf gecompileerdeC#

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
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

### <a name="c-script"></a>C# Script

```csharp
public static async Task<bool> Run(DurableOrchestrationContext context)
{
    TimeSpan timeout = TimeSpan.FromSeconds(30);
    DateTime deadline = context.CurrentUtcDateTime.Add(timeout);

    using (var cts = new CancellationTokenSource())
    {
        Task activityTask = context.CallActivityAsync("FlakyFunction");
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

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const deadline = moment.utc(context.df.currentUtcDateTime).add(30, "s");

    const activityTask = context.df.callActivity("FlakyFunction");
    const timeoutTask = context.df.createTimer(deadline.toDate());

    const winner = yield context.df.Task.any([activityTask, timeoutTask]);
    if (winner === activityTask) {
        // success case
        timeoutTask.cancel();
        return true;
    } else {
        // timeout case
        return false;
    }
});
```

> [!NOTE]
> Met dit mechanisme wordt de uitvoering van de uitgevoerde activiteiten functie niet daad werkelijk afgesloten. In plaats daarvan kan de Orchestrator-functie het resultaat negeren en verplaatsen. Zie de [time](durable-functions-timers.md#usage-for-timeout) -outdocumentatie voor meer informatie.

## <a name="unhandled-exceptions"></a>Onverwerkte uitzonderingen

Als een Orchestrator-functie mislukt met een niet-verwerkte uitzonde ring, worden de details van de uitzonde ring vastgelegd en `Failed` wordt het exemplaar voltooid met een status.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over het vaststellen van problemen](durable-functions-diagnostics.md)