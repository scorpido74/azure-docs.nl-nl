---
title: Overzicht van Durable Functions - Azure
description: Inleiding tot de extensie Durable Functions voor Azure Functions.
author: cgillum
ms.topic: overview
ms.date: 08/07/2019
ms.author: cgillum
ms.reviewer: azfuncdf
ms.openlocfilehash: 8b31a5ab716b58d167a0d16579b44aa7df95a0ff
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232834"
---
# <a name="what-are-durable-functions"></a>Wat is Durable Functions?

*Durable functions* is een uitbrei ding van [Azure functions](../functions-overview.md) waarmee stateful functies kunnen worden geschreven in een serverloze reken omgeving. Met de uitbrei ding kunt u stateful werk stromen definiëren door [*Orchestrator-functies*](durable-functions-orchestrations.md) en stateful-entiteiten te schrijven door [*entiteits functies*](durable-functions-entities.md) te schrijven met behulp van het Azure functions-programmeer model. Achter de schermen beheert de uitbrei ding status, controle punten en wordt de computer opnieuw opgestart, zodat u zich kunt concentreren op uw bedrijfs logica.

## <a name="language-support"></a>Ondersteunde talen

Durable Functions ondersteunt momenteel de volgende talen:

* **C#** : zowel [vooraf gecompileerde klassebibliotheken](../functions-dotnet-class-library.md) als [C#-script](../functions-reference-csharp.md).
* **F#** : vooraf gecompileerde klassebibliotheken en F#-script. F#-script wordt alleen ondersteund voor versie 1.x van de Azure Functions-runtime.
* **JavaScript**: alleen ondersteund voor versie 2.x van de Azure Functions-runtime. Versie 1.7.0 of hoger van de Durable Functions-extensie vereist. 

