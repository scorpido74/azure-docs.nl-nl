---
title: Bindingen voor Durable Functions-Azure
description: Triggers en bindingen gebruiken voor de uitbrei ding van de Durable Functions voor Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: azfuncdf
ms.openlocfilehash: f297c89d2c3ba5692a44fab631c0d46c75f48692
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033587"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Bindingen voor Durable Functions (Azure Functions)

De uitbrei ding [Durable functions](durable-functions-overview.md) introduceert twee nieuwe trigger bindingen die de uitvoering van Orchestrator-en activiteit functies regelen. Er wordt ook een uitvoer binding geïntroduceerd die fungeert als een client voor de Durable Functions runtime.

## <a name="orchestration-trigger"></a>Orchestration-trigger

Met de Orchestration-trigger kunt u [duurzame Orchestrator-functies](durable-functions-types-features-overview.md#orchestrator-functions)ontwerpen. Deze trigger ondersteunt het starten van nieuwe Orchestrator-functie instanties en het hervatten van bestaande Orchestrator-functie instanties die ' wachten op ' een taak.

Wanneer u de Visual Studio-hulpprogram ma's voor Azure Functions gebruikt, wordt de Orchestration-trigger geconfigureerd met het kenmerk [OrchestrationTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationTriggerAttribute.html) .net.

Wanneer u Orchestrator-functies schrijft in script talen (bijvoorbeeld Java script of C# scripting), wordt de Orchestration-trigger gedefinieerd door het volgende JSON-object in de `bindings` matrix van het bestand *Function. json* :

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration`is de naam van de indeling. Dit is de waarde die clients moeten gebruiken wanneer ze nieuwe exemplaren van deze Orchestrator-functie willen starten. Deze eigenschap is optioneel. Als u niets opgeeft, wordt de naam van de functie gebruikt.

Intern deze trigger roept een reeks wacht rijen aan in het standaard opslag account voor de functie-app. Deze wacht rijen zijn interne implementatie details van de uitbrei ding. Daarom worden ze niet expliciet geconfigureerd in de binding eigenschappen.

### <a name="trigger-behavior"></a>Gedrag activeren

Hier volgen enkele opmerkingen over de Orchestration-trigger:

* **Eén threading** : er wordt één dispatcher-thread gebruikt voor alle Orchestrator-functies die worden uitgevoerd op één exemplaar van een host. Daarom is het belang rijk om ervoor te zorgen dat de functie code van Orchestrator efficiënt is en geen I/O uitvoert. Het is ook belang rijk om ervoor te zorgen dat deze thread geen async-werk doet, behalve wanneer u op Durable Functions-specifieke taak typen wacht.
* **Poison-Message Handling** -er is geen ondersteuning voor Poison-berichten in Orchestration-triggers.
* **Bericht zichtbaarheid** : indelings trigger berichten worden niet in de wachtrij geplaatst en blijven onzichtbaar voor een Configureer bare duur. De zicht baarheid van deze berichten wordt automatisch vernieuwd zolang de functie-app wordt uitgevoerd en in orde is.
* **Retour waarden** : retour waarden worden GESERIALISEERD in JSON en persistent gemaakt in de Orchestration-geschiedenis tabel in azure Table Storage. Deze retour waarden kunnen worden opgevraagd door de Orchestrator-client binding, die later wordt beschreven.

> [!WARNING]
> Orchestrator-functies mogen nooit alle invoer-of uitvoer bindingen gebruiken, behalve de Orchestration-trigger binding. Als u dit doet, is het mogelijk om problemen met de extensie duurzame taak te veroorzaken, omdat deze bindingen de enkelvoudige threading en I/O-regels niet kunnen navolgen. Als u andere bindingen wilt gebruiken, voegt u deze toe aan een activiteit functie die wordt aangeroepen vanuit uw Orchestrator-functie.

> [!WARNING]
> Java script Orchestrator-functies mogen nooit worden `async`gedeclareerd.

### <a name="trigger-usage-net"></a>Gebruik van triggers (.NET)

De Orchestration-trigger binding ondersteunt zowel invoer als uitvoer. Hier volgen enkele dingen die u moet weten over de verwerking van de invoer en uitvoer:

* **invoer** -.net-Orchestration-functies ondersteunen alleen [DurableOrchestrationContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html) als parameter type. Het deserialiseren van invoer rechtstreeks in de functie handtekening wordt niet ondersteund. Code moet de [GetInput\<T >](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationContext.html#Microsoft_Azure_WebJobs_DurableOrchestrationContext_GetInput__1)(.net) of `getInput` (Java script)-methode gebruiken om de functie-invoer van Orchestrator te verkrijgen. Deze invoer moet JSON-Serializable typen zijn.
* **outputs** : indelings triggers ondersteunen uitvoer waarden en invoer. De geretourneerde waarde van de functie wordt gebruikt om de uitvoer waarde toe te wijzen en moet JSON serialiseerbaar zijn. Als een .net-functie `Task` retourneert `void`of, `null` wordt een waarde opgeslagen als uitvoer.

### <a name="trigger-sample"></a>Voor beeld trigger

In de volgende voorbeeld code ziet u wat de eenvoudigste functie ' Hallo wereld ' Orchestrator kan zien:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> Het `context` object in Java script vertegenwoordigt niet de DurableOrchestrationContext, maar de [functie context als geheel](../functions-reference-node.md#context-object). U kunt toegang krijgen tot indelings methoden `context` via de `df` eigenschap van het object.

> [!NOTE]
> Java script-Orchestrator moet `return`gebruiken. De `durable-functions` tape wisselaar zorgt ervoor dat de `context.done` methode wordt aangeroepen.

De meeste functies van Orchestrator roepen activiteit functies aan, dus hier volgt een voor beeld van een Hallo wereld dat laat zien hoe u een activiteit functie aanroept:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] DurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

#### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Activiteit trigger

Met de trigger activiteit kunt u functies schrijven die worden aangeroepen door Orchestrator-functies, ook wel [activiteit functies](durable-functions-types-features-overview.md#activity-functions)genoemd.

Als u Visual Studio gebruikt, wordt de activiteit trigger geconfigureerd met het [ActivityTriggerAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.ActivityTriggerAttribute.html) .net-kenmerk.

Als u VS code gebruikt of de Azure portal voor ontwikkeling, wordt de activiteit trigger gedefinieerd door het volgende JSON-object in de `bindings` matrix van *Function. json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity`is de naam van de activiteit. Deze waarde is de naam die door Orchestrator-functies wordt gebruikt om deze activiteit functie aan te roepen. Deze eigenschap is optioneel. Als u niets opgeeft, wordt de naam van de functie gebruikt.

Intern deze trigger roept een wachtrij aan in het standaard opslag account voor de functie-app. Deze wachtrij is een interne implementatie details van de uitbrei ding, wat daarom niet expliciet is geconfigureerd in de binding eigenschappen.

### <a name="trigger-behavior"></a>Gedrag activeren

Hier volgen enkele opmerkingen over de trigger voor activiteiten:

* **Threading** : in tegens telling tot de Orchestration-trigger gelden er geen beperkingen voor threading of I/O. Ze kunnen worden behandeld als normale functies.
* **Poison-verwerking van berichten** -er is geen Poison-bericht ondersteuning in activiteit triggers.
* **Bericht zichtbaarheid** : activiteit trigger berichten worden niet in de wachtrij geplaatst en worden gedurende een Configureer bare duur onzichtbaar bewaard. De zicht baarheid van deze berichten wordt automatisch vernieuwd zolang de functie-app wordt uitgevoerd en in orde is.
* **Retour waarden** : retour waarden worden GESERIALISEERD in JSON en persistent gemaakt in de Orchestration-geschiedenis tabel in azure Table Storage.

> [!WARNING]
> De opslag back-end voor activiteit functies is een implementatie detail en gebruikers code mogen niet rechtstreeks met deze opslag entiteiten werken.

### <a name="trigger-usage-net"></a>Gebruik van triggers (.NET)

De activiteit trigger binding ondersteunt zowel invoer als uitvoer, net als de Orchestration-trigger. Hier volgen enkele dingen die u moet weten over de verwerking van de invoer en uitvoer:

* **invoer** : .net-activiteit functies gebruiken systeem eigen [DurableActivityContext](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html) als parameter type. U kunt ook een functie activity declareren met elk parameter type dat kan worden geserialiseerd. Wanneer u gebruikt `DurableActivityContext`, kunt u [\<GetInput > T](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableActivityContext.html#Microsoft_Azure_WebJobs_DurableActivityContext_GetInput__1) aanroepen om de invoer van de activiteit functie op te halen en te deserialiseren.
* **uitvoer** -activiteit functies ondersteunen uitvoer waarden en invoer. De geretourneerde waarde van de functie wordt gebruikt om de uitvoer waarde toe te wijzen en moet JSON serialiseerbaar zijn. Als een .net-functie `Task` retourneert `void`of, `null` wordt een waarde opgeslagen als uitvoer.
* **meta gegevens** -.net-activiteit functies kunnen worden `string instanceId` gebonden aan een para meter om de exemplaar-id van de bovenliggende indeling op te halen.

### <a name="trigger-sample"></a>Voor beeld trigger

De volgende voorbeeld code laat zien hoe een eenvoudige activiteit functie ' Hallo wereld ' eruit kan zien als:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] DurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

Het standaard parameter type voor de .net `ActivityTriggerAttribute` -binding `DurableActivityContext`is. .NET-activiteit Triggers bieden echter ook ondersteuning voor het rechtstreeks binden aan JSON-serializeable-typen (waaronder primitieve typen), zodat dezelfde functie als volgt kan worden vereenvoudigd:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-2x-only"></a>Java script (alleen functies 2. x)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

Java script-bindingen kunnen ook worden door gegeven als aanvullende para meters, zodat dezelfde functie als volgt kan worden vereenvoudigd:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```


### <a name="using-input-and-output-bindings"></a>Invoer-en uitvoer bindingen gebruiken

U kunt normale invoer-en uitvoer bindingen gebruiken naast de trigger voor het activeren van de activiteit. U kunt bijvoorbeeld de invoer naar uw activiteit binden en een bericht naar een EventHub verzenden met behulp van de EventHub-uitvoer binding:

```json
{
  "bindings": [
    {
      "name": "message",
      "type": "activityTrigger",
      "direction": "in"
    },
    {
      "type": "eventHub",
      "name": "outputEventHubMessage",
      "connection": "EventhubConnectionSetting",
      "eventHubName": "eh_messages",
      "direction": "out"
  }
  ]
}
```

```javascript
module.exports = async function (context) {
    context.bindings.outputEventHubMessage = context.bindings.message;
};
```

## <a name="orchestration-client"></a>Orchestration-client

Met de Orchestrator client binding kunt u functies schrijven die communiceren met Orchestrator-functies. Deze functies worden soms [client functies](durable-functions-types-features-overview.md#client-functions)genoemd. U kunt bijvoorbeeld op de volgende manieren handelen op indelings instanties:

* Start deze.
* Hun status opvragen.
* Deze beëindigen.
* Verzend gebeurtenissen naar hen terwijl ze worden uitgevoerd.
* Exemplaar geschiedenis opschonen.

Als u Visual Studio gebruikt, kunt u verbinding maken met de Orchestration-client met behulp van het [OrchestrationClientAttribute](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.OrchestrationClientAttribute.html) .net-kenmerk voor Durable functions 1,0. Vanaf de preview-versie van Durable functions 2,0 kunt u verbinding maken met de Orchestration-client met `DurableClientAttribute` behulp van het .net-kenmerk.

Als u script talen (bijvoorbeeld *. CSX* -of *. js* -bestanden) gebruikt voor ontwikkeling, wordt de Orchestration-trigger gedefinieerd door het volgende JSON-object in de `bindings` matrix van *Function. json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub`-Wordt gebruikt in scenario's waarbij meerdere functie-apps hetzelfde opslag account delen, maar van elkaar moeten worden geïsoleerd. Als u niets opgeeft, wordt de standaard `host.json` waarde van gebruikt. Deze waarde moet overeenkomen met de waarde die wordt gebruikt door de doel Orchestrator-functies.
* `connectionName`-De naam van een app-instelling die een opslag account bevat connection string. Het opslag account dat door deze connection string wordt weer gegeven, moet hetzelfde zijn als de naam die wordt gebruikt door de doel Orchestrator-functies. Als dat niet is opgegeven, wordt het standaard-opslag account connection string voor de functie-app gebruikt.

> [!NOTE]
> In de meeste gevallen wordt u aangeraden deze eigenschappen over te slaan en te vertrouwen op het standaard gedrag.

### <a name="client-usage"></a>Client gebruik

In .net functions maakt u doorgaans een `DurableOrchestrationClient`binding aan. Dit biedt volledige toegang tot alle client-api's die door Durable functions worden ondersteund. Vanaf Durable functions 2,0 kunt u in plaats daarvan een binding `IDurableOrchestrationClient` met de interface maken. In Java script worden dezelfde Api's weer gegeven door het object dat wordt `getClient`geretourneerd door. Api's op het client object zijn onder andere:

* [StartNewAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_StartNewAsync_)
* [GetStatusAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_GetStatusAsync_)
* [TerminateAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_TerminateAsync_)
* [RaiseEventAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_RaiseEventAsync_)
* [PurgeInstanceHistoryAsync](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_PurgeInstanceHistoryAsync_)
* [CreateCheckStatusResponse](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateCheckStatusResponse_)
* [CreateHttpManagementPayload](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html#Microsoft_Azure_WebJobs_DurableOrchestrationClient_CreateHttpManagementPayload_)

.NET-functies kunnen ook worden gebonden aan `IAsyncCollector<T>` de `T` locatie [StartOrchestrationArgs](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.StartOrchestrationArgs.html) of `JObject`.

Zie de [DurableOrchestrationClient](https://azure.github.io/azure-functions-durable-extension/api/Microsoft.Azure.WebJobs.DurableOrchestrationClient.html) API-documentatie voor meer informatie over deze bewerkingen.

### <a name="client-sample-visual-studio-development"></a>Client-voor beeld (Visual Studio-ontwikkeling)

Hier volgt een voor beeld van een door de wachtrij geactiveerde functie waarmee de indeling HelloWorld wordt gestart.

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [OrchestrationClient] DurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

### <a name="client-sample-not-visual-studio"></a>Client-voor beeld (niet Visual Studio)

Als u Visual Studio niet gebruikt voor ontwikkeling, kunt u het volgende *Function. json* -bestand maken. In dit voor beeld ziet u hoe u een functie die door een wachtrij wordt geactiveerd, configureert die gebruikmaakt van de duurzame Orchestration-client binding:

```json
{
  "bindings": [
    {
      "name": "input",
      "type": "queueTrigger",
      "queueName": "durable-function-trigger",
      "direction": "in"
    },
    {
      "name": "starter",
      "type": "orchestrationClient",
      "direction": "in"
    }
  ]
}
```

Hieronder vindt u taalspecifieke voor beelden die nieuwe Orchestrator-functie instanties starten.

#### <a name="c-sample"></a>Voorbeeld van C#

In het volgende voor beeld ziet u hoe u de duurzame Orchestration-client binding kunt gebruiken om een nieuw functie C# -exemplaar te starten vanuit een script functie:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

public static Task<string> Run(string input, DurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

#### <a name="javascript-sample"></a>JavaScript-voorbeeld

In het volgende voor beeld ziet u hoe u de duurzame Orchestration-client binding kunt gebruiken om een nieuw functie-exemplaar te starten vanuit een Java script-functie:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

Meer informatie over begin instanties vindt u in [exemplaar beheer](durable-functions-instance-management.md).

## <a name="entity-trigger"></a>Entiteits trigger

Met entiteit triggers kunt u [entiteits functies](durable-functions-entities.md)ontwerpen. Deze trigger ondersteunt het verwerken van gebeurtenissen voor een specifiek entiteits exemplaar.

Wanneer u de Visual Studio-hulpprogram ma's voor Azure functions gebruikt, wordt de entiteits trigger `EntityTriggerAttribute` geconfigureerd met het .net-kenmerk.

> [!NOTE]
> Entiteit triggers zijn beschikbaar in Durable Functions 2,0 en hoger. Entiteit triggers zijn nog niet beschikbaar voor Java script.

Intern deze trigger roept een reeks wacht rijen aan in het standaard opslag account voor de functie-app. Deze wacht rijen zijn interne implementatie details van de uitbrei ding. Daarom worden ze niet expliciet geconfigureerd in de binding eigenschappen.

### <a name="trigger-behavior"></a>Gedrag activeren

Hier volgen enkele opmerkingen over de entiteits trigger:

* **Met één thread**: Er wordt één dispatcher-thread gebruikt om bewerkingen voor een bepaalde entiteit te verwerken. Als meerdere berichten gelijktijdig naar één entiteit worden verzonden, worden de bewerkingen één voor een in de tijd verwerkt.
* **Poison-Message Handling** -er is geen ondersteuning voor Poison-berichten in entiteit triggers.
* **Bericht zichtbaarheid** : entiteits trigger berichten worden niet in de wachtrij geplaatst en worden gedurende een Configureer bare duur onzichtbaar bewaard. De zicht baarheid van deze berichten wordt automatisch vernieuwd zolang de functie-app wordt uitgevoerd en in orde is.
* **Retour waarden** : entiteit functies bieden geen ondersteuning voor retour waarden. Er zijn specifieke Api's die kunnen worden gebruikt om de status op te slaan of waarden terug te geven naar Orchestrations.

Status wijzigingen die tijdens de uitvoering van een entiteit zijn aangebracht, worden automatisch behouden nadat de uitvoering is voltooid.

### <a name="trigger-usage-net"></a>Gebruik van triggers (.NET)

Elke entiteit functie heeft het parameter type `IDurableEntityContext`, dat de volgende leden heeft:

* **EntityName**: Hiermee wordt de naam opgehaald van de entiteit die momenteel wordt uitgevoerd.
* **EntityKey**: Hiermee wordt de sleutel opgehaald van de entiteit die momenteel wordt uitgevoerd.
* **EntityId**: Hiermee wordt de ID opgehaald van de entiteit die momenteel wordt uitgevoerd.
* **Operationname**: Hiermee wordt de naam van de huidige bewerking opgehaald.
* **IsNewlyConstructed**: retourneert `true` of de entiteit niet bestaat voordat de bewerking werd uitgevoerd.
* **GetState\<TState > ()** : Hiermee wordt de huidige status van de entiteit opgehaald. De `TState` para meter moet een primitief-of JSON-serializeable-type zijn.
* **SetState (object)** : Hiermee wordt de status van de entiteit bijgewerkt. De `object` para meter moet een primitief-of JSON-serializeable-object zijn.
* **GetInput\<TInput > ()** : Hiermee wordt de invoer voor de huidige bewerking opgehaald. De `TInput` type parameter moet een primitief of JSON-serializeable-type vertegenwoordigen.
* **Return (object)** : retourneert een waarde voor de indeling die de bewerking wordt genoemd. De `object` para meter moet een primitief-of JSON-serializeable-object zijn.
* **DestructOnExit ()** : de entiteit wordt verwijderd nadat de huidige bewerking is voltooid.
* **SignalEntity (EntityId, String, object)** : Hiermee verzendt u een bericht in één richting naar een entiteit. De `object` para meter moet een primitief-of JSON-serializeable-object zijn.

Wanneer u de op klasse gebaseerde entiteit programmeer modus gebruikt, `IDurableEntityContext` kan naar het object worden verwezen met `Entity.Current` behulp van de thread-static-eigenschap.

### <a name="trigger-sample---entity-function"></a>Trigger voorbeeld-entiteit functie

De volgende code is een voor beeld van een entiteit met een eenvoudige *teller* die is geïmplementeerd als een standaard functie. Deze functie definieert drie *bewerkingen*, `add`, `reset`en `get`, die allemaal op een waarde voor een geheel getal worden `currentValue`uitgevoerd.

```csharp
[FunctionName(nameof(Counter))]
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

### <a name="trigger-sample---entity-class"></a>Trigger voorbeeld-entiteits klasse

Het volgende voor beeld is een equivalente implementatie van `Counter` de vorige entiteit met behulp van .net-klassen en-methoden.

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

> [!NOTE]
> De methode van het functie-invoer `[FunctionName]` punt met het kenmerk `static` *moet* worden gedeclareerd bij het gebruik van entity klassen. Niet-statische toegangs punt methoden kunnen leiden tot meervoudige initialisatie van objecten en mogelijk andere niet-gedefinieerde gedragingen.

Entiteits klassen hebben speciale mechanismen voor interactie met bindingen en .NET-afhankelijkheids injectie. Zie het artikel [duurzame entities](durable-functions-entities.md) voor meer informatie.

## <a name="entity-client"></a>Entiteit-client

Met de client binding van de entiteit kunt u de [entiteits functies](#entity-trigger)asynchroon activeren. Deze functies worden soms [client functies](durable-functions-types-features-overview.md#client-functions)genoemd.

Als u Visual Studio gebruikt, kunt u verbinding maken met de entiteit client met behulp `DurableClientAttribute` van het .net-kenmerk.

> [!NOTE]
> De `[DurableClientAttribute]` kan ook worden gebruikt om een binding te maken met de [Orchestration-client](#orchestration-client).

Als u script talen (bijvoorbeeld *. CSX* -of *. js* -bestanden) gebruikt voor ontwikkeling, wordt de entiteit trigger gedefinieerd door het volgende JSON-object in de `bindings` matrix van *Function. json*:

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "out"
}
```

* `taskHub`-Wordt gebruikt in scenario's waarbij meerdere functie-apps hetzelfde opslag account delen, maar van elkaar moeten worden geïsoleerd. Als u niets opgeeft, wordt de standaard `host.json` waarde van gebruikt. Deze waarde moet overeenkomen met de waarde die wordt gebruikt door de functies van de doel entiteit.
* `connectionName`-De naam van een app-instelling die een opslag account bevat connection string. Het opslag account dat door deze connection string wordt weer gegeven, moet hetzelfde zijn als de locatie die wordt gebruikt door de functies van de doel entiteit. Als dat niet is opgegeven, wordt het standaard-opslag account connection string voor de functie-app gebruikt.

> [!NOTE]
> In de meeste gevallen is het raadzaam om de optionele eigenschappen te weglaten en het standaard gedrag te vertrouwen.

### <a name="entity-client-usage"></a>Client gebruik van entiteit

In .net functions maakt u doorgaans een `IDurableEntityClient`binding aan. Dit biedt volledige toegang tot alle client-api's die door duurzame entiteiten worden ondersteund. U kunt ook een binding maken `IDurableClient` met de interface, waarmee u toegang hebt tot client-api's voor zowel entiteiten als Orchestration. Api's op het client object zijn onder andere:

* **ReadEntityStateAsync\<T >** : de status van een entiteit wordt gelezen.
* **SignalEntityAsync**: Hiermee verzendt u een bericht in één richting naar een entiteit, waarna wordt gewacht tot deze in de wachtrij is geplaatst.
* **SignalEntityAsync\<TEntityInterface >** : zelfde als `SignalEntityAsync` maar gebruikt een gegenereerd proxy object van het `TEntityInterface`type.
* **CreateEntityProxy\<TEntityInterface >** : genereert dynamisch een dynamische proxy van het `TEntityInterface` type voor het maken van type veilige aanroepen naar entiteiten.

> [!NOTE]
> Het is belang rijk om te begrijpen dat de eerdere ' Signal '-bewerkingen allemaal asynchroon zijn. Het is niet mogelijk om een entiteits functie aan te roepen en terug te keren naar een retour waarde van een client. Op dezelfde manier `SignalEntityAsync` kan het retour neren voordat de entiteit begint met het uitvoeren van de bewerking. Alleen Orchestrator-functies kunnen entiteits functies synchroon aanroepen en retour waarden verwerken.

Voor `SignalEntityAsync` de api's moet de unieke id van de entiteit worden opgegeven `EntityId`als een. Deze api's nemen eventueel ook de naam van de entiteits bewerking als a `string` en de payload van de bewerking als een JSON-serializeable `object`. Als de doel entiteit niet bestaat, wordt deze automatisch gemaakt met de opgegeven Entiteits-ID.

### <a name="client-sample-untyped"></a>Client-voor beeld (niet-type)

Hier volgt een voor beeld van een door de wachtrij geactiveerde functie die een counter-entiteit aanroept.

```csharp
[FunctionName("AddFromQueue")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableEntityClient client)
{
    // Entity operation input comes from the queue message content.
    var entityId = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(input);
    return client.SignalEntityAsync(entityId, "Add", amount);
}
```

### <a name="client-sample-typed"></a>Client voorbeeld (getypt)

Het is mogelijk om een proxy object te genereren voor type veilige toegang tot entiteits bewerkingen. Voor het genereren van een type veilige proxy moet het entiteits type een interface implementeren. Stel bijvoorbeeld dat de `Counter` entiteit die eerder een `ICounter` interface heeft genoemd, als volgt heeft geïmplementeerd:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    int Get();
}

public class Counter : ICounter
{
    // ...
}
```

Client code kan vervolgens worden `SignalEntityAsync<TEntityInterface>` gebruikt en de `ICounter` interface opgeven als de type parameter voor het genereren van een type-veilige proxy. Dit gebruik van type veilige proxy's wordt geïllustreerd in het volgende code voorbeeld:

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [DurableClient] IDurableEntityClient client)
{
    var target = new EntityId(nameof(Counter), "myCounter");
    int amount = int.Parse(message);
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

In het vorige voor beeld is `proxy` de para meter een dynamisch gegenereerd exemplaar `ICounter`van, waarmee de aanroep naar `Add` de `SignalEntityAsync`equivalente (niet-getypte) aanroep wordt omgezet in.

Er zijn enkele regels voor het definiëren van entiteits interfaces:

* De type parameter `TEntityInterface` in `SignalEntityAsync<TEntityInterface>` moet een interface zijn.
* Entiteits interfaces moeten alleen methoden definiëren.
* De methoden van de entity-interface mogen niet meer dan één para meter definiëren.
* De methoden van de entity `void`-interface moeten `Task<T>` een `T` retour waarde Retour neren, `Task`of hebben.
* Entiteits interfaces moeten precies één concrete implementatie klasse hebben binnen dezelfde assembly (dat wil zeggen, de entiteits klasse).

Als een van deze regels wordt geschonden, wordt tijdens `InvalidOperationException` runtime een gegenereerd. In het uitzonderings bericht wordt uitgelegd welke regel is verbroken.

> [!NOTE]
> De `SignalEntityAsync` api's vertegenwoordigen eenrichtings bewerkingen. Als een entiteit interfaces retourneert `Task<T>`, is de waarde van `T` de para meter altijd Null of `default`.

<a name="host-json"></a>

## <a name="hostjson-settings"></a>instellingen voor host.JSON

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Ingebouwde HTTP API-referentie voor exemplaar beheer](durable-functions-http-api.md)