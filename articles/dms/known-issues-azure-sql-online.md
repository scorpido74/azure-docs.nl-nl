---
title: 'Bekende problemen: Online migraties naar SQL Database'
titleSuffix: Azure Database Migration Service
description: Meer informatie over bekende problemen/migratiebeperkingen met online migraties naar Azure SQL Database met behulp van de Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: e7efdb7244e2c7e4651a4507b538123f8d320c1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650772"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database"></a>Bekende problemen/migratiebeperkingen met online migraties naar Azure SQL Database

Bekende problemen en beperkingen in verband met online migraties van SQL Server naar Azure SQL Database worden hieronder beschreven.

> [!IMPORTANT]
> Met online migraties van SQL Server naar Azure SQL Database wordt migratie van SQL_variant gegevenstypen niet ondersteund.

### <a name="migration-of-temporal-tables-not-supported"></a>Migratie van tijdelijke tabellen die niet worden ondersteund

**Symptoom**

Als uw brondatabase uit een of meer tijdelijke tabellen bestaat, mislukt uw databasemigratie tijdens de bewerking 'Volledige gegevensbelasting' en ziet u mogelijk het volgende bericht:

```
{ "resourceId":"/subscriptions/<subscription id>/resourceGroups/migrateready/providers/Microsoft.DataMigration/services/<DMS Service name>", "errorType":"Database migration error", "errorEvents":"["Capture functionalities could not be set. RetCode: SQL_ERROR SqlState: 42000 NativeError: 13570 Message: [Microsoft][SQL Server Native Client 11.0][SQL Server]The use of replication is not supported with system-versioned temporal table '[Application. Cities]' Line: 1 Column: -1 "]" }
```

 ![Voorbeeld van tijdelijke tabelfouten](media/known-issues-azure-sql-online/dms-temporal-tables-errors.png)

**Oplossing**

Voer de volgende stappen uit.

1. Zoek de temporele tabellen in uw bronschema met behulp van de onderstaande query.

     ``` 
     select name,temporal_type,temporal_type_desc,* from sys.tables where temporal_type <>0
     ```

2. Sluit deze tabellen uit uit het blad **Migratie-instellingen configureren,** waarop u tabellen voor migratie opgeeft.

3. Voer de migratieactiviteit opnieuw uit.

**Resources**

Zie het artikel [Tijdelijke tabellen voor](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables?view=sql-server-2017)meer informatie .

### <a name="migration-of-tables-includes-one-or-more-columns-with-the-hierarchyid-data-type"></a>Migratie van tabellen bevat een of meer kolommen met het gegevenstype hierarchyid

**Symptoom**

Mogelijk ziet u een SQL-uitzondering die suggereert dat 'ntext niet compatibel is met hierarchyid' tijdens de bewerking 'Volledige gegevensbelasting':

![voorbeeld van hiërarchische fouten](media/known-issues-azure-sql-online/dms-hierarchyid-errors.png)

**Oplossing**

Voer de volgende stappen uit.

1. Zoek de gebruikerstabellen met kolommen met het gegevenstype hierarchyid met de onderstaande query.

      ``` 
      select object_name(object_id) 'Table name' from sys.columns where system_type_id =240 and object_id in (select object_id from sys.objects where type='U')
      ```

2. Sluit deze tabellen uit uit het blad **Migratie-instellingen configureren,** waarop u tabellen voor migratie opgeeft.

3. Voer de migratieactiviteit opnieuw uit.

### <a name="migration-failures-with-various-integrity-violations-with-active-triggers-in-the-schema-during-full-data-load-or-incremental-data-sync"></a>Migratiefouten met verschillende integriteitsschendingen met actieve triggers in het schema tijdens 'Volledige gegevensbelasting' of 'Incrementele gegevenssynchronisatie'

**Oplossing**

Voer de volgende stappen uit.

1. Zoek de triggers die momenteel actief zijn in de brondatabase met de onderstaande query:

     ```
     select * from sys.triggers where is_disabled =0
     ```

