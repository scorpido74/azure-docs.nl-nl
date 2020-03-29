---
title: Tabelcolocatie - Hyperscale (Citus) - Azure Database voor PostgreSQL
description: Gerelateerde informatie opslaan voor snellere query's
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 7e4073ec45f4c21f33d20924a9948e72f961c7f8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74967334"
---
# <a name="table-colocation-in-azure-database-for-postgresql--hyperscale-citus"></a>Tabelcolocatie in Azure Database voor PostgreSQL – Hyperscale (Citus)

Colocatie betekent het opslaan van gerelateerde informatie samen op dezelfde knooppunten. Query's kunnen snel gaan wanneer alle benodigde gegevens beschikbaar zijn zonder netwerkverkeer. Door gerelateerde gegevens op verschillende knooppunten te colocaten, kunnen query's op elk knooppunt efficiënt parallel worden uitgevoerd.

## <a name="data-colocation-for-hash-distributed-tables"></a>Gegevenscolocatie voor door hash gedistribueerde tabellen

In Azure Database for PostgreSQL – Hyperscale (Citus) wordt een rij opgeslagen in een shard als de hash van de waarde in de distributiekolom binnen het hashbereik van de shard valt. Scherven met hetzelfde hashbereik worden altijd op hetzelfde knooppunt geplaatst. Rijen met dezelfde kolomwaarden voor distributie bevinden zich altijd op hetzelfde knooppunt voor tabellen.

![Scherven](media/concepts-hyperscale-colocation/colocation-shards.png)

## <a name="a-practical-example-of-colocation"></a>Een praktisch voorbeeld van colocatie

Houd rekening met de volgende tabellen die deel kunnen uitmaken van een SaaS met multitenantwebanalyse:

```sql
CREATE TABLE event (
  tenant_id int,
  event_id bigint,
  page_id int,
  payload jsonb,
  primary key (tenant_id, event_id)
);

CREATE TABLE page (
  tenant_id int,
  page_id int,
  path text,
  primary key (tenant_id, page_id)
);
```

Nu willen we vragen beantwoorden die kunnen worden uitgegeven door een klantgericht dashboard. Een voorbeeldquery is 'Het aantal bezoeken in de afgelopen week retourneren voor alle pagina's die beginnen met '/blog' in tenant zes.'

Als onze gegevens zich in de optie Single-Server-implementatie bevond, konden we onze query eenvoudig uitdrukken met behulp van de uitgebreide set relationele bewerkingen die SQL aanbiedt:

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

Zolang de [werkset](https://en.wikipedia.org/wiki/Working_set) voor deze query in het geheugen past, is een tabel met één server een geschikte oplossing. Laten we eens kijken naar de mogelijkheden om het gegevensmodel te schalen met de optie Hyperscale (Citus) implementatie.

### <a name="distribute-tables-by-id"></a>Tabellen distribueren op id

Query's met één server beginnen te vertragen naarmate het aantal tenants en de gegevens die voor elke tenant zijn opgeslagen, groeit. De werkset stopt met het inpassen in het geheugen en CPU wordt een knelpunt.

In dit geval kunnen we de gegevens over veel knooppunten sharden met Behulp van Hyperscale (Citus). De eerste en belangrijkste keuze die we moeten maken wanneer we besluiten om te scherfen is de distributie kolom. Laten we beginnen met een `event_id` naïeve keuze van `page_id` het `page` gebruik voor de gebeurtenistabel en voor de tabel:

```sql
-- naively use event_id and page_id as distribution columns

SELECT create_distributed_table('event', 'event_id');
SELECT create_distributed_table('page', 'page_id');
```

Wanneer gegevens worden verspreid over verschillende werknemers, kunnen we geen join uitvoeren zoals we zouden doen op één PostgreSQL-knooppunt. In plaats daarvan moeten we twee query's uitgeven:

```sql
-- (Q1) get the relevant page_ids
SELECT page_id FROM page WHERE path LIKE '/blog%' AND tenant_id = 6;

-- (Q2) get the counts
SELECT page_id, count(*) AS count
FROM event
WHERE page_id IN (/*…page IDs from first query…*/)
  AND tenant_id = 6
  AND (payload->>'time')::date >= now() - interval '1 week'
GROUP BY page_id ORDER BY count DESC LIMIT 10;
```

Daarna moeten de resultaten van de twee stappen worden gecombineerd door de toepassing.

Het uitvoeren van de query's moet gegevens raadplegen in shards verspreid over knooppunten.

![Inefficiënte query's](media/concepts-hyperscale-colocation/colocation-inefficient-queries.png)

In dit geval creëert de gegevensdistributie aanzienlijke nadelen:

-   Overhead van het opvragen van elke shard en het uitvoeren van meerdere query's.
-   Overhead van Q1 terug te keren vele rijen aan de klant.
-   Q2 wordt groot.
-   De noodzaak om query's in meerdere stappen te schrijven vereist wijzigingen in de toepassing.

De gegevens worden verspreid, zodat de query's kunnen worden parallel. Het is alleen nuttig als de hoeveelheid werk die de query doet aanzienlijk groter is dan de overhead van het opvragen van veel scherven.

### <a name="distribute-tables-by-tenant"></a>Tabellen distribueren per tenant

In Hyperscale (Citus) staan rijen met dezelfde waarde van de distributiekolom gegarandeerd op hetzelfde knooppunt. Opnieuw beginnen, kunnen we `tenant_id` onze tabellen maken met als de distributiekolom.

```sql
-- co-locate tables by using a common distribution column
SELECT create_distributed_table('event', 'tenant_id');
SELECT create_distributed_table('page', 'tenant_id', colocate_with => 'event');
```

Nu kan Hyperscale (Citus) de oorspronkelijke single-server query beantwoorden zonder wijziging (Q1):

```sql
SELECT page_id, count(event_id)
FROM
  page
LEFT JOIN  (
  SELECT * FROM event
  WHERE (payload->>'time')::timestamptz >= now() - interval '1 week'
) recent
USING (tenant_id, page_id)
WHERE tenant_id = 6 AND path LIKE '/blog%'
GROUP BY page_id;
```

Vanwege filter en join op tenant_id, Hyperscale (Citus) weet dat de hele query kan worden beantwoord met behulp van de set van co-located shards die de gegevens voor die specifieke tenant bevatten. Eén PostgreSQL-knooppunt kan de query in één stap beantwoorden.

![Betere query](media/concepts-hyperscale-colocation/colocation-better-query.png)

In sommige gevallen moeten query's en tabelschema's worden gewijzigd om de tenant-id in unieke beperkingen en aansluitvoorwaarden op te nemen. Deze verandering is meestal eenvoudig.

## <a name="next-steps"></a>Volgende stappen

- Bekijk hoe tenantgegevens worden naast elkaar geplaatst in de [zelfstudie met meerdere tenants](tutorial-design-database-hyperscale-multi-tenant.md).
