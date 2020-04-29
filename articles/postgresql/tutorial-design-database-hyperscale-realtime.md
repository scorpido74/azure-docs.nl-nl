---
title: 'Zelf studie: een real-time dash board ontwerpen-grootschalige (Citus)-Azure Database for PostgreSQL'
description: In deze zelf studie ziet u hoe u gedistribueerde tabellen maakt, vult en doorzoekt op Azure Database for PostgreSQL grootschalige (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: f4eeb646de8b68c2c8d30586d0c75cece5317e40
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76716325"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Zelf studie: een real-time analyse dashboard ontwerpen met behulp van Azure Database for PostgreSQL – grootschalige (Citus)

In deze zelf studie gebruikt u Azure Database for PostgreSQL-grootschalige (Citus) voor meer informatie over het volgende:

> [!div class="checklist"]
> * Een Hyperscale (Citus)-servergroep maken
> * Het hulp programma psql gebruiken om een schema te maken
> * Shard-tabellen tussen knoop punten
> * Voorbeeldgegevens genereren
> * Rollups uitvoeren
> * Query's uitvoeren op onbewerkte en geaggregeerde gegevens
> * Verlopen gegevens

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Het hulp programma psql gebruiken om een schema te maken

Nadat u verbinding hebt gemaakt met de Azure Database for PostgreSQL-grootschalige (Citus) met behulp van psql, kunt u een aantal basis taken volt ooien. In deze zelf studie wordt uitgelegd hoe u verkeers gegevens opneemt vanuit Web Analytics en vervolgens de gegevens samenvoegt om in realtime Dash boards te bieden op basis van die gegevens.

We gaan een tabel maken waarin al onze onbewerkte webverkeersgegevens worden gebruikt. Voer de volgende opdrachten uit in de psql-terminal:

```sql
CREATE TABLE http_request (
  site_id INT,
  ingest_time TIMESTAMPTZ DEFAULT now(),

  url TEXT,
  request_country TEXT,
  ip_address TEXT,

  status_code INT,
  response_time_msec INT
);
```

U gaat ook een tabel maken waarin onze aggregaties per minuut worden bewaard, en een tabel die de positie van de laatste Rollup behoudt. Voer de volgende opdrachten in psql ook uit:

```sql
CREATE TABLE http_request_1min (
  site_id INT,
  ingest_time TIMESTAMPTZ, -- which minute this row represents

  error_count INT,
  success_count INT,
  request_count INT,
  average_response_time_msec INT,
  CHECK (request_count = error_count + success_count),
  CHECK (ingest_time = date_trunc('minute', ingest_time))
);

CREATE INDEX http_request_1min_idx ON http_request_1min (site_id, ingest_time);

CREATE TABLE latest_rollup (
  minute timestamptz PRIMARY KEY,

  CHECK (minute = date_trunc('minute', minute))
);
```

U kunt de zojuist gemaakte tabellen nu bekijken in de lijst met tabellen met de volgende psql-opdracht:

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>Shard-tabellen tussen knoop punten

In een grootschalige-implementatie worden tabel rijen op verschillende knoop punten opgeslagen op basis van de waarde van een door de gebruiker opgegeven kolom. Deze ' distributie kolom ' markeert hoe gegevens worden Shard tussen knoop punten.

Laten we de distributie kolom instellen op site\_-id, de Shard-sleutel. Voer in psql de volgende functies uit:

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

## <a name="generate-sample-data"></a>Voorbeeldgegevens genereren

Nu moet onze server groep gereed zijn om gegevens op te nemen. We kunnen het volgende lokaal uitvoeren vanuit onze `psql` verbinding om continu gegevens in te voegen.

```sql
DO $$
  BEGIN LOOP
    INSERT INTO http_request (
      site_id, ingest_time, url, request_country,
      ip_address, status_code, response_time_msec
    ) VALUES (
      trunc(random()*32), clock_timestamp(),
      concat('http://example.com/', md5(random()::text)),
      ('{China,India,USA,Indonesia}'::text[])[ceil(random()*4)],
      concat(
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2), '.',
        trunc(random()*250 + 2)
      )::inet,
      ('{200,404}'::int[])[ceil(random()*2)],
      5+trunc(random()*150)
    );
    COMMIT;
    PERFORM pg_sleep(random() * 0.25);
  END LOOP;
END $$;
```

De query voegt elke seconde ongeveer acht rijen in. De rijen worden op verschillende werk knooppunten opgeslagen, zoals wordt `site_id`aangegeven door de kolom distributie.

   > [!NOTE]
   > Verlaat de query voor het genereren van gegevens en open een tweede psql-verbinding voor de resterende opdrachten in deze zelf studie.
   >

## <a name="query"></a>Query’s uitvoeren

Met de optie voor het hosten van grootschalige kunnen meerdere knoop punten gelijktijdig query's verwerken voor snelheid. De data base berekent bijvoorbeeld aggregaties zoals SUM en COUNT op worker-knoop punten en combineert de resultaten in een eind antwoord.

Hier volgt een query voor het tellen van webaanvragen per minuut, samen met een aantal statistieken.
Probeer het programma uit te voeren in psql en Bekijk de resultaten.

```sql
SELECT
  site_id,
  date_trunc('minute', ingest_time) as minute,
  COUNT(1) AS request_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
  SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
  SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
FROM http_request
WHERE date_trunc('minute', ingest_time) > now() - '5 minutes'::interval
GROUP BY site_id, minute
ORDER BY minute ASC;
```

## <a name="rolling-up-data"></a>Gegevens inrollen

De vorige query werkt in de vroege stadia prima, maar de prestaties verslechteren naarmate uw gegevens worden geschaald. Zelfs bij gedistribueerde verwerking is het sneller om de gegevens vooraf te berekenen dan om deze herhaaldelijk opnieuw te berekenen.

We kunnen het dash board snel blijven door de onbewerkte gegevens regel matig in een samengevoegde tabel te verdelen. U kunt experimenteren met de aggregatie duur. We hebben een samenvoegings tabel per minuut gebruikt, maar u kunt gegevens in plaats daarvan in vijf, 15 of 60 minuten afsplitsen.

Als u deze rollup eenvoudiger wilt uitvoeren, gaan we deze in een plpgsql-functie plaatsen. Voer deze opdrachten uit in psql om de `rollup_http_request` functie te maken.

```sql
-- initialize to a time long ago
INSERT INTO latest_rollup VALUES ('10-10-1901');

-- function to do the rollup
CREATE OR REPLACE FUNCTION rollup_http_request() RETURNS void AS $$
DECLARE
  curr_rollup_time timestamptz := date_trunc('minute', now());
  last_rollup_time timestamptz := minute from latest_rollup;
BEGIN
  INSERT INTO http_request_1min (
    site_id, ingest_time, request_count,
    success_count, error_count, average_response_time_msec
  ) SELECT
    site_id,
    date_trunc('minute', ingest_time),
    COUNT(1) as request_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 1 ELSE 0 END) as success_count,
    SUM(CASE WHEN (status_code between 200 and 299) THEN 0 ELSE 1 END) as error_count,
    SUM(response_time_msec) / COUNT(1) AS average_response_time_msec
  FROM http_request
  -- roll up only data new since last_rollup_time
  WHERE date_trunc('minute', ingest_time) <@
          tstzrange(last_rollup_time, curr_rollup_time, '(]')
  GROUP BY 1, 2;

  -- update the value in latest_rollup so that next time we run the
  -- rollup it will operate on data newer than curr_rollup_time
  UPDATE latest_rollup SET minute = curr_rollup_time;
END;
$$ LANGUAGE plpgsql;
```

Als onze functie is geïmplementeerd, voert u deze uit om de gegevens samen te voegen:

```sql
SELECT rollup_http_request();
```

En met onze gegevens in een vooraf geaggregeerd formulier kunnen we een query uitvoeren op de samenvouw tabel om hetzelfde rapport te verkrijgen als eerder. Voer de volgende query uit:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Oude gegevens verlopen

De rollups maken query's sneller, maar we moeten nog steeds oude gegevens laten verlopen om ongebonden opslag kosten te voor komen. Bepaal hoe lang u gegevens voor elke granulatie wilt behouden en gebruik standaard query's om verlopen gegevens te verwijderen. In het volgende voor beeld hebben we besloten om onbewerkte gegevens voor één dag en per minuut aggregaties gedurende één maand te houden:

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

In productie kunt u deze query's in een functie laten teruglopen en deze elke minuut aanroepen in een cron-taak.

## <a name="clean-up-resources"></a>Resources opschonen

In de voor gaande stappen hebt u Azure-resources in een server groep gemaakt. Als u deze resources in de toekomst niet meer nodig hebt, verwijdert u de Server groep. Druk op de knop *verwijderen* op de pagina *overzicht* voor uw server groep. Wanneer u hierom wordt gevraagd, bevestigt u de naam van de Server groep en klikt u op de laatste knop *verwijderen* .

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u een grootschalige (Citus)-Server groep inricht. U hebt verbinding gemaakt met het psql, een schema en gedistribueerde gegevens. U hebt geleerd hoe u gegevens in het onbewerkte formulier opvraagt, regel matig de gegevens samenvoegt, query's uitvoert op de geaggregeerde tabellen en oude gegevens verloopt.

Vervolgens leert u meer over de concepten van grootschalige.
> [!div class="nextstepaction"]
> [Grootschalige-knooppunt typen](https://aka.ms/hyperscale-concepts)
