---
title: 'Zelfstudie: Een multi-tenant database ontwerpen - Hyperscale (Citus) - Azure Database voor PostgreSQL'
description: In deze zelfstudie ziet u hoe u gedistribueerde tabellen in Azure Database voor PostgreSQL Hyperscale (Citus) maken, invullen en opvragen.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 17ac29de243f4abfff1cfc83fc6424799978bf0e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74978148"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Zelfstudie: ontwerp een multi-tenant database met Azure Database voor PostgreSQL – Hyperscale (Citus)

In deze zelfstudie gebruikt u Azure Database voor PostgreSQL - Hyperscale (Citus) om te leren hoe u:

> [!div class="checklist"]
> * Een Hyperscale (Citus)-servergroep maken
> * PSQL-hulpprogramma gebruiken om een schema te maken
> * Shardtabellen over knooppunten
> * Voorbeeldgegevens opnemen
> * Querytenantgegevens
> * Gegevens delen tussen huurders
> * Het schema per tenant aanpassen

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>PSQL-hulpprogramma gebruiken om een schema te maken

Eenmaal verbonden met de Azure Database for PostgreSQL - Hyperscale (Citus) met behulp van psql, u een aantal basistaken voltooien. Deze zelfstudie leidt u door het creëren van een web-app waarmee adverteerders hun campagnes kunnen volgen.

Meerdere bedrijven kunnen de app gebruiken, dus laten we een tabel maken om bedrijven vast te houden en een andere voor hun campagnes. Voer in de psql-console de volgende opdrachten uit:

```sql
CREATE TABLE companies (
  id bigserial PRIMARY KEY,
  name text NOT NULL,
  image_url text,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL
);

CREATE TABLE campaigns (
  id bigserial,
  company_id bigint REFERENCES companies (id),
  name text NOT NULL,
  cost_model text NOT NULL,
  state text NOT NULL,
  monthly_budget bigint,
  blacklisted_site_urls text[],
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id)
);
```

Elke campagne betaalt om advertenties weer te geven. Voeg ook een tabel voor advertenties toe door de volgende code in psql weer te geven na de bovenstaande code:

```sql
CREATE TABLE ads (
  id bigserial,
  company_id bigint,
  campaign_id bigint,
  name text NOT NULL,
  image_url text,
  target_url text,
  impressions_count bigint DEFAULT 0,
  clicks_count bigint DEFAULT 0,
  created_at timestamp without time zone NOT NULL,
  updated_at timestamp without time zone NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, campaign_id)
    REFERENCES campaigns (company_id, id)
);
```

Tot slot houden we statistieken bij over klikken en vertoningen voor elke advertentie:

```sql
CREATE TABLE clicks (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  clicked_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_click_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);

CREATE TABLE impressions (
  id bigserial,
  company_id bigint,
  ad_id bigint,
  seen_at timestamp without time zone NOT NULL,
  site_url text NOT NULL,
  cost_per_impression_usd numeric(20,10),
  user_ip inet NOT NULL,
  user_data jsonb NOT NULL,

  PRIMARY KEY (company_id, id),
  FOREIGN KEY (company_id, ad_id)
    REFERENCES ads (company_id, id)
);
```

U de nieuw gemaakte tabellen in de lijst met tabellen nu in psql bekijken door:

```postgres
\dt
```

Multi-tenant toepassingen kunnen uniciteit alleen per tenant afdwingen, daarom bevatten alle primaire en buitenlandse sleutels de bedrijfs-ID.

## <a name="shard-tables-across-nodes"></a>Shardtabellen over knooppunten

Een hyperscale-implementatie slaat tabelrijen op verschillende knooppunten op op basis van de waarde van een door de gebruiker aangewezen kolom. Deze "distributiekolom" markeert welke tenant eigenaar is van welke rijen.

Laten we instellen dat de\_distributiekolom bedrijfs-id is, de tenant-id. Voer in psql de volgende functies uit:

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

## <a name="ingest-sample-data"></a>Voorbeeldgegevens opnemen

Buiten psql nu, in de normale opdrachtregel, download voorbeeldgegevenssets:

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

Terug in psql, bulk laden van de gegevens. Zorg ervoor dat u psql uitvoert in dezelfde map waar u de gegevensbestanden hebt gedownload.

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

