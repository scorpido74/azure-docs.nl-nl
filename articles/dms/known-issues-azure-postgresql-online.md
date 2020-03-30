---
title: 'Bekende problemen: Online migraties van PostgreSQL naar Azure Database voor PostgreSQL'
titleSuffix: Azure Database Migration Service
description: Meer informatie over bekende problemen en migratiebeperkingen met onlinemigraties van PostgreSQL naar Azure Database voor PostgreSQL met behulp van de Azure Database Migration Service.
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
ms.openlocfilehash: 3d1bc627ccb8814ab2dfb61fb0653ef0ac644038
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80235270"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-from-postgresql-to-azure-db-for-postgresql"></a>Bekende problemen/migratiebeperkingen met online migraties van PostgreSQL naar Azure DB voor PostgreSQL

Bekende problemen en beperkingen in verband met online migraties van PostgreSQL naar Azure Database voor PostgreSQL worden beschreven in de volgende secties.

## <a name="online-migration-configuration"></a>Online migratieconfiguratie

- De bron PostgreSQL-server moet versie 9.4, 9.5, 9.6, 10 of 11 uitvoeren. Zie voor meer informatie het artikel [Supported PostgreSQL Database Versions](../postgresql/concepts-supported-versions.md) (Ondersteunde versies van de PostgreSQL-database).
- Alleen migraties naar dezelfde of een hogere versie worden ondersteund. Het migreren van PostgreSQL 9.5 naar Azure Database voor PostgreSQL 9.6 of 10 wordt bijvoorbeeld ondersteund, maar migratie van PostgreSQL 11 naar PostgreSQL 9.6 wordt niet ondersteund.
- Als u logische replicatie wilt inschakelen in het **bestand PostgreSQL-postgresql.conf van de bron PostgreSQL.conf,** stelt u de volgende parameters in:
  - **wal_level** = logisch
  - **max_replication_slots** = [ten minste het maximum aantal databases voor migratie]; Als u vier databases wilt migreren, stelt u de waarde in op ten minste 4.
  - **max_wal_senders** = [aantal gelijktijdig draaiende databases]; de aanbevolen waarde is 10
- DMS-agent IP toevoegen aan de bron PostgreSQL pg_hba.conf
  1. Noteer het IP-adres van DMS nadat u een exemplaar van Azure Database Migration Service hebt ingericht.
  2. Voeg het IP-adres toe aan het pg_hba.conf-bestand zoals weergegeven:

      ```
          host  all     172.16.136.18/10    md5
          host  replication postgres    172.16.136.18/10    md5
      ```

- De gebruiker moet de replicatierol op de server die de brondatabase host, hebben.
- De bron- en doeldatabaseschema's moeten overeenkomen.
- Het schema in de doelazure-database voor PostgreSQL-Single-server mag geen externe sleutels hebben. Gebruik de volgende query om buitenlandse sleutels te laten vallen:

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

- Het schema in de doelazure-database voor PostgreSQL-Single-server mag geen triggers bevatten. Gebruik het volgende om triggers in de doeldatabase uit te schakelen:

     ```
    SELECT Concat('DROP TRIGGER ', Trigger_Name, ';') FROM  information_schema.TRIGGERS WHERE TRIGGER_SCHEMA = 'your_schema';
     ```

## <a name="datatype-limitations"></a>Beperkingen voor gegevenstype

  **Beperking:** als er geen primaire sleutel op tabellen staat, worden wijzigingen mogelijk niet gesynchroniseerd met de doeldatabase.

  **Tijdelijke oplossing**: stel tijdelijk een primaire sleutel in om de tabel voor migratie voort te zetten. U de primaire sleutel verwijderen nadat de gegevensmigratie is voltooid.

## <a name="limitations-when-migrating-online-from-aws-rds-postgresql"></a>Beperkingen bij online migreren van AWS RDS PostgreSQL

Wanneer u een online migratie van AWS RDS PostgreSQL naar Azure Database voor PostgreSQL probeert uit te voeren, u de volgende fouten tegenkomen.

- **Fout**: De standaardwaarde van kolom {kolom}' in tabel {tabel}' in database {database} is verschillend op bron- en doelservers. De waarde {waarde op bron} in de bron en {waarde op doel} op het doel.

  **Beperking:** deze fout treedt op wanneer de standaardwaarde op een kolomschema verschilt tussen de bron- en doeldatabases.
  **Tijdelijke oplossing:** zorg ervoor dat het schema op het doel overeenkomt met het schema op de bron. Raadpleeg voor meer informatie over het migratieschema de [online migratiedocumentatie van Azure PostgreSQL.](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)

- **Fout**: Doeldatabase {database}' heeft tabellen '{number of tables}' waarbij als brondatabase '{database}' tabellen '{number of tables}' tabellen hebben. Het aantal tabellen op de bron- en doeldatabase moet overeenkomen.

  **Beperking:** deze fout treedt op wanneer het aantal tabellen verschilt tussen de bron- en doeldatabases.

  **Tijdelijke oplossing:** zorg ervoor dat het schema op het doel overeenkomt met het schema op de bron. Raadpleeg voor meer informatie over het migratieschema de [online migratiedocumentatie van Azure PostgreSQL.](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)

- **Fout:** De brondatabase {database} is leeg.

  **Beperking:** deze fout treedt op wanneer de brondatabase leeg is. Dit is waarschijnlijk omdat u de verkeerde database als bron hebt geselecteerd.

  **Tijdelijke oplossing**: Controleer de brondatabase die u hebt geselecteerd voor migratie en probeer het opnieuw.

- **Fout:** De doeldatabase {database} is leeg. Migreer het schema.

  **Beperking:** deze fout treedt op wanneer er geen schema in de doeldatabase staat. Zorg ervoor dat het schema op het doel overeenkomt met het schema op de bron.
  **Tijdelijke oplossing:** zorg ervoor dat het schema op het doel overeenkomt met het schema op de bron. Raadpleeg voor meer informatie over het migratieschema de [online migratiedocumentatie van Azure PostgreSQL.](https://docs.microsoft.com/azure/dms/tutorial-postgresql-azure-postgresql-online#migrate-the-sample-schema)

## <a name="other-limitations"></a>Andere beperkingen

- De databasenaam kan geen puntkomma (;) bevatten.
- Een vastgelegde tabel moet een primaire sleutel hebben. Als een tabel geen primaire sleutel heeft, is het resultaat van recordbewerkingen verwijderen en bijwerken onvoorspelbaar.
- Het bijwerken van een segment primaire sleutel wordt genegeerd. In dergelijke gevallen wordt het toepassen van een dergelijke update door het doel geïdentificeerd als een update die geen rijen heeft bijgewerkt en zal resulteren in een record dat naar de tabel met uitzonderingen is geschreven.
- Migratie van meerdere tabellen met dezelfde naam, maar een andere aanvraag (bijvoorbeeld tabel1, TABEL1 en Tabel1) kan onvoorspelbaar gedrag veroorzaken en wordt daarom niet ondersteund.
- Wijzigingsverwerking van [CREËREN | WIJZIGING | DALING | TRUNCATE] tabel DDLs wordt niet ondersteund.
- In Azure Database Migration Service kan één migratieactiviteit slechts maximaal vier databases bieden.
