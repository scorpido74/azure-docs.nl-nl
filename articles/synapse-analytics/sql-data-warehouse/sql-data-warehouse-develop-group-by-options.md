---
title: Opties voor groep op
description: Tips voor het implementeren van groeps-op-opties in synapse SQL-pool.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 5d8d4c6d47e33ca365415542c2da9779b4d7d1dd
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416199"
---
# <a name="group-by-options-in-synapse-sql-pool"></a>Groeperen op opties in de Synapse SQL-groep

In dit artikel vindt u tips voor het implementeren van groeps-op-opties in SQL-pool.

## <a name="what-does-group-by-do"></a>Wat doet GROUP BY?

De [GROUP BY](/sql/t-sql/queries/select-group-by-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) T-SQL-component verzamelt gegevens tot een overzichtsset van rijen. GROUP BY heeft een aantal opties die SQL Pool niet ondersteunt. Deze opties hebben tijdelijke oplossingen, die als volgt zijn:

* GROEPEREN OP met ROLLUP
* GROEPERINGSSETS
* GROEP DOOR met KUBUS

## <a name="rollup-and-grouping-sets-options"></a>Opties voor rollup- en groeperingssets

De eenvoudigste optie hier is om UNION ALL te gebruiken om de rollup uit te voeren in plaats van te vertrouwen op de expliciete syntaxis. Het resultaat is precies hetzelfde.

In het volgende voorbeeld wordt de instructie GROEP PER met de optie ROLLUP gebruikt:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount)             AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t       ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY ROLLUP (
                        [SalesTerritoryCountry]
                ,       [SalesTerritoryRegion]
                )
;
```

Met behulp van ROLLUP worden in het voorgaande voorbeeld de volgende aggregaties gevraagd:

* Land en regio
* Land
* Eindtotaal

Als u ROLLUP wilt vervangen en dezelfde resultaten wilt retourneren, u UNION ALL gebruiken en de vereiste aggregaties expliciet opgeven:

```sql
SELECT [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
,      [SalesTerritoryRegion]
UNION ALL
SELECT [SalesTerritoryCountry]
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey
GROUP BY
       [SalesTerritoryCountry]
UNION ALL
SELECT NULL
,      NULL
,      SUM(SalesAmount) AS TotalSalesAmount
FROM  dbo.factInternetSales s
JOIN  dbo.DimSalesTerritory t     ON s.SalesTerritoryKey       = t.SalesTerritoryKey;
```

Om GROUPING SETS te vervangen, is het voorbeeldprincipe van toepassing. U hoeft alleen ALLE SECTIES UNION te maken voor de aggregatieniveaus die u wilt zien.

## <a name="cube-options"></a>Kubusopties

Het is mogelijk om een GROEP BY WITH CUBE te maken met behulp van de UNION ALL aanpak. Het probleem is dat de code snel omslachtig en onhandig kan worden. Om dit probleem te verhelpen, u deze meer geavanceerde aanpak gebruiken.

Met behulp van het vorige voorbeeld is de eerste stap het definiëren van de 'kubus' die alle aggregatieniveaus definieert die we willen maken.

Neem nota van de CROSS JOIN van de twee afgeleide tabellen, omdat dit genereert alle niveaus voor ons. De rest van de code is er voor opmaak:

```sql
CREATE TABLE #Cube
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
AS
WITH GrpCube AS
(SELECT    CAST(ISNULL(Country,'NULL')+','+ISNULL(Region,'NULL') AS NVARCHAR(50)) as 'Cols'
,          CAST(ISNULL(Country+',','')+ISNULL(Region,'') AS NVARCHAR(50))  as 'GroupBy'
,          ROW_NUMBER() OVER (ORDER BY Country) as 'Seq'
FROM       ( SELECT 'SalesTerritoryCountry' as Country
             UNION ALL
             SELECT NULL
           ) c
CROSS JOIN ( SELECT 'SalesTerritoryRegion' as Region
             UNION ALL
             SELECT NULL
           ) r
)
SELECT Cols
,      CASE WHEN SUBSTRING(GroupBy,LEN(GroupBy),1) = ','
            THEN SUBSTRING(GroupBy,1,LEN(GroupBy)-1)
            ELSE GroupBy
       END AS GroupBy  --Remove Trailing Comma
,Seq
FROM GrpCube;
```

De volgende afbeelding toont de resultaten van de CTAS:

![Groeperen op kubus](./media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

De tweede stap is het opgeven van een doeltabel voor het opslaan van tussentijdse resultaten:

```sql
DECLARE
 @SQL NVARCHAR(4000)
,@Columns NVARCHAR(4000)
,@GroupBy NVARCHAR(4000)
,@i INT = 1
,@nbr INT = 0
;
CREATE TABLE #Results
(
 [SalesTerritoryCountry] NVARCHAR(50)
,[SalesTerritoryRegion]  NVARCHAR(50)
,[TotalSalesAmount]      MONEY
)
WITH
(   DISTRIBUTION = ROUND_ROBIN
,   LOCATION = USER_DB
)
;
```

De derde stap is om lus over onze kubus van kolommen het uitvoeren van de aggregatie. De query wordt één keer uitgevoerd voor elke rij in de #Cube tijdelijke tabel. De resultaten worden opgeslagen in de #Results temp tabel:

```sql
SET @nbr =(SELECT MAX(Seq) FROM #Cube);

WHILE @i<=@nbr
BEGIN
    SET @Columns = (SELECT Cols    FROM #Cube where seq = @i);
    SET @GroupBy = (SELECT GroupBy FROM #Cube where seq = @i);

    SET @SQL ='INSERT INTO #Results
              SELECT '+@Columns+'
              ,      SUM(SalesAmount) AS TotalSalesAmount
              FROM  dbo.factInternetSales s
              JOIN  dbo.DimSalesTerritory t  
              ON s.SalesTerritoryKey = t.SalesTerritoryKey
              '+CASE WHEN @GroupBy <>''
                     THEN 'GROUP BY '+@GroupBy ELSE '' END

    EXEC sp_executesql @SQL;
    SET @i +=1;
END
```

Ten slotte u de resultaten retourneren door uit de #Results tijdelijke tabel te lezen:

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Door de code op te splitsen in secties en een lusconstructie te genereren, wordt de code beter beheersbaar en onderhoudbaar.

## <a name="next-steps"></a>Volgende stappen

Zie voor meer ontwikkelingstips [het ontwikkelingsoverzicht.](sql-data-warehouse-overview-develop.md)
