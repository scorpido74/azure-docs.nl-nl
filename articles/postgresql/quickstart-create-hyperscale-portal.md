---
title: Quick start voor Azure Database for PostgreSQL – grootschalige (Citus) (preview)
description: Quick start voor het maken en doorzoeken van gedistribueerde tabellen op Azure Database for PostgreSQL grootschalige (Citus) (preview).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/14/2019
ms.openlocfilehash: fe981167249e24a43a8cb14c51c9b7c1eb081225
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/21/2019
ms.locfileid: "70164021"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-preview-in-the-azure-portal"></a>Quick Start: een Azure Database for PostgreSQL-grootschalige (Citus) (preview) maken in de Azure Portal

Azure Database for PostgreSQL is een beheerde service waarmee u PostgreSQL-databases met hoge beschikbaarheid in de cloud kunt uitvoeren, beheren en schalen. In deze Quick start ziet u hoe u een Citus-Server groep (Azure Database for PostgreSQL-grootschalige) (preview) maakt met behulp van de Azure Portal. U bekijkt gedistribueerde gegevens: sharding-tabellen tussen knoop punten, opname van voorbeeld gegevens en uitvoeren van query's die worden uitgevoerd op meerdere knoop punten.

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>Tabellen maken en distribueren

Wanneer u bent verbonden met het grootschalige-coördinator knooppunt met behulp van psql, kunt u een aantal basis taken volt ooien.

Binnen grootschalige-servers zijn er drie soorten tabellen:

- Gedistribueerde of Shard tabellen (uitbrei ding om te schalen voor prestaties en parallel Lise ring)
- Referentie tabellen (meerdere exemplaren worden bewaard)
- Lokale tabellen (vaak gebruikt voor interne admin-tabellen)

In deze Quick Start worden voornamelijk de focus op gedistribueerde tabellen toegepast en worden deze vertrouwd.

Het gegevens model waarmee we werken, is eenvoudig: gebruikers-en gebeurtenis gegevens van GitHub. Gebeurtenissen zijn onder andere het maken van een Fork, Git-door Voer die betrekking hebben op een organisatie en meer.

Zodra u verbinding hebt gemaakt via psql, gaan we onze tabellen maken. Voer de volgende handelingen uit in de psql-console:

```sql
CREATE TABLE github_events
(
    event_id bigint,
    event_type text,
    event_public boolean,
    repo_id bigint,
    payload jsonb,
    repo jsonb,
    user_id bigint,
    org jsonb,
    created_at timestamp
);

CREATE TABLE github_users
(
    user_id bigint,
    url text,
    login text,
    avatar_url text,
    gravatar_id text,
    display_login text
);
```

Het `payload` veld van `github_events` heeft een JSONB-gegevens type. JSONB is het JSON-gegevens type in binaire vorm in post gres. Het gegevens type maakt het eenvoudig om een flexibel schema in één kolom op te slaan.

Post gres kan een `GIN` index voor dit type maken, waarmee elke sleutel en waarde erin wordt geïndexeerd. Met een index wordt het snel en eenvoudig om de payload te doorzoeken met verschillende voor waarden. We gaan nu een aantal indexen maken voordat we onze gegevens laden. In psql:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Daarna nemen we deze post gres-tabellen op het coördinator knooppunt door en geven ze grootschalige om ze te Shard over de werk nemers. Hiervoor voeren we een query uit voor elke tabel die de sleutel opgeeft waarop deze moet worden Shard. In het huidige voor beeld Shard de tabel Evenementen en gebruikers op `user_id`:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

We zijn klaar om gegevens te laden. In psql gaat u naar de bestanden downloaden:

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

Laad vervolgens de gegevens van de bestanden in de gedistribueerde tabellen:

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Query's uitvoeren

Nu is het tijd voor het leuke deel, waarbij sommige query's werkelijk worden uitgevoerd. Laten we beginnen met een eenvoudige `count (*)` om te zien hoeveel gegevens er zijn geladen:

```sql
SELECT count(*) from github_events;
```

Dat werkte goed. We gaan terug naar die combi natie van aggregatie in een bit, maar we kijken nu naar een aantal andere query's. In de JSONB-`payload` kolom bevinden zich een goede hoeveelheid gegevens, maar deze is afhankelijk van het gebeurtenis type. `PushEvent` gebeurtenissen bevatten een grootte die het aantal unieke door voeringen voor de push bevat. We kunnen deze gebruiken om het totale aantal door voeringen per uur te vinden:

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Tot nu toe hebben de query's alleen betrekking op de GitHub-\_events, maar we kunnen deze informatie combi neren met github \_users. Omdat we zowel gebruikers als gebeurtenissen in dezelfde id (`user_id`) Shard, worden de rijen van beide tabellen met overeenkomende gebruikers-Id's op dezelfde database knooppunten [geplaatst](https://docs.citusdata.com/en/stable/sharding/data_modeling.html#colocation) en kunnen ze eenvoudig worden gekoppeld.

Als we deel nemen aan `user_id`, kan grootschalige de uitvoering van de samen voeging naar Shards pushen om parallel te worden uitgevoerd op worker-knoop punten. Laten we bijvoorbeeld de gebruikers vinden die het grootste aantal opslag plaatsen hebben gemaakt:

```sql
SELECT gu.login, count(*)
  FROM github_events ge
  JOIN github_users gu
    ON ge.user_id = gu.user_id
 WHERE ge.event_type = 'CreateEvent'
   AND ge.payload @> '{"ref_type": "repository"}'
 GROUP BY gu.login
 ORDER BY count(*) DESC;
```

## <a name="clean-up-resources"></a>Resources opschonen

In de voor gaande stappen hebt u Azure-resources in een server groep gemaakt. Als u deze resources in de toekomst niet meer nodig hebt, verwijdert u de Server groep. Druk op de knop **verwijderen** op de pagina **overzicht** voor uw server groep. Wanneer u hierom wordt gevraagd, bevestigt u de naam van de Server groep en klikt u op de laatste knop **verwijderen** .

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u geleerd hoe u een grootschalige (Citus)-Server groep kunt inrichten. U hebt verbinding gemaakt met het psql, een schema en gedistribueerde gegevens.

Volg vervolgens een zelf studie voor het bouwen van schaal bare multi tenant-toepassingen.
> [!div class="nextstepaction"]
> [Een multi tenant-data base ontwerpen](https://aka.ms/hyperscale-tutorial-multi-tenant)
