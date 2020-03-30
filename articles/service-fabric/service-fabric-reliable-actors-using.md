---
title: Functies implementeren in Azure Service Fabric-actoren
description: Beschrijft hoe u uw eigen actorservice schrijft die functies op serviceniveau implementeert op dezelfde manier als wanneer u StatefulService overneemt.
author: vturecek
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: vturecek
ms.openlocfilehash: 55ee4c7498dcda3060d4e4221711793b80132bdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502285"
---
# <a name="implement-service-level-features-in-your-actor-service"></a>Functies op serviceniveau implementeren in uw actorservice

Zoals beschreven in [service gelaagdheid,](service-fabric-reliable-actors-platform.md#service-layering)de actor service zelf is een betrouwbare service. U uw eigen dienst `ActorService`schrijven die voortkomt uit. U functies op serviceniveau ook op dezelfde manier implementeren als wanneer u een stateful service overneemt, zoals:

- Service back-up en herstel.
- Gedeelde functionaliteit voor alle acteurs, bijvoorbeeld een stroomonderbreker.
- De verre procedure roept de acteurdienst zelf en op elke individuele acteur aan.

## <a name="use-the-actor-service"></a>De actorservice gebruiken

Actor-exemplaren hebben toegang tot de actorservice waarin ze worden uitgevoerd. Via de actorservice kunnen actor-instanties programmatisch de servicecontext verkrijgen. De servicecontext heeft de partitie-id, servicenaam, toepassingsnaam en andere Azure Service Fabric-platformspecifieke informatie.

```csharp
Task MyActorMethod()
{
    Guid partitionId = this.ActorService.Context.PartitionId;
    string serviceTypeName = this.ActorService.Context.ServiceTypeName;
    Uri serviceInstanceName = this.ActorService.Context.ServiceName;
    string applicationInstanceName = this.ActorService.Context.CodePackageActivationContext.ApplicationName;
}
```
```Java
CompletableFuture<?> MyActorMethod()
{
    UUID partitionId = this.getActorService().getServiceContext().getPartitionId();
    String serviceTypeName = this.getActorService().getServiceContext().getServiceTypeName();
    URI serviceInstanceName = this.getActorService().getServiceContext().getServiceName();
    String applicationInstanceName = this.getActorService().getServiceContext().getCodePackageActivationContext().getApplicationName();
}
```

Net als alle betrouwbare services moet de actorservice zijn geregistreerd met een servicetype in de runtime servicefabric. Als u de actorservice wilt uitvoeren, moet uw actortype ook zijn geregistreerd bij de actorservice. De `ActorRuntime`-registratiemethode doet dit voor actors. In het eenvoudigste geval u het actortype registreren en de actorservice gebruikt vervolgens de standaardinstellingen.

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>().GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```

U ook gebruik maken van een lambda die door de registratiemethode om de acteur dienst zelf te construeren. Vervolgens u de actorservice configureren en uw actor-instanties expliciet samenstellen. U afhankelijkheden injecteren om uw acteur via de constructor.

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new ActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
static class Program
{
    private static void Main()
    {
      ActorRuntime.registerActorAsync(
              MyActor.class,
              (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
              timeout);

        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="actor-service-methods"></a>Actor-servicemethoden

De actorservice `IActorService` implementeert (C#) of `ActorService` (Java), `IService` die op `Service` zijn beurt implementeert (C#) of (Java). Deze interface wordt gebruikt door Reliable Services remoting, waarmee externe procedure oproepen op servicemethoden. Het bevat service-level methoden die op afstand kunnen worden opgeroepen via service remoting. U het gebruiken om acteurs op te [sommen](service-fabric-reliable-actors-enumerate.md) en [te verwijderen.](service-fabric-reliable-actors-delete-actors.md)


## <a name="custom-actor-service"></a>Aangepaste actorservice

Door gebruik te maken van de actor registratie lambda, `ActorService` kunt u uw `FabricActorService` eigen aangepaste actor service die afkomstig is van (C #) en (Java) registreren. U vervolgens uw eigen functionaliteit op serviceniveau implementeren `ActorService` door een serviceklasse te schrijven die erft (C#) of `FabricActorService` (Java). Een aangepaste actorservice neemt alle runtime-functionaliteit van actor over (C#) `ActorService` of `FabricActorService` (Java). Het kan worden gebruikt om uw eigen servicemethoden te implementeren.

```csharp
class MyActorService : ActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }
}
```
```Java
class MyActorService extends FabricActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, BiFunction<FabricActorService, ActorId, ActorBase> newActor)
    {
         super(context, typeInfo, newActor);
    }
}
```

```csharp
static class Program
{
    private static void Main()
    {
        ActorRuntime.RegisterActorAsync<MyActor>(
            (context, actorType) => new MyActorService(context, actorType, () => new MyActor()))
            .GetAwaiter().GetResult();

        Thread.Sleep(Timeout.Infinite);
    }
}
```
```Java
public class Program
{
    public static void main(String[] args)
    {
        ActorRuntime.registerActorAsync(
                MyActor.class,
                (context, actorTypeInfo) -> new FabricActorService(context, actorTypeInfo),
                timeout);
        Thread.sleep(Long.MAX_VALUE);
    }
}
```

## <a name="implement-actor-backup-and-restore"></a>Actor back-up en herstel implementeren

Een aangepaste actorservice kan een methode blootleggen om een back-up `ActorService`te maken van actorgegevens door gebruik te maken van de remoting-listener die al aanwezig is in . Zie Bijvoorbeeld [Actoren back-upmaken en herstellen.](../synapse-analytics/sql-data-warehouse/backup-and-restore.md)

## <a name="actor-that-uses-a-remoting-v2-interface-compatible-stack"></a>Actor die een remoting V2-stack (interfacecompatibel) gebruikt

De remoting V2 (interface compatibel, bekend als V2_1) stack heeft alle functies van de V2 remoting stack. De interface is compatibel met de remoting V1 stack, maar het is niet achterwaarts compatibel met V2 en V1. Als u wilt upgraden van V1 naar V2_1 zonder effecten op de beschikbaarheid van de service, voert u de stappen in de volgende sectie uit.

De volgende wijzigingen zijn vereist om de remoting V2_1 stack te gebruiken:

1. Voeg het volgende verzamelkenmerk toe aan actorinterfaces.
  
   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Bouwen en upgraden actor service en actor client projecten om te beginnen met het gebruik van de V2 stack.

### <a name="actor-service-upgrade-to-remoting-v2-interface-compatible-stack-without-affecting-service-availability"></a>Update van de Actor-service naar de V2-stack (interfacecompatibel) zonder dat dit gevolgen heeft voor de beschikbaarheid van services

Deze wijziging is een upgrade in twee stappen. Volg de stappen in deze reeks.

1. Voeg het volgende verzamelkenmerk toe aan actorinterfaces. Met dit kenmerk worden twee listeners gestart voor de actorservice, V1 (bestaand) en de V2_1 listener. Upgrade de actorservice met deze wijziging.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
   ```

