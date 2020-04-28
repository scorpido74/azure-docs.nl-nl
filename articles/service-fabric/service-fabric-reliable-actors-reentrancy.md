---
title: Herbetreedbaarheid in azure Service Fabric Actors
description: Inleiding tot herbetreedbaarheid voor Service Fabric Reliable Actors, een manier om blok kering op basis van de aanroep context te voor komen.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 46ce91e607341e2fbdc0b6a3018e74cb24e76839
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75645528"
---
# <a name="reliable-actors-reentrancy"></a>Reliable Actors herbetreedbaarheid
Met de Reliable Actors runtime kunt u standaard herbetreedbaarheid op basis van logische oproepen. Hiermee kunnen actors worden ingetrokken als ze zich in dezelfde aanroep context keten bevinden. Actor A verzendt bijvoorbeeld een bericht naar actor B, die een bericht naar actor C verzendt. Als onderdeel van de bericht verwerking, als actor C actor A aanroept, wordt het bericht weer gegeven, zodat het is toegestaan. Andere berichten die deel uitmaken van een andere aanroep context, worden geblokkeerd op actor A tot de verwerking is voltooid.

Er zijn twee opties beschikbaar voor actor herbetreedbaarheid gedefinieerd in de `ActorReentrancyMode` Enum:

* `LogicalCallContext`(standaard gedrag)
* `Disallowed`-schakelt herbetreedbaarheid uit

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
Herbetreedbaarheid kunnen tijdens de registratie worden `ActorService`geconfigureerd in de instellingen van een. De instelling is van toepassing op alle actor-exemplaren die zijn gemaakt in de actor-service.

In het volgende voor beeld ziet u een actor service waarmee de herbetreedbaarheid `ActorReentrancyMode.Disallowed`-modus wordt ingesteld op. Als een actor een bericht naar een andere actor verzendt, wordt er in dit geval een uitzonde ring `FabricException` van het type gegenereerd.

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
* Meer informatie over herbetreedbaarheid in de [ACTOR API-referentie documentatie](https://msdn.microsoft.com/library/azure/dn971626.aspx)
