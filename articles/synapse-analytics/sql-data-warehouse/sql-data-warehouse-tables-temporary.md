---
title: Tijdelijke tabellen
description: Essentiële richtlijnen voor het gebruik van tijdelijke tabellen in synapse SQL-pool, waarbij de beginselen van tijdelijke tabellen op sessieniveau worden benadrukt.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 64490bbd44066389186a59e851045b6becbe7acc
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80632471"
---
# <a name="temporary-tables-in-synapse-sql-pool"></a>Tijdelijke tabellen in de Synapsische SQL-groep
Dit artikel bevat essentiële richtlijnen voor het gebruik van tijdelijke tabellen en belicht de principes van tijdelijke tabellen op sessieniveau. 

Het gebruik van de informatie in dit artikel kan u helpen uw code te modulariseren, waardoor zowel de herbruikbaarheid als het onderhoudsgemak wordt verbeterd.

## <a name="what-are-temporary-tables"></a>Wat zijn tijdelijke tabellen?
Tijdelijke tabellen zijn handig bij het verwerken van gegevens, vooral tijdens transformatie waarbij de tussenliggende resultaten van voorbijgaande aard zijn. In SQL-pool bestaan tijdelijke tabellen op sessieniveau.  

Tijdelijke tabellen zijn alleen zichtbaar voor de sessie waarin ze zijn gemaakt en worden automatisch verwijderd wanneer die sessie zich afmeldt.  

Tijdelijke tabellen bieden een prestatievoordeel omdat hun resultaten worden geschreven naar lokale in plaats van externe opslag.

## <a name="create-a-temporary-table"></a>Een tijdelijke tabel maken
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

## <a name="dropping-temporary-tables"></a>Tijdelijke tabellen laten vallen
Wanneer een nieuwe sessie wordt gemaakt, mogen er geen tijdelijke tabellen bestaan.  

Als u dezelfde opgeslagen procedure aanroept, die een tijdelijke met dezelfde `CREATE TABLE` naam maakt, om ervoor te `DROP` zorgen dat uw verklaringen succesvol zijn, kan een eenvoudige pre-existente-controle met een worden gebruikt zoals in het volgende voorbeeld:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Voor coderingsconsistentie is het een goede gewoonte om dit patroon te gebruiken voor zowel tabellen als tijdelijke tabellen.  Het is ook een goed `DROP TABLE` idee om tijdelijke tabellen te verwijderen wanneer u klaar bent met deze tabellen in uw code.  

Bij de ontwikkeling van opgeslagen procedures is het gebruikelijk om de dropopdrachten samen te zien aan het einde van een procedure om ervoor te zorgen dat deze objecten worden opgeschoond.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Modulariseringscode
Aangezien tijdelijke tabellen overal in een gebruikerssessie kunnen worden gezien, kan deze mogelijkheid worden benut om u te helpen uw toepassingscode te modulariseren.  

De volgende opgeslagen procedure genereert bijvoorbeeld DDL om alle statistieken in de database bij te werken op statistische naam:

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

In dit stadium is de enige actie die heeft plaatsgevonden het maken van een opgeslagen procedure die een tijdelijke tabel genereert, #stats_ddl, met DDL-instructies.  

Met deze opgeslagen procedure wordt een bestaand #stats_ddl om ervoor te zorgen dat deze niet mislukken als deze meer dan één keer binnen een sessie wordt uitgevoerd.  

Aangezien er echter `DROP TABLE` geen aan het einde van de opgeslagen procedure is, verlaat de opgeslagen procedure, wanneer de opgeslagen procedure is voltooid, zodat deze buiten de opgeslagen procedure kan worden gelezen.  

In SQL-groep is het, in tegenstelling tot andere SQL Server-databases, mogelijk om de tijdelijke tabel te gebruiken buiten de procedure die deze heeft gemaakt.  Sql pool tijdelijke tabellen kunnen **overal** in de sessie worden gebruikt. Deze functie kan leiden tot meer modulaire en beheersbare code zoals in het volgende voorbeeld:

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

## <a name="temporary-table-limitations"></a>Tijdelijke tabelbeperkingen
SQL-pool legt wel een paar beperkingen op bij het implementeren van tijdelijke tabellen.  Momenteel worden alleen tijdelijke sessies met sessiescoped ondersteund.  Globale tijdelijke tabellen worden niet ondersteund.  

Ook kunnen weergaven niet worden gemaakt op tijdelijke tabellen.  Tijdelijke tabellen kunnen alleen worden gemaakt met hash of round robin distributie.  Gerepliceerde tijdelijke tabeldistributie wordt niet ondersteund. 

## <a name="next-steps"></a>Volgende stappen
Zie het [tabeloverzicht](sql-data-warehouse-tables-overview.md)voor meer informatie over het ontwikkelen van tabellen.

