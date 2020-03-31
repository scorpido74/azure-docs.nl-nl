---
title: Status Azure Service Fabric beheren
description: Meer informatie over het openen, opslaan en verwijderen van status voor een Azure Service Fabric Reliable Actor en overwegingen bij het ontwerpen van een toepassing.
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 788c337a37ec66c5aa1521c5cd9f2816ed7a8bf9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645630"
---
# <a name="access-save-and-remove-reliable-actors-state"></a>Toegang tot, opslaan en verwijderen van de staat Betrouwbare actoren
[Betrouwbare actoren](service-fabric-reliable-actors-introduction.md) zijn objecten met één thread die zowel logica als status kunnen inkapselen en de status betrouwbaar kunnen behouden. Elke actorinstantie heeft zijn eigen [statusmanager:](service-fabric-reliable-actors-state-management.md)een woordenboekachtige gegevensstructuur die op betrouwbare wijze sleutel-/waardeparen opslaat. De state manager is een wrapper rond een state provider. U deze gebruiken om gegevens op te slaan, ongeacht welke [persistentieinstelling](service-fabric-reliable-actors-state-management.md#state-persistence-and-replication) wordt gebruikt.

De sleutels van de staatsmanager moeten tekenreeksen zijn. Waarden zijn generiek en kunnen elk type zijn, inclusief aangepaste typen. Waarden die zijn opgeslagen in de statusbeheerder, moeten serializable gegevenscontract zijn omdat ze tijdens de replicatie via het netwerk naar andere knooppunten kunnen worden verzonden en naar de schijf kunnen worden geschreven, afhankelijk van de instelling voor statuspersistentie van een actor.

De statusbeheerder legt algemene woordenboekmethoden bloot voor het beheren van de status, vergelijkbaar met die in Reliable Dictionary.

Zie aanbevolen [procedures voor het beheren van de actorstatus](service-fabric-reliable-actors-state-management.md#best-practices)voor meer informatie.

## <a name="access-state"></a>Toegangsstatus
Staat is toegankelijk via de staatmanager per sleutel. Methoden voor statusbeheer zijn allemaal asynchroon omdat ze mogelijk schijf-I/O vereisen wanneer de status van actoren is blijven bestaan. Bij de eerste toegang worden statusobjecten in het geheugen opgeslagen. Herhaal toegangsbewerkingen openen objecten rechtstreeks vanuit het geheugen en keren synchroon terug zonder dat schijf I/O of asynchrone context-switching overhead zijn. In de volgende gevallen wordt een statusobject uit de cache verwijderd:

* Een actormethode gooit een niet-afgehandelde uitzondering nadat een object van de statusbeheerder is opgehaald.
* Een actor wordt opnieuw geactiveerd, hetzij nadat deze is gedeactiveerd of na een storing.
* De pagina's van de status van de status van de status van de status van de status van de status op schijf. Dit gedrag is afhankelijk van de implementatie van de statusprovider. De standaardstatusprovider `Persisted` voor de instelling heeft dit gedrag.

U de status ophalen met `KeyNotFoundException`behulp van een `NoSuchElementException`standaard *bewerking Ophalen* die (C#) of (Java) gooit als een item niet bestaat voor de sleutel:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<int> GetCountAsync()
    {
        return this.StateManager.GetStateAsync<int>("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().getStateAsync("MyState");
    }
}
```

U de status ook ophalen met behulp van een *TryGet-methode* die niet wordt gegooid als een item niet bestaat voor een sleutel:

```csharp
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task<int> GetCountAsync()
    {
        ConditionalValue<int> result = await this.StateManager.TryGetStateAsync<int>("MyState");
        if (result.HasValue)
        {
            return result.Value;
        }

        return 0;
    }
}
```
```Java
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture<Integer> getCountAsync()
    {
        return this.stateManager().<Integer>tryGetStateAsync("MyState").thenApply(result -> {
            if (result.hasValue()) {
                return result.getValue();
            } else {
                return 0;
            });
    }
}
```

## <a name="save-state"></a>Status opslaan
De methoden voor het ophalen van de statusbeheerder retourneren een verwijzing naar een object in het lokale geheugen. Als u dit object alleen in het lokale geheugen wijzigt, wordt het niet blijvend opgeslagen. Wanneer een object wordt opgehaald bij de statusbeheerder en wordt gewijzigd, moet het opnieuw worden ingevoegd in de statusbeheerder om blijvend te worden opgeslagen.

U de status *Set*invoegen met behulp van `dictionary["key"] = value` een onvoorwaardelijke set, wat het equivalent is van de syntaxis:

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task SetCountAsync(int value)
    {
        return this.StateManager.SetStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture setCountAsync(int value)
    {
        return this.stateManager().setStateAsync("MyState", value);
    }
}
```

U status toevoegen met behulp van een *methode Toevoegen.* Deze methode `InvalidOperationException`gooit (C#) of `IllegalStateException`(Java) wanneer het probeert om een sleutel toe te voegen die al bestaat.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task AddCountAsync(int value)
    {
        return this.StateManager.AddStateAsync<int>("MyState", value);
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().addOrUpdateStateAsync("MyState", value, (key, old_value) -> old_value + value);
    }
}
```

U ook status toevoegen met behulp van een *tryadd-methode.* Deze methode gooit niet wanneer het probeert om een sleutel toe te voegen die al bestaat.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task AddCountAsync(int value)
    {
        bool result = await this.StateManager.TryAddStateAsync<int>("MyState", value);

        if (result)
        {
            // Added successfully!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture addCountAsync(int value)
    {
        return this.stateManager().tryAddStateAsync("MyState", value).thenApply((result)->{
            if(result)
            {
                // Added successfully!
            }
        });
    }
}
```

Aan het einde van een actormethode slaat de statusbeheerder automatisch waarden op die zijn toegevoegd of gewijzigd door een invoeg- of updatebewerking. Een "opslaan" kan bestaan uit de schijf en replicatie, afhankelijk van de gebruikte instellingen. Waarden die niet zijn gewijzigd, blijven niet bestaan of gerepliceerd. Als er geen waarden zijn gewijzigd, doet de opslagbewerking niets. Als het opslaan mislukt, wordt de gewijzigde status verwijderd en wordt de oorspronkelijke status opnieuw geladen.

U de status ook `SaveStateAsync` handmatig opslaan door de methode op de actorbasis aan te roepen:

```csharp
async Task IMyActor.SetCountAsync(int count)
{
    await this.StateManager.AddOrUpdateStateAsync("count", count, (key, value) => count > value ? count : value);

    await this.SaveStateAsync();
}
```
```Java
interface MyActor {
    CompletableFuture setCountAsync(int count)
    {
        this.stateManager().addOrUpdateStateAsync("count", count, (key, value) -> count > value ? count : value).thenApply();

        this.stateManager().saveStateAsync().thenApply();
    }
}
```

## <a name="remove-state"></a>Status verwijderen
U de status permanent verwijderen uit de statusbeheerder van een actor door de methode Verwijderen aan *te* roepen. Deze methode `KeyNotFoundException`gooit (C#) of `NoSuchElementException`(Java) wanneer het probeert om een sleutel die niet bestaat te verwijderen.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task RemoveCountAsync()
    {
        return this.StateManager.RemoveStateAsync("MyState");
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().removeStateAsync("MyState");
    }
}
```

U de status ook permanent verwijderen met de methode *TryRemove.* Deze methode gooit niet wanneer het probeert om een sleutel die niet bestaat te verwijderen.

```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
    public MyActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public async Task RemoveCountAsync()
    {
        bool result = await this.StateManager.TryRemoveStateAsync("MyState");

        if (result)
        {
            // State removed!
        }
    }
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl extends FabricActor implements  MyActor
{
    public MyActorImpl(ActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture removeCountAsync()
    {
        return this.stateManager().tryRemoveStateAsync("MyState").thenApply((result)->{
            if(result)
            {
                // State removed!
            }
        });
    }
}
```

## <a name="next-steps"></a>Volgende stappen

Staat die is opgeslagen in betrouwbare actoren moet worden geserialiseerd voordat de naar schijf worden geschreven en gerepliceerd voor hoge beschikbaarheid. Meer informatie over [serie-serie actortype](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Lees vervolgens meer over [actordiagnostiek en prestatiebewaking.](service-fabric-reliable-actors-diagnostics.md)
