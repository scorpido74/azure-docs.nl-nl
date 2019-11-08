---
title: Duurzame entiteiten-Azure Functions
description: Meer informatie over duurzame entiteiten en hoe u deze kunt gebruiken in de Durable Functions extensie voor Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: 62ca71e1b42e000f7528a2963793f9bf40663bf3
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818505"
---
# <a name="entity-functions"></a>Entiteit functies

Met entiteits functies worden bewerkingen gedefinieerd voor het lezen en bijwerken van kleine stukjes status, ook wel *duurzame entiteiten*genoemd. Net als Orchestrator functions zijn entiteits functies functies met een speciaal trigger type, *entiteits trigger*. In tegens telling tot Orchestrator-functies, beheren entiteits functies de status van een entiteit expliciet, in plaats van impliciet de status te bepalen via de controle stroom.
Entiteiten bieden een manier om toepassingen te schalen door het werk te verdelen over een groot aantal entiteiten, elk met een zeer grote status.

> [!NOTE]
> Entiteits functies en gerelateerde functionaliteit zijn alleen beschikbaar in Durable Functions 2,0 en hoger.

## <a name="general-concepts"></a>Algemene concepten

Entiteiten gedragen zich op dezelfde manier als kleine services die communiceren via berichten. Elke entiteit heeft een unieke identiteit en een interne status (indien aanwezig). Net als bij Services of objecten voeren entiteiten bewerkingen uit wanneer daarom wordt gevraagd. Wanneer deze wordt uitgevoerd, kan een bewerking de interne status van de entiteit bijwerken. Het kan ook externe services aanroepen en wachten op een reactie. Entiteiten communiceren met andere entiteiten, Orchestrations en clients die gebruikmaken van berichten die impliciet worden verzonden via betrouw bare wacht rijen. 

Om conflicten te voor komen, is het mogelijk dat alle bewerkingen op één entiteit een serieel, dat wil zeggen, een voor een worden uitgevoerd. 

### <a name="entity-id"></a>Entiteit-ID
Entiteiten worden geopend via een unieke id, de *entiteit-id*. Een Entiteits-ID is gewoon een paar teken reeksen waarmee een entiteits exemplaar uniek wordt geïdentificeerd. Het bestaat uit:

* De **naam**van een entiteit: een naam die het type van de entiteit aangeeft (bijvoorbeeld ' counter '). Deze naam moet overeenkomen met de naam van de entiteit functie die de entiteit implementeert. Het is niet hoofdletter gevoelig.
* Een **entiteits sleutel**: een teken reeks waarmee de entiteit wordt aangeduid met een unieke naam (bijvoorbeeld een GUID) van alle andere entiteiten.

Zo kan een *Counter* entity-functie worden gebruikt om de score in een online spel te bewaren. Elk exemplaar van het spel heeft een unieke entiteit-ID, zoals `@Counter@Game1`, `@Counter@Game2`, enzovoort. Alle bewerkingen die gericht zijn op een bepaalde entiteit, moeten een entiteit-ID opgeven als para meter.

### <a name="entity-operations"></a>Entiteits bewerkingen ###

Als u een bewerking wilt aanroepen voor een entiteit, geeft u er een op

* De *entiteit-id* van de doel entiteit
* De *naam*van de bewerking, een teken reeks die de bewerking specificeert die moet worden uitgevoerd. De entiteit item kan bijvoorbeeld de bewerkingen ' toevoegen ', ' ophalen ' of ' opnieuw instellen ' ondersteunen.
* De *invoer*van de bewerking, een optionele invoer parameter voor de bewerking. De bewerking ' toevoegen ' kan bijvoorbeeld een geheel getal als invoer hebben.

Bewerkingen kunnen resulteren in een resultaat waarde of een fout resultaat (zoals een Java script-fout of een .NET-uitzonde ring). Dit resultaat of deze fout kan worden waargenomen door integraties die de bewerking worden genoemd.

Een entiteits bewerking kan ook de status van de entiteit maken, lezen, bijwerken en verwijderen. De status van de entiteit is altijd blijvend behouden in de opslag.

## <a name="defining-entities"></a>Entiteiten definiëren

We bieden momenteel twee verschillende Api's voor het definiëren van entiteiten.

Een **syntaxis op basis van een functie** waarbij entiteiten worden weer gegeven als functies en bewerkingen expliciet worden verzonden door de toepassing. Deze syntaxis werkt goed voor entiteiten met eenvoudige status, weinig bewerkingen of een dynamische set bewerkingen (zoals in Application Frameworks). Het kan echter lastig zijn om te onderhouden, omdat het geen type fouten tijdens het compileren ophaalt.

