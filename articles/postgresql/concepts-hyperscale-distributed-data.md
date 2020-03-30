---
title: Gedistribueerde gegevens – Hyperscale (Citus) - Azure Database voor PostgreSQL
description: Meer informatie over gedistribueerde tabellen, referentietabellen, lokale tabellen en shards in Azure Database voor PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: ade7632dc042741a07bdb59e34e30b3fb464e0e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243651"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus"></a>Gedistribueerde gegevens in Azure Database voor PostgreSQL – Hyperscale (Citus)

In dit artikel worden de drie tabeltypen in Azure Database voor PostgreSQL – Hyperscale (Citus) beschreven.
Het laat zien hoe gedistribueerde tabellen worden opgeslagen als shards en de manier waarop shards op knooppunten worden geplaatst.

## <a name="table-types"></a>Tabeltypen

Er zijn drie typen tabellen in een Hyperscale (Citus) servergroep, elk gebruikt voor verschillende doeleinden.

### <a name="type-1-distributed-tables"></a>Type 1: Gedistribueerde tabellen

Het eerste type, en meest voorkomende, is gedistribueerde tabellen. Het lijken normale tabellen voor SQL-instructies, maar ze zijn horizontaal verdeeld over werknemersknooppunten. Dit betekent dat de rijen van de tabel worden opgeslagen op verschillende knooppunten, in fragmenttabellen die shards worden genoemd.

Hyperscale (Citus) voert niet alleen SQL- maar DDL-instructies uit in een cluster.
Het schema van een gedistribueerde tabeltraps wijzigen om alle shards van de tabel tussen werknemers bij te werken.

#### <a name="distribution-column"></a>Distributiekolom

Hyperscale (Citus) maakt gebruik van algoritmische sharding om rijen toe te wijzen aan scherven. De toewijzing wordt deterministisch gemaakt op basis van de waarde van een tabelkolom genaamd de distributiekolom. De clusterbeheerder moet deze kolom aanwijzen bij het distribueren van een tabel.
Het maken van de juiste keuze is belangrijk voor prestaties en functionaliteit.

### <a name="type-2-reference-tables"></a>Type 2: Referentietabellen

Een referentietabel is een type gedistribueerde tabel waarvan de volledige inhoud is geconcentreerd in één scherf. De scherf wordt gerepliceerd op elke werknemer. Query's op elke werknemer hebben lokaal toegang tot de referentie-informatie, zonder de netwerkoverhead van het aanvragen van rijen vanuit een ander knooppunt. Referentietabellen hebben geen distributiekolom omdat het niet nodig is afzonderlijke shards per rij te onderscheiden.

Referentietabellen zijn meestal klein en worden gebruikt om gegevens op te slaan die relevant zijn voor query's die worden uitgevoerd op een werknemersknooppunt. Een voorbeeld hiervan zijn opgesomde waarden zoals orderstatussen of productcategorieën.

### <a name="type-3-local-tables"></a>Type 3: Lokale tabellen

Wanneer u Hyperscale (Citus) gebruikt, is het coördinatorknooppunt waarmee u verbinding maakt een gewone PostgreSQL-database. U gewone tabellen op de coördinator maken en ervoor kiezen deze niet te sharden.

Een goede kandidaat voor lokale tabellen zijn kleine administratieve tabellen die niet deelnemen aan join queries. Een voorbeeld is een gebruikerstabel voor aanmelding en verificatie van toepassingen.

## <a name="shards"></a>Scherven

In de vorige sectie werd beschreven hoe gedistribueerde tabellen worden opgeslagen als shards op werkknooppunten. In deze sectie worden meer technische details besproken.

De `pg_dist_shard` metagegevenstabel op de coördinator bevat een rij voor elke shard van elke gedistribueerde tabel in het systeem. De rij komt overeen met een shard-id met een bereik van gehele getallen in een hashruimte (shardminvalue, shardmaxvalue).

```sql
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

Als het coördinatorknooppunt wil bepalen welke `github_events`shard een rij bevat, heeft het de waarde van de distributiekolom in de rij. Vervolgens controleert het knooppunt\'welke shards-bereik de gehashte waarde bevat. De bereiken worden zo gedefinieerd dat de afbeelding van de hashfunctie hun onsamenhangende unie is.

### <a name="shard-placements"></a>Shardplaatsingen

Stel dat de shard 102027 is gekoppeld aan de rij in kwestie. De rij wordt gelezen of `github_events_102027` geschreven in een tabel genaamd in een van de werknemers. Welke werknemer? Dat wordt volledig bepaald door de metadata tabellen. Het toewijzen van shard aan werknemer staat bekend als de shardplaatsing.

Het coördinatorknooppunt herschrijft query's in fragmenten `github_events_102027` die verwijzen naar de specifieke tabellen zoals en voert deze fragmenten uit op de juiste werknemers. Hier is een voorbeeld van een query die achter de schermen wordt uitgevoerd om de shard-id 102027 van het knooppunt te vinden.

```sql
SELECT
    shardid,
    node.nodename,
    node.nodeport
FROM pg_dist_placement placement
JOIN pg_dist_node node
  ON placement.groupid = node.groupid
 AND node.noderole = 'primary'::noderole
WHERE shardid = 102027;
```

    ┌─────────┬───────────┬──────────┐
    │ shardid │ nodename  │ nodeport │
    ├─────────┼───────────┼──────────┤
    │  102027 │ localhost │     5433 │
    └─────────┴───────────┴──────────┘

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [kiezen van een distributiekolom](concepts-hyperscale-choose-distribution-column.md) voor gedistribueerde tabellen.
