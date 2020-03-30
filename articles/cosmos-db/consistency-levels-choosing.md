---
title: Kies het juiste consistentieniveau voor uw Azure Cosmos DB-app
description: Het juiste consistentieniveau voor uw toepassing kiezen in Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: cea157e272a2bf464141e592b8e742f314a83233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441921"
---
# <a name="choose-the-right-consistency-level"></a>Het juiste consistentieniveau kiezen 

Gedistribueerde databases die vertrouwen op replicatie voor hoge beschikbaarheid, lage latentie of beide, maken de fundamentele afweging tussen de leesconsistentie versus beschikbaarheid, latentie en doorvoer. De meeste commercieel beschikbare gedistribueerde databases vragen ontwikkelaars om te kiezen tussen de twee extreme consistentiemodellen: *sterke* consistentie en *uiteindelijke* consistentie. Azure Cosmos DB stelt ontwikkelaars in staat om te kiezen uit de vijf goed gedefinieerde consistentiemodellen: *sterke,* *begrensde,* *sessie,* *consistent voorvoegsel* en *uiteindelijk*. Elk van deze consistentiemodellen is goed gedefinieerd, intuïtief en kan worden gebruikt voor specifieke scenario's in de echte wereld. Elk van de vijf consistentiemodellen biedt nauwkeurige [beschikbaarheid en prestatieafwegingen](consistency-levels-tradeoffs.md) en wordt ondersteund door uitgebreide SLA's. De volgende eenvoudige overwegingen zullen u helpen de juiste keuze te maken in veel voorkomende scenario's.

## <a name="sql-api-and-table-api"></a>SQL API en Tabel-API

Houd rekening met de volgende punten als uw toepassing is gebouwd met SQL API of Tabel API:

- Voor veel scenario's in de echte wereld is de consistentie van de sessie optimaal en is het de aanbevolen optie. Zie [Sessietoken voor uw toepassing voor](how-to-manage-consistency.md#utilize-session-tokens)meer informatie.

- Als uw toepassing een sterke consistentie vereist, wordt aanbevolen dat u het begrensde consistentieniveau van staleness gebruikt.

- Als u strengere consistentiegaranties nodig hebt dan die welke worden geboden door sessieconsistentie en latentie met één cijfer voor schrijft, wordt aanbevolen dat u het begrensde consistentieniveau van staleness gebruikt.  

- Als uw toepassing uiteindelijke consistentie vereist, wordt aanbevolen dat u consistent consistentieniveau voor voorvoegsel gebruikt.

- Als u minder strikte consistentiegaranties nodig hebt dan die welke worden geboden door de consistentie van de sessie, wordt aanbevolen om consistent consistentieniveau voor voorvoegsel te gebruiken.

- Als u de hoogste beschikbaarheid en de laagste latentie nodig hebt, gebruikt u het uiteindelijke consistentieniveau.

- Als u nog hogere gegevensduurzaamheid nodig hebt zonder dat dit ten koste gaat van de prestaties, u een aangepast consistentieniveau maken op de toepassingslaag. Zie Voor meer [informatie, Aangepaste synchronisatie implementeren in uw toepassingen.](how-to-custom-synchronization.md)

## <a name="cassandra-mongodb-and-gremlin-apis"></a>Cassandra, MongoDB en Gremlin API's

- Zie [Consistentieniveaus en Cosmos DB API's](consistency-levels-across-apis.md#cassandra-mapping)voor meer informatie over het in kaart brengen tussen "Lees consistentieniveau" in Apache Cassandra en Cosmos DB.

- Zie [Consistentieniveaus en Cosmos DB API's](consistency-levels-across-apis.md#mongo-mapping)voor meer informatie over het in kaart brengen tussen "Read Concern" van MongoDB en Azure Cosmos DB-consistentieniveaus.

## <a name="consistency-guarantees-in-practice"></a>Consistentiegaranties in de praktijk

In de praktijk u vaak sterkere consistentiegaranties krijgen. Consistentiegaranties voor een leesbewerking komen overeen met de versheid en volgorde van de databasewaarin u vraagt. Leesconsistentie is gekoppeld aan het bestellen en verspreiden van de schrijf-/updatebewerkingen.  

* Wanneer het consistentieniveau is ingesteld op **begrensde staleness**, garandeert Cosmos DB dat de clients altijd de waarde van een eerdere schrijven lezen, met een vertraging begrensd door het staleness-venster.

* Wanneer het consistentieniveau is ingesteld op **sterk**, is het venster staleness gelijk aan nul en de clients zijn gegarandeerd de meest recente vastgelegde waarde van de schrijfbewerking te lezen.

* Voor de overige drie consistentieniveaus is het venster grotendeels afhankelijk van uw werklast. Als er bijvoorbeeld geen schrijfbewerkingen in de database zijn, zal een leesbewerking met **uiteindelijke,** **sessie-** of consistente consistentieniveaus voor **voorvoegsel** waarschijnlijk dezelfde resultaten opleveren als een leesbewerking met een sterk consistentieniveau.

Als uw Azure Cosmos-account is geconfigureerd met een ander consistentieniveau dan de sterke consistentie, u de kans vinden dat uw clients sterke en consistente reads voor uw workloads krijgen door te kijken naar de *probabilistically Bounded Staleness* (PBS) statistiek. Deze statistiek wordt weergegeven in de Azure-portal, voor meer informatie, zie [Monitor Probabilistically Bounded Staleness (PBS) metric](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric).

Probabilistic begrensdstaleness laat zien hoe uiteindelijk is uw uiteindelijke consistentie. Deze statistiek geeft inzicht in hoe vaak u een sterkere consistentie krijgen dan het consistentieniveau dat u momenteel hebt geconfigureerd op uw Azure Cosmos-account. Met andere woorden, u de waarschijnlijkheid (gemeten in milliseconden) van het krijgen van sterk consistente leest voor een combinatie van schrijven en lezen regio's.

## <a name="next-steps"></a>Volgende stappen

Lees meer over de consistentieniveaus in de volgende artikelen:

* [Consistentieniveau toewijzing in Cosmos DB API's](consistency-levels-across-apis.md)
* [Beschikbaarheid en prestatieafwegingen voor verschillende consistentieniveaus](consistency-levels-tradeoffs.md)
* [Het sessietoken voor uw toepassing beheren](how-to-manage-consistency.md#utilize-session-tokens)
* [Metrische gegevens van aan waarschijnlijkheid gebonden veroudering (PBS) controleren](how-to-manage-consistency.md#monitor-probabilistically-bounded-staleness-pbs-metric)
