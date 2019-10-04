---
title: Duurzame entiteiten-Azure Functions
description: Meer informatie over duurzame entiteiten en hoe u deze kunt gebruiken in de Durable Functions extensie voor Azure Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: overview
ms.date: 08/31/2019
ms.author: azfuncdf
ms.openlocfilehash: 864a641968268c439c65996998cbb822746b96f9
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839003"
---
# <a name="entity-functions-preview"></a>Entiteit functies (preview-versie)

Met entiteits functies worden bewerkingen gedefinieerd voor het lezen en bijwerken van kleine stukjes status, ook wel *duurzame entiteiten*genoemd. Net als Orchestrator functions zijn entiteits functies functies met een speciaal trigger type, *entiteits trigger*. In tegens telling tot Orchestrator-functies hebben entiteits functies geen specifieke code beperkingen. Met entiteits functies wordt ook de status expliciet beheerd in plaats van impliciet de status via de controle stroom te vertegenwoordigen.

> [!NOTE]
> Entiteits functies en gerelateerde functionaliteit zijn alleen beschikbaar in Durable Functions 2,0 en hoger. Entiteits functies zijn momenteel beschikbaar als open bare preview.

## <a name="entity-identity"></a>Entiteits identiteit

Entiteiten (soms entiteit *instanties*genoemd) worden geopend via een unieke id, de *entiteit-id*. Een Entiteits-ID is gewoon een paar teken reeksen waarmee een entiteits exemplaar uniek wordt geïdentificeerd. Het bestaat uit:

* De **naam**van een entiteit: een naam die het type van de entiteit aangeeft (bijvoorbeeld ' counter ').
* Een **entiteits sleutel**: een teken reeks waarmee de entiteit wordt aangeduid met een unieke naam (bijvoorbeeld een GUID) van alle andere entiteiten.

Zo kan een *Counter* entity-functie worden gebruikt om de score in een online spel te bewaren. Elk exemplaar van het spel heeft een unieke entiteit-id, zoals `@Counter@Game1`, `@Counter@Game2`, enzovoort. Alle bewerkingen die gericht zijn op een bepaalde entiteit, moeten een entiteit-ID opgeven als para meter.

## <a name="programming-models"></a>Programmeer modellen

Duurzame entiteiten ondersteunen twee verschillende programmeer modellen. Het eerste model is een dynamisch ' functionele ' model waarbij de entiteit wordt gedefinieerd door één functie. Het tweede model is een object gericht model waarin de entiteit is gedefinieerd door een klasse en methoden. Deze modellen en de programmeer modellen voor interactie met entiteiten worden beschreven in de volgende secties.

### <a name="defining-entities"></a>Entiteiten definiëren

Er zijn twee optionele programmeer modellen voor het ontwerpen van duurzame entiteiten. De volgende code is een voor beeld van een entiteit met een eenvoudige *teller* die is geïmplementeerd als een standaard functie. Deze functie definieert drie *bewerkingen*, `add`, `reset`en `get`, die allemaal op een waarde voor een geheel getal worden `currentValue`uitgevoerd.

