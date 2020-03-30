---
title: Overzicht van de levenscyclus van de Azure Service Fabric-actor
description: Legt servicefabric betrouwbare actor levenscyclus, garbage collection, en handmatig verwijderen van acteurs en hun staat
author: amanbha
ms.topic: conceptual
ms.date: 10/06/2017
ms.author: amanbha
ms.openlocfilehash: b05da78091260297d94062c06cba100d01ce7e2e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258315"
---
# <a name="actor-lifecycle-automatic-garbage-collection-and-manual-delete"></a>Levenscyclus van actor, automatische garbage collection en handmatig verwijderen
Een actor wordt geactiveerd wanneer een aanroep wordt gedaan naar een van zijn methoden. Een actor wordt gedeactiveerd (garbage verzameld door de Acteurs runtime) als het niet wordt gebruikt voor een configureerbare periode. Een actor en de status ervan kunnen ook op elk gewenst moment handmatig worden verwijderd.

## <a name="actor-activation"></a>Actoractivering
Wanneer een actor wordt geactiveerd, treedt het volgende op:

* Wanneer een oproep komt voor een acteur en een is nog niet actief, een nieuwe acteur wordt gemaakt.
* De status van de actor wordt geladen als de status behouden blijft.
* De `OnActivateAsync` methode (C#) of `onActivateAsync` (Java) (die kan worden overschreven in de actorimplementatie) wordt aangeroepen.
* De acteur wordt nu beschouwd als actief.

## <a name="actor-deactivation"></a>Actor deactivering
Wanneer een actor wordt gedeactiveerd, treedt het volgende op:

* Wanneer een actor gedurende een bepaalde periode niet wordt gebruikt, wordt deze verwijderd uit de tabel Actieve actoren.
* De `OnDeactivateAsync` methode (C#) of `onDeactivateAsync` (Java) (die kan worden overschreven in de actorimplementatie) wordt aangeroepen. Dit maakt alle timers voor de acteur vrij. Actorbewerkingen zoals statuswijzigingen mogen niet worden aangeroepen met deze methode.

> [!TIP]
> De Fabric Actors runtime zendt een aantal [gebeurtenissen met betrekking tot acteur activering en deactivering](service-fabric-reliable-actors-diagnostics.md#list-of-events-and-performance-counters). Ze zijn nuttig bij diagnostiek en prestatiebewaking.
>
>

### <a name="actor-garbage-collection"></a>Actor garbage collection
Wanneer een actor wordt gedeactiveerd, verwijzingen naar de acteur object worden vrijgegeven en het kan vuilnis normaal verzameld door de gemeenschappelijke taal runtime (CLR) of Java virtual machine (JVM) garbage collector. Garbage collection ruimt alleen het actorobject op; de status die is opgeslagen in de statusmanager van de actor **niet** wordt verwijderd. De volgende keer dat de actor wordt geactiveerd, wordt een nieuw actorobject gemaakt en wordt de status hersteld.

Wat telt als "wordt gebruikt" voor het doel van deactivering en garbage collection?

* Een oproep ontvangen
* `IRemindable.ReceiveReminderAsync`methode die wordt aangeroepen (alleen van toepassing als de actor herinneringen gebruikt)

> [!NOTE]
> als de actor timers gebruikt en de timer callback wordt aangeroepen, telt deze **niet** als "wordt gebruikt".
>
>

Voordat we ingaan op de details van deactivering, is het belangrijk om de volgende termen te definiëren:

* *Scaninterval*. Dit is het interval waarop de rentijd van acteurs de actieve acteurstafel scant op actoren die kunnen worden gedeactiveerd en afval kunnen worden verzameld. De standaardwaarde hiervoor is 1 minuut.
* *Niet-actieve time-out*. Dit is de hoeveelheid tijd die een actor nodig heeft om ongebruikt te blijven (idle) voordat deze kan worden gedeactiveerd en afval kan worden verzameld. De standaardwaarde hiervoor is 60 minuten.

Normaal gesproken hoeft u deze standaardwaarden niet te wijzigen. Indien nodig kunnen deze intervallen echter `ActorServiceSettings` worden gewijzigd bij het registreren van uw [Actor-service:](service-fabric-reliable-actors-platform.md)

```csharp
public class Program
{
    public static void Main(string[] args)
    {
        ActorRuntime.RegisterActorAsync<MyActor>((context, actorType) =>
                new ActorService(context, actorType,
                    settings:
                        new ActorServiceSettings()
                        {
                            ActorGarbageCollectionSettings =
                                new ActorGarbageCollectionSettings(10, 2)
                        }))
            .GetAwaiter()
            .GetResult();
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
    }
}
```
Voor elke actieve actor houdt de runtime van de actor bij hoeveel tijd hij niet heeft gebruikt (d.w.z. niet gebruikt). De acteur runtime controleert elk `ScanIntervalInSeconds` van de acteurs elke om te zien of `IdleTimeoutInSeconds`het kan worden vuilnis verzameld en markeert als het is inactief voor .

Wanneer een actor wordt gebruikt, wordt de idle tijd teruggezet naar 0. Hierna kan de actor alleen worden opgehaald als deze `IdleTimeoutInSeconds`weer niet actief blijft voor . Bedenk dat een actor wordt geacht te zijn gebruikt als een actor-interfacemethode of een actorherinneringsherinnering wordt uitgevoerd. Een actor wordt **niet** geacht te zijn gebruikt als de timer callback wordt uitgevoerd.

Het volgende diagram toont de levenscyclus van één actor om deze concepten te illustreren.

![Voorbeeld van inactieve tijd][1]

In het voorbeeld wordt de impact weergegeven van aanroepen, herinneringen en timers op de levensduur van deze actor. De volgende punten over het voorbeeld zijn het vermelden waard:

* ScanInterval en IdleTimeout zijn ingesteld op respectievelijk 5 en 10. (Eenheden doen er hier niet toe, omdat ons doel alleen is om het concept te illustreren.)
* De scan voor acteurs die afval worden verzameld gebeurt bij T=0,5,10,15,20,25, zoals gedefinieerd door het scaninterval van 5.
* Een periodieke timer wordt geactiveerd bij T=4,8,12,16,20,24, en de terugroep wordt uitgevoerd. Het heeft geen invloed op de idle tijd van de acteur.
* Een actormethodecall op T=7 stelt de idle-tijd terug op 0 en vertraagt de garbage collection van de actor.
* Een actor reminder callback wordt uitgevoerd op T=14 en vertraagt de garbage collection van de acteur verder.
* Tijdens de garbage collection scan op T=25 overschrijdt de idle tijd van de acteur uiteindelijk de idle time-out van 10, en de acteur wordt vuilnis verzameld.

Een actor zal nooit worden vuilnis verzameld terwijl het uitvoeren van een van de methoden, ongeacht hoeveel tijd wordt besteed aan het uitvoeren van die methode. Zoals eerder vermeld, voorkomt de uitvoering van actorinterfacemethoden en herinneringsterugroepen het ophalen van afval door de inactieve tijd van de actor opnieuw in te stellen op 0. De uitvoering van timercallbacks stelt de idle tijd niet terug op 0. De garbage collection van de actor wordt echter uitgesteld totdat de timer callback de uitvoering heeft voltooid.

## <a name="manually-deleting-actors-and-their-state"></a>Acteurs en hun staat handmatig verwijderen
Garbage collection van gedeactiveerde acteurs ruimt alleen het actorobject op, maar verwijdert geen gegevens die zijn opgeslagen in de State Manager van een actor. Wanneer een actor opnieuw wordt geactiveerd, worden de gegevens opnieuw beschikbaar gesteld via de State Manager. In gevallen waarin acteurs gegevens opslaan in State Manager en worden gedeactiveerd, maar nooit opnieuw worden geactiveerd, kan het nodig zijn om hun gegevens op te schonen.  Voor voorbeelden van hoe acteurs te verwijderen, lees [verwijderen acteurs en hun staat](service-fabric-reliable-actors-delete-actors.md).

## <a name="next-steps"></a>Volgende stappen
* [Actortimers en herinneringen](service-fabric-reliable-actors-timers-reminders.md)
* [Actorgebeurtenissen](service-fabric-reliable-actors-events.md)
* [Acteur reentrancy](service-fabric-reliable-actors-reentrancy.md)
* [Actor diagnostiek en prestatiebewaking](service-fabric-reliable-actors-diagnostics.md)
* [Actor API-referentiedocumentatie](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [C# Voorbeeldcode](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Java-voorbeeldcode](https://github.com/Azure-Samples/service-fabric-java-getting-started)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-lifecycle/garbage-collection.png
