---
title: Tijdelijke tabellen gebruiken met Synapse SQL
description: Essentiële richtlijnen voor het gebruik van tijdelijke tabellen in Synapse SQL.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: xiaoyul
ms.reviewer: igorstan
ms.openlocfilehash: 090f453771dba6f537ad60605c6e9b96f3ca9957
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428756"
---
# <a name="temporary-tables-in-synapse-sql"></a>Tijdelijke tabellen in Synapse SQL

Dit artikel bevat essentiële richtlijnen voor het gebruik van tijdelijke tabellen en belicht de principes van tijdelijke tabellen op sessieniveau binnen Synapse SQL. 

Zowel de SQL-pool als sql on-demand (preview)-bronnen kunnen tijdelijke tabellen gebruiken. SQL on-demand heeft beperkingen die aan het einde van dit artikel worden besproken. 

## <a name="what-are-temporary-tables"></a>Wat zijn tijdelijke tabellen?

Tijdelijke tabellen zijn handig bij het verwerken van gegevens, vooral tijdens transformatie waarbij de tussenliggende resultaten van voorbijgaande aard zijn. Met Synapse SQL bestaan er tijdelijke tabellen op sessieniveau.  Ze zijn alleen zichtbaar voor de sessie waarin ze zijn gemaakt. Als zodanig worden ze automatisch gedropt wanneer die sessie zich afmeldt. 

## <a name="temporary-tables-in-sql-pool"></a>Tijdelijke tabellen in SQL-groep

In de SQL-poolbron bieden tijdelijke tabellen een prestatievoordeel omdat de resultaten ervan worden geschreven naar lokale in plaats van externe opslag.

### <a name="create-a-temporary-table"></a>Een tijdelijke tabel maken

Tijdelijke tabellen worden gemaakt door uw `#`tabelnaam vooraf te bevestigen met een .  Bijvoorbeeld:

```sql
CREATE TABLE #stats_ddl
(
    [schema_name]        NVARCHAR(128) NOT NULL
,    [table_name]            NVARCHAR(128) NOT NULL
,    [stats_name]            NVARCHAR(128) NOT NULL
,    [stats_is_filtered]     BIT           NOT NULL
,    [seq_nmbr]              BIGINT        NOT NULL
,    [two_part_name]         NVARCHAR(260) NOT NULL
,    [three_part_name]       NVARCHAR(400) NOT NULL
)
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
```

Tijdelijke tabellen kunnen ook `CTAS` worden gemaakt met een met behulp van precies dezelfde aanpak:

```sql
CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
,    HEAP
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
;
```

> [!NOTE]
> `CTAS`is een krachtige opdracht en heeft het extra voordeel van efficiënt in het gebruik van transactielogboekruimte. 
> 
> 

### <a name="dropping-temporary-tables"></a>Tijdelijke tabellen laten vallen
Wanneer een nieuwe sessie wordt gemaakt, mogen er geen tijdelijke tabellen bestaan.  Als u echter dezelfde opgeslagen procedure aanroept die een tijdelijke met dezelfde `CREATE TABLE` naam maakt, om ervoor te `DROP`zorgen dat uw verklaringen succesvol zijn, gebruikt u een eenvoudige pre-existentiecontrole met: 

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Voor coderingsconsistentie is het een goede gewoonte om dit patroon te gebruiken voor zowel tabellen als tijdelijke tabellen.  Het is ook een goed `DROP TABLE` idee om tijdelijke tabellen te verwijderen wanneer u klaar bent met deze tabellen.  

Bij de ontwikkeling van opgeslagen procedures is het gebruikelijk om de dropopdrachten samen te zien aan het einde van een procedure om ervoor te zorgen dat deze objecten worden opgeschoond.

```sql
DROP TABLE #stats_ddl
```

### <a name="modularizing-code"></a>Modulariseringscode
Tijdelijke tabellen kunnen overal in een gebruikerssessie worden gebruikt. Deze mogelijkheid kan vervolgens worden benut om u te helpen uw toepassingscode te modulariseren.  Om aan te tonen genereert de volgende opgeslagen procedure DDL om alle statistieken in de database bij te werken op statistische naam:

