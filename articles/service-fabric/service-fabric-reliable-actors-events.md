---
title: Gebeurtenissen in op actor's gebaseerde Azure Service Fabric-actoren
description: Meer informatie over gebeurtenissen voor Service Fabric Reliable Actors, een effectieve manier om te communiceren tussen acteur en klant.
author: vturecek
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: 73c149a0d0992fecd1acf633891057570285df64
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639663"
---
# <a name="actor-events"></a>Actorgebeurtenissen
Actor-gebeurtenissen bieden een manier om meldingen van de acteur met de beste inspanning naar de clients te sturen. Actor-gebeurtenissen zijn ontworpen voor communicatie tussen actor en client en mogen niet worden gebruikt voor communicatie tussen actor en acteur.

In de volgende codefragmenten ziet u hoe u actorgebeurtenissen in uw toepassing gebruiken.

Definieer een interface die de gebeurtenissen beschrijft die door de actor zijn gepubliceerd. Deze interface moet worden `IActorEvents` afgeleid uit de interface. De argumenten van de methoden moeten [gegevens contract serializable](service-fabric-reliable-actors-notes-on-actor-type-serialization.md). De methoden moeten nietig worden, omdat gebeurtenismeldingen één manier en beste inspanning zijn.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```
```Java
public interface GameEvents implements ActorEvents
{
    void gameScoreUpdated(UUID gameId, String currentScore);
}
```
Verklaar de gebeurtenissen gepubliceerd door de acteur in de actor-interface.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    Task<string> GetGameScore();
}
```
```Java
public interface GameActor extends Actor, ActorEventPublisherE<GameEvents>
{
    CompletableFuture<?> updateGameStatus(GameStatus status);

    CompletableFuture<String> getGameScore();
}
```
Implementeer aan de clientzijde de gebeurtenishandler.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

```Java
class GameEventsHandler implements GameEvents {
    public void gameScoreUpdated(UUID gameId, String currentScore)
    {
        System.out.println("Updates: Game: "+gameId+" ,Score: "+currentScore);
    }
}
```

Maak op de client een proxy voor de actor die de gebeurtenis publiceert en abonneer je op de gebeurtenissen.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);

await proxy.SubscribeAsync<IGameEvents>(new GameEventsHandler());
```

```Java
GameActor actorProxy = ActorProxyBase.create<GameActor>(GameActor.class, new ActorId(UUID.fromString(args)));

return ActorProxyEventUtility.subscribeAsync(actorProxy, new GameEventsHandler());
```

In het geval van failovers, kan de actor niet over naar een ander proces of knooppunt. De actorproxy beheert de actieve abonnementen en neemt deze automatisch opnieuw in. U het interval voor `ActorProxyEventExtensions.SubscribeAsync<TEvent>` opnieuw abonnementen beheren via de API. Als u zich `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>` wilt afmelden, gebruikt u de API.

Op de acteur, publiceren van de gebeurtenissen als ze gebeuren. Als er abonnees zijn van het evenement, stuurt de runtime acteurs hen de melding.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), score);
```
```Java
GameEvents event = getEvent<GameEvents>(GameEvents.class);
event.gameScoreUpdated(Id.getUUIDId(), score);
```


## <a name="next-steps"></a>Volgende stappen
* [Acteur reentrancy](service-fabric-reliable-actors-reentrancy.md)
* [Actor diagnostiek en prestatiebewaking](service-fabric-reliable-actors-diagnostics.md)
* [Actor API-referentiedocumentatie](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# Voorbeeldcode](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [C# .NET Core-voorbeeldcode](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started)
* [Java-voorbeeldcode](https://github.com/Azure-Samples/service-fabric-java-getting-started)
