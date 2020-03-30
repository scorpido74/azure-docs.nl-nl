---
title: Overzicht van Durable Functions - Azure
description: Inleiding tot de extensie Durable Functions voor Azure Functions.
author: cgillum
ms.topic: overview
ms.date: 08/07/2019
ms.author: cgillum
ms.reviewer: azfuncdf
ms.openlocfilehash: 5d454aefaba89bef9dc9009ff442fa5543dae2ef
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241344"
---
# <a name="what-are-durable-functions"></a>Wat is Durable Functions?

*Duurzame functies* is een uitbreiding van [Azure-functies](../functions-overview.md) waarmee u stateful-functies schrijven in een serverloze compute-omgeving. Met de extensie u stateful workflows definiëren door [*orchestrator-functies*](durable-functions-orchestrations.md) en stateful entiteiten te schrijven door [*entiteitsfuncties*](durable-functions-entities.md) te schrijven met behulp van het programmeermodel Azure Functions. Achter de schermen beheert de extensie status, controlepunten en opnieuw opstarten, zodat u zich concentreren op uw bedrijfslogica.

## <a name="supported-languages"></a><a name="language-support"></a>Ondersteunde talen

Durable Functions ondersteunt momenteel de volgende talen:

* **C#**: zowel [vooraf gecompileerde klassebibliotheken](../functions-dotnet-class-library.md) als [C#-script](../functions-reference-csharp.md).
* **JavaScript**: alleen ondersteund voor versie 2.x van de Azure Functions-runtime. Versie 1.7.0 of hoger van de Durable Functions-extensie vereist. 
* **F#**: vooraf gecompileerde klassebibliotheken en F#-script. F#-script wordt alleen ondersteund voor versie 1.x van de Azure Functions-runtime.

