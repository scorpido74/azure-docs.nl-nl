---
title: Verdelingsbelasting in evenwicht voor meerdere instanties - Azure Event Hubs | Microsoft Documenten
description: Beschrijft hoe u de verdelingsbelasting in meerdere exemplaren van uw toepassing balanceren met behulp van een gebeurtenisprocessor en de Azure Event Hubs SDK.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76907654"
---
# <a name="balance-partition-load-across-multiple-instances-of-your-application"></a>Verdelingsbelasting in evenwicht brengen in meerdere exemplaren van uw toepassing
Als u uw toepassing voor het verwerken van gebeurtenissen wilt schalen, u meerdere exemplaren van de toepassing uitvoeren en de belasting onderling in evenwicht brengen. In de oudere versies kon je met [EventProcessorHost](event-hubs-event-processor-host.md) de belasting tussen meerdere exemplaren van je programma en checkpointgebeurtenissen balanceren wanneer je ze ontvangen. In de nieuwere versies (5.0 vanaf), **EventProcessorClient** (.NET en Java), of **EventHubConsumerClient** (Python en JavaScript) u hetzelfde doen. Het ontwikkelingsmodel wordt eenvoudiger gemaakt door gebruik te maken van evenementen. Je abonneert je op de evenementen waarin je geïnteresseerd bent door een gebeurtenishandler te registreren.

In dit artikel wordt een voorbeeldscenario beschreven voor het gebruik van meerdere instanties om gebeurtenissen van een gebeurtenishub te lezen en u vervolgens details te geven over functies van de gebeurtenisprocessorclient, waarmee u gebeurtenissen van meerdere partities tegelijk ontvangen en het saldo met andere consumenten die dezelfde eventhub en consumentengroep gebruiken.