2. Schakel de triggers in uw brondatabase uit met de stappen in het artikel [TRIGGER uitschakelen (Transact-SQL).](https://docs.microsoft.com/sql/t-sql/statements/disable-trigger-transact-sql?view=sql-server-2017)

3. Voer de migratieactiviteit opnieuw uit.

### <a name="support-for-lob-data-types"></a>Ondersteuning voor LOB-gegevenstypen

**Symptoom**

Als de lengte van de kolom Groot object (LOB) groter is dan 32 KB, kunnen gegevens worden afgekapt bij het doel. U de lengte van de LOB-kolom controleren met de onderstaande query:

``` 
SELECT max(DATALENGTH(ColumnName)) as LEN from TableName
```

**Oplossing**

Als u een LOB-kolom hebt die groter is dan 32 KB, neemt u contact op met het engineeringteam van [Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="issues-with-timestamp-columns"></a>Problemen met tijdstempelkolommen

**Symptoom**

Azure Database Migration Service migreert de waarde van de brontijdstempel niet. Azure Database Migration Service genereert in plaats daarvan een nieuwe tijdstempelwaarde in de doeltabel.

**Oplossing**

Als u Azure Database Migration Service nodig hebt om de exacte tijdstempelwaarde te migreren die is opgeslagen in de brontabel, neemt u contact op met het engineeringteam van [Ask Azure Database Migrations](mailto:AskAzureDatabaseMigrations@service.microsoft.com).

### <a name="data-migration-errors-dont-provide-additional-details-on-the-database-detailed-status-blade"></a>Fouten in gegevensmigratie geven geen aanvullende details over het gedetailleerde statusblad database

**Symptoom**

Wanneer u migratiefouten tegenkomt in de statusweergave Databasesdetails, bevat het selecteren van de koppeling **Gegevensmigratiefouten** op het bovenste lint mogelijk geen aanvullende details die specifiek zijn voor de migratiefouten.

![fouten in gegevensmigratie geen voorbeeld van details](media/known-issues-azure-sql-online/dms-data-migration-errors-no-details.png)

**Oplossing**

Gebruik de volgende stappen om specifieke foutgegevens te bekijken.

1. Sluit het gedetailleerde statusblad database om het migratieactiviteitsscherm weer te geven.

     ![migratieactiviteitsscherm](media/known-issues-azure-sql-online/dms-migration-activity-screen.png)

2. Selecteer **Foutgegevens weergeven** om specifieke foutberichten weer te geven waarmee u migratiefouten oplossen.

### <a name="geography-datatype-not-supported-in-sqldb-online-migration"></a>Aardrijkskundedatatype niet ondersteund in SQLDB online migratie

**Symptoom**

Migratie mislukt met een foutbericht met de volgende tekst:

     “** encountered a fatal error”, "errorEvents":<Table>.<Column> is of type 'GEOGRAPHY', which is not supported by 'Full Load' under 'Full LOB' support mode."

**Oplossing**

Terwijl azure database migratieservice het gegevenstype Geografie ondersteunt voor offlinemigraties naar Azure SQL Database, wordt het gegevenstype Geografie niet ondersteund voor onlinemigraties. Probeer alternatieve methoden om het gegevenstype bij de bron te wijzigen in een ondersteund type voordat u azure databasemigratieservice probeert te gebruiken voor een online migratie van deze database.

### <a name="supported-editions"></a>Ondersteunde edities

**Symptoom**

Migratie mislukt met een foutbericht met de volgende tekst:

    Migration settings validation error: The edition of the server [Business Intelligence Edition (64-bit)] does not match the supported edition(s) [Enterprise,Standard,Developer].

**Oplossing**

Ondersteuning voor online migraties naar Azure SQL Database met Azure Database Migration Service geldt alleen voor de enterprise-, standard- en developer-edities. Zorg ervoor dat u een ondersteunde editie gebruikt voordat u het migratieproces start.
