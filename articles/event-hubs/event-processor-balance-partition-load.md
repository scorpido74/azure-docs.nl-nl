---
title: De verdeling van partities verdelen over meerdere exemplaren-Azure Event Hubs | Microsoft Docs
description: Hierin wordt beschreven hoe u de belasting van partities op meerdere exemplaren van uw toepassing kunt verdelen met behulp van een gebeurtenis processor en de Azure Event Hubs SDK.
services: event-hubs
documentationcenter: .net
author: ShubhaVijayasarathy
editor: ''
ms.service: event-hubs
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/16/2020
ms.author: shvija
ms.openlocfilehash: 1244fe64d0c23782fdae7a0f92415bada4bef55a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/31/2020
ms.locfileid: "76907654"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>De verdeling van partities verdelen over meerdere exemplaren van uw toepassing
U kunt de toepassing voor het verwerken van gebeurtenissen schalen door meerdere exemplaren van de toepassing uit te voeren en de belasting tussen zichzelf te verdelen. In de oudere versies heeft [EventProcessorHost](event-hubs-event-processor-host.md) u de mogelijkheid om de belasting te verdelen tussen meerdere exemplaren van uw programma en controlepunt gebeurtenissen tijdens ontvangst. In de nieuwere versies (5,0 en hoger), **EventProcessorClient** (.net en Java) of **EventHubConsumerClient** (python en Java script) kunt u hetzelfde doen. Het ontwikkelings model wordt eenvoudiger gemaakt met behulp van gebeurtenissen. U abonneert u op de gebeurtenissen waarin u bent geïnteresseerd door een gebeurtenis-handler te registreren.

In dit artikel wordt een voorbeeld scenario beschreven voor het gebruik van meerdere instanties voor het lezen van gebeurtenissen van een Event Hub. vervolgens krijgt u informatie over de functies van de client voor gebeurtenis processor, waarmee u gebeurtenissen van meerdere partities tegelijk kunt ontvangen en de taak verdeling met andere consumenten die gebruikmaken van dezelfde Event Hub en consumenten groep.