> [!NOTE]
> De sleutel tot schaal voor Event Hubs is het idee van verdeelde consumenten. In tegenstelling tot het patroon van de [concurrerende consumenten](https://msdn.microsoft.com/library/dn568101.aspx) maakt het verdeelde consumentenpatroon een grote schaal mogelijk door het geschilknelpunt weg te nemen en het parallellisme van de end-to-end te vergemakkelijken.

## <a name="example-scenario"></a>Voorbeeldscenario

Als voorbeeld scenario, overweeg dan een home security bedrijf dat 100.000 woningen controleert. Elke minuut, het krijgt gegevens van verschillende sensoren, zoals een bewegingsdetector, deur / raam open sensor, glas breken detector, en ga zo maar door, geïnstalleerd in elk huis. Het bedrijf biedt een website voor bewoners om de activiteit van hun huis te controleren in de buurt van real-time.

Elke sensor duwt gegevens naar een gebeurtenishub. De gebeurtenishub is geconfigureerd met 16 partities. Aan het verbruikende uiteinde hebt u een mechanisme nodig dat deze gebeurtenissen kan lezen, consolideren (filter, aggregaat, enzovoort) en het aggregaat dumpen naar een opslagblob, die vervolgens wordt geprojecteerd op een gebruiksvriendelijke webpagina.

## <a name="write-the-consumer-application"></a>Schrijf de consumententoepassing

Bij het ontwerpen van de consument in een gedistribueerde omgeving moet het scenario aan de volgende vereisten voldoen:

1. **Schaal:** Maak meerdere consumenten, waarbij elke consument eigenaar wordt van het lezen van een paar Event Hubs-partities.
2. **Load balans:** Verhoog of reduceer de consument dynamisch. Wanneer bijvoorbeeld een nieuw sensortype (bijvoorbeeld een koolmonoxidemelder) aan elk huis wordt toegevoegd, neemt het aantal gebeurtenissen toe. In dat geval verhoogt de operator (een mens) het aantal consumenteninstanties. Vervolgens kan de pool van consumenten het aantal partities dat ze bezitten opnieuw in evenwicht brengen, om de belasting te delen met de nieuw toegevoegde consumenten.
3. **Naadloos hervatten bij storingen:** Als een consument **(consument A)** faalt (bijvoorbeeld de virtuele machine die de consument host plotseling crasht), dan kunnen andere consumenten de partities van **consument A** oppakken en doorgaan. Ook moet het vervolgpunt, een *checkpoint* of *offset*genoemd, precies op het punt zijn waarop **consument A** faalde, of iets daarvoor.
4. **Gebeurtenissen consumeren:** Terwijl de vorige drie punten betrekking hebben op het beheer van de consument, moet er code zijn om de gebeurtenissen te consumeren en er iets nuttigs mee te doen. Verzamel het bijvoorbeeld en upload het naar blobopslag.

## <a name="event-processor-or-consumer-client"></a>Eventverwerker of consumentenklant

U hoeft geen eigen oplossing te bouwen om aan deze eisen te voldoen. De Azure Event Hubs SDKs bieden deze functionaliteit. In .NET of Java SDKs gebruikt u een eventprocessorclient (EventProcessorClient) en in Python- en Java Script SDKs gebruikt u EventHubConsumerClient. In de oude versie van SDK was het de host van de eventprocessor (EventProcessorHost) die deze functies ondersteunde.

Voor de meeste productiescenario's raden we u aan de gebeurtenisprocessorclient te gebruiken voor het lezen en verwerken van gebeurtenissen. De processorclient is bedoeld om een robuuste ervaring te bieden voor het verwerken van gebeurtenissen in alle partities van een gebeurtenishub op een performante en fouttolerante manier, terwijl het een middel biedt om de voortgang ervan te controleren. Eventprocessorklanten zijn ook in staat om samen te werken binnen de context van een consumentengroep voor een bepaalde eventhub. Clients beheren automatisch de distributie en het balanceren van werk zodra instanties beschikbaar of niet beschikbaar zijn voor de groep.

## <a name="partition-ownership-tracking"></a>Het bijhouden van partitieeigendom

Een gebeurtenisprocessorinstantie is doorgaans eigenaar en verwerkt gebeurtenissen van een of meer partities. Het eigendom van partities wordt gelijkmatig verdeeld over alle actieve gebeurtenisprocessorexemplaren die zijn gekoppeld aan een gebeurtenishub en combinatie van consumentengroepen. 

Elke gebeurtenisprocessor krijgt een unieke id en claimt het eigendom van partities door een vermelding in een controlepuntarchief toe te voegen of bij te werken. Alle gebeurtenisprocessorinstanties communiceren periodiek met deze winkel om de eigen verwerkingsstatus bij te werken en om meer te weten te komen over andere actieve exemplaren. Deze gegevens worden vervolgens gebruikt om de belasting tussen de actieve processors in evenwicht te brengen. Nieuwe exemplaren kunnen lid worden van de verwerkingsgroep om op te schalen. Wanneer exemplaren naar beneden gaan, als gevolg van fouten of om af te schalen, wordt partitieeigendom op een elegante manier overgedragen naar andere actieve processors.

Partitieeigendomsrecords in de controlepuntarchief houden de naamruimte van gebeurtenishubs, de naam van de gebeurtenishub, de consumentengroep, de gebeurtenisprocessor-id (ook bekend als eigenaar), partitie-id en de laatst gewijzigde tijd bij.



| Event Hubs-naamruimte               | Event Hub-naam | **Consumentengroep** | Eigenaar                                | Partitie-id | Laatst gewijzigde tijd  |
| ---------------------------------- | -------------- | :----------------- | :----------------------------------- | :----------- | :------------------ |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroep    | 3be3f9d3-9d9e-4c50-9491-85ece8334ff6 | 0            | 2020-01-15T01:22:15 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroep    | f5cc5176-ce96-4bb4-bbaa-a0e3a9054ecf | 1            | 2020-01-15T01:22:17 |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroep    | 72b980e9-2efc-4ca7-ab1b-ffd7bece8472 | 2            | 2020-01-15T01:22:10 |
|                                    |                | :                  |                                      |              |                     |
|                                    |                | :                  |                                      |              |                     |
| mynamespace.servicebus.windows.net | myeventhub     | myconsumergroep    | 844bd8fb-1f3a-4580-984d-6324f9e208af | 15           | 2020-01-15T01:22:00 |

Elke gebeurtenisprocessorinstantie verwerft het eigendom van een partitie en begint de verwerking van de partitie van het laatst bekende [controlepunt](# Checkpointing). Als een processor uitvalt (VM wordt afgesloten), detecteren andere instanties dit door naar de laatste gewijzigde tijd te kijken. Andere instanties proberen eigenaar te worden van de partities die voorheen eigendom waren van de inactieve instantie en het controlepuntarchief garandeert dat slechts één van de instanties erin slaagt het eigendom van een partitie te claimen. Dus, op een bepaald moment, is er hoogstens een processor die gebeurtenissen ontvangt van een partitie.

## <a name="receive-messages"></a>Berichten ontvangen

Wanneer u een gebeurtenisprocessor maakt, geeft u de functies op die gebeurtenissen en fouten verwerken. Elke aanroep naar de functie die gebeurtenissen verwerkt, levert één gebeurtenis uit een specifieke partitie. Het is jouw verantwoordelijkheid om dit evenement te behandelen. Als u ervoor wilt zorgen dat de consument elk bericht minstens één keer verwerkt, moet u uw eigen code met logica opnieuw proberen schrijven. Maar wees voorzichtig met vergiftigde berichten.

Wij raden u aan om dingen relatief snel te doen. Dat wil zeggen, doe zo weinig mogelijk verwerking. Als u naar opslag moet schrijven en een routebeschrijving moet maken, is het beter om twee consumentengroepen te gebruiken en twee gebeurtenisprocessors te hebben.

## <a name="checkpointing"></a>Controlepunten plaatsen

*Controlecontrole* is een proces waarbij een gebeurtenisverwerker de positie van de laatst verwerkte gebeurtenis binnen een partitie markeert of vastlegt. Het markeren van een controlepunt gebeurt meestal binnen de functie die de gebeurtenissen verwerkt en gebeurt op basis per partitie binnen een consumentengroep. 

Als een gebeurtenisprocessor de verbinding met een partitie verbreekt, kan een andere instantie de verwerking van de partitie hervatten bij het controlepunt dat eerder is vastgelegd door de laatste processor van die partitie in die consumentengroep. Wanneer de processor verbinding maakt, wordt de verschuiving doorgegeven aan de gebeurtenishub om de locatie op te geven waar u moet beginnen met lezen. Op deze manier u controlepunten gebruiken om zowel gebeurtenissen als 'voltooid' te markeren door downstreamtoepassingen als om tolerantie te bieden wanneer een gebeurtenisprocessor uitvalt. Het is mogelijk om terug te keren naar de oudere gegevens door een lagere offset van dit controlepuntproces op te geven. 

Wanneer het controlepunt wordt uitgevoerd om een gebeurtenis als verwerkt te markeren, wordt een item in het controlepuntarchief toegevoegd of bijgewerkt met het verschuivings- en volgnummer van de gebeurtenis. Gebruikers moeten beslissen over de frequentie van het bijwerken van het controlepunt. Bijwerken na elke met succes verwerkte gebeurtenis kan gevolgen hebben voor prestaties en kosten, omdat het een schrijfbewerking activeert naar het onderliggende controlepuntarchief. Ook is het controleren van elke gebeurtenis een indicatie van een berichtenpatroon in de wachtrij waarvoor een wachtrij voor servicebus een betere optie is dan een gebeurtenishub. Het idee achter Event Hubs is dat je "ten minste eenmaal" levering op grote schaal krijgt. Door uw downstreamsystemen idempotent te maken, is het gemakkelijk om te herstellen van storingen of opnieuw opstarten die resulteren in dezelfde gebeurtenissen die meerdere keren worden ontvangen.

## <a name="thread-safety-and-processor-instances"></a>Thread-beveiliging en processorinstanties

Standaard is de gebeurtenisprocessor of consument draadveilig en gedraagt zich synchroon. Wanneer gebeurtenissen aankomen voor een partitie, wordt de functie die de gebeurtenissen verwerkt aangeroepen. Latere berichten en oproepen naar deze functie wachtrij achter de schermen als het bericht pomp blijft draaien op de achtergrond op andere threads. Deze draadveiligheid elimineert de noodzaak van draadveilige verzamelingen en verhoogt de prestaties aanzienlijk.

## <a name="next-steps"></a>Volgende stappen
Zie de volgende snelle starts:

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](event-hubs-java-get-started-send.md)
- [Python](get-started-python-send-v2.md)
- [Javascript](get-started-node-send-v2.md)
