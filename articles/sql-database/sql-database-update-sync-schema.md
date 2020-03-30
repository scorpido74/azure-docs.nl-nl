---
title: De replicatie van schemawijzigingen in SQL Data Sync automatiseren
description: Meer informatie over het automatiseren van de replicatie van schemawijzigingen in Azure SQL Data Sync.
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: data sync
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: carlrab
ms.date: 11/14/2018
ms.openlocfilehash: 639901975bbb66b9f410bea297d9e48cd96d6d1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "73822433"
---
# <a name="automate-the-replication-of-schema-changes-in-azure-sql-data-sync"></a>De replicatie van schemawijzigingen in Azure SQL Data Sync automatiseren

MET SQL Data Sync kunnen gebruikers gegevens tussen Azure SQL-databases en on-premises SQL Server in één richting of in beide richtingen synchroniseren. Een van de huidige beperkingen van SQL Data Sync is een gebrek aan ondersteuning voor de replicatie van schemawijzigingen. Elke keer dat u het tabelschema wijzigt, moet u de wijzigingen handmatig toepassen op alle eindpunten, inclusief de hub en alle leden, en vervolgens het synchronisatieschema bijwerken.

In dit artikel wordt een oplossing geïntroduceerd om schemawijzigingen automatisch te repliceren naar alle SQL Data Sync-eindpunten.
1. Deze oplossing maakt gebruik van een DDL-trigger om schemawijzigingen bij te houden.
1. De trigger voegt de schemawijzigingsopdrachten in een trackingtabel in.
1. Deze trackingtabel wordt gesynchroniseerd met alle eindpunten met behulp van de Data Sync-service.
1. DML-triggers na het invoegen worden gebruikt om de schemawijzigingen op de andere eindpunten toe te passen.

In dit artikel wordt WIJZIGINGTABEL gebruikt als voorbeeld van een schemawijziging, maar deze oplossing werkt ook voor andere typen schemawijzigingen.

