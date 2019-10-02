---
title: Artikel over bekende problemen/migratie beperkingen met online migraties van PostgreSQL naar Azure Database for PostgreSQL-één server | Microsoft Docs
description: Meer informatie over bekende problemen/migratie beperkingen met online migraties van PostgreSQL naar Azure Database for PostgreSQL.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 10/03/2019
ms.openlocfilehash: 891e8a261e092de0ffcef3941dd48f01942a8030
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/02/2019
ms.locfileid: "71802593"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql-single-server"></a>Bekende problemen/migratie beperkingen met online migraties van PostgreSQL naar Azure DB voor PostgreSQL-één server

Bekende problemen en beperkingen die zijn gekoppeld aan online migraties van PostgreSQL naar Azure Database for PostgreSQL-Eén server worden in de volgende secties beschreven.

## <a name="online-migration-configuration"></a>Configuratie van online migratie

- Op de bron PostgreSQL-server moet versie 9.5.11, 9.6.7, of 10,3 of hoger worden uitgevoerd. Zie voor meer informatie het artikel [Supported PostgreSQL Database Versions](../postgresql/concepts-supported-versions.md) (Ondersteunde versies van de PostgreSQL-database).
- Er worden slechts dezelfde versie migraties ondersteund. Het migreren van PostgreSQL 9.5.11 naar Azure Database for PostgreSQL 9.6.7 wordt bijvoorbeeld niet ondersteund.

    > [!NOTE]
    > Voor PostgreSQL versie 10 ondersteunt DMS alleen de migratie van versie 10,3 naar Azure Database for PostgreSQL. We zijn van plan om nieuwere versies van PostgreSQL zeer snel te ondersteunen.

- Stel de volgende para meters in om logische replicatie in te scha kelen in het bestand **postgresql. conf van de bron postgresql** :
  - **wal_level** = logische
  - **max_replication_slots** = [maximum aantal data bases voor migratie]; Als u vier data bases wilt migreren, stelt u de waarde in op 4
  - **max_wal_senders** = [aantal data bases dat gelijktijdig wordt uitgevoerd]; de aanbevolen waarde is 10
- Het IP-adres van de DMS-agent toevoegen aan de bron PostgreSQL pg_hba. conf
  1. Noteer het DMS IP-adres nadat u klaar bent met het inrichten van een exemplaar van DMS.
  2. Voeg het IP-adres toe aan het bestand pg_hba. conf zoals wordt weer gegeven:

        host alle 172.16.136.18/10 MD5 post gres 172.16.136.18/10 MD5

- De gebruiker moet beschikken over de machtiging super gebruiker op de server die als host fungeert voor de bron database
- Afgezien van het hebben van ENUM in het schema van de bron database, moeten de bron-en doel database schema's overeenkomen.
- Het schema in de doel Azure Database for PostgreSQL-één server mag geen refererende sleutels hebben. Gebruik de volgende query om refererende sleutels te verwijderen:

    ```
                                SELECT Queries.tablename
           ,concat('alter table ', Queries.tablename, ' ', STRING_AGG(concat('DROP CONSTRAINT ', Queries.foreignkey), ',')) as DropQuery
                ,concat('alter table ', Queries.tablename, ' ', 
                                                STRING_AGG(concat('ADD CONSTRAINT ', Queries.foreignkey, ' FOREIGN KEY (', column_name, ')', 'REFERENCES ', foreign_table_name, '(', foreign_column_name, ')' ), ',')) as AddQuery
        FROM
        (SELECT
        tc.table_schema, 
        tc.constraint_name as foreignkey, 
        tc.table_name as tableName, 
        kcu.column_name, 
        ccu.table_schema AS foreign_table_schema,
        ccu.table_name AS foreign_table_name,
        ccu.column_name AS foreign_column_name 
    FROM 
        information_schema.table_constraints AS tc 
        JOIN information_schema.key_column_usage AS kcu
          ON tc.constraint_name = kcu.constraint_name
          AND tc.table_schema = kcu.table_schema
        JOIN information_schema.constraint_column_usage AS ccu
          ON ccu.constraint_name = tc.constraint_name
          AND ccu.table_schema = tc.table_schema
    WHERE constraint_type = 'FOREIGN KEY') Queries
      GROUP BY Queries.tablename;
    
    ```

    Voer het 'drop foreign key'-script (de tweede kolom) uit in het queryresultaat.

- Het schema in het doel Azure Database for PostgreSQL-één server mag geen triggers hebben. Gebruik het volgende om triggers in doel database uit te scha kelen:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>Beperkingen van gegevens typen