> [!NOTE]
> De sleutel worden geschaald voor Event Hubs is het idee van de gepartitioneerde consumenten. In tegenstelling tot de [concurrerende consumenten](https://msdn.microsoft.com/library/dn568101.aspx) patroon, de basis van gepartitioneerd gebruik patroon kan grote schaal door te verwijderen van het knelpunt conflicten en end-to-end parallelle uitvoering te vergemakkelijken.

## <a name="example-scenario"></a>Voorbeeldscenario

Een voorbeeldscenario, kunt u beter een beveiliging voor thuis-bedrijf dat 100.000 huisadressen bewaakt. Elke minuut haalt gegevens op uit verschillende Sens oren, zoals een bewegings detector, een deur/venster open sensor, glas afbreek detector, enzovoort, geïnstalleerd in elk thuis netwerk. Het bedrijf biedt een website voor inwoners van de voor het bewaken van de activiteit van de startpagina in bijna realtime.

Elke sensor pushes gegevens naar een event hub. De event hub is geconfigureerd met 16 partities. Op het verbruiks einde hebt u een mechanisme nodig dat deze gebeurtenissen kan lezen, consolideert (filter, aggregatie, enzovoort) en de aggregatie naar een opslag-Blob kan dumpen, die vervolgens wordt geprojecteerd op een gebruiks vriendelijke webpagina.

## <a name="write-the-consumer-application"></a>De consument toepassing schrijven

Bij het ontwerpen van de gebruiker in een gedistribueerde omgeving, moet het scenario ingang in de volgende vereisten:

1. **Schaal:** meerdere consumenten worden gedistribueerd, met elke consument eigenaar van het lezen van een paar Event Hubs-partities te maken.
2. **Verdeel de belasting van:** vergroten of verkleinen van de consumenten dynamisch. Bijvoorbeeld, wanneer een nieuw sensortype (bijvoorbeeld een koolmonoxide detector) wordt toegevoegd aan elke start, verhoogt het aantal gebeurtenissen. In dat geval wordt het aantal consumentexemplaren wordt verhoogd in de operator (een mens). Vervolgens, in de groep consumenten herverdelen van het aantal partities ze eigenaar zijn, voor het delen van de belasting met de zojuist toegevoegde consumenten.
3. **Naadloos hervatten bij fouten:** Als een Consumer (**Consumer a**) niet kan worden uitgevoerd (bijvoorbeeld de virtuele machine die de consument als host heeft van een storing), kunnen andere gebruikers de partities ophalen die eigendom zijn van **Consumer a** en door gaan. Ook het vervolg-punt met de naam een *controlepunt* of *offset*, moet op het exacte punt waarop **consument A** is mislukt, of iets vóór die.
4. **Gebeurtenissen gebruiken:** Hoewel de voor gaande drie punten van toepassing zijn op het beheer van de gebruiker, moet er code zijn om de gebeurtenissen te gebruiken en iets nuttig te doen. U kunt deze bijvoorbeeld samen voegen en uploaden naar Blob Storage.

## <a name="event-processor-or-consumer-client"></a>Gebeurtenis processor of consumenten client

U hoeft niet uw eigen oplossing te bouwen om aan deze vereisten te voldoen. De Azure Event Hubs Sdk's bieden deze functionaliteit. In .NET-of Java-Sdk's gebruikt u een event processor-client (EventProcessorClient) en in Python-en Java script-Sdk's gebruikt u EventHubConsumerClient. In de oude versie van SDK was het event processor host (EventProcessorHost) die deze functies ondersteunt.

Voor het meren deel van de productie scenario's wordt u aangeraden de gebeurtenis verwerker-client te gebruiken voor het lezen en verwerken van gebeurtenissen. De processor-client is bedoeld om een robuuste ervaring te bieden voor het verwerken van gebeurtenissen voor alle partities van een Event Hub in een uitvoerings-en fout tolerante manier, terwijl u een manier hebt om de voortgang ervan te controlepunt. Client voor gebeurtenis processoren kunnen ook samen werken in de context van een Consumer groep voor een bepaalde Event Hub. Clients beheren automatisch distributie en taak verdeling als instanties beschikbaar of niet beschikbaar zijn voor de groep.

## <a name="partition-ownership-tracking"></a>Partitie eigendom bijhouden

Een gebeurtenis processor instantie is doorgaans eigenaar en verwerkt gebeurtenissen van een of meer partities. Het eigendom van partities wordt gelijkmatig verdeeld over alle actieve gebeurtenis processor instanties die zijn gekoppeld aan een combi natie van Event Hub en Consumer groep. 

Elke gebeurtenis processor krijgt een unieke id en eigendom van de claims van partities door een vermelding toe te voegen aan of bij te werken in een controlepunt opslag. Alle gebeurtenis verwerkers communiceren periodiek met deze Store om de eigen verwerkings status bij te werken en om meer te weten te komen over andere actieve instanties. Deze gegevens worden vervolgens gebruikt om de belasting te verdelen over de actieve processors. Nieuwe exemplaren kunnen worden toegevoegd aan de verwerkings groep om omhoog te schalen. Wanneer instanties uitvallen, hetzij als gevolg van fouten of als u omlaag wilt schalen, wordt het eigendom van de partitie op de juiste wijze overgedragen aan andere actieve processors.

Voor het partitioneren van eigendoms records in de controlepunt opslag worden Event Hubs naam ruimte, de naam van Event Hub, de gebruikers groep, de gebeurtenis processor-id (ook wel eigenaar genoemd), de partitie-id en het tijdstip van de laatste wijziging bijgehouden.



| Event Hubs-naamruimte               | Event Hub-naam | **Consumentengroep** | Eigenaar                                | Partitie-ID | Tijdstip laatst gewijzigd  |
| ---------------------------------- | -------------- | :----------------- | :----------------------------------- | :----------- | :------------------ |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 3be3f9d3-9d9e-4c50-9491-85ece8334ff6 | 0            | 2020-01-15T01:22:15 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | f5cc5176-ce96-4bb4-bbaa-a0e3a9054ecf | 1            | 2020-01-15T01:22:17 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 72b980e9-2efc-4ca7-ab1b-ffd7bece8472 | 2            | 2020-01-15T01:22:10 |
|                                    |                | :                  |                                      |              |                     |
|                                    |                | :                  |                                      |              |                     |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroup    | 844bd8fb-1f3a-4580-984d-6324f9e208af | 15           | 2020-01-15T01:22:00 |

Elk gebeurtenis processor exemplaar verkrijgt het eigendom van een partitie en begint met de verwerking van de partitie van het laatste bekende [controle punt](# Checkpointing). Als een processor mislukt (VM wordt afgesloten), worden deze door andere instanties gedetecteerd door te kijken naar het tijdstip van de laatste wijziging. Andere exemplaren proberen eigenaar te worden van de partities die voorheen eigendom waren van het inactieve exemplaar, en het archief van het controle punt garandeert dat slechts één van de exemplaren slaagt bij het claimen van het eigendom van een partitie. Op een bepaald moment is er dus slechts één processor die gebeurtenissen van een partitie ontvangt.

## <a name="receive-messages"></a>Berichten ontvangen

Wanneer u een gebeurtenis processor maakt, geeft u de functies op waarmee gebeurtenissen en fouten worden verwerkt. Elke aanroep van de functie waarmee gebeurtenissen worden verwerkt, levert één gebeurtenis op uit een specifieke partitie. Het is uw verantwoordelijkheid om deze gebeurtenis af te handelen. Als u ervoor wilt zorgen dat de Consumer elk bericht ten minste één keer verwerkt, moet u uw eigen code schrijven met een nieuwe poging logica. Maar wees voorzichtig met verontreinigde berichten.

We raden u aan om dingen relatief snel te doen. Dat wil zeggen, zo weinig mogelijk verwerking. Als u naar opslag moet schrijven en een route ring wilt uitvoeren, is het beter om twee consumenten groepen te gebruiken en twee gebeurtenis processors te hebben.

## <a name="checkpointing"></a>Controlepunten plaatsen

*Controle punten* is een proces waarbij een gebeurtenis processor de positie van de laatste uitgevoerde gebeurtenis binnen een partitie markeert of doorvoert. Het markeren van een controle punt wordt doorgaans uitgevoerd binnen de functie waarmee de gebeurtenissen worden verwerkt en plaatsvindt per partitie binnen een Consumer groep. 

Als een gebeurtenis processor de verbinding met een partitie verbreekt, kan een ander exemplaar de verwerking van de partitie op het controle punt hervatten dat eerder is vastgelegd door de laatste processor van die partitie in die Consumer groep. Wanneer de processor verbinding maakt, wordt de offset door gegeven aan de Event Hub om de locatie op te geven waar u wilt beginnen met lezen. Op deze manier kunt u controle punten gebruiken om gebeurtenissen te markeren als ' voltooid ' door downstream-toepassingen en om tolerantie te bieden wanneer een gebeurtenis processor uitvalt. Het is mogelijk om terug te keren naar de oudere gegevens door een lagere offset van dit controlepuntproces op te geven. 

Wanneer het controle punt wordt uitgevoerd om een gebeurtenis te markeren als verwerkt, wordt een vermelding in het opslag punt archief toegevoegd of bijgewerkt met de offset en het Volg nummer van de gebeurtenis. Gebruikers moeten bepalen hoe vaak het controle punt moet worden bijgewerkt. Het bijwerken na elke geslaagde gebeurtenis kan gevolgen hebben voor prestaties en kosten omdat hiermee een schrijf bewerking wordt geactiveerd voor de onderliggende controlepunt opslag. Het is ook mogelijk om een controle punt te maken voor elke gebeurtenis, een bericht in de wachtrij waarin een Service Bus wachtrij een betere optie is dan een Event Hub. Het idee achter Event Hubs, is dat u 'ten minste eenmaal' levering op grote schaal. Door uw downstream-systemen idempotent zijn, het is eenvoudig om fouten te herstellen of opnieuw wordt gestart die resulteren in dezelfde gebeurtenissen meerdere keren worden ontvangen.

## <a name="thread-safety-and-processor-instances"></a>Thread-veiligheid en processor-instanties

Standaard is een gebeurtenis processor of Consumer thread veilig en gedraagt zich op synchrone wijze. Wanneer gebeurtenissen voor een partitie arriveren, wordt de functie aangeroepen waarmee de gebeurtenissen worden verwerkt. Volgende berichten en aanroepen naar deze functie worden achter de schermen in de wachtrij geplaatst, terwijl de bericht pomp blijft draaien op de achtergrond van andere threads. Deze thread veiligheid hoeven zich niet voor thread-veilige verzamelingen en worden de prestaties aanzienlijk verbeterd.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende Quick Start:

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-node-send-v2.md)
