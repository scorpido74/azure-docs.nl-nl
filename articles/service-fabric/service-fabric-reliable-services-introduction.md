---
title: Overzicht van het betrouw bare service programmeer model
description: Meer informatie over het betrouw bare service programmeer model van Service Fabric en aan de slag gaan met het schrijven van uw eigen services.
author: masnider
ms.topic: conceptual
ms.date: 3/9/2018
ms.author: masnider
ms.openlocfilehash: 88c8e4411c0bec23790b4f4c52fc4a3d1570edc6
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/02/2020
ms.locfileid: "75614244"
---
# <a name="reliable-services-overview"></a>Overzicht van Reliable Services
Azure Service Fabric vereenvoudigt het schrijven en beheren van stateless en stateful Reliable Services. In dit onderwerp komt het volgende aan de orde:

* Het programmeer model Reliable Services voor stateless en stateful Services.
* De keuzes die u moet maken bij het schrijven van een betrouw bare service.
* Sommige scenario's en voor beelden van het gebruik van Reliable Services en hoe ze worden geschreven.

Reliable Services is een van de beschik bare programmeer modellen op Service Fabric. De andere is het reliable actor-programmeer model, dat een virtueel actor-programmeer model bevat boven op het Reliable Services model. Zie [Introduction to Service Fabric reliable actors](service-fabric-reliable-actors-introduction.md)voor meer informatie over het programmeer model reliable actors.

Service Fabric beheert de levens duur van services, van inrichting en implementatie via een upgrade en verwijdering via [service Fabric toepassings beheer](service-fabric-deploy-remove-applications.md).

## <a name="what-are-reliable-services"></a>Wat zijn Reliable Services?
Reliable Services biedt u een eenvoudig, krachtig, op het hoogste niveau programmeer model waarmee u snel kunt zien wat belang rijk is voor uw toepassing. Met het Reliable Services-programmeer model krijgt u het volgende:

* Toegang tot de rest van de Service Fabric-programmeer-Api's. In tegens telling tot Service Fabric services die zijn gemodelleerd als [uitvoer bare gast bestanden](service-fabric-guest-executables-introduction.md), reliable Services de rest van de service Fabric api's rechtstreeks gebruiken. Hiermee kunnen services:
  * query uitvoeren op het systeem
  * status rapporteren over entiteiten in het cluster
  * meldingen over configuratie-en code wijzigingen ontvangen
  * Zoek en communiceer met andere services,
  * (optioneel) gebruik de [betrouw bare verzamelingen](service-fabric-reliable-services-reliable-collections.md)
  * ... en geven ze toegang tot veel andere mogelijkheden, allemaal van een eerste programmeer model van de klasse in verschillende programmeer talen.
* Een eenvoudig model voor het uitvoeren van uw eigen code die eruitziet als de programmeer modellen die u gebruikt. Uw code heeft een goed gedefinieerd ingangs punt en een eenvoudig beheerde levens cyclus.
* Een pluggable communicatie model. Gebruik het Trans Port van uw keuze, zoals HTTP met [Web-API](service-fabric-reliable-services-communication-webapi.md), websockets, aangepaste TCP-protocollen of iets anders. Reliable Services bieden enkele fantastische opties die u kunt gebruiken, of u kunt uw eigen keuze maken.
* Voor stateful Services kunt u met behulp van [betrouw bare verzamelingen](service-fabric-reliable-services-reliable-collections.md)uw status consistent en betrouwbaar in uw service opslaan met het programmeer model reliable Services. Betrouw bare verzamelingen zijn een eenvoudige set van zeer beschik bare en betrouw bare verzamelings klassen die bekend C# zijn bij iedereen die gebruik maakt van verzamelingen. Traditioneel, services die externe systemen nodig hebben voor betrouw bare status beheer. Met betrouw bare verzamelingen kunt u uw status naast uw computer opslaan met dezelfde hoge Beschik baarheid en betrouw baarheid die u kunt verwachten van Maxi maal beschik bare externe winkels. Dit model verbetert ook de latentie omdat u de reken kracht en de status moet gebruiken.

## <a name="what-makes-reliable-services-different"></a>Wat maakt Reliable Services anders?
Reliable Services in Service Fabric verschillen van de services die u eerder hebt geschreven. Service Fabric biedt betrouw baarheid, Beschik baarheid, consistentie en schaal baarheid.

