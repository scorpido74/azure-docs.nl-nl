---
title: 'Zelf studie: een multi tenant-data base ontwerpen-grootschalige (Citus)-Azure Database for PostgreSQL'
description: In deze zelf studie ziet u hoe u gedistribueerde tabellen maakt, vult en doorzoekt op Azure Database for PostgreSQL grootschalige (Citus).
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.custom: mvc
ms.devlang: azurecli
ms.topic: tutorial
ms.date: 05/14/2019
ms.openlocfilehash: 17ac29de243f4abfff1cfc83fc6424799978bf0e
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978148"
---
# <a name="tutorial-design-a-multi-tenant-database-by-using-azure-database-for-postgresql--hyperscale-citus"></a>Zelf studie: een Data Base met meerdere tenants ontwerpen met behulp van Azure Database for PostgreSQL – grootschalige (Citus)

In deze zelf studie gebruikt u Azure Database for PostgreSQL-grootschalige (Citus) voor meer informatie over het volgende:

> [!div class="checklist"]
> * Een Hyperscale (Citus)-servergroep maken
> * Het hulp programma psql gebruiken om een schema te maken
> * Shard-tabellen tussen knoop punten
> * Voorbeeldgegevens opnemen
> * Query's uitvoeren op Tenant gegevens
> * Gegevens delen tussen tenants
> * Het schema per Tenant aanpassen

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [azure-postgresql-hyperscale-create-db](../../includes/azure-postgresql-hyperscale-create-db.md)]

## <a name="use-psql-utility-to-create-a-schema"></a>Het hulp programma psql gebruiken om een schema te maken

Nadat u verbinding hebt gemaakt met de Azure Database for PostgreSQL-grootschalige (Citus) met behulp van psql, kunt u een aantal basis taken volt ooien. In deze zelf studie wordt u begeleid bij het maken van een web-app waarmee adverteerders hun campagnes kunnen volgen.

Meerdere bedrijven kunnen de app gebruiken, dus we maken een tabel om bedrijven en een andere voor hun campagnes te bewaren. Voer de volgende opdrachten uit in de psql-console:

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

Elke campagne betaalt voor het uitvoeren van advertenties. Voeg ook een tabel toe voor advertenties door de volgende code uit te voeren in psql na de bovenstaande code:

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

Ten slotte volgen we statistieken over klikken en hits voor elke advertentie:

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

U kunt de zojuist gemaakte tabellen in de lijst met tabellen nu in psql zien door de volgende handelingen uit te voeren:

```postgres
\dt
```

Toepassingen met meerdere tenants kunnen alleen uniekheid afdwingen per Tenant. dat is de reden waarom alle primaire en refererende sleutels de bedrijfs-ID bevatten.

## <a name="shard-tables-across-nodes"></a>Shard-tabellen tussen knoop punten

In een grootschalige-implementatie worden tabel rijen op verschillende knoop punten opgeslagen op basis van de waarde van een door de gebruiker opgegeven kolom. Deze distributie kolom markeert welke Tenant eigenaar is van welke rijen.

Laten we de distributie kolom instellen als bedrijfs\_-id, de Tenant-id. Voer in psql de volgende functies uit:

```sql
SELECT create_distributed_table('companies',   'id');
SELECT create_distributed_table('campaigns',   'company_id');
SELECT create_distributed_table('ads',         'company_id');
SELECT create_distributed_table('clicks',      'company_id');
SELECT create_distributed_table('impressions', 'company_id');
```

## <a name="ingest-sample-data"></a>Voorbeeldgegevens opnemen

Down load psql nu in de normale opdracht regel het volgende:

```bash
for dataset in companies campaigns ads clicks impressions geo_ips; do
  curl -O https://examples.citusdata.com/mt_ref_arch/${dataset}.csv
done
```

In psql kunt u de gegevens bulksgewijs laden. Zorg ervoor dat u psql uitvoert in dezelfde map waar u de gegevens bestanden hebt gedownload.

```sql
SET CLIENT_ENCODING TO 'utf8';

\copy companies from 'companies.csv' with csv
\copy campaigns from 'campaigns.csv' with csv
\copy ads from 'ads.csv' with csv
\copy clicks from 'clicks.csv' with csv
\copy impressions from 'impressions.csv' with csv
```

Deze gegevens worden nu verdeeld over werk knooppunten.

## <a name="query-tenant-data"></a>Query's uitvoeren op Tenant gegevens

