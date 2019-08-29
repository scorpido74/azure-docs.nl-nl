---
title: Preview-functies Durable Functions-Azure Functions
description: Meer informatie over preview-functies voor Durable Functions.
services: functions
author: cgillum
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: article
ms.date: 07/08/2019
ms.author: azfuncdf
ms.openlocfilehash: 94784ee9a72d11e168828f1543cbe62219ac4a74
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70097943"
---
# <a name="durable-functions-20-preview-azure-functions"></a>Durable Functions 2,0 Preview (Azure Functions)

*Durable functions* is een uitbrei ding van [Azure functions](../functions-overview.md) en [Azure WebJobs](../../app-service/web-sites-create-web-jobs.md) waarmee u stateful functies in een serverloze omgeving kunt schrijven. Met de extensie worden status, controlepunten en het opnieuw opstarten voor u beheerd. Als u nog niet bekend bent met Durable Functions, raadpleegt u de documentatie van het [overzicht](durable-functions-overview.md).

Durable Functions 1. x is een functie voor het maken van een GA (algemeen beschikbaar) van Azure Functions, maar bevat ook verschillende subonderdelen die momenteel zijn opgenomen in de open bare preview. In dit artikel worden nieuwe uitgebrachte preview-functies beschreven en wordt uitgelegd hoe ze werken en hoe u ze kunt gaan gebruiken.

> [!NOTE]
> Deze preview-functies maken deel uit van een Durable Functions 2,0-release, die momenteel een **Preview-kwaliteits release** is met verschillende belang rijke wijzigingen. De Azure Functions duurzame uitbreidings pakket builds kunt u vinden op nuget.org met versies in de vorm van **2.0.0-betax**. Deze builds zijn niet bedoeld voor productie werkbelastingen en volgende releases kunnen extra belang rijke wijzigingen bevatten.

## <a name="breaking-changes"></a>Wijzigingen die fouten veroorzaken

Verschillende belang rijke wijzigingen worden geïntroduceerd in Durable Functions 2,0. Bestaande toepassingen zijn niet naar verwachting compatibel met Durable Functions 2,0 zonder code wijzigingen. In deze sectie vindt u enkele van de wijzigingen:

### <a name="hostjson-schema"></a>Host. json-schema

Het volgende code fragment toont het nieuwe schema voor host. json. De belangrijkste wijzigingen die u moet herkennen, zijn de nieuwe subsecties:

