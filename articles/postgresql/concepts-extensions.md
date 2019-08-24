---
title: PostgreSQL-extensies gebruiken in Azure Database for PostgreSQL-één server
description: Beschrijft de mogelijkheid om de functionaliteit van uw data base uit te breiden met behulp van uitbrei dingen in Azure Database for PostgreSQL-één server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/23/2019
ms.openlocfilehash: 93cc02fafcfa153c452f37c2bc69bb47e2629f1d
ms.sourcegitcommit: 4b8a69b920ade815d095236c16175124a6a34996
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/23/2019
ms.locfileid: "69998066"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql---single-server"></a>PostgreSQL-extensies in Azure Database for PostgreSQL-één server
PostgreSQL biedt de mogelijkheid om de functionaliteit van uw data base uit te breiden met behulp van extensies. Extensies bundelen meerdere gerelateerde SQL-objecten in één pakket dat kan worden geladen of verwijderd uit uw data base met één opdracht. Nadat de gegevens zijn geladen in de data base, functioneren de extensies als ingebouwde functies.

## <a name="how-to-use-postgresql-extensions"></a>PostgreSQL-extensies gebruiken
PostgreSQL-extensies moeten worden geïnstalleerd in uw Data Base voordat u ze kunt gebruiken. Als u een bepaalde uitbrei ding wilt installeren, voert u de opdracht [extensie](https://www.postgresql.org/docs/current/static/sql-createextension.html) maken uit vanuit het hulp programma psql om de verpakte objecten in uw data base te laden.

Azure Database for PostgreSQL ondersteunt een subset van de sleutel uitbreidingen zoals hieronder wordt weer gegeven. Uitbrei dingen die niet in de lijst staan, worden niet ondersteund. U kunt geen eigen uitbrei ding maken in Azure Database for PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Extensies die worden ondersteund door Azure Database for PostgreSQL
De volgende tabellen geven een lijst van de standaard PostgreSQL-extensies die momenteel door Azure Database for PostgreSQL worden ondersteund. Deze informatie is ook beschikbaar door te `SELECT * FROM pg_available_extensions;`worden uitgevoerd.

### <a name="data-types-extensions"></a>Uitbrei dingen van gegevens typen

> [!div class="mx-tableFixed"]
> | **Switch** | **Beschrijving** |
> |---|---|
> | [chkpass](https://www.postgresql.org/docs/9.6/static/chkpass.html) | Biedt een gegevens type voor automatisch versleutelde wacht woorden. |
> | [citext](https://www.postgresql.org/docs/9.6/static/citext.html) | Bevat een hoofdletter gevoelig type teken reeks. |
> | [cube](https://www.postgresql.org/docs/9.6/static/cube.html) | Biedt een gegevens type voor multidimensionale kubussen. |
> | [hstore](https://www.postgresql.org/docs/9.6/static/hstore.html) | Biedt een gegevens type voor het opslaan van sets sleutel/waarde-paren. |
> | [isn](https://www.postgresql.org/docs/9.6/static/isn.html) | Biedt gegevens typen voor International product Numbering Standards. |
> | [ltree](https://www.postgresql.org/docs/9.6/static/ltree.html) | Voorziet in een gegevens type voor hiërarchische structuren op boom structuur. |

### <a name="functions-extensions"></a>Extensies voor functies

> [!div class="mx-tableFixed"]
> | **Switch** | **Beschrijving** |
> |---|---|
> | [earthdistance](https://www.postgresql.org/docs/9.6/static/earthdistance.html) | Biedt een manier om grote afstanden te berekenen op het Opper vlak van de aarde. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/9.6/static/fuzzystrmatch.html) | Biedt verschillende functies voor het bepalen van overeenkomsten en de afstand tussen teken reeksen. |
> | [intarray](https://www.postgresql.org/docs/9.6/static/intarray.html) | Biedt functies en Opera tors voor het bewerken van Null-vrije matrices van gehele getallen. |
> | [pgcrypto](https://www.postgresql.org/docs/9.6/static/pgcrypto.html) | Voorziet in cryptografische functies. |
> | [pg\_partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Hiermee beheert u gepartitioneerde tabellen op tijd of ID. |
> | [pg\_trgm](https://www.postgresql.org/docs/9.6/static/pgtrgm.html) | Voorziet in functies en Opera tors voor het bepalen van de gelijkenis van alfanumerieke tekst op basis van het afstemmen van Trigram. |
> | [tablefunc](https://www.postgresql.org/docs/9.6/static/tablefunc.html) | Biedt functies voor het bewerken van hele tabellen, inclusief Kruistabel query's. |
> | [uuid-ossp](https://www.postgresql.org/docs/9.6/static/uuid-ossp.html) | Genereert Universele unieke id's (UUID). (Zie hieronder voor een opmerking over deze uitbrei ding). |
> | [orafce](https://github.com/orafce/orafce) | Biedt een subset van functies en pakketten die worden geëmuleerd vanuit commerciële data bases. |

### <a name="full-text-search-extensions"></a>Extensies voor zoeken in volledige tekst

> [!div class="mx-tableFixed"]
> | **Switch** | **Beschrijving** |
> |---|---|
> | [dicteren\_int](https://www.postgresql.org/docs/9.6/static/dict-int.html) | Voorziet in een woordenlijst sjabloon voor tekst zoeken voor gehele getallen. |
> | [unaccent](https://www.postgresql.org/docs/9.6/static/unaccent.html) | Een zoek woordenlijst voor tekst waarmee accenten (diakritische tekens) van lexemes worden verwijderd. |

### <a name="index-types-extensions"></a>Extensies van index typen

> [!div class="mx-tableFixed"]
> | **Switch** | **Beschrijving** |
> |---|---|
> | [btree\_eginnen](https://www.postgresql.org/docs/9.6/static/btree-gin.html) | Biedt een voor beeld van EGINNEN-operator klassen die B-structuur als gedrag implementeren voor bepaalde gegevens typen. |
> | [btreee\_-registratie](https://www.postgresql.org/docs/9.6/static/btree-gist.html) | Bevat de klassen van een categorie-operator die de B-structuur implementeert. |

### <a name="language-extensions"></a>Taal extensies

> [!div class="mx-tableFixed"]
> | **Switch** | **Beschrijving** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/9.6/static/plpgsql.html) | Door PL/pgSQL geladen procedure taal. |
> | [plv8](https://plv8.github.io/) | Een Java script-taal uitbreiding voor PostgreSQL die kan worden gebruikt voor opgeslagen procedures, triggers, enzovoort. |

### <a name="miscellaneous-extensions"></a>Diverse uitbrei dingen

> [!div class="mx-tableFixed"]
> | **Switch** | **Beschrijving** |
> |---|---|
> | [pg\_buffercache](https://www.postgresql.org/docs/9.6/static/pgbuffercache.html) | Biedt een manier om te controleren wat er gebeurt in de gedeelde buffer cache in realtime. |
> | [pg\_prewarm](https://www.postgresql.org/docs/9.6/static/pgprewarm.html) | Biedt een manier om relatie gegevens in de buffer cache te laden. |
> | [PG\_stat\_-instructies](https://www.postgresql.org/docs/9.6/static/pgstatstatements.html) | Biedt een manier om uitvoerings statistieken op te sporen van alle SQL-instructies die worden uitgevoerd door een server. (Zie hieronder voor een opmerking over deze uitbrei ding). |
> | [pgrowlocks](https://www.postgresql.org/docs/9.6/static/pgrowlocks.html) | Biedt een manier om vergrendelings informatie op rijniveau te tonen. |
> | [pgstattuple](https://www.postgresql.org/docs/9.6/static/pgstattuple.html) | Biedt een manier om statistieken op tupleniveau weer te geven. |
> | [post gres\_fdw](https://www.postgresql.org/docs/9.6/static/postgres-fdw.html) | Externe-gegevens wrapper die wordt gebruikt voor toegang tot gegevens die zijn opgeslagen in externe PostgreSQL-servers. (Zie hieronder voor een opmerking over deze uitbrei ding).|
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Biedt een manier om hypothetische indexen te maken die geen CPU of schijf kosten. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Een module die verbindingen met andere PostgreSQL-data bases in een database sessie ondersteunt. (Zie hieronder voor een opmerking over deze uitbrei ding). |


### <a name="postgis-extensions"></a>PostGIS-extensies

> [!div class="mx-tableFixed"]
> | **Switch** | **Beschrijving** |
> |---|---|
> | [Postgis](https://www.postgis.net/), postgis\_-topologie,\_postgis\_Tiger geocodeer,\_postgis sfcgal | Ruimtelijke en geografische objecten voor PostgreSQL. |
> | adres\_standaardiserer,\_adresseer\_data gegevens\_ | Wordt gebruikt om een adres te parseren in onderdeel elementen. Wordt gebruikt ter ondersteuning van de stap voor het normaliseren van het adres voor geocodering. |
> | [pgrouting](https://pgrouting.org/) | Breidt de georuimtelijke PostGIS/PostgreSQL-data base uit om georuimtelijke routerings functionaliteit te bieden. |


### <a name="time-series-extensions"></a>Extensies voor time series

> [!div class="mx-tableFixed"]
> | **Switch** | **Beschrijving** |
> |---|---|
> | [TimescaleDB](https://docs.timescale.com/latest) | Een SQL database met een tijd reeks die automatische partitionering ondersteunt voor snellere opname en query's. Biedt tijdgebonden analytische functies, optimalisaties en schaal PostgreSQL voor werk belastingen in de tijd reeks. TimescaleDB is ontwikkeld door en een geregistreerd handels merk van de [tijd schaal, Inc.](https://www.timescale.com/) (Zie hieronder voor een opmerking over deze uitbrei ding). |

## <a name="postgres-11-extensions"></a>Post gres 11-extensies

De volgende uitbrei dingen zijn beschikbaar op Azure Database for PostgreSQL servers die post gres-versie 11 hebben.

> [!div class="mx-tableFixed"]
> | **Switch**| **Extensie versie** | **Beschrijving** |
> |---|---|---|
> |[address_standardizer](http://postgis.net/docs/Address_Standardizer.html)         | 2.5.1           | Wordt gebruikt om een adres te parseren in onderdeel elementen. |
> |[address_standardizer_data_us](http://postgis.net/docs/Address_Standardizer.html) | 2.5.1           | Adres van voor beeld van een gegevensset voor de standaardiserer|
> |[btree_gin](https://www.postgresql.org/docs/11/btree-gin.html)                    | 1.3             | ondersteuning voor het indexeren van algemene gegevens typen in EGINNEN|
> |[btree_gist](https://www.postgresql.org/docs/11/btree-gist.html)                   | 1.5             | ondersteuning voor het indexeren van veelvoorkomende gegevens typen in het REGI ster|
> |[citext](https://www.postgresql.org/docs/11/static/citext.html)                       | 1.5             | gegevens type voor niet-hoofdletter gevoelige teken reeksen|
> |[cube](https://www.postgresql.org/docs/11/static/cube.html)                         | 1.4             | gegevens type voor multidimensionale kubussen|
> |[dblink](https://www.postgresql.org/docs/11/dblink.html)                       | 1.2             | verbinding maken met andere PostgreSQL-data bases vanuit een Data Base|
> |[dict_int](https://www.postgresql.org/docs/11/static/dict-int.html)                     | 1.0             | Zoek woordenlijst sjabloon voor tekst met gehele getallen|
> |[earthdistance](https://www.postgresql.org/docs/11/static/earthdistance.html)                | 1.1             | de afstanden van de cirkel op het Opper vlak van de aarde berekenen|
> |[fuzzystrmatch](https://www.postgresql.org/docs/11/static/fuzzystrmatch.html)                | 1.1             | Vergelijk bare en afstand tussen teken reeksen bepalen|
> |[hstore](https://www.postgresql.org/docs/11/static/hstore.html)                       | 1.5             | gegevens type voor het opslaan van sets van (sleutel-, waarde-) paren|
> |[hypopg](https://hypopg.readthedocs.io/en/latest/)                       | 1.1.2           | Hypothetische indexen voor PostgreSQL|
> |[intarray](https://www.postgresql.org/docs/11/static/intarray.html)                     | 1.2             | ondersteuning voor functies, Opera tors en index voor 1d-matrices met gehele getallen|
> |[isn](https://www.postgresql.org/docs/11/static/isn.html)                          | 1.2             | gegevens typen voor International product Numbering Standards|
> |[ltree](https://www.postgresql.org/docs/11/static/ltree.html)                        | 1.1             | gegevens type voor hiërarchische structuren in structuur|
> |[orafce](https://github.com/orafce/orafce)                       | 3.7             | Functies en Opera tors die een subset van functies en pakketten van commerciële RDBMS emuleren|
> |[pgcrypto](https://www.postgresql.org/docs/11/static/pgcrypto.html)                     | 1.3             | cryptografische functies|
> |[pgrouting](https://pgrouting.org/)                    | 2.6.2           | pgRouting-extensie|
> |[pgrowlocks](https://www.postgresql.org/docs/11/static/pgrowlocks.html)                   | 1.2             | vergrendelings informatie op rijniveau weer geven|
> |[pgstattuple](https://www.postgresql.org/docs/11/static/pgstattuple.html)                  | 1.5             | statistieken van tuple-niveau weer geven|
> |[pg_buffercache](https://www.postgresql.org/docs/11/static/pgbuffercache.html)               | 1.3             | de gedeelde buffer cache controleren|
> |[pg_partman](https://github.com/pgpartman/pg_partman)                   | 4.0.0           | Extensie voor het beheren van gepartitioneerde tabellen op tijd of ID|
> |[pg_prewarm](https://www.postgresql.org/docs/11/pgprewarm.html)                   | 1.2             | prewarme-relatie gegevens|
> |[pg_stat_statements](https://www.postgresql.org/docs/11/static/pgstatstatements.html)           | 1.6             | uitvoerings statistieken bijhouden van alle SQL-instructies die zijn uitgevoerd|
> |[pg_trgm](https://www.postgresql.org/docs/11/static/pgtrgm.html)                      | 1.4             | tekstgelijkeniss meting en index zoeken op basis van trigrams|
> |[plpgsql](https://www.postgresql.org/docs/11/static/plpgsql.html)                      | 1.0             | Taal van PL/pgSQL-procedure|
> |[plv8](https://plv8.github.io/)                         | 2.3.11          | PL/java script (V8) betrouw bare procedure taal|
> |[postgis](https://www.postgis.net/)                      | 2.5.1           | Ruimtelijke typen en functies voor PostGIS geometrie, geografie en raster|
> |[postgis_sfcgal](https://www.postgis.net/)               | 2.5.1           | PostGIS SFCGAL-functies|
> |[postgis_tiger_geocoder](https://www.postgis.net/)       | 2.5.1           | PostGIS Tiger geocodeer en reverse geocodeer|
> |[postgis_topology](https://postgis.net/docs/Topology.html)             | 2.5.1           | Ruimtelijke typen en functies van de PostGIS-topologie|
> |[postgres_fdw](https://www.postgresql.org/docs/11/static/postgres-fdw.html)                 | 1.0             | externe-gegevens wrapper voor externe PostgreSQL-servers|
> |[tablefunc](https://www.postgresql.org/docs/11/static/tablefunc.html)                    | 1.0             | functies voor het bewerken van hele tabellen, inclusief Kruistabel query's|
> |[unaccent](https://www.postgresql.org/docs/11/static/unaccent.html)                     | 1.1             | Zoek woordenlijst voor tekst die accenten verwijdert|
> |[uuid-ossp](https://www.postgresql.org/docs/11/static/uuid-ossp.html)                    | 1.1             | Universele unieke id's (UUID) genereren|


## <a name="pg_stat_statements"></a>pg_stat_statements
De uitbrei ding pg_stat_statements is vooraf geladen op elke Azure Database for PostgreSQL-server, zodat u de uitvoerings statistieken van SQL-instructies kunt volgen.
De instelling `pg_stat_statements.track`, die bepaalt welke instructies door de uitbrei ding worden geteld, wordt `top`standaard ingesteld op, wat betekent dat alle instructies die rechtstreeks door clients worden uitgegeven, worden bijgehouden. De twee andere tracking niveaus zijn `none` en `all`. Deze instelling kan worden geconfigureerd als een server parameter via de [Azure Portal](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) of de [Azure cli](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Er is een verhouding tussen de pg_stat_statements voor het uitvoeren van query's en de invloed op de prestaties van de server bij het vastleggen van elke SQL-instructie. Als u de pg_stat_statements-extensie niet actief gebruikt, wordt u aangeraden om `pg_stat_statements.track` in `none`te stellen op. Houd er rekening mee dat sommige bewakings services van derden afhankelijk zijn van pg_stat_statements om query prestaties te kunnen leveren, dus bevestig of dit het geval voor u is.

## <a name="dblink-and-postgres_fdw"></a>dblink en postgres_fdw
dblink en postgres_fdw bieden u de mogelijkheid om verbinding te maken tussen een PostgreSQL-server en een andere Data Base op dezelfde server. De ontvangende server moet verbindingen vanaf de verzendende server via de Firewall toestaan. Als u deze uitbrei dingen gebruikt om verbinding te maken tussen Azure Database for PostgreSQL-servers, kunt u dit doen door de optie toegang tot Azure-Services toestaan in te stellen op aan. Dit is ook nodig als u de uitbrei dingen wilt gebruiken om naar dezelfde server te gaan. De instelling toegang tot Azure-Services toestaan vindt u op de pagina Azure Portal voor de post gres-server, onder verbindings beveiliging. Als u toegang tot Azure-Services toestaan inschakelt, worden alle Azure Ip's in de acceptatie lijst geplaatst.

Momenteel worden uitgaande verbindingen van Azure Database for PostgreSQL niet ondersteund, met uitzonde ring van verbindingen met andere Azure Database for PostgreSQL-servers.

## <a name="uuid"></a>uuid
Als u van plan bent om `uuid_generate_v4()` te gebruiken vanuit de UUID-ossp-extensie, `gen_random_uuid()` kunt u overwegen om te vergelijken met de pgcrypto-extensie voor prestatie voordelen.


## <a name="timescaledb"></a>TimescaleDB
TimescaleDB is een Data Base met een tijd reeks die is verpakt als een uitbrei ding voor PostgreSQL. TimescaleDB biedt tijdgebonden analytische functies, optimalisaties en schaal baarheid van post gres voor werk belastingen in de tijd reeks.

Meer [informatie over TimescaleDB](https://docs.timescale.com/latest), een geregistreerd handels merk van de [tijd schaal, Inc.](https://www.timescale.com/)

### <a name="installing-timescaledb"></a>TimescaleDB installeren
Als u TimescaleDB wilt installeren, moet u deze toevoegen aan de gedeelde vooraf geladen bibliotheken van de server. Voor een wijziging van de `shared_preload_libraries` para meter post gres moet de **server opnieuw worden opgestart** . U kunt para meters wijzigen met behulp van de [Azure Portal](howto-configure-server-parameters-using-portal.md) of de [Azure cli](howto-configure-server-parameters-using-cli.md).

> [!NOTE]
> TimescaleDB kan worden ingeschakeld op Azure Database for PostgreSQL versies 9,6 en 10

Met behulp van de [Azure Portal](https://portal.azure.com/):

1. Selecteer uw Azure Database for PostgreSQL-server.

2. Selecteer op de zijbalk **server parameters**.

3. Zoek de `shared_preload_libraries` para meter.

4. Selecteer **TimescaleDB**.

5. Selecteer **Opslaan** om uw wijzigingen te bewaren. U ontvangt een melding zodra de wijziging is opgeslagen. 

6. Nadat de melding is ontvangen, start u de server **opnieuw** op om deze wijzigingen toe te passen. Zie [een Azure database for postgresql server opnieuw starten](howto-restart-server-portal.md)voor meer informatie over het opnieuw opstarten van een server.


U kunt nu TimescaleDB inschakelen in uw post gres-data base. Maak verbinding met de data base en voer de volgende opdracht uit:
```sql
CREATE EXTENSION IF NOT EXISTS timescaledb CASCADE;
```
> [!TIP]
> Als er een fout optreedt, controleert u of u [de server opnieuw hebt opgestart na het](howto-restart-server-portal.md) opslaan van shared_preload_libraries. 

U kunt nu een [nieuwe](https://docs.timescale.com/getting-started/creating-hypertables) TimescaleDB-hypertable maken of [bestaande gegevens van de tijd reeks migreren in postgresql](https://docs.timescale.com/getting-started/migrating-data).


## <a name="next-steps"></a>Volgende stappen
Als u een uitbrei ding die u wilt gebruiken, niet ziet, laat het ons dan weten. Stem op bestaande aanvragen of maak nieuwe feedback aanvragen in ons [Feedback forum](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).
