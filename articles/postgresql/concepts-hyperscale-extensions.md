---
title: Extensies – Hyperscale (Citus) - Azure Database voor PostgreSQL
description: Beschrijft de mogelijkheid om de functionaliteit van uw database uit te breiden met extensies in Azure Database voor PostgreSQL - Hyperscale (Citus)
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 84b28096904db49f98c16601c5927928ad38743b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77485400"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>PostgreSQL-extensies in Azure Database voor PostgreSQL – Hyperscale (Citus)

PostgreSQL biedt de mogelijkheid om de functionaliteit van uw database uit te breiden met behulp van extensies. Extensies maken het mogelijk om meerdere gerelateerde SQL-objecten samen te voegen in één pakket dat met één opdracht kan worden geladen of verwijderd uit uw database. Nadat extensies in de database zijn geladen, kunnen ze functioneren als ingebouwde functies. Zie [Pakketgerelateerde objecten in een extensie voor](https://www.postgresql.org/docs/current/static/extend-extensions.html)meer informatie over PostgreSQL-extensies.

## <a name="use-postgresql-extensions"></a>PostgreSQL-extensies gebruiken

PostgreSQL-extensies moeten in uw database worden geïnstalleerd voordat u ze gebruiken. Als u een bepaalde extensie wilt installeren, voert u de opdracht [UITBREIDING MAKEN](https://www.postgresql.org/docs/current/static/sql-createextension.html) uit van het psql-gereedschap om de verpakte objecten in uw database te laden.

Azure Database voor PostgreSQL - Hyperscale (Citus) ondersteunt momenteel een subset van belangrijke extensies zoals hier vermeld. Andere extensies dan de genoemde worden niet ondersteund. U uw eigen extensie niet maken met Azure Database voor PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Extensies ondersteund door Azure Database voor PostgreSQL

In de volgende tabellen worden de standaard PostgreSQL-extensies weergegeven die momenteel worden ondersteund door Azure Database voor PostgreSQL. Deze informatie is ook `SELECT * FROM pg_available_extensions;`beschikbaar door het uitvoeren van .

### <a name="data-types-extensions"></a>Extensies voor gegevenstypen

> [!div class="mx-tableFixed"]
> | **Toestelnummer** | **Beschrijving** |
> |---|---|
> | [citekst](https://www.postgresql.org/docs/current/static/citext.html) | Biedt een hoofdletter-ongevoelig tekentekenreekstype. |
> | [Kubus](https://www.postgresql.org/docs/current/static/cube.html) | Biedt een gegevenstype voor multidimensionale kubussen. |
> | [Hstore](https://www.postgresql.org/docs/current/static/hstore.html) | Biedt een gegevenstype voor het opslaan van sets sleutelwaardeparen. |
> | [hll hll](https://github.com/citusdata/postgresql-hll) | Biedt een HyperLogLog-gegevensstructuur. |
> | [Isn](https://www.postgresql.org/docs/current/static/isn.html) | Biedt gegevenstypen voor internationale productnummernormen. |
> | [Lo](https://www.postgresql.org/docs/current/lo.html) | Groot object onderhoud. |
> | [ltree ltree](https://www.postgresql.org/docs/current/static/ltree.html) | Biedt een gegevenstype voor hiërarchische structuurachtige structuren. |
> | [Seg](https://www.postgresql.org/docs/current/seg.html) | Gegevenstype voor het weergeven van lijnsegmenten of zwevende-puntintervallen. |
> | [topn topn](https://github.com/citusdata/postgresql-topn/) | Typ voor top-n JSONB. |

### <a name="full-text-search-extensions"></a>Zoekextensies voor volledige tekst

> [!div class="mx-tableFixed"]
> | **Toestelnummer** | **Beschrijving** |
> |---|---|
> | [dict\_int](https://www.postgresql.org/docs/current/static/dict-int.html) | Biedt een sjabloon voor tekstzoekwoordenlijst voor gehele getallen. |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Tekst zoek woordenboek sjabloon voor uitgebreide synoniem verwerking. |
> | [Unaccent](https://www.postgresql.org/docs/current/static/unaccent.html) | Een tekst zoek woordenboek dat accenten verwijdert (diacritic borden) uit lexemes. |

### <a name="functions-extensions"></a>Functies-extensies

> [!div class="mx-tableFixed"]
> | **Toestelnummer** | **Beschrijving** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Functies voor het automatisch instappen van velden. |
> | [aardafstand](https://www.postgresql.org/docs/current/static/earthdistance.html) | Biedt een middel om grote cirkel afstanden op het oppervlak van de aarde te berekenen. |
> | [fuzzystrmatch fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | Biedt verschillende functies om overeenkomsten en afstand tussen tekenreeksen te bepalen. |
> | [gebruikersnaam\_invoegen](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Functies voor het bijhouden van wie een tabel heeft gewijzigd. |
> | [intagg intagg](https://www.postgresql.org/docs/current/intagg.html) | Integer aggregator en enumerator (verouderd). |
> | [intarray intarray](https://www.postgresql.org/docs/current/static/intarray.html) | Biedt functies en operatoren voor het manipuleren van null-vrije arrays van gehele getallen. |
> | [moddatetime moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Functies voor het bijhouden van de laatste wijzigingstijd. |
> | [pgcrypto pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | Biedt cryptografische functies. |
> | [pg\_partman pg partman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Beheert partitietabellen op tijd of id. |
> | [pg\_trgm pg trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | Biedt functies en operatoren voor het bepalen van de gelijkenis van alfanumerieke tekst op basis van trigrammatching. |
> | [refint](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Functies voor de implementatie van referentiële integriteit (verouderd). |
> | sessieanalyse\_ | Functies voor het opvragen van hstore-arrays. |
> | [tablefunc tablefunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | Biedt functies die hele tabellen manipuleren, inclusief crosstab. |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Geactiveerde wijzigingsmeldingen. |
> | [tijdreizen](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Functies voor het implementeren van tijdreizen. |
> | [uuid-ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | Genereert universeel unieke id's (UUId's). |

### <a name="hyperscale-extensions"></a>Hyperscale-extensies

> [!div class="mx-tableFixed"]
> | **Toestelnummer** | **Beschrijving** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Citus gedistribueerde database. |
> | shard\_rebalancer | Breng gegevens in een servergroep veilig opnieuw in evenwicht in geval van toevoeging of verwijdering van het knooppunt. |

### <a name="index-types-extensions"></a>Extensies voor indextypen

> [!div class="mx-tableFixed"]
> | **Toestelnummer** | **Beschrijving** |
> |---|---|
> | [Bloom](https://www.postgresql.org/docs/current/bloom.html) | Bloom access methode - signature file-based index. |
> | [btree\_gin](https://www.postgresql.org/docs/current/static/btree-gin.html) | Biedt voorbeeld-GIN-operatorklassen die B-tree-achtig gedrag implementeren voor bepaalde gegevenstypen. |
> | [btree\_gist](https://www.postgresql.org/docs/current/static/btree-gist.html) | Biedt gist-indexoperatorklassen die B-tree implementeren. |

### <a name="language-extensions"></a>Taalextensies

> [!div class="mx-tableFixed"]
> | **Toestelnummer** | **Beschrijving** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | PL/pgSQL loadable proceduretaal. |

### <a name="miscellaneous-extensions"></a>Diverse extensies

> [!div class="mx-tableFixed"]
> | **Toestelnummer** | **Beschrijving** |
> |---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Administratieve functies voor PostgreSQL. |
> | [amcheck amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Functies voor het verifiëren van relatie-integriteit. |
> | [bestand\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Buitenlandse gegevens wrapper voor platte toegang tot bestanden. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | Controleer de inhoud van databasepagina's op een laag niveau. |
> | [pg\_buffercache](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | Biedt een middel om in realtime te onderzoeken wat er in de gedeelde buffercache gebeurt. |
> | [pg\_cron pg cron](https://github.com/citusdata/pg_cron) | Taakplanner voor PostgreSQL. |
> | [pg\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Bestudeer de vrije ruimtekaart (FSM). |
> | [pg\_prewarm](https://www.postgresql.org/docs/current/static/pgprewarm.html) | Biedt een manier om relatiegegevens in de buffercache te laden. |
> | [pg\_\_stat verklaringen](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | Biedt een middel voor het bijhouden van uitvoeringsstatistieken van alle SQL-instructies die door een server worden uitgevoerd. Zie de sectie 'pg_stat_statements' voor informatie over deze extensie. |
> | [pg\_zichtbaarheid](https://www.postgresql.org/docs/current/pgvisibility.html) | Bekijk de zichtbaarheidskaart (VM) en zichtbaarheidsinformatie op paginaniveau. |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | Biedt een middel voor het weergeven van vergrendelingsinformatie op rijniveau. |
> | [pgstattuple pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | Biedt een middel voor het weergeven van statistieken op tuple-niveau. |
> | [postgres\_fdw](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | Foreign-data wrapper gebruikt om toegang te krijgen tot gegevens die zijn opgeslagen in externe PostgreSQL-servers. Zie de sectie 'dblink en postgres_fdw' voor informatie over deze extensie.|
> | [sslinfo sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informatie over SSL-certificaten. |
> | [tsm-systeemrijen\_\_](https://www.postgresql.org/docs/current/tsm-system-rows.html) | TABELVOORBEELDmethode, waarbij het aantal rijen als limiet wordt geaccepteerd. |
> | [tsm-systeemtijd\_\_](https://www.postgresql.org/docs/current/tsm-system-time.html) | TABELSAMPLE-methode, die tijd in milliseconden als limiet accepteert. |
> | [hypopg hypopg](https://hypopg.readthedocs.io/en/latest/) | Biedt een middel om hypothetische indexen te maken die geen CPU of schijf kosten. |
> | [dblink dblink](https://www.postgresql.org/docs/current/dblink.html) | Een module die verbindingen met andere PostgreSQL-databases ondersteunt vanuit een databasesessie. Zie de sectie 'dblink en postgres_fdw' voor informatie over deze extensie. |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | XPath-query's en XSLT. |


### <a name="postgis-extensions"></a>PostGIS-extensies

> [!div class="mx-tableFixed"]
> | **Toestelnummer** | **Beschrijving** |
> |---|---|
> | [PostGIS](https://www.postgis.net/),\_postgis topologie,\_\_postgis tijger geocoder, postgis\_sfcgal | Ruimtelijke en geografische objecten voor PostgreSQL. |
> | adres\_standaardiseert,\_adres\_\_standaardiseerten ons | Wordt gebruikt om een adres te ontlopen in samenstellende elementen. Wordt gebruikt om de normalisatiestap van geocodering te ondersteunen. |
> | postgis\_sfcgal | PostGIS SFCGAL-functies. |
> | postgis\_\_tijger geocoder | PostGIS tijger geocoder en omgekeerde geocoder. |
> | postgis\_topologie | PostGIS-topologie ruimtelijke typen en functies. |


## <a name="pg_stat_statements"></a>pg_stat_statements
De [\_pg\_stat statements extensie](https://www.postgresql.org/docs/current/pgstatstatements.html) is vooraf geladen op elke Azure Database voor PostgreSQL server om u te voorzien van een middel om uitvoeringsstatistieken van SQL-instructies bij te houden.

Met `pg_stat_statements.track` de instelling bepaalt u welke instructies worden geteld door de extensie. Het standaard `top`aan , wat betekent dat alle verklaringen rechtstreeks uitgegeven door klanten worden bijgehouden. De twee andere `none` tracking `all`niveaus zijn en . Deze instelling kan worden geconfigureerd als een serverparameter via de [Azure-portal](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) of de [Azure CLI.](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli)

Er is een afweging tussen de queryuitvoeringsgegevens die pg_stat_statements biedt en het effect op de serverprestaties terwijl elke SQL-instructie wordt logboeken. Als u de extensie pg_stat_statements niet actief gebruikt, `pg_stat_statements.track` raden `none`we u aan in te stellen op . Sommige bewakingsservices van derden kunnen afhankelijk zijn van pg_stat_statements om inzichten in queryprestaties te leveren, dus controleer of dit voor u het geval is of niet.

## <a name="dblink-and-postgres_fdw"></a>dblink en postgres_fdw
U dblink en postgres_fdw gebruiken om verbinding te maken van de ene PostgreSQL-server naar de andere of naar een andere database in dezelfde server. De ontvangende server moet verbindingen van de verzendende server via de firewall toestaan. Als u deze extensies wilt gebruiken om verbinding te maken tussen Azure Database voor PostgreSQL-servers, stelt **u Toegang tot Azure-services toestaan** in bij AAN. U moet deze instelling ook inschakelen als u de extensies wilt gebruiken om terug te lopen naar dezelfde server. De instelling **Toegang tot Azure-services toestaan** is te vinden op de Azure-portalpagina voor de Postgres-server onder **Verbindingsbeveiliging**. **Het inschakelen van toegang tot Azure-services** op whitelists voor alle Azure IP's.

Op dit moment worden uitgaande verbindingen van Azure Database voor PostgreSQL niet ondersteund, behalve voor verbindingen met andere Azure Database voor PostgreSQL-servers.
