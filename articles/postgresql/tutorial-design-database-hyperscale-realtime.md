---
title: 'Zelfstudie: Ontwerp een realtime dashboard - Hyperscale (Citus) - Azure Database voor PostgreSQL'
description: In deze zelfstudie ziet u hoe u gedistribueerde tabellen in Azure Database voor PostgreSQL Hyperscale (Citus) maken, invullen en opvragen.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: f4eeb646de8b68c2c8d30586d0c75cece5317e40
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76716325"
---
# <a name="tutorial-design-a-real-time-analytics-dashboard-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Zelfstudie: Ontwerp een realtime analysedashboard met Azure Database voor PostgreSQL – Hyperscale (Citus)

In deze zelfstudie gebruikt u Azure Database voor PostgreSQL - Hyperscale (Citus) om te leren hoe u:

> [!div class="checklist"]
> * Een Hyperscale (Citus)-servergroep maken
> * PSQL-hulpprogramma gebruiken om een schema te maken
> * Shardtabellen over knooppunten
> * Voorbeeldgegevens genereren
> * Rollups uitvoeren
> * Onbewerkte en geaggregeerde gegevens opvragen
> * Gegevens verlopen

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>PSQL-hulpprogramma gebruiken om een schema te maken

Eenmaal verbonden met de Azure Database for PostgreSQL - Hyperscale (Citus) met behulp van psql, u een aantal basistaken voltooien. Deze zelfstudie leidt u door het innemen van verkeersgegevens van webanalytics en vervolgens de gegevens oprollen om realtime dashboards te bieden op basis van die gegevens.

Laten we een tabel maken die al onze ruwe webverkeersgegevens verbruikt. Voer de volgende opdrachten uit in de psql-terminal:

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

We gaan ook een tabel maken die onze aggregaten per minuut vasthoudt, en een tabel die de positie van onze laatste rollup behoudt. Voer ook de volgende opdrachten uit in psql:

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

Met deze psql-opdracht u de nieuw gemaakte tabellen nu in de lijst met tabellen bekijken:

```postgres
\dt
```

## <a name="shard-tables-across-nodes"></a>Shardtabellen over knooppunten

Een hyperscale-implementatie slaat tabelrijen op verschillende knooppunten op op basis van de waarde van een door de gebruiker aangewezen kolom. Deze "distributiekolom" markeert hoe gegevens worden geshard tussen knooppunten.

Laten we de distributiekolom instellen\_als site-id, de shardtoets. Voer in psql de volgende functies uit:

  ```sql
SELECT create_distributed_table('http_request',      'site_id');
SELECT create_distributed_table('http_request_1min', 'site_id');
```

## <a name="generate-sample-data"></a>Voorbeeldgegevens genereren

Nu moet onze servergroep klaar zijn om wat gegevens in te nemen. We kunnen het volgende lokaal `psql` uitvoeren vanuit onze verbinding om continu gegevens in te voegen.

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

De query voegt ongeveer acht rijen per seconde toe. De rijen worden opgeslagen op verschillende werkknooppunten `site_id`zoals aangegeven door de distributiekolom.

   > [!NOTE]
   > Laat de query voor het genereren van gegevens uitvoeren en open een tweede psql-verbinding voor de resterende opdrachten in deze zelfstudie.
   >

## <a name="query"></a>Query’s uitvoeren

Met de hyperscale hosting-optie kunnen meerdere knooppunten query's parallel verwerken voor snelheid. De database berekent bijvoorbeeld aggregaten zoals SOM en TELLING op werknemersknooppunten en combineert de resultaten tot een definitief antwoord.

Hier is een query om webaanvragen per minuut te tellen, samen met een paar statistieken.
Probeer het uit te voeren in psql en observeer de resultaten.

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

## <a name="rolling-up-data"></a>Gegevens oprollen

De vorige query werkt prima in de vroege stadia, maar de prestaties degradeert als uw gegevens schalen. Zelfs met gedistribueerde verwerking is het sneller om de gegevens vooraf te berekenen dan om de gegevens herhaaldelijk opnieuw te berekenen.

We kunnen ervoor zorgen dat ons dashboard snel blijft door de ruwe gegevens regelmatig in een geaggregeerde tabel op te rollen. U experimenteren met de aggregatieduur. We gebruikten een aggregatietabel per minuut, maar u gegevens in 5, 15 of 60 minuten opsplitsen.

Om deze roll-up gemakkelijker uit te voeren, gaan we het in een plpgsql-functie plaatsen. Voer deze opdrachten uit in `rollup_http_request` psql om de functie te maken.

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

Met onze functie op zijn plaats, uit te voeren om het oprollen van de gegevens:

```sql
SELECT rollup_http_request();
```

En met onze gegevens in een vooraf geaggregeerde vorm kunnen we de rollup tabel opvragen om hetzelfde rapport te krijgen als eerder. Voer de volgende query uit:

```sql
SELECT site_id, ingest_time as minute, request_count,
       success_count, error_count, average_response_time_msec
  FROM http_request_1min
 WHERE ingest_time > date_trunc('minute', now()) - '5 minutes'::interval;
 ```

## <a name="expiring-old-data"></a>Oude gegevens verlopen

De rollups maken query's sneller, maar we moeten nog steeds oude gegevens verlopen om onbegrensde opslagkosten te voorkomen. Bepaal hoe lang u gegevens voor elke granulariteit wilt bewaren en gebruik standaardquery's om verlopen gegevens te verwijderen. In het volgende voorbeeld hebben we besloten om ruwe gegevens één dag te bewaren en aggregaties per minuut gedurende een maand:

```sql
DELETE FROM http_request WHERE ingest_time < now() - interval '1 day';
DELETE FROM http_request_1min WHERE ingest_time < now() - interval '1 month';
```

In de productie u deze query's in een functie verpakken en deze elke minuut in een cron-taak aanroepen.

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u Azure-resources in een servergroep gemaakt. Als u deze bronnen in de toekomst niet meer nodig verwacht, verwijdert u de servergroep. Druk op de knop *Verwijderen* op de pagina *Overzicht* voor uw servergroep. Bevestig de naam van de servergroep wanneer u daarom wordt gevraagd op een pop-uppagina en klik op de laatste knop *Verwijderen.*

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een Servergroep Hyperscale (Citus) indient. U hebt ermee verbinding gemaakt met psql, een schema gemaakt en gegevens gedistribueerd. U hebt geleerd om gegevens in de onbewerkte vorm op te vragen, die gegevens regelmatig samen te voegen, de samengevoegde tabellen op te vragen en oude gegevens te verlopen.

Leer vervolgens over de concepten van hyperscale.
> [!div class="nextstepaction"]
> [Typen hyperschaalknooppunttypen](https://aka.ms/hyperscale-concepts)
