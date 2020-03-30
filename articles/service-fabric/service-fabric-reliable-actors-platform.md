---
title: Betrouwbare acteurs op Service Fabric
description: Beschrijft hoe betrouwbare actoren zijn gelaagd op betrouwbare services en gebruik maken van de functies van de Service Fabric platform.
author: vturecek
ms.topic: conceptual
ms.date: 3/9/2018
ms.author: vturecek
ms.openlocfilehash: 92c717fa2c82dd147acd3c28333e37ccf8dd2e89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282300"
---
# <a name="how-reliable-actors-use-the-service-fabric-platform"></a>Hoe betrouwbare actoren het Service Fabric-platform gebruiken
In dit artikel wordt uitgelegd hoe betrouwbare actoren werken op het Azure Service Fabric-platform. Betrouwbare actoren draaien in een kader dat wordt gehost in een implementatie van een stateful betrouwbare dienst genaamd de *actor service*. De actorservice bevat alle onderdelen die nodig zijn om de levenscyclus en berichtverzending voor uw acteurs te beheren:

* De Actor Runtime beheert de levenscyclus, garbage collection en dwingt toegang tot single-threaded af.
* Een actorservice die listener remoting accepteert oproepen op afstand naar acteurs en stuurt deze naar een dispatcher om naar de juiste actor-instantie te leiden.
* De Actor State Provider verpakt staatproviders (zoals de provider van de status Betrouwbare verzamelingen) en biedt een adapter voor actorstatemanagement.

Deze componenten vormen samen het Reliable Actor framework.

## <a name="service-layering"></a>Servicelaaglaag
Omdat de actorservice zelf een betrouwbare service is, zijn alle [toepassingsmodellen,](service-fabric-application-model.md)levenscyclus, [verpakking,](service-fabric-package-apps.md) [implementatie,](service-fabric-deploy-remove-applications.md)upgrade en schaalconcepten van Betrouwbare Services op dezelfde manier van toepassing op actorservices.

![Inlagen van actorservice][1]

Het voorgaande diagram toont de relatie tussen de servicefabric-toepassingsframeworks en de gebruikerscode. Blauwe elementen vertegenwoordigen het toepassingskader voor Betrouwbare Services, oranje vertegenwoordigt het Reliable Actor-framework en groen staat voor gebruikerscode.

In Reliable Services erft `StatefulService` uw service de klasse. Deze klasse is `StatefulServiceBase` zelf `StatelessService` afgeleid van (of voor staatsloze diensten). In Reliable Actors maakt u gebruik van de acteursservice. De actor service is een `StatefulServiceBase` andere implementatie van de klasse die de acteur patroon waar uw acteurs lopen implementeert. Omdat de actor service zelf `StatefulServiceBase`is slechts een implementatie van , `ActorService` u uw eigen service die afkomstig `StatefulService`is van en implementeren service-level functies op dezelfde manier als je zou bij het erven, zoals:

* Service back-up en herstel.
* Gedeelde functionaliteit voor alle acteurs, bijvoorbeeld een stroomonderbreker.
* De verre procedure roept de acteurdienst zelf en op elke individuele acteur aan.

Zie [Functies op serviceniveau implementeren in uw actorservice](service-fabric-reliable-actors-using.md)voor meer informatie.

## <a name="application-model"></a>Toepassingsmodel
Actor-services zijn betrouwbare services, dus het toepassingsmodel is hetzelfde. De hulpprogramma's voor het bouwen van actor-framework's genereren echter een aantal van de toepassingsmodelbestanden voor u.

### <a name="service-manifest"></a>Servicemanifest
De hulpprogramma's voor het bouwen van actorframework's genereren automatisch de inhoud van het ServiceManifest.xml-bestand van uw actorservice. Dit bestand bevat:

* Actor servicetype. De typenaam wordt gegenereerd op basis van de projectnaam van uw actor. Op basis van het persistentieattribuut op uw actor, is de Vlag HasPersistedState ook dienovereenkomstig ingesteld.
* Codepakket.
* Config pakket.
* Resources en eindpunten.

### <a name="application-manifest"></a>Manifest van de toepassing
De hulpprogramma's voor het bouwen van actor-framework's maken automatisch een standaardservicedefinitie voor uw actorservice. De buildtools vullen de standaardservice-eigenschappen in:

* Het aantal replicasets wordt bepaald door het persistentiekenmerk op uw actor. Telkens wanneer het persistentiekenmerk op uw actor wordt gewijzigd, wordt het aantal replicasets in de standaardservicedefinitie dienovereenkomstig gereset.
* Partitieschema en bereik zijn ingesteld op Uniform Int64 met het volledige Int64-sleutelbereik.

## <a name="service-fabric-partition-concepts-for-actors"></a>Service Fabric partitieconcepten voor acteurs
Actor-services zijn verdeelde stateful services. Elke partitie van een actorservice bevat een reeks acteurs. Servicepartities worden automatisch verdeeld over meerdere knooppunten in Service Fabric. Actor-exemplaren worden hierdoor verdeeld.

![Verdeling en distributie van actor][5]

Betrouwbare services kunnen worden gemaakt met verschillende partitieschema's en partitiesleutelbereiken. De actorservice maakt gebruik van het Int64-partitieschema met het volledige Int64-sleutelbereik om actoren naar partities toe te zetten.

### <a name="actor-id"></a>Actor-id
Aan elke actor die in de service is gemaakt, is `ActorId` een unieke id gekoppeld, die wordt weergegeven door de klasse. `ActorId`is een ondoorzichtige ID-waarde die kan worden gebruikt voor een uniforme verdeling van actoren over de servicepartities door het genereren van willekeurige ID's:

```csharp
ActorProxy.Create<IMyActor>(ActorId.CreateRandom());
```
```Java
ActorProxyBase.create<MyActor>(MyActor.class, ActorId.newId());
```


Elke `ActorId` is hashed naar een Int64. Daarom moet de actor-service een Int64-partitioneringsschema gebruiken met het volledige Int64-sleutelbereik. Aangepaste ID-waarden kunnen echter `ActorID`worden gebruikt voor een , inclusief GUIDs/UUID's, tekenreeksen en Int64's.

```csharp
ActorProxy.Create<IMyActor>(new ActorId(Guid.NewGuid()));
ActorProxy.Create<IMyActor>(new ActorId("myActorId"));
ActorProxy.Create<IMyActor>(new ActorId(1234));
```
```Java
ActorProxyBase.create(MyActor.class, new ActorId(UUID.randomUUID()));
ActorProxyBase.create(MyActor.class, new ActorId("myActorId"));
ActorProxyBase.create(MyActor.class, new ActorId(1234));
```

Wanneer u GUID's/UUID's en tekenreeksen gebruikt, worden de waarden gehasht naar een Int64. Echter, wanneer u expliciet een Int64 `ActorId`aan een verstrekt, zal de Int64 rechtstreeks naar een partitie worden toegewezen zonder verder te hashing. U deze techniek gebruiken om te bepalen in welke partitie de actoren zijn geplaatst.


## <a name="next-steps"></a>Volgende stappen
* [Actor staatbeheer](service-fabric-reliable-actors-state-management.md)
* [Levenscyclus van actor's en garbage collection](service-fabric-reliable-actors-lifecycle.md)
* [Verwijzingen naar API-verwijzingen van actoren](https://docs.microsoft.com/dotnet/api/microsoft.servicefabric.actors?redirectedfrom=MSDN&view=azure-dotnet)
* [.NET-voorbeeldcode](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-voorbeeldcode](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/actor-service.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
[5]: ./media/service-fabric-reliable-actors-introduction/distribution.png
