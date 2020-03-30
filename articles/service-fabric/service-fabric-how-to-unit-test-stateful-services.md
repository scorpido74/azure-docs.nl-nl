---
title: Unittests ontwikkelen voor statige diensten
description: Meer informatie over het testen van eenheden in Azure Service Fabric voor stateful services en speciale overwegingen om in gedachten te houden tijdens de ontwikkeling.
ms.topic: conceptual
ms.date: 09/04/2018
ms.openlocfilehash: 9c657bd8295d01a4e0fa4e44e969b33946684bfa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639833"
---
# <a name="create-unit-tests-for-stateful-services"></a>Eenheidstests maken voor Stateful Services
Unit testing Service Fabric stateful services onthult veelvoorkomende fouten die niet noodzakelijkerwijs zouden worden gevangen door conventionele toepassing of domein-specifieke eenheid testen. Tijdens het ontwikkelen van unit tests voor stateful diensten, zijn er een aantal speciale overwegingen die in gedachten moeten worden gehouden.

1. Elke replica voert toepassingscode uit, maar onder verschillende context. Als de service drie replica's gebruikt, wordt de servicecode uitgevoerd op drie knooppunten parallel onder verschillende context/rol.
2. Status die is opgeslagen binnen de stateful service moet consistent zijn tussen alle replica's. De staatsmanager en betrouwbare collecties zorgen voor deze out-of-the-box consistentie. De status in het geheugen moet echter worden beheerd door de toepassingscode.
3. Elke replica verandert op een bepaald moment van rol tijdens het uitvoeren op het cluster. Een secundaire replica wordt een primaire, in het geval dat het knooppunt dat de primaire host niet beschikbaar of overbelast wordt. Dit is natuurlijk gedrag voor Service Fabric daarom diensten moeten plannen voor uiteindelijk uitvoeren onder een andere rol.

In dit artikel wordt ervan uitgegaan dat [servicevolle services in Service Fabric](service-fabric-concepts-unit-testing.md) van de eenheid zijn gelezen.