* `"storageProvider"`(en de `"azureStorage"` Subsectie) voor Storage-specifieke configuratie
* `"tracking"`voor het bijhouden en registreren van configuratie
* `"notifications"`(en de `"eventGrid"` Subsectie) voor configuratie van gebeurtenis raster meldingen

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "azureStorage": {
          "connectionStringName": <string>,
          "controlQueueBatchSize": <int?>,
          "partitionCount": <int?>,
          "controlQueueVisibilityTimeout": <hh:mm:ss?>,
          "workItemQueueVisibilityTimeout": <hh:mm:ss?>,
          "trackingStoreConnectionStringName": <string?>,
          "trackingStoreNamePrefix": <string?>,
          "maxQueuePollingInterval": <hh:mm:ss?>
        }
      },
      "tracking": {
        "traceInputsAndOutputs": <bool?>,
        "traceReplayEvents": <bool?>,
      },
      "notifications": {
        "eventGrid": {
          "topicEndpoint": <string?>,
          "keySettingName": <string?>,
          "publishRetryCount": <string?>,
          "publishRetryInterval": <hh:mm:ss?>,
          "publishRetryHttpStatus": <int[]?>,
          "publishEventTypes": <string[]?>,
        }
      },
      "maxConcurrentActivityFunctions": <int?>,
      "maxConcurrentOrchestratorFunctions": <int?>,
      "extendedSessionsEnabled": <bool?>,
      "extendedSessionIdleTimeoutInSeconds": <int?>,
      "customLifeCycleNotificationHelperType": <string?>
  }
}
```

Als Durable functions 2,0 blijft stabiliseren, worden er meer wijzigingen aangebracht in de `durableTask` sectie host. json. Zie [Dit github-probleem](https://github.com/Azure/azure-functions-durable-extension/issues/641)voor meer informatie over deze wijzigingen.

### <a name="public-interface-changes"></a>Wijzigingen in de open bare interface

De verschillende ' context ' objecten die door Durable Functions worden ondersteund, bevatten abstracte basis klassen die zijn bedoeld voor gebruik in eenheids testen. Als onderdeel van Durable Functions 2,0 zijn deze abstracte basis klassen vervangen door interfaces. Functie code die rechtstreeks gebruikmaakt van de concrete typen, wordt niet beïnvloed.

De volgende tabel bevat de belangrijkste wijzigingen:

| Oud type | Nieuw type |
|----------|----------|
| DurableOrchestrationClientBase | IDurableOrchestrationClient |
| DurableOrchestrationContextBase | IDurableOrchestrationContext |
| DurableActivityContextBase | IDurableActivityContext |

Als een abstracte basis klasse virtuele methoden bevatte, zijn deze virtuele methoden vervangen door extensie methoden die zijn gedefinieerd in `DurableContextExtensions`.

## <a name="entity-functions"></a>Entiteit functies

Met entiteits functies worden bewerkingen gedefinieerd voor het lezen en bijwerken van kleine stukjes status, ook wel *duurzame entiteiten*genoemd. Net als Orchestrator functions zijn entiteits functies functies met een speciaal trigger type, *entiteits trigger*. In tegens telling tot Orchestrator-functies hebben entiteits functies geen specifieke code beperkingen. Met entiteits functies wordt ook de status expliciet beheerd in plaats van impliciet de status via de controle stroom te vertegenwoordigen.

### <a name="net-programing-models"></a>.NET-programma modellen

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

Dit model werkt het beste voor eenvoudige entiteits implementaties of implementaties die een dynamische set bewerkingen hebben. Er is echter ook een op klassen gebaseerd programmeer model dat nuttig is voor entiteiten die statisch zijn, maar die complexere implementaties hebben. Het volgende voor beeld is een gelijkwaardige implementatie `Counter` van de entiteit met behulp van .net-klassen en-methoden.

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

Het model op basis van klassen is vergelijkbaar met het programmeer model dat is gepopulaird door [Orleans](https://www.microsoft.com/research/project/orleans-virtual-actors/). In dit model wordt een entiteits type gedefinieerd als een .NET-klasse. Elke methode van de klasse is een bewerking die kan worden aangeroepen door een externe client. In tegens telling tot Orleans zijn .NET-interfaces echter optioneel. Het vorige voor beeld van het object heeft geen interface gebruikt, maar kan nog wel worden aangeroepen via andere functies of via HTTP-API-aanroepen.

Entiteit *exemplaren* worden geopend via een unieke id, de *entiteit-id*. Een Entiteits-ID is gewoon een paar teken reeksen waarmee een entiteits exemplaar uniek wordt geïdentificeerd. Het bestaat uit:

* De **naam**van een entiteit: een naam die het type van de entiteit aangeeft (bijvoorbeeld ' counter ').
* Een **entiteits sleutel**: een teken reeks waarmee de entiteit wordt aangeduid met een unieke naam (bijvoorbeeld een GUID) van alle andere entiteiten.

Zo kan een *Counter* entity-functie worden gebruikt om de score in een online spel te bewaren. Elk exemplaar van het spel heeft een unieke entiteit-id, zoals `@Counter@Game1`, `@Counter@Game2`, enzovoort.

### <a name="comparison-with-virtual-actors"></a>Vergelijking met virtuele Actors

Het ontwerp van duurzame entiteiten wordt sterk beïnvloed door het [actor model](https://en.wikipedia.org/wiki/Actor_model). Als u al bekend bent met actors, moeten de concepten achter duurzame entiteiten bekend zijn. Met name een duurzame entiteit is op verschillende manieren vergelijkbaar met [virtuele actoren](https://research.microsoft.com/projects/orleans/) :

* Duurzame entiteiten zijn adresseerbaar via een *entiteit-id*.
* De bewerkingen van een duurzame entiteit worden op een later tijdstip uitgevoerd om race voorwaarden te voor komen.
* Duurzame entiteiten worden automatisch gemaakt wanneer ze worden aangeroepen of gesignaleerd.
* Wanneer er geen bewerkingen worden uitgevoerd, worden duurzame entiteiten op de achtergrond uit het geheugen verwijderd.

Er zijn echter enkele belang rijke verschillen die te maken hebben met:

* Duurzame entiteiten bepalen de *duurzaamheid* over de *latentie*en zijn dus mogelijk niet geschikt voor toepassingen met strikte latentie vereisten.
* Berichten die tussen entiteiten worden verzonden, worden betrouwbaar en in de juiste volg orde bezorgd.
* Duurzame entiteiten kunnen worden gebruikt in combi natie met duurzame Orchestrations en kunnen fungeren als gedistribueerde vergren deling, die verderop in dit artikel worden beschreven.
* Aanvraag/antwoord-patronen in entiteiten zijn beperkt tot de integratie. Voor communicatie tussen entiteiten zijn alleen eenrichtings berichten (ook wel ' Signa lering ' genoemd) toegestaan, zoals in het oorspronkelijke actor model. Dit gedrag voor komt gedistribueerde deadlocks.

### <a name="durable-entity-net-apis"></a>Duurzame entiteit .NET-Api's

Voor de ondersteuning van entiteiten zijn meerdere Api's vereist. Een voor beeld: er is een nieuwe API voor het definiëren van entiteits functies, zoals hierboven wordt weer gegeven. hier kunt u opgeven wat er moet gebeuren wanneer een bewerking wordt aangeroepen voor een entiteit. Daarnaast zijn bestaande Api's voor-clients en-indelingen bijgewerkt met nieuwe functionaliteit voor interactie met entiteiten.

#### <a name="implementing-entity-operations"></a>Entiteits bewerkingen implementeren

De uitvoering van een bewerking op een entiteit kan deze leden aanroepen in het context object`IDurableEntityContext` (in .net):

* **Operationname**: Hiermee wordt de naam van de bewerking opgehaald.
* **GetInput\<TInput >** : Hiermee wordt de invoer voor de bewerking opgehaald.
* **GetState\<TState >** : Hiermee wordt de huidige status van de entiteit opgehaald.
* **SetState**: de status van de entiteit wordt bijgewerkt.
* **SignalEntity**: verzendt een eenrichtings bericht naar een entiteit.
* **Self**: Hiermee wordt de id van de entiteit opgehaald.
* **Retour**: retourneert een waarde voor de client of indeling die de bewerking wordt genoemd.
* **IsNewlyConstructed**: retourneert `true` of de entiteit niet bestaat voordat de bewerking werd uitgevoerd.
* **DestructOnExit**: de entiteit wordt verwijderd nadat de bewerking is voltooid.

Bewerkingen zijn minder beperkt dan Orchestrations:

* Bewerkingen kunnen externe I/O aanroepen met behulp van synchrone of asynchrone Api's (het wordt aangeraden alleen asynchrone items te gebruiken).
* Bewerkingen kunnen niet-deterministisch zijn. Het is bijvoorbeeld veilig om te bellen `DateTime.UtcNow` `Guid.NewGuid()` of `new Random()`.

#### <a name="accessing-entities-from-clients"></a>Toegang tot entiteiten van clients

Duurzame entiteiten kunnen worden aangeroepen vanuit normale functies via de `orchestrationClient` binding (`IDurableOrchestrationClient` in .net). De volgende methoden worden ondersteund:

* **ReadEntityStateAsync\<T >** : de status van een entiteit wordt gelezen.
* **SignalEntityAsync**: Hiermee verzendt u een bericht in één richting naar een entiteit, waarna wordt gewacht tot deze in de wachtrij is geplaatst.
* **SignalEntityAsync\<T >** : zelfde als `SignalEntityAsync` maar gebruikt een gegenereerd proxy object van het `T`type.

Voor de `SignalEntityAsync` vorige aanroep moet u de naam van de entiteits bewerking `string` opgeven als a en de payload van de `object`bewerking als een. De volgende voorbeeld code is een voor beeld van dit patroon:

```csharp
EntityId id = // ...
object amount = 5;
context.SignalEntityAsync(id, "Add", amount);
```

Het is ook mogelijk om een proxy-object te genereren voor type veilige toegang. Voor het genereren van een type veilige proxy moet het entiteits type een interface implementeren. Stel bijvoorbeeld dat de `Counter` entiteit die eerder een `ICounter` interface heeft genoemd, als volgt heeft geïmplementeerd:

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

Client code kan vervolgens worden `SignalEntityAsync<T>` gebruikt en de `ICounter` interface opgeven als de type parameter voor het genereren van een type-veilige proxy. Dit gebruik van type veilige proxy's wordt geïllustreerd in het volgende code voorbeeld:

```csharp
[FunctionName("UserDeleteAvailable")]
public static async Task AddValueClient(
    [QueueTrigger("my-queue")] string message,
    [OrchestrationClient] IDurableOrchestrationClient client)
{
    int amount = int.Parse(message);
    var target = new EntityId(nameof(Counter), "MyCounter");
    await client.SignalEntityAsync<ICounter>(target, proxy => proxy.Add(amount));
}
```

In het vorige voor beeld is `proxy` de para meter een dynamisch gegenereerd exemplaar `ICounter`van, waarmee de aanroep naar `Add` de `SignalEntityAsync`equivalente (niet-getypte) aanroep wordt omgezet in.

Voor de type parameter `SignalEntityAsync<T>` voor gelden de volgende beperkingen:

* De type parameter moet een interface zijn.
* Op de interface kunnen alleen methoden worden gedefinieerd. Eigenschappen worden niet ondersteund.
* Elke methode moet een of geen para meters definiëren.
* Elke methode moet een van `void`de `Task`volgende typen `Task<T>` of `T` een JSON-serializeable retour neren.
* De interface moet worden geïmplementeerd door precies één type in de assembly van de interface.

In de meeste gevallen wordt een runtime-uitzonde ring veroorzaakt door interfaces die niet aan deze vereisten voldoen.

> [!NOTE]
> Het is belang rijk te weten dat `ReadEntityStateAsync` de `SignalEntityAsync` methoden en `IDurableOrchestrationClient` de prioriteit van de prestaties ten opzichte van consistentie zijn. `ReadEntityStateAsync`een verouderde waarde kan worden `SignalEntityAsync` geretourneerd en kan worden geretourneerd voordat de bewerking is voltooid.

#### <a name="accessing-entities-from-orchestrations"></a>Toegang tot entiteiten vanuit Orchestrations

Orchestrations hebben toegang tot entiteiten die `IDurableOrchestrationContext` het object gebruiken. Ze kunnen kiezen tussen eenrichtings communicatie (vuur en verg eten) en communicatie in twee richtingen (aanvraag en antwoord). De respectieve methoden zijn:

* **SignalEntity**: verzendt een eenrichtings bericht naar een entiteit.
* **CallEntityAsync**: verzendt een bericht naar een entiteit en wacht op een antwoord dat aangeeft dat de bewerking is voltooid.
* **CallEntityAsync\<T >** : er wordt een bericht naar een entiteit verzonden en er wordt gewacht op een antwoord met een resultaat van het type T.

Wanneer u twee richtings communicatie gebruikt, worden eventuele uitzonde ringen die zijn opgetreden tijdens de uitvoering van de bewerking, ook teruggestuurd naar de aanroep-indeling en opnieuw genereren. Bij het gebruik van Fire-en-vergeet daarentegen worden uitzonde ringen niet in acht genomen.

Voor type veilige toegang kunnen Orchestration-functies proxy's genereren op basis van een interface. De `CreateEntityProxy` uitbreidings methode kan worden gebruikt voor dit doel einde:

```csharp
public interface IAsyncCounter
{
    Task AddAsync(int amount);
    Task ResetAsync();
    Task<int> GetAsync();
}

