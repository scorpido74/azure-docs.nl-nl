---
title: Artikel over bekende problemen/migratie beperkingen met online migraties naar Azure Database for MySQL | Microsoft Docs
description: Meer informatie over bekende problemen/migratie beperkingen met online migraties voor Azure Database for MySQL.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 11/08/2019
ms.openlocfilehash: 39c1928f1d38276418b2e1a3e766c4b9d8a0d8d2
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/10/2019
ms.locfileid: "73902788"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-db-for-mysql"></a>Bekende problemen/migratie beperkingen met online migraties naar Azure DB voor MySQL

Bekende problemen en beperkingen die zijn gekoppeld aan online migraties van MySQL naar Azure Database for MySQL worden beschreven in de volgende secties.

## <a name="online-migration-configuration"></a>Configuratie van online migratie

- De versie van de MySQL-bron server moet versie 5.6.35, 5.7.18 of hoger zijn
- Azure Database for MySQL ondersteunt:
  - MySQL-Community-editie
  - InnoDB-engine
- Migratie van dezelfde versie. Het migreren van MySQL 5,6 naar Azure Database for MySQL 5,7 wordt niet ondersteund.
- Binaire logboek registratie inschakelen in my. ini (Windows) of my. cnf (UNIX)
  - Stel Server_id in op een wille keurig getal dat groter is dan of gelijk is aan 1, bijvoorbeeld Server_id = 1 (alleen voor MySQL 5,6)
  - Log-bin instellen = \<pad > (alleen voor MySQL 5,6)
  - Binlog_format instellen = rij
  - Expire_logs_days = 5 (alleen aanbevolen voor MySQL 5,6)
- De gebruiker moet de rol ReplicationAdmin hebben.
- Sorteringen die zijn gedefinieerd voor de bron-MySQL-data base zijn dezelfde als die zijn gedefinieerd in doel Azure Database for MySQL.
- Het schema moet overeenkomen tussen de bron-MySQL-data base en de doel database in Azure Database for MySQL.
- Het schema in de doel Azure Database for MySQL mag geen refererende sleutels hebben. Gebruik de volgende query om refererende sleutels te verwijderen:
    ```
    SET group_concat_max_len = 8192;
    SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery
    FROM
    (SELECT 
    KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery,
        CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery
        FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC
        WHERE
          KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME
          AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA
      AND KCU.REFERENCED_TABLE_SCHEMA = ['schema_name']) Queries
      GROUP BY SchemaName;
    ```

    Voer het 'drop foreign key'-script (de tweede kolom) uit in het queryresultaat.
- Het schema in de doel Azure Database for MySQL mag geen triggers hebben. Triggers in doel database weghalen:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Beperkingen van gegevens typen

- **Beperking**: als er een JSON-gegevens type is in de MySQL-bron database, mislukt de migratie tijdens continue synchronisatie.

    **Tijdelijke oplossing**: Wijzig het JSON-gegevens type in middel grote tekst of LONGTEXT in de bron-MySQL-data base.

- **Beperking**: als er geen primaire sleutel voor tabellen is, mislukt de continue synchronisatie.

    **Tijdelijke oplossing**: Stel tijdelijk een primaire sleutel in voor de tabel voor migratie om door te gaan. U kunt de primaire sleutel verwijderen nadat de gegevens migratie is voltooid.

## <a name="lob-limitations"></a>LOB-beperkingen

Large Object LOB-kolommen zijn kolommen die groot kunnen worden uitgebreid. Voor MySQL worden middel grote tekst, LONGTEXT, blob, Mediumblob, Longblob, etc., een deel van de gegevens typen van LOB zijn.

- **Beperking**: als LOB-gegevens typen worden gebruikt als primaire sleutel, zal de migratie mislukken.

    **Tijdelijke oplossing**: Vervang de primaire sleutel door andere gegevens typen of kolommen die niet LOB zijn.

