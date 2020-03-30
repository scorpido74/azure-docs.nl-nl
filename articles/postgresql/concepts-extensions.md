---
title: Extensies - Azure Database voor PostgreSQL - Single Server
description: Meer informatie over de beschikbare Postgres-extensies in Azure Database voor PostgreSQL - Single Server
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/13/2020
ms.openlocfilehash: a12738f5de783c8a34718b8d9cb4bbf54f230589
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77201268"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>PostgreSQL-extensies in Azure Database voor PostgreSQL - Single Server
PostgreSQL biedt de mogelijkheid om de functionaliteit van uw database uit te breiden met behulp van extensies. Extensies bundelen meerdere gerelateerde SQL-objecten samen in één pakket dat met één opdracht kan worden geladen of verwijderd uit uw database. Nadat ze in de database zijn geladen, werken extensies als ingebouwde functies.

## <a name="how-to-use-postgresql-extensions"></a>PostgreSQL-extensies gebruiken
PostgreSQL-extensies moeten in uw database worden geïnstalleerd voordat u ze gebruiken. Als u een bepaalde extensie wilt installeren, voert u de opdracht [UITBREIDING MAKEN](https://www.postgresql.org/docs/current/sql-createextension.html) uit psql uit om de verpakte objecten in uw database te laden.

Azure Database voor PostgreSQL ondersteunt een subset van belangrijke extensies zoals hieronder vermeld. Deze informatie is ook `SELECT * FROM pg_available_extensions;`beschikbaar door het uitvoeren van . Extensies die verder gaan dan de genoemde worden niet ondersteund. U uw eigen extensie niet maken in Azure Database voor PostgreSQL.

## <a name="postgres-11-extensions"></a>Postgres 11 extensies

De volgende extensies zijn beschikbaar in Azure Database voor PostgreSQL-servers met Postgres versie 11. 

> [!div class="mx-tableFixed"]
> | **Toestelnummer**| **Versie van de extensie** | **Beschrijving** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Wordt gebruikt om een adres te ontlopen in samenstellende elementen. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Voorbeeld van het voorbeeld van de Amerikaanse gegevensset adres|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | ondersteuning voor het indexeren van gemeenschappelijke gegevenstypen in GIN|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | ondersteuning voor het indexeren van veelvoorkomende gegevenstypen in GiST|
> |[citekst](https://www.postgresql.org/docs/11/citext.html)                       | 1.5             | gegevenstype voor hoofdlettergevoelige tekentekenreeksen|
> |[Kubus](https://www.postgresql.org/docs/11/cube.html)                         | 1.4             | gegevenstype voor multidimensionale kubussen|
> |[dblink dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | verbinding maken met andere PostgreSQL-databases vanuit een database|
> |[dict_int](https://www.postgresql.org/docs/11/dict-int.html)                     | 1.0             | sjabloon voor tekstzoeken naar gehele getallen|
> |[aardafstand](https://www.postgresql.org/docs/11/earthdistance.html)                | 1.1             | grote cirkelafstanden op het oppervlak van de aarde berekenen|
> |[fuzzystrmatch fuzzystrmatch](https://www.postgresql.org/docs/11/fuzzystrmatch.html)                | 1.1             | overeenkomsten en afstand tussen tekenreeksen bepalen|
> |[Hstore](https://www.postgresql.org/docs/11/hstore.html)                       | 1.5             | gegevenstype voor het opslaan van sets (sleutel, waarde)paren|
> |[hypopg hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | Hypothetische indexen voor PostgreSQL|
> |[intarray intarray](https://www.postgresql.org/docs/11/intarray.html)                     | 1.2             | functies, operatoren en indexondersteuning voor 1D-arrays van gehele getallen|
> |[Isn](https://www.postgresql.org/docs/11/isn.html)                          | 1.2             | gegevenstypen voor internationale productnummernormen|
> |[ltree ltree](https://www.postgresql.org/docs/11/ltree.html)                        | 1.1             | gegevenstype voor hiërarchische structuurachtige structuren|
> |[Orafce](https://github.com/orafce/orafce)                       | 3.7             | Functies en operatoren die een subset van functies en pakketten van commerciële RDBMS emuleren|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.3.1             | biedt controlefunctionaliteit|
> |[pgcrypto pgcrypto](https://www.postgresql.org/docs/11/pgcrypto.html)                     | 1.3             | cryptografische functies|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | pgRouting-extensie|
> |[pgrowlocks](https://www.postgresql.org/docs/11/pgrowlocks.html)                   | 1.2             | vergrendelingsgegevens op rijniveau weergeven|
> |[pgstattuple pgstattuple](https://www.postgresql.org/docs/11/pgstattuple.html)                  | 1.5             | statistieken op tuple-niveau weergeven|
> |[pg_buffercache](https://www.postgresql.org/docs/11/pgbuffercache.html)               | 1.3             | de gedeelde buffercache onderzoeken|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | Extensie voor het beheren van partitietabellen op tijd of id|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | prewarmterelatiegegevens|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/pgstatstatements.html)           | 1.6             | uitvoeringsstatistieken van alle SQL-instructies bijhouden|
> |[pg_trgm](https://www.postgresql.org/docs/11/pgtrgm.html)                      | 1.4             | tekstgelijkenismeting en indexzoeken op basis van trigrammen|
> |[plpgsql](https://www.postgresql.org/docs/11/plpgsql.html)                      | 1.0             | PL/pgSQL-proceduretaal|
> |[plv8 plv8](https://plv8.github.io/)                         | 2.3.11          | VERTROUWDE proceduretaal pl/JavaScript (v8)|
> |[Postgis](https://www.postgis.net/)                      | 2.5.1           | Ruimtelijke typen en functies van PostGIS-geometrie, geografie en raster|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL-functies|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS tijger geocoder en omgekeerde geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | PostGIS-topologie ruimtelijke typen en functies|
> |[postgres_fdw](https://www.postgresql.org/docs/11/postgres-fdw.html)                 | 1.0             | foreign-data wrapper voor externe PostgreSQL-servers|
> |[tablefunc tablefunc](https://www.postgresql.org/docs/11/tablefunc.html)                    | 1.0             | functies die hele tabellen manipuleren, inclusief kruistabel|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.3.2             | Hiermee kunnen schaalbare inserts en complexe query's voor tijdreeksgegevens worden gemaakt|
> |[Unaccent](https://www.postgresql.org/docs/11/unaccent.html)                     | 1.1             | tekstzoekwoordenboek dat accenten verwijdert|
> |[uuid-ossp](https://www.postgresql.org/docs/11/uuid-ossp.html)                    | 1.1             | universeel unieke id's (UUId's) genereren|

## <a name="postgres-10-extensions"></a>Postgres 10 uitbreidingen 

De volgende extensies zijn beschikbaar in Azure Database voor PostgreSQL-servers met Postgres versie 10.

> [!div class="mx-tableFixed"]
> | **Toestelnummer**| **Versie van de extensie** | **Beschrijving** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Wordt gebruikt om een adres te ontlopen in samenstellende elementen. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Voorbeeld van het voorbeeld van de Amerikaanse gegevensset adres|
> |[btree_gin](https://www.postgresql.org/docs/10/btree-gin.html)                    | 1.3             | ondersteuning voor het indexeren van gemeenschappelijke gegevenstypen in GIN|
> |[btree_gist](https://www.postgresql.org/docs/10/btree-gist.html)                   | 1.5             | ondersteuning voor het indexeren van veelvoorkomende gegevenstypen in GiST|
> |[chkpass](https://www.postgresql.org/docs/10/chkpass.html)                       | 1.0             | gegevenstype voor automatisch versleutelde wachtwoorden|
> |[citekst](https://www.postgresql.org/docs/10/citext.html)                       | 1.4             | gegevenstype voor hoofdlettergevoelige tekentekenreeksen|
> |[Kubus](https://www.postgresql.org/docs/10/cube.html)                         | 1.2             | gegevenstype voor multidimensionale kubussen|
> |[dblink dblink](https://www.postgresql.org/docs/10/dblink.html)                       | 1.2             | verbinding maken met andere PostgreSQL-databases vanuit een database|
> |[dict_int](https://www.postgresql.org/docs/10/dict-int.html)                     | 1.0             | sjabloon voor tekstzoeken naar gehele getallen|
> |[aardafstand](https://www.postgresql.org/docs/10/earthdistance.html)                | 1.1             | grote cirkelafstanden op het oppervlak van de aarde berekenen|
> |[fuzzystrmatch fuzzystrmatch](https://www.postgresql.org/docs/10/fuzzystrmatch.html)                | 1.1             | overeenkomsten en afstand tussen tekenreeksen bepalen|
> |[Hstore](https://www.postgresql.org/docs/10/hstore.html)                       | 1.4             | gegevenstype voor het opslaan van sets (sleutel, waarde)paren|
> |[hypopg hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Hypothetische indexen voor PostgreSQL|
> |[intarray intarray](https://www.postgresql.org/docs/10/intarray.html)                     | 1.2             | functies, operatoren en indexondersteuning voor 1D-arrays van gehele getallen|
> |[Isn](https://www.postgresql.org/docs/10/isn.html)                          | 1.1             | gegevenstypen voor internationale productnummernormen|
> |[ltree ltree](https://www.postgresql.org/docs/10/ltree.html)                        | 1.1             | gegevenstype voor hiërarchische structuurachtige structuren|
> |[Orafce](https://github.com/orafce/orafce)                       | 3.7             | Functies en operatoren die een subset van functies en pakketten van commerciële RDBMS emuleren|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.2             | biedt controlefunctionaliteit|
> |[pgcrypto pgcrypto](https://www.postgresql.org/docs/10/pgcrypto.html)                     | 1.3             | cryptografische functies|
> |[pgrouting](https://pgrouting.org/)                    | 2.5.2           | pgRouting-extensie|
> |[pgrowlocks](https://www.postgresql.org/docs/10/pgrowlocks.html)                   | 1.2             | vergrendelingsgegevens op rijniveau weergeven|
> |[pgstattuple pgstattuple](https://www.postgresql.org/docs/10/pgstattuple.html)                  | 1.5             | statistieken op tuple-niveau weergeven|
> |[pg_buffercache](https://www.postgresql.org/docs/10/pgbuffercache.html)               | 1.3             | de gedeelde buffercache onderzoeken|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Extensie voor het beheren van partitietabellen op tijd of id|
> |[pg_prewarm](https://www.postgresql.org/docs/10/pgprewarm.html)                   | 1.1             | prewarmterelatiegegevens|
> |[pg_stat_statements](https://www.postgresql.org/docs/10/pgstatstatements.html)           | 1.6             | uitvoeringsstatistieken van alle SQL-instructies bijhouden|
> |[pg_trgm](https://www.postgresql.org/docs/10/pgtrgm.html)                      | 1.3             | tekstgelijkenismeting en indexzoeken op basis van trigrammen|
> |[plpgsql](https://www.postgresql.org/docs/10/plpgsql.html)                      | 1.0             | PL/pgSQL-proceduretaal|
> |[plv8 plv8](https://plv8.github.io/)                         | 2.1.0          | VERTROUWDE proceduretaal pl/JavaScript (v8)|
> |[Postgis](https://www.postgis.net/)                      | 2.4.3           | Ruimtelijke typen en functies van PostGIS-geometrie, geografie en raster|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.4.3           | PostGIS SFCGAL-functies|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.4.3           | PostGIS tijger geocoder en omgekeerde geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.4.3           | PostGIS-topologie ruimtelijke typen en functies|
> |[postgres_fdw](https://www.postgresql.org/docs/10/postgres-fdw.html)                 | 1.0             | foreign-data wrapper voor externe PostgreSQL-servers|
> |[tablefunc tablefunc](https://www.postgresql.org/docs/10/tablefunc.html)                    | 1.0             | functies die hele tabellen manipuleren, inclusief kruistabel|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | Hiermee kunnen schaalbare inserts en complexe query's voor tijdreeksgegevens worden gemaakt|
> |[Unaccent](https://www.postgresql.org/docs/10/unaccent.html)                     | 1.1             | tekstzoekwoordenboek dat accenten verwijdert|
> |[uuid-ossp](https://www.postgresql.org/docs/10/uuid-ossp.html)                    | 1.1             | universeel unieke id's (UUId's) genereren|

## <a name="postgres-96-extensions"></a>Postgres 9.6 extensies 

De volgende extensies zijn beschikbaar in Azure Database voor PostgreSQL-servers met Postgres versie 9.6.

> [!div class="mx-tableFixed"]
> | **Toestelnummer**| **Versie van de extensie** | **Beschrijving** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.2           | Wordt gebruikt om een adres te ontlopen in samenstellende elementen. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.2           | Voorbeeld van het voorbeeld van de Amerikaanse gegevensset adres|
> |[btree_gin](https://www.postgresql.org/docs/9.6/btree-gin.html)                    | 1.0             | ondersteuning voor het indexeren van gemeenschappelijke gegevenstypen in GIN|
> |[btree_gist](https://www.postgresql.org/docs/9.6/btree-gist.html)                   | 1.2             | ondersteuning voor het indexeren van veelvoorkomende gegevenstypen in GiST|
> |[chkpass](https://www.postgresql.org/docs/9.6/chkpass.html)                       | 1.0             | gegevenstype voor automatisch versleutelde wachtwoorden|
> |[citekst](https://www.postgresql.org/docs/9.6/citext.html)                       | 1.3             | gegevenstype voor hoofdlettergevoelige tekentekenreeksen|
> |[Kubus](https://www.postgresql.org/docs/9.6/cube.html)                         | 1.2             | gegevenstype voor multidimensionale kubussen|
> |[dblink dblink](https://www.postgresql.org/docs/9.6/dblink.html)                       | 1.2             | verbinding maken met andere PostgreSQL-databases vanuit een database|
> |[dict_int](https://www.postgresql.org/docs/9.6/dict-int.html)                     | 1.0             | sjabloon voor tekstzoeken naar gehele getallen|
> |[aardafstand](https://www.postgresql.org/docs/9.6/earthdistance.html)                | 1.1             | grote cirkelafstanden op het oppervlak van de aarde berekenen|
> |[fuzzystrmatch fuzzystrmatch](https://www.postgresql.org/docs/9.6/fuzzystrmatch.html)                | 1.1             | overeenkomsten en afstand tussen tekenreeksen bepalen|
> |[Hstore](https://www.postgresql.org/docs/9.6/hstore.html)                       | 1.4             | gegevenstype voor het opslaan van sets (sleutel, waarde)paren|
> |[hypopg hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Hypothetische indexen voor PostgreSQL|
> |[intarray intarray](https://www.postgresql.org/docs/9.6/intarray.html)                     | 1.2             | functies, operatoren en indexondersteuning voor 1D-arrays van gehele getallen|
> |[Isn](https://www.postgresql.org/docs/9.6/isn.html)                          | 1.1             | gegevenstypen voor internationale productnummernormen|
> |[ltree ltree](https://www.postgresql.org/docs/9.6/ltree.html)                        | 1.1             | gegevenstype voor hiërarchische structuurachtige structuren|
> |[Orafce](https://github.com/orafce/orafce)                       | 3.7             | Functies en operatoren die een subset van functies en pakketten van commerciële RDBMS emuleren|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.1.2             | biedt controlefunctionaliteit|
> |[pgcrypto pgcrypto](https://www.postgresql.org/docs/9.6/pgcrypto.html)                     | 1.3             | cryptografische functies|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.2           | pgRouting-extensie|
> |[pgrowlocks](https://www.postgresql.org/docs/9.6/pgrowlocks.html)                   | 1.2             | vergrendelingsgegevens op rijniveau weergeven|
> |[pgstattuple pgstattuple](https://www.postgresql.org/docs/9.6/pgstattuple.html)                  | 1.4             | statistieken op tuple-niveau weergeven|
> |[pg_buffercache](https://www.postgresql.org/docs/9.6/pgbuffercache.html)               | 1.2             | de gedeelde buffercache onderzoeken|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Extensie voor het beheren van partitietabellen op tijd of id|
> |[pg_prewarm](https://www.postgresql.org/docs/9.6/pgprewarm.html)                   | 1.1             | prewarmterelatiegegevens|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.6/pgstatstatements.html)           | 1.4             | uitvoeringsstatistieken van alle SQL-instructies bijhouden|
> |[pg_trgm](https://www.postgresql.org/docs/9.6/pgtrgm.html)                      | 1.3             | tekstgelijkenismeting en indexzoeken op basis van trigrammen|
> |[plpgsql](https://www.postgresql.org/docs/9.6/plpgsql.html)                      | 1.0             | PL/pgSQL-proceduretaal|
> |[plv8 plv8](https://plv8.github.io/)                         | 2.1.0          | VERTROUWDE proceduretaal pl/JavaScript (v8)|
> |[Postgis](https://www.postgis.net/)                      | 2.3.2           | Ruimtelijke typen en functies van PostGIS-geometrie, geografie en raster|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.2           | PostGIS SFCGAL-functies|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.2           | PostGIS tijger geocoder en omgekeerde geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.2           | PostGIS-topologie ruimtelijke typen en functies|
> |[postgres_fdw](https://www.postgresql.org/docs/9.6/postgres-fdw.html)                 | 1.0             | foreign-data wrapper voor externe PostgreSQL-servers|
> |[tablefunc tablefunc](https://www.postgresql.org/docs/9.6/tablefunc.html)                    | 1.0             | functies die hele tabellen manipuleren, inclusief kruistabel|
> |[timescaledb](https://docs.timescale.com/latest)                    | 1.1.1             | Hiermee kunnen schaalbare inserts en complexe query's voor tijdreeksgegevens worden gemaakt|
> |[Unaccent](https://www.postgresql.org/docs/9.6/unaccent.html)                     | 1.1             | tekstzoekwoordenboek dat accenten verwijdert|
> |[uuid-ossp](https://www.postgresql.org/docs/9.6/uuid-ossp.html)                    | 1.1             | universeel unieke id's (UUId's) genereren|

## <a name="postgres-95-extensions"></a>Postgres 9,5 extensies 

De volgende extensies zijn beschikbaar in Azure Database voor PostgreSQL-servers met Postgres versie 9.5.

> [!div class="mx-tableFixed"]
> | **Toestelnummer**| **Versie van de extensie** | **Beschrijving** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.3.0           | Wordt gebruikt om een adres te ontlopen in samenstellende elementen. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.3.0           | Voorbeeld van het voorbeeld van de Amerikaanse gegevensset adres|
> |[btree_gin](https://www.postgresql.org/docs/9.5/btree-gin.html)                    | 1.0             | ondersteuning voor het indexeren van gemeenschappelijke gegevenstypen in GIN|
> |[btree_gist](https://www.postgresql.org/docs/9.5/btree-gist.html)                   | 1.1             | ondersteuning voor het indexeren van veelvoorkomende gegevenstypen in GiST|
> |[chkpass](https://www.postgresql.org/docs/9.5/chkpass.html)                       | 1.0             | gegevenstype voor automatisch versleutelde wachtwoorden|
> |[citekst](https://www.postgresql.org/docs/9.5/citext.html)                       | 1.1             | gegevenstype voor hoofdlettergevoelige tekentekenreeksen|
> |[Kubus](https://www.postgresql.org/docs/9.5/cube.html)                         | 1.0             | gegevenstype voor multidimensionale kubussen|
> |[dblink dblink](https://www.postgresql.org/docs/9.5/dblink.html)                       | 1.1             | verbinding maken met andere PostgreSQL-databases vanuit een database|
> |[dict_int](https://www.postgresql.org/docs/9.5/dict-int.html)                     | 1.0             | sjabloon voor tekstzoeken naar gehele getallen|
> |[aardafstand](https://www.postgresql.org/docs/9.5/earthdistance.html)                | 1.0             | grote cirkelafstanden op het oppervlak van de aarde berekenen|
> |[fuzzystrmatch fuzzystrmatch](https://www.postgresql.org/docs/9.5/fuzzystrmatch.html)                | 1.0             | overeenkomsten en afstand tussen tekenreeksen bepalen|
> |[Hstore](https://www.postgresql.org/docs/9.5/hstore.html)                       | 1.3             | gegevenstype voor het opslaan van sets (sleutel, waarde)paren|
> |[hypopg hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.1           | Hypothetische indexen voor PostgreSQL|
> |[intarray intarray](https://www.postgresql.org/docs/9.5/intarray.html)                     | 1.0             | functies, operatoren en indexondersteuning voor 1D-arrays van gehele getallen|
> |[Isn](https://www.postgresql.org/docs/9.5/isn.html)                          | 1.0             | gegevenstypen voor internationale productnummernormen|
> |[ltree ltree](https://www.postgresql.org/docs/9.5/ltree.html)                        | 1.0             | gegevenstype voor hiërarchische structuurachtige structuren|
> |[Orafce](https://github.com/orafce/orafce)                       | 3.7             | Functies en operatoren die een subset van functies en pakketten van commerciële RDBMS emuleren|
> |[pgaudit](https://www.pgaudit.org/)                     | 1.0.7             | biedt controlefunctionaliteit|
> |[pgcrypto pgcrypto](https://www.postgresql.org/docs/9.5/pgcrypto.html)                     | 1.2             | cryptografische functies|
> |[pgrouting](https://pgrouting.org/)                    | 2.3.0           | pgRouting-extensie|
> |[pgrowlocks](https://www.postgresql.org/docs/9.5/pgrowlocks.html)                   | 1.1             | vergrendelingsgegevens op rijniveau weergeven|
> |[pgstattuple pgstattuple](https://www.postgresql.org/docs/9.5/pgstattuple.html)                  | 1.3             | statistieken op tuple-niveau weergeven|
> |[pg_buffercache](https://www.postgresql.org/docs/9.5/pgbuffercache.html)               | 1.1             | de gedeelde buffercache onderzoeken|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 2.6.3           | Extensie voor het beheren van partitietabellen op tijd of id|
> |[pg_prewarm](https://www.postgresql.org/docs/9.5/pgprewarm.html)                   | 1.0             | prewarmterelatiegegevens|
> |[pg_stat_statements](https://www.postgresql.org/docs/9.5/pgstatstatements.html)           | 1.3             | uitvoeringsstatistieken van alle SQL-instructies bijhouden|
> |[pg_trgm](https://www.postgresql.org/docs/9.5/pgtrgm.html)                      | 1.1             | tekstgelijkenismeting en indexzoeken op basis van trigrammen|
> |[plpgsql](https://www.postgresql.org/docs/9.5/plpgsql.html)                      | 1.0             | PL/pgSQL-proceduretaal|
> |[Postgis](https://www.postgis.net/)                      | 2.3.0           | Ruimtelijke typen en functies van PostGIS-geometrie, geografie en raster|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.3.0           | PostGIS SFCGAL-functies|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.3.0           | PostGIS tijger geocoder en omgekeerde geocoder|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.3.0           | PostGIS-topologie ruimtelijke typen en functies|
> |[postgres_fdw](https://www.postgresql.org/docs/9.5/postgres-fdw.html)                 | 1.0             | foreign-data wrapper voor externe PostgreSQL-servers|
> |[tablefunc tablefunc](https://www.postgresql.org/docs/9.5/tablefunc.html)                    | 1.0             | functies die hele tabellen manipuleren, inclusief kruistabel|
> |[Unaccent](https://www.postgresql.org/docs/9.5/unaccent.html)                     | 1.0             | tekstzoekwoordenboek dat accenten verwijdert|
> |[uuid-ossp](https://www.postgresql.org/docs/9.5/uuid-ossp.html)                    | 1.0             | universeel unieke id's (UUId's) genereren|


## <a name="pg_stat_statements"></a>pg_stat_statements
De [pg_stat_statements extensie](https://www.postgresql.org/docs/current/pgstatstatements.html) is vooraf geladen op elke Azure Database voor PostgreSQL-server om u een middel te bieden om uitvoeringsstatistieken van SQL-instructies bij te houden.
De `pg_stat_statements.track`instelling , die bepaalt welke instructies worden geteld `top`door de extensie, standaard , wat betekent dat alle verklaringen rechtstreeks uitgegeven door clients worden bijgehouden. De twee andere `none` tracking `all`niveaus zijn en . Deze instelling kan worden geconfigureerd als een serverparameter via de [Azure-portal](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) of de [Azure CLI.](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli)

Er is een afweging tussen de queryuitvoeringsgegevens die pg_stat_statements biedt en de impact op de serverprestaties als deze elke SQL-instructie registreert. Als u de extensie pg_stat_statements niet actief gebruikt, `pg_stat_statements.track` `none`raden we u aan om in te stellen op . Houd er rekening mee dat sommige bewakingsservices van derden kunnen vertrouwen op pg_stat_statements om inzichten in queryprestaties te leveren, dus controleer of dit voor u het geval is of niet.

## <a name="dblink-and-postgres_fdw"></a>dblink en postgres_fdw
[met dblink](https://www.postgresql.org/docs/current/contrib-dblink-function.html) en [postgres_fdw](https://www.postgresql.org/docs/current/postgres-fdw.html) u verbinding maken van de ene PostgreSQL-server naar de andere of naar een andere database in dezelfde server. De ontvangende server moet verbindingen van de verzendende server via de firewall toestaan. Wanneer u deze extensies gebruikt om verbinding te maken tussen Azure Database voor PostgreSQL-servers, kan dit worden gedaan door 'Toegang tot Azure-services toestaan' in te stellen voor AAN. Dit is ook nodig als u de extensies wilt gebruiken om terug te lopen naar dezelfde server. De instelling 'Toegang tot Azure-services toestaan' is te vinden op de Azure-portalpagina voor de Postgres-server onder Verbindingsbeveiliging. Als u 'Toegang tot Azure-services toestaan' on geeft, worden alle Azure IP's op de lijst met toegestane services gezet.

Op dit moment worden uitgaande verbindingen van Azure Database voor PostgreSQL niet ondersteund, behalve voor verbindingen met andere Azure Database voor PostgreSQL-servers.

## <a name="uuid"></a>uuid
Als u van `uuid_generate_v4()` plan bent om te gebruiken van de `gen_random_uuid()` [uuid-ossp extensie,](https://www.postgresql.org/docs/current/uuid-ossp.html)overwegen te vergelijken met van de [pgcrypto extensie](https://www.postgresql.org/docs/current/pgcrypto.html) voor prestatievoordelen.

## <a name="pgaudit"></a>pgAudit
De [pgAudit-extensie](https://github.com/pgaudit/pgaudit/blob/master/README.md) biedt sessie- en objectauditlogboekregistratie. Ga naar het [artikel controleconcepten](concepts-audit.md)voor meer informatie over het gebruik van deze extensie in Azure Database voor PostgreSQL. 

## <a name="pg_prewarm"></a>pg_prewarm
De pg_prewarm extensie laadt relationele gegevens in cache. Als u uw caches vooraf opwarmt, hebben uw query's betere responstijden bij de eerste run na een herstart. In Postgres 10 en lager wordt voorverwarmen handmatig gedaan met behulp van de [prewarmfunctie.](https://www.postgresql.org/docs/10/pgprewarm.html)

In Postgres 11 en hoger u de vooropwarming [automatisch](https://www.postgresql.org/docs/current/pgprewarm.html)configureren. U moet pg_prewarm opnemen `shared_preload_libraries` in de lijst van uw parameter en de server opnieuw starten om de wijziging toe te passen. Parameters kunnen worden ingesteld vanuit de [Azure-portal,](howto-configure-server-parameters-using-portal.md) [CLI,](howto-configure-server-parameters-using-cli.md)REST API of ARM-sjabloon. 

## <a name="timescaledb"></a>TijdschaalDB
TimescaleDB is een tijdreeksdatabase die is verpakt als een extensie voor PostgreSQL. TimescaleDB biedt tijdgeoriënteerde analytische functies, optimalisaties en schalen Postgres voor werkbelasting in de tijdreeks.

[Meer informatie over TimescaleDB](https://docs.timescale.com/latest), een geregistreerd handelsmerk van [Timescale, Inc.](https://www.timescale.com/). Azure Database voor PostgreSQL biedt de open-sourceversie van Timescale. Zie [de productvergelijking timescale](https://www.timescale.com/products/)om te zien welke tijdschaalfuncties beschikbaar zijn in deze versie.

### <a name="installing-timescaledb"></a>TijdschaalDB installeren
Als u TimescaleDB wilt installeren, moet u deze opnemen in de gedeelde preload-bibliotheken van de server. Voor een wijziging in `shared_preload_libraries` de parameter van Postgres moet een **serveropnieuw opstarten** van kracht worden. U parameters wijzigen met de [Azure-portal](howto-configure-server-parameters-using-portal.md) of de [Azure CLI.](howto-configure-server-parameters-using-cli.md)

De [Azure-portal gebruiken:](https://portal.azure.com/)

1. Selecteer uw Azure Database for PostgreSQL-server.

2. Selecteer **serverparameters**op de zijbalk .

3. Zoek naar `shared_preload_libraries` de parameter.

4. Selecteer **TimescaleDB**.

5. Selecteer **Opslaan** om uw wijzigingen te behouden. U ontvangt een melding zodra de wijziging is opgeslagen. 

6. Start na de melding de server **opnieuw** om deze wijzigingen toe te passen. Zie [Een Azure Database opnieuw starten voor PostgreSQL-server](howto-restart-server-portal.md)voor meer informatie over het opnieuw opstarten van een server.


U TimescaleDB nu inschakelen in uw Postgres-database. Maak verbinding met de database en geef de volgende opdracht uit:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Als er een fout wordt weergegeven, bevestigt u dat u [de server opnieuw hebt opgestart](howto-restart-server-portal.md) nadat u shared_preload_libraries hebt opgeslagen. 

U nu een TimescaleDB-hypertabel [helemaal opnieuw maken](https://docs.timescale.com/getting-started/creating-hypertables) of bestaande tijdreeksgegevens migreren in [PostgreSQL.](https://docs.timescale.com/getting-started/migrating-data)

### <a name="restoring-a-timescale-database"></a>Een tijdschaaldatabase herstellen
Als u een tijdschaaldatabase wilt herstellen met pg_dump en pg_restore, moet `timescaledb_pre_restore()` `timescaledb_post restore()`u twee helperprocedures uitvoeren in de doeldatabase: en .

Bereid eerst de doeldatabase voor:

```SQL
--create the new database where you'll perform the restore
CREATE DATABASE tutorial;
\c tutorial --connect to the database 
CREATE EXTENSION timescaledb;

SELECT timescaledb_pre_restore();
```

Nu u pg_dump uitvoeren op de oorspronkelijke database en vervolgens pg_restore doen. Zorg ervoor dat u na het herstellen de volgende opdracht uitvoert in de herstelde database:

```SQL
SELECT timescaledb_post_restore();
```


## <a name="next-steps"></a>Volgende stappen
Als u geen extensie ziet die u wilt gebruiken, laat het ons dan weten. Stem op bestaande verzoeken of maak nieuwe feedbackverzoeken in ons [feedbackforum.](https://feedback.azure.com/forums/597976-azure-database-for-postgresql)
