---
title: Acteurs opsommen op Azure Service Fabric
description: Meer informatie over het opsommen van betrouwbare actoren en hun metagegevens in een Azure Service Fabric-toepassing aan de hand van voorbeelden.
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 1516c9005a7c4dd0adcb279e9954e5f882c575c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645596"
---
# <a name="enumerate-service-fabric-reliable-actors"></a>Opsommen Service Fabric Betrouwbare Actoren
Met de Service Betrouwbare Actoren kan een klant metadata opsommen over de actoren die de service host. Omdat de actorservice een partitievolle service is, wordt de opsomming per partitie uitgevoerd. Omdat elke partitie veel actoren kan bevatten, wordt de opsomming geretourneerd als een reeks oppiepende resultaten. De pagina's worden overgelooped totdat alle pagina's worden gelezen. In het volgende voorbeeld ziet u hoe u een lijst maakt met alle actieve actoren in één partitie van een actorservice:

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
* [Actor staatbeheer](service-fabric-reliable-actors-state-management.md)
* [Levenscyclus van actor's en garbage collection](service-fabric-reliable-actors-lifecycle.md)
* [Verwijzingen naar API-verwijzingen van actoren](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [.NET-voorbeeldcode](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-voorbeeldcode](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