- **Beperking**: Als er een ENUM-gegevens type in de bron-PostgreSQL-data base is, mislukt de migratie tijdens doorlopende synchronisatie.

    **Tijdelijke oplossing**: Het ENUM-gegevens type wijzigen in teken variërend in Azure Database for PostgreSQL.

- **Beperking**: Als er geen primaire sleutel voor tabellen is, mislukt de continue synchronisatie.

    **Tijdelijke oplossing**: Stel tijdelijk een primaire sleutel voor de tabel in voor de migratie om door te gaan. U kunt de primaire sleutel verwijderen nadat de gegevens migratie is voltooid.

- **Beperking**: Het gegevens type JSONB wordt niet ondersteund voor migratie.

## <a name="lob-limitations"></a>LOB-beperkingen

Large Object LOB-kolommen zijn kolommen die groot kunnen groeien. Voor PostgreSQL zijn voor beelden van LOB-gegevens typen XML, JSON, afbeelding, tekst, enzovoort.

- **Beperking**: Als er LOB-gegevens typen worden gebruikt als primaire sleutel, mislukt de migratie.

    **Tijdelijke oplossing**: Vervang de primaire sleutel door andere gegevens typen of kolommen die niet LOB zijn.

- **Beperking**: Als de LOB-kolom (lengte van Large Object) groter is dan 32 KB, kunnen gegevens op het doel worden afgekapt. U kunt de lengte van de LOB-kolom controleren met behulp van deze query:

    ```
    SELECT max(length(cast(body as text))) as body FROM customer_mail
    ```

    **Tijdelijke oplossing**: Als u een LOB-object hebt dat groter is dan 32 KB, neemt u contact op met het technische team om [Azure data base-migraties te vragen](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

- **Beperking**: Als er sprake is van LOB-kolommen in de tabel en er geen primaire sleutel is ingesteld voor de tabel, kunnen de gegevens niet worden gemigreerd voor deze tabel.

    **Tijdelijke oplossing**: Stel tijdelijk een primaire sleutel in voor de tabel voor migratie om door te gaan. U kunt de primaire sleutel verwijderen nadat de gegevens migratie is voltooid.

## <a name="postgresql10-workaround"></a>Tijdelijke oplossing PostgreSQL10

PostgreSQL 10. x brengt verschillende wijzigingen aan in pg_xlog, waardoor de migratie niet zoals verwacht wordt uitgevoerd. Als u migreert van PostgreSQL 10. x naar Azure Database for PostgreSQL 10,3, voert u het volgende script uit op de bron-PostgreSQL-data base om de wrapper-functie te maken rond pg_xlog-functies.

```
BEGIN;
CREATE SCHEMA IF NOT EXISTS fnRenames;
CREATE OR REPLACE FUNCTION fnRenames.pg_switch_xlog() RETURNS pg_lsn AS $$ 
   SELECT pg_switch_wal(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_pause() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_pause(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_replay_resume() RETURNS VOID AS $$ 
   SELECT pg_wal_replay_resume(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_is_xlog_replay_paused() RETURNS boolean AS $$ 
   SELECT pg_is_wal_replay_paused(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name(lsn pg_lsn) RETURNS TEXT AS $$ 
   SELECT pg_walfile_name(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_replay_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_replay_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_last_xlog_receive_location() RETURNS pg_lsn AS $$ 
   SELECT pg_last_wal_receive_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_flush_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_flush_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_current_xlog_insert_location() RETURNS pg_lsn AS $$ 
   SELECT pg_current_wal_insert_lsn(); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlog_location_diff(lsn1 pg_lsn, lsn2 pg_lsn) RETURNS NUMERIC AS $$ 
   SELECT pg_wal_lsn_diff(lsn1, lsn2); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_xlogfile_name_offset(lsn pg_lsn, OUT TEXT, OUT INTEGER) AS $$ 
   SELECT pg_walfile_name_offset(lsn); $$ LANGUAGE SQL;
CREATE OR REPLACE FUNCTION fnRenames.pg_create_logical_replication_slot(slot_name name, plugin name, 
   temporary BOOLEAN DEFAULT FALSE, OUT slot_name name, OUT xlog_position pg_lsn) RETURNS RECORD AS $$ 
   SELECT slot_name::NAME, lsn::pg_lsn FROM pg_catalog.pg_create_logical_replication_slot(slot_name, plugin, 
   temporary); $$ LANGUAGE SQL;
ALTER USER PG_User SET search_path = fnRenames, pg_catalog, "$user", public;

-- DROP SCHEMA fnRenames CASCADE;
-- ALTER USER PG_User SET search_path TO DEFAULT;
COMMIT;
```

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>Beperkingen bij het online migreren van AWS RDS PostgreSQL

Wanneer u probeert een online migratie uit te voeren vanaf AWS RDS PostgreSQL naar Azure Database for PostgreSQL, kunnen de volgende fouten optreden.

- **Fout**: De standaardwaarde van de kolom {kolom} in tabel {tabel} in database {database} is verschillend op bron- en doelservers. De waarde {waarde op bron} in de bron en {waarde op doel} op het doel.

  **Beperking**: Deze fout treedt op wanneer de standaard waarde op een kolom schema afwijkt van de bron-en doel database.
  **Tijdelijke oplossing**: Zorg ervoor dat het schema op het doel overeenkomt met het schema op de bron. Raadpleeg de [documentatie over Azure postgresql Online Migration](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)voor meer informatie over het migreren van het schema.

- **Fout**: Doeldatabase {database} heeft {aantal tabellen} tabellen terwijl brondatabase {database} {aantal tabellen} tabellen heeft. Het aantal tabellen op de bron- en doeldatabase moet overeenkomen.

  **Beperking**: Deze fout treedt op wanneer het aantal tabellen verschilt tussen de bron-en doel database.
  **Tijdelijke oplossing**: Zorg ervoor dat het schema op het doel overeenkomt met het schema op de bron. Raadpleeg de [documentatie over Azure postgresql Online Migration](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)voor meer informatie over het migreren van het schema.

- **Fout:** De bron database {Data Base} is leeg.

  **Beperking**: Deze fout treedt op wanneer de bron database leeg is. Dit komt waarschijnlijk omdat u de verkeerde database als bron hebt geselecteerd.
  **Tijdelijke oplossing**: Controleer de bron database die u hebt geselecteerd voor migratie en probeer het opnieuw.

- **Fout:** De doel database {Data Base} is leeg. Migreer het schema.

  **Beperking**: Deze fout treedt op wanneer er geen schema is voor de doel database. Zorg ervoor dat schema op het doel overeenkomt met het schema op de bron.
  **Tijdelijke oplossing**: Zorg ervoor dat het schema op het doel overeenkomt met het schema op de bron. Raadpleeg de [documentatie over Azure postgresql Online Migration](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)voor meer informatie over het migreren van het schema.

## <a name="other-limitations"></a>Andere beperkingen

- De database naam mag geen punt komma (;)) bevatten.
- Het wacht woord voor het openen en sluiten van accolades {} wordt niet ondersteund. Deze beperking is van toepassing op beide verbindingen met de bron-PostgreSQL en de doel Azure Database for PostgreSQL.
- Een vastgelegde tabel moet een primaire sleutel hebben. Als een tabel geen primaire sleutel heeft, is het resultaat van de bewerkingen DELETE en UPDATE record onvoorspelbaar.
- Het bijwerken van een primaire-sleutel segment wordt genegeerd. In dergelijke gevallen wordt het Toep assen van een dergelijke update geïdentificeerd door het doel als een update waarbij geen rijen zijn bijgewerkt en resulteert dit in een record die naar de uitzonderingen tabel wordt geschreven.
- Migratie van meerdere tabellen met dezelfde naam, maar een andere case (bijvoorbeeld Tabel1, TABEL1 en Tabel1) kan onvoorspelbaar gedrag veroorzaken en wordt daarom niet ondersteund.
- De verwerking van [maken | ALTER | DROP] tabel DDLs worden ondersteund, tenzij ze worden vastgehouden in een interne functie-of procedure hoofdtekst blok of in andere geneste constructs. De volgende wijziging wordt bijvoorbeeld niet vastgelegd:

    ```
    CREATE OR REPLACE FUNCTION pg.create_distributors1() RETURNS void
    LANGUAGE plpgsql
    AS $$
    BEGIN
    create table pg.distributors1(did serial PRIMARY KEY,name varchar(40)
    NOT NULL);
    END;
    $$;
    ```

- Wijzigings verwerking (doorlopende synchronisatie) van AFGEKAPTe bewerkingen wordt niet ondersteund. De migratie van gepartitioneerde tabellen wordt niet ondersteund. Wanneer een gepartitioneerde tabel wordt gedetecteerd, gebeurt het volgende:

  - De data base rapporteert een lijst met bovenliggende en onderliggende tabellen.
  - De tabel wordt op het doel gemaakt als een normale tabel met dezelfde eigenschappen als de geselecteerde tabellen.
  - Als de bovenliggende tabel in de bron database dezelfde primaire-sleutel waarde heeft als de onderliggende tabellen, wordt de fout ' dubbele sleutel ' gegenereerd.

- In het DMS zijn de limieten voor het migreren van data bases in één enkele migratie activiteit vier.
