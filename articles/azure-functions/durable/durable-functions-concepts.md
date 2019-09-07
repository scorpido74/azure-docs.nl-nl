---
title: Durable Functions patronen en technische concepten in Azure Functions
description: Meer informatie over de uitbrei ding van de Durable Functions in Azure Functions biedt u de voor delen van de uitvoering van stateful code in de Cloud.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: azfuncdf
ms.openlocfilehash: fe3000181ed02e3640e7af48fa492f4a7db55191
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70734573"
---
# <a name="durable-functions-patterns-and-technical-concepts-azure-functions"></a>Durable Functions patronen en technische concepten (Azure Functions)

Durable Functions is een uitbrei ding van [Azure functions](../functions-overview.md) en [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md). U kunt Durable Functions gebruiken om stateful functies te schrijven in een serverloze omgeving. Met de extensie worden status, controlepunten en het opnieuw opstarten voor u beheerd. 

In dit artikel vindt u gedetailleerde informatie over het gedrag van de Durable Functions extensie voor Azure Functions en algemene implementatie patronen. Deze informatie kan u helpen bij het bepalen van de manier waarop u Durable Functions kunt gebruiken om uw ontwikkel uitdagingen te helpen oplossen.

> [!NOTE]
> Durable Functions is een geavanceerde extensie voor Azure Functions die niet geschikt is voor alle toepassingen. In dit artikel wordt ervan uitgegaan dat u een sterke kennis hebt van concepten in [Azure functions](../functions-overview.md) en de uitdagingen die zijn betrokken bij het ontwikkelen van serverloze toepassingen.

## <a name="patterns"></a>Patronen

In deze sectie worden enkele algemene toepassings patronen beschreven, waarbij Durable Functions nuttig kan zijn.

### <a name="chaining"></a>Patroon #1: Functiekoppeling

In het patroon functie koppeling wordt een reeks functies in een specifieke volg orde uitgevoerd. In dit patroon wordt de uitvoer van een functie toegepast op de invoer van een andere functie.

![Een diagram van het patroon van de functie koppeling](./media/durable-functions-concepts/function-chaining.png)

U kunt Durable Functions gebruiken om het patroon van de functie koppeling beknopt te implementeren, zoals wordt weer gegeven in het volgende voor beeld:

#### <a name="precompiled-c"></a>Vooraf gecompileerdeC#

```csharp
public static async Task<object> Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1");
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

#### <a name="c-script"></a>C#-script

```csharp
public static async Task<object> Run(DurableOrchestrationContext context)
{
    try
    {
        var x = await context.CallActivityAsync<object>("F1");
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

> [!NOTE]
> Er zijn subtiele verschillen tussen het schrijven van een vooraf gecompileerde, duurzame functie in C# en het schrijven C# van een vooraf gecompileerde, duurzame functie in een script. In een C# vooraf gecompileerde functie moeten duurzame para meters worden gedecoreerd met de betreffende kenmerken. Een voor beeld is `[OrchestrationTrigger]` het kenmerk voor `DurableOrchestrationContext` de para meter. Als de C# para meters in een vooraf gecompileerde, duurzame functie niet op de juiste wijze zijn gebundeld, kunnen de variabelen niet worden geïnjecteerd in de functie en treedt er een fout op. Zie voor meer voor beelden de [Azure-functions-duurzame extensie voorbeelden op github](https://github.com/Azure/azure-functions-durable-extension/blob/master/samples).

#### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const x = yield context.df.callActivity("F1");
    const y = yield context.df.callActivity("F2", x);
    const z = yield context.df.callActivity("F3", y);
    return yield context.df.callActivity("F4", z);
});
```

In dit voor beeld zijn de `F1`waarden `F2` `F3`,, en `F4` de namen van andere functies in de functie-app. U kunt de controle stroom implementeren met behulp van normale verplichte coderings constructies. Code wordt van boven naar beneden uitgevoerd. De code kan bestaan uit de bestaande semantiek van de taal besturings flow, zoals voor waarden en lussen. U kunt de logica voor fout afhandeling `try` in / / `catch` `finally` blokken toevoegen.

U `context` kunt de para meter [DurableOrchestrationContext] \(.net\) en het `context.df` object (Java script) gebruiken om andere functies aan te roepen op naam, para meters door geven en retour uitvoer. Telkens wanneer de code `await` aanroeptC#() `yield` of (Java script), wordt in het Durable functions Framework de voortgang van het huidige functie-exemplaar gecontroleerd. Als het proces of de virtuele machine halverwege de uitvoering wordt gerecycled, wordt het functie-exemplaar hervat `await` vanuit `yield` de voor gaande of-aanroep. Zie voor meer informatie de volgende sectie, patroon #2: Uitwaaieren/inwaaieren in.

> [!NOTE]
> Het `context` object in Java script vertegenwoordigt de volledige [functie context](../functions-reference-node.md#context-object), niet alleen de para meter [DurableOrchestrationContext] .

### <a name="fan-in-out"></a>Patroon #2: Uitwaaieren/ventilator in

In het patroon uitwaaieren/ventilatoren voert u meerdere functies parallel uit en wacht u totdat alle functies zijn voltooid. Vaak wordt een samen voeging uitgevoerd op de resultaten die worden geretourneerd door de functies.

![Een diagram van het uitwaaieren/ventilator patroon](./media/durable-functions-concepts/fan-out-fan-in.png)

Met normale functies kunt u uitwaaieren door de functie meerdere berichten naar een wachtrij te verzenden. Fanning weer in is veel lastiger. Als u in een normale functie wilt inwaaieren, schrijft u code om bij te houden wanneer de functies die door de wachtrij worden geactiveerd, eindigen en vervolgens de functie-uitvoer op te slaan. 

Met de extensie Durable Functions wordt dit patroon afgehandeld met relatief eenvoudige code:

#### <a name="precompiled-c"></a>Vooraf gecompileerdeC#

```csharp
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1");
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