```csharp
[FunctionName("Counter")]
public static void Counter([EntityTrigger] IDurableEntityContext ctx)
{
    int currentValue = ctx.GetState<int>();

    switch (ctx.OperationName.ToLowerInvariant())
    {
        case "add":
            int amount = ctx.GetInput<int>();
            currentValue += amount;
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

Dit model werkt het beste voor eenvoudige entiteits implementaties of implementaties die een dynamische set bewerkingen hebben. U kunt echter ook een op klassen gebaseerd programmeer model gebruiken dat nuttig is voor entiteiten die statisch zijn, maar die complexere implementaties hebben. Het volgende voor beeld is een gelijkwaardige implementatie `Counter` van de entiteit met behulp van klassen en methoden.

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

In het op klassen gebaseerde programmeer model is het `IDurableEntityContext` object beschikbaar in de `Entity.Current` statische eigenschap.

Het model op basis van klassen is vergelijkbaar met het programmeer model dat is gepopulaird door [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/). In dit model wordt een entiteits type gedefinieerd als een .NET-klasse. Elke methode van de klasse is een bewerking die kan worden aangeroepen door een externe client. In tegens telling tot Orleans zijn .NET-interfaces echter optioneel. Het vorige *voor* beeld van het object heeft geen interface gebruikt, maar kan nog wel worden aangeroepen via andere functies of via HTTP-API-aanroepen.

> [!NOTE]
> Functies voor entiteits triggers zijn beschikbaar in Durable Functions 2,0 en hoger. Op dit moment zijn entiteits trigger functies alleen beschikbaar voor .NET-functie-apps.

### <a name="accessing-entities-from-clients"></a>Toegang tot entiteiten van clients

Duurzame entiteiten kunnen worden opgeroepen of opgevraagd uit normale functies, ook wel bekend als *client functies* , door gebruik te maken van de [client uitvoer binding](durable-functions-bindings.md#entity-client)van de entiteit. In het volgende voor beeld ziet u een door de wachtrij geactiveerde functie waarmee een entiteit wordt *gesignaleerd* met behulp van deze binding.

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

> [!NOTE]
> .NET-functies ondersteunen zowel niet-getypte als type veilige methoden voor het verzenden van entiteiten. Zie de documentatie voor de binding van de [entiteit client](durable-functions-bindings.md#entity-client-usage) voor meer informatie.

De term *Signal* geeft aan dat de aanroep van de entiteits-API in één richting en asynchroon is. Het is niet mogelijk om een *client functie* te laten weten wanneer de entiteit de bewerking heeft verwerkt, en het is ook mogelijk dat de functie entiteit een waarde retourneert naar een client functie. Eenrichtings berichten afhandeling op basis van een wachtrij is een opzettelijke ontwerp keuze voor duurzame entiteiten om de duurzaamheid van de prestaties te bepalen. Deze ontwerp keuze is een van de compromissen van duurzame entiteiten vergeleken met andere vergelijk bare technologieën. Momenteel kunnen alleen Orchestrations retour waarden van entiteiten verwerken, zoals beschreven in de volgende sectie.

Client functies kunnen ook de status van entiteiten opvragen, zoals wordt weer gegeven in het volgende voor beeld:

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

Entiteits status query's worden verzonden naar het duurzame opslag archief en retour neren de laatst *persistente* status van de entiteit. Het is mogelijk dat de geretourneerde status kan worden verouderd vergeleken met de in-memory status van de entiteit. Alleen indelingen kunnen de in-memory status van een entiteit lezen, zoals wordt beschreven in de volgende sectie.

### <a name="accessing-entities-from-orchestrations"></a>Toegang tot entiteiten vanuit Orchestrations

Orchestrator-functies hebben toegang tot entiteiten met behulp van Api's in de [Orchestration-trigger binding](durable-functions-bindings.md#orchestration-trigger). Orchestrator-functies kunnen kiezen tussen eenrichtings communicatie (brand en verg eten, ook wel *Signa lering*genoemd) en communicatie in twee richtingen (aanvraag en antwoord, ook wel *aanroep*genoemd). De volgende voorbeeld code toont een Orchestrator-functie waarmee een *item* entiteit wordt *aangeroepen* en *gesignaleerd* .

```csharp
[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    var entityId = new EntityId(nameof(Counter), "myCounter");

    // Synchronous call to the entity which returns a value - will await a response
    int currentValue = await context.CallEntityAsync<int>(entityId, "Get");
    if (currentValue < 10)
    {
        // Asynchronous call which updates the value - will not await a response
        context.SignalEntity(entityId, "Add", 1);
    }
}
```

Alleen indelingen kunnen het aanroepen van entiteiten en het verkrijgen van een reactie zijn, wat een retour waarde of een uitzonde ring kan zijn. Met client functies die gebruikmaken van de [client binding](durable-functions-bindings.md#entity-client) kunnen alleen entiteiten worden *gesignaleerd* .

> [!NOTE]
> Het aanroepen van een entiteit vanuit een functie orchestrtor is vergelijkbaar met het aanroepen van een [activiteit functie](durable-functions-types-features-overview.md#activity-functions) vanuit een Orchestrator-functie. Het belangrijkste verschil is dat entiteits functies duurzame objecten zijn met een adres (de *entiteit-id*) en ze ondersteunen het opgeven van een bewerkings naam. Activiteit functies zijn daarentegen stateless en hebben niet het concept van bewerkingen.

### <a name="dependency-injection-in-entity-classes-net"></a>Afhankelijkheids injectie in entity classes (.NET)

Entiteits klassen ondersteunen [Azure functions afhankelijkheids injectie](../functions-dotnet-dependency-injection.md). In het volgende voor beeld ziet u hoe u `IHttpClientFactory` een service registreert in een entiteit op basis van een klasse.

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

    // The function entry point must be declared static
    [FunctionName(nameof(HttpEntity))]
    public static Task Run([EntityTrigger] IDurableEntityContext ctx)
        => ctx.DispatchAsync<HttpEntity>();
}
```

