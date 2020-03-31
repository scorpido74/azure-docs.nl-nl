---
title: Acteurs diagnostiek en monitoring
description: In dit artikel worden de functies voor diagnostische en prestatiebewaking beschreven in de runtime van servicefabric betrouwbare actoren, inclusief de gebeurtenissen en prestatiemeteritems die door de functie worden uitgezonden.
author: abhishekram
ms.topic: conceptual
ms.date: 10/26/2017
ms.author: abhisram
ms.openlocfilehash: e6e9fb66368461e0d3ebdd2709f4ced0e796bea5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282326"
---
# <a name="diagnostics-and-performance-monitoring-for-reliable-actors"></a>Diagnose- en prestatiecontrole voor betrouwbare actoren
De runtime van Betrouwbare actoren zendt [EventSource-gebeurtenissen](https://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource.aspx) en [prestatiemeteritems](https://msdn.microsoft.com/library/system.diagnostics.performancecounter.aspx)uit. Deze bieden inzicht in hoe de runtime werkt en helpen bij het oplossen van problemen en prestatiebewaking.

## <a name="eventsource-events"></a>EventSource-gebeurtenissen
De naam van de EventSource-provider voor de runtime van Betrouwbare Actoren is "Microsoft-ServiceFabric-Actors". Gebeurtenissen uit deze gebeurtenisbron worden weergegeven in het venster [Diagnostische gebeurtenissen](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md#view-service-fabric-system-events-in-visual-studio) wanneer de actortoepassing wordt verwijderd in [Visual Studio](service-fabric-debugging-your-application.md).

Voorbeelden van hulpprogramma's en technologieën die helpen bij het verzamelen en/of bekijken van EventSource-gebeurtenissen zijn [PerfView,](https://www.microsoft.com/download/details.aspx?id=28567) [Azure Diagnostics,](../cloud-services/cloud-services-dotnet-diagnostics.md) [Semantic Logging](https://msdn.microsoft.com/library/dn774980.aspx)en de [Microsoft TraceEvent-bibliotheek](https://www.nuget.org/packages/Microsoft.Diagnostics.Tracing.TraceEvent).

### <a name="keywords"></a>Trefwoorden
Alle gebeurtenissen die behoren tot de Gebeurtenisbron Betrouwbare Actoren zijn gekoppeld aan een of meer zoekwoorden. Dit maakt het mogelijk om gebeurtenissen die worden verzameld te filteren. De volgende trefwoordbits worden gedefinieerd.

| Beetje | Beschrijving |
| --- | --- |
| 0x1 |Reeks belangrijke gebeurtenissen die de werking van de runtime van de Actoren van de Stof samenvatten. |
| 0x2 |Set gebeurtenissen die aanroepen naar actormethoden beschrijven. Zie voor meer informatie het [inleidende onderwerp over acteurs.](service-fabric-reliable-actors-introduction.md) |
| 0x4 |Reeks gebeurtenissen met betrekking tot de actorstatus. Zie voor meer informatie het onderwerp [actor state management](service-fabric-reliable-actors-state-management.md). |
| 0x8 |Reeks gebeurtenissen met betrekking tot turn-based gelijktijdigheid in de actor. Zie voor meer informatie het onderwerp [gelijktijdigheid](service-fabric-reliable-actors-introduction.md#concurrency). |

## <a name="performance-counters"></a>Prestatiemeteritems
De runtime van Betrouwbare actoren definieert de volgende categorieën van de prestatiesteller.

| Categorie | Beschrijving |
| --- | --- |
| De Acteur van de Stof van de dienst |Tellers die specifiek zijn voor Azure Service Fabric-actoren, bijvoorbeeld de tijd die nodig is om de actorstatus op te slaan |
| Methode voor actor van servicestof |Tellers die specifiek zijn voor methoden die worden geïmplementeerd door actoren van Service Fabric, bijvoorbeeld hoe vaak een actormethode wordt aangeroepen |

Elk van de bovenstaande categorieën heeft een of meer tellers.

De [Windows Performance Monitor-toepassing](https://technet.microsoft.com/library/cc749249.aspx) die standaard beschikbaar is in het Windows-besturingssysteem, kan worden gebruikt om prestatiemetergegevens te verzamelen en weer te geven. [Azure Diagnostics](../cloud-services/cloud-services-dotnet-diagnostics.md) is een andere optie voor het verzamelen van prestatiemetergegevens en het uploaden naar Azure-tabellen.

### <a name="performance-counter-instance-names"></a>Instantienamen voor prestatiemeter
Een cluster met een groot aantal actorservices of actorservicepartities heeft een groot aantal actorprestatiemeterinstanties. De instantienamen van de prestatieteller kunnen helpen bij het identificeren van de specifieke [partitie-](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) en actormethode (indien van toepassing) waarmee de instantie van het prestatiemeterrecht is gekoppeld.

#### <a name="service-fabric-actor-category"></a>Categorie Service Fabric Actor
Voor de `Service Fabric Actor`categorie bevinden de namen van de tellerinstantie zich in de volgende indeling:

`ServiceFabricPartitionID_ActorsRuntimeInternalID`

*ServiceFabricPartitionID* is de tekenreeksweergave van de servicefabricpartitie-id waarmee de instantie van de prestatieteller is gekoppeld. De partitie-id is een GUID en de [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) tekenreeksweergave wordt gegenereerd via de methode met indelingsaanduiding "D".

*ActorRuntimeInternalID* is de tekenreeksweergave van een 64-bits geheelgetal dat wordt gegenereerd door de runtime van Fabric Actors voor intern gebruik. Dit is opgenomen in de naam van de instantie van de prestatieteller om de uniciteit ervan te garanderen en conflicten met andere namen van prestatiemeteritems te voorkomen. Gebruikers moeten niet proberen om dit gedeelte van de naam van de prestatiemeterinstantie te interpreteren.

Het volgende is een voorbeeld van een naam van `Service Fabric Actor` een tellerinstantie voor een teller die tot de categorie behoort:

`2740af29-78aa-44bc-a20b-7e60fb783264_635650083799324046`

In het bovenstaande `2740af29-78aa-44bc-a20b-7e60fb783264` voorbeeld is de tekenreeksweergave van `635650083799324046` de servicefabricpartitie-id en de 64-bits ID die wordt gegenereerd voor intern gebruik van de runtime.

#### <a name="service-fabric-actor-method-category"></a>Categorie Actor-methode voor servicestof
Voor de `Service Fabric Actor Method`categorie bevinden de namen van de tellerinstantie zich in de volgende indeling:

`MethodName_ActorsRuntimeMethodId_ServiceFabricPartitionID_ActorsRuntimeInternalID`

*MethodName* is de naam van de actormethode waarmee de instantie van het prestatiemeterrecht is gekoppeld. De indeling van de methodenaam wordt bepaald op basis van enige logica in de runtime Van Fabric Actors die de leesbaarheid van de naam in evenwicht brengt met beperkingen op de maximale lengte van de instantienamen van de prestatiemeter in Windows.

*ActorsRuntimeMethodId* is de snaarweergave van een 32-bits geheelgetal dat wordt gegenereerd door de Fabric Actors runtime voor intern gebruik. Dit is opgenomen in de naam van de instantie van de prestatieteller om de uniciteit ervan te garanderen en conflicten met andere namen van prestatiemeteritems te voorkomen. Gebruikers moeten niet proberen om dit gedeelte van de naam van de prestatiemeterinstantie te interpreteren.

*ServiceFabricPartitionID* is de tekenreeksweergave van de servicefabricpartitie-id waarmee de instantie van de prestatieteller is gekoppeld. De partitie-id is een GUID en de [`Guid.ToString`](https://msdn.microsoft.com/library/97af8hh4.aspx) tekenreeksweergave wordt gegenereerd via de methode met indelingsaanduiding "D".

*ActorRuntimeInternalID* is de tekenreeksweergave van een 64-bits geheelgetal dat wordt gegenereerd door de runtime van Fabric Actors voor intern gebruik. Dit is opgenomen in de naam van de instantie van de prestatieteller om de uniciteit ervan te garanderen en conflicten met andere namen van prestatiemeteritems te voorkomen. Gebruikers moeten niet proberen om dit gedeelte van de naam van de prestatiemeterinstantie te interpreteren.

Het volgende is een voorbeeld van een naam van `Service Fabric Actor Method` een tellerinstantie voor een teller die tot de categorie behoort:

`ivoicemailboxactor.leavemessageasync_2_89383d32-e57e-4a9b-a6ad-57c6792aa521_635650083804480486`

In het bovenstaande `ivoicemailboxactor.leavemessageasync` voorbeeld is `2` de methodenaam, is de 32-bits ID `89383d32-e57e-4a9b-a6ad-57c6792aa521` die wordt gegenereerd voor intern gebruik `635650083804480486` van de runtime, is de tekenreeksweergave van de servicefabricpartitie-id en is de 64-bits ID die wordt gegenereerd voor intern gebruik van de runtime.

## <a name="list-of-events-and-performance-counters"></a>Lijst met gebeurtenissen en prestatiemeters
### <a name="actor-method-events-and-performance-counters"></a>Gebeurtenissen voor actormethoden en prestatiemeteritems
De Runtime Van betrouwbare actoren zendt de volgende gebeurtenissen uit met betrekking tot [actormethoden.](service-fabric-reliable-actors-introduction.md)

| Gebeurtenisnaam | Gebeurtenis-id | Niveau | Trefwoord | Beschrijving |
| --- | --- | --- | --- | --- |
| ActorMethodStart (ActorMethodStart) |7 |Verbose |0x2 |Acteurs runtime staat op het punt om een actor methode aan te roepen. |
| ActorMethodStop |8 |Verbose |0x2 |Een actormethode is voltooid. Dat wil zeggen dat de asynchrone aanroep van de runtime naar de actormethode is teruggekeerd en de taak die is geretourneerd met de actormethode is voltooid. |
| ActorMethodThrewException ActorMethodThrewException ActorMethodThrewException ActorMethod |9 |Waarschuwing |0x3 |Er is een uitzondering gemaakt tijdens de uitvoering van een actormethode, tijdens de asynchrone aanroep van de runtime naar de actormethode of tijdens de uitvoering van de taak die door de actormethode wordt geretourneerd. Deze gebeurtenis duidt op een soort fout in de actorcode die moet worden onderzocht. |

De Reliable Actors runtime publiceert de volgende performance tellers met betrekking tot de uitvoering van actor methoden.

| Category name | Naam van het meteritem | Beschrijving |
| --- | --- | --- |
| Methode voor actor van servicestof |Aanroepingen/Sec |Aantal keren dat de actorservicemethode per seconde wordt ingeroepen |
| Methode voor actor van servicestof |Gemiddelde milliseconden per aanroep |Tijd die nodig is om de actorservicemethode in milliseconden uit te voeren |
| Methode voor actor van servicestof |Uitzonderingen gegooid/Sec |Aantal keren dat de actorservicemethode een uitzondering per seconde heeft gegeven |

### <a name="concurrency-events-and-performance-counters"></a>Gelijktijdigheidsgebeurtenissen en prestatiemeteritems
De runtime van betrouwbare actoren zendt de volgende gebeurtenissen uit met betrekking tot [gelijktijdigheid](service-fabric-reliable-actors-introduction.md#concurrency).

| Gebeurtenisnaam | Gebeurtenis-id | Niveau | Trefwoord | Beschrijving |
| --- | --- | --- | --- | --- |
| ActorMethodcallswaitingforlock |12 |Verbose |0x8 |Deze gebeurtenis wordt geschreven aan het begin van elke nieuwe beurt in een acteur. Het bevat het aantal lopende actoroproepen die wachten om het slot per actor te verwerven dat turn-based gelijktijdigheid afdwingt. |

De Reliable Actors runtime publiceert de volgende performance tellers met betrekking tot gelijktijdigheid.

| Category name | Naam van het meteritem | Beschrijving |
| --- | --- | --- |
| De Acteur van de Stof van de dienst |# van acteur roept te wachten op acteur slot |Aantal lopende actoroproepen die wachten om het slot per actor te verwerven dat turn-based gelijktijdigheid afdwingt |
| De Acteur van de Stof van de dienst |Gemiddelde milliseconden per slot wachten |Tijd die nodig is (in milliseconden) om het slot per actor te verkrijgen dat turn-based gelijktijdigheid afdwingt |
| De Acteur van de Stof van de dienst |Gemiddelde milliseconden acteur slot gehouden |Tijd (in milliseconden) waarvoor het slot per actor wordt vastgehouden |

### <a name="actor-state-management-events-and-performance-counters"></a>Gebeurtenissen en prestatiemeteritems voor actorstatemanagement
De Betrouwbare Acteurs runtime zendt de volgende gebeurtenissen met betrekking tot [actor state management](service-fabric-reliable-actors-state-management.md).

| Gebeurtenisnaam | Gebeurtenis-id | Niveau | Trefwoord | Beschrijving |
| --- | --- | --- | --- | --- |
| ActorSaveStateStart |10 |Verbose |0x4 |Acteurs runtime staat op het punt om de acteur staat te redden. |
| ActorSaveStateStop |11 |Verbose |0x4 |Acteurs runtime is klaar met het opslaan van de acteur staat. |

De Betrouwbare Actoren runtime publiceert de volgende prestatiestellers met betrekking tot actor staatsbeheer.

| Category name | Naam van het meteritem | Beschrijving |
| --- | --- | --- |
| De Acteur van de Stof van de dienst |Gemiddelde milliseconden per opslagstatusbewerking |Tijd die nodig is om de actorstatus in milliseconden op te slaan |
| De Acteur van de Stof van de dienst |Gemiddelde milliseconden per bewerking van de belastingsstatus |Tijd die nodig is om de actorstatus in milliseconden te laden |

### <a name="events-related-to-actor-replicas"></a>Gebeurtenissen met betrekking tot actorreplica's
De runtime van betrouwbare acteurs zendt de volgende gebeurtenissen uit met betrekking tot [actorreplica's.](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors)

| Gebeurtenisnaam | Gebeurtenis-id | Niveau | Trefwoord | Beschrijving |
| --- | --- | --- | --- | --- |
| ReplicaChangeRoleToPrimary |1 |Informatief |0x1 |Actorreplica heeft de rol gewijzigd in Primair. Dit houdt in dat de actoren voor deze partitie zullen worden gemaakt in deze replica. |
| ReplicachangeroleFromPrimary |2 |Informatief |0x1 |Actorreplica heeft de rol gewijzigd in niet-primair. Dit houdt in dat de actoren voor deze partitie niet langer worden gemaakt in deze replica. Er worden geen nieuwe aanvragen geleverd aan actoren die al in deze replica zijn gemaakt. De acteurs worden vernietigd nadat alle lopende verzoeken zijn voltooid. |

### <a name="actor-activation-and-deactivation-events-and-performance-counters"></a>Gebeurtenissen en prestatiemeteritems voor actoractivering en -deactivering
De runtime van betrouwbare acteurs zendt de volgende gebeurtenissen uit met betrekking tot [activering en deactivering van actors.](service-fabric-reliable-actors-lifecycle.md)

| Gebeurtenisnaam | Gebeurtenis-id | Niveau | Trefwoord | Beschrijving |
| --- | --- | --- | --- | --- |
| ActorActivated |5 |Informatief |0x1 |Een acteur is geactiveerd. |
| ActorGedeactiveerd |6 |Informatief |0x1 |Een acteur is gedeactiveerd. |

De Reliable Actors runtime publiceert de volgende performance tellers met betrekking tot actor activatie en deactivering.

| Category name | Naam van het meteritem | Beschrijving |
| --- | --- | --- |
| De Acteur van de Stof van de dienst |Gemiddelde onactivateasync milliseconden |Tijd die nodig is om OnActivateAsync-methode in milliseconden uit te voeren |

### <a name="actor-request-processing-performance-counters"></a>Prestatiemeteritems voor actoraanvragen
Wanneer een client een methode aanroept via een proxyobject voor actor' s, resulteert dit in een verzoekbericht dat via het netwerk naar de actorservice wordt verzonden. De service verwerkt het verzoekbericht en stuurt een antwoord terug naar de client. De Reliable Actors runtime publiceert de volgende performance tellers met betrekking tot actor request processing.

| Category name | Naam van het meteritem | Beschrijving |
| --- | --- | --- |
| De Acteur van de Stof van de dienst |Aantal openstaande verzoeken |Aantal aanvragen dat in de service wordt verwerkt |
| De Acteur van de Stof van de dienst |Gemiddelde milliseconden per aanvraag |Tijd die de service nodig heeft om een aanvraag te verwerken (in milliseconden) |
| De Acteur van de Stof van de dienst |Gemiddelde milliseconden voor aanvraagdeserialisatie |Tijd die nodig is (in milliseconden) om actor request bericht te deserialiseren wanneer het wordt ontvangen bij de service |
| De Acteur van de Stof van de dienst |Gemiddelde milliseconden voor responsserialisatie |Tijd die nodig is (in milliseconden) om het actorreactiebericht bij de service te serialiseren voordat het antwoord naar de client wordt verzonden |

## <a name="next-steps"></a>Volgende stappen
* [Hoe betrouwbare actoren het Service Fabric-platform gebruiken](service-fabric-reliable-actors-platform.md)
* [Actor API-referentiedocumentatie](https://msdn.microsoft.com/library/azure/dn971626.aspx)
* [Voorbeeldcode](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [EventSource-providers in PerfView](https://blogs.msdn.microsoft.com/vancem/2012/07/09/introduction-tutorial-logging-etw-events-in-c-system-diagnostics-tracing-eventsource/)
