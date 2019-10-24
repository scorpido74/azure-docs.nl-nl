---
title: Beschik baarheid en prestaties voor diverse consistentie niveaus in Azure Cosmos DB
description: Beschik baarheid en prestaties voor diverse consistentie niveaus in Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: 9178b8007d707af2df150102b2d344a44106a9ca
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755187"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Compromissen tussen consistentie, beschikbaarheid en prestaties 

Gedistribueerde data bases die afhankelijk zijn van replicatie voor hoge Beschik baarheid, lage latentie of beide moeten afwegingen maken. De nadelen liggen tussen lees consistentie versus Beschik baarheid, latentie en door voer.

Azure Cosmos DB consistentie van gegevens als een breed scala aan mogelijkheden. Deze aanpak bevat meer opties dan de twee extreme, sterke en uiteindelijke consistentie. U kunt kiezen uit vijf goed gedefinieerde modellen op het consistentie spectrum. De modellen zijn van sterk tot zwakste:

- *Strakk*
- *Gebonden veroudering*
- *Beëindigen*
- *Consistent voor voegsel*
- *Uiteindelijke*

Elk model biedt Beschik baarheid en prestatie-afwegingen en wordt ondersteund door de uitgebreide service overeenkomsten.

## <a name="consistency-levels-and-latency"></a>Consistentie niveaus en latentie

De lees latentie voor alle consistentie niveaus is altijd gegarandeerd minder dan 10 milliseconden bij het 99e percentiel. Deze lees latentie wordt ondersteund door de SLA. De gemiddelde lees latentie, op het 50e percentiel, is doorgaans 2 milliseconden of minder. Azure Cosmos-accounts die verschillende regio's omvatten en die zijn geconfigureerd met sterke consistentie vormen een uitzonde ring op deze garantie.

De schrijf latentie voor alle consistentie niveaus is altijd gegarandeerd minder dan 10 milliseconden bij het 99e percentiel. Deze schrijf latentie wordt ondersteund door de SLA. De gemiddelde schrijf latentie, op het 50e percentiel, is meestal 5 milliseconden of minder.

Voor Azure Cosmos-accounts die zijn geconfigureerd met een sterke consistentie van meer dan één regio, wordt de schrijf latentie gegarandeerd minder dan twee keer round-trip tijd (RTT) tussen de twee versste regio's, plus 10 milliseconden in het 99e percentiel.

De exacte RTT-latentie is een functie van de snelheid van de afstand en de Azure-netwerk topologie. Azure Networking biedt geen latentie-Sla's voor de RTT tussen twee Azure-regio's. Voor uw Azure Cosmos-account worden replicatie latenties weer gegeven in de Azure Portal. U kunt de Azure Portal (Ga naar de Blade metrische gegevens) gebruiken om de replicatie latentie te bewaken tussen verschillende regio's die zijn gekoppeld aan uw Azure Cosmos-account.

## <a name="consistency-levels-and-throughput"></a>Consistentie niveaus en door Voer

- Voor hetzelfde aantal aanvraag eenheden bieden de sessie, het consistente voor voegsel en de uiteindelijke consistentie niveaus ongeveer twee keer de Lees doorvoer in vergelijking met sterke en gebonden veroudering.

- Voor een bepaald type schrijf bewerking, zoals invoegen, vervangen, upsert en verwijderen, is de schrijf doorvoer voor aanvraag eenheden identiek voor alle consistentie niveaus.

## <a id="rto"></a>Consistentie niveaus en gegevens duurzaamheid

Binnen een wereld wijd gedistribueerde database omgeving is er een rechtstreekse relatie tussen het consistentie niveau en de duurzaamheid van de gegevens in de aanwezigheid van een regionale storing. Wanneer u uw bedrijfs continuïteits plan ontwikkelt, moet u weten wat de Maxi maal toegestane tijd is voordat de toepassing volledig wordt hersteld na een storende gebeurtenis. De tijd die nodig is om een toepassing volledig te herstellen, wordt de **beoogde herstel tijd** (**RTO**) genoemd. U moet ook inzicht krijgen in de maximale periode van recente gegevens updates die de toepassing kan afnemen bij het herstellen na een storende gebeurtenis. De tijds periode van updates die u mogelijk wilt verliezen, is **Recovery Point Objective** (**RPO**) genoemd.

In de onderstaande tabel wordt de relatie tussen consistentie model en gegevens duurzaamheid gedefinieerd in aanwezigheid van een regionale storing. Het is belang rijk te weten dat u in een gedistribueerd systeem, zelfs met een sterke consistentie, geen gedistribueerde data base met een RPO en RTO van nul hebt als gevolg van de CAP theorema. Zie voor meer informatie over waarom de [consistentie niveaus in azure Cosmos DB](consistency-levels.md).

|**Regio (s)**|**Replicatie modus**|**Consistentie niveau**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Eén of meerdere masters|Elk consistentie niveau|< 240 minuten|< 1 week|
|> 1|Eén Master|Sessie, consistent voor voegsel, uiteindelijk|< 15 minuten|< 15 minuten|
|> 1|Eén Master|Gebonden veroudering|*K*  & *t*|< 15 minuten|
|> 1|Eén Master|Sterk|0|< 15 minuten|
|> 1|Multi-Master|Sessie, consistent voor voegsel, uiteindelijk|< 15 minuten|0|
|> 1|Multi-Master|Gebonden veroudering|*K*  & *t*|0|

*K* = het aantal *"K"* versies (bijvoorbeeld updates) van een item.

*T* = het tijds interval *' t '* sinds de laatste update.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over globale distributie en algemene consistentie-afwegingen in gedistribueerde systemen. Zie de volgende artikelen:

- [Consistentie-afwegingen in het moderne ontwerp van gedistribueerde database systemen](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Hoge beschikbaarheid](high-availability.md)
- [SLA voor Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