[FunctionName("CounterOrchestration")]
public static async Task Run(
    [OrchestrationTrigger] IDurableOrchestrationContext context)
{
    // ...
    IAsyncCounter proxy = context.CreateEntityProxy<IAsyncCounter>("MyCounter");
    await proxy.AddAsync(5);
    int newValue = await proxy.GetAsync();
    // ...
}
```

In het vorige voor beeld werd ervan uitgegaan dat er een counter-entiteit bestaat waarmee de `IAsyncCounter` interface wordt geïmplementeerd. De indeling kan de `IAsyncCounter` type definitie vervolgens gebruiken voor het genereren van een proxy type voor synchrone interactie met de entiteit.

### <a name="locking-entities-from-orchestrations"></a>Entiteiten vergren delen op basis van integratie

Orchestrations kunnen entiteiten vergren delen. Deze functie biedt een eenvoudige manier om ongewenste races te voor komen met behulp van *essentiële secties*.

Het context object biedt de volgende methoden:

* **LockAsync**: Hiermee worden de vergren delingen van een of meer entiteiten opgehaald.
* **IsLocked**: retourneert waar als deze zich momenteel in een kritieke sectie bewaart, anders false.

De sectie kritiek wordt beëindigd en alle vergren delingen worden vrijgegeven wanneer de indeling wordt beëindigd. In .net `LockAsync` retourneert een `IDisposable` die de sectie kritiek afbreekt wanneer deze is verwijderd, die kan worden gebruikt in combi natie `using` met een-component om een syntactische weer gave van de sectie kritiek te krijgen.

Denk bijvoorbeeld aan een indeling die nodig is om te testen of er twee spelers beschikbaar zijn en wijs deze beide toe aan een spel. Deze taak kan als volgt worden geïmplementeerd met behulp van een kritieke sectie:

```csharp
[FunctionName("Orchestrator")]
public static async Task RunOrchestrator(
    [OrchestrationTrigger] IDurableOrchestrationContext ctx)
{
    EntityId player1 = /* ... */;
    EntityId player2 = /* ... */;

    using (await ctx.LockAsync(player1, player2))
    {
        bool available1 = await ctx.CallEntityAsync<bool>(player1, "is-available");
        bool available2 = await ctx.CallEntityAsync<bool>(player2, "is-available");

        if (available1 && available2)
        {
            Guid gameId = ctx.NewGuid();

            await ctx.CallEntityAsync(player1, "assign-game", gameId);
            await ctx.CallEntityAsync(player2, "assign-game", gameId);
        }
    }
}
```

Binnen de sectie kritiek worden beide-entiteiten vergrendeld, wat betekent dat er geen andere bewerkingen worden uitgevoerd dan degene die in de sectie kritiek worden aangeroepen. Dit gedrag voor komt dat races met conflicterende bewerkingen, zoals spelers die aan een ander spel worden toegewezen, of zich afmelden.

We bieden verschillende beperkingen voor de manier waarop essentiële secties kunnen worden gebruikt. Deze beperkingen dienen om deadlocks en herbetreedbaarheid te voor komen.

* Kritieke secties kunnen niet worden genest.
* In essentiële secties kunnen geen subintegraties worden gemaakt.
* Kritieke secties kunnen alleen entiteiten aanroepen die ze hebben vergrendeld.
* Kritieke secties kunnen niet dezelfde entiteit aanroepen met meerdere parallelle aanroepen.
* Met kritieke secties kunnen alleen entiteiten worden gesignaleerd die niet zijn vergrendeld.

## <a name="alternate-storage-providers"></a>Alternatieve opslag providers

Het duurzame taak raamwerk ondersteunt nu meerdere opslag providers, waaronder [Azure Storage](https://github.com/Azure/durabletask/tree/master/src/DurableTask.AzureStorage), [Azure service bus](https://github.com/Azure/durabletask/tree/master/src/DurableTask.ServiceBus), een [in-Memory Emulator](https://github.com/Azure/durabletask/tree/master/src/DurableTask.Emulator)en een experimentele [redis](https://github.com/Azure/durabletask/tree/redis/src/DurableTask.Redis) -provider. Tot nu toe daarentegen is de duurzame taak extensie voor Azure Functions alleen de Azure Storage provider ondersteund. Vanaf Durable Functions 2,0 wordt de ondersteuning voor alternatieve opslag providers toegevoegd, te beginnen met de redis-provider.

> [!NOTE]
> Durable Functions 2,0 ondersteunt alleen .NET Standard 2,0-compatibele providers. Op het moment van schrijven ondersteunt de Azure Service Bus-provider geen ondersteuning voor .NET Standard 2,0 en is daarom niet beschikbaar als alternatieve opslag provider.

### <a name="emulator"></a>Emulator

De [DurableTask. emulator](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Emulator/) -provider is een lokaal geheugen, een niet-duurzame opslag provider, die geschikt is voor lokale test scenario's. Het kan worden geconfigureerd met behulp van het volgende minimale **host. json** -schema:

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "emulator": { }
      }
    }
  }
}
```

### <a name="redis-experimental"></a>Redis (experimenteel)

De provider [DurableTask. redis](https://www.nuget.org/packages/Microsoft.Azure.DurableTask.Redis/) houdt alle indelings status van een geconfigureerd redis-cluster.

```json
{
  "version": "2.0",
  "extensions": {
    "durableTask": {
      "hubName": <string>,
      "storageProvider": {
        "redis": {
          "connectionStringName": <string>,
        }
      }
    }
  }
}
```

De `connectionStringName` moet verwijzen naar de naam van een app-instelling of omgevings variabele. Deze app-instelling of omgevings variabele moet een redis connection string-waarde bevatten in de vorm *Server: poort*. Bijvoorbeeld `localhost:6379` om verbinding te maken met een lokale redis-cluster.

> [!NOTE]
> De redis-provider is momenteel experimentele en ondersteunt alleen functie-apps die worden uitgevoerd op één knoop punt. Het is niet gegarandeerd dat de redis-provider ooit algemeen beschikbaar wordt gemaakt en kan worden verwijderd in een toekomstige versie.
