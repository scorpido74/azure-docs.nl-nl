---
title: Tijdelijke tabellen
description: Essentiële richt lijnen voor het gebruik van tijdelijke tabellen in Azure SQL Data Warehouse, waarbij de principes van tijdelijke tabellen op sessie niveau worden gemarkeerd.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/01/2019
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 23a5825a32c602f70aff1d9f577ce13d3e9f2260
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685431"
---
# <a name="temporary-tables-in-sql-data-warehouse"></a>Tijdelijke tabellen in SQL Data Warehouse
Dit artikel bevat essentiële richt lijnen voor het gebruik van tijdelijke tabellen en markeert de principes van tijdelijke tabellen op sessie niveau. Met behulp van de informatie in dit artikel kunt u uw code modularize, waardoor zowel het hergebruik als het gemak van uw code kan worden verbeterd.

## <a name="what-are-temporary-tables"></a>Wat zijn tijdelijke tabellen?
Tijdelijke tabellen zijn handig bij het verwerken van gegevens, met name tijdens trans formatie waarbij de tussenliggende resultaten tijdelijk zijn. In SQL Data Warehouse bestaan er tijdelijke tabellen op sessie niveau.  Ze zijn alleen zichtbaar voor de sessie waarin ze zijn gemaakt en die automatisch worden verwijderd wanneer deze sessie wordt afgemeld.  Tijdelijke tabellen bieden een prestatie voordelen omdat hun resultaten naar een lokale locatie worden geschreven in plaats van externe opslag.

## <a name="create-a-temporary-table"></a>Een tijdelijke tabel maken
Tijdelijke tabellen worden gemaakt door de naam van de tabel met een `#`voor voegsel te maken.  Bijvoorbeeld:

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

Tijdelijke tabellen kunnen ook worden gemaakt met een `CTAS` precies dezelfde benadering gebruiken:

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
> `CTAS` is een krachtige opdracht en het toegevoegde voor deel van het gebruik van de transactie logboek ruimte is efficiënt. 
> 
> 

## <a name="dropping-temporary-tables"></a>Tijdelijke tabellen verwijderen
Wanneer er een nieuwe sessie wordt gemaakt, moeten er geen tijdelijke tabellen aanwezig zijn.  Als u echter dezelfde opgeslagen procedure aanroept, waardoor er een tijdelijke met dezelfde naam ontstaat, om ervoor te zorgen dat uw `CREATE TABLE`-instructies een eenvoudige controle van de voor bereiding met een `DROP` kan worden uitgevoerd, zoals in het volgende voor beeld:

```sql
IF OBJECT_ID('tempdb..#stats_ddl') IS NOT NULL
BEGIN
    DROP TABLE #stats_ddl
END
```

Voor het coderen van consistentie is het een goed idee om dit patroon te gebruiken voor zowel tabellen als tijdelijke tabellen.  Het is ook een goed idee om met `DROP TABLE` tijdelijke tabellen te verwijderen wanneer u deze in uw code hebt voltooid.  Bij het ontwikkelen van opgeslagen procedures is het gebruikelijk om de door u gebundelde opdrachten aan het einde van een procedure te bekijken om ervoor te zorgen dat deze objecten worden opgeruimd.

```sql
DROP TABLE #stats_ddl
```

## <a name="modularizing-code"></a>Modularizing-code
Omdat tijdelijke tabellen overal in een gebruikers sessie kunnen worden weer gegeven, kan dit worden misbruikt om u te helpen bij het modularize van uw toepassings code.  Met de volgende opgeslagen procedure wordt bijvoorbeeld DDL gegenereerd om alle statistieken in de-data base bij te werken met een statistische naam.

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

In deze fase is de enige actie die is opgetreden, het maken van een opgeslagen procedure waarmee een tijdelijke tabel, #stats_ddl, met DDL-instructies wordt gegenereerd.  Deze opgeslagen procedure gaat #stats_ddl als deze al bestaat, om ervoor te zorgen dat deze niet meer dan één keer in een sessie wordt uitgevoerd.  Omdat er echter geen `DROP TABLE` aan het einde van de opgeslagen procedure, wanneer de opgeslagen procedure is voltooid, blijft de tabel gemaakt, zodat deze kan worden gelezen buiten de opgeslagen procedure.  In SQL Data Warehouse, in tegens telling tot andere SQL Server-data bases, is het mogelijk om de tijdelijke tabel te gebruiken buiten de procedure waarmee deze is gemaakt.  SQL Data Warehouse tijdelijke tabellen kunnen **overal** in de sessie worden gebruikt. Dit kan leiden tot meer modulaire en beheersbaarere code, zoals in het volgende voor beeld:

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

## <a name="temporary-table-limitations"></a>Tijdelijke tabel beperkingen
SQL Data Warehouse kent een aantal beperkingen bij het implementeren van tijdelijke tabellen.  Momenteel worden alleen tijdelijke tabellen met sessie bereik ondersteund.  Globale tijdelijke tabellen worden niet ondersteund.  Daarnaast kunnen geen weer gaven worden gemaakt voor tijdelijke tabellen.  Tijdelijke tabellen kunnen alleen worden gemaakt met hash-of round robin distributie.  De tijdelijke tabel distributie met een replicatie wordt niet ondersteund. 

## <a name="next-steps"></a>Volgende stappen
Zie het [tabel Overzicht](sql-data-warehouse-tables-overview.md)voor meer informatie over het ontwikkelen van tabellen.

