---
title: Include-bestand
description: Include-bestand
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: dc7c86ff1df48f9ce96769098f7aab76d33c8822
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "68481429"
---
Event Hubs daarentegen biedt streaming van berichten via een model op basis van gepartitioneerd gebruik, waarbij elke consumer slechts een specifieke subset of partitie van de berichtenstroom leest. Dit patroon maakt een horizontale schaal voor de verwerking van gebeurtenissen mogelijk en biedt andere stroomgerichte functies die niet beschikbaar zijn in wachtrijen en onderwerpen.

Een partitie is een geordende reeks gebeurtenissen die in een Event Hub wordt bewaard. Als er nieuwere gebeurtenissen plaatsvinden, worden deze toegevoegd aan het einde van deze reeks. Een partitie kan worden beschouwd als een 'doorvoerlogboek'.

![Event Hubs](./media/event-hubs-partitions/partition.png)

Event Hubs behoudt gegevens voor een geconfigureerde Bewaar tijd die van toepassing is op alle partities in de Event Hub. Gebeurtenissen verlopen op basis van tijd. U kunt ze niet expliciet verwijderen. Omdat partities onafhankelijk zijn en hun eigen reeks gegevens bevatten, groeien ze vaak met verschillende snelheden.

![Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

Het aantal partities wordt opgegeven bij het maken en moet tussen 2 en 32 liggen. Het aantal partities kan niet worden gewijzigd. Houd bij het instellen van het aantal partities dus uw doelen op de lange termijn in gedachten. Partities zijn een mechanisme voor gegevensordening. Ze hebben betrekking op de mate van downstreamparallelheid die is vereist bij het gebruik van toepassingen. Het aantal partities in een Event Hub houdt rechtstreeks verband met het aantal verwachte gelijktijdige lezers. Neem contact op met het team van Event Hubs als u meer dan 32 partities wilt maken.

Het is raadzaam om de waarde in te stellen op het hoogst mogelijke niveau: 32, op het moment dat deze wordt gemaakt. Houd er rekening mee dat er meer dan één partitie heeft als gevolg dat er gebeurtenissen worden verzonden naar meerdere partities zonder de volg orde te behouden, tenzij u afzenders configureert om alleen te verzenden naar één enkele partitie van de 32, waardoor de resterende 31 partities overbodig zijn. In het eerste geval moet u gebeurtenissen lezen in alle 32-partities. In het laatste geval zijn er geen duidelijke extra kosten van de extra configuratie die u moet maken op de host van de gebeurtenis processor.

Hoewel partities herkenbaar zijn en direct naar een partitie kunnen worden verzonden, wordt niet aanbevolen. In plaats daarvan kunt u constructies op een hoger niveau gebruiken die in de sectie [gebeurtenis uitgevers](../articles/event-hubs/event-hubs-features.md#event-publishers) worden geïntroduceerd. 

Partities worden gevuld met een reeks gebeurtenis gegevens die de hoofd tekst van de gebeurtenis bevatten, een door de gebruiker gedefinieerde eigenschappen verzameling en meta gegevens, zoals de offset in de partitie en het nummer in de stroom reeks.

We raden u aan om 1:1 doorvoer eenheden en partities te verdelen om optimaal te kunnen schalen. Eén partitie heeft een gegarandeerde binnenkomend en uitgaand verkeer van Maxi maal één doorvoer eenheid. Hoewel het mogelijk is dat er een hogere door Voer op een partitie kan worden gerealiseerd, zijn de prestaties niet gegarandeerd. Daarom raden we u ten zeerste aan het aantal partities in een Event Hub groter dan of gelijk aan het aantal doorvoer eenheden te zijn.

Gezien de totale door Voer die u wilt plannen, weet u hoeveel doorvoer eenheden u nodig hebt en hoe u het minimum aantal partities hebt? Kies het aantal partities op basis van de downstream parallelie die u wilt uitvoeren, evenals uw toekomstige doorvoer behoeften. Er worden geen kosten in rekening gebracht voor het aantal partities dat u hebt binnen een event hub.

Zie de artikelen [Programmeergids voor Event Hubs](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) en [Beschikbaarheid en consistentie in Event Hubs](../articles/event-hubs/event-hubs-availability-and-consistency.md) voor meer informatie over partities en de verhouding tussen de beschikbaarheid en betrouwbaarheid.
