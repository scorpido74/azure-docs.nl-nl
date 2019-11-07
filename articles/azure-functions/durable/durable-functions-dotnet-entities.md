---
title: Ontwikkelaars handleiding voor duurzame entiteiten in .NET-Azure Functions
description: Werken met duurzame entiteiten in .NET met de extensie Durable Functions voor Azure Functions.
services: functions
author: sebastianburckhardt
manager: gwallace
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: azfuncdf
ms.openlocfilehash: a59e5443c80c9372f646edfdae2261157a41acc9
ms.sourcegitcommit: b2fb32ae73b12cf2d180e6e4ffffa13a31aa4c6f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/05/2019
ms.locfileid: "73614894"
---
# <a name="developers-guide-to-durable-entities-in-net"></a>Ontwikkelaars handleiding voor duurzame entiteiten in .NET

In dit artikel beschrijven we de beschik bare interfaces voor het ontwikkelen van duurzame entiteiten met .NET in detail, inclusief voor beelden en algemeen advies. 

Entiteits functies bieden ontwikkel aars van serverloze toepassingen een handige manier om de toepassings status te organiseren als een verzameling van verfijnde entiteiten. Zie het artikel over [duurzame entiteiten: concepten](durable-functions-entities.md) voor meer informatie over de onderliggende concepten.

Momenteel bieden we twee Api's voor het definiëren van entiteiten:

- De **op klassen gebaseerde syntaxis** vertegenwoordigt entiteiten en bewerkingen als klassen en methoden. Deze syntaxis produceert gemakkelijk Lees bare code en Hiermee kunnen bewerkingen worden aangeroepen door middel van een type ingeschakelde interface via interfaces. 

- De **syntaxis op basis van functies** is een interface op een lager niveau die entiteiten als functies vertegenwoordigt. Het biedt nauw keurige controle over hoe de entiteits bewerkingen worden verzonden en hoe de status van de entiteit wordt beheerd.  

