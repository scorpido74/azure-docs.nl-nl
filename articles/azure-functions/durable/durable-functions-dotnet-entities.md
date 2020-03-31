---
title: Handleiding voor ontwikkelaars voor duurzame entiteiten in .NET - Azure-functies
description: Werken met duurzame entiteiten in .NET met de extensie Duurzame functies voor Azure-functies.
author: sebastianburckhardt
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: azfuncdf
ms.openlocfilehash: 01e07eaee705634b03cc4462c4058e290daa8bc2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278127"
---
# <a name="developers-guide-to-durable-entities-in-net"></a>Handleiding voor ontwikkelaars voor duurzame entiteiten in .NET

In dit artikel beschrijven we de beschikbare interfaces voor het ontwikkelen van duurzame entiteiten met .NET in detail, inclusief voorbeelden en algemeen advies. 

Entiteitsfuncties bieden serverloze toepassingsontwikkelaars een handige manier om de toepassingsstatus te ordenen als een verzameling fijnmazige entiteiten. Zie het artikel [Duurzame entiteiten: Concepten](durable-functions-entities.md) voor meer informatie over de onderliggende concepten.

We bieden momenteel twee API's voor het definiëren van entiteiten:

- De **syntaxis op basis van klasse** vertegenwoordigt entiteiten en bewerkingen als klassen en methoden. Deze syntaxis produceert gemakkelijk leesbare code en maakt het mogelijk bewerkingen aan te roepen op een type-gecontroleerde manier via interfaces. 

- De **op de functie gebaseerde syntaxis** is een interface op een lager niveau die entiteiten als functies vertegenwoordigt. Het biedt nauwkeurige controle over hoe de entiteitsbewerkingen worden verzonden en hoe de entiteitsstatus wordt beheerd.  

