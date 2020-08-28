---
title: Reliable Actors notities op serialisatie van het actor type
description: Hierin worden de basis vereisten beschreven voor het definiëren van serialiseerbare klassen die kunnen worden gebruikt voor het definiëren van Service Fabric Reliable Actors status en interfaces
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.custom: devx-track-csharp
ms.openlocfilehash: 33a15b3c6f570d3b4d36a205f91bce7b042f3bb4
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89016577"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Opmerkingen over Service Fabric Reliable Actors type serialisatie
De argumenten van alle methoden, resultaat typen van de taken die worden geretourneerd door elke methode in een actor-interface, en objecten die zijn opgeslagen in de status Manager van een actor moeten [serialiseerbaar](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer)zijn. Dit geldt ook voor de argumenten van de methoden die in [actor-gebeurtenis interfaces](service-fabric-reliable-actors-events.md)zijn gedefinieerd. (De interface methoden van actor-gebeurtenissen retour neren altijd void.)

## <a name="custom-data-types"></a>Aangepaste gegevens typen
In dit voor beeld definieert de volgende actor-interface een methode die resulteert in een aangepast gegevens type met de naam `VoicemailBox` :

```csharp
public interface IVoiceMailBoxActor : IActor
{
    Task<VoicemailBox> GetMailBoxAsync();
}
```

```Java
public interface VoiceMailBoxActor extends Actor
{
    CompletableFuture<VoicemailBox> getMailBoxAsync();
}
```

De interface wordt geïmplementeerd door een actor die gebruikmaakt van de status Manager voor het opslaan van een `VoicemailBox` object:

```csharp
[StatePersistence(StatePersistence.Persisted)]
public class VoiceMailBoxActor : Actor, IVoicemailBoxActor
{
    public VoiceMailBoxActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task<VoicemailBox> GetMailboxAsync()
    {
        return this.StateManager.GetStateAsync<VoicemailBox>("Mailbox");
    }
}

```

```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
public class VoiceMailBoxActorImpl extends FabricActor implements VoicemailBoxActor
{
    public VoiceMailBoxActorImpl(ActorService actorService, ActorId actorId)
    {
         super(actorService, actorId);
    }

    public CompletableFuture<VoicemailBox> getMailBoxAsync()
    {
         return this.stateManager().getStateAsync("Mailbox");
    }
}

```

In dit voor beeld `VoicemailBox` wordt het object geserialiseerd wanneer:

* Het object wordt verzonden tussen een actor-exemplaar en een aanroeper.
* Het object wordt opgeslagen in de status Manager, waar het wordt bewaard op schijf en gerepliceerd naar andere knoop punten.

Het reliable actor-Framework gebruikt data contract-serialisatie. Daarom moeten de aangepaste gegevens objecten en hun leden worden voorzien van de kenmerken **Data contract** en **Data member** , respectievelijk.

```csharp
[DataContract]
public class Voicemail
{
    [DataMember]
    public Guid Id { get; set; }

    [DataMember]
    public string Message { get; set; }

    [DataMember]
    public DateTime ReceivedAt { get; set; }
}
```
```Java
public class Voicemail implements Serializable
{
    private static final long serialVersionUID = 42L;

    private UUID id;                    //getUUID() and setUUID()

    private String message;             //getMessage() and setMessage()

    private GregorianCalendar receivedAt; //getReceivedAt() and setReceivedAt()
}
```


```csharp
[DataContract]
public class VoicemailBox
{
    public VoicemailBox()
    {
        this.MessageList = new List<Voicemail>();
    }

    [DataMember]
    public List<Voicemail> MessageList { get; set; }

    [DataMember]
    public string Greeting { get; set; }
}
```
```Java
public class VoicemailBox implements Serializable
{
    static final long serialVersionUID = 42L;
    
    public VoicemailBox()
    {
        this.messageList = new ArrayList<Voicemail>();
    }

    private List<Voicemail> messageList;   //getMessageList() and setMessageList()

    private String greeting;               //getGreeting() and setGreeting()
}
```


## <a name="next-steps"></a>Volgende stappen
* [Actor-levens cyclus en garbagecollection](service-fabric-reliable-actors-lifecycle.md)
* [Actor timers en herinneringen](service-fabric-reliable-actors-timers-reminders.md)
* [Actor gebeurtenissen](service-fabric-reliable-actors-events.md)
* [Actor herbetreedbaarheid](service-fabric-reliable-actors-reentrancy.md)
* [Actor-polymorfisme-en object gerichte ontwerp patronen](service-fabric-reliable-actors-polymorphism.md)
* [De functie voor het controleren van actor en prestaties](service-fabric-reliable-actors-diagnostics.md)