> [!IMPORTANT]
> We raden u aan dit artikel zorgvuldig te lezen, met name de secties over [probleemoplossing](#troubleshoot) en [andere overwegingen,](#other)voordat u begint met het implementeren van geautomatiseerde replicatie van schemawijzigingen in uw synchronisatieomgeving. We raden u ook aan synchronisatiegegevens te lezen [in meerdere cloud- en on-premises databases met SQL Data Sync.](sql-database-sync-data.md) Sommige databasebewerkingen kunnen de in dit artikel beschreven oplossing doorbreken. Aanvullende domeinkennis van SQL Server en Transact-SQL kan nodig zijn om deze problemen op te lossen.

![De replicatie van schemawijzigingen automatiseren](media/sql-database-update-sync-schema/automate-schema-changes.png)

## <a name="set-up-automated-schema-change-replication"></a>Geautomatiseerde replicatie van schemawijziging instellen

### <a name="create-a-table-to-track-schema-changes"></a>Een tabel maken om schemawijzigingen bij te houden

Maak een tabel om schemawijzigingen bij te houden in alle databases in de synchronisatiegroep:

```sql
CREATE TABLE SchemaChanges (
ID bigint IDENTITY(1,1) PRIMARY KEY,
SqlStmt nvarchar(max),
[Description] nvarchar(max)
)
```

Deze tabel heeft een identiteitskolom om de volgorde van schemawijzigingen bij te houden. U indien nodig meer velden toevoegen om meer informatie te registreren.

### <a name="create-a-table-to-track-the-history-of-schema-changes"></a>Een tabel maken om de geschiedenis van schemawijzigingen bij te houden

Maak op alle eindpunten een tabel om de id van de meest recent toegepaste opdracht voor schemawijziging bij te houden.

```sql
CREATE TABLE SchemaChangeHistory (
LastAppliedId bigint PRIMARY KEY
)
GO

INSERT INTO SchemaChangeHistory VALUES (0)
```

### <a name="create-an-alter-table-ddl-trigger-in-the-database-where-schema-changes-are-made"></a>Een ALTER TABLE DDL-trigger maken in de database waar schemawijzigingen worden aangebracht

Maak een DDL-trigger voor ALTER TABLE-bewerkingen. U hoeft deze trigger alleen te maken in de database waar schemawijzigingen worden aangebracht. Om conflicten te voorkomen, moet u alleen schemawijzigingen toestaan in één database in een synchronisatiegroep.

```sql
CREATE TRIGGER AlterTableDDLTrigger
ON DATABASE
FOR ALTER_TABLE
AS

-- You can add your own logic to filter ALTER TABLE commands instead of replicating all of them.

IF NOT (EVENTDATA().value('(/EVENT_INSTANCE/SchemaName)[1]', 'nvarchar(512)') like 'DataSync')

INSERT INTO SchemaChanges (SqlStmt, Description)
    VALUES (EVENTDATA().value('(/EVENT_INSTANCE/TSQLCommand/CommandText)[1]', 'nvarchar(max)'), 'From DDL trigger')
```

De trigger voegt een record in de schemawijzigingstrackingtabel in voor elke opdracht ALTER TABLE. In dit voorbeeld wordt een filter toegevoegd om te voorkomen dat schemawijzigingen worden gerepliceerd die zijn aangebracht onder schema **DataSync,** omdat deze waarschijnlijk zijn gemaakt door de gegevenssynchronisatieservice. Voeg meer filters toe als u alleen bepaalde typen schemawijzigingen wilt repliceren.

U ook meer triggers toevoegen om andere typen schemawijzigingen te repliceren. Maak bijvoorbeeld CREATE_PROCEDURE, ALTER_PROCEDURE en DROP_PROCEDURE activeert om wijzigingen in opgeslagen procedures te repliceren.

### <a name="create-a-trigger-on-other-endpoints-to-apply-schema-changes-during-insertion"></a>Een trigger maken op andere eindpunten om schemawijzigingen toe te passen tijdens het invoegen

Met deze trigger wordt de opdracht schemawijziging uitgevoerd wanneer deze wordt gesynchroniseerd met andere eindpunten. U moet deze trigger maken op alle eindpunten, behalve die waarin schemawijzigingen worden aangebracht (dat wil zeggen in de database waar de DDL-trigger `AlterTableDDLTrigger` in de vorige stap is gemaakt).

```sql
CREATE TRIGGER SchemaChangesTrigger
ON SchemaChanges
AFTER INSERT
AS
DECLARE \@lastAppliedId bigint
DECLARE \@id bigint
DECLARE \@sqlStmt nvarchar(max)
SELECT TOP 1 \@lastAppliedId=LastAppliedId FROM SchemaChangeHistory
SELECT TOP 1 \@id = id, \@SqlStmt = SqlStmt FROM SchemaChanges WHERE id \> \@lastAppliedId ORDER BY id
IF (\@id = \@lastAppliedId + 1)
BEGIN
    EXEC sp_executesql \@SqlStmt
        UPDATE SchemaChangeHistory SET LastAppliedId = \@id
    WHILE (1 = 1)
    BEGIN
        SET \@id = \@id + 1
        IF exists (SELECT id FROM SchemaChanges WHERE ID = \@id)
            BEGIN
                SELECT \@sqlStmt = SqlStmt FROM SchemaChanges WHERE ID = \@id
                EXEC sp_executesql \@SqlStmt
                UPDATE SchemaChangeHistory SET LastAppliedId = \@id
            END
        ELSE
            BREAK;
    END
END
```

Deze trigger wordt uitgevoerd na het invoegen en controleert of de huidige opdracht vervolgens moet worden uitgevoerd. De codelogica zorgt ervoor dat er geen schemawijzigingsinstructie wordt overgeslagen en dat alle wijzigingen worden toegepast, zelfs als de invoeging niet in orde is.

### <a name="sync-the-schema-change-tracking-table-to-all-endpoints"></a>De trackingtabel voor schemawijzigingen synchroniseren met alle eindpunten

U de schemawijzigingstrackingtabel synchroniseren met alle eindpunten met behulp van de bestaande synchronisatiegroep of een nieuwe synchronisatiegroep. Zorg ervoor dat de wijzigingen in de trackingtabel kunnen worden gesynchroniseerd met alle eindpunten, vooral wanneer u synchronisatie met één richting gebruikt.

Synchroniseer de geschiedenistabel voor schemawijziging niet, omdat die tabel een andere status op verschillende eindpunten behoudt.

### <a name="apply-the-schema-changes-in-a-sync-group"></a>De schemawijzigingen toepassen in een synchronisatiegroep

Alleen schemawijzigingen die zijn aangebracht in de database waar de DDL-trigger wordt gemaakt, worden gerepliceerd. Schemawijzigingen die in andere databases zijn aangebracht, worden niet gerepliceerd.

Nadat de schemawijzigingen zijn gerepliceerd naar alle eindpunten, moet u ook extra stappen ondernemen om het synchronisatieschema bij te werken om de nieuwe kolommen te starten of te stoppen met synchroniseren.

#### <a name="add-new-columns"></a>Nieuwe kolommen toevoegen

1.  Breng het schema wijzigen.

1.  Vermijd gegevenswijzigingen waarbij de nieuwe kolommen betrokken zijn totdat u de stap hebt voltooid waarmee de trigger wordt gemaakt.

1.  Wacht tot de schemawijzigingen op alle eindpunten worden toegepast.

1.  Vernieuw het databaseschema en voeg de nieuwe kolom toe aan het synchronisatieschema.

1.  Gegevens in de nieuwe kolom worden gesynchroniseerd tijdens de volgende synchronisatiebewerking.

#### <a name="remove-columns"></a>Kolommen verwijderen

1.  Verwijder de kolommen uit het synchronisatieschema. Data Sync stopt met het synchroniseren van gegevens in deze kolommen.

1.  Breng het schema wijzigen.

1.  Het databaseschema vernieuwen.

#### <a name="update-data-types"></a>Gegevenstypen bijwerken

1.  Breng het schema wijzigen.

1.  Wacht tot de schemawijzigingen op alle eindpunten worden toegepast.

1.  Het databaseschema vernieuwen.

1.  Als de nieuwe en oude gegevenstypen niet volledig compatibel `int` zijn `bigint` - bijvoorbeeld als u van naar - gaat, kan de synchronisatie mislukken voordat de stappen die de triggers maken zijn voltooid. Synchronisatie slaagt na een nieuwe poging.

#### <a name="rename-columns-or-tables"></a>De naam van kolommen of tabellen wijzigen

Als u de naam van kolommen of tabellen wijzigen, werkt Data Sync niet meer. Maak een nieuwe tabel of kolom, vul de gegevens in en verwijder vervolgens de oude tabel of kolom in plaats van de naam te wijzigen.

#### <a name="other-types-of-schema-changes"></a>Andere typen schemawijzigingen

Voor andere typen schemawijzigingen - bijvoorbeeld het maken van opgeslagen procedures of het laten vallen van een index - is het bijwerken van het synchronisatieschema niet vereist.

## <a name="troubleshoot-automated-schema-change-replication"></a><a name="troubleshoot"></a>Geautomatiseerde replicatie van schemawijzigingen oplossen

De replicatielogica die in dit artikel wordt beschreven, werkt niet meer in sommige situaties, bijvoorbeeld als u een schemawijziging hebt aangebracht in een on-premises database die niet wordt ondersteund in Azure SQL Database. In dat geval mislukt het synchroniseren van de schemawijzigingstrackingtabel. U moet dit probleem handmatig oplossen:

1.  Schakel de DDL-trigger uit en vermijd verdere schemawijzigingen totdat het probleem is opgelost.

1.  Schakel in de endpoint-database waar het probleem zich voordoet de trigger NA INVOEGEN uit op het eindpunt waar de schemawijziging niet kan worden aangebracht. Met deze actie kan de opdracht schemawijziging worden gesynchroniseerd.

1.  Trigger synchronisatie om de schemawijzigingstrackingtabel te synchroniseren.

1.  Queryer de tabel schemawijziging somin de id van de laatst toegepaste schemawijzigingsopdracht in de endpoint-database waar het probleem zich voordoet.

1.  Stel de trackingtabel voor schemawijzigingen op om alle opdrachten met een id op te sommen die groter zijn dan de id-waarde die u in de vorige stap hebt opgehaald.

    a.  Negeer de opdrachten die niet kunnen worden uitgevoerd in de endpoint-database. Je moet omgaan met het schema inconsistentie. De oorspronkelijke schemawijzigingen terugdraaien als de inconsistentie van invloed is op uw toepassing.

    b.  Pas handmatig de opdrachten toe die moeten worden toegepast.

1.  Werk de tabel schemawijzigingsgeschiedenis bij en stel de laatst toegepaste id in op de juiste waarde.

1.  Controleer nogmaals of het schema up-to-date is.

1.  Schakel de trigger AFTER INSERT in de tweede stap opnieuw in.

1.  Schakel de DDL-trigger in de eerste stap opnieuw in.

Als u de records in de tabel schemawijziging wilt opschonen, gebruikt u Verwijderen in plaats van TRUNCATE. Nooit de identiteitskolom opnieuw zaad in de trackingtabel voor schemawijzigingen met DBCC CHECKIDENT. U nieuwe schemawijzigingenbijhoudentabellen maken en de tabelnaam in de DDL-trigger bijwerken als opnieuw zaaien nodig is.

## <a name="other-considerations"></a><a name="other"></a>Andere overwegingen

-   Databasegebruikers die de hub- en liddatabases configureren, moeten voldoende toestemming hebben om de opdrachten voor schemawijzigingen uit te voeren.

-   U meer filters toevoegen aan de DDL-trigger om alleen schemawijzigingen in geselecteerde tabellen of bewerkingen te repliceren.

-   U alleen schemawijzigingen aanbrengen in de database waar de DDL-trigger wordt gemaakt.

-   Als u een wijziging aanbrengt in een on-premises SQL Server-database, controleert u of de schemawijziging wordt ondersteund in Azure SQL Database.

-   Als er schemawijzigingen worden aangebracht in andere databases dan de database waar de DDL-trigger wordt gemaakt, worden de wijzigingen niet gerepliceerd. Om dit probleem te voorkomen, u DDL-triggers maken om wijzigingen op andere eindpunten te blokkeren.

-   Als u het schema van de trackingtabel voor schemawijzigingen wilt wijzigen, schakelt u de DDL-trigger uit voordat u de wijziging aanbrengt en past u de wijziging handmatig toe op alle eindpunten. Het bijwerken van het schema in een TRIGGER NA INVOEGEN in dezelfde tabel werkt niet.

-   Zaai de identiteitskolom niet opnieuw met DBCC CHECKIDENT.

-   Gebruik TRUNCATE niet om gegevens op te schonen in de tabel schemawijziging.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende onderwerpen voor meer informatie over SQL Data Sync:

-   Overzicht: [Gegevens synchroniseren tussen meerdere cloud- en on-premises databases met SQL Data Sync](sql-database-sync-data.md)
-   Data Sync instellen
    - In de portal: [Zelfstudie: SQL Data Sync instellen om gegevens te synchroniseren tussen Azure SQL Database en SQL Server on-premises](sql-database-get-started-sql-data-sync.md)
    - Met PowerShell
        -  [PowerShell gebruiken om meerdere Azure SQL-databases te synchroniseren](scripts/sql-database-sync-data-between-sql-databases.md)
        -  [PowerShell gebruiken om te synchroniseren tussen een Azure SQL-database en een on-premises database](scripts/sql-database-sync-data-between-azure-onprem.md)
-   Data Sync-agent: [Data Sync-agent voor Azure SQL Data Sync](sql-database-data-sync-agent.md)
-   Best practices: [Best practices voor Azure SQL Data Sync](sql-database-best-practices-data-sync.md)
-   Monitor - [SQL-gegevenssynchronisatie met Azure Monitor-logboeken controleren](sql-database-sync-monitor-oms.md)
-   Problemen oplossen: [Problemen met Azure SQL Data Sync oplossen](sql-database-troubleshoot-data-sync.md)
-   Het synchronisatieschema bijwerken
    -   Met PowerShell: [PowerShell gebruiken voor het bijwerken van het synchronisatieschema in een bestaande synchronisatiegroep](scripts/sql-database-sync-update-schema.md)
