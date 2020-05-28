---
title: XEvent ring-buffer code
description: Biedt een voor beeld van de Transact-SQL-code die eenvoudig en snel kan worden gemaakt door gebruik te maken van het ring buffer doel, in Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: faba9eaf59f5d1c941bacb58ba1faf9f817d39cf
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84046983"
---
# <a name="ring-buffer-target-code-for-extended-events-in-azure-sql-database"></a>Doel code ring buffer voor uitgebreide gebeurtenissen in Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

U wilt een volledig code voorbeeld voor de eenvoudigste snelle manier om informatie vast te leggen en te rapporteren voor een uitgebreide gebeurtenis tijdens een test. Het eenvoudigste doel voor uitgebreide gebeurtenis gegevens is het [doel van de ring buffer](https://msdn.microsoft.com/library/ff878182.aspx).

Dit onderwerp bevat een voor beeld van een Transact-SQL-code die:

1. Hiermee maakt u een tabel met gegevens om te demonstreren.
2. Hiermee maakt u een sessie voor een bestaande uitgebreide gebeurtenis, namelijk **sqlserver. sql_statement_starting**.

   * De gebeurtenis is beperkt tot SQL-instructies die een bepaalde update teken reeks bevatten: **instructie like% update tabEmployee%**.
   * Hiermee wordt de uitvoer van de gebeurtenis verzonden naar een doel van het type ring buffer, te weten **package0. ring_buffer**.
3. Hiermee wordt de gebeurtenis sessie gestart.
4. Hiermee wordt een aantal eenvoudige SQL UPDATE-instructies beschreven.
5. Hiermee wordt een SQL SELECT-instructie uitgegeven om gebeurtenis uitvoer van de ring buffer op te halen.

   * **sys. dm_xe_database_session_targets** en andere dynamische beheer weergaven (dmv's) zijn gekoppeld.
6. Hiermee stopt u de gebeurtenis sessie.
7. De ring buffer doel neerzetten om de resources vrij te geven.
8. De gebeurtenis sessie en de demo tabel weglaten.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account en -abonnement. U  kunt zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/). 
* Elke Data Base waarin u een tabel kunt maken.
  
  * U kunt desgewenst binnen enkele minuten [een **AdventureWorksLT** -demonstratie database maken](single-database-create-quickstart.md) .
* SQL Server Management Studio (SSMS. exe), de meest recente maandelijkse update versie.
  U kunt de nieuwste versie van SSMS. exe downloaden van:
  
  * Onderwerp met de titel [down load SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
  * [Een directe koppeling naar de down load.](https://go.microsoft.com/fwlink/?linkid=616025)

## <a name="code-sample"></a>Codevoorbeeld

Met een zeer kleine wijziging kan het volgende voor beeld van een ring buffer worden uitgevoerd op Azure SQL Database of Microsoft SQL Server. Het verschil is de aanwezigheid van het knoop punt ' _database ' in de naam van sommige dynamische beheer weergaven (Dmv's), die worden gebruikt in de component FROM in stap 5. Bijvoorbeeld:

* sys. dm_xe<strong>_database</strong>_session_targets
* sys. dm_xe_session_targets

&nbsp;

```sql
GO
----  Transact-SQL.
---- Step set 1.

SET NOCOUNT ON;
GO


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'tabEmployee')
BEGIN
    DROP TABLE tabEmployee;
END
GO


CREATE TABLE tabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO tabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

---- Step set 2.


IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'eventsession_gm_azuresqldb51')
BEGIN
    DROP EVENT SESSION eventsession_gm_azuresqldb51
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE '%UPDATE tabEmployee%'
            )
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
GO

---- Step set 3.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = START;
GO

---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
GO

---- Step set 5.


SELECT
    se.name                      AS [session-name],
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source,
    st.target_data,
    CAST(st.target_data AS XML)  AS [target_data_XML]
FROM
               sys.dm_xe_database_session_event_actions  AS ac

    INNER JOIN sys.dm_xe_database_session_events         AS ev  ON ev.event_name = ac.event_name
        AND CAST(ev.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_object_columns AS oc
         ON CAST(oc.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_targets        AS st
         ON CAST(st.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_sessions               AS se
         ON CAST(ac.event_session_address AS BINARY(8)) = CAST(se.address AS BINARY(8))
WHERE
        oc.column_name = 'occurrence_number'
    AND
        se.name        = 'eventsession_gm_azuresqldb51'
    AND
        ac.action_name = 'sql_text'
ORDER BY
    se.name,
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source
;
GO

---- Step set 6.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = STOP;
GO

---- Step set 7.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO

---- Step set 8.


DROP EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE;
GO

DROP TABLE tabEmployee;
GO
```

&nbsp;

## <a name="ring-buffer-contents"></a>Inhoud van ring buffer

We hebben gebruikt `ssms.exe` om het code voorbeeld uit te voeren.

Als u de resultaten wilt weer geven, klikt u op de cel onder de kolomkop **target_data_XML**.

Vervolgens klikt u in het resultaten venster op de cel onder de kolomkop **target_data_XML**. Klik op een ander bestand tabblad gemaakt in SSMS. exe waarin de inhoud van de resultaat cel is weer gegeven als XML.

De uitvoer wordt weer gegeven in het volgende blok. Het lijkt lang, maar het is slechts twee **\<event>** elementen.

&nbsp;

```
<RingBufferTarget truncated="0" processingTime="0" totalEventsProcessed="2" eventCount="2" droppedCount="0" memoryUsed="1728">
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.317Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>7</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>184</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>328</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.327Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>10</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>340</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>486</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
</RingBufferTarget>
```

### <a name="release-resources-held-by-your-ring-buffer"></a>Release-resources die worden beheerd door de ring buffer

Wanneer u klaar bent met de ring buffer, kunt u deze verwijderen en de resources vrijgeven die een **wijzigen** , zoals de volgende:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```

De definitie van uw gebeurtenis sessie wordt bijgewerkt, maar niet verwijderd. U kunt later nog een exemplaar van de ring buffer toevoegen aan uw gebeurtenis sessie:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
```

## <a name="more-information"></a>Meer informatie

Het primaire onderwerp voor uitgebreide gebeurtenissen op Azure SQL Database is:

* [Uitgebreide gebeurtenis overwegingen in Azure SQL database](xevent-db-diff-from-svr.md), die een aantal aspecten van uitgebreide gebeurtenissen tegen elkaar verschillen tussen Azure SQL Database versus Microsoft SQL Server.

Andere onderwerpen over voorbeeld code voor uitgebreide gebeurtenissen vindt u op de volgende koppelingen. U moet echter regel matig een voor beeld controleren om te zien of de voor beeld-doelen Microsoft SQL Server versus Azure SQL Database. Vervolgens kunt u bepalen of er kleine wijzigingen nodig zijn om het voor beeld uit te voeren.

* Code voorbeeld voor Azure SQL Database: [doel code van gebeurtenis bestand voor uitgebreide gebeurtenissen in Azure SQL database](xevent-code-event-file.md)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