#### <a name="c-script"></a>C#-script

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    var parallelTasks = new List<Task<int>>();

    // Get a list of N work items to process in parallel.
    object[] workBatch = await context.CallActivityAsync<object[]>("F1");
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

#### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

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

Het werk van de ventilator wordt gedistribueerd naar meerdere exemplaren `F2` van de functie. Het werk wordt bijgehouden met behulp van een dynamische lijst met taken. De .net `Task.WhenAll` API of Java `context.df.Task.all` script-API wordt aangeroepen, zodat wordt gewacht tot alle aangeroepen functies zijn voltooid. Vervolgens worden de `F2` functie-uitvoer geaggregeerd van de lijst met dynamische taken en door gegeven aan de `F3` functie.

De automatische controle punten die zich voordoen `await` bij `yield` of aanroepen `context.df.Task.all` `Task.WhenAll` of ervoor zorgen dat een mogelijke Midway-crash of opnieuw opstarten niet nodig is om een reeds voltooide taak opnieuw te starten.

### <a name="async-http"></a>Patroon #3: Asynchrone HTTP-Api's

Het patroon async HTTP Api's behandelt het probleem van het coördineren van de status van langlopende bewerkingen met externe clients. Een veelvoorkomende manier om dit patroon te implementeren, is door een HTTP-aanroep de langlopende actie te activeren. Leid vervolgens de client om naar een status eindpunt dat de client navraagt om te leren wanneer de bewerking is voltooid.

![Een diagram van het HTTP API-patroon](./media/durable-functions-concepts/async-http-api.png)

Durable Functions biedt ingebouwde Api's waarmee de code die u schrijft om te communiceren met langlopende functie-uitvoeringen, wordt vereenvoudigd. De Durable Functions Quick Start-[C#](durable-functions-create-first-csharp.md) voor beelden (en [Java script](quickstart-js-vscode.md)) tonen een eenvoudige rest-opdracht die u kunt gebruiken om nieuwe Orchestrator-functie instanties te starten. Nadat een exemplaar is gestart, worden de HTTP-Api's van de webhook met de status van de Orchestrator-functie weer gegeven. 

In het volgende voor beeld worden REST-opdrachten weer gegeven die een Orchestrator starten en de status ervan opvragen. Voor duidelijkheid worden bepaalde gegevens uit het voor beeld wegge laten.