Een **op klassen gebaseerde syntaxis** waarbij entiteiten en bewerkingen worden vertegenwoordigd door klassen en methoden. Deze syntaxis produceert eenvoudiger lees bare code en maakt het mogelijk om bewerkingen op een type veilige manier te activeren. De op klassen gebaseerde syntaxis is slechts een smalle laag bovenop de syntaxis op basis van functies, zodat beide varianten door elkaar kunnen worden gebruikt in dezelfde toepassing.

### <a name="example-function-based-syntax---c"></a>Voor beeld: syntaxis op basis van een functie-C#

De volgende code is een voor beeld van een eenvoudige *teller* -entiteit die is geïmplementeerd als een duurzame functie. Met deze functie worden drie bewerkingen, `add`, `reset`en `get`gedefinieerd, die allemaal werken met een gehele status.

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
            ctx.Return(ctx.GetState<int>());
            break;
    }
}
```

Zie [syntaxis op basis van functies](durable-functions-dotnet-entities.md#function-based-syntax)voor meer informatie over de syntaxis van de functie en hoe u deze kunt gebruiken.

### <a name="example-class-based-syntax---c"></a>Voor beeld: op klasse gebaseerde syntaxis-C#

Het volgende voor beeld is een equivalente implementatie van de `Counter` entiteit met klassen en methoden.

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

De status van deze entiteit is een object van het type `Counter`, dat een veld bevat waarin de huidige waarde van de teller wordt opgeslagen. Als u dit object in de opslag ruimte wilt behouden, wordt het geserialiseerd en gedeserialiseerd door de [JSON.net](https://www.newtonsoft.com/json) -bibliotheek. 

Zie [entity branches definiëren](durable-functions-dotnet-entities.md#defining-entity-classes)voor meer informatie over de op klassen gebaseerde syntaxis en hoe u deze kunt gebruiken.

### <a name="example-javascript-entity"></a>Voor beeld: Java script-entiteit

Duurzame entiteiten zijn beschikbaar in Java script vanaf versie **1.3.0** van het NPM-pakket van `durable-functions`. De volgende code is de entiteit *teller* die is geïmplementeerd als een duurzame functie die is geschreven in Java script.

**function. json**
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

**index. js**
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

## <a name="accessing-entities"></a>Toegang tot entiteiten

U kunt toegang krijgen tot entiteiten via eenrichtings-of twee richtings communicatie. We gebruiken de volgende termen om onderscheid te maken: 

* Het **aanroepen** van een entiteit betekent dat er twee richtings communicatie (round-trip) wordt gebruikt: we sturen een bewerkings bericht naar de entiteit en wachten vervolgens op het antwoord bericht voordat u doorgaat. Het antwoord bericht kan een resultaat waarde of een fout resultaat (zoals een Java script-fout of een .NET-uitzonde ring) bevatten. Dit resultaat of deze fout wordt vervolgens waargenomen door de oproepende functie.
* Wanneer een entiteit wordt **gesignaleerd** , wordt de communicatie in één richting (vuur en verg eten) gebruikt. er wordt een bewerkings bericht verzonden, maar u hoeft niet te wachten op een reactie. Wanneer het bericht wordt gegarandeerd uiteindelijk wordt bezorgd, weet de afzender niet wanneer en kunnen er geen resultaat waarden of fouten worden gevonden.

Entiteiten kunnen worden geopend vanuit client functies, vanuit Orchestrator-functies, of vanuit entiteits functies. Niet alle communicatie vormen worden door alle contexten ondersteund:

* Vanuit clients kunt u een *signaal* sturen naar entiteiten en kunt u de status van de entiteit *lezen* .
* Vanuit Orchestrations kunt u entiteiten *Signa leren* en kunt u entiteiten *aanroepen* .
* Vanuit entiteiten kunt u aangeven dat entiteiten moeten worden *gesignaleerd* .

Hieronder ziet u enkele voor beelden die deze verschillende manieren van toegang tot entiteiten illustreren.

> [!NOTE]
> In de onderstaande voor beelden ziet u de syntaxis die u hebt opgegeven voor het openen van entiteiten. Over het algemeen raden wij aan [om toegang te krijgen tot entiteiten via interfaces](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) , omdat het meer type controle biedt.

### <a name="example-client-signals-an-entity"></a>Voor beeld: client geeft een entiteit aan

Voor toegang tot entiteiten van een normale Azure-functie-ook wel bekend als *client functie* : gebruik de [entiteit client uitvoer binding](durable-functions-bindings.md#entity-client). In het volgende voor beeld ziet u een door de wachtrij geactiveerde functie waarmee een entiteit wordt *gesignaleerd* met behulp van deze binding.

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

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await context.df.signalEntity(entityId, "add", 1);
};
```