Durable Functions heeft als doel alle [Azure Functions-talen](../supported-languages.md) te ondersteunen. Zie [Durable Functions issues list](https://github.com/Azure/azure-functions-durable-extension/issues) voor de laatste voortgangsstatus van de ondersteuning voor meer talen.

Net als Azure Functions zijn er sjablonen waarmee u Durable Functions kunt ontwikkelen met behulp van [Visual studio 2019](durable-functions-create-first-csharp.md), [Visual Studio Code](quickstart-js-vscode.md)en de [Azure Portal](durable-functions-create-portal.md).

## <a name="application-patterns"></a>Toepassingspatronen

De primaire use case voor Durable Functions is het vereenvoudigen van complexe stateful coördinatievereisten in serverloze toepassingen. In de volgende secties worden typische toepassings patronen beschreven die kunnen profiteren van Durable Functions:

* [Functie koppeling](#chaining)
* [Fan-out/fan-in](#fan-in-out)
* [Asynchrone HTTP-API's](#async-http)
* [Controle](#monitoring)
* [Menselijke tussenkomst](#human)
* [Aggregator](#aggregator)

### <a name="chaining"></a>Patroon #1: functie koppeling

In het patroon functie koppeling wordt een reeks functies in een specifieke volg orde uitgevoerd. In dit patroon wordt de uitvoer van een functie toegepast op de invoer van een andere functie.

![Een diagram van het patroon van de functie koppeling](./media/durable-functions-concepts/function-chaining.png)

U kunt Durable Functions gebruiken om het patroon van de functie koppeling beknopt te implementeren, zoals wordt weer gegeven in het volgende voor beeld:

#### <a name="c"></a>C#

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

#### <a name="javascript-functions-20-only"></a>Java script (alleen voor de functies 2,0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return    yield context.df.callActivity("F4", z);
});
```

In dit voor beeld zijn de waarden `F1`, `F2`, `F3`en `F4` de namen van andere functies in de functie-app. U kunt de controle stroom implementeren met behulp van normale verplichte coderings constructies. Code wordt van boven naar beneden uitgevoerd. De code kan bestaan uit de bestaande semantiek van de taal besturings flow, zoals voor waarden en lussen. U kunt de logica voor fout afhandeling in `try`/`catch`/`finally` blokken toevoegen.

U kunt de `context` para meter [IDurableOrchestrationContext] \(.NET\) en het object `context.df` (Java script) gebruiken om andere functies aan te roepen op naam, para meters en retour uitvoer. Telkens wanneer de code `await` (C#) of `yield` (Java script) aanroept, wordt in het Durable functions Framework de voortgang van het huidige functie-exemplaar gecontroleerd. Als het proces of de virtuele machine halverwege de uitvoering wordt gerecycled, wordt het functie-exemplaar hervat vanaf de vorige `await` of `yield` aanroep. Zie de volgende sectie, patroon #2: uitwaaieren/ventilatoren in voor meer informatie.

> [!NOTE]
> Het `context`-object in Java script vertegenwoordigt de volledige [functie context](../functions-reference-node.md#context-object), niet alleen de para meter [IDurableOrchestrationContext].

### <a name="fan-in-out"></a>Patroon #2: uitwaaieren/ventilator in

In het patroon uitwaaieren/ventilatoren voert u meerdere functies parallel uit en wacht u totdat alle functies zijn voltooid. Vaak wordt een samen voeging uitgevoerd op de resultaten die worden geretourneerd door de functies.

![Een diagram van het uitwaaieren/ventilator patroon](./media/durable-functions-concepts/fan-out-fan-in.png)

Met normale functies kunt u uitwaaieren door de functie meerdere berichten naar een wachtrij te verzenden. Fanning weer in is veel lastiger. Als u in een normale functie wilt inwaaieren, schrijft u code om bij te houden wanneer de functies die door de wachtrij worden geactiveerd, eindigen en vervolgens de functie-uitvoer op te slaan.

Met de extensie Durable Functions wordt dit patroon afgehandeld met relatief eenvoudige code:

#### <a name="c"></a>C#

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

#### <a name="javascript-functions-20-only"></a>Java script (alleen voor de functies 2,0)

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

Het werk van de ventilator wordt gedistribueerd naar meerdere exemplaren van de functie `F2`. Het werk wordt bijgehouden met behulp van een dynamische lijst met taken. De .NET `Task.WhenAll` API of Java script `context.df.Task.all`-API wordt aangeroepen, zodat wordt gewacht tot alle aangeroepen functies zijn voltooid. Vervolgens worden de resultaten van de `F2` functie geaggregeerd van de lijst met dynamische taken en door gegeven aan de functie `F3`.

De automatische controle punten die zich voordoen bij de `await` of `yield` aanroepen op `Task.WhenAll` of `context.df.Task.all` zorgen ervoor dat een mogelijke Midway-crash of opnieuw opstarten niet nodig is om een reeds voltooide taak opnieuw te starten.

> [!NOTE]
> In zeldzame gevallen is het mogelijk dat er een crash in het venster kan optreden nadat een activiteit functie is voltooid, maar voordat de voltooiing wordt opgeslagen in de Orchestration-geschiedenis. Als dit gebeurt, wordt de activiteit functie opnieuw uitgevoerd vanaf het begin nadat het proces is hersteld.

### <a name="async-http"></a>Patroon #3: async HTTP-Api's

Het asynchrone HTTP API-patroon is een oplossing voor het probleem van het coördineren van de status van langlopende bewerkingen met externe clients. Een veelvoorkomende manier om dit patroon te implementeren, is door een HTTP-eind punt te laten activeren van de langlopende actie. Leid vervolgens de client om naar een status eindpunt dat de client navraagt om te leren wanneer de bewerking is voltooid.

![Een diagram van het HTTP API-patroon](./media/durable-functions-concepts/async-http-api.png)

Durable Functions biedt **ingebouwde ondersteuning** voor dit patroon, waardoor de code die u moet schrijven om te communiceren met langlopende functie-uitvoeringen, wordt vereenvoudigd of verwijderd. De Durable Functions Quick start-voor beelden ([C#](durable-functions-create-first-csharp.md) en [Java script](quickstart-js-vscode.md)) tonen bijvoorbeeld een eenvoudige rest-opdracht die u kunt gebruiken om nieuwe Orchestrator-functie instanties te starten. Nadat een exemplaar is gestart, worden de HTTP-Api's van de webhook met de status van de Orchestrator-functie weer gegeven. 

In het volgende voor beeld worden REST-opdrachten weer gegeven die een Orchestrator starten en de status ervan opvragen. Voor de duidelijkheid worden bepaalde protocol gegevens uit het voor beeld wegge laten.

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

Omdat de Durable Functions runtime de status voor u beheert, hoeft u uw eigen status tracking-mechanisme niet te implementeren.

De uitbrei ding Durable Functions maakt ingebouwde HTTP-Api's beschikbaar waarmee langlopende integraties worden beheerd. U kunt dit patroon ook zelf implementeren door gebruik te maken van uw eigen functie Triggers (zoals HTTP, een wachtrij of Azure Event Hubs) en de [Orchestration client-binding](durable-functions-bindings.md#orchestration-client). U kunt bijvoorbeeld een wachtrij bericht gebruiken om beëindiging te activeren. U kunt ook een HTTP-trigger gebruiken die wordt beveiligd met een Azure Active Directory verificatie beleid in plaats van de ingebouwde HTTP-Api's die een gegenereerde sleutel voor verificatie gebruiken.

Zie het artikel [http-functies](durable-functions-http-features.md) voor meer informatie. hierin wordt uitgelegd hoe u asynchrone, langlopende processen via http kunt weer geven met behulp van de extensie Durable functions.

### <a name="monitoring"></a>Patroon #4: monitor

Het monitor patroon verwijst naar een flexibel, terugkerend proces in een werk stroom. Een voor beeld is een polling totdat aan bepaalde voor waarden wordt voldaan. U kunt een normale [Timer trigger](../functions-bindings-timer.md) gebruiken om een basis scenario te verhelpen, zoals een periodieke opschoon taak, maar het bijbehorende interval is statisch en het beheren van de levens duur van het exemplaar wordt complex. U kunt Durable Functions gebruiken voor het maken van flexibele terugkeer intervallen, het beheren van de levens duur van taken en het maken van meerdere monitor processen vanuit één indeling.

Een voor beeld van het monitor patroon is het terugdraaien van het eerdere async HTTP API-scenario. In plaats van een eind punt weer te geven voor een externe client om een langlopende bewerking te bewaken, gebruikt de langlopende monitor een extern eind punt en wordt er gewacht op een status wijziging.

![Een diagram van het monitor patroon](./media/durable-functions-concepts/monitor.png)

In een paar regels code kunt u Durable Functions gebruiken om meerdere monitors te maken die wille keurige eind punten observeren. De monitors kunnen de uitvoering beëindigen wanneer aan een voor waarde wordt voldaan, of de `IDurableOrchestrationClient` kan de monitors beëindigen. U kunt het `wait` interval van een monitor wijzigen op basis van een specifieke voor waarde (bijvoorbeeld exponentiële uitstel.) 

Met de volgende code wordt een basis monitor geïmplementeerd:

#### <a name="c"></a>C#

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

#### <a name="javascript-functions-20-only"></a>Java script (alleen voor de functies 2,0)

```javascript
const df = require("durable-functions");
const moment = require("moment");

module.exports = df.orchestrator(function*(context) {
    const jobId = context.df.getInput();
    const pollingInternal = getPollingInterval();
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

Wanneer een aanvraag wordt ontvangen, wordt er een nieuw Orchestration-exemplaar gemaakt voor die taak-ID. Het exemplaar pollt een status totdat aan een voor waarde wordt voldaan en de lus wordt afgesloten. Een duurzame timer bepaalt het polling-interval. Vervolgens kan er meer werk worden uitgevoerd of kan de indeling worden beëindigd. Wanneer de `context.CurrentUtcDateTime` (.NET) of `context.df.currentUtcDateTime` (Java script) de `expiryTime` waarde overschrijdt, wordt de monitor beëindigd.

### <a name="human"></a>Patroon #5: menselijke interactie

Veel geautomatiseerde processen omvatten een soort Human interactie. Mensen met een geautomatiseerd proces kunnen lastig zijn omdat ze niet als Maxi maal beschikbaar zijn en als reactie van Cloud Services. Een geautomatiseerd proces kan deze interactie toestaan door gebruik te maken van time-outs en compensatie logica.

Een goedkeurings proces is een voor beeld van een bedrijfs proces waarbij menselijke interactie betrokken is. Goed keuring van een manager kan vereist zijn voor een onkosten rapport dat een bepaald bedrag in Euro's overschrijdt. Als het onkosten rapport binnen 72 uur niet door de Manager wordt goedgekeurd (misschien is de Manager op vakantie gezet), wordt een escalatie proces uitgevoerd om de goed keuring van iemand anders (mogelijk de Manager van de Manager) te verkrijgen.

![Een diagram van het menselijke interactie patroon](./media/durable-functions-concepts/approval.png)

U kunt het patroon in dit voor beeld implementeren met behulp van een Orchestrator-functie. De Orchestrator gebruikt een [duurzame timer](durable-functions-timers.md) om goed keuring aan te vragen. De Orchestrator escaleren als er een time-out optreedt. De Orchestrator wacht op een [externe gebeurtenis](durable-functions-external-events.md), zoals een melding die wordt gegenereerd door een menselijke interactie.

In deze voor beelden wordt een goedkeurings proces voor het voor beeld van het menselijke interactie patroon gemaakt:

#### <a name="c"></a>C#

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

#### <a name="javascript-functions-20-only"></a>Java script (alleen voor de functies 2,0)

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

Als u de duurzame timer wilt maken, roept u `context.CreateTimer` (.NET) of `context.df.createTimer` (Java script) aan. De melding wordt ontvangen door `context.WaitForExternalEvent` (.NET) of `context.df.waitForExternalEvent` (Java script). Vervolgens wordt `Task.WhenAny` (.NET) of `context.df.Task.any` (Java script) aangeroepen om te bepalen of er eerst een time-out optreedt of de goed keuring verwerken (de goed keuring wordt ontvangen vóór de time-out).

Een externe client kan de gebeurtenis melding verzenden naar een wacht functie die gebruikmaakt van de [ingebouwde http-api's](durable-functions-http-api.md#raise-event) of met behulp van de methode `RaiseEventAsync` (.net) of `raiseEvent` (Java script) vanuit een andere functie:

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

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

```bash
curl -d "true" http://localhost:7071/runtime/webhooks/durabletask/instances/{instanceId}/raiseEvent/ApprovalEvent -H "Content-Type: application/json"
```

### <a name="aggregator"></a>Patroon #6: aggregator

Het zesde patroon is het verzamelen van gebeurtenis gegevens over een bepaalde periode in een enkele, adresseer bare *entiteit*. In dit patroon kunnen de gegevens die worden geaggregeerd afkomstig zijn uit meerdere bronnen, worden geleverd in batches of kunnen ze over een lange periode worden verspreid. De aggregator moet mogelijk actie ondernemen op gebeurtenis gegevens terwijl deze arriveert, en externe clients moeten mogelijk query's uitvoeren op de geaggregeerde gegevens.

![Aggregatie diagram](./media/durable-functions-concepts/aggregator.png)

Het lastiger zijn om dit patroon te implementeren met normale, stateless functies is dat gelijktijdigheids beheer een enorme uitdaging wordt. U hoeft zich geen zorgen te maken over meerdere threads die tegelijkertijd dezelfde gegevens wijzigen. u moet er ook voor zorgen dat de aggregator alleen op één virtuele machine tegelijk wordt uitgevoerd.

U kunt [duurzame entiteiten](durable-functions-entities.md) gebruiken om dit patroon eenvoudig te implementeren als een enkele functie.

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

Duurzame entiteiten kunnen ook worden gemodelleerd als klassen in .NET. Dit model kan nuttig zijn als de lijst met bewerkingen is opgelost en groot wordt. Het volgende voor beeld is een equivalente implementatie van de `Counter` entiteit met behulp van .NET-klassen en-methoden.

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

Clients kunnen *bewerkingen* in de wachtrij plaatsen voor (ook wel ' Signa lering ' genoemd) een entiteits functie met de [client binding](durable-functions-bindings.md#entity-client)van de entiteit.

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
> Dynamische gegenereerde proxy's zijn ook beschikbaar in .NET voor het Signa leren van entiteiten op een type veilige manier. Naast Signa lering kunnen clients ook een query uitvoeren op de status van een entiteit functie met behulp van [type veilige methoden](durable-functions-bindings.md#entity-client-usage) op de Orchestration-client binding.


```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

Entiteits functies zijn beschikbaar in [Durable Functions 2,0](durable-functions-versions.md) en hoger.

## <a name="the-technology"></a>De technologie

Achter de schermen is de uitbrei ding van de Durable Functions gebaseerd op het [duurzame taak raamwerk](https://github.com/Azure/durabletask), een open-source bibliotheek op github die wordt gebruikt voor het bouwen van werk stromen in code. Net als Azure Functions is de serverloze evolutie van Azure WebJobs, Durable Functions de serverloze evolutie van het duurzame taak raamwerk. Micro soft en andere organisaties gebruiken het duurzame taak raamwerk uitgebreid om essentiële processen te automatiseren. Het is natuurlijk geschikt voor de serverloze Azure Functions omgeving.

## <a name="code-constraints"></a>Code beperkingen

Orchestrator-functies hebben een set coderings regels die moeten worden gevolgd om betrouw bare en langdurige uitvoerings garanties te bieden. Zie het artikel [Orchestrator functie code constraints](durable-functions-code-constraints.md) voor meer informatie.

## <a name="billing"></a>Billing

Durable Functions worden op dezelfde manier in rekening gebracht als Azure Functions. Zie [Prijzen voor Azure Functions](https://azure.microsoft.com/pricing/details/functions/) voor meer informatie. Bij het uitvoeren van Orchestrator-functies in het Azure Functions [verbruiks abonnement](../functions-scale.md#consumption-plan), zijn er enkele facturerings gedrag van belang. Zie het artikel over [Durable functions facturering](durable-functions-billing.md) voor meer informatie over deze problemen.

## <a name="jump-right-in"></a>Duik er meteen in

U kunt in minder dan 10 minuten aan de slag gaan met Durable Functions door een van deze taalspecifieke quickstart-zelfstudies te volgen:

* [C#Visual Studio 2019 gebruiken](durable-functions-create-first-csharp.md)
* [JavaScript met Visual Studio Code](quickstart-js-vscode.md)

In beide quickstarts maakt en test u een lokale ‘hallo wereld’-duurzame functie. Vervolgens publiceert u de functiecode op Azure. De functie die u maakt, organiseert en koppelt aanroepen naar andere functies.

## <a name="learn-more"></a>Meer informatie

De volgende video laat de voordelen van Durable Functions zien:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Durable-Functions-in-Azure-Functions/player] 

Voor een uitgebreidere bespreking van Durable Functions en de onderliggende technologie raadpleegt u de volgende video (deze is gericht op .NET, maar de concepten zijn ook van toepassing op andere ondersteunde talen):

> [!VIDEO https://channel9.msdn.com/Events/dotnetConf/2018/S204/player]

Omdat Durable Functions een geavanceerde extensie voor [Azure Functions](../functions-overview.md) is, is het niet geschikt voor alle toepassingen. Zie voor een vergelijking met andere Azure-technologieën [Compare Azure Functions and Azure Logic Apps](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-azure-functions-and-azure-logic-apps).

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Functie typen en functies van Durable Functions](durable-functions-types-features-overview.md)
