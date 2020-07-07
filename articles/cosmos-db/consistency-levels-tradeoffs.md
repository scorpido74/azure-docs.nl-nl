---
title: Azure Cosmos DB consistentie, Beschik baarheid en prestatie afweging
description: Beschik baarheid en prestaties voor diverse consistentie niveaus in Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/23/2020
ms.reviewer: sngun
ms.openlocfilehash: 4de696e2538bf1fa4823aafe30f931b7852535a7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82191733"
---
# <a name="consistency-availability-and-performance-tradeoffs"></a>Compromissen tussen consistentie, beschikbaarheid en prestaties

Gedistribueerde databases die afhankelijk zijn van replicatie voor hoge beschikbaarheid, lage latentie of beide moeten afwegingen maken. Er moet een afweging worden gemaakt tussen leesconsistentie versus beschikbaarheid, latentie en doorvoer.

Azure Cosmos DB consistentie van gegevens als een breed scala aan mogelijkheden. Deze aanpak bevat meer opties dan de twee extreme, sterke en uiteindelijke consistentie. U kunt kiezen uit vijf goed gedefinieerde niveaus op het consistentie spectrum. Van het sterkst tot het zwakst zijn de niveaus:

- *Sterk*
- *Gebonden veroudering*
- *Sessie*
- *Consistent voor voegsel*
- *Mogelijk*

Elk niveau biedt Beschik baarheid en prestatie afwegingen en wordt ondersteund door de uitgebreide Sla's.

## <a name="consistency-levels-and-latency"></a>Consistentie niveaus en latentie

De lees latentie voor alle consistentie niveaus is altijd gegarandeerd minder dan 10 milliseconden bij het 99e percentiel. Deze lees latentie wordt ondersteund door de SLA. De gemiddelde lees latentie, op het 50e percentiel, is doorgaans 4 milliseconden of minder.

De schrijf latentie voor alle consistentie niveaus is altijd gegarandeerd minder dan 10 milliseconden bij het 99e percentiel. Deze schrijf latentie wordt ondersteund door de SLA. De gemiddelde schrijf latentie, op het 50e percentiel, is meestal 5 milliseconden of minder. Azure Cosmos-accounts die verschillende regio's omvatten en die zijn geconfigureerd met sterke consistentie vormen een uitzonde ring op deze garantie.

### <a name="write-latency-and-strong-consistency"></a>Schrijf latentie en sterke consistentie

Voor Azure Cosmos-accounts die zijn geconfigureerd met een sterke consistentie van meer dan één regio, is de schrijf latentie gelijk aan twee maal een round-trip tijd (RTT) tussen de twee versste regio's, plus 10 milliseconden in het 99e percentiel. Een hoge netwerk-RTT tussen de regio's wordt omgezet naar een hogere latentie voor Cosmos DB aanvragen omdat de sterke consistentie pas een bewerking heeft voltooid nadat u hebt gecontroleerd of deze is doorgevoerd voor alle regio's binnen een account.

De exacte RTT-latentie is een functie van de snelheid van de afstand en de Azure-netwerk topologie. Azure Networking biedt geen latentie-Sla's voor de RTT tussen twee Azure-regio's. Voor uw Azure Cosmos-account worden replicatie latenties weer gegeven in de Azure Portal. U kunt de Azure Portal (Ga naar de Blade metrische gegevens, het tabblad consistentie selecteren) gebruiken om de replicatie latentie te controleren tussen verschillende regio's die zijn gekoppeld aan uw Azure Cosmos-account.

> [!IMPORTANT]
> Sterke consistentie voor accounts met regio's van meer dan 5000 mijl (8000 kilo meters) wordt standaard geblokkeerd als gevolg van een hoge schrijf latentie. Neem contact op met de ondersteuning om deze functie in te scha kelen.

## <a name="consistency-levels-and-throughput"></a>Consistentie niveaus en door Voer

- Voor sterke en gebonden veroudering worden Lees bewerkingen uitgevoerd voor twee replica's in een vier replicaset (minderheids quorum) om consistentie garanties te bieden. Sessie, consistent voor voegsel en uiteindelijk mogelijke lees bewerkingen met één replica. Het resultaat is dat, voor hetzelfde aantal aanvraag eenheden, de Lees doorvoer voor sterke en gebonden veroudering de helft van de andere consistentie niveaus is.

