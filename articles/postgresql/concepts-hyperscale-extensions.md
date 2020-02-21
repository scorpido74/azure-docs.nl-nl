---
title: Extensies – grootschalige (Citus)-Azure Database for PostgreSQL
description: Beschrijft de mogelijkheid om de functionaliteit van uw data base uit te breiden met behulp van uitbrei dingen in Azure Database for PostgreSQL-grootschalige (Citus)
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: 84b28096904db49f98c16601c5927928ad38743b
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/20/2020
ms.locfileid: "77485400"
---
# <a name="postgresql-extensions-in-azure-database-for-postgresql--hyperscale-citus"></a>PostgreSQL-uitbrei dingen in Azure Database for PostgreSQL – grootschalige (Citus)

PostgreSQL biedt de mogelijkheid om de functionaliteit van uw data base uit te breiden met behulp van extensies. Met uitbrei dingen kunt u meerdere gerelateerde SQL-objecten bundelen in één pakket dat kan worden geladen of verwijderd uit uw data base met één opdracht. Nadat de gegevens in de Data Base zijn geladen, kunnen extensies functioneren als ingebouwde functies. Zie [pakket verwante objecten in een uitbrei ding](https://www.postgresql.org/docs/current/static/extend-extensions.html)voor meer informatie over postgresql-extensies.

## <a name="use-postgresql-extensions"></a>PostgreSQL-extensies gebruiken

PostgreSQL-extensies moeten worden geïnstalleerd in uw Data Base voordat u ze kunt gebruiken. Als u een bepaalde uitbrei ding wilt installeren, voert u de opdracht [extensie maken](https://www.postgresql.org/docs/current/static/sql-createextension.html) uit vanuit het hulp programma psql om de verpakte objecten in uw data base te laden.

Azure Database for PostgreSQL-grootschalige (Citus) ondersteunt momenteel een subset met sleutel uitbreidingen, zoals hier wordt weer gegeven. Extensies die niet worden vermeld, worden niet ondersteund. U kunt geen eigen uitbrei ding maken met Azure Database for PostgreSQL.

## <a name="extensions-supported-by-azure-database-for-postgresql"></a>Extensies die worden ondersteund door Azure Database for PostgreSQL

De volgende tabellen geven een lijst van de standaard PostgreSQL-extensies die momenteel door Azure Database for PostgreSQL worden ondersteund. Deze informatie is ook beschikbaar door `SELECT * FROM pg_available_extensions;`uit te voeren.

### <a name="data-types-extensions"></a>Uitbrei dingen van gegevens typen

> [!div class="mx-tableFixed"]
> | **Switch** | **Beschrijving** |
> |---|---|
> | [citext](https://www.postgresql.org/docs/current/static/citext.html) | Bevat een hoofdletter gevoelig type teken reeks. |
> | [kubus](https://www.postgresql.org/docs/current/static/cube.html) | Biedt een gegevens type voor multidimensionale kubussen. |
> | [hstore](https://www.postgresql.org/docs/current/static/hstore.html) | Biedt een gegevens type voor het opslaan van sets sleutel-waardeparen. |
> | [hll](https://github.com/citusdata/postgresql-hll) | Biedt een HyperLogLog-gegevens structuur. |
> | [hebben](https://www.postgresql.org/docs/current/static/isn.html) | Biedt gegevens typen voor International product Numbering Standards. |
> | [laag](https://www.postgresql.org/docs/current/lo.html) | Large Object onderhoud. |
> | [ltree](https://www.postgresql.org/docs/current/static/ltree.html) | Voorziet in een gegevens type voor hiërarchische structuren op boom structuur. |
> | [seg](https://www.postgresql.org/docs/current/seg.html) | Gegevens type voor de weer gave van lijn segmenten of drijvende-komma intervallen. |
> | [topn](https://github.com/citusdata/postgresql-topn/) | Typ voor de bovenste n JSONB. |

### <a name="full-text-search-extensions"></a>Extensies voor zoeken in volledige tekst

> [!div class="mx-tableFixed"]
> | **Switch** | **Beschrijving** |
> |---|---|
> | [dicteren\_int](https://www.postgresql.org/docs/current/static/dict-int.html) | Voorziet in een woordenlijst sjabloon voor tekst zoeken voor gehele getallen. |
> | [dict\_xsyn](https://www.postgresql.org/docs/current/dict-xsyn.html) | Zoek woordenlijst sjabloon voor tekst voor uitgebreide synoniemen verwerking. |
> | [accenten opzeggen](https://www.postgresql.org/docs/current/static/unaccent.html) | Een zoek woordenlijst voor tekst waarmee accenten (diakritische tekens) van lexemes worden verwijderd. |

### <a name="functions-extensions"></a>Extensies voor functies

> [!div class="mx-tableFixed"]
> | **Switch** | **Beschrijving** |
> |---|---|
> | [autoinc](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.7) | Functies voor auto incrementele velden. |
> | [earthdistance](https://www.postgresql.org/docs/current/static/earthdistance.html) | Biedt een manier om grote afstanden te berekenen op het Opper vlak van de aarde. |
> | [fuzzystrmatch](https://www.postgresql.org/docs/current/static/fuzzystrmatch.html) | Biedt verschillende functies voor het bepalen van overeenkomsten en de afstand tussen teken reeksen. |
> | [\_gebruikers naam invoegen](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.8) | Functies voor het bijhouden wie een tabel heeft gewijzigd. |
> | [intagg](https://www.postgresql.org/docs/current/intagg.html) | Geheel getal, aggregator en Enumerator (verouderd). |
> | [intarray](https://www.postgresql.org/docs/current/static/intarray.html) | Biedt functies en Opera tors voor het bewerken van Null-vrije matrices van gehele getallen. |
> | [moddatetime](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.9) | Functies voor het bijhouden van de tijd van de laatste wijziging. |
> | [pgcrypto](https://www.postgresql.org/docs/current/static/pgcrypto.html) | Voorziet in cryptografische functies. |
> | [pag\_deelman](https://pgxn.org/dist/pg_partman/doc/pg_partman.html) | Hiermee beheert u gepartitioneerde tabellen op tijd of ID. |
> | [pag\_trgm](https://www.postgresql.org/docs/current/static/pgtrgm.html) | Voorziet in functies en Opera tors voor het bepalen van de gelijkenis van alfanumerieke tekst op basis van het afstemmen van Trigram. |
> | [verfijnt](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.5) | Functies voor het implementeren van referentiële integriteit (verouderd). |
> | analyse van sessie-\_ | Functies voor het uitvoeren van query's op hstore-matrices. |
> | [tablefunc](https://www.postgresql.org/docs/current/static/tablefunc.html) | Biedt functies voor het bewerken van hele tabellen, inclusief Kruistabel query's. |
> | [tcn](https://www.postgresql.org/docs/current/tcn.html) | Geactiveerde wijzigings meldingen. |
> | [timetravel](https://www.postgresql.org/docs/current/contrib-spi.html#id-1.11.7.45.6) | Functies voor het implementeren van tijd reizen. |
> | [uuid-ossp](https://www.postgresql.org/docs/current/static/uuid-ossp.html) | Genereert Universele unieke id's (UUID). |

### <a name="hyperscale-extensions"></a>Grootschalige-extensies

> [!div class="mx-tableFixed"]
> | **Switch** | **Beschrijving** |
> |---|---|
> | [citus](https://github.com/citusdata/citus) | Gedistribueerde data base Citus. |
> | Shard\_-herbalancer | Gegevens in een server groep veilig opnieuw verdelen in geval van toevoeging of verwijdering van knoop punten. |

### <a name="index-types-extensions"></a>Extensies van index typen

> [!div class="mx-tableFixed"]
> | **Switch** | **Beschrijving** |
> |---|---|
> | [bloei](https://www.postgresql.org/docs/current/bloom.html) | Toegangs methode voor bloei-index op basis van een hand tekening bestand. |
> | [btree\_eginnen](https://www.postgresql.org/docs/current/static/btree-gin.html) | Biedt een voor beeld van EGINNEN-operator klassen waarmee B-structuur-like-gedrag voor bepaalde gegevens typen kan worden geïmplementeerd. |
> | [btree\_e-ster](https://www.postgresql.org/docs/current/static/btree-gist.html) | Bevat de klassen van een categorie-operator die de B-structuur implementeert. |

### <a name="language-extensions"></a>Taal extensies

> [!div class="mx-tableFixed"]
> | **Switch** | **Beschrijving** |
> |---|---|
> | [plpgsql](https://www.postgresql.org/docs/current/static/plpgsql.html) | Door PL/pgSQL geladen procedure taal. |

### <a name="miscellaneous-extensions"></a>Diverse uitbrei dingen

> [!div class="mx-tableFixed"]
> | **Switch** | **Beschrijving** |
> |---|---|
> | [adminpack](https://www.postgresql.org/docs/current/adminpack.html) | Beheer functies voor PostgreSQL. |
> | [amcheck](https://www.postgresql.org/docs/current/amcheck.html) | Functies voor het controleren van relatie-integriteit. |
> | [bestands\_fdw](https://www.postgresql.org/docs/current/file-fdw.html) | Externe-gegevens wrapper voor toegang tot platte bestanden. |
> | [pageinspect](https://www.postgresql.org/docs/current/pageinspect.html) | De inhoud van database pagina's op een laag niveau controleren. |
> | [pag\_buffercache](https://www.postgresql.org/docs/current/static/pgbuffercache.html) | Biedt een manier om te controleren wat er gebeurt in de gedeelde buffer cache in realtime. |
> | [pag\_cron](https://github.com/citusdata/pg_cron) | Job Scheduler voor PostgreSQL. |
> | [pag\_freespacemap](https://www.postgresql.org/docs/current/pgfreespacemap.html) | Bekijk de toewijzing van de vrije ruimte (FSM). |
> | [pag\_-warm](https://www.postgresql.org/docs/current/static/pgprewarm.html) | Biedt een manier om relatie gegevens in de buffer cache te laden. |
> | [pagina\_stat\_-instructies](https://www.postgresql.org/docs/current/static/pgstatstatements.html) | Biedt een manier om uitvoerings statistieken op te sporen van alle SQL-instructies die worden uitgevoerd door een server. Zie de sectie ' pg_stat_statements ' voor informatie over deze uitbrei ding. |
> | [zicht baarheid van pagina's\_](https://www.postgresql.org/docs/current/pgvisibility.html) | Bekijk de informatie over de zichtbaarheids kaart (VM) en de zicht baarheid op pagina niveau. |
> | [pgrowlocks](https://www.postgresql.org/docs/current/static/pgrowlocks.html) | Biedt een manier om vergrendelings informatie op rijniveau te tonen. |
> | [pgstattuple](https://www.postgresql.org/docs/current/static/pgstattuple.html) | Biedt een manier om statistieken op tupleniveau weer te geven. |
> | [post gres\_fdw](https://www.postgresql.org/docs/current/static/postgres-fdw.html) | Externe-gegevens wrapper die wordt gebruikt voor toegang tot gegevens die zijn opgeslagen in externe PostgreSQL-servers. Zie de sectie ' dblink and postgres_fdw ' voor informatie over deze uitbrei ding.|
> | [sslinfo](https://www.postgresql.org/docs/current/sslinfo.html) | Informatie over SSL-certificaten. |
> | [TSM\_systeem\_rijen](https://www.postgresql.org/docs/current/tsm-system-rows.html) | Methode TABLESAMPLE, waarmee het aantal rijen als een limiet wordt geaccepteerd. |
> | [TSM\_systeem\_tijd](https://www.postgresql.org/docs/current/tsm-system-time.html) | TABLESAMPLE-methode, waarmee de tijd in milliseconden als limiet wordt geaccepteerd. |
> | [hypopg](https://hypopg.readthedocs.io/en/latest/) | Biedt een manier om hypothetische indexen te maken die geen CPU of schijf kosten. |
> | [dblink](https://www.postgresql.org/docs/current/dblink.html) | Een module die verbindingen met andere PostgreSQL-data bases in een database sessie ondersteunt. Zie de sectie ' dblink and postgres_fdw ' voor informatie over deze uitbrei ding. |
> | [xml2](https://www.postgresql.org/docs/current/xml2.html) | XPath-query's en XSLT. |


### <a name="postgis-extensions"></a>PostGIS-extensies

> [!div class="mx-tableFixed"]
> | **Switch** | **Beschrijving** |
> |---|---|
> | [Postgis](https://www.postgis.net/), postgis\_topologie, postgis\_Tiger\_geocodeer, postgis\_sfcgal | Ruimtelijke en geografische objecten voor PostgreSQL. |
> | Address\_Standardization, adres\_Standardization\_data\_ons | Wordt gebruikt om een adres te parseren in onderdeel elementen. Wordt gebruikt ter ondersteuning van de stap voor het normaliseren van het adres voor geocodering. |
> | postgis\_sfcgal | PostGIS SFCGAL-functies. |
> | postgis\_Tiger\_geocodeer | PostGIS Tiger geocodeer en reverse geocodeer. |
> | postgis\_-topologie | Ruimtelijke typen en functies van de PostGIS-topologie. |


## <a name="pg_stat_statements"></a>pg_stat_statements
De [uitbrei ding pag\_stat\_-instructies](https://www.postgresql.org/docs/current/pgstatstatements.html) is vooraf geladen op elke Azure database for postgresql-server, zodat u de uitvoerings statistieken van SQL-instructies kunt volgen.

De instelling `pg_stat_statements.track` bepaalt welke instructies worden geteld door de uitbrei ding. De standaard instelling is `top`, wat betekent dat alle instructies die rechtstreeks door clients worden uitgegeven, worden bijgehouden. De twee andere tracking niveaus zijn `none` en `all`. Deze instelling kan worden geconfigureerd als een server parameter via de [Azure Portal](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-portal) of de [Azure cli](https://docs.microsoft.com/azure/postgresql/howto-configure-server-parameters-using-cli).

Er is sprake van een afweging tussen de informatie over het uitvoeren van query's pg_stat_statements biedt en het effect op Server prestaties, terwijl elke SQL-instructie wordt geregistreerd. Als u de extensie pg_stat_statements niet actief gebruikt, raden we u aan om `pg_stat_statements.track` in te stellen op `none`. Sommige bewakings services van derden zijn afhankelijk van pg_stat_statements om query prestaties te kunnen leveren, dus bevestig of dit het geval voor u is.

## <a name="dblink-and-postgres_fdw"></a>dblink en postgres_fdw
U kunt dblink en postgres_fdw gebruiken om verbinding te maken tussen een PostgreSQL-server of een andere Data Base op dezelfde server. De ontvangende server moet verbindingen vanaf de verzendende server via de Firewall toestaan. Als u deze uitbrei dingen wilt gebruiken om verbinding te maken tussen Azure Database for PostgreSQL servers, stelt u **toegang tot Azure-Services toestaan** in op aan. U moet deze instelling ook inschakelen als u de uitbrei dingen wilt gebruiken om naar dezelfde server te gaan. De instelling **toegang tot Azure-Services toestaan vindt u** op de pagina Azure portal voor de post gres-server onder **verbindings beveiliging**. **Toegang tot Azure-Services toestaan** op whitelists alle Azure ip's.

Momenteel worden uitgaande verbindingen van Azure Database for PostgreSQL niet ondersteund, met uitzonde ring van verbindingen met andere Azure Database for PostgreSQL-servers.
