---
title: Azure-servicefabric-actoren verwijderen
description: Meer informatie over het handmatig en volledig verwijderen van betrouwbare actoren en hun status in een Azure Service Fabric-toepassing.
author: amanbha
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: amanbha
ms.openlocfilehash: b90c5a10c64e273f1c8f48c7bf5713859796db65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645613"
---
# <a name="delete-reliable-actors-and-their-state"></a>Reliable Actors en hun status verwijderen
Garbage collection van gedeactiveerde acteurs ruimt alleen het actorobject op, maar verwijdert geen gegevens die zijn opgeslagen in de State Manager van een actor. Wanneer een actor opnieuw wordt geactiveerd, worden de gegevens opnieuw beschikbaar gesteld via de State Manager. In gevallen waarin acteurs gegevens opslaan in State Manager en worden gedeactiveerd, maar nooit opnieuw worden geactiveerd, kan het nodig zijn om hun gegevens op te schonen.

De [Actor-service](service-fabric-reliable-actors-platform.md) biedt een functie voor het verwijderen van acteurs van een externe beller:

```csharp
ActorId actorToDelete = new ActorId(id);

IActorService myActorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

await myActorServiceProxy.DeleteActorAsync(actorToDelete, cancellationToken)
```
```Java
ActorId actorToDelete = new ActorId(id);

ActorService myActorServiceProxy = ActorServiceProxy.create(
    new Uri("fabric:/MyApp/MyService"), actorToDelete);

myActorServiceProxy.deleteActorAsync(actorToDelete);
```

Het verwijderen van een actor heeft de volgende effecten, afhankelijk van het feit of de actor momenteel actief is:

* **Actieve actor**
  * Actor wordt verwijderd uit de lijst met actieve acteurs en wordt gedeactiveerd.
  * De status wordt permanent verwijderd.
* **Inactieve actor**
  * De status wordt permanent verwijderd.

Een actor kan zichzelf niet verwijderen uit een van zijn actormethoden omdat de actor niet kan worden verwijderd tijdens het uitvoeren binnen een actorcallcontext, waarbij de runtime een slot rond de actor-aanroep heeft verkregen om toegang met één thread af te dwingen.

Voor meer informatie over betrouwbare actoren, lees het volgende:
* [Actortimers en herinneringen](service-fabric-reliable-actors-timers-reminders.md)
* [Actorgebeurtenissen](service-fabric-reliable-actors-events.md)
* [Acteur reentrancy](service-fabric-reliable-actors-reentrancy.md)
* [Actor diagnostiek en prestatiebewaking](service-fabric-reliable-actors-diagnostics.md)
* [Actor API-referentiedocumentatie](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# Voorbeeldcode](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-voorbeeldcode](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