- Voor een bepaald type schrijf bewerking, zoals invoegen, vervangen, upsert en verwijderen, is de schrijf doorvoer voor aanvraag eenheden identiek voor alle consistentie niveaus.

|**Consistentieniveau**|**Quorum Lees bewerkingen**|**Quorum schrijf bewerkingen**|
|--|--|--|
|**Sterk**|Lokale minderheid|Wereld wijde meerderheid|
|**Gebonden veroudering**|Lokale minderheid|Lokale meerderheid|
|**Sessie**|Enkele replica (met sessie token)|Lokale meerderheid|
|**Consistent prefix**|Enkele replica|Lokale meerderheid|
|**Mogelijk**|Enkele replica|Lokale meerderheid|

## <a name="consistency-levels-and-data-durability"></a><a id="rto"></a>Consistentie niveaus en gegevens duurzaamheid

Binnen een wereld wijd gedistribueerde database omgeving is er een rechtstreekse relatie tussen het consistentie niveau en de duurzaamheid van de gegevens in de aanwezigheid van een regionale storing. Wanneer u uw bedrijfs continuïteits plan ontwikkelt, moet u weten wat de Maxi maal toegestane tijd is voordat de toepassing volledig wordt hersteld na een storende gebeurtenis. De tijd die nodig is om een toepassing volledig te herstellen, wordt de **beoogde herstel tijd** (**RTO**) genoemd. U moet ook inzicht krijgen in de maximale periode van recente gegevens updates die de toepassing kan afnemen bij het herstellen na een storende gebeurtenis. De tijds periode van updates die u mogelijk wilt verliezen, is **Recovery Point Objective** (**RPO**) genoemd.

In de onderstaande tabel wordt de relatie tussen consistentie model en gegevens duurzaamheid gedefinieerd in aanwezigheid van een regionale storing. Het is belang rijk te weten dat u in een gedistribueerd systeem, zelfs met een sterke consistentie, geen gedistribueerde data base met een RPO en RTO van nul hebt als gevolg van de CAP theorema. Zie voor meer informatie over waarom de [consistentie niveaus in azure Cosmos DB](consistency-levels.md).

|**Regio (s)**|**Replicatie modus**|**Consistentie niveau**|**RPO**|**RTO**|
|---------|---------|---------|---------|---------|
|1|Eén of meerdere masters|Elk consistentie niveau|< 240 minuten|<1 week|
|>1|Eén Master|Sessie, consistent voor voegsel, uiteindelijk|< 15 minuten|< 15 minuten|
|>1|Eén Master|Gebonden veroudering|*K*  &  *T*|< 15 minuten|
|>1|Eén Master|Sterk|0|< 15 minuten|
|>1|Multi-Master|Sessie, consistent voor voegsel, uiteindelijk|< 15 minuten|0|
|>1|Multi-Master|Gebonden veroudering|*K*  &  *T*|0|

*K* = het aantal *"K"* versies (bijvoorbeeld updates) van een item.

*T* = het tijds interval *' t '* sinds de laatste update.

## <a name="strong-consistency-and-multi-master"></a>Sterke consistentie en multi-master

Cosmos-accounts die zijn geconfigureerd voor multi-master, kunnen niet worden geconfigureerd voor sterke consistentie omdat het niet mogelijk is dat een gedistribueerd systeem een RPO van nul en een RTO van nul levert. Daarnaast zijn er geen schrijf latentie voordelen voor het gebruik van sterke consistentie met multi-master, omdat elke schrijf bewerking in een regio moet worden gerepliceerd en doorgevoerd voor alle geconfigureerde regio's binnen het account. Dit resulteert in dezelfde schrijf latentie als één hoofd account.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over globale distributie en algemene consistentie-afwegingen in gedistribueerde systemen. Zie de volgende artikelen:

- [Consistentie-afwegingen in het moderne ontwerp van gedistribueerde database systemen](https://www.computer.org/csdl/magazine/co/2012/02/mco2012020037/13rRUxjyX7k)
- [Hoge beschikbaarheid](high-availability.md)
- [SLA voor Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
