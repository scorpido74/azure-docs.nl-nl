---
title: Reliable Actors noteert over actor type serialisatie
description: Bespreekt basisvereisten voor het definiëren van serializable klassen die kunnen worden gebruikt om service fabric betrouwbare actoren staten en interfaces te definiëren
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 876c4f5f45ff6c81a53274cf32e8bebecc1acfce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75349304"
---
# <a name="notes-on-service-fabric-reliable-actors-type-serialization"></a>Opmerkingen over Service Fabric Betrouwbare Actoren type serialisatie
De argumenten van alle methoden, resultaattypen van de taken die door elke methode in een actorinterface worden geretourneerd en objecten die zijn opgeslagen in de statusbeheerder van een actor, moeten [gegevenscontract serializable](/dotnet/framework/wcf/feature-details/types-supported-by-the-data-contract-serializer)zijn . Dit geldt ook voor de argumenten van de methoden die zijn gedefinieerd in [actorgebeurtenis-interfaces](service-fabric-reliable-actors-events.md). (Actor gebeurtenis interface methoden altijd nietig terug.)

## <a name="custom-data-types"></a>Aangepaste gegevenstypen
In dit voorbeeld definieert de volgende actorinterface een `VoicemailBox`methode die een aangepast gegevenstype met de naam retourneert:

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

De interface wordt geïmplementeerd door een actor die `VoicemailBox` de statusbeheerder gebruikt om een object op te slaan:

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

In dit voorbeeld `VoicemailBox` wordt het object geserialiseerd wanneer:

* Het object wordt verzonden tussen een actorinstantie en een beller.
* Het object wordt opgeslagen in de statusbeheerder waar het wordt ingeschakeld op schijf en gerepliceerd naar andere knooppunten.

Het Framework Reliable Actor maakt gebruik van DataContract serialisatie. Daarom moeten de aangepaste gegevensobjecten en hun leden worden geannoteerd met respectievelijk de kenmerken **DataContract** en **DataMember.**

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
* [Levenscyclus van actor's en garbage collection](service-fabric-reliable-actors-lifecycle.md)
* [Actortimers en herinneringen](service-fabric-reliable-actors-timers-reminders.md)
* [Actorgebeurtenissen](service-fabric-reliable-actors-events.md)
* [Acteur reentrancy](service-fabric-reliable-actors-reentrancy.md)
* [Actor polymorfisme en object-georiënteerde ontwerppatronen](service-fabric-reliable-actors-polymorphism.md)
* [Actor diagnostiek en prestatiebewaking](service-fabric-reliable-actors-diagnostics.md)