Wanneer de toepassing gegevens aanvraagt voor één Tenant, kan de data base de query uitvoeren op één worker-knoop punt. Query's met één Tenant filteren op één Tenant-ID. De volgende query filtert bijvoorbeeld `company_id = 5` voor advertenties en impressies. Probeer het programma uit te voeren in psql om de resultaten te bekijken.

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

## <a name="share-data-between-tenants"></a>Gegevens delen tussen tenants

Tot nu toe zijn alle tabellen gedistribueerd door `company_id`, maar sommige gegevens maken geen deel uit van een wille keurige Tenant en kunnen worden gedeeld. Zo kan het voor komen dat alle bedrijven in het voor beeld-ad-platform geografische gegevens voor hun doel groep willen ophalen op basis van IP-adressen.

Een tabel maken voor het opslaan van gedeelde geografische informatie. Voer de volgende opdrachten uit in psql:

```sql
CREATE TABLE geo_ips (
  addrs cidr NOT NULL PRIMARY KEY,
  latlon point NOT NULL
    CHECK (-90  <= latlon[0] AND latlon[0] <= 90 AND
           -180 <= latlon[1] AND latlon[1] <= 180)
);
CREATE INDEX ON geo_ips USING gist (addrs inet_ops);
```

Maak vervolgens `geo_ips` een ' verwijzings tabel ' om een kopie van de tabel op elk worker-knoop punt op te slaan.

```sql
SELECT create_reference_table('geo_ips');
```

Laad deze met voorbeeld gegevens. Vergeet niet om deze opdracht in psql uit te voeren vanuit de map waar u de gegevensset hebt gedownload.

```sql
\copy geo_ips from 'geo_ips.csv' with csv
```

Het toevoegen van de klikkens tabel met geo\_ip's is efficiënt op alle knoop punten.
Hier volgt een koppeling om de locaties te vinden van iedereen die op ad heeft geklikt
290. Probeer de query uit te voeren in psql.

```sql
SELECT c.id, clicked_at, latlon
  FROM geo_ips, clicks c
 WHERE addrs >> c.user_ip
   AND c.company_id = 5
   AND c.ad_id = 290;
```

## <a name="customize-the-schema-per-tenant"></a>Het schema per Tenant aanpassen

Elke Tenant moet mogelijk speciale gegevens opslaan die niet nodig zijn voor anderen. Alle tenants delen echter een gemeen schappelijke infra structuur met een identiek database schema. Waar kunnen de extra gegevens worden geplaatst?

Een slag is het gebruik van een open-beëindigd kolom Type, zoals PostgreSQL van JSONB.  Ons schema bevat een JSONB-veld in `clicks` met de naam `user_data`.
Een bedrijf (bedrijf 5), kan de kolom gebruiken om bij te houden of de gebruiker zich op een mobiel apparaat bevindt.

Hier volgt een query om te ontdekken wie er meer te weten komt: mobiele of traditionele bezoekers.

```sql
SELECT
  user_data->>'is_mobile' AS is_mobile,
  count(*) AS count
FROM clicks
WHERE company_id = 5
GROUP BY user_data->>'is_mobile'
ORDER BY count DESC;
```

We kunnen deze query voor één bedrijf optimaliseren door een [gedeeltelijke index](https://www.postgresql.org/docs/current/static/indexes-partial.html)te maken.

```sql
CREATE INDEX click_user_data_is_mobile
ON clicks ((user_data->>'is_mobile'))
WHERE company_id = 5;
```

Meer in het algemeen kunnen we een [eginnen-indexen](https://www.postgresql.org/docs/current/static/gin-intro.html) maken voor elke sleutel en waarde in de kolom.

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

In de voor gaande stappen hebt u Azure-resources in een server groep gemaakt. Als u deze resources in de toekomst niet meer nodig hebt, verwijdert u de Server groep. Druk op de knop *verwijderen* op de pagina *overzicht* voor uw server groep. Wanneer u hierom wordt gevraagd, bevestigt u de naam van de Server groep en klikt u op de laatste knop *verwijderen* .

## <a name="next-steps"></a>Volgende stappen

In deze zelf studie hebt u geleerd hoe u een grootschalige (Citus)-Server groep inricht. U hebt verbinding gemaakt met het psql, een schema en gedistribueerde gegevens. U hebt geleerd hoe u gegevens in en tussen tenants kunt opvragen en het schema kunt aanpassen per Tenant.

Vervolgens leert u meer over de concepten van grootschalige.
> [!div class="nextstepaction"]
> [Grootschalige-knooppunt typen](https://aka.ms/hyperscale-concepts)