```
> curl -X POST https://myfunc.azurewebsites.net/orchestrators/DoWork -H "Content-Length: 0" -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"id":"b79baf67f717453ca9e86c5da21e03ec", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 202 Accepted
Content-Type: application/json
Location: https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec

{"runtimeStatus":"Running","lastUpdatedTime":"2017-03-16T21:20:47Z", ...}

> curl https://myfunc.azurewebsites.net/admin/extensions/DurableTaskExtension/b79baf67f717453ca9e86c5da21e03ec -i
HTTP/1.1 200 OK
Content-Length: 175
Content-Type: application/json

{"runtimeStatus":"Completed","lastUpdatedTime":"2017-03-16T21:20:57Z", ...}
```

Omdat de Durable Functions-runtime status beheert, hoeft u uw eigen status tracking-mechanisme niet te implementeren.

De uitbrei ding Durable Functions heeft ingebouwde webhooks waarmee langlopende integraties worden beheerd. U kunt dit patroon zelf implementeren door gebruik te maken van uw eigen functie Triggers (zoals http, een wachtrij of Azure Event hubs) en `orchestrationClient` de binding. U kunt bijvoorbeeld een wachtrij bericht gebruiken om beëindiging te activeren. U kunt ook een HTTP-trigger gebruiken die wordt beveiligd met een Azure Active Directory verificatie beleid in plaats van de ingebouwde webhooks die een gegenereerde sleutel voor verificatie gebruiken.

Hier volgen enkele voor beelden van het gebruik van het HTTP API-patroon:

#### <a name="precompiled-c"></a>Vooraf gecompileerdeC#

```csharp
// An HTTP-triggered function starts a new orchestrator function instance.
[FunctionName("StartNewOrchestration")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger] HttpRequestMessage req,
    [OrchestrationClient] DurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // The function name comes from the request URL.
    // The function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

#### <a name="c-script"></a>C#-script

```csharp
// An HTTP-triggered function starts a new orchestrator function instance.
public static async Task<HttpResponseMessage> Run(
    HttpRequestMessage req,
    DurableOrchestrationClient starter,
    string functionName,
    ILogger log)
{
    // The function name comes from the request URL.
    // The function input comes from the request content.
    dynamic eventData = await req.Content.ReadAsAsync<object>();
    string instanceId = await starter.StartNewAsync(functionName, eventData);

    log.LogInformation($"Started orchestration with ID = '{instanceId}'.");

    return starter.CreateCheckStatusResponse(req, instanceId);
}
```

#### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

```javascript
// An HTTP-triggered function starts a new orchestrator function instance.
const df = require("durable-functions");

module.exports = async function (context, req) {
    const client = df.getClient(context);

    // The function name comes from the request URL.
    // The function input comes from the request content.
    const eventData = req.body;
    const instanceId = await client.startNew(req.params.functionName, undefined, eventData);

    context.log(`Started orchestration with ID = '${instanceId}'.`);

    return client.createCheckStatusResponse(req, instanceId);
};
```

In .net is de para meter [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) `starter` een waarde uit de `orchestrationClient` uitvoer binding, die deel uitmaakt van de uitbrei ding Durable functions. In Java script wordt dit object geretourneerd door aan `df.getClient(context)`te roepen. Deze objecten bieden methoden die u kunt gebruiken om te starten, gebeurtenissen te verzenden naar, beëindiging en query's uit te voeren voor nieuwe of bestaande Orchestrator-functie exemplaren.

In de voor gaande voor beelden krijgt een door http geactiveerde functie een `functionName` waarde van de binnenkomende URL en wordt de waarde door gegeven aan [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_). De [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_System_Net_Http_HttpRequestMessage_System_String_) -binding-API retourneert een antwoord met een `Location` kop en aanvullende informatie over het exemplaar. U kunt de informatie later gebruiken om de status van het gestarte exemplaar op te zoeken of om het exemplaar te beëindigen.

### <a name="monitoring"></a>Patroon #4: Controleren

Het monitor patroon verwijst naar een flexibel, terugkerend proces in een werk stroom. Een voor beeld is een polling totdat aan bepaalde voor waarden wordt voldaan. U kunt een normale [Timer trigger](../functions-bindings-timer.md) gebruiken om een basis scenario te verhelpen, zoals een periodieke opschoon taak, maar het bijbehorende interval is statisch en het beheren van de levens duur van het exemplaar wordt complex. U kunt Durable Functions gebruiken voor het maken van flexibele terugkeer intervallen, het beheren van de levens duur van taken en het maken van meerdere monitor processen vanuit één indeling.

Een voor beeld van het monitor patroon is het terugdraaien van het eerdere async HTTP API-scenario. In plaats van een eind punt weer te geven voor een externe client om een langlopende bewerking te bewaken, gebruikt de langlopende monitor een extern eind punt en wordt er gewacht op een status wijziging.

![Een diagram van het monitor patroon](./media/durable-functions-concepts/monitor.png)

In een paar regels code kunt u Durable Functions gebruiken om meerdere monitors te maken die wille keurige eind punten observeren. De monitors kunnen de uitvoering beëindigen wanneer aan een voor waarde wordt voldaan, of de [DurableOrchestrationClient](durable-functions-instance-management.md) kan de monitors beëindigen. U kunt het interval van `wait` een monitor wijzigen op basis van een specifieke voor waarde (bijvoorbeeld exponentiële uitstel.) 

Met de volgende code wordt een basis monitor geïmplementeerd:

#### <a name="precompiled-c"></a>Vooraf gecompileerdeC#

```csharp
[FunctionName("Orchestrator")]
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
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