De term *Signal* geeft aan dat de aanroep van de entiteits-API in één richting en asynchroon is. Het is niet mogelijk dat een *client functie* weet wanneer de entiteit de bewerking heeft verwerkt. De client functie kan ook geen resultaat waarden of uitzonde ringen observeren. 

### <a name="example-client-reads-an-entity-state"></a>Voor beeld: client leest de status van een entiteit

Client functies kunnen ook een query uitvoeren op de status van een entiteit, zoals wordt weer gegeven in het volgende voor beeld:

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    JObject state = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, state);
}
```

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    return context.df.readEntityState(entityId);
};
```

Entiteits status query's worden verzonden naar het duurzame opslag archief en retour neren de laatst *persistente* status van de entiteit. Deze status is altijd een ' toegezegde ' status, dat wil zeggen dat het nooit een tijdelijke tussenliggende status is die wordt aangenomen in het midden van het uitvoeren van een bewerking. Het is echter mogelijk dat deze status is verlopen ten opzichte van de status in het geheugen van de entiteit. Alleen indelingen kunnen de in-memory status van een entiteit lezen, zoals wordt beschreven in de volgende sectie.

### <a name="example-orchestration-signals-and-calls-an-entity"></a>Voor beeld: Orchestration signalen en aanroepen van een entiteit

Orchestrator-functies hebben toegang tot entiteiten met behulp van Api's in de [Orchestration-trigger binding](durable-functions-bindings.md#orchestration-trigger). De volgende voorbeeld code toont een Orchestrator-functie waarmee een *item* entiteit wordt *aangeroepen* en *gesignaleerd* .

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // One-way signal to the entity which updates the value - does not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const entityId = new df.EntityId("Counter", "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    currentValue = yield context.df.callEntity(entityId, "get");
    if (currentValue < 10) {
        // One-way signal to the entity which updates the value - does not await a response
        yield context.df.signalEntity(entityId, "add", 1);
    }
});
```

Alleen indelingen kunnen het aanroepen van entiteiten en het verkrijgen van een reactie zijn, wat een retour waarde of een uitzonde ring kan zijn. Met client functies die gebruikmaken van de [client binding](durable-functions-bindings.md#entity-client) kunnen alleen entiteiten worden *gesignaleerd* .

> [!NOTE]
> Het aanroepen van een entiteit vanuit een Orchestrator-functie is vergelijkbaar met het aanroepen van een [activiteit functie](durable-functions-types-features-overview.md#activity-functions) vanuit een Orchestrator-functie. Het belangrijkste verschil is dat entiteits functies duurzame objecten zijn met een adres (de *entiteit-id*) en ze ondersteunen het opgeven van een bewerkings naam. Activiteit functies zijn daarentegen stateless en hebben niet het concept van bewerkingen.

### <a name="example-entity-signals-an-entity"></a>Voor beeld: entiteit signaleert een entiteit

Een functie entiteit kan signalen verzenden naar andere entiteiten (of zelfs zichzelf!) tijdens het uitvoeren van een bewerking.
We kunnen bijvoorbeeld het bovenstaande voor beeld van de meter entiteit wijzigen, zodat er een ' mijl paal is bereikt ' wordt verzonden naar een bepaalde monitor entiteit wanneer de teller de waarde 100 bereikt:

```csharp
   case "add":
        var currentValue = ctx.GetState<int>();
        var amount = ctx.GetInput<int>();
        if (currentValue < 100 && currentValue + amount >= 100)
        {
            ctx.SignalEntity(new EntityId("MonitorEntity", ""), "milestone-reached", ctx.EntityKey);
        }

        ctx.SetState(currentValue + amount);
        break;
```

```javascript
    case "add":
        const amount = context.df.getInput();
        if (currentValue < 100 && currentValue + amount >= 100) {
            const entityId = new df.EntityId("MonitorEntity", "");
            context.df.signalEntity(entityId, "milestone-reached", context.df.instanceId);
        }
        context.df.setState(currentValue + amount);
        break;