## <a name="the-servicefabricmocks-library"></a>De ServiceFabric.Mocks-bibliotheek
Vanaf versie 3.3.0 biedt [ServiceFabric.Mocks](https://www.nuget.org/packages/ServiceFabric.Mocks/) een API voor het bespotten van zowel de orkestratie van de replica's als het staatsbeheer. Dit zal worden gebruikt in de voorbeelden.

[Nuget](https://www.nuget.org/packages/ServiceFabric.Mocks/)
GitHub (Nuget[GitHub)](https://github.com/loekd/ServiceFabric.Mocks)

*ServiceFabric.Mocks is niet eigendom van of wordt onderhouden door Microsoft. Dit is momenteel echter de door Microsoft aanbevolen bibliotheek voor stateful services voor het testen van eenheden.*

## <a name="set-up-the-mock-orchestration-and-state"></a>De mock orchestration en status instellen
Als onderdeel van het regelgedeelte van een test worden een mock replica set en state manager gemaakt. De replicaset is dan eigenaar van het maken van een exemplaar van de geteste service voor elke replica. Het zal ook eigenaar zijn `OnChangeRole` van `RunAsync`het uitvoeren van levenscyclusgebeurtenissen zoals en . De mock state manager zorgt ervoor dat alle bewerkingen uitgevoerd tegen de statusmanager worden uitgevoerd en bewaard zoals de werkelijke statusmanager zou doen.

1. Maak een servicefabriekgemachtigde die de geteste service onmiddellijkzal maken. Dit moet vergelijkbaar of hetzelfde zijn als de `Program.cs` callback van de servicefabriek die doorgaans wordt gevonden voor een Service Fabric-service of -actor. Dit moet de volgende handtekening volgen:
   ```csharp
   MyStatefulService CreateMyStatefulService(StatefulServiceContext context, IReliableStateManagerReplica2 stateManager)
   ```
2. Een instantie `MockReliableStateManager` van klasse maken. Dit zal alle interacties met de statusmanager bespotten.
3. Maak een `MockStatefulServiceReplicaSet<TStatefulService>` instantie `TStatefulService` van waar is het type van de service wordt getest. Hiervoor moet de gemachtigde die in stap #1 is gemaakt en de statusbeheerder die in #2
4. Replica's toevoegen aan de replicaset. De rol opgeven (zoals Primair, ActiveSecondary, IdleSecondary) en de id van de replica
   > Houd vast aan de replica-iDs! Deze zullen waarschijnlijk worden gebruikt tijdens de handeling en beweren delen van een eenheid test.

```csharp
//service factory to instruct how to create the service instance
var serviceFactory = (StatefulServiceContext context, IReliableStateManagerReplica2 stateManager) => new MyStatefulService(context, stateManager);
//instantiate a new mock state manager
var stateManager = new MockReliableStateManager();
//instantiate a new replica set with the service factory and state manager
var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
//add a new Primary replica with id 1
await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
//add a new ActiveSecondary replica with id 2
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
//add a second ActiveSecondary replica with id 3
await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);
```

## <a name="execute-service-requests"></a>Serviceaanvragen uitvoeren
Serviceaanvragen kunnen worden uitgevoerd op een specifieke replica met behulp van de gemakseigenschappen en opzoekingen.
```csharp
const string stateName = "test";
var payload = new Payload(StatePayload);

//execute a request on the primary replica using
await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);

//execute a request against replica with id 2
await replicaSet[2].ServiceInstance.InsertAsync(stateName, payload);

//execute a request against one of the active secondary replicas
await replicaSet.FirstActiveSecondary.InsertAsync(stateName, payload);
```

## <a name="execute-a-service-move"></a>Een serviceverplaatsing uitvoeren
De mock replica set onthult verschillende gemaksmethoden om verschillende soorten service bewegingen te activeren.
```csharp
//promote the first active secondary to primary
replicaSet.PromoteNewReplicaToPrimaryAsync();
//promote the secondary with replica id 4 to primary
replicaSet.PromoteNewReplicaToPrimaryAsync(4);

//promote the first idle secondary to an active secondary
PromoteIdleSecondaryToActiveSecondaryAsync();
//promote idle secondary with replica id 4 to active secondary
PromoteIdleSecondaryToActiveSecondaryAsync(4);

//add a new replica with randomly assigned replica id and promote it to primary
PromoteNewReplicaToPrimaryAsync()
//add a new replica with replica id 4 and promote it to primary
PromoteNewReplicaToPrimaryAsync(4)
```

## <a name="putting-it-all-together"></a>Alles samenvoegen
De volgende test toont het instellen van een drie knooppunt replica set en controleren of de gegevens beschikbaar zijn vanaf een secundaire na een rol te veranderen. Een typisch probleem dat dit kan `InsertAsync` vangen is als de gegevens die tijdens zijn `CommitAsync`opgeslagen, hetzij op iets in het geheugen of aan een betrouwbare verzameling zonder te worden uitgevoerd . In beide gevallen zou de secundaire niet synchroon met de primaire. Dit zou leiden tot inconsistente reacties na serviceverplaatsingen.

```csharp
[TestMethod]
public async Task TestServiceState_InMemoryState_PromoteActiveSecondary()
{
    var stateManager = new MockReliableStateManager();
    var replicaSet = new MockStatefulServiceReplicaSet<MyStatefulService>(CreateStatefulService, stateManager);
    await replicaSet.AddReplicaAsync(ReplicaRole.Primary, 1);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 2);
    await replicaSet.AddReplicaAsync(ReplicaRole.ActiveSecondary, 3);

    const string stateName = "test";
    var payload = new Payload(StatePayload);

    //insert data
    await replicaSet.Primary.ServiceInstance.InsertAsync(stateName, payload);
    //promote one of the secondaries to primary
    await replicaSet.PromoteActiveSecondaryToPrimaryAsync(2);
    //get data
    var payloads = (await replicaSet.Primary.ServiceInstance.GetPayloadsAsync()).ToList();

    //data should match what was inserted against the primary
    Assert.IsTrue(payloads.Count == 1);
    Assert.IsTrue(payloads[0].Content == payload.Content);

    //verify the data was saved against the reliable dictionary
    var dictionary = await StateManager.GetOrAddAsync<IReliableDictionary<string, Payload>>(MyStatefulService.StateManagerDictionaryKey);
    using(var tx = StateManager.CreateTransaction())
    {
        var payload = await dictionary.TryGetValue(stateName);
        Assert.IsTrue(payload.HasValue);
        Assert.IsTrue(payload.Value.Content == payload.Content);
    }
}
```

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het testen [van service-to-service communicatie](service-fabric-testability-scenarios-service-communication.md) en [het simuleren van storingen met behulp van gecontroleerde chaos.](service-fabric-controlled-chaos.md)
