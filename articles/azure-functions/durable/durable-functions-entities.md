---
title: Duurzame entiteiten - Azure-functies
description: Ontdek wat duurzame entiteiten zijn en hoe u deze gebruiken in de extensie Duurzame functies voor Azure-functies.
author: cgillum
ms.topic: overview
ms.date: 12/17/2019
ms.author: azfuncdf
ms.openlocfilehash: 6ecf3bb5999296b2f5f8f5c25616fac8e0278cda
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "80132510"
---
# <a name="entity-functions"></a>Entiteitsfuncties

Entiteitsfuncties definiëren bewerkingen voor het lezen en bijwerken van kleine stukjes staat, bekend als *duurzame entiteiten*. Net als orchestrator-functies zijn entiteitsfuncties functies met een speciaal triggertype, de *entiteitstrigger*. In tegenstelling tot orchestrator-functies beheren entiteitsfuncties de status van een entiteit expliciet, in plaats van impliciet status via controlestroom weer te geven.
Entiteiten bieden een middel voor het uitschalen van toepassingen door het werk te distribueren over vele entiteiten, elk met een bescheiden formaat status.

> [!NOTE]
> Entiteitsfuncties en gerelateerde functionaliteit zijn alleen beschikbaar in Duurzame functies 2.0 en hoger.

## <a name="general-concepts"></a>Algemene concepten

Entiteiten gedragen zich een beetje als kleine services die communiceren via berichten. Elke entiteit heeft een unieke identiteit en een interne status (als deze bestaat). Net als services of objecten voeren entiteiten bewerkingen uit wanneer daarom wordt gevraagd. Wanneer een bewerking wordt uitgevoerd, kan de interne status van de entiteit worden bijgewerkt. Het kan ook bellen met externe diensten en wachten op een antwoord. Entiteiten communiceren met andere entiteiten, orkestraties en clients met behulp van berichten die impliciet via betrouwbare wachtrijen worden verzonden. 

Om conflicten te voorkomen, worden alle bewerkingen op één entiteit gegarandeerd serieel uitgevoerd, dat wil zeggen, de ene na de andere. 

### <a name="entity-id"></a>Entiteits-id
Entiteiten worden benaderd via een unieke id, de *entiteits-id*. Een entiteits-id is gewoon een tekenreeksdie een entiteitsinstantie op unieke wijze identificeert. Het bestaat uit een:

* **Entiteitsnaam**, een naam die het type entiteit identificeert. Een voorbeeld is 'Counter'. Deze naam moet overeenkomen met de naam van de entiteitsfunctie die de entiteit implementeert. Het is niet gevoelig voor de zaak.
* **Entiteitssleutel**, een tekenreeks die de entiteit op unieke wijze identificeert tussen alle andere entiteiten met dezelfde naam. Een voorbeeld is een GUID.

Een `Counter` entiteitsfunctie kan bijvoorbeeld worden gebruikt voor het bijhouden van de score in een online game. Elk exemplaar van het spel heeft een `@Counter@Game1` `@Counter@Game2`unieke entiteit-ID, zoals en . Voor alle bewerkingen die zich op een bepaalde entiteit richten, moet een entiteits-id als parameter worden opgegeven.

### <a name="entity-operations"></a>Entiteitsbewerkingen ###

Als u een bewerking op een entiteit wilt aanroepen, geeft u het volgende op:

* **Entiteits-id** van de doelentiteit.
* **De naam van**de bewerking , een tekenreeks die de uit te voeren bewerking opgeeft. De `Counter` entiteit kan bijvoorbeeld `add` `get` `reset` ondersteuning bieden voor of bewerkingen.
* **Bewerkingsinvoer**, een optionele invoerparameter voor de bewerking. De add-bewerking kan bijvoorbeeld een geheel getal als invoer nemen.
* **Geplande tijd**, wat een optionele parameter is voor het opgeven van de levertijd van de bewerking. Een bewerking kan bijvoorbeeld betrouwbaar worden gepland om in de toekomst meerdere dagen uit te voeren.

Bewerkingen kunnen een resultaatwaarde of een foutresultaat retourneren, zoals een JavaScript-fout of een .NET-uitzondering. Dit resultaat of deze fout kan worden waargenomen door orkestraties die de bewerking hebben genoemd.

Een entiteitsbewerking kan ook de status van de entiteit maken, lezen, bijwerken en verwijderen. De toestand van de entiteit is altijd blijvend in opslag.

## <a name="define-entities"></a>Entiteiten definiëren