```

## <a name="entity-coordination"></a>Entiteits coördinatie

Het kan voor komen dat u bewerkingen tussen meerdere entiteiten moet coördineren. In een bank toepassing kunt u bijvoorbeeld entiteiten hebben die individuele Bank rekeningen vertegenwoordigen. Wanneer u fondsen van het ene naar het andere account overbrengt, moet u ervoor zorgen dat het _bron_ account voldoende fondsen heeft en dat de updates van de _bron_ -en _doel_ accounts op een transactioneel consistente manier worden uitgevoerd.

### <a name="example-transfer-funds-c"></a>Voor beeld: overdracht fondsenC#()

Met de volgende voorbeeld code worden fondsen tussen twee _account_ entiteiten overgedragen met behulp van een Orchestrator-functie. Voor het coördineren van entiteits updates moet u de `LockAsync` methode gebruiken om een _kritieke sectie_ in de indeling te maken:

> [!NOTE]
> Voor het gemak wordt in dit voor beeld de eerder gedefinieerde `Counter` entiteit opnieuw gebruikt. In een echte toepassing is het echter beter om in plaats daarvan een meer gedetailleerde `BankAccount` entiteit te definiëren.

```csharp
// This is a method called by an orchestrator function
public static async Task<bool> TransferFundsAsync(
    string sourceId,
    string destinationId,
    int transferAmount,
    IDurableOrchestrationContext context)
{
    var sourceEntity = new EntityId(nameof(Counter), sourceId);
    var destinationEntity = new EntityId(nameof(Counter), destinationId);

    // Create a critical section to avoid race conditions.
    // No operations can be performed on either the source or
    // destination accounts until the locks are released.
    using (await context.LockAsync(sourceEntity, destinationEntity))
    {
        ICounter sourceProxy = 
            context.CreateEntityProxy<ICounter>(sourceEntity);
        ICounter destinationProxy =
            context.CreateEntityProxy<ICounter>(destinationEntity);

        int sourceBalance = await sourceProxy.Get();

        if (sourceBalance >= transferAmount)
        {
            await sourceProxy.Add(-transferAmount);
            await destinationProxy.Add(transferAmount);

            // the transfer succeeded
            return true;
        }
        else
        {
            // the transfer failed due to insufficient funds
            return false;
        }
    }
}
```

In .NET retourneert `LockAsync` een `IDisposable` die de kritieke sectie afbreekt wanneer deze wordt verwijderd. Dit `IDisposable` resultaat kan worden gebruikt in combi natie met een `using` blok om een syntactische weer gave van de sectie kritiek te krijgen.

In het vorige voor beeld heeft een Orchestrator-functie fondsen van een _bron_ entiteit overgezet naar een _doel_ entiteit. De `LockAsync` methode heeft de _bron_ -en _doel_ account-entiteiten vergrendeld. Deze vergren deling heeft gezorgd dat een andere client de status van een van de accounts niet kan opvragen of wijzigen, totdat de indelings logica het _kritieke gedeelte_ aan het einde van de `using`-instructie heeft verlaten. Dit gedrag voor komt dat de mogelijkheid van _overkomsten_ van het bron account kan worden verwisseld.

> [!NOTE] 
> Wanneer een indeling wordt beëindigd (normaal of met een fout), worden alle kritieke secties die worden uitgevoerd impliciet beëindigd en worden alle vergren delingen vrijgegeven.

### <a name="critical-section-behavior"></a>Gedrag van kritieke secties

Met de methode `LockAsync` maakt u een _kritieke sectie_ in een indeling. Deze _essentiële secties_ kunnen voor komen dat andere indelingen overlappende wijzigingen aanbrengen in een opgegeven set entiteiten. Intern verzendt de `LockAsync`-API ' Lock '-bewerkingen naar de entiteiten en worden geretourneerd wanneer het antwoord bericht ' lock verworven ' van elk van deze entiteiten wordt ontvangen. Zowel *vergren delen* als *ontgrendelen* zijn ingebouwde bewerkingen die door alle entiteiten worden ondersteund.

Er zijn geen bewerkingen van andere clients toegestaan voor een entiteit terwijl deze een vergrendelde status heeft. Dit gedrag zorgt ervoor dat er slechts één Orchestrator-exemplaar tegelijk een entiteit kan vergren delen. Als een aanroeper probeert een bewerking aan te roepen voor een entiteit terwijl deze is vergrendeld door een indeling, wordt die bewerking geplaatst in een *wachtrij voor bewerkingen in behandeling*. Er worden geen bewerkingen uitgevoerd totdat de vergren deling is vrijgegeven.

> [!NOTE] 
> Dit wijkt enigszins af van synchronisatie primitieven die worden gebruikt in de meeste programmeer talen, zoals de `lock` C#-instructie in. In C#moet bijvoorbeeld de `lock`-instructie door alle threads worden gebruikt om de juiste synchonization voor meerdere threads te garanderen. Voor entiteiten is het echter niet vereist dat alle bellers expliciet een entiteit _vergren delen_ . Als een beller een entiteit vergrendelt, worden alle andere bewerkingen op die entiteit geblokkeerd en achter deze vergren deling in de wachtrij geplaatst.

Vergren delingen op entiteiten zijn duurzaam, zodat ze behouden blijven, zelfs als het uitvoeren van het proces wordt gerecycled. Vergren delingen worden intern persistent gemaakt als onderdeel van de duurzame status van een entiteit.

In tegens telling tot trans acties worden in essentiële secties de wijzigingen in het geval van fouten niet automatisch hersteld. In plaats daarvan moet elke fout afhandeling (terugdraaien, opnieuw proberen of een andere) expliciet worden gecodeerd. bijvoorbeeld door fouten of uitzonde ringen te ondervangen. Deze ontwerp keuze is opzettelijk. Automatisch terugdraaien van de effecten van een indeling is moeilijk of onmogelijk in het algemeen, omdat met de integratie activiteiten kunnen worden uitgevoerd en aanroepen naar externe services die niet terug kunnen worden gedraaid. Er kunnen ook pogingen worden gedaan om de fout op te lossen.

### <a name="critical-section-rules"></a>Kritieke sectie regels

In tegens telling tot vergrendelings primitieve op laag niveau in de meeste programmeer talen, zijn essentiële secties **gegarandeerd geen deadlock**. Om deadlocks te voor komen, moeten we de volgende beperkingen afdwingen: 

* Kritieke secties kunnen niet worden genest.
* In essentiële secties kunnen geen subintegraties worden gemaakt.
* Kritieke secties kunnen alleen entiteiten aanroepen die ze hebben vergrendeld.
* Kritieke secties kunnen niet dezelfde entiteit aanroepen met meerdere parallelle aanroepen.
* Met kritieke secties kunnen alleen entiteiten worden gesignaleerd die niet zijn vergrendeld.

Eventuele schendingen van deze regels veroorzaken een runtime-fout (zoals een `LockingRulesViolationException` in .NET) met een bericht waarin wordt uitgelegd welke regel is verbroken.

## <a name="comparison-with-virtual-actors"></a>Vergelijking met virtuele Actors

Veel van de functies van duurzame entiteiten zijn geïnspireerd op het [actor model](https://en.wikipedia.org/wiki/Actor_model). Als u al bekend bent met actors, kan het zijn dat u veel van de concepten die in dit artikel worden beschreven, kunt herkennen. Duurzame entiteiten zijn met name vergelijkbaar met [virtuele actoren](https://research.microsoft.com/projects/orleans/), of *korrels*, die zijn gepopulaird door het [Orleans-project](http://dotnet.github.io/orleans/). Bijvoorbeeld:

* Duurzame entiteiten zijn adresseerbaar via een *entiteit-id*.
* De bewerkingen van een duurzame entiteit worden op een later tijdstip uitgevoerd om race voorwaarden te voor komen.
* Duurzame entiteiten worden impliciet gemaakt wanneer ze worden aangeroepen of gesignaleerd.
* Wanneer er geen bewerkingen worden uitgevoerd, worden duurzame entiteiten op de achtergrond uit het geheugen verwijderd.

Er zijn echter enkele belang rijke verschillen die te maken hebben met:

* Duurzame entiteiten bepalen de *duurzaamheid* over de *latentie*en zijn dus mogelijk niet geschikt voor toepassingen met strikte latentie vereisten.
* Duurzame entiteiten hebben geen ingebouwde time-outs voor berichten. In Orleans time-out voor alle berichten na een Configureer bare tijd (standaard 30 seconden).
* Berichten die tussen entiteiten worden verzonden, worden betrouwbaar en in de juiste volg orde bezorgd. In Orleans wordt een betrouw bare of bestelde levering ondersteund voor inhoud die via stromen wordt verzonden, maar niet wordt gegarandeerd voor alle berichten tussen korrels.
* Aanvraag/antwoord-patronen in entiteiten zijn beperkt tot de integratie. Vanuit entiteiten is alleen eenrichtings berichten (ook wel ' Signa lering ' genoemd) toegestaan, zoals in het oorspronkelijke actor model en in tegens telling tot korrels in Orleans. 
* Duurzame entiteiten bieden geen deadlock. In Orleans kunnen deadlocks optreden (en niet omzetten tot er een time-out optreedt).
* Duurzame entiteiten kunnen worden gebruikt in combi natie met duurzame Orchestrations en bieden ondersteuning voor gedistribueerde vergrendelings mechanismen. 


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Lees de ontwikkelaars handleiding voor duurzame entiteiten in .NET](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [Meer informatie over taak hubs](durable-functions-task-hubs.md)
