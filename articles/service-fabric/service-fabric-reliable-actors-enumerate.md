---
title: Actors op Azure Service Fabric opsommen
description: Meer informatie over de inventarisatie van Reliable Actors en de meta gegevens in een Azure Service Fabric-toepassing met voor beelden.
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 1516c9005a7c4dd0adcb279e9954e5f882c575c1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75645596"
---
# <a name="enumerate-service-fabric-reliable-actors"></a>Service Fabric Reliable Actors opsommen
Met de Reliable Actors-service kan een client meta gegevens over de actors opsommen waarvoor de service wordt gehost. Omdat de actor-service een gepartitioneerde stateful service is, wordt er een inventarisatie uitgevoerd per partitie. Omdat elke partitie veel actors kan bevatten, wordt de inventarisatie geretourneerd als een set met pagina resultaten. De pagina's worden herhaald totdat alle pagina's zijn gelezen. In het volgende voor beeld ziet u hoe u een lijst met alle actieve actors maakt in één partitie van een actor service:

```csharp
IActorService actorServiceProxy = ActorServiceProxy.Create(
    new Uri("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new List<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = await actorServiceProxy.GetActorsAsync(continuationToken, cancellationToken);

    activeActors.AddRange(page.Items.Where(x => x.IsActive));

    continuationToken = page.ContinuationToken;
}
while (continuationToken != null);
```

```Java
ActorService actorServiceProxy = ActorServiceProxy.create(
    new URI("fabric:/MyApp/MyService"), partitionKey);

ContinuationToken continuationToken = null;
List<ActorInformation> activeActors = new ArrayList<ActorInformation>();

do
{
    PagedResult<ActorInformation> page = actorServiceProxy.getActorsAsync(continuationToken);

    while(ActorInformation x: page.getItems())
    {
         if(x.isActive()){
              activeActors.add(x);
         }
    }

    continuationToken = page.getContinuationToken();
}
while (continuationToken != null);
```



## <a name="next-steps"></a>Volgende stappen
* [Beheer van actor status](service-fabric-reliable-actors-state-management.md)
* [Actor-levens cyclus en garbagecollection](service-fabric-reliable-actors-lifecycle.md)
* [Naslag documentatie voor actors-API](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [.NET-voorbeeld code](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-voorbeeld code](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
