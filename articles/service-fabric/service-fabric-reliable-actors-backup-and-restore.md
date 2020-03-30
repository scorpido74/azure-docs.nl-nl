---
title: Azure Service Fabric-actoren back-ups maken en herstellen
description: Meer informatie over het implementeren van back-ups en herstelin uw Azure Service Fabric-actoren.
author: vturecek
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: vturecek
ms.openlocfilehash: 41ba3f9c7d362756b800005d0c140c23dd96caa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75370456"
---
# <a name="implement-reliable-actors-backup-and-restore"></a>Implementeren betrouwbare actoren back-up en herstel

> [!NOTE]
> Microsoft raadt aan om [periodieke back-ups en herstel](service-fabric-backuprestoreservice-quickstart-azurecluster.md) te gebruiken voor het configureren van gegevensback-up van betrouwbare stateful-services en betrouwbare actoren. 
> 

In het volgende voorbeeld stelt een aangepaste actorservice een methode bloot om een back-up te maken van actorgegevens door gebruik te maken van de remoting-listener die al aanwezig is in `ActorService`:

```csharp
public interface IMyActorService : IService
{
    Task BackupActorsAsync();
}

class MyActorService : ActorService, IMyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<ActorBase> newActor)
        : base(context, typeInfo, newActor)
    { }

    public Task BackupActorsAsync()
    {
        return this.BackupAsync(new BackupDescription(PerformBackupAsync));
    }

    private async Task<bool> PerformBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           Directory.Delete(backupInfo.Directory, recursive: true);
        }
    }
}
```
```Java
public interface MyActorService extends Service
{
    CompletableFuture<?> backupActorsAsync();
}

class MyActorServiceImpl extends ActorService implements MyActorService
{
    public MyActorService(StatefulServiceContext context, ActorTypeInformation typeInfo, Func<FabricActorService, ActorId, ActorBase> newActor)
    {
       super(context, typeInfo, newActor);
    }

    public CompletableFuture backupActorsAsync()
    {
        return this.backupAsync(new BackupDescription((backupInfo, cancellationToken) -> performBackupAsync(backupInfo, cancellationToken)));
    }

    private CompletableFuture<Boolean> performBackupAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
    {
        try
        {
           // store the contents of backupInfo.Directory
           return true;
        }
        finally
        {
           deleteDirectory(backupInfo.Directory)
        }
    }

    void deleteDirectory(File file) {
        File[] contents = file.listFiles();
        if (contents != null) {
            for (File f : contents) {
               deleteDirectory(f);
             }
        }
        file.delete();
    }
}
```

In dit `IMyActorService` voorbeeld is een remoting-contract dat `IService` `Service` implementeert (C#) en `MyActorService`(Java) en vervolgens wordt geïmplementeerd door . Door het toevoegen van deze `IMyActorService` remoting contract, methoden op zijn nu `ActorServiceProxy`ook beschikbaar voor een klant door het creëren van een remoting proxy via:

```csharp
IMyActorService myActorServiceProxy = ActorServiceProxy.Create<IMyActorService>(
    new Uri("fabric:/MyApp/MyService"), ActorId.CreateRandom());

await myActorServiceProxy.BackupActorsAsync();
```
```Java
MyActorService myActorServiceProxy = ActorServiceProxy.create(MyActorService.class,
    new URI("fabric:/MyApp/MyService"), actorId);

myActorServiceProxy.backupActorsAsync();
```

Voor meer informatie over betrouwbare actoren, lees de volgende artikelen:
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