2. Upgrade de actorclients nadat u de vorige upgrade hebt voltooid.
   Deze stap zorgt ervoor dat de actorproxy de V2_1-stack wordt gebruikt.

3. Deze stap is optioneel. Wijzig het vorige kenmerk om de V1-listener te verwijderen.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2_1,RemotingClientVersion = RemotingClientVersion.V2_1)]
    ```

## <a name="actor-that-uses-the-remoting-v2-stack"></a>Actor die de remoting V2-stack gebruikt

Met het versie 2.8 NuGet-pakket kunnen gebruikers nu de remoting V2-stack gebruiken, die beter presteert en functies biedt zoals aangepaste serialisatie. Remoting V2 is niet achterwaarts compatibel met de bestaande remoting stack (nu de V1 remoting stack genoemd).

De volgende wijzigingen zijn vereist om de remoting V2-stack te gebruiken.

1. Voeg het volgende verzamelkenmerk toe aan actorinterfaces.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Bouw en upgrade de actorservice- en actorclientprojecten om de V2-stack te gebruiken.

### <a name="upgrade-the-actor-service-to-the-remoting-v2-stack-without-affecting-service-availability"></a>Upgrade de actorservice naar de remoting V2-stack zonder de beschikbaarheid van de service te beïnvloeden

Deze wijziging is een upgrade in twee stappen. Volg de stappen in deze reeks.

1. Voeg het volgende verzamelkenmerk toe aan actorinterfaces. Met dit kenmerk worden twee listeners gestart voor de actorservice, V1 (bestaand) en de V2-listener. Upgrade de actorservice met deze wijziging.

   ```csharp
   [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V1|RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
   ```

2. Upgrade de actorclients nadat u de vorige upgrade hebt voltooid.
   Deze stap zorgt ervoor dat de actorproxy de remoting V2-stack gebruikt.

3. Deze stap is optioneel. Wijzig het vorige kenmerk om de V1-listener te verwijderen.

    ```csharp
    [assembly:FabricTransportActorRemotingProvider(RemotingListenerVersion = RemotingListenerVersion.V2,RemotingClientVersion = RemotingClientVersion.V2)]
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