Dit artikel richt zich voornamelijk op de op klassen gebaseerde syntaxis, aangezien we verwachten dat deze beter geschikt zijn voor de meeste toepassingen. De [syntaxis op basis van functies](#function-based-syntax) kan echter geschikt zijn voor toepassingen die hun eigen abstracties voor entiteits status en-bewerkingen willen definiëren of beheren. Het kan ook geschikt zijn voor het implementeren van bibliotheken die op dit moment niet worden ondersteund door de op klassen gebaseerde syntaxis. 

> [!NOTE]
> De op klassen gebaseerde syntaxis is slechts een laag bovenop de syntaxis op basis van functies, zodat beide varianten door elkaar kunnen worden gebruikt in dezelfde toepassing. 
 
## <a name="defining-entity-classes"></a>Entiteits klassen definiëren

Het volgende voor beeld is een implementatie van een `Counter`-entiteit die één waarde van het type integer opslaat en vier bewerkingen `Add`, `Reset`, `Get`en `Delete`biedt.

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

De functie `Run` bevat de standaard die is vereist voor het gebruik van de op klassen gebaseerde syntaxis. Dit moet een *statische* Azure-functie zijn. Deze wordt eenmaal uitgevoerd voor elk bewerkings bericht dat door de entiteit wordt verwerkt. Als `DispatchAsync<T>` wordt aangeroepen en de entiteit nog niet in het geheugen is, wordt een object van het type `T` gemaakt en worden de bijbehorende velden gevuld van de laatste persistente JSON die in de opslag is gevonden (indien van toepassing). Vervolgens wordt de-methode aangeroepen met de overeenkomende naam.

> [!NOTE]
> De status van een entiteit op basis van een klasse wordt **impliciet gemaakt** voordat de entiteit een bewerking verwerkt en kan expliciet in een bewerking worden **verwijderd** door `Entity.Current.DeleteState()`aan te roepen.

### <a name="class-requirements"></a>Klasse-vereisten
 
Entiteits klassen zijn POCOs (gewone, verouderde CLR-objecten) waarvoor geen speciale superklassen, interfaces of kenmerken zijn vereist. Indien

- De klasse moet constructible zijn (Zie [entiteits constructie](#entity-construction)).
- De klasse moet JSON-serialiseerbaar zijn (Zie [serialisatie van entiteit](#entity-serialization)).

Daarnaast moet de methode die is bedoeld om te worden aangeroepen als een bewerking, voldoen aan aanvullende vereisten:

- Een bewerking mag Maxi maal één argument hebben en mag geen Overloads of algemene type argumenten hebben.
- Een bewerking die moet worden aangeroepen vanuit een indeling met behulp van een interface, moet `Task` of `Task<T>`retour neren.
- Argumenten en retour waarden moeten serialiseerbare waarden of objecten zijn.

### <a name="what-can-operations-do"></a>Wat kunnen bewerkingen doen?

Alle entiteits bewerkingen kunnen de status van de entiteit lezen en bijwerken, en wijzigingen in de status worden automatisch opgeslagen in de opslag. Bovendien kunnen bewerkingen externe I/O-of andere berekeningen uitvoeren, binnen de algemene limieten die gelden voor alle Azure Functions.

Bewerkingen hebben ook toegang tot de functionaliteit van de `Entity.Current` context:

* `EntityName`: de naam van de entiteit die momenteel wordt uitgevoerd.
* `EntityKey`: de sleutel van de entiteit die momenteel wordt uitgevoerd.
* `EntityId`: de ID van de entiteit die momenteel wordt uitgevoerd (inclusief naam en sleutel).
* `SignalEntity`: verzendt een eenrichtings bericht naar een entiteit.
* `CreateNewOrchestration`: er wordt een nieuwe indeling gestart.
* `DeleteState`: de status van deze entiteit wordt verwijderd.

We kunnen de entiteit teller bijvoorbeeld zodanig wijzigen dat een indeling wordt gestart wanneer de teller 100 bereikt en de entiteit-ID als invoer argument wordt door gegeven:

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

## <a name="accessing-entities-directly"></a>Rechtstreeks toegang tot entiteiten

Op klassen gebaseerde entiteiten kunnen rechtstreeks worden geopend, met behulp van expliciete teken reeks namen voor de entiteit en de bijbehorende bewerkingen. Hieronder vindt u enkele voor beelden. Zie de discussie over het [openen van entiteiten](durable-functions-entities.md#accessing-entities)voor een diep gaande uitleg van de onderliggende concepten (zoals signalen versus aanroepen). 

> [!NOTE]
> Waar mogelijk wordt u aangeraden om [toegang te krijgen tot entiteiten via interfaces](#accessing-entities-through-interfaces), omdat deze meer type controle biedt.

### <a name="example-client-signals-entity"></a>Voor beeld: entiteit client signalen

De volgende Azure http-functie implementeert een Verwijder bewerking met behulp van REST-conventies. Er wordt een delete-signaal verzonden naar de item entiteit waarvan de sleutel wordt door gegeven in het URL-pad.

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

### <a name="example-client-reads-entity-state"></a>Voor beeld: client leest entiteits status

De volgende Azure http-functie implementeert een GET-bewerking met behulp van REST-conventies. Hiermee wordt de huidige status van de item entiteit gelezen waarvan de sleutel wordt door gegeven in het URL-pad.

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
> Het object dat door `ReadEntityStateAsync` wordt geretourneerd, is slechts een lokale kopie, dat wil zeggen, een moment opname van de entiteits status van een eerder tijdstip. Dit kan met name verlopen en het wijzigen van dit object heeft geen invloed op de werkelijke entiteit. 

### <a name="example-orchestration-first-signals-then-calls-entity"></a>Voor beeld: Orchestration-eerste signalen en vervolgens entiteit aanroepen

In de volgende indeling wordt een item entiteit gesignaleerd om deze te verhogen en vervolgens wordt dezelfde entiteit aangeroepen om de meest recente waarde te lezen.

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

Interfaces kunnen worden gebruikt voor toegang tot entiteiten via gegenereerde proxy-objecten. Deze aanpak zorgt ervoor dat de naam en het argument type van een bewerking overeenkomt met wat er wordt geïmplementeerd. U wordt aangeraden interfaces te gebruiken voor het verkrijgen van toegang tot entiteiten wanneer dat mogelijk is.

Zo kunnen we het voor beeld van het item als volgt wijzigen:

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

Entiteits klassen en entiteits interfaces zijn vergelijkbaar met die van de korrels en korrels die worden gepopulaird door [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/). Zie [vergelijking met virtuele actors](durable-functions-entities.md#comparison-with-virtual-actors)voor meer informatie over overeenkomsten en verschillen tussen duurzame entiteiten en Orleans.

Naast het leveren van type controle zijn interfaces handig voor een betere schei ding van de problemen binnen de toepassing. Omdat een entiteit bijvoorbeeld meerdere interfaces kan implementeren, kan één entiteit meerdere rollen hebben. Omdat een interface mogelijk door meerdere entiteiten kan worden geïmplementeerd, kunnen algemene communicatie patronen worden geïmplementeerd als herbruikbare bibliotheken.

### <a name="example-client-signals-entity-through-interface"></a>Voor beeld: client signaleert entiteit via interface

Client code kan `SignalEntityAsync<TEntityInterface>` gebruiken om signalen te verzenden naar entiteiten die `TEntityInterface`implementeren. Bijvoorbeeld:

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

In dit voor beeld is de para meter `proxy` een dynamisch gegenereerd exemplaar van `ICounter`, dat intern de aanroep naar `Delete` in een signaal zet.

> [!NOTE]
> De `SignalEntityAsync`-Api's kunnen alleen worden gebruikt voor eenrichtings bewerkingen. Zelfs als een bewerking `Task<T>`retourneert, is de waarde van de para meter `T` altijd Null of `default`, niet het werkelijke resultaat.
Het is bijvoorbeeld niet zinvol om de `Get` bewerking te Signa leren, omdat er geen waarde wordt geretourneerd. In plaats daarvan kunnen clients `ReadStateAsync` gebruiken om rechtstreeks toegang te krijgen tot de status van het item, of kan een Orchestrator-functie worden gestart die de `Get`-bewerking aanroept. 

### <a name="example-orchestration-first-signals-then-calls-entity-through-proxy"></a>Voor beeld: Orchestration-eerste signalen en vervolgens entiteit aanroepen via proxy

Als u een entiteit vanuit een indeling wilt aanroepen of als u een signaal wilt ontvangen, kunt u `CreateEntityProxy` gebruiken, samen met het interface type, om een proxy voor de entiteit te genereren. Deze proxy kan vervolgens worden gebruikt om bewerkingen uit te voeren of te Signa leren:

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

Alle bewerkingen die `void` retour neren, worden impliciet gesignaleerd en alle bewerkingen die `Task` of `Task<T>` retour neren worden genoemd. Eén kan dit standaard gedrag wijzigen en signaal bewerkingen, zelfs als ze een taak retour neren met behulp van de methode `SignalEntity<IInterfaceType>` expliciet.

### <a name="shorter-option-for-specifying-the-target"></a>Kortere optie voor het opgeven van het doel

Wanneer u een entiteit aanroept of signaleert met behulp van een interface, moet in het eerste argument de doel entiteit worden opgegeven. U kunt het doel opgeven door de entiteit-ID op te geven, of, in gevallen waarin er slechts één klasse is die de entiteit implementeert, alleen de entiteits sleutel:

```csharp
context.SignalEntity<ICounter>(new EntityId(nameof(Counter), "myCounter"), ...);
context.SignalEntity<ICounter>("myCounter", ...);
```

Als alleen de entiteits sleutel is opgegeven en tijdens runtime geen unieke implementatie wordt gevonden, wordt `InvalidOperationException` gegenereerd. 

### <a name="restrictions-on-entity-interfaces"></a>Beperkingen voor entiteits interfaces

Zoals gebruikelijk, moeten alle para meters en retour typen JSON-serialiseerbaar zijn. Anders worden er uitzonde ringen voor serialisatie gegenereerd tijdens runtime.

Daarnaast worden enkele extra regels afgedwongen:
* Entiteits interfaces moeten alleen methoden definiëren.
* Entiteits interfaces mogen geen generieke para meters bevatten.
* De methoden van de entity-interface mogen niet meer dan één para meter hebben.
* De methoden van de entity-interface moeten `void`, `Task`of `Task<T>` retour neren 

Als een van deze regels wordt geschonden, wordt tijdens runtime een `InvalidOperationException` gegenereerd wanneer de interface wordt gebruikt als een type argument voor `SignalEntity` of `CreateProxy`. In het uitzonderings bericht wordt uitgelegd welke regel is verbroken.

> [!NOTE]
> Interface methoden die `void` retour neren, kunnen alleen worden gesignaleerd (eenrichtings), niet aangeroepen (twee richtingen). Interface methoden die `Task` of `Task<T>` retour neren, kunnen worden aangeroepen of gedeponeerd zijn. Als deze wordt aangeroepen, retour neren ze het resultaat van de bewerking of genereren ze uitzonde ringen die door de bewerking zijn gegenereerd. Als ze echter worden geresulteerd, retour neren ze niet het werkelijke resultaat of de uitzonde ring van de bewerking, maar alleen de standaard waarde.

## <a name="entity-serialization"></a>Serialisatie van entiteit

Omdat de status van een entiteit blijvend persistent is, moet de entiteits klasse serialiseerbaar zijn. De Durable Functions runtime maakt gebruik van de [JSON.net](https://www.newtonsoft.com/json) -bibliotheek voor dit doel, die een aantal beleids regels en kenmerken ondersteunt om het serialisatie-en deserialisatie proces te beheren. Meest gebruikte C# gegevens typen (inclusief matrices en verzamelings typen) zijn al serialiseerbaar en kunnen eenvoudig worden gebruikt voor het definiëren van de status van duurzame entiteiten.

Json.NET kan bijvoorbeeld eenvoudig de volgende klasse serialiseren en deserialiseren:

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

### <a name="serialization-attributes"></a>Serialisatie kenmerken

In het bovenstaande voor beeld hebben we gekozen om verschillende kenmerken op te nemen om de onderliggende serialisatie meer zichtbaar te maken:
- We maken aantekeningen op de klasse met `[JsonObject(MemberSerialization.OptIn)]` om ons eraan te herinneren dat de klasse serialiseerbaar moet zijn en om alleen leden te blijven die expliciet zijn gemarkeerd als JSON-eigenschappen.
-  We geven aantekeningen aan de velden die moeten worden vastgehouden met `[JsonProperty("name")]` om eraan te herinneren dat een veld deel uitmaakt van de persistente entiteits status en om de naam van de eigenschap die in de JSON-weer gave moet worden gebruikt te specificeren.

Deze kenmerken zijn echter niet vereist. andere conventies of kenmerken zijn toegestaan zolang ze met Json.NET werken. Een voor beeld: een kan `[DataContract]` kenmerken of helemaal geen kenmerken gebruiken.

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

De naam van de klasse wordt standaard *niet* opgeslagen als onderdeel van de JSON-weer gave. dat wil zeggen dat we `TypeNameHandling.None` als standaard instelling gebruiken. Dit standaard gedrag kan worden overschreven met `JsonObject`-of `JsonProperty`-kenmerken.

### <a name="making-changes-to-class-definitions"></a>Wijzigingen aanbrengen in klassen definities

Let op wanneer u wijzigingen aanbrengt in een klassen definitie nadat een toepassing is uitgevoerd, omdat het opgeslagen JSON-object mogelijk niet meer overeenkomt met de nieuwe klassedefinitie. Het is echter vaak mogelijk om goed te kunnen omgaan met veranderende gegevens indelingen, zolang het deserialisatie proces dat wordt gebruikt door `JsonConvert.PopulateObject`wordt begrepen.

Hier volgen enkele voor beelden van wijzigingen en hun effect:

1. Als er een nieuwe eigenschap wordt toegevoegd, die niet aanwezig is in de opgeslagen JSON, wordt ervan uitgegaan dat deze de standaard waarde heeft.
1. Als een eigenschap wordt verwijderd, die aanwezig is in de opgeslagen JSON, gaat de vorige inhoud verloren.
1. Als de naam van een eigenschap wordt gewijzigd, is het effect alsof het oude wordt verwijderd en een nieuw item wordt toegevoegd.
1. Als het type van een eigenschap wordt gewijzigd zodat deze niet meer kan worden gedeserialiseerd van de opgeslagen JSON, wordt een uitzonde ring gegenereerd.
1. Als het type van een eigenschap wordt gewijzigd, maar het kan nog steeds worden gedeserialiseerd van de opgeslagen JSON, wordt dit gedaan.

Er zijn veel opties beschikbaar voor het aanpassen van het gedrag van Json.NET. Als u bijvoorbeeld een uitzonde ring wilt afdwingen als de opgeslagen JSON een veld bevat dat niet aanwezig is in de-klasse, geeft u het kenmerk `JsonObject(MissingMemberHandling = MissingMemberHandling.Error)`op. Het is ook mogelijk om aangepaste code te schrijven voor deserialisatie, waardoor JSON kan worden gelezen die in wille keurige indelingen is opgeslagen.

## <a name="entity-construction"></a>Entiteits constructie

Soms willen we meer controle uitoefenen over hoe entiteits objecten worden samengesteld. We beschrijven nu diverse opties voor het wijzigen van het standaard gedrag bij het maken van entiteits objecten. 

### <a name="custom-initialization-on-first-access"></a>Aangepaste initialisatie bij eerste toegang

Af en toe moeten we enige speciale initialisatie uitvoeren voordat een bewerking wordt verzonden naar een entiteit die nooit is geopend of die is verwijderd. Als u dit gedrag wilt opgeven, kunt u een voorwaardelijke actie toevoegen vóór de `DispatchAsync`:

```csharp
[FunctionName(nameof(Counter))]
public static Task Run([EntityTrigger] IDurableEntityContext ctx)
{
    if (!ctx.HasState)
    {
        ctx.SetState(...);
    }
    ctx.DispatchAsync<Counter>();
}
```

### <a name="bindings-in-entity-classes"></a>Bindingen in entiteits klassen

In tegens telling tot reguliere functies hebben entity class-methoden geen directe toegang tot invoer-en uitvoer bindingen. In plaats daarvan moeten bindings gegevens worden vastgelegd in de declaratie van de invoer punt functie en vervolgens worden door gegeven aan de `DispatchAsync<T>` methode. Objecten die aan `DispatchAsync<T>` worden door gegeven, worden automatisch door gegeven aan de constructor van de entiteits klasse als een argument.

In het volgende voor beeld ziet u hoe een `CloudBlobContainer` referentie van de [BLOB-invoer binding](../functions-bindings-storage-blob.md#input) beschikbaar kan worden gemaakt voor een entiteit op basis van een klasse.

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

Zie de documentatie voor [Azure functions triggers en bindingen](../functions-triggers-bindings.md) voor meer informatie over bindingen in azure functions.

### <a name="dependency-injection-in-entity-classes"></a>Afhankelijkheids injectie in entiteits klassen

Entiteits klassen ondersteunen [Azure functions afhankelijkheids injectie](../functions-dotnet-dependency-injection.md). In het volgende voor beeld ziet u hoe u een `IHttpClientFactory`-service registreert bij een entiteit op basis van een klasse.

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

Het volgende code fragment laat zien hoe u de geïnjecteerde service kunt opnemen in uw entiteits klasse.

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
> Als u problemen met serialisatie wilt voor komen, moet u ervoor zorgen dat velden die bedoeld zijn voor het opslaan van geïnjecteerde waarden uit de serialisatie uitsluiten.

> [!NOTE]
> In tegens telling tot het gebruik van constructor-injectie in reguliere .NET Azure Functions, *moeten* de toegangs punt methode functions worden gedeclareerd `static`. Het declareren van een niet-statisch functie-ingangs punt kan conflicten veroorzaken tussen de normale Azure Functions voor object initialisatie en de initialisatie functie voor het object van duurzame entiteiten.

## <a name="function-based-syntax"></a>Syntaxis op basis van functies

Tot nu toe hebben we gefocust op de op klassen gebaseerde syntaxis, aangezien we verwachten dat deze beter geschikt zijn voor de meeste toepassingen. De syntaxis op basis van functies kan echter geschikt zijn voor toepassingen die hun eigen abstracties voor entiteits status en-bewerkingen willen definiëren of beheren. Het kan ook geschikt zijn bij het implementeren van bibliotheken die op dit moment niet worden ondersteund door de op klassen gebaseerde syntaxis. 

Met de functie-gebaseerde syntaxis wordt de bewerking door de entiteits functie expliciet verwerkt en wordt de status van de entiteit expliciet beheerd. De volgende code toont bijvoorbeeld de *item* entiteit die is geïmplementeerd met behulp van de syntaxis op basis van de functie.  

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

### <a name="the-entity-context-object"></a>Het context object entiteit

U kunt toegang krijgen tot entiteit-specifieke functionaliteit via een context object van het type `IDurableEntityContext`. Dit context object is beschikbaar als een para meter voor de functie entiteit en via de eigenschap async-Local `Entity.Current`.

De volgende leden bieden informatie over de huidige bewerking en kunnen we een retour waarde opgeven. 

* `EntityName`: de naam van de entiteit die momenteel wordt uitgevoerd.
* `EntityKey`: de sleutel van de entiteit die momenteel wordt uitgevoerd.
* `EntityId`: de ID van de entiteit die momenteel wordt uitgevoerd (inclusief naam en sleutel).
* `OperationName`: de naam van de huidige bewerking.
* `GetInput<TInput>()`: Hiermee wordt de invoer voor de huidige bewerking opgehaald.
* `Return(arg)`: retourneert een waarde voor de indeling die de bewerking aanroept.

De volgende leden beheren de status van de entiteit (maken, lezen, bijwerken, verwijderen). 

* `HasState`: of de entiteit bestaat, dat wil zeggen een bepaalde status heeft. 
* `GetState<TState>()`: Hiermee wordt de huidige status van de entiteit opgehaald. Als deze nog niet bestaat, wordt deze gemaakt.
* `SetState(arg)`: Hiermee wordt de status van de entiteit gemaakt of bijgewerkt.
* `DeleteState()`: de status van de entiteit wordt verwijderd als deze bestaat. 

Als de status die is geretourneerd door `GetState` een object is, kan het rechtstreeks worden gewijzigd door de toepassings code. Het is niet nodig om `SetState` aan het einde aan te roepen (maar ook geen schade). Als `GetState<TState>` meerdere keren wordt aangeroepen, moet hetzelfde type worden gebruikt.

Ten slotte worden de volgende leden gebruikt om andere entiteiten te Signa leren, of om nieuwe integraties te starten:

* `SignalEntity(EntityId, operation, input)`: verzendt een eenrichtings bericht naar een entiteit.
* `CreateNewOrchestration(orchestratorFunctionName, input)`: er wordt een nieuwe indeling gestart.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over entiteits concepten](durable-functions-entities.md)
