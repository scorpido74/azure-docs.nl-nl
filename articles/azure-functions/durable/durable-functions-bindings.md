---
title: Bindingen voor duurzame functies - Azure
description: Triggers en bindingen gebruiken voor de extensie Duurzame functies voor Azure-functies.
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 1f42c6c9b0086d49e539040334c83cfc0c6feb42
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278218"
---
# <a name="bindings-for-durable-functions-azure-functions"></a>Bindingen voor duurzame functies (Azure-functies)

De extensie [Duurzame functies](durable-functions-overview.md) introduceert twee nieuwe triggerbindingen die de uitvoering van orchestrator- en activiteitsfuncties regelen. Het introduceert ook een uitvoerbinding die fungeert als klant voor de runtime duurzame functies.

## <a name="orchestration-trigger"></a>Orchestration trigger

Met de orchestration trigger u [duurzame orchestrator-functies schrijven.](durable-functions-types-features-overview.md#orchestrator-functions) Deze trigger ondersteunt het starten van nieuwe orchestrator-functieinstanties en het hervatten van bestaande orchestrator-functie-exemplaren die "wachten" op een taak.

Wanneer u de hulpprogramma's voor Visual Studio voor Azure-functies gebruikt, wordt de orchestration-trigger geconfigureerd met het kenmerk [OrchestrationTriggerAttribute](https://docs.microsoft.com/dotnet/api/Microsoft.Azure.WebJobs.Extensions.DurableTask.OrchestrationTriggerAttribute?view=azure-dotnet) .NET.

Wanneer u orchestrator-functies schrijft in scripttalen (bijvoorbeeld JavaScript of C#scripting), wordt de orchestration-trigger gedefinieerd door het volgende JSON-object in de `bindings` array van het *function.json-bestand:*

```json
{
    "name": "<Name of input parameter in function signature>",
    "orchestration": "<Optional - name of the orchestration>",
    "type": "orchestrationTrigger",
    "direction": "in"
}
```

* `orchestration`is de naam van de orchestration die clients moeten gebruiken wanneer ze nieuwe exemplaren van deze orchestrator-functie willen starten. Deze eigenschap is optioneel. Als dit niet is opgegeven, wordt de naam van de functie gebruikt.

Intern leidt dit tot bindende peilingen van een reeks wachtrijen in het standaardopslagaccount voor de functie-app. Deze wachtrijen zijn interne implementatiedetails van de extensie, daarom zijn ze niet expliciet geconfigureerd in de bindingseigenschappen.

### <a name="trigger-behavior"></a>Triggergedrag

Hier zijn enkele opmerkingen over de orchestration trigger:

* **Single-threading** - Een enkele dispatcher thread wordt gebruikt voor alle orchestrator functie uitvoering op een enkele host instantie. Daarom is het belangrijk om ervoor te zorgen dat orchestrator-functiecode efficiënt is en geen I/O uitvoert. Het is ook belangrijk om ervoor te zorgen dat deze thread geen async-werk doet, behalve wanneer u wacht op duurzame functies-specifieke taaktypen.
* **Poison-message handling** - Er is geen gif bericht ondersteuning in orchestration triggers.
* **Zichtbaarheid van berichten** - De triggerberichten van orchestration worden in de wachtrij geplaatst en onzichtbaar gehouden voor een configureerbare duur. De zichtbaarheid van deze berichten wordt automatisch vernieuwd zolang de functie-app actief en gezond is.
* **Retourwaarden** : Retourwaarden worden geserialiseerd naar JSON en blijven bestaan in de tabel orchestration history in Azure Table-opslag. Deze retourwaarden kunnen worden opgevraagd door de orchestration client binding, later beschreven.

> [!WARNING]
> Orchestrator-functies mogen nooit andere invoer- of uitvoerbindingen gebruiken dan de orchestration trigger binding. Hierdoor kan problemen met de extensie Duurzame taak ontstaan, omdat deze bindingen mogelijk niet voldoen aan de single-threading- en I/O-regels. Als u andere bindingen wilt gebruiken, voegt u deze toe aan een activiteitsfunctie die vanuit de functie Orchestrator wordt aangeroepen.

> [!WARNING]
> JavaScript orchestrator-functies mogen `async`nooit worden gedeclareerd .

### <a name="trigger-usage-net"></a>Gebruik activeren (.NET)

De orchestration trigger binding ondersteunt zowel ingangen als uitgangen. Hier volgen enkele dingen die u moet weten over invoer- en uitvoerafhandeling:

* **ingangen** - .NET-orkestratiefuncties ondersteunen alleen `DurableOrchestrationContext` als parametertype. Deserialisatie van ingangen direct in de functiehandtekening wordt niet ondersteund. Code moet `GetInput<T>` de methode (.NET) of `getInput` (JavaScript) gebruiken om de functie-ingangen van orchestrator op te halen. Deze ingangen moeten JSON-serializable types zijn.
* **uitgangen** - Orchestration activeert ondersteuningsuitvoerwaarden en ingangen. De retourwaarde van de functie wordt gebruikt om de uitvoerwaarde toe te wijzen en moet JSON-serializable zijn. Als een .NET-functie retourneert `Task` of `void`een `null` waarde wordt opgeslagen als de uitvoer.

### <a name="trigger-sample"></a>Voorbeeld van trigger

De volgende voorbeeldcode laat zien hoe de eenvoudigste orchestrator-functie "Hello World" eruit zou kunnen zien:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static string Run([OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    return $"Hello {name}!";
}
```
> [!NOTE]
> De vorige code is voor duurzame functies 2.x. Voor duurzame functies 1.x `DurableOrchestrationContext` moet `IDurableOrchestrationContext`u in plaats van . Zie het artikel [Duurzame functiesversies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

#### <a name="javascript-functions-20-only"></a>JavaScript (alleen functies 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    return `Hello ${name}!`;
});
```

> [!NOTE]
> Het `context` object in JavaScript vertegenwoordigt niet de SustainableOrchestrationContext, maar de [functiecontext als geheel.](../functions-reference-node.md#context-object) U hebt toegang tot orkestratiemethoden via de `context` eigenschap van `df` het object.

> [!NOTE]
> JavaScript-orchestrators `return`moeten . De `durable-functions` bibliotheek zorgt voor `context.done` het aanroepen van de methode.

De meeste orchestrator-functies roepen activiteitsfuncties op, dus hier is een voorbeeld van "Hello World" dat laat zien hoe je een activiteitsfunctie aanroept:

#### <a name="c"></a>C#

```csharp
[FunctionName("HelloWorld")]
public static async Task<string> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    string name = context.GetInput<string>();
    string result = await context.CallActivityAsync<string>("SayHello", name);
    return result;
}
```

> [!NOTE]
> De vorige code is voor duurzame functies 2.x. Voor duurzame functies 1.x `DurableOrchestrationContext` moet `IDurableOrchestrationContext`u in plaats van . Zie het artikel [Duurzame functies voor](durable-functions-versions.md) meer informatie over de verschillen tussen versies.

#### <a name="javascript-functions-20-only"></a>JavaScript (alleen functies 2.0)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context) {
    const name = context.df.getInput();
    const result = yield context.df.callActivity("SayHello", name);
    return result;
});
```

## <a name="activity-trigger"></a>Activiteitstrigger

Met de activiteitstrigger u functies schrijven die worden aangeroepen door orchestrator-functies, ook wel [activiteitsfuncties genoemd.](durable-functions-types-features-overview.md#activity-functions)

Als u Visual Studio gebruikt, wordt de activiteitstrigger geconfigureerd met het `ActivityTriggerAttribute` kenmerk .NET.

Als u VS-code of de Azure-portal voor ontwikkeling gebruikt, wordt de activiteitstrigger gedefinieerd door het volgende JSON-object in de `bindings` array van *function.json:*

```json
{
    "name": "<Name of input parameter in function signature>",
    "activity": "<Optional - name of the activity>",
    "type": "activityTrigger",
    "direction": "in"
}
```

* `activity`is de naam van de activiteit. Deze waarde is de naam die orchestrator-functies gebruiken om deze activiteitsfunctie aan te roepen. Deze eigenschap is optioneel. Als dit niet is opgegeven, wordt de naam van de functie gebruikt.

Intern leidt deze trigger tot bindende polls voor een wachtrij in het standaardopslagaccount voor de functie-app. Deze wachtrij is een intern implementatiedetail van de extensie, daarom is deze niet expliciet geconfigureerd in de bindingseigenschappen.

### <a name="trigger-behavior"></a>Triggergedrag

Hier zijn enkele opmerkingen over de activiteit trigger:

* **Threading** - In tegenstelling tot de orchestration trigger, activiteit triggers hebben geen beperkingen rond threading of I / O. Ze kunnen worden behandeld als gewone functies.
* **Poison-message handling** - Er is geen gif bericht ondersteuning in de activiteit triggers.
* **Zichtbaarheid van berichten** - Berichten met activiteitstrigger worden in de wachtrij geplaatst en onzichtbaar gehouden voor een configureerbare duur. De zichtbaarheid van deze berichten wordt automatisch vernieuwd zolang de functie-app actief en gezond is.
* **Retourwaarden** : Retourwaarden worden geserialiseerd naar JSON en blijven bestaan in de tabel orchestration history in Azure Table-opslag.

> [!WARNING]
> De backend voor opslag voor activiteitsfuncties is een implementatiedetail en de gebruikerscode mag niet rechtstreeks met deze opslagentiteiten communiceren.

### <a name="trigger-usage-net"></a>Gebruik activeren (.NET)

De binding van de activiteitstrigger ondersteunt zowel ingangen als uitgangen, net als de orchestration-trigger. Hier volgen enkele dingen die u moet weten over invoer- en uitvoerafhandeling:

* **ingangen** - .NET-activiteitsfuncties die native worden gebruikt `DurableActivityContext` als parametertype. Als alternatief kan een activiteitsfunctie worden gedeclareerd met elk parametertype dat JSON-serializable is. Wanneer u `DurableActivityContext`de invoer `GetInput<T>` van de activiteitsfunctie wilt ophalen en deserialiseren.
* **uitgangen - Activiteitsfuncties** ondersteunen uitvoerwaarden en ingangen. De retourwaarde van de functie wordt gebruikt om de uitvoerwaarde toe te wijzen en moet JSON-serializable zijn. Als een .NET-functie retourneert `Task` of `void`een `null` waarde wordt opgeslagen als de uitvoer.
* **metagegevens** - .NET-activiteitsfuncties kunnen aan een `string instanceId` parameter binden om de instantie-id van de bovenliggende orkestratie te krijgen.

### <a name="trigger-sample"></a>Voorbeeld van trigger

In de volgende voorbeeldcode ziet u hoe een eenvoudige activiteitsfunctie "Hello World" eruit zou kunnen zien:

#### <a name="c"></a>C#

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] IDurableActivityContext helloContext)
{
    string name = helloContext.GetInput<string>();
    return $"Hello {name}!";
}
```

> [!NOTE]
> De vorige code is voor duurzame functies 2.x. Voor duurzame functies 1.x `DurableActivityContext` moet `IDurableActivityContext`u in plaats van . Zie het artikel [Duurzame functiesversies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

Het standaardparametertype voor `ActivityTriggerAttribute` de `IDurableActivityContext`.NET-binding is . .NET-activiteitstriggers ondersteunen echter ook de binding die rechtstreeks aan JSON-serializeable types (inclusief primitieve typen) is gebonden, zodat dezelfde functie als volgt kan worden vereenvoudigd:

```csharp
[FunctionName("SayHello")]
public static string SayHello([ActivityTrigger] string name)
{
    return $"Hello {name}!";
}
```

#### <a name="javascript-functions-20-only"></a>JavaScript (alleen functies 2.0)

```javascript
module.exports = async function(context) {
    return `Hello ${context.bindings.name}!`;
};
```

JavaScript-bindingen kunnen ook worden doorgegeven als aanvullende parameters, zodat dezelfde functie als volgt kan worden vereenvoudigd:

```javascript
module.exports = async function(context, name) {
    return `Hello ${name}!`;
};
```


### <a name="using-input-and-output-bindings"></a>Invoer- en uitvoerbindingen gebruiken

U naast de binding van de activiteitstrigger ook regelmatige invoer- en uitvoerbindingen gebruiken. U bijvoorbeeld de invoer naar uw activiteitsbinding nemen en een bericht naar een EventHub verzenden met de gebeurtenishub-uitvoerbinding:

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

## <a name="orchestration-client"></a>Orchestration client

Met de orchestration client binding u functies schrijven die interageren met orchestrator functies. Deze functies worden soms [clientfuncties](durable-functions-types-features-overview.md#client-functions)genoemd. U bijvoorbeeld op de volgende manieren reageren op orchestration-instanties:

* Begin ze.
* Vraag hun status op.
* Beëindig ze.
* Stuur gebeurtenissen naar hen terwijl ze actief zijn.
* De geschiedenis van de instantie van het zuiveren.

Als u Visual Studio gebruikt, u zich binden `OrchestrationClientAttribute` aan de orchestration-client met behulp van het kenmerk .NET voor duurzame functies 1.0. Vanaf het kenmerk Duurzame functies 2.0 u aan `DurableClientAttribute` de orchestration-client binden met behulp van het kenmerk .NET.

Als u scripttalen (bijvoorbeeld *.csx-* of *.js-bestanden)* gebruikt voor ontwikkeling, wordt de orchestration-trigger `bindings` gedefinieerd door het volgende JSON-object in de array van *function.json:*

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "orchestrationClient",
    "direction": "in"
}
```

* `taskHub`- Wordt gebruikt in scenario's waarin meerdere functie-apps hetzelfde opslagaccount delen, maar van elkaar moeten worden geïsoleerd. Als dit niet is `host.json` opgegeven, wordt de standaardwaarde van gebruikt. Deze waarde moet overeenkomen met de waarde die wordt gebruikt door de functies van de doelorchestrator.
* `connectionName`- De naam van een app-instelling die een tekenreeks voor opslagaccountverbinding bevat. Het opslagaccount dat door deze verbindingstekenreeks wordt weergegeven, moet dezelfde zijn die wordt gebruikt door de functies van de doelorchestrator. Als dit niet is opgegeven, wordt de standaardtekenreeks voor de opslagaccountverbinding voor de functie-app gebruikt.

> [!NOTE]
> In de meeste gevallen raden we u aan deze eigenschappen weg te laten en te vertrouwen op het standaardgedrag.

### <a name="client-usage"></a>Clientgebruik

In .NET-functies bindt u `IDurableOrchestrationClient`meestal aan , waardoor u volledige toegang hebt tot alle API's van orchestrationclient die worden ondersteund door duurzame functies. In de oudere duurzame functies 2.x releases, u in plaats daarvan binden aan de `DurableOrchestrationClient` klasse. In JavaScript worden dezelfde API's weergegeven `getClient`door het object dat wordt geretourneerd van . API's op het clientobject zijn onder andere:

* `StartNewAsync`
* `GetStatusAsync`
* `TerminateAsync`
* `RaiseEventAsync`
* `PurgeInstanceHistoryAsync`
* `CreateCheckStatusResponse`
* `CreateHttpManagementPayload`

Als alternatief kunnen .NET-functies `T` `StartOrchestrationArgs` binden `JObject`aan `IAsyncCollector<T>` waar is of .

Zie de `IDurableOrchestrationClient` API-documentatie voor meer informatie over deze bewerkingen.

### <a name="client-sample-visual-studio-development"></a>Clientvoorbeeld (ontwikkeling van Visual Studio)

Hier is een voorbeeld wachtrij geactiveerdfunctie die een "HelloWorld" orchestration start.

```csharp
[FunctionName("QueueStart")]
public static Task Run(
    [QueueTrigger("durable-function-trigger")] string input,
    [DurableClient] IDurableOrchestrationClient starter)
{
    // Orchestration input comes from the queue message content.
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> De vorige C#-code is voor duurzame functies 2.x. Voor duurzame functies 1.x `OrchestrationClient` moet u `DurableClient` attribuut gebruiken in plaats `DurableOrchestrationClient` van het `IDurableOrchestrationClient`kenmerk en moet u het parametertype gebruiken in plaats van . Zie het artikel [Duurzame functiesversies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

### <a name="client-sample-not-visual-studio"></a>Clientvoorbeeld (niet Visual Studio)

Als u Visual Studio niet gebruikt voor ontwikkeling, u het volgende *function.json-bestand* maken. In dit voorbeeld ziet u hoe u een functie die in de wachtrij wordt geactiveerd, configureren met de binding van de duurzame orchestrationclient:

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
      "type": "durableClient",
      "direction": "in"
    }
  ]
}
```

> [!NOTE]
> De vorige JSON is voor duurzame functies 2.x. Voor duurzame functies 1.x `orchestrationClient` moet u `durableClient` het type activeren als trigger gebruiken. Zie het artikel [Duurzame functiesversies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

Hieronder volgen taalspecifieke voorbeelden die nieuwe orchestrator-functie-exemplaren starten.

#### <a name="c-script-sample"></a>Voorbeeld van C#-script

In het volgende voorbeeld ziet u hoe u de binding met de duurzame orchestrationclient gebruiken om een nieuwe functie-instantie te starten vanuit een c#-functie die in de wachtrij wordt geactiveerd:

```csharp
#r "Microsoft.Azure.WebJobs.Extensions.DurableTask"

using Microsoft.Azure.WebJobs.Extensions.DurableTask;

public static Task Run(string input, IDurableOrchestrationClient starter)
{
    return starter.StartNewAsync("HelloWorld", input);
}
```

> [!NOTE]
> De vorige code is voor duurzame functies 2.x. Voor duurzame functies 1.x moet `DurableOrchestrationClient` u het `IDurableOrchestrationClient`parametertype gebruiken in plaats van . Zie het artikel [Duurzame functiesversies](durable-functions-versions.md) voor meer informatie over de verschillen tussen versies.

#### <a name="javascript-sample"></a>JavaScript-voorbeeld

In het volgende voorbeeld ziet u hoe u de binding met de duurzame orchestrationclient gebruiken om een nieuwe functie-instantie te starten vanuit een JavaScript-functie:

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    return instanceId = await client.startNew("HelloWorld", undefined, context.bindings.input);
};
```

Meer informatie over startinstanties vindt u in [Instantiebeheer](durable-functions-instance-management.md).

## <a name="entity-trigger"></a>Entiteitstrigger

Met entiteitstriggers u [entiteitsfuncties opmaken.](durable-functions-entities.md) Deze trigger ondersteunt verwerkingsgebeurtenissen voor een specifieke entiteitsinstantie.

Wanneer u de hulpprogramma's voor Visual Studio voor Azure-functies gebruikt, wordt de entiteitstrigger geconfigureerd met het `EntityTriggerAttribute` kenmerk .NET.

> [!NOTE]
> Entiteittriggers zijn beschikbaar vanaf duurzame functies 2.x.

Intern leidt dit tot bindende peilingen van een reeks wachtrijen in het standaardopslagaccount voor de functie-app. Deze wachtrijen zijn interne implementatiedetails van de extensie, daarom zijn ze niet expliciet geconfigureerd in de bindingseigenschappen.

### <a name="trigger-behavior"></a>Triggergedrag

Hier volgen enkele opmerkingen over de entiteitstrigger:

* **Single-threaded**: Een enkele dispatcher thread wordt gebruikt om bewerkingen voor een bepaalde entiteit te verwerken. Als meerdere berichten tegelijkertijd naar één entiteit worden verzonden, worden de bewerkingen één-op-één verwerkt.
* **Poison-message handling** - Er is geen gif bericht ondersteuning in entiteit triggers.
* **Zichtbaarheid van berichten** - Entiteittriggerberichten worden in de wachtrij geplaatst en onzichtbaar gehouden voor een configureerbare duur. De zichtbaarheid van deze berichten wordt automatisch vernieuwd zolang de functie-app actief en gezond is.
* **Retourwaarden** : entiteitsfuncties ondersteunen geen retourwaarden. Er zijn specifieke API's die kunnen worden gebruikt om status op te slaan of waarden terug te geven aan orkestraties.

Alle statuswijzigingen die tijdens de uitvoering in een entiteit zijn aangebracht, worden automatisch gehandhaafd nadat de uitvoering is voltooid.

### <a name="trigger-usage-net"></a>Gebruik activeren (.NET)

Elke entiteitsfunctie heeft `IDurableEntityContext`een parametertype van , dat de volgende leden heeft:

* **EntityName:** de naam van de momenteel uitvoerende entiteit.
* **EntityKey:** de sleutel van de momenteel uitvoerende entiteit.
* **EntityId:** de ID van de momenteel uitvoerende entiteit.
* **OperationName:** de naam van de huidige bewerking.
* **HasState**: of de entiteit bestaat, dat wil zeggen, heeft een staat. 
* **GetState\<TState>()**: krijgt de huidige status van de entiteit. Als het nog niet bestaat, wordt het `default<TState>`gemaakt en geïnitialiseerd tot . De `TState` parameter moet een primitief of JSON-serialiserend type zijn. 
* **GetState\<TState>(initfunction)**: krijgt de huidige status van de entiteit. Als deze nog niet bestaat, wordt het `initfunction` gemaakt door de opgegeven parameter aan te roepen. De `TState` parameter moet een primitief of JSON-serialiserend type zijn. 
* **SetState(arg)**: maakt of werkt de status van de entiteit aan. De `arg` parameter moet een JSON-serializeable object of primitief zijn.
* **DeleteState()**: verwijdert de status van de entiteit. 
* **GetInput\<TInput>()**: krijgt de invoer voor de huidige bewerking. De `TInput` parameter type moet een primitief of JSON-serialiserend type zijn.
* **Return(arg)**: retourneert een waarde naar de orchestration die de bewerking heeft genoemd. De `arg` parameter moet een primitief of JSON-serialiserend object zijn.
* **SignalEntity(EntityId, scheduledTimeUtc, operation, input)**: stuurt een eenrichtingsbericht naar een entiteit. De `operation` parameter moet een niet-null-tekenreeks zijn, de optionele `scheduledTimeUtc` moet een UTC-datum zijn waarop de bewerking moet worden aanroepen en de `input` parameter moet een primitief of JSON-serialiserend object zijn.
* **CreateNewOrchestration(orchestratorFunctionName, input)**: start een nieuwe orchestration. De `input` parameter moet een primitief of JSON-serialiserend object zijn.

Het `IDurableEntityContext` object dat is doorgegeven aan `Entity.Current` de entiteitsfunctie, kan worden geopend met behulp van de eigenschap async-local. Deze aanpak is handig bij het gebruik van het op klasse gebaseerde programmeermodel.

### <a name="trigger-sample-c-function-based-syntax"></a>Voorbeeld van trigger (syntaxis van de functie C#)

De volgende code is een voorbeeld van een eenvoudige *Counter entiteit* geïmplementeerd als een duurzame functie. Deze functie definieert `add` `reset`drie `get`bewerkingen, , en , die elk werken op een gehele staat.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            ctx.SetState(ctx.GetState<int>() + ctx.GetInput<int>());
            break;
        case "reset":
            ctx.SetState(0);
            break;
        case "get":
            ctx.Return(ctx.GetState<int>()));
            break;
    }
}
```

Zie [Op functie gebaseerde syntaxis](durable-functions-dotnet-entities.md#function-based-syntax)voor meer informatie over de op de functie gebaseerde syntaxis en hoe deze te gebruiken.

### <a name="trigger-sample-c-class-based-syntax"></a>Voorbeeld van trigger (syntaxis op basis van de klasse C#)

Het volgende voorbeeld is een `Counter` gelijkwaardige implementatie van de entiteit met behulp van klassen en methoden.

```csharp
[JsonObject(MemberSerialization.OptIn)]
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

