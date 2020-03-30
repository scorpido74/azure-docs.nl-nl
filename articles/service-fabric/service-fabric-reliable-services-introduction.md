---
title: Overzicht van het programmeermodel Betrouwbare Service
description: Lees meer over het programmamodel van Service Fabric Reliable Service en ga aan de slag met het schrijven van uw eigen services.
author: masnider
ms.topic: conceptual
ms.date: 3/9/2018
ms.author: masnider
ms.custom: sfrev
ms.openlocfilehash: 58259b0d19d68c468779a579bd9c86e77106c18d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77083515"
---
# <a name="reliable-services-overview"></a>Overzicht van Reliable Services

Azure Service Fabric vereenvoudigt het schrijven en beheren van stateless en stateful services. In dit onderwerp komt het volgende aan de orde:

* Het programmeermodel Reliable Services voor stateless en stateful services.
* De keuzes die je moet maken bij het schrijven van een betrouwbare service.
* Enkele scenario's en voorbeelden van wanneer betrouwbare services moeten worden gebruikt en hoe ze zijn geschreven.

*Reliable Services* is een van de programmeermodellen die beschikbaar zijn op Service Fabric. Een andere is de *Reliable Actor* programmeermodel, die een [Virtual Actor](https://research.microsoft.com/en-us/projects/orleans/) applicatie kader biedt op de top van de Reliable Services model. Voor meer informatie over betrouwbare actoren, zie [Inleiding tot Service Fabric Betrouwbare Actoren](service-fabric-reliable-actors-introduction.md).

Service Fabric beheert de levensduur van services, van inrichting en implementatie via upgrade en verwijdering, via [Service Fabric-applicatiebeheer.](service-fabric-deploy-remove-applications.md)

## <a name="what-are-reliable-services"></a>Wat zijn betrouwbare services

Reliable Services biedt u een eenvoudig, krachtig, top-level programmeermodel om u te helpen uitdrukken wat belangrijk is voor uw toepassing. Met het programmeermodel Reliable Services krijgt u:

* Toegang tot API's voor servicefabric. In tegenstelling tot Service Fabric-services die zijn gemodelleerd als [Gastuitvoerbare services,](service-fabric-guest-executables-introduction.md)kunnen betrouwbare services direct gebruikmaken van Service Fabric API's. Hierdoor kunnen services:
  * Een query uitvoeren op het systeem
  * Status rapporteren over entiteiten in het cluster
  * Meldingen ontvangen over configuratie- en codewijzigingen
  * Zoeken en communiceren met andere diensten,
  * Gebruik de [betrouwbare collecties](service-fabric-reliable-services-reliable-collections.md)
  * Krijg toegang tot vele andere mogelijkheden, allemaal vanuit een eersteklas programmeermodel in verschillende programmeertalen.
* Een eenvoudig model voor het uitvoeren van uw eigen code die aanvoelt als andere bekende programmeermodellen. Uw code heeft een goed gedefinieerd toegangspunt en eenvoudig beheerde levenscyclus.
* Een pluggable communicatiemodel. Gebruik het transport van uw keuze, zoals HTTP met [Web API,](service-fabric-reliable-services-communication-webapi.md)WebSockets, aangepaste TCP-protocollen of iets anders. Betrouwbare services bieden een aantal geweldige kant-en-klare opties die u gebruiken, of u uw eigen.
* Voor stateful services u met het programmeermodel Reliable Services uw status consistent en betrouwbaar binnen uw service opslaan met behulp van [betrouwbare collecties.](service-fabric-reliable-services-reliable-collections.md) Betrouwbare collecties zijn een eenvoudige set van zeer beschikbare en betrouwbare collectie klassen die bekend zal zijn voor iedereen die heeft gebruikt C # collecties. Traditioneel, diensten nodig externe systemen voor betrouwbare staatsbeheer. Met betrouwbare collecties u uw status naast uw computeropslaan met dezelfde hoge beschikbaarheid en betrouwbaarheid die u gewend bent van zeer beschikbare externe winkels. Dit model verbetert ook de latentie omdat u de rekenkracht en status die moet werken, co-locatt.

## <a name="what-makes-reliable-services-different"></a>Wat maakt Betrouwbare Diensten anders

Betrouwbare services verschillen van services die u eerder hebt geschreven, omdat Service Fabric biedt:

* **Betrouwbaarheid** - Uw service blijft omhoog, zelfs in onbetrouwbare omgevingen waar uw machines uitvallen of netwerkproblemen raken, of in gevallen waarin de services zelf fouten tegenkomen en crashen of falen. Voor stateful services blijft uw status behouden, zelfs in aanwezigheid van netwerk- of andere fouten.
* **Beschikbaarheid** - Uw service is bereikbaar en responsief. Service Fabric behoudt het gewenste aantal lopende exemplaren.
* **Schaalbaarheid** - Services worden losgekoppeld van specifieke hardware en kunnen zo nodig groeien of krimpen door het toevoegen of verwijderen van hardware of andere resources. Services kunnen eenvoudig worden verdeeld (vooral in het geval van stateful) om ervoor te zorgen dat de service gedeeltelijke fouten kan schalen en verwerken. Services kunnen dynamisch worden gemaakt en verwijderd via code, waardoor meer exemplaren zo nodig kunnen worden gesponnen, bijvoorbeeld in antwoord op verzoeken van klanten. Tot slot moedigt Service Fabric services aan om licht van gewicht te zijn. Met Service Fabric kunnen duizenden services binnen één proces worden ingericht, in plaats van volledige OS-exemplaren of -processen te vereisen of te wijden aan één exemplaar van een service.
* **Consistentie** - Alle informatie die is opgeslagen in een betrouwbare service kan worden gegarandeerd consistent te zijn. Dit geldt zelfs voor meerdere betrouwbare collecties binnen een service. Wijzigingen tussen verzamelingen binnen een service kunnen op een transactioneel atomaire manier worden aangebracht.

## <a name="service-lifecycle"></a>Levenscyclus van services

Of uw service nu stateful of stateloos is, Reliable Services biedt een eenvoudige levenscyclus waarmee u snel uw code aansluiten en aan de slag.  Als u een nieuwe service operationeel maakt, moet u twee methoden implementeren:

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** - Met deze methode definieert de service de communicatiestack(s) die deze wil gebruiken. De communicatiestack, zoals [Web API,](service-fabric-reliable-services-communication-webapi.md)is wat het luistereindpunt of eindpunten voor de service definieert (hoe clients de service bereiken). Het definieert ook hoe de berichten die worden weergegeven, communiceren met de rest van de servicecode.
* **RunAsync** - Deze methode is waar uw service de bedrijfslogica uitvoert en waar deze alle achtergrondtaken start die moeten worden uitgevoerd voor de levensduur van de service. Het annuleringstoken dat wordt geleverd is een signaal voor wanneer dat werk moet stoppen. Als de service bijvoorbeeld berichten uit een betrouwbare wachtrij moet halen en verwerken, gebeurt dat werk hier.

Als u voor het eerst leert over betrouwbare services, lees dan verder! Als u op zoek bent naar een gedetailleerde doorloop van de levenscyclus van betrouwbare services, raadpleegt u het overzicht van de [levenscyclus van Reliable Services.](service-fabric-reliable-services-lifecycle.md)

## <a name="example-services"></a>Voorbeeldservices

Laten we eens kijken hoe het Reliable Services-model werkt met zowel stateloze als stateful services.

### <a name="stateless-reliable-services"></a>Staatloze betrouwbare services

Een *stateless service* is een service waarbij er geen status wordt onderhouden binnen de service voor gesprekken. Elke status die aanwezig is, is volledig beschikbaar en vereist geen synchronisatie, replicatie, persistentie of hoge beschikbaarheid.

Denk bijvoorbeeld aan een rekenmachine die geen geheugen heeft en alle termen en bewerkingen ontvangt om in één keer uit te voeren.

In dit geval `RunAsync()` kan de `runAsync()` (C#) of (Java) van de service leeg zijn, omdat er geen achtergrondtaakverwerking is die de service moet uitvoeren. Wanneer de rekenmachineservice wordt gemaakt, retourneert deze een `ICommunicationListener` (C#) of `CommunicationListener` (Java) (bijvoorbeeld Web [API)](service-fabric-reliable-services-communication-webapi.md)die een luistereindpunt opent op een bepaalde poort. Dit luistereindpunt sluit aan bij de verschillende berekeningsmethoden (bijvoorbeeld "Toevoegen(n1, n2)") die de openbare API van de rekenmachine definiëren.

Wanneer een aanroep wordt gedaan vanuit een client, wordt de juiste methode aangeroepen en voert de rekenmachineservice de bewerkingen uit op de verstrekte gegevens en retourneert het resultaat. Het slaat geen staat op.

Als u geen interne status opslaat, wordt deze voorbeeldcalculator eenvoudig. Maar de meeste diensten zijn niet echt stateloos. In plaats daarvan externaliseren ze hun status naar een andere winkel. (Elke web-app die bijvoorbeeld afhankelijk is van het bijhouden van de sessiestatus in een back-upopslag of cache, is niet stateloos.)

Een veelvoorkomend voorbeeld van hoe stateloze services worden gebruikt in Service Fabric is als een front-end die de openbare API voor een webtoepassing blootlegt. De front-end service praat vervolgens met stateful services om een gebruikersverzoek te voltooien. In dit geval worden oproepen van clients doorgestuurd naar een bekende poort, zoals 80, waar de staatloze service luistert. Deze staatloze service ontvangt de oproep en bepaalt of het gesprek afkomstig is van een vertrouwde partij en voor welke service deze is bestemd.  Vervolgens stuurt de stateless service de oproep door naar de juiste partitie van de stateful service en wacht op een antwoord. Wanneer de statusloze service een antwoord ontvangt, wordt deze beantwoord aan de oorspronkelijke client. Een voorbeeld van een dergelijke service is de *Service Fabric Getting Started* sample[(C#](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started) / [Java),](https://github.com/Azure-Samples/service-fabric-java-getting-started)onder andere Service Fabric monsters in die repo.

### <a name="stateful-reliable-services"></a>Stateful Betrouwbare Diensten

Een *stateful service* is er een die moet een deel van de staat consistent en aanwezig gehouden om voor de dienst te laten functioneren. Overweeg een service die voortdurend een voortschrijdend gemiddelde van een bepaalde waarde berekent op basis van updates die deze ontvangt. Om dit te doen, moet het de huidige set van inkomende aanvragen die het moet verwerken en het huidige gemiddelde hebben. Elke service die informatie ophaalt, verwerkt en opslaat in een extern archief (zoals een Azure-blob of tabelarchief vandaag) is stateful. Het houdt gewoon zijn staat in de externe staat op te slaan.

De meeste services slaan hun status tegenwoordig extern op, omdat de externe winkel betrouwbaarheid, beschikbaarheid, schaalbaarheid en consistentie biedt voor die status. In Service Fabric zijn services niet verplicht om hun status extern op te slaan. Service Fabric zorgt voor deze vereisten voor zowel de servicecode als de servicestatus.

Laten we zeggen dat we een service willen schrijven die afbeeldingen verwerkt. Om dit te doen, neemt de service een afbeelding en de reeks conversies in zich op die afbeelding. Deze service retourneert een communicatielistener (laten we aannemen dat het `ConvertImage(Image i, IList<Conversion> conversions)`een WebAPI is) die een API als blootlegt. Wanneer de service een aanvraag ontvangt, `IReliableQueue`slaat deze op in een , en stuurt deze een identiteitsbewijs terug naar de client, zodat deze de aanvraag kan volgen.

In deze `RunAsync()` dienst, kan complexer zijn. De service heeft een `RunAsync()` lus in `IReliableQueue` de service die aanvragen uit trekt en de gevraagde conversies uitvoert. De resultaten worden `IReliableDictionary` opgeslagen in een, zodat wanneer de klant terug komt ze hun geconverteerde beelden kunnen krijgen. Om ervoor te zorgen dat zelfs als er iets niet verloren gaat, deze betrouwbare service zich uit de wachtrij trekt, de conversies uitvoert en het resultaat in één transactie opslaat. In dit geval wordt het bericht uit de wachtrij verwijderd en worden de resultaten alleen opgeslagen in het resultaatwoordenboek wanneer de conversies zijn voltooid. Als alternatief kan de service de afbeelding uit de wachtrij trekken en deze onmiddellijk opslaan in een externe winkel. Dit vermindert de hoeveelheid status die de service moet beheren, maar verhoogt de complexiteit omdat de service de benodigde metagegevens moet behouden om de externe winkel te beheren. Bij beide benaderingen blijft de aanvraag in de wachtrij staan om te worden verwerkt als er iets in het midden is mislukt.

Hoewel deze service klinkt als een typische .NET-service, is`IReliableQueue` `IReliableDictionary`het verschil dat de gegevensstructuren die worden gebruikt (en ) worden geleverd door Service Fabric en zeer betrouwbaar, beschikbaar en consistent zijn.

## <a name="when-to-use-reliable-services-apis"></a>Wanneer moeten api's van betrouwbare services worden gebruikt

Overweeg betrouwbare services API's als:

* U wilt dat de code van uw service (en optioneel staat) zeer beschikbaar en betrouwbaar is.
* U hebt transactionele garanties nodig voor meerdere staatseenheden (bijvoorbeeld orders en orderregelitems).
* De status van uw toepassing kan natuurlijk worden gemodelleerd als betrouwbare woordenboeken en wachtrijen.
* Uw toepassingen code of status moet zeer beschikbaar zijn met lage latentie leest en schrijft.
* Uw toepassing moet de gelijktijdigheid of granulariteit van transacties in een of meer betrouwbare verzamelingen beheren.
* U wilt de communicatie beheren of het partitieschema voor uw service beheren.
* Uw code heeft een free-threaded runtime-omgeving nodig.
* Uw toepassing moet dynamisch betrouwbare woordenboeken of wachtrijen of hele services maken of vernietigen tijdens runtime.
* U moet de door Service Fabric geleverde back-up- en herstelfuncties voor de status van uw service programmatisch beheren.
* Uw toepassing moet de wijzigingsgeschiedenis voor de statuseenheden bijhouden.
* U wilt externe, op maat gemaakte overheidsproviders ontwikkelen of consumeren.

## <a name="next-steps"></a>Volgende stappen

* [Betrouwbare services snel van start](service-fabric-reliable-services-quick-start.md)
* [Betrouwbare collecties](service-fabric-reliable-services-reliable-collections.md)
* [Het programmeermodel Betrouwbare Actoren](service-fabric-reliable-actors-introduction.md)