```sql
CREATE PROCEDURE    [dbo].[prc_sqldw_update_stats]
(   @update_type    tinyint -- 1 default 2 fullscan 3 sample 4 resample
    ,@sample_pct     tinyint
)
AS

IF @update_type NOT IN (1,2,3,4)
BEGIN;
    THROW 151000,'Invalid value for @update_type parameter. Valid range 1 (default), 2 (fullscan), 3 (sample) or 4 (resample).',1;
END;

IF @sample_pct IS NULL
BEGIN;
    SET @sample_pct = 20;
END;

IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END

CREATE TABLE #stats_ddl
WITH
(
    DISTRIBUTION = HASH([seq_nmbr])
)
AS
(
SELECT
        sm.[name]                                                                AS [schema_name]
,        tb.[name]                                                                AS [table_name]
,        st.[name]                                                                AS [stats_name]
,        st.[has_filter]                                                            AS [stats_is_filtered]
,       ROW_NUMBER()
        OVER(ORDER BY (SELECT NULL))                                            AS [seq_nmbr]
,                                 QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [two_part_name]
,        QUOTENAME(DB_NAME())+'.'+QUOTENAME(sm.[name])+'.'+QUOTENAME(tb.[name])  AS [three_part_name]
FROM    sys.objects            AS ob
JOIN    sys.stats            AS st    ON    ob.[object_id]        = st.[object_id]
JOIN    sys.stats_columns    AS sc    ON    st.[stats_id]        = sc.[stats_id]
                                    AND st.[object_id]        = sc.[object_id]
JOIN    sys.columns            AS co    ON    sc.[column_id]        = co.[column_id]
                                    AND    sc.[object_id]        = co.[object_id]
JOIN    sys.tables            AS tb    ON    co.[object_id]        = tb.[object_id]
JOIN    sys.schemas            AS sm    ON    tb.[schema_id]        = sm.[schema_id]
WHERE    1=1
AND        st.[user_created]   = 1
GROUP BY
        sm.[name]
,        tb.[name]
,        st.[name]
,        st.[filter_definition]
,        st.[has_filter]
)
SELECT
    CASE @update_type
    WHEN 1
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+');'
    WHEN 2
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH FULLSCAN;'
    WHEN 3
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH SAMPLE '+CAST(@sample_pct AS VARCHAR(20))+' PERCENT;'
    WHEN 4
    THEN 'UPDATE STATISTICS '+[two_part_name]+'('+[stats_name]+') WITH RESAMPLE;'
    END AS [update_stats_ddl]
,   [seq_nmbr]
FROM    t1
;
GO
```

In dit stadium is de enige actie die heeft plaatsgevonden het maken van een opgeslagen procedure die de #stats_ddl tijdelijke tabel genereert.  De opgeslagen procedure daalt #stats_ddl als deze al bestaat. Deze daling zorgt ervoor dat het niet mislukt als het meer dan één keer wordt uitgevoerd binnen een sessie.  

Aangezien er geen `DROP TABLE` aan het einde van de opgeslagen procedure is, blijft de gemaakte tabel, wanneer de opgeslagen procedure is voltooid, en kan deze buiten de opgeslagen procedure worden gelezen.  

In tegenstelling tot andere SQL Server-databases u met Synapse SQL de tijdelijke tabel buiten de procedure gebruiken die deze heeft gemaakt.  De tijdelijke tabellen die via SQL-pool zijn gemaakt, kunnen **overal** in de sessie worden gebruikt. Als gevolg hiervan hebt u meer modulaire en beheersbare code, zoals aangetoond in het onderstaande voorbeeld:

```sql
EXEC [dbo].[prc_sqldw_update_stats] @update_type = 1, @sample_pct = NULL;

DECLARE @i INT              = 1
,       @t INT              = (SELECT COUNT(*) FROM #stats_ddl)
,       @s NVARCHAR(4000)   = N''

WHILE @i <= @t
BEGIN
    SET @s=(SELECT update_stats_ddl FROM #stats_ddl WHERE seq_nmbr = @i);

    PRINT @s
    EXEC sp_executesql @s
    SET @i+=1;
END

DROP TABLE #stats_ddl;
```

### <a name="temporary-table-limitations"></a>Tijdelijke tabelbeperkingen

SQL-groep heeft wel een paar implementatiebeperkingen voor tijdelijke tabellen:

- Alleen tijdelijke sessies met een bereik worden ondersteund.  Globale tijdelijke tabellen worden niet ondersteund.
- Weergaven kunnen niet worden gemaakt op tijdelijke tabellen.
- Tijdelijke tabellen kunnen alleen worden gemaakt met hash of round robin distributie.  Gerepliceerde tijdelijke tabeldistributie wordt niet ondersteund. 

## <a name="temporary-tables-in-sql-on-demand-preview"></a>Tijdelijke tabellen in SQL on-demand (voorbeeld)

Tijdelijke tabellen in SQL on-demand worden ondersteund, maar het gebruik ervan is beperkt. Ze kunnen niet worden gebruikt in query's die bestanden targeten. 

U bijvoorbeeld niet deelnemen aan een tijdelijke tabel met gegevens uit bestanden in opslag. Het aantal tijdelijke tabellen is beperkt tot 100 en de totale grootte ervan is beperkt tot 100 MB.

## <a name="next-steps"></a>Volgende stappen

Zie de tabellen Ontwerpen voor meer informatie over het ontwikkelen van tabellen in het artikel [Synapse SQL-bronnen.](develop-tables-overview.md)