* **Betrouw baarheid** : uw service blijft zelfs in onbetrouwbare omgevingen waar uw machines mislukken of problemen met het netwerk raken, of in gevallen waarin de services zelf fouten ondervinden en crashen of mislukken. Voor stateful Services blijft uw status behouden, zelfs in de aanwezigheid van netwerk of andere storingen.
* **Beschik baarheid** : uw service is bereikbaar en reageert. Service Fabric onderhoudt het gewenste aantal actieve exemplaren.
* **Schaal baarheid** : services zijn losgekoppeld van specifieke hardware en kunnen zo nodig worden verg root of verkleind door hardware of andere bronnen toe te voegen of te verwijderen. Services kunnen eenvoudig worden gepartitioneerd (met name in de staat van de status) om ervoor te zorgen dat de service gedeeltelijke fouten kan schalen en verwerken. Services kunnen dynamisch worden gemaakt en verwijderd via code, zodat er meer exemplaren kunnen worden uitgevoerd als dat nodig is, in reactie op aanvragen van klanten. Ten slotte Service Fabric moedigen Services lichter te zijn. Met Service Fabric kunnen duizenden services worden ingericht binnen één proces, in plaats van dat er volledige instanties of processen van het besturings systeem worden vereist voor één exemplaar van een service.
* **Consistentie** : alle informatie die in deze service is opgeslagen, kan gegarandeerd consistent zijn. Dit geldt ook voor meerdere betrouw bare verzamelingen binnen een service. Wijzigingen tussen verzamelingen in een service kunnen op een transactioneel atomische wijze worden uitgevoerd.

## <a name="service-lifecycle"></a>Service levenscyclus
Of uw service stateful of stateless is, Reliable Services een eenvoudige levens cyclus bieden waarmee u snel en aan de slag kunt met uw code.  Er zijn slechts één of twee methoden die u moet implementeren om uw service actief te laten zijn.

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** : deze methode is de plaats waar de service de communicatie stack (s) definieert die hij wil gebruiken. De communicatie stack, zoals [Web API](service-fabric-reliable-services-communication-webapi.md), definieert het luisterende eind punt of eind punten voor de service (hoe clients de service bereiken). Ook wordt gedefinieerd hoe de berichten die worden weer gegeven, communiceren met de rest van de service code.
* **RunAsync** : deze methode is waar uw service zijn bedrijfs logica uitvoert en waar alle achtergrond taken worden uitgevoerd die voor de levens duur van de service moeten worden gestart. Het Afbrekings token dat wordt gegeven, is een signaal voor wanneer het werk moet worden gestopt. Als de service bijvoorbeeld berichten uit een betrouw bare wachtrij moet ophalen en ze kan verwerken, is dit het geval.

Lees over als u voor het eerst een betrouw bare service bekijkt. Als u op zoek bent naar een gedetailleerd overzicht van de levens cyclus van betrouw bare Services, kunt u [het hoofd artikel](service-fabric-reliable-services-lifecycle.md)raadplegen.

## <a name="example-services"></a>Voorbeeld Services
Als u dit programmeer model wilt weten, kunt u een kort overzicht maken van twee verschillende services om te zien hoe deze onderdelen samen passen.

### <a name="stateless-reliable-services"></a>Stateless Reliable Services
Een stateless service is een locatie waar er geen status in de service wordt onderhouden tussen aanroepen. Een aanwezige staat is volledig beschikbaar en vereist geen synchronisatie, replicatie, persistentie of hoge Beschik baarheid.

Denk bijvoorbeeld aan een reken machine die geen geheugen heeft en ontvangt alle voor waarden en bewerkingen die tegelijk worden uitgevoerd.

