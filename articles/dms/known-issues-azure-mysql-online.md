---
title: 'Bekende problemen: Online migraties naar Azure Database voor MySQL'
titleSuffix: Azure Database Migration Service
description: Meer informatie over bekende problemen en migratiebeperkingen met onlinemigraties naar Azure Database voor MySQL wanneer u de Azure Database Migration Service gebruikt.
services: database-migration
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom:
- seo-lt-2019
- seo-dt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 8c3de28ea934302086a5b14e61482e6a4ab9a7ca
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235280"
---
# <a name="online-migration-issues--limitations-to-azure-db-for-mysql-with-azure-database-migration-service"></a>Problemen met onlinemigratie & beperkingen voor Azure DB voor MySQL met Azure Database Migration Service

Bekende problemen en beperkingen in verband met online migraties van MySQL naar Azure Database voor MySQL worden beschreven in de volgende secties.

## <a name="online-migration-configuration"></a>Online migratieconfiguratie


- De bron-MySQL Server-versie moet versie 5.6.35, 5.7.18 of hoger zijn
- Azure Database voor MySQL-ondersteuningen:
  - MySQL-community-editie
  - InnoDB motor
- Dezelfde versie migratie. Het migreren van MySQL 5.6 naar Azure Database voor MySQL 5.7 wordt niet ondersteund.
- Binaire logboekregistratie inschakelen in my.ini (Windows) of my.cnf (Unix)
  - Stel Server_id in op een groter getal of gelijk aan 1, bijvoorbeeld Server_id=1 (alleen voor MySQL 5.6)
  - Logboekopslaginstellen = \<pad> (alleen voor MySQL 5.6)
  - Binlog_format instellen = rij
  - Expire_logs_days = 5 (aanbevolen - alleen voor MySQL 5.6)