Deze gegevens worden nu verspreid over werknemersknooppunten.

## <a name="query-tenant-data"></a>Querytenantgegevens

Wanneer de toepassing gegevens opvraagt voor één tenant, kan de database de query uitvoeren op één werknemersknooppunt. Query's met één tenant filteren op één tenant-id. Bijvoorbeeld de volgende queryfilters `company_id = 5` voor advertenties en vertoningen. Probeer het uit te voeren in psql om de resultaten te zien.

```sql
SELECT a.campaign_id,
       RANK() OVER (
         PARTITION BY a.campaign_id
         ORDER BY a.campaign_id, count(*) desc
       ), count(*) as n_impressions, a.id
  FROM ads as a
  JOIN impressions as i
    ON i.company_id = a.company_id
   AND i.ad_id      = a.id
 WHERE a.company_id = 5
GROUP BY a.campaign_id, a.id
ORDER BY a.campaign_id, n_impressions desc;
```

## <a name="share-data-between-tenants"></a>Gegevens delen tussen huurders

Tot nu toe zijn alle `company_id`tabellen gedistribueerd door , maar sommige gegevens zijn van nature niet "behoren" tot een tenant in het bijzonder, en kan worden gedeeld. Alle bedrijven in het voorbeeldadvertentieplatform willen bijvoorbeeld geografische informatie voor hun doelgroep op basis van IP-adressen.

Maak een tabel om gedeelde geografische informatie vast te houden. Voer de volgende opdrachten uit in psql:

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

Maak `geo_ips` vervolgens een "referentietabel" om een kopie van de tabel op elk werknemersknooppunt op te slaan.

```sql
SELECT create_reference_table('geo_ips');
```

Laad het met voorbeeldgegevens. Vergeet niet om deze opdracht uit te voeren in psql vanuit de map waar u de gegevensset hebt gedownload.

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

Het samenvoegen van\_de kliktabel met geo ips is efficiënt op alle knooppunten.
Hier is een join om de locaties te vinden van iedereen die op advertentie heeft geklikt
290. Probeer de query in psql uit te voeren.

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>Het schema per tenant aanpassen

Elke huurder kan nodig zijn om speciale informatie niet nodig door anderen op te slaan. Alle tenants delen echter een gemeenschappelijke infrastructuur met een identiek databaseschema. Waar kunnen de extra gegevens naartoe?

Een truc is om een open kolom type zoals PostgreSQL's JSONB te gebruiken.  Ons schema heeft een JSONB-veld met `clicks` de naam `user_data`.
Een bedrijf (bijvoorbeeld bedrijf vijf), kan de kolom gebruiken om bij te houden of de gebruiker zich op een mobiel apparaat bevindt.

Hier is een query om te zien wie meer klikt: mobiel of traditionele bezoekers.

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

We kunnen deze query voor één bedrijf optimaliseren door een [gedeeltelijke index te](https://www.postgresql.org/docs/current/static/indexes-partial.html)maken.

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

Meer in het algemeen kunnen we een [GIN-indexen](https://www.postgresql.org/docs/current/static/gin-intro.html) maken op elke sleutel en waarde binnen de kolom.

```sql
CREATE INDEX click_user_data
ON clicks USING gin (user_data);

-- this speeds up queries like, "which clicks have
-- the is_mobile key present in user_data?"

SELECT id
  FROM clicks
 WHERE user_data ? 'is_mobile'
   AND company_id = 5;
```

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u Azure-resources in een servergroep gemaakt. Als u deze bronnen in de toekomst niet meer nodig verwacht, verwijdert u de servergroep. Druk op de knop *Verwijderen* op de pagina *Overzicht* voor uw servergroep. Bevestig de naam van de servergroep wanneer u daarom wordt gevraagd op een pop-uppagina en klik op de laatste knop *Verwijderen.*

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u geleerd hoe u een Servergroep Hyperscale (Citus) indient. U hebt ermee verbinding gemaakt met psql, een schema gemaakt en gegevens gedistribueerd. U hebt geleerd om gegevens op te vragen, zowel binnen als tussen tenants, en om het schema per tenant aan te passen.

Leer vervolgens over de concepten van hyperscale.
> [!div class="nextstepaction"]
> [Typen hyperschaalknooppunttypen](https://aka.ms/hyperscale-concepts)
