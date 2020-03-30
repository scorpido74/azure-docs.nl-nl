---
title: Azure Cosmos DB-consistentie, beschikbaarheid en prestatieafwegingen
description: Beschikbaarheid en prestatie-trade-offs voor verschillende consistentieniveaus in Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: a16acfc8f9be820e9cc9b3bd59d6675b7f75d2ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445548"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Compromissen tussen consistentie, beschikbaarheid en prestaties 

Gedistribueerde databases die afhankelijk zijn van replicatie voor hoge beschikbaarheid, lage latentie of beide moeten afwegingen maken. Er moet een afweging worden gemaakt tussen leesconsistentie versus beschikbaarheid, latentie en doorvoer.

Azure Cosmos DB benadert gegevensconsistentie als een spectrum van keuzes. Deze aanpak omvat meer opties dan de twee uitersten van sterke en uiteindelijke consistentie. U kiezen uit vijf goed gedefinieerde modellen op het consistentiespectrum. Van sterkste tot zwakste, de modellen zijn:

- *Sterk*
- *Begrensde staleness*
- *Sessie*
- *Consistent voorvoegsel*
- *Mogelijk*

Elk model biedt beschikbaarheid en prestaties afwegingen en wordt ondersteund door uitgebreide SLA's.

## <a name="consistency-levels-and-latency"></a>Consistentieniveaus en latentie

De leeslatentie voor alle consistentieniveaus is altijd gegarandeerd minder dan 10 milliseconden op het 99e percentiel. Deze leeslatentie wordt ondersteund door de SLA. De gemiddelde leeslatentie, op het 50e percentiel, is meestal 2 milliseconden of minder. Azure Cosmos-accounts die meerdere regio's omvatten en met een sterke consistentie zijn geconfigureerd, vormen een uitzondering op deze garantie.

De schrijflatentie voor alle consistentieniveaus is altijd gegarandeerd minder dan 10 milliseconden op het 99e percentiel. Deze schrijflatentie wordt ondersteund door de SLA. De gemiddelde schrijflatentie, op het 50e percentiel, is meestal 5 milliseconden of minder.

Voor Azure Cosmos-accounts die met een sterke consistentie met meer dan één regio zijn geconfigureerd, is de schrijflatentie gegarandeerd minder dan twee keer de RTT (Round-trip- tijd) tussen een van de twee verste regio's, plus 10 milliseconden op het 99e percentiel.

De exacte RTT-latentie is een functie van de afstand van de snelheid van het licht en de Azure-netwerktopologie. Azure-netwerken bieden geen latentieSLA's voor de RTT tussen twee Azure-regio's. Voor uw Azure Cosmos-account worden replicatielatencies weergegeven in de Azure-portal. U de Azure-portal (ga naar het blade met metrische gegevens) gebruiken om de replicatielatencies tussen verschillende regio's die zijn gekoppeld aan uw Azure Cosmos-account te controleren.

## <a name="consistency-levels-and-throughput"></a>Consistentieniveaus en doorvoer

- Voor hetzelfde aantal aanvraageenheden bieden de sessie, het consistente voorvoegsel en de uiteindelijke consistentieniveaus ongeveer twee keer de leesdoorvoer in vergelijking met sterke en begrensde staleness.

- Voor een bepaald type schrijfbewerking, zoals invoegen, vervangen, upsert en verwijderen, is de schrijfdoorvoer voor aanvraageenheden identiek voor alle consistentieniveaus.

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>Consistentieniveaus en gegevensduurzaamheid

Binnen een wereldwijd gedistribueerde databaseomgeving is er een direct verband tussen het consistentieniveau en de duurzaamheid van gegevens in aanwezigheid van een regio-brede uitval. Terwijl u uw bedrijfscontinuïteitsplan ontwikkelt, moet u de maximaal aanvaardbare tijd begrijpen voordat de toepassing volledig herstelt na een storende gebeurtenis. De tijd die nodig is voor een aanvraag om volledig te herstellen staat bekend als **hersteltijddoelstelling** **(RTO).** U moet ook de maximale periode van recente gegevensupdates begrijpen die de toepassing kan tolereren wanneer deze herstelt na een storende gebeurtenis. De periode van updates die u zich zou kunnen veroorloven om te verliezen staat bekend als **herstelpunt doelstelling** **(RPO).**

In de onderstaande tabel wordt de relatie tussen consistentiemodel en gegevensduurzaamheid gedefinieerd in aanwezigheid van regiobrede uitval. Het is belangrijk op te merken dat in een gedistribueerd systeem, zelfs met een sterke consistentie, is het onmogelijk om een gedistribueerde database met een RPO en RTO van nul als gevolg van de STELLING van het GLB. Zie [Consistentieniveaus in Azure Cosmos DB](consistency-levels.md)voor meer informatie over waarom.

|**Regio(s)**|**Replicatiemodus**|**Consistentieniveau**|**RPO**|**Rto**|
|---------|---------|---------|---------|---------|
|1|Single of Multi-Master|Elk consistentieniveau|< 240 minuten|<1 week|
|>1.|Eén master|Sessie, Consistent Voorvoegsel, Eventueel|< 15 minuten|< 15 minuten|
|>1.|Eén master|Gebonden veroudering|*K* & *T*|< 15 minuten|
|>1.|Eén master|Sterk|0|< 15 minuten|
|>1.|Multi-Master|Sessie, Consistent Voorvoegsel, Eventueel|< 15 minuten|0|
|>1.|Multi-Master|Gebonden veroudering|*K* & *T*|0|

*K* = Het aantal *"K"-versies* (d.w.z. updates) van een item.

*T* = Het tijdsinterval *"T"* sinds de laatste update.

## <a name="strong-consistency-and-multi-master"></a>Sterke consistentie en multi-master

Cosmos-accounts die zijn geconfigureerd voor multimaster kunnen niet worden geconfigureerd voor een sterke consistentie, omdat het niet mogelijk is dat een gedistribueerd systeem een RPO van nul en een RTO van nul biedt. Bovendien zijn er geen voordelen voor schrijflatentie voor het gebruik van sterke consistentie met multi-master, omdat elke schrijfervaring in een regio moet worden gerepliceerd en vastgelegd op alle geconfigureerde regio's binnen het account. Dit resulteert in dezelfde schrijflatentie als een enkel masteraccount.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over wereldwijde distributie en algemene consistentieafwegingen in gedistribueerde systemen. Zie de volgende artikelen:

- [Consistentie afwegingen in moderne gedistribueerde database systemen ontwerp](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Hoge beschikbaarheid](high-availability.md)
- [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
