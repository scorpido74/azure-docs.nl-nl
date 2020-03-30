---
title: Reentrancy in Azure Service Fabric-actoren
description: Inleiding tot reentrancy voor Service Fabric Reliable Actors, een manier om logisch te voorkomen dat blokkeren op basis van oproepcontext.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 46ce91e607341e2fbdc0b6a3018e74cb24e76839
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645528"
---
# <a name="reliable-actors-reentrancy"></a>Betrouwbare acteurs re-entrancy
De runtime van Betrouwbare actoren maakt standaard logische re-entrancy op basis van gesprekscontext mogelijk. Dit maakt het mogelijk voor acteurs om re-entrant als ze in dezelfde call context keten. Actor A stuurt bijvoorbeeld een bericht naar Actor B, die een bericht stuurt naar Actor C. Als onderdeel van de berichtverwerking, als Actor C Actor A aanroept, is het bericht opnieuw toetreder, dus het is toegestaan. Alle andere berichten die deel uitmaken van een andere gesprekscontext, worden geblokkeerd op Actor A totdat de verwerking is voltooid.

Er zijn twee opties beschikbaar voor actor reentrancy gedefinieerd in het `ActorReentrancyMode` enum:

* `LogicalCallContext`(standaardgedrag)
* `Disallowed`- re-entrancy uitschakelt

```csharp
public enum ActorReentrancyMode
{
    LogicalCallContext = 1,
    Disallowed = 2
}
```
```Java
public enum ActorReentrancyMode
{
    LogicalCallContext(1),
    Disallowed(2)
}
```
Reentrancy kan worden geconfigureerd `ActorService`in een 's instellingen tijdens de registratie. De instelling is van toepassing op alle actor-exemplaren die in de actorservice zijn gemaakt.

In het volgende voorbeeld wordt een actorservice weergegeven `ActorReentrancyMode.Disallowed`die de reentrantiemodus instelt op . In dit geval wordt een uitzondering op het type `FabricException` gegooid als een actor een nieuw-deelnemend bericht naar een andere actor stuurt.

```csharp
static class Program
{
    static void Main()
    {
        try
        {
            ActorRuntime.RegisterActorAsync<Actor1>(
                (context, actorType) => new ActorService(
                    context,
                    actorType, () => new Actor1(),
                    settings: new ActorServiceSettings()
                    {
                        ActorConcurrencySettings = new ActorConcurrencySettings()
                        {
                            ReentrancyMode = ActorReentrancyMode.Disallowed
                        }
                    }))
                .GetAwaiter().GetResult();

            Thread.Sleep(Timeout.Infinite);
        }
        catch (Exception e)
        {
            ActorEventSource.Current.ActorHostInitializationFailed(e.ToString());
            throw;
        }
    }
}
```
```Java
static class Program
{
    static void Main()
    {
        try
        {
            ActorConcurrencySettings actorConcurrencySettings = new ActorConcurrencySettings();
            actorConcurrencySettings.setReentrancyMode(ActorReentrancyMode.Disallowed);

            ActorServiceSettings actorServiceSettings = new ActorServiceSettings();
            actorServiceSettings.setActorConcurrencySettings(actorConcurrencySettings);

            ActorRuntime.registerActorAsync(
                Actor1.getClass(),
                (context, actorType) -> new FabricActorService(
                    context,
                    actorType, () -> new Actor1(),
                    null,
                    stateProvider,
                    actorServiceSettings, timeout);

            Thread.sleep(Long.MAX_VALUE);
        }
        catch (Exception e)
        {
            throw e;
        }
    }
}
```


## <a name="next-steps"></a>Volgende stappen
* Meer informatie over re-entrantie in de [referentiedocumentatie voor actor-API's](https://msdn.microsoft.com/library/azure/dn971626.aspx)
