---
title: Polymorfisme in het kader van betrouwbare actoren
description: Bouw hiërarchieën van .NET-interfaces en -typen in het framework Betrouwbare actoren om functionaliteit en API-definities te hergebruiken.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 4e485463f41cdfbadeb166ecbb3a86d4a32c1589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75348924"
---
# <a name="polymorphism-in-the-reliable-actors-framework"></a>Polymorfisme in het kader van betrouwbare actoren
Met het Framework Reliable Actors u acteurs bouwen met behulp van veel van dezelfde technieken die u zou gebruiken in objectgeoriënteerd ontwerp. Een van die technieken is polymorfisme, waardoor types en interfaces te erven van meer gegeneraliseerde ouders. Overerving in het framework Betrouwbare actoren volgt over het algemeen het .NET-model met een paar extra beperkingen. In het geval van Java/Linux volgt het het Java-model.

## <a name="interfaces"></a>Interfaces
Het framework Reliable Actors vereist dat u ten minste één interface definieert die door uw actortype moet worden geïmplementeerd. Deze interface wordt gebruikt om een proxyklasse te genereren die door clients kan worden gebruikt om met uw acteurs te communiceren. Interfaces kunnen erven van andere interfaces zolang elke interface die wordt geïmplementeerd door een actortype en al zijn ouders uiteindelijk afkomstig zijn van IActor(C#) of Actor(Java). IActor(C#) en Actor(Java) zijn de platformgedefinieerde basisinterfaces voor actoren in respectievelijk de frameworks .NET en Java. Zo kan het klassieke polymorfisme voorbeeld met behulp van vormen er ongeveer als volgt uitzien:

![Interfacehiërarchie voor vormactoren][shapes-interface-hierarchy]

## <a name="types"></a>Typen
U ook een hiërarchie van actortypen maken, die zijn afgeleid van de basisklasse Actor die door het platform wordt geleverd. In het geval van vormen hebt `Shape`u mogelijk `ShapeImpl`een basistype (C#) of (Java):

```csharp
public abstract class Shape : Actor, IShape
{
    public abstract Task<int> GetVerticeCount();

    public abstract Task<double> GetAreaAsync();
}
```
```Java
public abstract class ShapeImpl extends FabricActor implements Shape
{
    public abstract CompletableFuture<int> getVerticeCount();

    public abstract CompletableFuture<double> getAreaAsync();
}
```

Subtypen `Shape`(C#) `ShapeImpl`of (Java) kunnen methoden vanaf de basis overschrijven.

```csharp
[ActorService(Name = "Circle")]
[StatePersistence(StatePersistence.Persisted)]
public class Circle : Shape, ICircle
{
    public override Task<int> GetVerticeCount()
    {
        return Task.FromResult(0);
    }

    public override async Task<double> GetAreaAsync()
    {
        CircleState state = await this.StateManager.GetStateAsync<CircleState>("circle");

        return Math.PI *
            state.Radius *
            state.Radius;
    }
}
```
```Java
@ActorServiceAttribute(name = "Circle")
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class Circle extends ShapeImpl implements Circle
{
    @Override
    public CompletableFuture<Integer> getVerticeCount()
    {
        return CompletableFuture.completedFuture(0);
    }

    @Override
    public CompletableFuture<Double> getAreaAsync()
    {
        return (this.stateManager().getStateAsync<CircleState>("circle").thenApply(state->{
          return Math.PI * state.radius * state.radius;
        }));
    }
}
```

Let `ActorService` op het kenmerk op het actortype. Dit kenmerk vertelt het Framework Betrouwbare Actor dat het automatisch een service moet maken voor het hosten van actoren van dit type. In sommige gevallen u een basistype maken dat uitsluitend bedoeld is voor het delen van functionaliteit met subtypen en nooit zal worden gebruikt om concrete actoren te instantiëren. In die gevallen moet `abstract` u het trefwoord gebruiken om aan te geven dat u nooit een actor op basis van dat type zult maken.

## <a name="next-steps"></a>Volgende stappen
* Bekijk [hoe het Framework Reliable Actors gebruik maakt van het Service Fabric-platform](service-fabric-reliable-actors-platform.md) om betrouwbaarheid, schaalbaarheid en consistente status te bieden.
* Meer informatie over de [levenscyclus van actors](service-fabric-reliable-actors-lifecycle.md).

<!-- Image references -->

[shapes-interface-hierarchy]: ./media/service-fabric-reliable-actors-polymorphism/Shapes-Interface-Hierarchy.png