Durable Functions heeft als doel alle [Azure Functions-talen](../supported-languages.md) te ondersteunen. Zie [Durable Functions issues list](https://github.com/Azure/azure-functions-durable-extension/issues) voor de laatste voortgangsstatus van de ondersteuning voor meer talen.

Net als Azure-functies zijn er sjablonen om u te helpen duurzame functies te ontwikkelen met behulp van [Visual Studio 2019,](durable-functions-create-first-csharp.md) [Visual Studio Code](quickstart-js-vscode.md)en de [Azure-portal.](durable-functions-create-portal.md)

## <a name="application-patterns"></a>Toepassingspatronen

De primaire use case voor Durable Functions is het vereenvoudigen van complexe stateful coördinatievereisten in serverloze toepassingen. In de volgende secties worden typische toepassingspatronen beschreven die kunnen profiteren van duurzame functies:

* [Functiekoppeling](#chaining)
* [Uit-/inwaaieren](#fan-in-out)
* [Asynchrone HTTP-API's](#async-http)
* [Monitoring](#monitoring)
* [Menselijke tussenkomst](#human)
* [Aggregator (stateful entiteiten)](#aggregator)

### <a name="pattern-1-function-chaining"></a><a name="chaining"></a>Patroon #1: Functiechaining

In het functieketenpatroon wordt een reeks functies in een specifieke volgorde uitgevoerd. In dit patroon wordt de uitvoer van de ene functie toegepast op de invoer van een andere functie.

![Een diagram van het functieketenpatroon](./media/durable-functions-concepts/function-chaining.png)

U Duurzame functies gebruiken om het functieketenpatroon beknopt te implementeren, zoals in het volgende voorbeeld wordt weergegeven.

In dit voorbeeld `F1`worden `F2` `F3`de `F4` waarden , en zijn de namen van andere functies in dezelfde functie-app. U de besturingsstroom implementeren met behulp van normale verplichte coderingsconstructies. Code wordt van boven naar beneden uitgevoerd. De code kan bestaan uit bestaande taalcontrolestroomsemantiek, zoals conditionals en loops. U logica voor `try` / `catch` / `finally` foutafhandeling in blokken opnemen.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("Chaining")]
public static async Task<object> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1", null);
        var y = await context.CallActivityAsync<object>("F2", x);
        var z = await context.CallActivityAsync<object>("F3", y);
        return  await context.CallActivityAsync<object>("F4", z);
    }
    catch (Exception)
    {
        // Error handling or compensation goes here.
    }
}
```

U `context` de parameter gebruiken om andere functies aan te roepen op naam, doorgeefparameters en uitvoer van de retourfunctie. Telkens wanneer de `await`code aanroept, controleert het framework Duurzame functies de voortgang van de huidige functie-instantie. Als het proces of de virtuele machine halverwege de uitvoering wordt `await` gerecycled, wordt de functie-instantie hervat vanaf de vorige aanroep. Zie voor meer informatie de volgende sectie, Pattern #2: Fan out/fan in.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    try {
        const x = yield context.df.callActivity("F1");
        const y = yield context.df.callActivity("F2", x);
        const z = yield context.df.callActivity("F3", y);
        return    yield context.df.callActivity("F4", z);
    } catch (error) {
        // Error handling or compensation goes here.
    }
});
```

U `context.df` het object gebruiken om andere functies aan te roepen op naam, doorgeefparameters en uitvoer van de retourfunctie. Telkens wanneer de `yield`code aanroept, controleert het framework Duurzame functies de voortgang van de huidige functie-instantie. Als het proces of de virtuele machine halverwege de uitvoering wordt `yield` gerecycled, wordt de functie-instantie hervat vanaf de vorige aanroep. Zie voor meer informatie de volgende sectie, Pattern #2: Fan out/fan in.

> [!NOTE]
> Het `context` object in JavaScript vertegenwoordigt de volledige [functiecontext](../functions-reference-node.md#context-object). Toegang tot de context `df` Duurzame functies met behulp van de eigenschap op de hoofdcontext.

---

### <a name="pattern-2-fan-outfan-in"></a><a name="fan-in-out"></a>Patroon #2: Uitwaaieren/ventilator binnen

In de ventilator uit/ventilator in patroon voert u meerdere functies parallel uit en wacht u tot alle functies zijn voltooid. Vaak wordt er wat aggregatiewerk gedaan aan de resultaten die worden geretourneerd uit de functies.

![Een diagram van het ventilatoruit/ventilatorpatroon](./media/durable-functions-concepts/fan-out-fan-in.png)

Met normale functies u uitwaaieren door de functie meerdere berichten naar een wachtrij te laten verzenden. Fanning terug in is veel uitdagender. Als u wilt inwaaieren, schrijft u in een normale functie code om bij te houden wanneer de door de wachtrij geactiveerde functies eindigen en vervolgens functie-uitvoer op te slaan.

De extensie Duurzame functies behandelt dit patroon met relatief eenvoudige code:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("FanOutFanIn")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1", null);
    for (int i = 0; i < workBatch.Length; i++)
    {
        Task<int> task = context.CallActivityAsync<int>("F2", workBatch[i]);
        parallelTasks.Add(task);
    }

    await Task.WhenAll(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    int sum = parallelTasks.Sum(t => t.Result);
    await context.CallActivityAsync("F3", sum);
}
```

Het uitwaaierwerk wordt verdeeld over meerdere `F2` exemplaren van de functie. Het werk wordt bijgehouden met behulp van een dynamische lijst met taken. `Task.WhenAll`wordt opgeroepen om te wachten tot alle zogenaamde functies zijn voltooid. Vervolgens worden `F2` de functie-uitgangen samengevoegd vanuit de dynamische `F3` takenlijst en doorgegeven aan de functie.

De automatische controlecontrole die `await` plaatsvindt `Task.WhenAll` bij de aanroep zorgt ervoor dat een potentiële halverwege crash of reboot niet vereist dat een reeds voltooide taak opnieuw wordt opgestart.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const parallelTasks = [];

    // Get a list of N work items to process in parallel.
    const workBatch = yield context.df.callActivity("F1");
    for (let i = 0; i < workBatch.length; i++) {
        parallelTasks.push(context.df.callActivity("F2", workBatch[i]));
    }

    yield context.df.Task.all(parallelTasks);

    // Aggregate all N outputs and send the result to F3.
    const sum = parallelTasks.reduce((prev, curr) => prev + curr, 0);
    yield context.df.callActivity("F3", sum);
});
```

Het uitwaaierwerk wordt verdeeld over meerdere `F2` exemplaren van de functie. Het werk wordt bijgehouden met behulp van een dynamische lijst met taken. `context.df.Task.all`API is geroepen om te wachten tot alle aangeroepen functies zijn voltooid. Vervolgens worden `F2` de functie-uitgangen samengevoegd vanuit de dynamische `F3` takenlijst en doorgegeven aan de functie.

De automatische controlecontrole die `yield` plaatsvindt `context.df.Task.all` bij de aanroep zorgt ervoor dat een potentiële halverwege crash of reboot niet vereist dat een reeds voltooide taak opnieuw wordt opgestart.

---

> [!NOTE]
> In zeldzame omstandigheden is het mogelijk dat een crash in het venster kan plaatsvinden nadat een activiteitsfunctie is voltooid, maar voordat de voltooiing ervan is opgeslagen in de orkestratiegeschiedenis. Als dit gebeurt, wordt de activiteitsfunctie opnieuw uitgevoerd vanaf het begin nadat het proces is hersteld.

### <a name="pattern-3-async-http-apis"></a><a name="async-http"></a>Patroon #3: Async HTTP API's

Het async HTTP API-patroon lost het probleem op van het coördineren van de status van langlopende bewerkingen met externe clients. Een veelvoorkomende manier om dit patroon te implementeren is door een HTTP-eindpunt de langlopende actie te laten activeren. Verwijs de client vervolgens door naar een statuseindpunt dat de client peilt om te leren wanneer de bewerking is voltooid.

![Een diagram van het HTTP API-patroon](./media/durable-functions-concepts/async-http-api.png)

Duurzame functies bieden **ingebouwde ondersteuning** voor dit patroon, waardoor de code die u moet schrijven om te communiceren met langlopende functie-uitvoeringen wordt vereenvoudigd of zelfs verwijderd. De snelstartvoorbeelden voor duurzame functies[(C#](durable-functions-create-first-csharp.md) en [JavaScript)](quickstart-js-vscode.md)tonen bijvoorbeeld een eenvoudige REST-opdracht die u gebruiken om nieuwe instanties voor orchestrator-functies te starten. Nadat een instantie is gestart, worden webhook HTTP-API's weergegeven die de status van de orchestrator-functie opvragen. 

In het volgende voorbeeld worden REST-opdrachten weergegeven die een orchestrator starten en de status ervan opvragen. Voor de duidelijkheid, sommige protocoldetails worden weggelaten uit het voorbeeld.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2019-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/runtime/webhooks/durabletask/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2019-03-16T21:20:57Z", ...}
```

Omdat de runtime duurzame functies de status voor u beheert, hoeft u uw eigen mechanisme voor statustracking niet te implementeren.

De extensie Duurzame functies legt ingebouwde HTTP-API's bloot die langdurige orkestraties beheren. U dit patroon ook zelf implementeren door uw eigen functietriggers (zoals HTTP, een wachtrij of Azure Event Hubs) en de [orchestration-clientbinding te gebruiken.](durable-functions-bindings.md#orchestration-client) U bijvoorbeeld een wachtrijbericht gebruiken om beëindiging te activeren. U ook een HTTP-trigger gebruiken die is beveiligd door een Azure Active Directory-verificatiebeleid in plaats van de ingebouwde HTTP-API's die een gegenereerde sleutel gebruiken voor verificatie.

Zie voor meer informatie het [http-functiesartikel,](durable-functions-http-features.md) waarin wordt uitgelegd hoe u asynchrone, langlopende processen via HTTP blootleggen met de extensie Duurzame functies.

### <a name="pattern-4-monitor"></a><a name="monitoring"></a>Patroon #4: Monitor

Het monitorpatroon verwijst naar een flexibel, terugkerend proces in een workflow. Een voorbeeld is polling totdat aan specifieke voorwaarden is voldaan. U een normale [timertrigger](../functions-bindings-timer.md) gebruiken om een basisscenario aan te pakken, zoals een periodieke opschoningstaak, maar het interval is statisch en het beheren van instantielevensduur wordt complex. U Duurzame functies gebruiken om flexibele herhalingsintervallen te maken, taaklevensduur te beheren en meerdere monitorprocessen te maken vanuit één orkestratie.

Een voorbeeld van het monitorpatroon is het omkeren van het eerdere async HTTP API-scenario. In plaats van een eindpunt bloot te stellen voor een externe client om een langdurige bewerking te controleren, verbruikt de langlopende monitor een extern eindpunt en wacht vervolgens op een statuswijziging.

![Een diagram van het monitorpatroon](./media/durable-functions-concepts/monitor.png)

In een paar regels code u duurzame functies gebruiken om meerdere monitoren te maken die willekeurige eindpunten observeren. De monitoren kunnen de uitvoering beëindigen wanneer aan een voorwaarde is voldaan, of een andere functie kan de duurzame orchestration-client gebruiken om de monitoren te beëindigen. U het interval `wait` van een monitor wijzigen op basis van een specifieke voorwaarde (bijvoorbeeld exponentiële backoff.) 

De volgende code implementeert een basismonitor:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("MonitorJobStatus")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    int jobId = context.GetInput<int>();
    int pollingInterval = GetPollingInterval();
    DateTime expiryTime = GetExpiryTime();

    while (context.CurrentUtcDateTime < expiryTime)
    {
        var jobStatus = await context.CallActivityAsync<string>("GetJobStatus", jobId);
        if (jobStatus == "Completed")
        {
            // Perform an action when a condition is met.
            await context.CallActivityAsync("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        var nextCheck = context.CurrentUtcDateTime.AddSeconds(pollingInterval);
        await context.CreateTimer(nextCheck, CancellationToken.None);
    }

    // Perform more work here, or let the orchestration end.
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInterval = getPollingInterval();
    const expiryTime = getExpiryTime();

    while (moment.utc(context.df.currentUtcDateTime).isBefore(expiryTime)) {
        const jobStatus = yield context.df.callActivity("GetJobStatus", jobId);
        if (jobStatus === "Completed") {
            // Perform an action when a condition is met.
            yield context.df.callActivity("SendAlert", machineId);
            break;
        }

        // Orchestration sleeps until this time.
        const nextCheck = moment.utc(context.df.currentUtcDateTime).add(pollingInterval, 's');
        yield context.df.createTimer(nextCheck.toDate());
    }

    // Perform more work here, or let the orchestration end.
});
```

---

Wanneer een aanvraag wordt ontvangen, wordt een nieuwe orchestration-instantie gemaakt voor die taak-id. De instantie peilt een status totdat aan een voorwaarde is voldaan en de lus wordt verlaten. Een duurzame timer regelt het polling-interval. Dan kan meer werk worden uitgevoerd, of de orkestratie kan eindigen. Wanneer `nextCheck` `expiryTime`groter is , eindigt de monitor.

### <a name="pattern-5-human-interaction"></a><a name="human"></a>Patroon #5: Menselijke interactie

Veel geautomatiseerde processen betrekken een soort van menselijke interactie. Het betrekken van mensen bij een geautomatiseerd proces is lastig omdat mensen niet zo zeer beschikbaar en zo responsief zijn als cloudservices. Een geautomatiseerd proces kan deze interactie mogelijk maken door time-outs en compensatielogica te gebruiken.

Een goedkeuringsproces is een voorbeeld van een bedrijfsproces waarbij menselijke interactie betrokken is. Goedkeuring van een manager kan nodig zijn voor een onkostenrapport dat een bepaald bedrag in dollars overschrijdt. Als de manager het onkostenrapport niet binnen 72 uur goedkeurt (misschien ging de manager op vakantie), een escalatieproces om de goedkeuring van iemand anders (misschien de manager van de manager) te krijgen.

![Een diagram van het menselijke interactiepatroon](./media/durable-functions-concepts/approval.png)

U het patroon in dit voorbeeld implementeren met behulp van een orchestrator-functie. De orchestrator gebruikt een [duurzame timer](durable-functions-timers.md) om goedkeuring aan te vragen. De orchestrator escaleert als er een time-out optreedt. De orchestrator wacht op een [externe gebeurtenis,](durable-functions-external-events.md)zoals een melding die wordt gegenereerd door een menselijke interactie.

Met deze voorbeelden wordt een goedkeuringsproces gemaakt om het menselijke interactiepatroon aan te tonen:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("ApprovalWorkflow")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval", null);
    using (var timeoutCts = new CancellationTokenSource())
    {
        DateTime dueTime = context.CurrentUtcDateTime.AddHours(72);
        Task durableTimeout = context.CreateTimer(dueTime, timeoutCts.Token);

        Task<bool> approvalEvent = context.WaitForExternalEvent<bool>("ApprovalEvent");
        if (approvalEvent == await Task.WhenAny(approvalEvent, durableTimeout))
        {
            timeoutCts.Cancel();
            await context.CallActivityAsync("ProcessApproval", approvalEvent.Result);
        }
        else
        {
            await context.CallActivityAsync("Escalate", null);
        }
    }
}
```

Als u de duurzame `context.CreateTimer`timer wilt maken, belt u. De melding wordt `context.WaitForExternalEvent`ontvangen door . Vervolgens `Task.WhenAny` wordt opgeroepen om te beslissen of te escaleren (time-out gebeurt eerst) of het verwerken van de goedkeuring (de goedkeuring wordt ontvangen voordat time-out).

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");
const moment = require('moment');

module.exports = df.orchestrator(function*(context) {
    yield context.df.callActivity("RequestApproval");

    const dueTime = moment.utc(context.df.currentUtcDateTime).add(72, 'h');
    const durableTimeout = context.df.createTimer(dueTime.toDate());

    const approvalEvent = context.df.waitForExternalEvent("ApprovalEvent");
    if (approvalEvent === yield context.df.Task.any([approvalEvent, durableTimeout])) {
        durableTimeout.cancel();
        yield context.df.callActivity("ProcessApproval", approvalEvent.result);
    } else {
        yield context.df.callActivity("Escalate");
    }
});
```

Als u de duurzame `context.df.createTimer`timer wilt maken, belt u. De melding wordt `context.df.waitForExternalEvent`ontvangen door . Vervolgens `context.df.Task.any` wordt opgeroepen om te beslissen of te escaleren (time-out gebeurt eerst) of het verwerken van de goedkeuring (de goedkeuring wordt ontvangen voordat time-out).

---

Een externe client kan de gebeurtenismelding leveren aan een wachtorkestorfunctie met behulp van de [ingebouwde HTTP-API's:](durable-functions-http-api.md#raise-event)

```bash
curl -d "true" http://localhost:7071/runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/ApprovalEvent -H "Content-Type: application/json"
```

Een gebeurtenis kan ook worden verhoogd met behulp van de duurzame orchestration client van een andere functie in dezelfde functie-app:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("RaiseEventToOrchestration")]
public static async Task Run(
    [HttpTrigger] string instanceId,
    [DurableClient] IDurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

---

### <a name="pattern-6-aggregator-stateful-entities"></a><a name="aggregator"></a>Patroon #6: Aggregator (stateful entiteiten)

Het zesde patroon gaat over het samenvoegen van gebeurtenisgegevens over een bepaalde periode in één enkele, adresseerbare *entiteit*. In dit patroon kunnen de gegevens die worden samengevoegd uit meerdere bronnen afkomstig zijn, in batches worden geleverd of over lange perioden worden verspreid. Mogelijk moet de aggregator actie ondernemen op gebeurtenisgegevens zodra deze binnenkomen en moeten externe clients mogelijk de geaggregeerde gegevens opvragen.

![Aggregator-diagram](./media/durable-functions-concepts/aggregator.png)

Het lastige van het proberen om dit patroon met normale, stateless functies uit te voeren is dat gelijktijdigheidscontrole een reusachtige uitdaging wordt. Niet alleen hoeft u zich zorgen te maken over meerdere threads die dezelfde gegevens tegelijkertijd wijzigen, u moet zich ook zorgen maken dat de aggregator alleen op één VM tegelijk wordt uitgevoerd.

U [duurzame entiteiten](durable-functions-entities.md) gebruiken om dit patroon eenvoudig als één functie te implementeren.

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            ctx.SetState(currentValue + amount);
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }
}
```

Duurzame entiteiten kunnen ook worden gemodelleerd als klassen in .NET. Dit model kan handig zijn als de lijst met bewerkingen is opgelost en groot wordt. Het volgende voorbeeld is een `Counter` gelijkwaardige implementatie van de entiteit met behulp van .NET-klassen en -methoden.

```csharp
public class Counter
{
    [JsonProperty("value")]
    public int CurrentValue { get; set; }

    public void Add(int amount) => this.CurrentValue += amount;

    public void Reset() => this.CurrentValue = 0;

    public int Get() => this.CurrentValue;

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.entity(function(context) {
    const currentValue = context.df.getState(() => 0);
    switch (context.df.operationName) {
        case "add":
            const amount = context.df.getInput();
            context.df.setState(currentValue + amount);
            break;
        case "reset":
            context.df.setState(0);
            break;
        case "get":
            context.df.return(currentValue);
            break;
    }
});
```

---

Clients kunnen *bewerkingen* in de wachtrij plaatsen voor (ook wel 'signalering' genoemd) een entiteitsfunctie met behulp van de [entiteitsclientbinding](durable-functions-bindings.md#entity-client).

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [DurableClient] IDurableOrchestrationClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

> [!NOTE]
> Dynamisch gegenereerde proxy's zijn ook beschikbaar in .NET voor het signaleren van entiteiten op een typeveilige manier. En naast signalering kunnen clients ook vragen naar de status van een [entiteitsfunctie](durable-functions-bindings.md#entity-client-usage) met behulp van typeveilige methoden op de orchestration clientbinding.

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

---

Entiteitsfuncties zijn beschikbaar in [Duurzame functies 2.0](durable-functions-versions.md) en hoger.

## <a name="the-technology"></a>De technologie

Achter de schermen is de extensie Duurzame functies gebouwd bovenop het [Sustainable Task Framework](https://github.com/Azure/durabletask), een open-source bibliotheek op GitHub die wordt gebruikt om workflows in code te bouwen. Net als Azure Functions is de serverloze evolutie van Azure WebJobs, duurzame functies is de serverless evolutie van de Duurzame Taak Framework. Microsoft en andere organisaties gebruiken het Sustainable Task Framework uitgebreid om bedrijfskritieke processen te automatiseren. Het is een natuurlijke pasvorm voor de serverloze Azure Functions-omgeving.

## <a name="code-constraints"></a>Codebeperkingen

Om betrouwbare en langlopende uitvoeringsgaranties te bieden, hebben orchestrator-functies een reeks coderingsregels die moeten worden gevolgd. Zie het artikel [Orchestrator-functiecodebeperkingen](durable-functions-code-constraints.md) voor meer informatie.

## <a name="billing"></a>Billing

Durable Functions worden op dezelfde manier in rekening gebracht als Azure Functions. Zie [Prijzen voor Azure Functions](https://azure.microsoft.com/pricing/details/functions/) voor meer informatie. Bij het uitvoeren van orchestrator-functies in het Azure Functions [Consumption-plan](../functions-scale.md#consumption-plan)zijn er enkele factureringsgedragingen om rekening mee te houden. Zie het factureringsartikel Duurzame [functies](durable-functions-billing.md) voor meer informatie over dit gedrag.

## <a name="jump-right-in"></a>Duik er meteen in

U kunt in minder dan 10 minuten aan de slag gaan met Durable Functions door een van deze taalspecifieke quickstart-zelfstudies te volgen:

* [C# met Visual Studio 2019](durable-functions-create-first-csharp.md)
* [JavaScript met Visual Studio Code](quickstart-js-vscode.md)

In beide quickstarts maakt en test u een lokale ‘hallo wereld’-duurzame functie. Vervolgens publiceert u de functiecode op Azure. De functie die u maakt, organiseert en koppelt aanroepen naar andere functies.

## <a name="learn-more"></a>Meer informatie

De volgende video laat de voordelen van Durable Functions zien:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Voor een meer diepgaande bespreking van duurzame functies en de onderliggende technologie, zie de volgende video (het is gericht op .NET, maar de concepten zijn ook van toepassing op andere ondersteunde talen):

> [!VIDEO https://channel9.msdn.com/Events/dotnetConf/2018/S204/player]

Omdat Durable Functions een geavanceerde extensie voor [Azure Functions](../functions-overview.md) is, is het niet geschikt voor alle toepassingen. Zie voor een vergelijking met andere Azure-technologieën [Compare Azure Functions and Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Functietypen en functies voor duurzame functies](durable-functions-types-features-overview.md)
