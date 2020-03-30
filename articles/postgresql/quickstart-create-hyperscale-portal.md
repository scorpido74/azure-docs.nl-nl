---
title: Gedistribueerde tabellen maken - Hyperscale (Citus) - Azure Database voor PostgreSQL
description: Snel aan de slag met het maken en opvragen van gedistribueerde tabellen in Azure Database voor PostgreSQL Hyperscale (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: quickstart
ms.date: 05/14/2019
ms.openlocfilehash: 02e009e6fff2e717693d1579d409199ab179d941
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241512"
---
# <a name="quickstart-create-an-azure-database-for-postgresql---hyperscale-citus-in-the-azure-portal"></a>Snelstart: een Azure-database maken voor PostgreSQL - Hyperscale (Citus) in de Azure-portal

Azure Database for PostgreSQL is een beheerde service waarmee u PostgreSQL-databases met hoge beschikbaarheid in de cloud kunt uitvoeren, beheren en schalen. In deze Quickstart ziet u hoe u een Azure Database voor PostgreSQL - Hyperscale (Citus) servergroep maakt met behulp van de Azure-portal. U verkent gedistribueerde gegevens: shardtabellen tussen knooppunten, inname van voorbeeldgegevens en query's uitvoeren die op meerdere knooppunten worden uitgevoerd.

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="create-and-distribute-tables"></a>Tabellen maken en distribueren

Eenmaal verbonden met het knooppunt hyperscale coördinator met behulp van psql, u een aantal basistaken voltooien.

Binnen Hyperscale servers zijn er drie soorten tabellen:

- Gedistribueerde of geshardtabellen (verspreid om te schalen voor prestaties en parallelisatie)
- Referentietabellen (meerdere exemplaren onderhouden)
- Lokale tabellen (vaak gebruikt voor interne beheertabellen)

In deze quickstart richten we ons voornamelijk op gedistribueerde tabellen en zullen we ze vertrouwd maken.

Het datamodel waarmee we gaan werken is eenvoudig: gebruikers- en eventgegevens van GitHub. Evenementen omvatten fork creatie, git commits met betrekking tot een organisatie, en nog veel meer.

Zodra u verbinding hebt gemaakt via psql, laten we onze tabellen maken. In de psql-consolerun:

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

Het `payload` veld `github_events` van heeft een JSONB datatype. JSONB is het JSON datatype in binaire vorm in Postgres. Het gegevenstype maakt het eenvoudig om een flexibel schema in één kolom op te slaan.

Postgres kan `GIN` een index maken op dit type, die elke sleutel en waarde binnen het indexeert. Met een index wordt het snel en eenvoudig om de payload met verschillende omstandigheden op te vragen. Laten we doorgaan en een paar indexen maken voordat we onze gegevens laden. In psql:

```sql
CREATE INDEX event_type_index ON github_events (event_type);
CREATE INDEX payload_index ON github_events USING GIN (payload jsonb_path_ops);
```

Vervolgens nemen we die Postgres tafels op de coördinator knooppunt en vertel Hyperscale om ze te scherfen over de werknemers. Hiervoor voeren we voor elke tabel een query uit waarin de sleutel wordt opgegeven om deze te scherfen. In het huidige voorbeeld zullen we zowel de `user_id`evenementen- als de gebruikerstabel op sharden:

```sql
SELECT create_distributed_table('github_events', 'user_id');
SELECT create_distributed_table('github_users', 'user_id');
```

We zijn klaar om gegevens te laden. In psql nog steeds, shell uit om de bestanden te downloaden:

```sql
\! curl -O https://examples.citusdata.com/users.csv
\! curl -O https://examples.citusdata.com/events.csv
```

Laad vervolgens de gegevens uit de bestanden in de gedistribueerde tabellen:

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy github_events from 'events.csv' WITH CSV
\copy github_users from 'users.csv' WITH CSV
```

## <a name="run-queries"></a>Query's uitvoeren

Nu is het tijd voor het leuke deel, eigenlijk het uitvoeren van een aantal vragen. Laten we beginnen met `count (*)` een eenvoudige om te zien hoeveel gegevens we geladen:

```sql
SELECT count(*) from github_events;
```

Dat werkte goed. We komen terug op dat soort aggregatie in een beetje, maar voor nu laten we eens kijken naar een paar andere query's. Binnen de JSONB-kolom `payload` is er een goed stukje gegevens, maar deze varieert op basis van gebeurtenistype. `PushEvent`gebeurtenissen bevatten een grootte die het aantal verschillende commits voor de push bevat. We kunnen het gebruiken om het totale aantal commits per uur te vinden:

```sql
SELECT date_trunc('hour', created_at) AS hour,
       sum((payload->>'distinct_size')::int) AS num_commits
FROM github_events
WHERE event_type = 'PushEvent'
GROUP BY hour
ORDER BY hour;
```

Tot nu toe hebben de zoekopdrachten uitsluitend betrekking op de github-evenementen,\_maar we kunnen deze informatie combineren met github-gebruikers.\_ Aangezien we zowel gebruikers als gebeurtenissen op`user_id`dezelfde id hebben gehard ( ), worden de rijen van beide tabellen met overeenkomende gebruikers-id's op dezelfde databaseknooppunten [geplaatst](https://docs.citusdata.com/en/stable/sharding/data_modeling.html#colocation) en kunnen ze eenvoudig worden samengevoegd.

Als we `user_id`deelnemen aan , kan Hyperscale de join-uitvoering naar beneden duwen in shards voor uitvoering parallel op werkknooppunten. Laten we bijvoorbeeld de gebruikers zoeken die het grootste aantal repositories hebben gemaakt:

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

In de voorgaande stappen hebt u Azure-resources in een servergroep gemaakt. Als u deze bronnen in de toekomst niet meer nodig verwacht, verwijdert u de servergroep. Druk op de knop **Verwijderen** op de pagina **Overzicht** voor uw servergroep. Bevestig de naam van de servergroep wanneer u daarom wordt gevraagd op een pop-uppagina en klik op de laatste knop **Verwijderen.**

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u geleerd hoe u een Hyperscale -servergroep (Citus) indient. U hebt ermee verbinding gemaakt met psql, een schema gemaakt en gegevens gedistribueerd.

Volg vervolgens een zelfstudie om schaalbare multi-tenant toepassingen te bouwen.
> [!div class="nextstepaction"]
> [Een multitenantdatabase ontwerpen](https://aka.ms/hyperscale-tutorial-multi-tenant)
