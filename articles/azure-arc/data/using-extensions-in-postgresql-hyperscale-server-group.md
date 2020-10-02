---
title: PostgreSQL-extensies gebruiken
description: PostgreSQL-extensies gebruiken
titleSuffix: Azure Arc enabled data services
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 58386786266c48c6e721094f9f2837709bb684e5
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631763"
---
# <a name="use-postgresql-extensions-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>PostgreSQL-extensies gebruiken in uw Azure-PostgreSQL grootschalige-Server groep

PostgreSQL is de beste optie wanneer u deze gebruikt met uitbrei dingen. Eigenlijk is een belang rijk element van onze eigen grootschalige-functionaliteit de door micro soft verschafte `citus` uitbrei ding die standaard wordt geïnstalleerd, waardoor post gres transparante gegevens kan Shard op meerdere knoop punten.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="list-of-extensions"></a>Lijst met extensies
Naast de uitbrei dingen in [`contrib`](https://www.postgresql.org/docs/12/contrib.html) , is de lijst met uitbrei dingen die aanwezig zijn in de containers van uw Azure-postgresql grootschalige-Server groep:
- `citus`, v: 9,4
- `pg_cron`, v: 1,2
- `plpgsql`, v: 1,0
- `postgis`, v: 3.0.2
- `plv8`, v: 2.3.14

In deze lijst worden de overtijd en updates die in dit document worden geplaatst, gewerkt. Het is nog niet mogelijk om uitbrei dingen toe te voegen dan hierboven vermeld.

Deze hand leiding gaat in een scenario over het gebruik van twee van deze uitbrei dingen:
- [PostGIS](https://postgis.net/)
- [`pg_cron`](https://github.com/citusdata/pg_cron)


## <a name="manage-extensions"></a>Extensies beheren

### <a name="enable-extensions"></a>Uitbrei dingen inschakelen
Deze stap is niet nodig voor de uitbrei dingen die deel uitmaken van `contrib` .
De algemene indeling van de opdracht voor het inschakelen van extensies is:

#### <a name="enable-an-extension-at-the-creation-time-of-a-server-group"></a>Een uitbrei ding inschakelen op de aanmaak tijd van een server groep:
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>
```
#### <a name="enable-an-extension-on-an-instance-that-already-exists"></a>Een uitbrei ding inschakelen voor een exemplaar dat al bestaat:
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>
```

#### <a name="get-the-list-of-extensions-enabled"></a>De lijst met ingeschakelde uitbrei dingen ophalen:
Voer een van de volgende opdrachten uit.

##### <a name="with-azdata"></a>Met azdata
```console
azdata arc postgres server show -n <server group name>
```
Schuif in de uitvoer en Let op de engine\extensions-secties in de specificaties van uw server groep. Bijvoorbeeld:
```console
"engine": {
      "extensions": [
        {
          "name": "citus"
        },
        {
          "name": "pg_cron"
        }
      ]
    },
```
##### <a name="with-kubectl"></a>Met kubectl
```console
kubectl describe postgresql-12s/postgres02
```
Schuif in de uitvoer en Let op de engine\extensions-secties in de specificaties van uw server groep. Bijvoorbeeld:
```console
Engine:
    Extensions:
      Name:  citus
      Name:  pg_cron
```


### <a name="create-extensions"></a>Extensies maken:
Maak verbinding met uw server groep met het client hulpprogramma van uw keuze en voer de standaard PostgreSQL-query uit:
```console
CREATE EXTENSION <extension name>;
```

### <a name="get-the-list-of-extension-created-in-your-server-group"></a>Haal de lijst met extensies op die zijn gemaakt in uw server groep:
Maak verbinding met uw server groep met het client hulpprogramma van uw keuze en voer de standaard PostgreSQL-query uit:
```console
select * from pg_extension;
```

### <a name="drop-an-extension-from-your-server-group"></a>Een uitbrei ding verwijderen uit uw server groep:
Maak verbinding met uw server groep met het client hulpprogramma van uw keuze en voer de standaard PostgreSQL-query uit:
```console
drop extension <extension name>;
```

## <a name="use-the-postgis-and-the-pg_cron-extensions"></a>De PostGIS-en Pg_cron-extensies gebruiken

### <a name="the-postgis-extension"></a>De PostGIS-extensie

U kunt de uitbrei ding PostGIS inschakelen voor een bestaande server groep of een nieuwe maken met de extensie die al is ingeschakeld:

**Een uitbrei ding in te scha kelen op het moment van aanmaak van een server groep:**
```console
azdata arc postgres server create -n <name of your postgresql server group> --extensions <extension names>

#Example:
azdata arc postgres server create -n pg2 -w 2 --extensions postgis
```

**Een uitbrei ding inschakelen voor een exemplaar dat al bestaat:**
```console
azdata arc postgres server edit -n <name of your postgresql server group> --extensions <extension names>

#Example:
azdata arc postgres server edit --extensions postgis -n pg2
```

Als u wilt controleren welke uitbrei dingen zijn geïnstalleerd, gebruikt u de onderstaande standaard PostgreSQL-opdracht nadat u verbinding hebt gemaakt met het exemplaar met uw favoriete PostgreSQL-client hulpprogramma, zoals Azure Data Studio:
```console
select * from pg_extension;
```

Voor een PostGIS-voor beeld moet u eerst [voorbeeld gegevens](http://duspviz.mit.edu/tutorials/intro-postgis/) van het departement van de MIT van het stads onderzoek & planning ophalen. Mogelijk moet u uitvoeren `apt-get install unzip` om unzip te installeren wanneer u de virtuele machine gebruikt voor het testen.

```console
wget http://duspviz.mit.edu/_assets/data/intro-postgis-datasets.zip
unzip intro-postgis-datasets.zip
```

We gaan nu verbinding maken met de data base en de uitbrei ding PostGIS:

```console
CREATE EXTENSION postgis;
```

> [!NOTE]
> Als u een van de uitbrei dingen in het pakket wilt gebruiken (bijvoorbeeld,,... `postgis` `postgis_raster` ), `postgis_topology` `postgis_sfcgal` `fuzzystrmatch` moet u eerst de postgis-extensie maken en vervolgens de andere extensie maken. Bijvoorbeeld: `CREATE EXTENSION postgis` ; `CREATE EXTENSION postgis_raster` ;

En maak het schema:

```sql
CREATE TABLE coffee_shops (
  id serial NOT NULL,
  name character varying(50),
  address character varying(50),
  city character varying(50),
  state character varying(50),
  zip character varying(10),
  lat numeric,
  lon numeric,
  geom geometry(POINT,4326)
);
CREATE INDEX coffee_shops_gist ON coffee_shops USING gist (geom);
```

Nu kunnen we PostGIS combi neren met de functionaliteit voor uitschalen door de coffee_shops tabel gedistribueerd te maken:

```sql
SELECT create_distributed_table('coffee_shops', 'id');
```

Laten we een aantal gegevens laden:

```console
\copy coffee_shops(id,name,address,city,state,zip,lat,lon) from cambridge_coffee_shops.csv CSV HEADER;
```

En vul het `geom` veld in met de juiste versleutelde breedte graad en lengte graad in het `geometry` gegevens type postgis:

```sql
UPDATE coffee_shops SET geom = ST_SetSRID(ST_MakePoint(lon,lat),4326);
```

Nu kunnen we de koffie winkels weer geven die het dichtst bij MIT (77 Massachusetts Ave op 42,359055,-71,093500):

```sql
SELECT name, address FROM coffee_shops ORDER BY geom <-> ST_SetSRID(ST_MakePoint(-71.093500,42.359055),4326);
```


### <a name="the-pg_cron-extension"></a>De uitbrei ding pg_cron

We gaan nu een `pg_cron` postgresql-server groep inschakelen, naast postgis:

```console
azdata postgres server update -n pg2 -ns arc --extensions postgis,pg_cron
```

Houd er rekening mee dat de knoop punten opnieuw worden opgestart en de extra uitbrei dingen moeten worden geïnstalleerd. Dit kan 2-3 minuten duren.

Er kan nu opnieuw verbinding worden gemaakt en de `pg_cron` uitbrei ding te maken:

```sql
CREATE EXTENSION pg_cron;
```

In het kader van test doeleinden kunt u een tabel maken `the_best_coffee_shop` die een wille keurige naam uit onze eerdere `coffee_shops` tabel haalt en de inhoud van de tabel instelt:

```sql
CREATE TABLE the_best_coffee_shop(name text);
```

We kunnen `cron.schedule` plus enkele SQL-instructies gebruiken om een wille keurige tabel naam op te halen (Let op het gebruik van een tijdelijke tabel om een gedistribueerd query resultaat op te slaan) en sla het op in `the_best_coffee_shop` :

```sql
SELECT cron.schedule('* * * * *', $$
  TRUNCATE the_best_coffee_shop;
  CREATE TEMPORARY TABLE tmp AS SELECT name FROM coffee_shops ORDER BY random() LIMIT 1;
  INSERT INTO the_best_coffee_shop SELECT * FROM tmp;
  DROP TABLE tmp;
$$);
```

En nu, één keer per minuut, krijgen we een andere naam:

```sql
SELECT * FROM the_best_coffee_shop;
```

```console
      name
-----------------
 B & B Snack Bar
(1 row)
```

Raadpleeg het [Leesmij-bestand van pg_cron](https://github.com/citusdata/pg_cron) voor volledige informatie over de syntaxis.

>[!NOTE]
>Het wordt niet ondersteund om de `citus` uitbrei ding te verwijderen. De `citus` uitbrei ding is vereist om de grootschalige-ervaring te bieden.

## <a name="next-steps"></a>Volgende stappen:
- Documentatie lezen over [plv8](https://plv8.github.io/)
- Documentatie lezen over [postgis](https://postgis.net/)
- Documentatie lezen op [`pg_cron`](https://github.com/citusdata/pg_cron)