#### <a name="c-script"></a>C#-script

```csharp
public static async Task Run(DurableOrchestrationContext context)
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

#### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

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

Wanneer een aanvraag wordt ontvangen, wordt er een nieuw Orchestration-exemplaar gemaakt voor die taak-ID. Het exemplaar pollt een status totdat aan een voor waarde wordt voldaan en de lus wordt afgesloten. Een duurzame timer bepaalt het polling-interval. Vervolgens kan er meer werk worden uitgevoerd of kan de indeling worden beëindigd. Wanneer de `context.CurrentUtcDateTime` (.net) of `context.df.currentUtcDateTime` (Java script) de `expiryTime` waarde overschrijdt, wordt de monitor beëindigd.

### <a name="human"></a>Patroon #5: Menselijke tussenkomst

Veel geautomatiseerde processen omvatten een soort Human interactie. Mensen met een geautomatiseerd proces kunnen lastig zijn omdat ze niet als Maxi maal beschikbaar zijn en als reactie van Cloud Services. Een geautomatiseerd proces kan dit toestaan door time-outs en compensatie logica te gebruiken.

Een goedkeurings proces is een voor beeld van een bedrijfs proces waarbij menselijke interactie betrokken is. Goed keuring van een manager kan vereist zijn voor een onkosten rapport dat een bepaald bedrag in Euro's overschrijdt. Als het onkosten rapport binnen 72 uur niet door de Manager wordt goedgekeurd (misschien is de Manager op vakantie gezet), wordt een escalatie proces uitgevoerd om de goed keuring van iemand anders (mogelijk de Manager van de Manager) te verkrijgen.

![Een diagram van het menselijke interactie patroon](./media/durable-functions-concepts/approval.png)

U kunt het patroon in dit voor beeld implementeren met behulp van een Orchestrator-functie. De Orchestrator gebruikt een [duurzame timer](durable-functions-timers.md) om goed keuring aan te vragen. De Orchestrator escaleren als er een time-out optreedt. De Orchestrator wacht op een [externe gebeurtenis](durable-functions-external-events.md), zoals een melding die wordt gegenereerd door een menselijke interactie.

In deze voor beelden wordt een goedkeurings proces voor het voor beeld van het menselijke interactie patroon gemaakt:

#### <a name="precompiled-c"></a>Vooraf gecompileerdeC#

```csharp
[FunctionName("Orchestrator")]
public static async Task Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval");
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
            await context.CallActivityAsync("Escalate");
        }
    }
}
```

#### <a name="c-script"></a>C#-script

```csharp
public static async Task Run(DurableOrchestrationContext context)
{
    await context.CallActivityAsync("RequestApproval");
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
            await context.CallActivityAsync("Escalate");
        }
    }
}
```

#### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

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

Voor het maken van de duurzame timer `context.CreateTimer` , aanroep (.net `context.df.createTimer` ) of (Java script). De melding wordt ontvangen door `context.WaitForExternalEvent` (.net) of `context.df.waitForExternalEvent` (Java script). `context.df.Task.any` Vervolgens wordt `Task.WhenAny` (.net) of (Java script) aangeroepen om te bepalen of er eerst een time-out optreedt of de goed keuring verwerken (de goed keuring wordt ontvangen vóór de time-out).

Een externe client kan de gebeurtenis melding verzenden naar een wacht functie die gebruikmaakt van de [ingebouwde http-api's](durable-functions-http-api.md#raise-event) of met behulp van de [DurableOrchestrationClient. RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_System_String_System_String_System_Object_) -API van een andere functie:

#### <a name="precompiled-c"></a>Vooraf gecompileerdeC#

```csharp
public static async Task Run(
  [HttpTrigger] string instanceId,
  [OrchestrationClient] DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

#### <a name="c-script"></a>C# Script

```csharp
public static async Task Run(string instanceId, DurableOrchestrationClient client)
{
    bool isApproved = true;
    await client.RaiseEventAsync(instanceId, "ApprovalEvent", isApproved);
}
```

#### <a name="javascript"></a>Javascript

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const isApproved = true;
    await client.raiseEvent(instanceId, "ApprovalEvent", isApproved);
};
```

### <a name="aggregator"></a>Patroon #6: Aggregator (preview-versie)

Het zesde patroon is het verzamelen van gebeurtenis gegevens over een bepaalde periode in een enkele, adresseer bare *entiteit*. In dit patroon kunnen de gegevens die worden geaggregeerd afkomstig zijn uit meerdere bronnen, worden geleverd in batches of kunnen ze over een lange periode worden verspreid. De aggregator moet mogelijk actie ondernemen op gebeurtenis gegevens terwijl deze arriveert, en externe clients moeten mogelijk query's uitvoeren op de geaggregeerde gegevens.

![Aggregatie diagram](./media/durable-functions-concepts/aggregator.png)

Het lastiger zijn om dit patroon te implementeren met normale, stateless functies is dat gelijktijdigheids beheer een enorme uitdaging wordt. U hoeft zich geen zorgen te maken over meerdere threads die tegelijkertijd dezelfde gegevens wijzigen. u moet er ook voor zorgen dat de aggregator alleen op één virtuele machine tegelijk wordt uitgevoerd.

Met behulp van een [duurzame entiteits functie](durable-functions-preview.md#entity-functions)kan dit patroon eenvoudig als één functie worden geïmplementeerd.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += operand;
            break;
        case "reset":
            currentValue = 0;
            break;
        case "get":
            ctx.Return(currentValue);
            break;
    }

    ctx.SetState(currentValue);
}
```