Dit artikel richt zich voornamelijk op de klasse-gebaseerde syntaxis, omdat we verwachten dat deze beter geschikt is voor de meeste toepassingen. De [op de functie gebaseerde syntaxis](#function-based-syntax) kan echter geschikt zijn voor toepassingen die hun eigen abstracties voor entiteitsstatus en -bewerkingen willen definiëren of beheren. Het kan ook geschikt zijn voor het implementeren van bibliotheken waarvoor genealiteit vereist is die momenteel niet wordt ondersteund door de syntaxis van de klasse. 

> [!NOTE]
> De syntaxis op basis van de klasse is slechts een laag boven op de functiegebaseerde syntaxis, zodat beide varianten door elkaar kunnen worden gebruikt in dezelfde toepassing. 
 
## <a name="defining-entity-classes"></a>Entiteitsklassen definiëren

Het volgende voorbeeld is `Counter` een implementatie van een entiteit die één `Add`waarde `Reset` `Get`van `Delete`het gehele getal getal opslaat en vier bewerkingen biedt , , en .

```csharp
[JsonObject(MemberSerialization.OptIn)]
public class Counter
{
    [JsonProperty("value")]
    public int Value { get; set; }

    public void Add(int amount) 
    {
        this.Value += amount;
    }

    public Task Reset() 
    {
        this.Value = 0;
        return Task.CompletedTask;
    }

    public Task<int> Get() 
    {
        return Task.FromResult(this.Value);
    }

    public void Delete() 
    {
        Entity.Current.DeleteState();
    }

    [FunctionName(nameof(Counter))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<Counter>();
}
```

De `Run` functie bevat de boilerplate die nodig is voor het gebruik van de klasse-gebaseerde syntaxis. Het moet een *statische* Azure-functie zijn. Het wordt één keer uitgevoerd voor elk bewerkingsbericht dat door de entiteit wordt verwerkt. Wanneer `DispatchAsync<T>` wordt aangeroepen en de entiteit nog niet in het `T` geheugen staat, construeert het een object van type en vult het de velden van de laatst aanhoudende JSON die in opslag is gevonden (indien aanwezig). Vervolgens roept het de methode met de overeenkomende naam.

> [!NOTE]
> De status van een op klasse gebaseerde entiteit wordt **impliciet gemaakt** voordat de entiteit een bewerking verwerkt en kan expliciet worden **verwijderd** in een bewerking door aanteroepen `Entity.Current.DeleteState()`.

### <a name="class-requirements"></a>Klassenvereisten
 
Entiteitsklassen zijn POCOs (gewone oude CLR-objecten) waarvoor geen speciale superklassen, interfaces of kenmerken nodig zijn. Echter:

- De klasse moet bouwbaar zijn (zie [Constructie van de entiteit).](#entity-construction)
- De klasse moet JSON-serializable zijn (zie [Entiteitserialisatie](#entity-serialization)).

Elke methode die als verrichting moet worden ingeroepen, moet ook aan aanvullende eisen voldoen:

- Een bewerking moet hoogstens één argument hebben en mag geen overbelasting of algemene typeargumenten hebben.
- Een bewerking die bedoeld is om te worden `Task` `Task<T>`aangeroepen vanaf een orkestratie met behulp van een interface moet terugkeren of .
- Argumenten en retourwaarden moeten serializable waarden of objecten zijn.

### <a name="what-can-operations-do"></a>Wat kunnen operaties doen?

Alle entiteitsbewerkingen kunnen de entiteitsstatus lezen en bijwerken en wijzigingen in de status blijven automatisch bestaan in opslag. Bovendien kunnen bewerkingen externe I/O- of andere berekeningen uitvoeren, binnen de algemene limieten die alle Azure-functies gemeen hebben.

Bewerkingen hebben ook toegang tot `Entity.Current` functionaliteit die door de context wordt geleverd:

* `EntityName`: de naam van de momenteel uitvoerende entiteit.
* `EntityKey`: de sleutel van de momenteel uitvoerende entiteit.
* `EntityId`: de id van de momenteel uitvoerende entiteit (inclusief naam en sleutel).
* `SignalEntity`: stuurt een eenrichtingsbericht naar een entiteit.
* `CreateNewOrchestration`: start een nieuwe orkestratie.
* `DeleteState`: hiermee wordt de status van deze entiteit verwijderd.

We kunnen bijvoorbeeld de voorentiteit wijzigen zodat deze een orkestratie start wanneer de teller 100 bereikt en de entiteits-id als invoerargument doorgeeft:

```csharp
    public void Add(int amount) 
    {
        if (this.Value < 100 && this.Value + amount > 100)
        {
            Entity.Current.StartNewOrchestration("MilestoneReached", Entity.Current.EntityId)
        }
        this.Value += amount;      
    }
```

## <a name="accessing-entities-directly"></a>Rechtstreeks toegang krijgen tot entiteiten

Entiteiten op basis van klasse kunnen rechtstreeks worden geopend met expliciete tekenreeksnamen voor de entiteit en haar activiteiten. Hieronder geven we enkele voorbeelden; voor een diepere uitleg van de onderliggende concepten (zoals signalen versus oproepen) zie de discussie in [Access-entiteiten](durable-functions-entities.md#access-entities). 

> [!NOTE]
> Waar mogelijk raden we [toegang tot entiteiten aan via interfaces,](#accessing-entities-through-interfaces)omdat het meer typecontrole biedt.

### <a name="example-client-signals-entity"></a>Voorbeeld: entiteit clientsignalen

Met de volgende Azure Http-functie wordt een DELETE-bewerking uitgevoerd met REST-conventies. Het stuurt een verwijdersignaal naar de tegenentiteit waarvan de sleutel wordt doorgegeven in het URL-pad.

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync(entityId, "Delete");    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

### <a name="example-client-reads-entity-state"></a>Voorbeeld: client leest entiteitsstatus

De volgende Azure Http-functie implementeert een GET-bewerking met REST-conventies. Het leest de huidige status van de contraentiteit waarvan de sleutel wordt doorgegeven in het URL-pad.

```csharp
[FunctionName("GetCounter")]
public static async Task<HttpResponseMessage> GetCounter(
    [HttpTrigger(AuthorizationLevel.Function, "get", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    var state = await client.ReadEntityStateAsync<Counter>(entityId); 
    return req.CreateResponse(state);
}
```

> [!NOTE]
> Het object `ReadEntityStateAsync` dat wordt geretourneerd is slechts een lokale kopie, dat wil zeggen een momentopname van de entiteitsstatus vanaf een eerder tijdstip. In het bijzonder kan het verouderd zijn en het wijzigen van dit object heeft geen effect op de werkelijke entiteit. 

### <a name="example-orchestration-first-signals-then-calls-entity"></a>Voorbeeld: orchestration eerst signalen, dan roept entiteit

De volgende orkestratie signaleert een tegenentiteit om deze te verhogen en roept vervolgens dezelfde entiteit aan om de nieuwste waarde te lezen.

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");

    // One-way signal to the entity - does not await a response
    context.SignalEntity(entityId, "Add", 1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");

    return currentValue;
}
```

## <a name="accessing-entities-through-interfaces"></a>Toegang tot entiteiten via interfaces

Interfaces kunnen worden gebruikt voor toegang tot entiteiten via gegenereerde proxyobjecten. Deze benadering zorgt ervoor dat de naam en het argumenttype van een bewerking overeenkomen met wat wordt geïmplementeerd. We raden u aan interfaces te gebruiken voor toegang tot entiteiten waar mogelijk.

We kunnen bijvoorbeeld het tegenvoorbeeld als volgt wijzigen:

```csharp
public interface ICounter
{
    void Add(int amount);
    Task Reset();
    Task<int> Get();
    void Delete();
}

public class Counter : ICounter
{
    ...
}
```

Entiteitklassen en entiteitsinterfaces zijn vergelijkbaar met de korrels en korrelinterfaces die door [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/)worden gepopulariseerd. Voor meer informatie over overeenkomsten en verschillen tussen duurzame entiteiten en Orleans, zie [Vergelijking met virtuele actoren](durable-functions-entities.md#comparison-with-virtual-actors).

Naast het verstrekken van type controle, interfaces zijn nuttig voor een betere scheiding van zorgen binnen de toepassing. Omdat een entiteit bijvoorbeeld meerdere interfaces kan implementeren, kan één entiteit meerdere rollen vervullen. Aangezien een interface door meerdere entiteiten kan worden geïmplementeerd, kunnen algemene communicatiepatronen ook worden geïmplementeerd als herbruikbare bibliotheken.

### <a name="example-client-signals-entity-through-interface"></a>Voorbeeld: clientsignalen entiteit via interface

Clientcode kan `SignalEntityAsync<TEntityInterface>` gebruiken om signalen te `TEntityInterface`verzenden naar entiteiten die implementeren. Bijvoorbeeld:

```csharp
[FunctionName("DeleteCounter")]
public static async Task<HttpResponseMessage> DeleteCounter(
    [HttpTrigger(AuthorizationLevel.Function, "delete", Route = "Counter/{entityKey}")] HttpRequestMessage req,
    [DurableClient] IDurableEntityClient client,
    string entityKey)
{
    var entityId = new EntityId("Counter", entityKey);
    await client.SignalEntityAsync<ICounter>(entityId, proxy => proxy.Delete());    
    return req.CreateResponse(HttpStatusCode.Accepted);
}
```

In dit voorbeeld `proxy` is de parameter een `ICounter`dynamisch gegenereerde instantie van `Delete` , die de aanroep intern vertaalt naar een signaal.

> [!NOTE]
> De `SignalEntityAsync` API's kunnen alleen worden gebruikt voor eenrichtingsbewerkingen. Zelfs als een `Task<T>`bewerking terugkeert, `T` is de waarde `default`van de parameter altijd null of , niet het werkelijke resultaat.
Het heeft bijvoorbeeld geen zin om `Get` de bewerking te signaleren, omdat er geen waarde wordt geretourneerd. In plaats daarvan `ReadStateAsync` kunnen clients rechtstreeks toegang krijgen tot de tellerstatus of `Get` een orchestrator-functie starten die de bewerking aanroept. 

### <a name="example-orchestration-first-signals-then-calls-entity-through-proxy"></a>Voorbeeld: orchestration eerst signalen, dan roept entiteit via proxy

Als u een entiteit wilt aanroepen `CreateEntityProxy` of signaleren vanuit een orkestratie, kan u samen met het interfacetype een proxy voor de entiteit genereren. Deze proxy kan vervolgens worden gebruikt om te bellen of te signaleren:

```csharp
[FunctionName("IncrementThenGet")]
public static async Task<int> Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId("Counter", "myCounter");
    var proxy = context.CreateEntityProxy<ICounter>(entityId);

    // One-way signal to the entity - does not await a response
    proxy.Add(1);

    // Two-way call to the entity which returns a value - awaits the response
    int currentValue = await proxy.Get();

    return currentValue;
}
```

Impliciet worden alle bewerkingen die terugkeren `void` gesignaleerd en `Task` `Task<T>` alle bewerkingen die terugkeren of worden aangeroepen. Men kan dit standaardgedrag wijzigen en seinbewerkingen `SignalEntity<IInterfaceType>` toevoegen, zelfs als ze Taak retourneren, door de methode expliciet te gebruiken.

### <a name="shorter-option-for-specifying-the-target"></a>Kortere optie voor het opgeven van het doel

Wanneer u een entiteit aanbelt of signaleert met een interface, moet in het eerste argument de doelentiteit worden opgegeven. Het doel kan worden opgegeven door de entiteits-id op te geven, of, in gevallen waarin er slechts één klasse is die de entiteit implementeert, alleen de entiteitssleutel:

```csharp
context.SignalEntity<ICounter>(new EntityId(nameof(Counter), "myCounter"), ...);
context.SignalEntity<ICounter>("myCounter", ...);
```

Als alleen de entiteitssleutel is opgegeven en een unieke `InvalidOperationException` implementatie niet kan worden gevonden bij runtime, wordt het gegooid. 

### <a name="restrictions-on-entity-interfaces"></a>Beperkingen op entiteitsinterfaces

Zoals gebruikelijk moeten alle parameter- en retourtypen JSON-serializable zijn. Anders worden uitzonderingen op serialisatie bij runtime gegooid.

We handhaven ook enkele aanvullende regels:
* Entiteitsinterfaces mogen alleen methoden definiëren.
* Entiteitsinterfaces mogen geen algemene parameters bevatten.
* Entiteitsinterfacemethoden mogen niet meer dan één parameter hebben.
* Entiteitsinterfacemethoden moeten `void` `Task`terugkeren , of`Task<T>` 

Als een van deze regels `InvalidOperationException` wordt overtreden, wordt een geworpen op runtime wanneer de interface wordt gebruikt als een type argument of `SignalEntity` `CreateProxy`. In het uitzonderingsbericht wordt uitgelegd welke regel is overtreden.

> [!NOTE]
> Interface methoden `void` terugkeren kan alleen worden gesignaleerd (one-way), niet aangeroepen (two-way). Interface methoden `Task` terug `Task<T>` te keren of kan worden aangeroepen of gesignaleerd. Als ze worden aangeroepen, geven ze het resultaat van de bewerking terug of gooien ze uitzonderingen die door de bewerking worden gegooid opnieuw. Wanneer ze echter worden gesignaleerd, geven ze het werkelijke resultaat of de uitzondering niet terug van de bewerking, maar alleen de standaardwaarde.

## <a name="entity-serialization"></a>Entiteitserialisatie

Aangezien de status van een entiteit blijvend is, moet de entiteitsklasse serialiseerbaar zijn. De runtime Duurzame [Json.NET](https://www.newtonsoft.com/json) functies maakt hiervoor gebruik van de Json.NET-bibliotheek, die een aantal beleidsregels en kenmerken ondersteunt om het serialisatie- en deserialisatieproces te beheren. De meest gebruikte C#-gegevenstypen (inclusief arrays en verzamelingstypen) zijn al serializable en kunnen eenvoudig worden gebruikt voor het definiëren van de status van duurzame entiteiten.

Json.NET bijvoorbeeld eenvoudig de volgende klasse serialiseren en deserialiseren:

```csharp
[JsonObject(MemberSerialization = MemberSerialization.OptIn)]
public class User
{
    [JsonProperty("name")]
    public string Name { get; set; }

    [JsonProperty("yearOfBirth")]
    public int YearOfBirth { get; set; }

    [JsonProperty("timestamp")]
    public DateTime Timestamp { get; set; }

    [JsonProperty("contacts")]
    public Dictionary<Guid, Contact> Contacts { get; set; } = new Dictionary<Guid, Contact>();

    [JsonObject(MemberSerialization = MemberSerialization.OptOut)]
    public struct Contact
    {
        public string Name;
        public string Number;
    }

    ...
}
```

### <a name="serialization-attributes"></a>Serialisatiekenmerken

In het bovenstaande voorbeeld hebben we ervoor gekozen om verschillende kenmerken op te nemen om de onderliggende serialisatie zichtbaarder te maken:
- We annoteren de klasse `[JsonObject(MemberSerialization.OptIn)]` met om ons eraan te herinneren dat de klasse moet worden serializable, en om alleen leden die expliciet zijn gemarkeerd als JSON eigenschappen voort te duren.
-  We annoteren de velden waarmee moet `[JsonProperty("name")]` worden volgehouden om ons eraan te herinneren dat een veld deel uitmaakt van de status van de volgehouden entiteit en om de eigenschapsnaam op te geven die moet worden gebruikt in de JSON-representatie.

Deze kenmerken zijn echter niet vereist; andere conventies of attributen zijn toegestaan zolang ze met Json.NET werken. Men kan bijvoorbeeld `[DataContract]` kenmerken gebruiken of helemaal geen kenmerken:

```csharp
[DataContract]
public class Counter
{
    [DataMember]
    public int Value { get; set; }
    ...
}

public class Counter
{
    public int Value;
    ...
}
```

Standaard wordt de naam van de klasse *niet* opgeslagen als onderdeel van `TypeNameHandling.None` de JSON-representatie: dat wil zeggen dat we de standaardinstelling gebruiken. Dit standaardgedrag kan worden `JsonObject` overschreven met behulp van of `JsonProperty` kenmerken.

### <a name="making-changes-to-class-definitions"></a>Wijzigingen aanbrengen in klassendefinities

Enige zorg is vereist bij het aanbrengen van wijzigingen in een klassendefinitie nadat een toepassing is uitgevoerd, omdat het opgeslagen JSON-object mogelijk niet meer overeenkomt met de nieuwe klassendefinitie. Toch is het vaak mogelijk om correct om te gaan met veranderende gegevensformaten, zolang men het deserialisatieproces begrijpt dat wordt gebruikt door `JsonConvert.PopulateObject`.

Hier volgen bijvoorbeeld enkele voorbeelden van wijzigingen en het effect ervan:

1. Als een nieuwe eigenschap wordt toegevoegd, die niet aanwezig is in de opgeslagen JSON, gaat het uit van de standaardwaarde.
1. Als een eigenschap wordt verwijderd, die aanwezig is in de opgeslagen JSON, gaat de vorige inhoud verloren.
1. Als een eigenschap wordt hernoemd, is het effect alsof u de oude wordt verwijderd en een nieuwe eigenschap toevoegt.
1. Als het type eigenschap wordt gewijzigd zodat deze niet meer kan worden gedeserialiseerd uit de opgeslagen JSON, wordt er een uitzondering gemaakt.
1. Als het type eigenschap wordt gewijzigd, maar het kan nog steeds worden gedeserialiseerd van de opgeslagen JSON, zal dit doen.

Er zijn veel opties beschikbaar voor het aanpassen van het gedrag van Json.NET. Als u bijvoorbeeld een uitzondering wilt forceren als de opgeslagen JSON een `JsonObject(MissingMemberHandling = MissingMemberHandling.Error)`veld bevat dat niet aanwezig is in de klasse, geeft u het kenmerk op . Het is ook mogelijk om aangepaste code te schrijven voor deserialisatie die JSON kan lezen die is opgeslagen in willekeurige formaten.

## <a name="entity-construction"></a>Entiteitsconstructie

Soms willen we meer controle uitoefenen over hoe entiteitsobjecten worden geconstrueerd. We beschrijven nu verschillende opties voor het wijzigen van het standaardgedrag bij het maken van entiteitsobjecten. 

### <a name="custom-initialization-on-first-access"></a>Aangepaste initialisatie bij eerste toegang

Af en toe moeten we een speciale initialisatie uitvoeren voordat we een bewerking verzenden naar een entiteit die nooit is geopend of die is verwijderd. Als u dit gedrag wilt opgeven, `DispatchAsync`kan u een voorwaardelijke vermelding toevoegen vóór :

```csharp
[FunctionName(nameof(Counter))]
public static Task Run([EntityTrigger] IDurableEntityContext ctx)
{
    if (!ctx.HasState)
    {
        ctx.SetState(...);
    }
    return ctx.DispatchAsync<Counter>();
}
```

### <a name="bindings-in-entity-classes"></a>Bindingen in entiteitsklassen

In tegenstelling tot reguliere functies hebben methoden voor entiteitsklasse geen directe toegang tot invoer- en uitvoerbindingen. In plaats daarvan moeten bindende gegevens worden vastgelegd in de `DispatchAsync<T>` entry-point functie declaratie en vervolgens worden doorgegeven aan de methode. Alle objecten `DispatchAsync<T>` die worden doorgegeven aan zal automatisch worden doorgegeven aan de entiteit klasse constructor als een argument.

In het volgende `CloudBlobContainer` voorbeeld ziet u hoe een verwijzing van de [blob-invoerbinding](../functions-bindings-storage-blob-input.md) beschikbaar kan worden gesteld aan een entiteit op basis van een klasse.

```csharp
public class BlobBackedEntity
{
    [JsonIgnore]
    private readonly CloudBlobContainer container;

    public BlobBackedEntity(CloudBlobContainer container)
    {
        this.container = container;
    }

    // ... entity methods can use this.container in their implementations ...
    
    [FunctionName(nameof(BlobBackedEntity))]
    public static Task Run(
        [EntityTrigger] IDurableEntityContext context,
        [Blob("my-container", FileAccess.Read)] CloudBlobContainer container)
    {
        // passing the binding object as a parameter makes it available to the
        // entity class constructor
        return context.DispatchAsync<BlobBackedEntity>(container);
    }
}
```

Zie de documentatie Azure Functions Triggers [and Bindings](../functions-triggers-bindings.md) voor meer informatie over bindingen in Azure-functies.

### <a name="dependency-injection-in-entity-classes"></a>Afhankelijkheidsinjectie in entiteitsklassen

Entiteitsklassen ondersteunen [Azure Functions Dependency Injection](../functions-dotnet-dependency-injection.md). In het volgende voorbeeld wordt `IHttpClientFactory` uitgelegd hoe u een service registreren bij een entiteit op basis van klasse.

```csharp
[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();
        }
    }
}
```

In het volgende fragment wordt uitgelegd hoe u de geïnjecteerde service opnemen in uw entiteitsklasse.

```csharp
public class HttpEntity
{
    [JsonIgnore]
    private readonly HttpClient client;

    public class HttpEntity(IHttpClientFactory factory)
    {
        this.client = factory.CreateClient();
    }

    public Task<int> GetAsync(string url)
    {
        using (var response = await this.client.GetAsync(url))
        {
            return (int)response.StatusCode;
        }
    }

    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> Om problemen met serialisatie te voorkomen, moet u velden uitsluiten die bedoeld zijn om geïnjecteerde waarden op te slaan van de serialisatie.

> [!NOTE]
> In tegenstelling tot bij het gebruik van constructor-injectie in reguliere .NET Azure-functies, *moet* de functie-invoerpuntmethode voor op klasse gebaseerde entiteiten worden gedeclareerd `static`. Het declareren van een niet-statisch functieitem kan conflicten veroorzaken tussen de oorspronkelijke initialisator van het object Azure Functions en de initialisator van het object Duurzame entiteiten.

## <a name="function-based-syntax"></a>Syntaxis op basis van functie

Tot nu toe hebben we ons gericht op de klasse-gebaseerde syntaxis, omdat we verwachten dat het beter geschikt is voor de meeste toepassingen. De op de functie gebaseerde syntaxis kan echter geschikt zijn voor toepassingen die hun eigen abstracties voor entiteitsstatus en -bewerkingen willen definiëren of beheren. Ook kan het geschikt zijn bij het implementeren van bibliotheken waarvoor genericiteit vereist is die momenteel niet wordt ondersteund door de syntaxis van de klasse. 

Met de op de functie gebaseerde syntaxis verwerkt de entiteitsfunctie expliciet de bewerkingsverzending en beheert de status van de entiteit expliciet. In de volgende code wordt bijvoorbeeld de entiteit *Teller* weergegeven die is geïmplementeerd met behulp van de op de functie gebaseerde syntaxis.  

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
        case "delete":
            ctx.DeleteState();
            break;
    }
}
```

### <a name="the-entity-context-object"></a>Het contextobject van de entiteit

Entiteitsspecifieke functionaliteit kan worden benaderd via een `IDurableEntityContext`contextobject van het type . Dit contextobject is beschikbaar als parameter voor de entiteitsfunctie `Entity.Current`en via de eigenschap async-local .

De volgende leden geven informatie over de huidige bewerking en stellen ons in staat een retourwaarde op te geven. 

* `EntityName`: de naam van de momenteel uitvoerende entiteit.
* `EntityKey`: de sleutel van de momenteel uitvoerende entiteit.
* `EntityId`: de id van de momenteel uitvoerende entiteit (inclusief naam en sleutel).
* `OperationName`: de naam van de huidige bewerking.
* `GetInput<TInput>()`: krijgt de invoer voor de huidige bewerking.
* `Return(arg)`: geeft een waarde terug aan de orkestratie die de bewerking heeft aangeroepen.

De volgende leden beheren de status van de entiteit (maken, lezen, bijwerken, verwijderen). 

* `HasState`: of de entiteit bestaat, dat wil zeggen, heeft een staat. 
* `GetState<TState>()`: krijgt de huidige status van de entiteit. Als het nog niet bestaat, wordt het gemaakt.
* `SetState(arg)`: maakt of actualiseert de status van de entiteit.
* `DeleteState()`: verwijdert de status van de entiteit als deze bestaat. 

Als de status `GetState` die wordt geretourneerd door een object is, kan deze rechtstreeks worden gewijzigd door de toepassingscode. Er is geen `SetState` noodzaak om opnieuw te bellen aan het einde (maar ook geen kwaad). Als `GetState<TState>` hetzelfde type meerdere keren wordt aangeroepen, moet het worden gebruikt.

Ten slotte worden de volgende leden gebruikt om andere entiteiten te signaleren of nieuwe orkestraties te starten:

* `SignalEntity(EntityId, operation, input)`: stuurt een eenrichtingsbericht naar een entiteit.
* `CreateNewOrchestration(orchestratorFunctionName, input)`: start een nieuwe orkestratie.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over entiteitsconcepten](durable-functions-entities.md)