De status van deze entiteit `Counter`is een object van type , dat een veld bevat dat de huidige waarde van de teller opslaat. Om dit object in opslag te blijven gebruiken, wordt het geserialiseerd en gedeserialiseerd door de [Json.NET](https://www.newtonsoft.com/json) bibliotheek. 

Zie [Entiteitklassen definiëren](durable-functions-dotnet-entities.md#defining-entity-classes)voor meer informatie over de syntaxis van de klasse en hoe deze te gebruiken.

> [!NOTE]
> De functie-invoerpuntmethode `[FunctionName]` met het `static` kenmerk *moet* worden gedeclareerd bij het gebruik van entiteitsklassen. Niet-statische invoerpuntmethoden kunnen leiden tot meervoudige objectinitialisatie en mogelijk andere ongedefinieerde gedragingen.

Entiteitsklassen hebben speciale mechanismen voor interactie met bindingen en .NET-afhankelijkheidsinjectie. Zie [Entiteitsconstructie](durable-functions-dotnet-entities.md#entity-construction)voor meer informatie .

### <a name="trigger-sample-javascript"></a>Voorbeeld van trigger (JavaScript)

De volgende code is een voorbeeld van een eenvoudige *Counter entiteit* geïmplementeerd als een duurzame functie geschreven in JavaScript. Deze functie definieert `add` `reset`drie `get`bewerkingen, , en , die elk werken op een gehele staat.

**function.json**
```json
{
  "bindings": [
    {
      "name": "context",
      "type": "entityTrigger",
      "direction": "in"
    }
  ],
  "disabled": false
}
```

**index.js**
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

> [!NOTE]
> Duurzame entiteiten zijn beschikbaar in JavaScript vanaf versie `durable-functions` **1.3.0** van het npm-pakket.

## <a name="entity-client"></a>Entiteitclient

Met de entiteitsclientbinding u [entiteitsfuncties](#entity-trigger)asynchroon activeren. Deze functies worden soms [clientfuncties](durable-functions-types-features-overview.md#client-functions)genoemd.

Als u Visual Studio gebruikt, u zich binden `DurableClientAttribute` aan de entiteitsclient met behulp van het kenmerk .NET.

> [!NOTE]
> Het `[DurableClientAttribute]` kan ook worden gebruikt om te binden aan de [orchestration client](#orchestration-client).

Als u scripttalen (bijvoorbeeld *.csx-* of *.js-bestanden)* gebruikt voor ontwikkeling, wordt de entiteitstrigger `bindings` gedefinieerd door het volgende JSON-object in de array van *function.json:*

```json
{
    "name": "<Name of input parameter in function signature>",
    "taskHub": "<Optional - name of the task hub>",
    "connectionName": "<Optional - name of the connection string app setting>",
    "type": "durableClient",
    "direction": "in"
}
```

* `taskHub`- Wordt gebruikt in scenario's waarin meerdere functie-apps hetzelfde opslagaccount delen, maar van elkaar moeten worden geïsoleerd. Als dit niet is `host.json` opgegeven, wordt de standaardwaarde van gebruikt. Deze waarde moet overeenkomen met de waarde die wordt gebruikt door de functies van de doelentiteit.
* `connectionName`- De naam van een app-instelling die een tekenreeks voor opslagaccountverbinding bevat. Het opslagaccount dat door deze verbindingstekenreeks wordt weergegeven, moet dezelfde zijn die wordt gebruikt door de functies van de doelentiteit. Als dit niet is opgegeven, wordt de standaardtekenreeks voor de opslagaccountverbinding voor de functie-app gebruikt.

> [!NOTE]
> In de meeste gevallen raden we u aan de optionele eigenschappen weg te laten en te vertrouwen op het standaardgedrag.

### <a name="entity-client-usage"></a>Clientgebruik van entiteiten

In .NET-functies bindt u `IDurableEntityClient`doorgaans aan , waardoor u volledige toegang hebt tot alle client-API's die worden ondersteund door duurzame entiteiten. U ook `IDurableOrchestrationClient` binden aan de interface, die toegang biedt tot client-API's voor zowel entiteiten als orkestraties. API's op het clientobject zijn onder andere:

* **ReadEntityStateAsync\<T>: **leest de status van een entiteit. Het retourneert een antwoord dat aangeeft of de doelentiteit bestaat, en zo ja, wat de status ervan is.
* **SignalEntityAsync**: stuurt een eenrichtingsbericht naar een entiteit en wacht tot het in de wachtrij staat.
* **ListEntitiesAsync**: query's voor de status van meerdere entiteiten. Entiteiten kunnen worden opgevraagd op *naam* en *laatste bewerkingstijd*.

Het is niet nodig om de doelentiteit te maken voordat u een signaal verzendt - de entiteitsstatus kan worden gemaakt vanuit de entiteitsfunctie die het signaal verwerkt.

> [!NOTE]
> Het is belangrijk om te begrijpen dat de "signalen" verzonden van de client zijn gewoon in de wachtrij, om asynchroon worden verwerkt op een later tijdstip. In het `SignalEntityAsync` bijzonder, de meestal rendementen voordat de entiteit zelfs begint met de operatie, en het is niet mogelijk om terug te krijgen van de terugkeer waarde of uitzonderingen observeren. Als er sterkere garanties nodig zijn (bijvoorbeeld voor werkstromen), moeten *orchestrator-functies* worden gebruikt, die kunnen wachten tot entiteitsbewerkingen zijn voltooid, en retourwaarden kunnen verwerken en uitzonderingen kunnen observeren.

### <a name="example-client-signals-entity-directly---c"></a>Voorbeeld: entiteit voor clientsignalen rechtstreeks - C #

Hier is een voorbeeld functie die in de wachtrij wordt geactiveerd en die een entiteit 'Teller' aanroept.

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

### <a name="example-client-signals-entity-via-interface---c"></a>Voorbeeld: clientsignalen entiteit via interface - C #

Waar mogelijk raden we aan [om toegang te krijgen tot entiteiten via interfaces,](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) omdat het meer typecontrole biedt. Stel dat de `Counter` eerder genoemde entiteit `ICounter` een interface heeft geïmplementeerd, gedefinieerd als volgt:

```csharp
public interface ICounter
{
    void Add(int amount);
    void Reset();
    Task<int> Get();
}

public class Counter : ICounter
{
    // ...
}
```

Clientcode kan `SignalEntityAsync<ICounter>` vervolgens worden gebruikt om een typeveilige proxy te genereren:

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

De `proxy` parameter is een dynamisch `ICounter`gegenereerde instantie van , `Add` die de aanroep intern `SignalEntityAsync`vertaalt naar de gelijkwaardige (niet-getypte) aanroep naar .

> [!NOTE]
> De `SignalEntityAsync` API's vertegenwoordigen eenrichtingsoperaties. Als een entiteit `Task<T>`interfaces retourneert, is de waarde van de `T` parameter altijd null of `default`.

In het bijzonder heeft het geen `Get` zin om de bewerking te signaleren, omdat er geen waarde wordt geretourneerd. In plaats daarvan `ReadStateAsync` kunnen clients rechtstreeks toegang krijgen tot de tellerstatus of `Get` een orchestrator-functie starten die de bewerking aanroept.

### <a name="example-client-signals-entity---javascript"></a>Voorbeeld: entiteit clientsignalen - JavaScript

Hier is een voorbeeld wachtrij geactiveerde functie die een "Counter" entiteit in JavaScript signalen.

**function.json**
```json
{
    "bindings": [
      {
        "name": "input",
        "type": "queueTrigger",
        "queueName": "durable-entity-trigger",
        "direction": "in",
      },
      {
        "name": "starter",
        "type": "durableClient",
        "direction": "in"
      }
    ],
    "disabled": false
  }
```

**index.js**
```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

> [!NOTE]
> Duurzame entiteiten zijn beschikbaar in JavaScript vanaf versie `durable-functions` **1.3.0** van het npm-pakket.

<a name="host-json"></a>
## <a name="hostjson-settings"></a>host.json-instellingen

[!INCLUDE [durabletask](../../../includes/functions-host-json-durabletask.md)]

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Ingebouwde HTTP API-referentie voor bijvoorbeeld beheer](durable-functions-http-api.md)