Duurzame entiteiten kunnen ook worden gemodelleerd als .NET-klassen. Dit kan handig zijn als de lijst met bewerkingen groot wordt en als deze voornamelijk statisch is. Het volgende voor beeld is een gelijkwaardige implementatie `Counter` van de entiteit met behulp van .net-klassen en-methoden.

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

Clients kunnen *bewerkingen* in de wachtrij plaatsen voor (ook wel ' Signa lering ' genoemd) een entiteits functie met behulp van de `orchestrationClient` binding.

```csharp
[FunctionName("EventHubTriggerCSharp")]
public static async Task Run(
    [EventHubTrigger("device-sensor-events")] EventData eventData,
    [OrchestrationClient] IDurableOrchestrationClient entityClient)
{
    var metricType = (string)eventData.Properties["metric"];
    var delta = BitConverter.ToInt32(eventData.Body, eventData.Body.Offset);

    // The "Counter/{metricType}" entity is created on-demand.
    var entityId = new EntityId("Counter", metricType);
    await entityClient.SignalEntityAsync(entityId, "add", delta);
}
```

Dynamische gegenereerde proxy's zijn ook beschikbaar voor het verzenden van een signaal entiteit op een type veilige manier. Naast Signa lering kunnen clients ook een query uitvoeren op de status van een entiteits functie met behulp van methoden `orchestrationClient` voor de binding.

> [!NOTE]
> Entiteits functies zijn momenteel alleen beschikbaar in de [Durable Functions 2,0 Preview](durable-functions-preview.md).

## <a name="the-technology"></a>De technologie

