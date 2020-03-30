---
title: bestand opnemen
description: bestand opnemen
services: event-hubs
author: spelluru
ms.service: event-hubs
ms.topic: include
ms.date: 05/22/2019
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: dc7c86ff1df48f9ce96769098f7aab76d33c8822
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "68481429"
---
Event Hubs daarentegen biedt streaming van berichten via een model op basis van gepartitioneerd gebruik, waarbij elke consumer slechts een specifieke subset of partitie van de berichtenstroom leest. Dit patroon maakt een horizontale schaal voor de verwerking van gebeurtenissen mogelijk en biedt andere stroomgerichte functies die niet beschikbaar zijn in wachtrijen en onderwerpen.

Een partitie is een geordende reeks gebeurtenissen die in een Event Hub wordt bewaard. Als er nieuwere gebeurtenissen plaatsvinden, worden deze toegevoegd aan het einde van deze reeks. Een partitie kan worden beschouwd als een 'doorvoerlogboek'.

![Event Hubs](./media/event-hubs-partitions/partition.png)

Gebeurtenishubs bewaren gegevens voor een geconfigureerde bewaartijd die van toepassing is op alle partities in de gebeurtenishub. Gebeurtenissen verlopen op basis van tijd. U kunt ze niet expliciet verwijderen. Omdat partities onafhankelijk zijn en hun eigen reeks gegevens bevatten, groeien ze vaak met verschillende snelheden.

![Event Hubs](./media/event-hubs-partitions/multiple-partitions.png)

Het aantal partities wordt opgegeven bij het maken en moet tussen 2 en 32 liggen. Het aantal partities kan niet worden gewijzigd. Houd bij het instellen van het aantal partities dus uw doelen op de lange termijn in gedachten. Partities zijn een mechanisme voor gegevensordening. Ze hebben betrekking op de mate van downstreamparallelheid die is vereist bij het gebruik van toepassingen. Het aantal partities in een Event Hub houdt rechtstreeks verband met het aantal verwachte gelijktijdige lezers. Neem contact op met het team van Event Hubs als u meer dan 32 partities wilt maken.

U instellen dat het de hoogst mogelijke waarde is, namelijk 32, op het moment van creatie. Vergeet niet dat het hebben van meer dan één partitie zal resulteren in gebeurtenissen die naar meerdere partities worden verzonden zonder de volgorde te behouden, tenzij u afzenders configureert om slechts naar één partitie van de 32 te verzenden en de resterende 31 partities overbodig te laten. In het eerste geval moet u gebeurtenissen lezen over alle 32 partities. In het laatste geval zijn er geen duidelijke extra kosten, afgezien van de extra configuratie die u moet maken op Event Processor Host.

Hoewel partities identificeerbaar zijn en rechtstreeks naar een partitie kunnen worden verzonden, wordt het verzenden naar een partitie niet aanbevolen. In plaats daarvan u constructies op een hoger niveau gebruiken die zijn geïntroduceerd in de sectie [Logboekuitgevers.](../articles/event-hubs/event-hubs-features.md#event-publishers) 

Partities worden gevuld met een reeks gebeurtenisgegevens die de hoofdtekst van de gebeurtenis, een door de gebruiker gedefinieerde eigenschappenzak en metagegevens bevatten, zoals de verschuiving in de partitie en het aantal in de streamreeks.

Wij raden u aan 1:1 doorvoereenheden en partities in evenwicht te brengen om een optimale schaal te bereiken. Een enkele partitie heeft een gegarandeerde binnendringen en uitgang van maximaal één doorvoereenheid. Hoewel u mogelijk een hogere doorvoer op een partitie bereiken, zijn de prestaties niet gegarandeerd. Daarom raden we ten zeerste aan dat het aantal partities in een gebeurtenishub groter is dan of gelijk is aan het aantal doorvoereenheden.

Gezien de totale doorvoer die u van plan bent te hebben, weet u het aantal doorvoereenheden dat u nodig hebt en het minimumaantal partities, maar hoeveel partities moet u hebben? Kies het aantal partities op basis van de downstream parallellisme die u wilt bereiken, evenals uw toekomstige doorvoerbehoeften. Er worden geen kosten in rekening gebracht voor het aantal partities dat u binnen een gebeurtenishub hebt.

Zie de artikelen [Programmeergids voor Event Hubs](../articles/event-hubs/event-hubs-programming-guide.md#partition-key) en [Beschikbaarheid en consistentie in Event Hubs](../articles/event-hubs/event-hubs-availability-and-consistency.md) voor meer informatie over partities en de verhouding tussen de beschikbaarheid en betrouwbaarheid.