- **Beperking**: als de LOB-kolom (lengte van large object) groter is dan 32 KB, kunnen gegevens worden afgekapt bij het doel. U kunt de lengte van de LOB-kolom controleren met behulp van deze query:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Tijdelijke oplossing**: als u een LOB-object hebt dat groter is dan 32 KB, neemt u contact op met het technische team om [Azure data base-migraties te vragen](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>Beperkingen bij het online migreren van AWS RDS MySQL

Wanneer u probeert een online migratie uit te voeren vanaf AWS RDS MySQL naar Azure Database for MySQL, kunt u de volgende fouten tegen komen.

- **Fout:** De data base{0}heeft een refererende sleutel (s) op het doel. Corrigeer het doel en start een nieuwe gegevensmigratie. Uitvoeren onder script op doel om de refererende sleutel (s) weer te geven

  **Beperking**: als u een refererende sleutel in uw schema hebt, zullen de initiële belasting en continue synchronisatie van de migratie mislukken.
  **Tijdelijke oplossing**: Voer het volgende script uit in MySQL Workbench om het neerzetten van refererende sleutel script uit te pakken en refererende-sleutel script toe te voegen:

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **Fout:** De data base{0}bestaat niet op de server. Opgegeven MySQL-bronserver is hoofdlettergevoelig. Controleer de databasenaam.

  **Beperking**: bij het migreren van een MySQL-data base naar Azure met behulp van de opdracht regel interface (CLI), kunnen gebruikers deze fout aanraken. De service kan de data base niet vinden op de bron server, omdat u mogelijk een onjuiste database naam hebt opgegeven of omdat de data base niet aanwezig is op de vermelde server. Opmerking database namen zijn hoofdletter gevoelig.

  **Tijdelijke oplossing**: Geef de exacte database naam op en probeer het opnieuw.

- **Fout:** Er zijn tabellen met dezelfde naam in de data base {data base}. Azure Database for MySQL biedt geen ondersteuning voor hoofdlettergevoelige tabellen.

  **Beperking**: deze fout treedt op wanneer er twee tabellen zijn met dezelfde naam in de bron database. Azure Database for MySQL ondersteunt geen hoofdletter gevoelige tabellen.

  **Tijdelijke oplossing**: werk de tabel namen bij zodat deze uniek zijn en probeer het opnieuw.

- **Fout:** De doel database {Data Base} is leeg. Migreer het schema.

  **Beperking**: deze fout treedt op wanneer de doel Azure database for MySQL data base niet over het vereiste schema beschikt. Schema migratie is vereist om het migreren van gegevens naar uw doel in te scha kelen.

  **Tijdelijke oplossing**: [Migreer het schema](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#migrate-the-sample-schema) van de bron database naar de doel database.

## <a name="other-limitations"></a>Andere beperkingen

- Een wachtwoord teken reeks met accolades {} aan het begin en einde van de teken reeks van het wacht woord wordt niet ondersteund. Deze beperking is van toepassing op beide verbindingen met de bron-MySQL en doel Azure Database for MySQL.
- De volgende DDLs worden niet ondersteund:
  - Alle partitie DDLs
  - Tabel neerzetten
  - Tabel naam wijzigen
- Met behulp van de *Alter table < table_name > kolom < COLUMN_NAME* >-instructie toevoegen om kolommen toe te voegen aan het begin of aan het midden van een tabel niet wordt ondersteund. De *tabel Alter table < table_name > kolom < COLUMN_NAME toevoegen* > voegt de kolom toe aan het einde van de tabel.
- Indexen die slechts op een deel van de kolom gegevens zijn gemaakt, worden niet ondersteund. De volgende instructie is een voor beeld van het maken van een index met slechts een deel van de kolom gegevens:

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- In Azure Database Migration Service is de limiet van de data bases die worden gemigreerd in één enkele migratie activiteit vier.

- **Fout:** De Rijgrootte is te groot (> 8126). Het kan helpen om sommige kolommen te wijzigen in tekst of BLOB. In de huidige rij-indeling is het BLOB-voor voegsel van 0 bytes inline opgeslagen.

  **Beperking**: deze fout treedt op wanneer u migreert naar Azure database for MySQL met behulp van de InnoDB-opslag engine en de grootte van de tabelrij te groot is (> 8126 bytes).

  **Tijdelijke oplossing**: werk het schema bij van de tabel met een Rijgrootte groter dan 8126 bytes. Het is niet raadzaam de strikte modus te wijzigen, omdat de gegevens worden afgekapt. Het wijzigen van de page_size wordt niet ondersteund.
