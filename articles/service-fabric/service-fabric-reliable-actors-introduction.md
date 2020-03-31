---
title: Overzicht van betrouwbare actoren van servicestof
description: Inleiding tot het programmeermodel Van betrouwbare acteurs van Service Fabric, gebaseerd op het virtual actor-patroon.
author: vturecek
ms.topic: conceptual
ms.date: 11/01/2017
ms.author: vturecek
ms.openlocfilehash: 6aafa2a3372c431f8afa7fad41051c26c3fe5fcd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75645562"
---
# <a name="introduction-to-service-fabric-reliable-actors"></a>Inleiding tot service fabric betrouwbare actoren
Reliable Actors is een Service Fabric-toepassingskader gebaseerd op het [Virtual Actor-patroon.](https://research.microsoft.com/en-us/projects/orleans/) De Reliable Actors API biedt een single-threaded programmeermodel dat is gebaseerd op de schaalbaarheid en betrouwbaarheidsgaranties van Service Fabric.

## <a name="what-are-actors"></a>Wat zijn acteurs?
Een actor is een geïsoleerde, onafhankelijke eenheid van compute en state met single-threaded uitvoering. Het [actorpatroon](https://en.wikipedia.org/wiki/Actor_model) is een rekenmodel voor gelijktijdige of gedistribueerde systemen waarin een groot aantal van deze actoren gelijktijdig en onafhankelijk van elkaar kan uitvoeren. Acteurs kunnen met elkaar communiceren en ze kunnen meer acteurs creëren.

### <a name="when-to-use-reliable-actors"></a>Wanneer betrouwbare actoren te gebruiken
Service Fabric Reliable Actors is een implementatie van het actor design patroon. Zoals met elk software ontwerppatroon, wordt de beslissing om een specifiek patroon te gebruiken gemaakt op basis van de vraag of een software ontwerp probleem past in het patroon.

Hoewel het actor-ontwerppatroon goed kan passen bij een aantal problemen en scenario's met gedistribueerde systemen, moet zorgvuldig rekening worden gehouden met de beperkingen van het patroon en het kader dat het implementeert. Als algemene richtlijnen moet u rekening houden met het actorpatroon om uw probleem of scenario te modelleren als:

* Uw probleemruimte omvat een groot aantal (duizenden of meer) kleine, onafhankelijke en geïsoleerde eenheden van staat en logica.
* U wilt werken met objecten met één thread die geen significante interactie van externe componenten vereisen, inclusief querystatus over een reeks actoren.
* Uw actor-exemplaren blokkeren bellers met onvoorspelbare vertragingen niet door I/O-bewerkingen uit te geven.

## <a name="actors-in-service-fabric"></a>Actoren in de Stof van de Dienst
In Service Fabric worden actoren geïmplementeerd in het Framework Reliable Actors: An actor-pattern-based application framework built on top of [Service Fabric Reliable Services](service-fabric-reliable-services-introduction.md). Elke Reliable Actor-service die u schrijft is eigenlijk een verdeelde, stateful Reliable Service.

Elke actor wordt gedefinieerd als een instantie van een actortype, identiek aan de manier waarop een .NET-object een instantie van een .NET-type is. Er kan bijvoorbeeld een actortype zijn dat de functionaliteit van een rekenmachine implementeert en er kunnen veel actoren van dat type zijn die op verschillende knooppunten over een cluster worden gedistribueerd. Elk van deze actor is uniek geïdentificeerd door een actor-ID.

## <a name="actor-lifetime"></a>Acteur Lifetime
Service Fabric acteurs zijn virtueel, wat betekent dat hun leven niet is gekoppeld aan hun in-memory vertegenwoordiging. Als gevolg hiervan hoeven ze niet expliciet te worden gemaakt of vernietigd. De runtime van Betrouwbare acteurs activeert automatisch een actor wanneer hij voor het eerst een aanvraag voor die actor-ID ontvangt. Als een actor gedurende een bepaalde periode niet wordt gebruikt, verzamelt het garbage-verzamelt het object In-memory de runtime garbage van Betrouwbare actoren. Het zal ook kennis van het bestaan van de acteur te handhaven moet het later moeten worden gereactiveerd. Zie [Levenscyclus van Actor en garbage collection](service-fabric-reliable-actors-lifecycle.md)voor meer informatie.

Deze virtuele acteur levenslange abstractie draagt een aantal kanttekeningen als gevolg van de virtuele acteur model, en in feite de Betrouwbare Acteurs implementatie wijkt af en toe van dit model.

* Een actor wordt automatisch geactiveerd (waardoor een actorobject wordt gebouwd) de eerste keer dat een bericht naar de actor-id wordt verzonden. Na enige tijd is het object van de actor garbage collected. In de toekomst zorgt het opnieuw gebruiken van de actor-id ervoor dat een nieuw actorobject wordt gebouwd. De status van een actor overleeft de levensduur van het object wanneer deze wordt opgeslagen in de statusbeheerder.
* Als u een actor-methode voor een actor-id aanroept, wordt die actor geactiveerd. Om deze reden, acteur types hebben hun constructor impliciet genoemd door de runtime. Daarom kan clientcode geen parameters doorgeven aan de constructor van het actortype, hoewel parameters door de service zelf kunnen worden doorgegeven aan de constructor van de actor. Het resultaat is dat actoren in een gedeeltelijk geïnitialiseerde toestand kunnen worden geconstrueerd tegen de tijd dat er andere methoden op worden aangeroepen, als de actor initialisatieparameters van de client vereist. Er is geen enkele ingang voor de activering van een actor van de client.
* Hoewel Reliable Actors impliciet acteurobjecten maken; je hebt de mogelijkheid om een actor en de status ervan expliciet te verwijderen.

## <a name="distribution-and-failover"></a>Distributie en failover
Om schaalbaarheid en betrouwbaarheid te bieden, distribueert Service Fabric actoren over het cluster en migreert deze automatisch van mislukte knooppunten naar gezonde knooppunten, indien nodig. Dit is een abstractie over een [verdeelde, stateful Betrouwbare Service](service-fabric-concepts-partitioning.md). Distributie, schaalbaarheid, betrouwbaarheid en automatische failover worden allemaal geleverd op grond van het feit dat acteurs worden uitgevoerd binnen een stateful Reliable Service genaamd de *Actor Service*.

Acteurs worden verdeeld over de partities van de Actor-service en deze partities worden verdeeld over de knooppunten in een cluster van Servicefabric. Elke servicepartitie bevat een set actoren. Service Fabric beheert de distributie en failover van de servicepartities.

Een actorservice met negen partities die zijn geïmplementeerd in drie knooppunten met behulp van de standaardplaatsing van de actorpartitie, wordt bijvoorbeeld zo verdeeld:

![Betrouwbare acteurs distributie][2]

Het Actor Framework beheert het partitieschema en de instellingen voor het belangrijkste bereik voor u. Dit vereenvoudigt een aantal keuzes, maar draagt ook enige aandacht:

* Met Reliable Services u een partitieschema, sleutelbereik (bij gebruik van een bereikpartitieschema) en partitietelling kiezen. Betrouwbare actoren is beperkt tot het bereik verdeling regeling (de uniforme Int64 regeling) en vereist dat u gebruik maken van de volledige Int64 toetsbereik.
* Standaard worden acteurs willekeurig in partities geplaatst, wat resulteert in een uniforme verdeling.
* Omdat actoren willekeurig worden geplaatst, moet worden verwacht dat actorbewerkingen altijd netwerkcommunicatie vereisen, inclusief serialisatie en deserialisatie van methodegespreksgegevens, waardoor latentie en overhead nodig zijn.
* In geavanceerde scenario's is het mogelijk om de plaatsing van actorpartities te beheren met int64-actor-id's die toewijzen aan specifieke partities. Dit kan echter leiden tot een onevenwichtige verdeling van actoren over partities.

Voor meer informatie over hoe actorservices worden verdeeld, verwijzen naar [partitionering concepten voor acteurs](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors).

## <a name="actor-communication"></a>Actor communicatie
Actorinteracties worden gedefinieerd in een interface die wordt gedeeld door de actor die de interface implementeert, en de client die een proxy aan een actor via dezelfde interface krijgt. Omdat deze interface wordt gebruikt om actormethoden asynchroon aan te roepen, moet elke methode op de interface Taak-terugkerend zijn.

Aanroepingen van methoden en hun antwoorden resulteren uiteindelijk in netwerkaanvragen in het cluster, zodat de argumenten en de resultaattypen van de taken die ze retourneren, serialiseerbaar moeten zijn door het platform. In het bijzonder moeten zij [gegevens contract serializable](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

### <a name="the-actor-proxy"></a>De actorproxy
De client-API betrouwbare actoren biedt communicatie tussen een actor-instantie en een actorclient. Als u met een actor wilt communiceren, maakt een client een proxyobject voor actoren dat de actor-interface implementeert. De client werkt samen met de actor door een beroep te doen op methoden op het proxyobject. De actorproxy kan worden gebruikt voor client-to-actor- en actor-to-actor-communicatie.

```csharp
// Create a randomly distributed actor ID
ActorId actorId = ActorId.CreateRandom();

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
IMyActor myActor = ActorProxy.Create<IMyActor>(actorId, new Uri("fabric:/MyApp/MyActorService"));

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
await myActor.DoWorkAsync();
```

```java
// Create actor ID with some name
ActorId actorId = new ActorId("Actor1");

// This only creates a proxy object, it does not activate an actor or invoke any methods yet.
MyActor myActor = ActorProxyBase.create(actorId, new URI("fabric:/MyApp/MyActorService"), MyActor.class);

// This will invoke a method on the actor. If an actor with the given ID does not exist, it will be activated by this method call.
myActor.DoWorkAsync().get();
```


Houd er rekening mee dat de twee gegevens die worden gebruikt om het proxyobject van de actor te maken, de actor-id en de toepassingsnaam zijn. De actor-id identificeert de actor op unieke wijze, terwijl de toepassingsnaam de [Toepassing Servicefabric](service-fabric-reliable-actors-platform.md#application-model) identificeert waar de actor wordt geïmplementeerd.

De `ActorProxy`(C#) `ActorProxyBase`/ (Java) klasse aan de clientzijde voert de nodige resolutie uit om de actor te lokaliseren door ID en een communicatiekanaal mee te openen. Het herprobeert ook om de acteur te lokaliseren in het geval van communicatiefouten en failovers. Als gevolg hiervan heeft de bezorging van berichten de volgende kenmerken:

* Bericht levering is de beste inspanning.
* Acteurs kunnen dubbele berichten van dezelfde client ontvangen.

## <a name="concurrency"></a>Gelijktijdigheid
De Runtime Betrouwbare Actoren biedt een eenvoudig turn-based toegangsmodel voor toegang tot actormethoden. Dit betekent dat er op geen enkel moment meer dan één thread actief kan zijn in de code van een actorobject. Turn-based toegang vereenvoudigt gelijktijdige systemen sterk, omdat er geen synchronisatiemechanismen nodig zijn voor gegevenstoegang. Het betekent ook dat systemen moeten worden ontworpen met speciale overwegingen voor de single-threaded toegangsaard van elk actor-exemplaar.

* Een enkele actorinstantie kan niet meer dan één aanvraag tegelijk verwerken. Een actorinstantie kan een doorvoerknelpunt veroorzaken als wordt verwacht dat gelijktijdige aanvragen worden verwerkt.
* Actoren kunnen op elkaar ineenlopen als er een cirkelvormig verzoek is tussen twee actoren, terwijl een extern verzoek tegelijkertijd aan een van de actoren wordt gedaan. De actor runtime zal automatisch een time-out op acteur gesprekken en gooi een uitzondering op de beller om mogelijke impasse situaties te onderbreken.

![Betrouwbare acteurs communicatie][3]

### <a name="turn-based-access"></a>Turn-based access
Een beurt bestaat uit de volledige uitvoering van een actormethode in reactie op een verzoek van andere actoren of clients, of de volledige uitvoering van een [timer/herinneringscallback.](service-fabric-reliable-actors-timers-reminders.md) Hoewel deze methoden en callbacks asynchroon zijn, interleave de Runtime van actoren hen niet. Een bocht moet volledig klaar zijn voordat een nieuwe beurt is toegestaan. Met andere woorden, een actormethode of timer/herinneringscallback die momenteel wordt uitgevoerd, moet volledig zijn voltooid voordat een nieuwe aanroep naar een methode of terugroep is toegestaan. Een methode of callback wordt geacht te zijn voltooid als de uitvoering is teruggekeerd van de methode of callback en de taak die is geretourneerd door de methode of callback is voltooid. Het is de moeite waard te benadrukken dat turn-based gelijktijdigheid wordt gerespecteerd, zelfs over verschillende methoden, timers en callbacks.

De Acteurs runtime dwingt turn-based gelijktijdigheid door het verwerven van een per-actor slot aan het begin van een beurt en het vrijgeven van het slot aan het einde van de beurt. Zo wordt turn-based gelijktijdigheid afgedwongen op basis van een per-actor basis en niet over acteurs. Actormethoden en timer/herinneringsoproepen kunnen gelijktijdig worden uitgevoerd namens verschillende actoren.

Het volgende voorbeeld illustreert de bovenstaande concepten. Overweeg een actortype dat twee asynchrone methoden implementeert (bijvoorbeeld *Methode1* en *Methode2),* een timer en een herinnering. Het onderstaande diagram toont een voorbeeld van een tijdlijn voor de uitvoering van deze methoden en callbacks namens twee acteurs *(ActorId1* en *ActorId2)* die tot dit actortype behoren.

![Betrouwbare acteurs runtime turn-based gelijktijdigheid en toegang][1]

Dit diagram volgt deze conventies:

* Elke verticale lijn toont de logische uitvoeringsstroom van een methode of een callback namens een bepaalde actor.
* De gebeurtenissen die op elke verticale lijn zijn gemarkeerd, vinden plaats in chronologische volgorde, waarbij nieuwere gebeurtenissen zich onder oudere gebeurtenissen voordoen.
* Verschillende kleuren worden gebruikt voor tijdlijnen die overeenkomen met verschillende actoren.
* Markering wordt gebruikt om aan te geven voor welke duur het vergrendelingsslot per actor wordt gehouden namens een methode of terugroepen.

Enkele belangrijke punten om te overwegen:

* Terwijl *Method1* wordt uitgevoerd namens *ActorId2* in antwoord op client verzoek *xyz789*, een andere client verzoek (*abc123*) komt dat ook vereist *Method1* worden uitgevoerd door *ActorId2*. De tweede uitvoering van *Methode1* begint echter pas nadat de voorafgaande uitvoering is voltooid. Op dezelfde manier wordt een herinnering geregistreerd door *ActorId2,* terwijl *Methode1* wordt uitgevoerd in antwoord op clientverzoek *xyz789*. De herinneringsterugroep wordt pas uitgevoerd nadat beide uitvoeringen van *Methode1* zijn voltooid. Dit alles is te wijten aan turn-based gelijktijdigheid wordt afgedwongen voor *ActorId2*.
* Op dezelfde manier wordt turn-based gelijktijdigheid ook afgedwongen voor *ActorId1*, zoals blijkt uit de uitvoering van *Method1*, *Method2*, en de timer callback namens *ActorId1* gebeurt op een seriële manier.
* Uitvoering van *Methode1* namens *ActorId1* overlapt met de uitvoering ervan namens *ActorId2*. Dit komt omdat turn-based gelijktijdigheid wordt afgedwongen alleen binnen een acteur en niet tussen acteurs.
* In sommige van de method/callback-uitvoeringen wordt de `Task`(C#) / `CompletableFuture`(Java) geretourneerd door de methode/callback-uitvoeringen nadat de methode is teruggekeerd. In sommige andere is de asynchrone bewerking al voltooid tegen de tijd dat de methode/callback terugkeert. In beide gevallen wordt het vergrendelingsslot per actor pas vrijgegeven nadat zowel de methode/callback terugkeert als de asynchrone bewerking is voltooid.

### <a name="reentrancy"></a>Herintreding
De runtime van Acteurs maakt reentrancy standaard mogelijk. Dit betekent dat als een actormethode van *Actor A* een methode aanroept op *Actor B*, die op zijn beurt een andere methode aanroept op Actor *A*, die methode mag worden uitgevoerd. Dit komt omdat het deel uitmaakt van dezelfde logische callchain context. Alle timer- en herinneringsoproepen beginnen met de nieuwe logische gesprekscontext. Zie de [re-entrancy van betrouwbare actoren](service-fabric-reliable-actors-reentrancy.md) voor meer details.

### <a name="scope-of-concurrency-guarantees"></a>Reikwijdte van gelijktijdigheidsgaranties
De runtime van de actoren biedt deze gelijktijdigheidsgaranties in situaties waarin zij de aanroep van deze methoden controleert. Het biedt bijvoorbeeld deze garanties voor de methodeaanroepingen die worden uitgevoerd in reactie op een clientverzoek, evenals voor timer- en herinneringsoproepen. Als de actorcode echter rechtstreeks een beroep doet op deze methoden buiten de mechanismen die door de runtime van de actoren worden geboden, kan de runtime geen gelijktijdigheidsgaranties bieden. Als de methode bijvoorbeeld wordt aangeroepen in de context van een taak die niet is gekoppeld aan de taak die door de actormethoden wordt geretourneerd, kan de runtime geen gelijktijdigheidsgaranties bieden. Als de methode wordt aangeroepen uit een thread die de actor zelf maakt, kan de runtime ook geen gelijktijdigheidsgaranties bieden. Daarom moeten acteurs voor het uitvoeren van [achtergrondbewerkingen actortimers en actorherinneringen](service-fabric-reliable-actors-timers-reminders.md) gebruiken die turn-based gelijktijdigheid respecteren.

## <a name="next-steps"></a>Volgende stappen
Ga aan de slag door uw eerste Reliable Actors-service te bouwen:
   * [Aan de slag met betrouwbare actoren op .NET](service-fabric-reliable-actors-get-started.md)
   * [Aan de slag met betrouwbare acteurs op Java](service-fabric-reliable-actors-get-started-java.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png
[2]: ./media/service-fabric-reliable-actors-introduction/distribution.png
[3]: ./media/service-fabric-reliable-actors-introduction/actor-communication.png