Achter de schermen is de uitbrei ding van de Durable Functions gebaseerd op het [duurzame taak raamwerk](https://github.com/Azure/durabletask), een open-source bibliotheek op github die wordt gebruikt voor het bouwen van duurzame taak integraties. Net als Azure Functions is de serverloze evolutie van Azure WebJobs, Durable Functions de serverloze evolutie van het duurzame taak raamwerk. Micro soft en andere organisaties gebruiken het duurzame taak raamwerk uitgebreid om essentiële processen te automatiseren. Het is natuurlijk geschikt voor de serverloze Azure Functions omgeving.

### <a name="event-sourcing-checkpointing-and-replay"></a>Gebeurtenis bronnen, controle punten en herhalingen

Orchestrator functioneert de uitvoerings status op betrouw bare wijze met behulp van het ontwerp patroon [gebeurtenis sourcing](https://docs.microsoft.com/azure/architecture/patterns/event-sourcing) . In plaats van de huidige status van een indeling rechtstreeks op te slaan, gebruikt de uitbrei ding van de Durable Functions een archief alleen toevoegen voor het vastleggen van de volledige reeks acties die de functie indeling neemt. Een archief met alleen-lezen heeft veel voor delen ten opzichte van "dumping" van de volledige runtime status. Voor delen zijn onder andere betere prestaties, schaal baarheid en reactie snelheid. U krijgt ook de uiteindelijke consistentie voor transactionele gegevens en volledige controle sporen en geschiedenis. De controle spoor ondersteunt betrouw bare veredelings acties.

Durable Functions gebeurtenis bronnen worden op transparante wijze gebruikt. Achter de schermen levert de `await` operatorC#() `yield` of (Java script) in een Orchestrator-functie de controle over van de Orchestrator-thread terug naar de verzender van het duurzame taak raamwerk. De dispatcher voert vervolgens nieuwe acties uit die de Orchestrator-functie heeft gepland (zoals het aanroepen van een of meer onderliggende functies of het plannen van een duurzame timer) op de opslag. De transparante doorvoer actie wordt toegevoegd aan de uitvoerings geschiedenis van het Orchestration-exemplaar. De geschiedenis wordt opgeslagen in een opslag tabel. Met de actie door voeren worden vervolgens berichten toegevoegd aan een wachtrij om de werkelijke hoeveelheid werk te plannen. Op dit moment kan de Orchestrator-functie uit het geheugen worden verwijderd. 

Facturering voor de Orchestrator-functie stopt als u het verbruik van de Azure Functions gebruikt. Wanneer er meer werk moet worden gedaan, wordt de functie opnieuw gestart en wordt de status opnieuw opgebouwd.

Wanneer een Orchestration-functie meer werk heeft gekregen (bijvoorbeeld wanneer er een antwoord bericht wordt ontvangen of een duurzame timer verloopt), wordt de volledige functie door de Orchestrator geactiveerd en opnieuw uitgevoerd vanaf de start om de lokale status opnieuw op te bouwen. 

Als de code tijdens het opnieuw afspelen probeert een functie aan te roepen (of andere async-werkzaamheden uit te voeren), wordt in het duurzame taak raamwerk de uitvoerings geschiedenis van de huidige indeling geraadpleegd. Als wordt gedetecteerd dat de [functie activiteit](durable-functions-types-features-overview.md#activity-functions) al is uitgevoerd en een resultaat oplevert, wordt het resultaat van die functie opnieuw afgespeeld en wordt de Orchestrator-code nog steeds uitgevoerd. Opnieuw afspelen gaat door totdat de functie code is voltooid of totdat het nieuwe async-werk is gepland.

### <a name="orchestrator-code-constraints"></a>Orchestrator-code beperkingen

Het gedrag voor opnieuw afspelen van Orchestrator-code maakt beperkingen voor het type code dat u kunt schrijven in een Orchestrator-functie. Orchestrator-code moet bijvoorbeeld deterministisch zijn, omdat deze meerdere keren opnieuw moet worden afgespeeld en het resultaat elke keer kan worden geproduceerd. Zie voor de volledige lijst met beperkingen [Orchestrator-code beperkingen](durable-functions-checkpointing-and-replay.md#orchestrator-code-constraints).

## <a name="monitoring-and-diagnostics"></a>Controle en diagnose

De Durable Functions-extensie verzendt automatisch gestructureerde tracerings gegevens voor [Application Insights](../functions-monitoring.md) als u uw functie-app instelt met een Azure-toepassing Insights-instrumentatie sleutel. U kunt de tracerings gegevens gebruiken om de acties en voortgang van uw integraties te bewaken.

Hier volgt een voor beeld van hoe de Durable Functions tracerings gebeurtenissen eruitzien in de Application Insights Portal wanneer u [Application Insights Analytics](../../application-insights/app-insights-analytics.md)gebruikt:

![Query resultaten Application Insights](./media/durable-functions-concepts/app-insights-1.png)

In het `customDimensions` veld in elk logboek item vindt u nuttige gestructureerde gegevens. Hier volgt een voor beeld van een vermelding die volledig is uitgevouwen:

![Het veld customDimensions in een Application Insights query](./media/durable-functions-concepts/app-insights-2.png)

Vanwege het replay gedrag van de verzender van het duurzame taak raamwerk, kunt u de overbodige logboek vermeldingen voor opnieuw gespelde acties bekijken. Redundante logboek vermeldingen kunnen u helpen inzicht te krijgen in het gedrag van opnieuw afspelen van de kern engine. Het artikel [diagnostiek](durable-functions-diagnostics.md) bevat voorbeeld query's waarmee herhalings logboeken worden gefilterd, zodat u alleen de logboeken ' real-time ' kunt zien.

## <a name="storage-and-scalability"></a>Opslag en schaalbaarheid

De uitbrei ding Durable Functions maakt gebruik van wacht rijen, tabellen en blobs in Azure Storage om de status van de uitvoerings geschiedenis en de uitvoering van de functie te behouden. U kunt het standaard opslag account voor de functie-app gebruiken, maar u kunt ook een afzonderlijk opslag account configureren. Mogelijk wilt u een afzonderlijk account op basis van de doorvoer limieten van de opslag. De Orchestrator-code die u schrijft, communiceert niet met de entiteiten in deze opslag accounts. Het duurzame taak raamwerk beheert de entiteiten rechtstreeks als een implementatie detail.

Orchestrator-functies plannen activiteit functies en ontvangen hun antwoorden via interne wachtrij berichten. Wanneer een functie-app wordt uitgevoerd in het Azure Functions verbruiks abonnement, worden deze wacht rijen bewaakt door de [Azure functions Scale-controller](../functions-scale.md#how-the-consumption-and-premium-plans-work) . Nieuwe reken instanties worden toegevoegd als dat nodig is. Bij het schalen naar meerdere Vm's kan een Orchestrator-functie op één virtuele machine worden uitgevoerd, terwijl activiteit functies die de Orchestrator-functie aanroepen mogelijk op verschillende Vm's worden uitgevoerd. Zie [prestaties en schalen](durable-functions-perf-and-scale.md)voor meer informatie over het schaal gedrag van Durable functions.

De uitvoerings geschiedenis voor Orchestrator-accounts wordt opgeslagen in de tabel opslag. Wanneer een exemplaar op een bepaalde virtuele machine wordt gehydrateerd, haalt de Orchestrator de uitvoerings geschiedenis op uit de tabel opslag zodat de lokale status opnieuw kan worden samengesteld. Een handig aspect van het gebruik van de geschiedenis in tabel opslag is dat u hulpprogram ma's zoals [Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md) kunt gebruiken om de geschiedenis van uw integraties te bekijken.

Opslag-blobs worden voornamelijk gebruikt als lease mechanisme om de scale-out van indelings instanties op meerdere Vm's te coördineren. Opslag-blobs bevatten gegevens voor grote berichten die niet rechtstreeks in tabellen of wacht rijen kunnen worden opgeslagen.

![Een scherm opname van Azure Storage Explorer](./media/durable-functions-concepts/storage-explorer.png)

> [!NOTE]
> Hoewel het eenvoudig en handig is om uitvoerings geschiedenis in tabel opslag te bekijken, moet u geen afhankelijkheden maken in deze tabel. De tabel kan worden gewijzigd als de Durable Functions extensie wordt ontwikkeld.

## <a name="known-issues"></a>Bekende problemen

Alle bekende problemen moeten worden bijgehouden in de lijst met [github-problemen](https://github.com/Azure/azure-functions-durable-extension/issues) . Als u een probleem ondervindt en u het probleem niet kunt vinden in GitHub, opent u een nieuw probleem. Neem een gedetailleerde beschrijving van het probleem op.

## <a name="next-steps"></a>Volgende stappen

Zie [Durable functions functie typen en-functies](durable-functions-types-features-overview.md)voor meer informatie over Durable functions. 

Aan de slag:

> [!div class="nextstepaction"]
> [Uw eerste duurzame functie maken](durable-functions-create-first-csharp.md)

[DurableOrchestrationContext]: https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html