> [!NOTE]
> In tegens telling tot het gebruik van constructor-injecties in reguliere .NET-Azure Functions, *moeten* de toegangs punt methode functions worden `static`gedeclareerd voor op klassen gebaseerde entiteiten. Het declareren van een niet-statisch functie-ingangs punt kan conflicten veroorzaken tussen de normale Azure Functions voor object initialisatie en de initialisatie functie voor het object van duurzame entiteiten.

### <a name="bindings-in-entity-classes-net"></a>Bindingen in entity classes (.NET)

In tegens telling tot reguliere functies hebben entity class-methoden geen directe toegang tot invoer-en uitvoer bindingen. In plaats daarvan moeten bindings gegevens worden vastgelegd in de declaratie van de invoer punt functie en vervolgens `DispatchAsync<T>` worden door gegeven aan de-methode. Objecten die worden door `DispatchAsync<T>` gegeven aan, worden automatisch door gegeven aan de constructor van de entiteits klasse als een argument.

In het volgende voor beeld ziet `CloudBlobContainer` u hoe een verwijzing van de [BLOB-invoer binding](../functions-bindings-storage-blob.md#input) beschikbaar kan worden gemaakt voor een entiteit op basis van klasse.

```csharp
public class BlobBackedEntity
{
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

## <a name="entity-coordination"></a>Entiteits coördinatie

Het kan voor komen dat u bewerkingen tussen meerdere entiteiten moet coördineren. In een bank toepassing kunt u bijvoorbeeld entiteiten hebben die individuele Bank rekeningen vertegenwoordigen. Wanneer u fondsen van het ene naar het andere account overbrengt, moet u ervoor zorgen dat het _bron_ account voldoende fondsen heeft en dat de updates van de _bron_ -en _doel_ accounts op een transactioneel consistente manier worden uitgevoerd.

### <a name="transfer-funds-example-in-c"></a>Voor beeld van overdrachts fondsen inC#

Met de volgende voorbeeld code worden fondsen tussen twee _account_ entiteiten overgedragen met behulp van een Orchestrator-functie. Het coördineren van entiteits updates vereist `LockAsync` het gebruik van de methode voor het maken van een _essentiële sectie_ in de indeling:

> [!NOTE]
> Voor het gemak wordt in dit voor beeld `Counter` de eerder gedefinieerde entiteit opnieuw gebruikt. In een echte toepassing is het echter beter om in plaats daarvan een meer gedetailleerde `BankAccount` entiteit te definiëren.

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

In .net `LockAsync` retourneert een `IDisposable` die de kritieke sectie afbreekt wanneer deze wordt verwijderd. Dit `IDisposable` resultaat kan worden gebruikt in combi natie `using` met een blok om een syntaxis weergave van de sectie kritiek te verkrijgen.

In het vorige voor beeld heeft een Orchestrator-functie fondsen van een _bron_ entiteit overgezet naar een _doel_ entiteit. De `LockAsync` methode heeft de _bron_ -en _doel_ account-entiteiten vergrendeld. Deze vergren deling heeft gezorgd dat een andere client de status van een van de accounts niet kan opvragen of wijzigen, totdat de indelings logica de _sectie kritiek_ heeft `using` verlaten aan het einde van de instructie. Zo voor komt u de mogelijkheid om te scha assen van het _bron_ account.

### <a name="critical-section-behavior"></a>Gedrag van kritieke secties

De `LockAsync` -methode maakt een _kritieke sectie_ in een indeling. Deze _essentiële secties_ kunnen voor komen dat andere indelingen overlappende wijzigingen aanbrengen in een opgegeven set entiteiten. Intern verzendt de `LockAsync` API "lock"-bewerkingen naar de entiteiten en worden geretourneerd wanneer het antwoord bericht "lock verworven" van elk van deze entiteiten wordt ontvangen. Zowel *vergren delen* als *ontgrendelen* zijn ingebouwde bewerkingen die door alle entiteiten worden ondersteund.

Er zijn geen bewerkingen van andere clients toegestaan voor een entiteit terwijl deze een vergrendelde status heeft. Dit gedrag zorgt ervoor dat er slechts één Orchestrator-exemplaar tegelijk een entiteit kan vergren delen. Als een aanroeper probeert een bewerking aan te roepen voor een entiteit terwijl deze is vergrendeld door een indeling, wordt die bewerking geplaatst in een *wachtrij voor bewerkingen in behandeling*. Er worden geen bewerkingen uitgevoerd totdat de vergren deling is vrijgegeven.

> [!NOTE] 
> Dit wijkt enigszins af van synchronisatie primitieven die worden gebruikt in de meeste programmeer talen, `lock` zoals de C#-instructie in. In C#moet bijvoorbeeld de `lock` instructie worden gebruikt door alle threads om te zorgen voor de juiste synchonization in meerdere threads. Voor entiteiten is het echter niet vereist dat alle bellers expliciet een entiteit _vergren delen_ . Als een beller een entiteit vergrendelt, worden alle andere bewerkingen op die entiteit geblokkeerd en achter deze vergren deling in de wachtrij geplaatst.

Vergren delingen op entiteiten zijn duurzaam, zodat ze behouden blijven, zelfs als het uitvoeren van het proces wordt gerecycled. Vergren delingen worden intern persistent gemaakt als onderdeel van de duurzame status van een entiteit.

### <a name="critical-section-restrictions"></a>Kritieke sectie beperkingen

We bieden verschillende beperkingen voor de manier waarop essentiële secties kunnen worden gebruikt. Deze beperkingen dienen om deadlocks en herbetreedbaarheid te voor komen.

* Kritieke secties kunnen niet worden genest.
* In essentiële secties kunnen geen subintegraties worden gemaakt.
* Kritieke secties kunnen alleen entiteiten aanroepen die ze hebben vergrendeld.
* Kritieke secties kunnen niet dezelfde entiteit aanroepen met meerdere parallelle aanroepen.
* Met kritieke secties kunnen alleen entiteiten worden gesignaleerd die niet zijn vergrendeld.

## <a name="comparison-with-virtual-actors"></a>Vergelijking met virtuele Actors

Veel van de functies van duurzame entiteiten zijn geïnspireerd op het [actor model](https://en.wikipedia.org/wiki/Actor_model). Als u al bekend bent met actors, kan het zijn dat u veel van de concepten die in dit artikel worden beschreven, kunt herkennen. Met name een duurzame entiteit is op verschillende manieren vergelijkbaar met [virtuele actoren](https://research.microsoft.com/projects/orleans/) :

* Duurzame entiteiten zijn adresseerbaar via een *entiteit-id*.
* De bewerkingen van een duurzame entiteit worden op een later tijdstip uitgevoerd om race voorwaarden te voor komen.
* Duurzame entiteiten worden automatisch gemaakt wanneer ze worden aangeroepen of gesignaleerd.
* Wanneer er geen bewerkingen worden uitgevoerd, worden duurzame entiteiten op de achtergrond uit het geheugen verwijderd.

Er zijn echter enkele belang rijke verschillen die te maken hebben met:

* Duurzame entiteiten bepalen de *duurzaamheid* over de *latentie*en zijn dus mogelijk niet geschikt voor toepassingen met strikte latentie vereisten.
* Berichten die tussen entiteiten worden verzonden, worden betrouwbaar en in de juiste volg orde bezorgd.
* Duurzame entiteiten kunnen worden gebruikt in combi natie met duurzame Orchestrations en bieden ondersteuning voor gedistribueerde vergrendelings mechanismen.
* Aanvraag/antwoord-patronen in entiteiten zijn beperkt tot de integratie. Voor communicatie tussen *clients* en entiteit *-naar-entiteit* is alleen eenrichtings berichten (ook wel ' Signa lering ' genoemd) toegestaan, zoals in het oorspronkelijke actor model. Dit gedrag voor komt gedistribueerde deadlocks.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Meer informatie over taak hubs](durable-functions-task-hubs.md)
