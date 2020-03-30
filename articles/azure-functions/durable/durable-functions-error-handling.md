---
title: Fouten in duurzame functies verwerken - Azure
description: Meer informatie over het verwerken van fouten in de extensie Duurzame functies voor Azure-functies.
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 447b3dcf5040835f5a853beff68bde794ece51f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277854"
---
# <a name="handling-errors-in-durable-functions-azure-functions"></a>Fouten in duurzame functies verwerken (Azure-functies)

Orkestraties voor duurzame functies worden in code geïmplementeerd en kunnen de ingebouwde functies voor foutafhandeling van de programmeertaal gebruiken. Er zijn echt geen nieuwe concepten die je moet leren om foutafhandeling en compensatie toe te voegen aan je orkestraties. Er zijn echter een paar gedragingen waar u zich bewust van moet zijn.

## <a name="errors-in-activity-functions"></a>Fouten in activiteitsfuncties

Elke uitzondering die wordt gegooid in een activiteit functie wordt teruggemarshald naar de orchestrator functie en gegooid als een `FunctionFailedException`. U foutafhandeling en compensatiecode schrijven die bij uw behoeften past in de orchestrator-functie.

Denk bijvoorbeeld aan de volgende orchestrator-functie die geld overmaakt van de ene rekening naar de andere:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TransferFunds")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var transferDetails = context.GetInput<TransferOperation>();

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

> [!NOTE]
> De vorige C# voorbeelden zijn voor Duurzame Functies 2.x. Voor duurzame functies 1.x `DurableOrchestrationContext` moet `IDurableOrchestrationContext`u in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

Als de eerste **Functieaanroep creditering** mislukt, compenseert de orchestrator-functie door het geld terug te schrijven naar de bronrekening.

## <a name="automatic-retry-on-failure"></a>Automatisch opnieuw proberen bij uitval

Wanneer u activiteitsfuncties of suborchestration-functies aanroept, u een automatisch beleid voor nieuwe gegevens uitvoeren. In het volgende voorbeeld wordt geprobeerd een functie tot drie keer aan te roepen en wordt 5 seconden tussen elke nieuwe poging gewacht:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TimerOrchestratorWithRetry")]
public static async Task Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var retryOptions = new RetryOptions(
        firstRetryInterval: TimeSpan.FromSeconds(5),
        maxNumberOfAttempts: 3);

    await context.CallActivityWithRetryAsync("FlakyFunction", retryOptions, null);

    // ...
}
```

> [!NOTE]
> De vorige C# voorbeelden zijn voor Duurzame Functies 2.x. Voor duurzame functies 1.x `DurableOrchestrationContext` moet `IDurableOrchestrationContext`u in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const firstRetryIntervalInMilliseconds = 5000;
    const maxNumberOfAttempts = 3;

    const retryOptions = 
        new df.RetryOptions(firstRetryIntervalInMilliseconds, maxNumberOfAttempts);

    yield context.df.callActivityWithRetry("FlakyFunction", retryOptions);

    // ...
});
```

---

De aanroep met de activiteitsfunctie in het vorige voorbeeld neemt een parameter voor het configureren van een automatisch beleid voor opnieuw proberen. Er zijn verschillende opties voor het aanpassen van het beleid voor automatische opnieuw proberen:

* **Maximum aantal pogingen**: Het maximum aantal pogingen om opnieuw te proberen.
* **Eerste retry interval:** De hoeveelheid tijd om te wachten voordat de eerste poging opnieuw proberen.
* **Terug-afschakelcoëfficiënt**: De coëfficiënt die wordt gebruikt om de stijgingsgraad van de back-off te bepalen. Standaard op 1.
* **Interval voor het opnieuw proberen:** de maximale hoeveelheid tijd om tussen pogingen opnieuw te proberen.
* **Time-out opnieuw proberen:** de maximale hoeveelheid tijd die u moet besteden aan het doen van nieuwe pogingen. Het standaardgedrag is om het voor onbepaalde tijd opnieuw te proberen.
* **Greep:** Een door de gebruiker gedefinieerde callback kan worden opgegeven om te bepalen of een functie opnieuw moet worden geprobeerd.

## <a name="function-timeouts"></a>Time-outs van functies

U een functieaanroep binnen een orchestrator-functie opgeven als het te lang duurt om deze te voltooien. De juiste manier om dit vandaag te `context.CreateTimer` doen is door `context.df.createTimer` het creëren van `Task.WhenAny` een [duurzame](durable-functions-timers.md) timer `context.df.Task.any` met behulp van (.NET) of (JavaScript) in combinatie met (.NET) of (JavaScript), zoals in het volgende voorbeeld:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("TimerOrchestrator")]
public static async Task<bool> Run([OrchestrationTrigger] IDurableOrchestrationContext context)
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

> [!NOTE]
> De vorige C# voorbeelden zijn voor Duurzame Functies 2.x. Voor duurzame functies 1.x `DurableOrchestrationContext` moet `IDurableOrchestrationContext`u in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

> [!NOTE]
> Dit mechanisme beëindigt niet daadwerkelijk de uitvoering van de activiteitsfunctie in uitvoering. Integendeel, het laat gewoon de orchestrator functie om het resultaat te negeren en verder te gaan. Zie voor meer informatie de documentatie [Timers.](durable-functions-timers.md#usage-for-timeout)

## <a name="unhandled-exceptions"></a>Onverwerkte uitzonderingen

Als een orchestrator-functie mislukt met een niet-behandelde uitzondering, worden de `Failed` details van de uitzondering geregistreerd en wordt de instantie voltooid met een status.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over eeuwige orkestraties](durable-functions-eternal-orchestrations.md)

> [!div class="nextstepaction"]
> [Meer informatie over het diagnosticeren van problemen](durable-functions-diagnostics.md)