Momenteel zijn de twee afzonderlijke API's voor het definiëren van entiteiten:

**Functiegebaseerde syntaxis**, waarbij entiteiten worden weergegeven als functies en bewerkingen, worden expliciet door de toepassing verzonden. Deze syntaxis werkt goed voor entiteiten met een eenvoudige status, weinig bewerkingen of een dynamische set bewerkingen zoals in toepassingskaders. Deze syntaxis kan vervelend zijn om te onderhouden omdat deze geen typefouten opneemt tijdens het compileren.

**Syntaxis van klasse (alleen.NET)**, waarbij entiteiten en bewerkingen worden vertegenwoordigd door klassen en methoden. Deze syntaxis produceert gemakkelijker leesbare code en maakt het mogelijk bewerkingen op een typeveilige manier aan te roepen. De syntaxis op basis van de klasse is een dunne laag boven op de functiegebaseerde syntaxis, zodat beide varianten door elkaar kunnen worden gebruikt in dezelfde toepassing.

# <a name="c"></a>[C #](#tab/csharp)

### <a name="example-function-based-syntax---c"></a>Voorbeeld: op functie gebaseerde syntaxis - C #

De volgende code is een `Counter` voorbeeld van een eenvoudige entiteit geïmplementeerd als een duurzame functie. Deze functie definieert `add` `reset`drie `get`bewerkingen, , en , die elk werkt op een gehele staat.

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

