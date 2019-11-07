---
title: Groeperen op opties gebruiken
description: Tips voor het implementeren van groeps opties in Azure SQL Data Warehouse voor het ontwikkelen van oplossingen.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: query
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: 8d5d0f28fa210a56460ced25323b96a68f245895
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685848"
---
# <a name="group-by-options-in-sql-data-warehouse"></a>Groeperen op Opties in SQL Data Warehouse
Tips voor het implementeren van groeps opties in Azure SQL Data Warehouse voor het ontwikkelen van oplossingen.

## <a name="what-does-group-by-do"></a>Wat doet GROUP BY?

Met de component [Group by](/sql/t-sql/queries/select-group-by-transact-sql) T-SQL worden gegevens geaggregeerd naar een samen vatting van rijen. GROUP BY bevat enkele opties die SQL Data Warehouse niet ondersteunt. Deze opties hebben tijdelijke oplossingen.

Deze opties zijn

* GROEPEREN op met ROLLUP
* GROEPEER SETS
* GROEPEREN op met kubus

## <a name="rollup-and-grouping-sets-options"></a>Opties voor samen vouwen en groeperen van sets
De eenvoudigste optie is om UNION ALL te gebruiken om de rollup uit te voeren in plaats van te vertrouwen op de expliciete syntaxis. Het resultaat is precies hetzelfde

In het volgende voor beeld met de instructie GROUP BY met de optie samen vouwen:
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

Als u ROLLUP gebruikt, vraagt het vorige voor beeld de volgende aggregaties aan:

* Land en regio
* Land
* Eindtotaal

Als u het pakket wilt vervangen en dezelfde resultaten wilt retour neren, kunt u alle gebruiken en expliciet de vereiste aggregaties opgeven:

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

Als u GROEPERINGs SETS wilt vervangen, is het voorbeeld principe van toepassing. U hoeft alleen UNION alle secties te maken voor de aggregatie niveaus die u wilt zien.

## <a name="cube-options"></a>Kubus opties
Het is mogelijk om een GROUP BY WITH CUBE te maken met behulp van de methode UNION ALL. Het probleem is dat de code snel en lastig kan worden. Als u dit wilt beperken, kunt u deze geavanceerde benadering gebruiken.

We gaan het bovenstaande voor beeld gebruiken.

De eerste stap is het definiëren van de ' kubus ' waarmee alle aggregatie niveaus worden gedefinieerd die u wilt maken. Het is belang rijk dat u rekening moet houden met de CROSS-koppeling van de twee afgeleide tabellen. Hiermee worden alle niveaus voor ons gegenereerd. De rest van de code is echt voor de opmaak.

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

Hieronder ziet u de resultaten van de CTAS:

![Groeperen op kubus](media/sql-data-warehouse-develop-group-by-options/sql-data-warehouse-develop-group-by-cube.png)

De tweede stap is het opgeven van een doel tabel voor het opslaan van interim-resultaten:

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

De derde stap bestaat uit het herhalen van de kubus van kolommen die de aggregatie uitvoeren. De query wordt één keer uitgevoerd voor elke rij in de tijdelijke tabel van #Cube en de resultaten worden opgeslagen in de tijdelijke tabel #Results

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

Ten slotte kunt u de resultaten retour neren door eenvoudigweg te lezen uit de tijdelijke tabel #Results

```sql
SELECT *
FROM #Results
ORDER BY 1,2,3
;
```

Door de code in secties te splitsen en een lus te maken, wordt de code beheersbaarder en onderhouden.

## <a name="next-steps"></a>Volgende stappen
Zie [ontwikkelings overzicht](sql-data-warehouse-overview-develop.md)voor meer tips voor ontwikkel aars.

