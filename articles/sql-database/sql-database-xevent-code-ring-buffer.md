---
title: XEvent RingBuffercode
description: Biedt een transact-SQL-codevoorbeeld dat eenvoudig en snel wordt gemaakt door gebruik te maken van het doel Ringbuffer in Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 12/19/2018
ms.openlocfilehash: ad98b61d6339388551af93671b3d4d892942f4e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213971"
---
# <a name="ring-buffer-target-code-for-extended-events-in-sql-database"></a>RingBuffer-doelcode voor uitgebreide gebeurtenissen in SQL-database

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

U wilt een volledig codevoorbeeld voor de eenvoudigste snelle manier om informatie vast te leggen en te rapporteren voor een uitgebreide gebeurtenis tijdens een test. Het eenvoudigste doel voor uitgebreide gebeurtenisgegevens is het [doel Ringbuffer](https://msdn.microsoft.com/library/ff878182.aspx).

In dit onderwerp wordt een transact-SQL-codevoorbeeld gepresenteerd dat:

1. Hiermee maakt u een tabel met gegevens waarmee u aantonen.
2. Hiermee maakt u een sessie voor een bestaande uitgebreide gebeurtenis, namelijk **sqlserver.sql_statement_starting**.
   
   * De gebeurtenis is beperkt tot SQL-instructies die een bepaalde updatetekenreeks bevatten: **instructie ALS '%UPDATE tabEmployee%'**.
   * Hiermee kiest u ervoor om de uitvoer van de gebeurtenis naar een doel van type Ring Buffer te verzenden, namelijk **package0.ring_buffer**.
3. Hiermee start u de gebeurtenissessie.
4. Geeft een paar eenvoudige SQL UPDATE-instructies uit.
5. Hiermee geeft u een SQL SELECT-instructie uit om gebeurtenisuitvoer op te halen uit de ringbuffer.
   
   * **sys.dm_xe_database_session_targets** en andere dynamische managementviews (DMVs) worden samengevoegd.
6. Hiermee stopt u de gebeurtenissessie.
7. Laat het Ring Buffer-doel vallen om de resources vrij te geven.
8. Laat de sessie van het evenement en de demotafel vallen.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account en -abonnement. U  kunt zich aanmelden voor een [gratis proefversie](https://azure.microsoft.com/pricing/free-trial/). 
* Elke database waarin u een tabel maken.
  
  * Optioneel u binnen enkele minuten [een **AdventureWorksLT-demonstratiedatabase** maken.](sql-database-get-started.md)
* SQL Server Management Studio (ssms.exe), idealiter de nieuwste maandelijkse updateversie. 
  U de nieuwste ssms.exe downloaden van:
  
  * Onderwerp met de titel [SQL Server Management Studio downloaden](https://msdn.microsoft.com/library/mt238290.aspx).
  * [Een directe link naar de download.](https://go.microsoft.com/fwlink/?linkid=616025)

## <a name="code-sample"></a>Codevoorbeeld

Met zeer kleine wijzigingen kan het volgende voorbeeld van ringbuffercode worden uitgevoerd op Azure SQL Database of Microsoft SQL Server. Het verschil is de aanwezigheid van het knooppunt '_database' in naam van een aantal dynamische managementviews (DMVs), gebruikt in de FROM-clausule in stap 5. Bijvoorbeeld:

* sys.dm_xe<strong>_database</strong>_session_targets
* sys.dm_xe_session_targets

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

## <a name="ring-buffer-contents"></a>Inhoud ringbuffer

We gebruikten ssms.exe om het codemonster uit te voeren.

Om de resultaten te bekijken, klikten we op de cel onder de kolomkop **target_data_XML**.

Vervolgens klikten we in het resultatenvenster op de cel onder de kolomkop **target_data_XML**. Met deze klik wordt een ander tabblad bestand in ssms.exe gemaakt waarin de inhoud van de resultaatcel als XML is weergegeven.

De uitvoer wordt weergegeven in het volgende blok. Het ziet er lang uit, maar het is slechts twee ** \<gebeurtenis>** elementen.

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


#### <a name="release-resources-held-by-your-ring-buffer"></a>Resources vrijgeven die in het bezit zijn van uw ringbuffer

Wanneer u klaar bent met uw ringbuffer, u deze verwijderen en de bronnen vrijgeven die een **ALTER** uitgeven, zoals het volgende:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```


De definitie van uw gebeurtenissessie wordt bijgewerkt, maar niet geschrapt. Later u een ander exemplaar van de ringbuffer toevoegen aan uw gebeurtenissessie:

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

Het primaire onderwerp voor uitgebreide gebeurtenissen in Azure SQL Database is:

* [Uitgebreide gebeurtenisoverwegingen in SQL Database](sql-database-xevent-db-diff-from-svr.md), die een aantal aspecten van uitgebreide gebeurtenissen contrasteert die verschillen tussen Azure SQL Database versus Microsoft SQL Server.

Andere code voorbeeldonderwerpen voor uitgebreide gebeurtenissen zijn beschikbaar op de volgende links. U moet echter routinematig elk voorbeeld controleren om te zien of het voorbeeld is gericht op Microsoft SQL Server versus Azure SQL Database. Vervolgens u bepalen of kleine wijzigingen nodig zijn om het voorbeeld uit te voeren.

* Codevoorbeeld voor Azure SQL Database: [Doelcode gebeurtenisbestand voor uitgebreide gebeurtenissen in SQL-database](sql-database-xevent-code-event-file.md)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](https://msdn.microsoft.com/library/bb677357.aspx)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](https://msdn.microsoft.com/library/bb630355.aspx)
-->