- De gebruiker moet de rol ReplicationAdmin hebben.
- Collaties gedefinieerd voor de bron MySQL-database zijn dezelfde als die gedefinieerd in doel Azure Database voor MySQL.
- Schema moet overeenkomen tussen de bron MySQL-database en de doeldatabase in Azure Database voor MySQL.
- Schema in doel Azure Database voor MySQL mag geen buitenlandse sleutels hebben. Gebruik de volgende query om buitenlandse sleutels te laten vallen:
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
- Schema in doel Azure Database voor MySQL mag geen triggers hebben. Triggers in doeldatabase laten vallen:
    ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
    ```

## <a name="datatype-limitations"></a>Beperkingen voor gegevenstype

- **Beperking:** Als er een JSON-gegevenstype in de bron-MySQL-database staat, mislukt migratie tijdens continue synchronisatie.

    **Tijdelijke oplossing:** JSON-gegevenstype wijzigen in medium tekst of longtext in de MySQL-database van de bron.

- **Beperking:** Als er geen primaire sleutel op tabellen staat, mislukt continue synchronisatie.

    **Tijdelijke oplossing**: stel tijdelijk een primaire sleutel in om de tabel voor migratie voort te zetten. U de primaire sleutel verwijderen nadat de gegevensmigratie is voltooid.

## <a name="lob-limitations"></a>LOB-beperkingen

Lob-kolommen (Large Object) zijn kolommen die groot kunnen worden. Voor MySQL zijn medium tekst, Longtext, Blob, Mediumblob, Longblob, enz., enkele van de gegevenstypen van LOB.

- **Beperking:** Als LOB-gegevenstypen als primaire sleutels worden gebruikt, mislukt migratie.

    **Tijdelijke oplossing:** Vervang primaire sleutel door andere gegevenstypen of kolommen die geen LOB zijn.

- **Beperking:** Als de lengte van de kolom Groot object (LOB) groter is dan 32 KB, kunnen gegevens worden afgekapt bij het doel. U de lengte van de LOB-kolom controleren met behulp van deze query:
    ```
    SELECT max(length(description)) as LEN from catalog;
    ```

    **Tijdelijke oplossing:** Als u een LOB-object hebt dat groter is dan 32 KB, neemt u contact op met het engineeringteam [van Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

## <a name="limitations-when-migrating-online-from-aws-rds-mysql"></a>Beperkingen bij het online migreren van AWS RDS MySQL

Wanneer u een online migratie van AWS RDS MySQL naar Azure Database voor MySQL probeert uit te voeren, u de volgende fouten tegenkomen.

- **Fout:** Database{0}' heeft buitenlandse sleutel(s) op doel. Corrigeer het doel en start een nieuwe gegevensmigratie. Hieronder script op doel uitvoeren om de buitenlandse sleutel(s) op te sommen

  **Beperking:** Als u buitenlandse sleutels in uw schema hebt, mislukken de initiële belasting en continue synchronisatie van de migratie.
  **Tijdelijke oplossing:** Voer het volgende script uit in MySQL-werkbank om het drop foreign key script te extraheren en een buitenlands sleutelscript toe te voegen:

  ```
  SET group_concat_max_len = 8192; SELECT SchemaName, GROUP_CONCAT(DropQuery SEPARATOR ';\n') as DropQuery, GROUP_CONCAT(AddQuery SEPARATOR ';\n') as AddQuery FROM (SELECT KCU.REFERENCED_TABLE_SCHEMA as SchemaName, KCU.TABLE_NAME, KCU.COLUMN_NAME, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' DROP FOREIGN KEY ', KCU.CONSTRAINT_NAME) AS DropQuery, CONCAT('ALTER TABLE ', KCU.TABLE_NAME, ' ADD CONSTRAINT ', KCU.CONSTRAINT_NAME, ' FOREIGN KEY (`', KCU.COLUMN_NAME, '`) REFERENCES `', KCU.REFERENCED_TABLE_NAME, '` (`', KCU.REFERENCED_COLUMN_NAME, '`) ON UPDATE ',RC.UPDATE_RULE, ' ON DELETE ',RC.DELETE_RULE) AS AddQuery FROM INFORMATION_SCHEMA.KEY_COLUMN_USAGE KCU, information_schema.REFERENTIAL_CONSTRAINTS RC WHERE KCU.CONSTRAINT_NAME = RC.CONSTRAINT_NAME AND KCU.REFERENCED_TABLE_SCHEMA = RC.UNIQUE_CONSTRAINT_SCHEMA AND KCU.REFERENCED_TABLE_SCHEMA = 'SchemaName') Queries GROUP BY SchemaName;
  ```

- **Fout:** Database{0}' bestaat niet op de server. Opgegeven MySQL-bronserver is hoofdlettergevoelig. Controleer de databasenaam.

  **Beperking:** Wanneer u een MySQL-database migreert naar Azure met behulp van Command Line Interface (CLI), kunnen gebruikers deze fout raken. De service kan de database niet vinden op de bronserver, wat kan zijn omdat u mogelijk een onjuiste databasenaam hebt opgegeven of dat de database niet op de vermelde server bestaat. Opmerking database namen zijn case-gevoelig.

  **Tijdelijke oplossing:** geef de exacte databasenaam op en probeer het opnieuw.

- **Fout:** Er zijn tabellen met dezelfde naam in de database '{database}'. Azure Database for MySQL biedt geen ondersteuning voor hoofdlettergevoelige tabellen.

  **Beperking:** deze fout treedt op wanneer u twee tabellen met dezelfde naam in de brondatabase hebt. Azure Database voor MySQL biedt geen ondersteuning voor hoofdlettergevoelige tabellen.

  **Tijdelijke oplossing:** werk de tabelnamen bij om uniek te zijn en probeer het vervolgens opnieuw.

- **Fout:** De doeldatabase {database} is leeg. Migreer het schema.

  **Beperking:** deze fout treedt op wanneer de doelazure-database voor MySQL-database niet over het vereiste schema beschikt. Schemamigratie is vereist om het migreren van gegevens naar uw doel mogelijk te maken.

  **Tijdelijke oplossing:** [migreer het schema](https://docs.microsoft.com/azure/dms/tutorial-mysql-azure-mysql-online#migrate-the-sample-schema) van uw brondatabase naar de doeldatabase.

## <a name="other-limitations"></a>Andere beperkingen

- Een wachtwoordtekenreeks met het openen en sluiten van krullende haakjes { } aan het begin en einde van de wachtwoordtekenreeks wordt niet ondersteund. Deze beperking is van toepassing op zowel verbinding maken met bron MySQL als target Azure Database voor MySQL.
- De volgende DD's worden niet ondersteund:
  - Alle partitie-DDLs
  - Tabel neerzetten
  - Naam van tabel wijzigen
- Met de *wijzigingstabel <table_name> kolom <column_name>-instructie toevoegen* om kolommen toe te voegen aan het begin of het midden van een tabel, wordt deze niet ondersteund. De *tabel <table_name> kolom toevoegen <column_name>* voegt de kolom aan het einde van de tabel toe.
- Indexen die slechts op een deel van de kolomgegevens zijn gemaakt, worden niet ondersteund. De volgende instructie is een voorbeeld waarmee een index wordt gemaakt met slechts een deel van de kolomgegevens:

    ``` 
    CREATE INDEX partial_name ON customer (name(10));
    ```

- In Azure Database Migration Service is de limiet voor het migreren van databases in één enkele migratieactiviteit vier.

- **Fout:** Rijgrootte te groot (> 8126). Het wijzigen van bepaalde kolommen in TEKST of BLOB kan helpen. In de huidige rijnotatie wordt blob-voorvoegsel van 0 bytes inline opgeslagen.

  **Beperking:** deze fout treedt op wanneer u migreert naar Azure Database voor MySQL met behulp van de InnoDB-opslagengine en de grootte van de tabelrij te groot is (>8126 bytes).

  **Tijdelijke oplossing:** werk het schema van de tabel met een rijgrootte van meer dan 8126 bytes bij. We raden u aan de strikte modus te wijzigen, omdat de gegevens worden afgekapt. Het wijzigen van de page_size wordt niet ondersteund.
