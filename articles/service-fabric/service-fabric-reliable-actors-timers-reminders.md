---
title: Betrouwbare acteurs timers en herinneringen
description: Inleiding tot timers en herinneringen voor servicefabric betrouwbare actoren, inclusief richtlijnen over wanneer ze elk moeten worden gebruikt.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 02d6220b31ee9c991e8450759bf46759af6177a3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75639612"
---
# <a name="actor-timers-and-reminders"></a>Actortimers en herinneringen
Acteurs kunnen periodiek werk aan zichzelf plannen door timers of herinneringen te registreren. In dit artikel ziet u hoe u timers en herinneringen gebruikt en worden de verschillen tussen deze artikelen uitgelegd.

## <a name="actor-timers"></a>Actortimers
Actor timers bieden een eenvoudige wrapper rond een .NET of Java timer om ervoor te zorgen dat de callback methoden respecteren de turn-based gelijktijdigheid garandeert dat de Actors runtime biedt.

Acteurs kunnen `RegisterTimer`de (C#) of `registerTimer` `UnregisterTimer`(Java) en `unregisterTimer`(C#) of (Java) methoden op hun basisklasse gebruiken om hun timers te registreren en uit te schrijven. Het onderstaande voorbeeld toont het gebruik van timer-API's. De API's lijken erg op de timer van .NET of Java. In dit voorbeeld, wanneer de timer is verschuldigd, `MoveObject`de acteurs `moveObject`runtime zal de (C#) of (Java) methode aanroepen. De methode is gegarandeerd om de turn-based gelijktijdigheid te respecteren. Dit betekent dat er geen andere actormethoden of timer/herinneringsterugroepen aan de gang zijn totdat deze callback de uitvoering heeft voltooid.

```csharp
class VisualObjectActor : Actor, IVisualObject
{
    private IActorTimer _updateTimer;

    public VisualObjectActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    protected override Task OnActivateAsync()
    {
        ...

        _updateTimer = RegisterTimer(
            MoveObject,                     // Callback method
            null,                           // Parameter to pass to the callback method
            TimeSpan.FromMilliseconds(15),  // Amount of time to delay before the callback is invoked
            TimeSpan.FromMilliseconds(15)); // Time interval between invocations of the callback method

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
    {
        if (_updateTimer != null)
        {
            UnregisterTimer(_updateTimer);
        }

        return base.OnDeactivateAsync();
    }

    private Task MoveObject(object state)
    {
        ...
        return Task.FromResult(true);
    }
}
```
```Java
public class VisualObjectActorImpl extends FabricActor implements VisualObjectActor
{
    private ActorTimer updateTimer;

    public VisualObjectActorImpl(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    @Override
    protected CompletableFuture onActivateAsync()
    {
        ...

        return this.stateManager()
                .getOrAddStateAsync(
                        stateName,
                        VisualObject.createRandom(
                                this.getId().toString(),
                                new Random(this.getId().toString().hashCode())))
                .thenApply((r) -> {
                    this.registerTimer(
                            (o) -> this.moveObject(o),                        // Callback method
                            "moveObject",
                            null,                                             // Parameter to pass to the callback method
                            Duration.ofMillis(10),                            // Amount of time to delay before the callback is invoked
                            Duration.ofMillis(timerIntervalInMilliSeconds));  // Time interval between invocations of the callback method
                    return null;
                });
    }

    @Override
    protected CompletableFuture onDeactivateAsync()
    {
        if (updateTimer != null)
        {
            unregisterTimer(updateTimer);
        }

        return super.onDeactivateAsync();
    }

    private CompletableFuture moveObject(Object state)
    {
        ...
        return this.stateManager().getStateAsync(this.stateName).thenCompose(v -> {
            VisualObject v1 = (VisualObject)v;
            v1.move();
            return (CompletableFuture<?>)this.stateManager().setStateAsync(stateName, v1).
                    thenApply(r -> {
                      ...
                      return null;});
        });
    }
}
```

De volgende periode van de timer begint nadat de callback de uitvoering heeft voltooid. Dit houdt in dat de timer wordt gestopt terwijl de callback wordt uitgevoerd en wordt gestart wanneer de callback is voltooid.

De runtime van Acteurs slaat wijzigingen op in de statusmanager van de actor wanneer de terugbelperiode is afgelopen. Als er een fout optreedt bij het opslaan van de status, wordt dat object deactiveren en wordt een nieuwe instantie geactiveerd.

Alle timers worden gestopt wanneer de actor wordt gedeactiveerd als onderdeel van garbage collection. Daarna worden er geen timercallbacks ingeroepen. Ook behoudt de runtime Van Acteurs geen informatie over de timers die vóór de activering werden uitgevoerd. Het is aan de actor om alle timers te registreren die het nodig heeft wanneer het in de toekomst opnieuw wordt geactiveerd. Zie voor meer informatie de sectie over [het ophalen van actorgarbage.](service-fabric-reliable-actors-lifecycle.md)

## <a name="actor-reminders"></a>Herinneringen voor actor's
Herinneringen zijn een mechanisme om permanente callbacks op een actor op bepaalde tijdstippen te activeren. Hun functionaliteit is vergelijkbaar met timers. Maar in tegenstelling tot timers, herinneringen worden geactiveerd onder alle omstandigheden totdat de acteur expliciet de registreert ze of de acteur is expliciet verwijderd. In het bijzonder worden herinneringen geactiveerd voor actordeactiveringen en failovers omdat de runtime van Acteurs informatie over de herinneringen van de actor blijft gebruiken met behulp van actor state provider. Houd er rekening mee dat de betrouwbaarheid van herinneringen is gekoppeld aan de betrouwbaarheidsgaranties van de staat die door de aanbieder van de actorstaat worden geboden. Dit betekent dat voor actoren waarvan de staat persistentie is ingesteld op Geen, de herinneringen niet zal vuren na een failover. 

Als u een herinnering wilt `RegisterReminderAsync` registreren, roept een actor de methode aan die in de basisklasse is opgegeven, zoals in het volgende voorbeeld wordt weergegeven:

```csharp
protected override async Task OnActivateAsync()
{
    string reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    IActorReminder reminderRegistration = await this.RegisterReminderAsync(
        reminderName,
        BitConverter.GetBytes(amountInDollars),
        TimeSpan.FromDays(3),    //The amount of time to delay before firing the reminder
        TimeSpan.FromDays(1));    //The time interval between firing of reminders
}
```

```Java
@Override
protected CompletableFuture onActivateAsync()
{
    String reminderName = "Pay cell phone bill";
    int amountInDollars = 100;

    ActorReminder reminderRegistration = this.registerReminderAsync(
            reminderName,
            state,
            dueTime,    //The amount of time to delay before firing the reminder
            period);    //The time interval between firing of reminders
}
```

In dit `"Pay cell phone bill"` voorbeeld is de herinneringsnaam. Dit is een tekenreeks die de actor gebruikt om een herinnering op unieke wijze te identificeren. `BitConverter.GetBytes(amountInDollars)`(C#) is de context die is gekoppeld aan de herinnering. Het zal worden doorgegeven aan de acteur als een argument `IRemindable.ReceiveReminderAsync`om de herinnering `Remindable.receiveReminderAsync`callback, dat wil zeggen (C #) of (Java).

Acteurs die herinneringen gebruiken, `IRemindable` moeten de interface implementeren, zoals in het onderstaande voorbeeld wordt weergegeven.

```csharp
public class ToDoListActor : Actor, IToDoListActor, IRemindable
{
    public ToDoListActor(ActorService actorService, ActorId actorId)
        : base(actorService, actorId)
    {
    }

    public Task ReceiveReminderAsync(string reminderName, byte[] context, TimeSpan dueTime, TimeSpan period)
    {
        if (reminderName.Equals("Pay cell phone bill"))
        {
            int amountToPay = BitConverter.ToInt32(context, 0);
            System.Console.WriteLine("Please pay your cell phone bill of ${0}!", amountToPay);
        }
        return Task.FromResult(true);
    }
}
```
```Java
public class ToDoListActorImpl extends FabricActor implements ToDoListActor, Remindable
{
    public ToDoListActor(FabricActorService actorService, ActorId actorId)
    {
        super(actorService, actorId);
    }

    public CompletableFuture receiveReminderAsync(String reminderName, byte[] context, Duration dueTime, Duration period)
    {
        if (reminderName.equals("Pay cell phone bill"))
        {
            int amountToPay = ByteBuffer.wrap(context).getInt();
            System.out.println("Please pay your cell phone bill of " + amountToPay);
        }
        return CompletableFuture.completedFuture(true);
    }

```

Wanneer een herinnering wordt geactiveerd, wordt de `ReceiveReminderAsync`runtime van `receiveReminderAsync`betrouwbare actoren een beroep doen op de methode (C#) of (Java) op de Actor. Een actor kan meerdere herinneringen `ReceiveReminderAsync`registreren en de `receiveReminderAsync`methode (C#) of (Java) wordt aangeroepen wanneer een van deze herinneringen wordt geactiveerd. De actor kan de herinneringsnaam gebruiken `ReceiveReminderAsync`die wordt `receiveReminderAsync`doorgegeven aan de methode (C#) of (Java) om erachter te komen welke herinnering is geactiveerd.

De runtime van Acteurs slaat de `ReceiveReminderAsync`status van `receiveReminderAsync`de actor op wanneer de (C#) of (Java)-oproep is afgelopen. Als er een fout optreedt bij het opslaan van de status, wordt dat object deactiveren en wordt een nieuwe instantie geactiveerd.

Als u een herinnering wilt `UnregisterReminderAsync`uitschrijven, roept `unregisterReminderAsync`een actor de methode (C#) of (Java) aan, zoals in de onderstaande voorbeelden wordt weergegeven.

```csharp
IActorReminder reminder = GetReminder("Pay cell phone bill");
Task reminderUnregistration = UnregisterReminderAsync(reminder);
```
```Java
ActorReminder reminder = getReminder("Pay cell phone bill");
CompletableFuture reminderUnregistration = unregisterReminderAsync(reminder);
```

Zoals hierboven wordt `UnregisterReminderAsync`weergegeven, accepteert `unregisterReminderAsync`de methode (C#) of (Java) een `IActorReminder`(C#) of `ActorReminder`(Java) interface. De basisklasse actor `GetReminder`ondersteunt een `getReminder`(C#) of (Java)-methode die kan worden gebruikt om de `IActorReminder`(C#) of `ActorReminder`(Java)-interface op te halen door de herinneringsnaam door te geven. Dit is handig omdat de actor `IActorReminder`de (C#) of `ActorReminder`(Java)-interface `RegisterReminder`die is `registerReminder`geretourneerd van de (C#) of (Java) methodeaanroep niet hoeft voort te duren.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over gebeurtenissen en re-entrantie voor betrouwbare actoren:
* [Actorgebeurtenissen](service-fabric-reliable-actors-events.md)
* [Acteur reentrancy](service-fabric-reliable-actors-reentrancy.md)