In dit geval kan de `RunAsync()` (C#) of de `runAsync()` (Java) van de service leeg zijn, omdat er geen achtergrond taak verwerking is die door de service moet worden uitgevoerd. Wanneer de rekenmachine service wordt gemaakt, retourneert deze een `ICommunicationListener` (C#) of `CommunicationListener` (Java) (bijvoorbeeld [Web-API](service-fabric-reliable-services-communication-webapi.md)) waarmee een luister eindpunt wordt geopend op een bepaalde poort. Deze luisterende eindpunt hooks tot de verschillende berekenings methoden (bijvoorbeeld: ' toevoegen (N1, N2) ') waarmee de open bare API van de reken machine wordt gedefinieerd.

Wanneer een aanroep van een client wordt uitgevoerd, wordt de juiste methode aangeroepen en worden de bewerkingen uitgevoerd op de opgegeven gegevens en wordt het resultaat geretourneerd. Er wordt geen status opgeslagen.

Als u een interne status niet opslaat, is dit voorbeeld Calculator eenvoudig. Maar de meeste services zijn niet echt stateless. In plaats daarvan Externalize ze hun status aan een ander archief. (Een voor beeld van een web-app die afhankelijk is van het bewaren van de sessie status in een back-uparchief of cache is niet stateless.)

Een algemeen voor beeld van hoe stateless services worden gebruikt in Service Fabric is een front-end waarmee de open bare API voor een webtoepassing wordt weer gegeven. De front-end-service spreekt vervolgens naar stateful Services om een gebruikers aanvraag te volt ooien. In dit geval worden aanroepen van clients omgeleid naar een bekende poort, zoals 80, waar de stateless service luistert. Deze stateless service ontvangt de oproep en bepaalt of de oproep van een vertrouwde partij is en voor welke service deze is bestemd.  De stateless service stuurt de aanroep vervolgens door naar de juiste partitie van de stateful service en wacht op een antwoord. Wanneer het stateless service een antwoord ontvangt, wordt de oorspronkelijke client beantwoord. Een voor beeld van een dergelijke service vindt u in [C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) onze voor beelden / [Java](https://github.com/Azure-Samples/service-fabric-java-getting-started). Dit is slechts één voor beeld van dit patroon in de voor beelden, maar er zijn ook andere voor beelden.

### <a name="stateful-reliable-services"></a>Stateful Reliable Services
Een stateful service is een onderdeel dat een deel van de status moet hebben die consistent is en aanwezig is om de service te laten functioneren. Overweeg een service die voortdurend een voortschrijdend gemiddelde van een bepaalde waarde berekent op basis van updates die worden ontvangen. Hiervoor moet de huidige set van binnenkomende aanvragen die moeten worden verwerkt en het huidige gemiddelde hebben. Een service die gegevens ophaalt, verwerkt en opslaat in een extern archief (zoals een Azure Blob of tabel archief) is stateful. De status wordt alleen in het externe status archief bewaard.

Voor de meeste services wordt de status van het externe archief extern opgeslagen, omdat het externe archief een betrouw baarheid, Beschik baarheid, schaal baarheid en consistentie biedt voor die status. In Service Fabric zijn services niet vereist om hun status extern op te slaan. Service Fabric zorgt voor de service code en de service status van deze vereisten.

Stel dat we een service willen schrijven die afbeeldingen verwerkt. Hiervoor neemt de service in een installatie kopie en de reeks conversies die op die installatie kopie worden uitgevoerd. Deze service retourneert een communicatie-listener (Stel dat het een WebAPI is) waarmee een API wordt weer gegeven, zoals `ConvertImage(Image i, IList<Conversion> conversions)`. Wanneer een aanvraag wordt ontvangen, slaat de service deze op in een `IReliableQueue`en retourneert deze een id naar de client zodat de aanvraag kan worden gevolgd.

In deze service is `RunAsync()` wellicht ingewik kelder. De service heeft een lus in de `RunAsync()` waarmee aanvragen van `IReliableQueue` worden opgehaald en de gevraagde conversies worden uitgevoerd. De resultaten worden opgeslagen in een `IReliableDictionary`, zodat de client de geconverteerde installatie kopieën kan ophalen. Om ervoor te zorgen dat, zelfs als er iets mislukt, de installatie kopie niet verloren gaat, zou deze betrouw bare service uit de wachtrij halen, de conversies uitvoeren en het resultaat opslaan in één trans actie. In dit geval wordt het bericht uit de wachtrij verwijderd en worden de resultaten alleen in de resultaat woordenlijst opgeslagen wanneer de conversies zijn voltooid. Het is ook mogelijk dat de service de installatie kopie uit de wachtrij haalt en deze onmiddellijk opslaat in een externe opslag. Dit vermindert de hoeveelheid status die de service moet beheren, maar neemt de complexiteit toe omdat de service de vereiste meta gegevens moet bewaren om de externe opslag te beheren. Als er een van beide benaderingen is mislukt, blijft de aanvraag in de wachtrij staan om te worden verwerkt.

Een opmerking over deze service is dat deze net als een normale .NET-service klinkt. Het enige verschil is dat de gebruikte gegevens structuren (`IReliableQueue` en `IReliableDictionary`) worden aangeboden door Service Fabric, en zijn zeer betrouwbaar, beschikbaar en consistent.

## <a name="when-to-use-reliable-services-apis"></a>Wanneer moet u Reliable Services-Api's gebruiken?
Als een van de volgende kenmerken van uw toepassings service nodig is, kunt u Reliable Services-Api's overwegen:

* U wilt dat de code van uw service (en optioneel staat) Maxi maal beschikbaar en betrouwbaar is
* U hebt transactionele garanties nodig in meerdere status eenheden (bijvoorbeeld orders en order regel items).
* De status van uw toepassing kan natuurlijk worden gemodelleerd als betrouw bare woorden lijsten en wacht rijen.
* Uw toepassings code of-status moet Maxi maal beschikbaar zijn met lees-en schrijf bewerkingen met een lage latentie.
* Uw toepassing moet de gelijktijdigheid of granulatie van transactionele bewerkingen in een of meer betrouw bare verzamelingen beheren.
* U wilt de communicatie beheren of het partitie schema voor uw service regelen.
* Voor uw code is een gratis threaded runtime-omgeving vereist.
* Uw toepassing moet op dynamische wijze betrouw bare woorden lijsten of wacht rijen of volledige services maken of vernietigen tijdens runtime.
* U moet op een programmatische manier de Service Fabric back-up-en herstel functies voor de status van uw service beheren.
* Uw toepassing moet de wijzigings geschiedenis voor de status eenheden behouden.
* U wilt aangepaste status providers van derden ontwikkelen of gebruiken.

## <a name="next-steps"></a>Volgende stappen
* [Reliable Services snel starten](service-fabric-reliable-services-quick-start.md)
* [Betrouw bare verzamelingen](service-fabric-reliable-services-reliable-collections.md)
* [Het Reliable Actors-programmeer model](service-fabric-reliable-actors-introduction.md)