Zie [Op functie gebaseerde syntaxis](durable-functions-dotnet-entities.md#function-based-syntax)voor meer informatie over de op de functie gebaseerde syntaxis en hoe deze te gebruiken.

### <a name="example-class-based-syntax---c"></a>Voorbeeld: Syntaxis op basis van klasse - C #

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

De status van deze entiteit `Counter`is een object van type , dat een veld bevat dat de huidige waarde van de teller opslaat. Om dit object in opslag te blijven opslaan, wordt het geserialiseerd en geserialiseerd door de [Json.NET](https://www.newtonsoft.com/json) bibliotheek. 

Zie [Entiteitklassen definiëren](durable-functions-dotnet-entities.md#defining-entity-classes)voor meer informatie over de syntaxis van de klasse en hoe deze te gebruiken.

# <a name="javascript"></a>[Javascript](#tab/javascript)

### <a name="example-javascript-entity"></a>Voorbeeld: JavaScript-entiteit

Duurzame entiteiten zijn beschikbaar in JavaScript vanaf versie `durable-functions` **1.3.0** van het npm-pakket. De volgende code `Counter` is de entiteit die is geïmplementeerd als een duurzame functie die is geschreven in JavaScript.

**Teller/functie.json**
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

**Teller/index.js**
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

## <a name="access-entities"></a>Toegangsentiteiten

Entiteiten zijn toegankelijk via één richting of tweerichtingscommunicatie. De volgende terminologie onderscheidt de twee vormen van communicatie: 

* **Bellen naar** een entiteit maakt gebruik van tweerichtingscommunicatie (retour)communicatie. U verzendt een bewerkingsbericht naar de entiteit en wacht vervolgens op het antwoordbericht voordat u verdergaat. Het antwoordbericht kan een resultaatwaarde of een foutresultaat opleveren, zoals een JavaScript-fout of een .NET-uitzondering. Dit resultaat of deze fout wordt vervolgens waargenomen door de beller.
* **Signaleren van** een entiteit maakt gebruik van eenrichtingscommunicatie (brand en vergeet). U stuurt een operationeel bericht, maar wacht niet op een reactie. Hoewel het bericht gegarandeerd uiteindelijk wordt bezorgd, weet de afzender niet wanneer en kan hij geen resultaatwaarde of fouten waarnemen.

Entiteiten zijn toegankelijk vanuit clientfuncties, vanuit orchestrator-functies of vanuit entiteitsfuncties. Niet alle vormen van communicatie worden ondersteund door alle contexten:

* Vanuit clients u entiteiten signaleren en u de entiteitsstatus lezen.
* Vanuit orkestraties u entiteiten signaleren en entiteiten bellen.
* Vanuit entiteiten u entiteiten signaleren.

De volgende voorbeelden illustreren deze verschillende manieren om toegang te krijgen tot entiteiten.

### <a name="example-client-signals-an-entity"></a>Voorbeeld: Client signaleert een entiteit

Als u toegang wilt krijgen tot entiteiten vanuit een gewone Azure-functie, die ook wel een clientfunctie wordt genoemd, gebruikt u de [entiteitsclientbinding](durable-functions-bindings.md#entity-client). In het volgende voorbeeld ziet u een functie die door de wachtrij wordt geactiveerd en een entiteit signaleert met deze binding.

# <a name="c"></a>[C #](#tab/csharp)

> [!NOTE]
> Voor de eenvoud worden in de volgende voorbeelden de loslatende syntaxis weergegeven voor toegang tot entiteiten. In het algemeen raden we u aan [om toegang te krijgen tot entiteiten via interfaces,](durable-functions-dotnet-entities.md#accessing-entities-through-interfaces) omdat dit meer typecontrole biedt.

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    await client.signalEntity(entityId, "add", 1);
};
```

---

Het *term-signaal* betekent dat de aanroep van de entiteit-API één richting en asynchroon is. Het is niet mogelijk dat een clientfunctie weet wanneer de entiteit de bewerking heeft verwerkt. Ook kan de clientfunctie geen resultaatwaarden of uitzonderingen in acht nemen. 

### <a name="example-client-reads-an-entity-state"></a>Voorbeeld: Client leest een entiteitsstatus

Clientfuncties kunnen ook de status van een entiteit opvragen, zoals in het volgende voorbeeld wordt weergegeven:

# <a name="c"></a>[C #](#tab/csharp)

```csharp
[FunctionName("QueryCounter")]
public static async Task<HttpResponseMessage> Run(
    [HttpTrigger(AuthorizationLevel.Function)] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");
    EntityStateResponse<JObject> stateResponse = await client.ReadEntityStateAsync<JObject>(entityId);
    return req.CreateResponse(HttpStatusCode.OK, stateResponse.EntityState);
}
```

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = async function (context) {
    const client = df.getClient(context);
    const entityId = new df.EntityId("Counter", "myCounter");
    const stateResponse = await context.df.readEntityState(entityId);
    return stateResponse.entityState;
};
```

---

Entiteitsstatusquery's worden verzonden naar het opslagarchief Duurzaam bijhouden en de meest recente status van de entiteit retourneren. Deze staat is altijd een "vastgelegde" staat, dat wil zeggen, het is nooit een tijdelijke tussenliggende staat verondersteld in het midden van het uitvoeren van een bewerking. Het is echter mogelijk dat deze status verouderd is in vergelijking met de status van de entiteit in het geheugen. Alleen orkestraties kunnen de geheugenstatus van een entiteit lezen, zoals beschreven in de volgende sectie.

### <a name="example-orchestration-signals-and-calls-an-entity"></a>Voorbeeld: Orchestration-signalen en aanroepen van een entiteit

Orchestrator-functies hebben toegang tot entiteiten met API's op de [orchestration trigger binding](durable-functions-bindings.md#orchestration-trigger). De volgende voorbeeldcode toont een orchestrator-functie die een `Counter` entiteit aanroept en signaleert.

# <a name="c"></a>[C #](#tab/csharp)

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

```javascript
const df = require("durable-functions");

module.exports = df.orchestrator(function*(context){
    const entityId = new df.EntityId("Counter", "myCounter");

    // Two-way call to the entity which returns a value - awaits the response
    currentValue = yield context.df.callEntity(entityId, "get");
});
```

> [!NOTE]
> JavaScript ondersteunt momenteel geen signalering van een entiteit vanuit een orchestrator. Gebruik in plaats daarvan `callEntity`.

---

Alleen orkestraties zijn in staat om entiteiten te bellen en een antwoord te krijgen, wat een retourwaarde of een uitzondering kan zijn. Clientfuncties die de [clientbinding gebruiken,](durable-functions-bindings.md#entity-client) kunnen alleen entiteiten signaleren.

> [!NOTE]
> Het aanroepen van een entiteit vanuit een orchestrator-functie is vergelijkbaar met het aanroepen van een [activiteitsfunctie](durable-functions-types-features-overview.md#activity-functions) vanuit een orchestratorfunctie. Het belangrijkste verschil is dat entiteitsfuncties duurzame objecten zijn met een adres, dat de entiteits-id is. Entiteitfuncties ondersteunen het opgeven van een bewerkingsnaam. Activiteitsfuncties daarentegen zijn stateloos en hebben niet het concept van bewerkingen.

### <a name="example-entity-signals-an-entity"></a>Voorbeeld: Entiteit signaleert een entiteit

Een entiteitsfunctie kan signalen verzenden naar andere entiteiten, of zelfs naar zichzelf, terwijl deze een bewerking uitvoert.
We kunnen bijvoorbeeld het `Counter` vorige entiteitsvoorbeeld wijzigen, zodat het een 'door mijlpalen bereikt' signaal naar een monitorentiteit stuurt wanneer de teller de waarde 100 bereikt.

# <a name="c"></a>[C #](#tab/csharp)

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

# <a name="javascript"></a>[Javascript](#tab/javascript)

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

---

## <a name="entity-coordination-currently-net-only"></a><a name="entity-coordination"></a>Entiteitscoördinatie (momenteel alleen .NET)

Er kunnen momenten zijn waarop u bewerkingen voor meerdere entiteiten moet coördineren. In een bankaanvraag u bijvoorbeeld entiteiten hebben die afzonderlijke bankrekeningen vertegenwoordigen. Wanneer u geld overmaakt van de ene rekening naar de andere, moet u ervoor zorgen dat de bronrekening voldoende geld heeft. U moet er ook voor zorgen dat updates van zowel de bron- als de bestemmingsaccounts op een transactioneel consistente manier worden uitgevoerd.

### <a name="example-transfer-funds-c"></a>Voorbeeld: Geld overmaken (C#)

In de volgende voorbeeldcode worden geld overgemaakt tussen twee accountentiteiten met behulp van een orchestrator-functie. Voor het coördineren van entiteitsupdates moet u de `LockAsync` methode gebruiken om een kritieke _sectie_ in de orkestratie te maken.

> [!NOTE]
> Voor eenvoud wordt in `Counter` dit voorbeeld de entiteit die eerder is gedefinieerd, opnieuw gebruikt. In een echte toepassing zou het beter `BankAccount` zijn om een meer gedetailleerde entiteit te definiëren.

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

In .NET `LockAsync` `IDisposable`retourneert , waarmee de kritieke sectie wordt beëindigd wanneer deze wordt verwijderd. Dit `IDisposable` resultaat kan samen `using` met een blok worden gebruikt om een syntactische weergave van de kritieke sectie te krijgen.

In het voorgaande voorbeeld heeft een orchestrator-functie geld overgemaakt van een bronentiteit naar een doelentiteit. De `LockAsync` methode heeft zowel de bron- als het doelaccount vergrendeld. Deze vergrendeling zorgde ervoor dat geen enkele andere client de status van beide accounten kon `using` opvragen of wijzigen totdat de orchestration-logica de kritieke sectie aan het einde van de instructie verliet. Dit gedrag voorkomt de mogelijkheid van overschrijven van het bronaccount.

> [!NOTE] 
> Wanneer een orkestratie eindigt, normaal of met een fout, worden alle kritieke secties in uitvoering impliciet beëindigd en worden alle vergrendelingen vrijgegeven.

### <a name="critical-section-behavior"></a>Gedrag van kritieke sectie

De `LockAsync` methode maakt een kritieke sectie in een orkestratie. Deze kritieke secties voorkomen dat andere orkestraties overlappende wijzigingen aanbrengen in een bepaalde set entiteiten. Intern verzendt `LockAsync` de API "lock"-bewerkingen naar de entiteiten en retourneert wanneer deze een antwoordbericht 'door vergrendeling verworven' van elk van deze zelfde entiteiten ontvangt. Zowel vergrendelen als ontgrendelen zijn ingebouwde bewerkingen die worden ondersteund door alle entiteiten.

Er zijn geen bewerkingen van andere clients toegestaan op een entiteit terwijl deze in een vergrendelde status is. Dit gedrag zorgt ervoor dat slechts één orchestration-instantie een entiteit tegelijk kan vergrendelen. Als een beller een bewerking op een entiteit probeert aan te roepen terwijl deze is vergrendeld door een orkestratie, wordt die bewerking in een wachtrij in behandeling geplaatst. Geen lopende bewerkingen worden verwerkt tot na de holding orchestration releases zijn slot.

> [!NOTE] 
> Dit gedrag verschilt enigszins van synchronisatieprimitieven die in de meeste `lock` programmeertalen worden gebruikt, zoals de instructie in C#. In C#moet de `lock` instructie bijvoorbeeld door alle threads worden gebruikt om een goede synchronisatie over meerdere threads te garanderen. Entiteiten vereisen echter niet dat alle bellers een entiteit expliciet vergrendelen. Als een beller een entiteit vergrendelt, worden alle andere bewerkingen op die entiteit geblokkeerd en achter dat slot in de wachtrij geplaatst.

Sloten op entiteiten zijn duurzaam, zodat ze blijven bestaan, zelfs als het uitvoeringsproces wordt gerecycled. Vergrendelingen worden intern gehandhaafd als onderdeel van de duurzame status van een entiteit.

In tegenstelling tot transacties worden wijzigingen in kritieke secties niet automatisch teruggedraaid in het geval van fouten. In plaats daarvan moet elke foutafhandeling, zoals terugdraaien of opnieuw proberen, expliciet worden gecodeerd, bijvoorbeeld door fouten of uitzonderingen te vangen. Deze ontwerpkeuze is opzettelijk. Het automatisch terugdraaien van alle effecten van een orkestratie is in het algemeen moeilijk of onmogelijk, omdat orkestraties activiteiten kunnen uitvoeren en oproepen naar externe services die niet kunnen worden teruggedraaid. Ook pogingen om terug te draaien kan zelf mislukken en vereisen verdere foutafhandeling.

### <a name="critical-section-rules"></a>Kritische sectieregels

In tegenstelling tot low-level vergrendeling primitieven in de meeste programmeertalen, kritische secties zijn *gegarandeerd niet vast te lopen*. Om impasses te voorkomen, handhaven we de volgende beperkingen: 

* Kritieke secties kunnen niet worden genest.
* Kritieke secties kunnen geen suborchestrations maken.
* Kritieke secties kunnen alleen entiteiten aanroepen die ze hebben vergrendeld.
* Kritieke secties kunnen niet dezelfde entiteit aanroepen met behulp van meerdere parallelle oproepen.
* Kritieke secties kunnen alleen entiteiten signaleren die ze niet hebben vergrendeld.

Overtredingen van deze regels veroorzaken een runtime-fout, zoals `LockingRulesViolationException` in .NET, die een bericht bevat waarin wordt uitgelegd welke regel is overtreden.

## <a name="comparison-with-virtual-actors"></a>Vergelijking met virtuele actoren

Veel van de duurzame entiteiten functies zijn geïnspireerd door de [acteur model](https://en.wikipedia.org/wiki/Actor_model). Als u al bekend bent met acteurs, herkent u misschien veel van de concepten die in dit artikel worden beschreven. Duurzame entiteiten zijn vooral vergelijkbaar met [virtuele actoren](https://research.microsoft.com/projects/orleans/), of granen, zoals gepopulariseerd door de [Orleans project](http://dotnet.github.io/orleans/). Bijvoorbeeld:

* Duurzame entiteiten zijn adresseerbaar via een entiteits-id.
* Duurzame entiteitsbewerkingen worden serieel uitgevoerd, één voor één, om raceomstandigheden te voorkomen.
* Duurzame entiteiten worden impliciet gemaakt wanneer ze worden aangeroepen of gesignaleerd.
* Wanneer u geen bewerkingen uitvoert, worden duurzame entiteiten in stilte uit het geheugen gelost.

Er zijn een aantal belangrijke verschillen die vermeldenswaard zijn:

* Duurzame entiteiten geven prioriteit aan duurzaamheid boven latentie en zijn dus mogelijk niet geschikt voor toepassingen met strenge latentievereisten.
* Duurzame entiteiten hebben geen ingebouwde time-outs voor berichten. In Orleans, alle berichten time-out na een configureerbare tijd. De standaardwaarde is 30 seconden.
* Berichten die tussen entiteiten worden verzonden, worden betrouwbaar en in orde geleverd. In Orleans wordt betrouwbare of bestelde levering ondersteund voor inhoud die via streams wordt verzonden, maar is niet gegarandeerd voor alle berichten tussen korrels.
* Aanvraagresponspatronen in entiteiten zijn beperkt tot orkestraties. Vanuit entiteiten is alleen eenrichtingsberichten (ook bekend als signalering) toegestaan, zoals in het oorspronkelijke acteurmodel, en in tegenstelling tot granen in Orleans. 
* Duurzame entiteiten impasse niet. In Orleans kunnen impasses optreden en niet oplossen totdat berichten een time-out hebben.
* Duurzame entiteiten kunnen worden gebruikt in combinatie met duurzame orkestraties en ondersteuning van gedistribueerde vergrendelingsmechanismen. 


## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Lees de handleiding voor ontwikkelaars voor duurzame entiteiten in .NET](durable-functions-dotnet-entities.md)

> [!div class="nextstepaction"]
> [Meer informatie over taakhubs](durable-functions-task-hubs.md)
