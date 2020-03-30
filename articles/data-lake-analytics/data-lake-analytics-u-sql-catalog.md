---
title: De U-SQL-catalogus gebruiken in Azure Data Lake Analytics
description: Meer informatie over het gebruik van de U-SQL-catalogus om code en gegevens te delen. Maak functies met een tabelwaarde, maak weergaven, maak tabellen en queryze.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: 57143396-ab86-47dd-b6f8-613ba28c28d2
ms.topic: conceptual
ms.date: 05/09/2017
ms.openlocfilehash: f3b9f14be4422373fb30f8c3d4909fd9c9546fdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71672849"
---
# <a name="get-started-with-the-u-sql-catalog-in-azure-data-lake-analytics"></a>Aan de slag met de U-SQL-catalogus in Azure Data Lake Analytics

## <a name="create-a-tvf"></a>Een TVF maken

In het vorige U-SQL-script hebt u het gebruik van EXTRACT herhaald om uit hetzelfde bronbestand te lezen. Met de u-SQL-tabelwaardefunctie (TVF) u de gegevens inkapselen voor toekomstig hergebruik.  

Met het volgende script `Searchlog()` wordt een TVF gemaakt die in de standaarddatabase en het schema wordt aangeroepen:

```
DROP FUNCTION IF EXISTS Searchlog;

CREATE FUNCTION Searchlog()
RETURNS @searchlog TABLE
(
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
)
AS BEGIN
@searchlog =
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
USING Extractors.Tsv();
RETURN;
END;
```

In het volgende script ziet u hoe u de TVF gebruikt die in het vorige script is gedefinieerd:

```
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM Searchlog() AS S
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/SearchLog-use-tvf.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-views"></a>Weergaven maken

Als u één queryexpressie hebt, u in plaats van een TVF een U-SQL-weergave gebruiken om die expressie in te kapselen.

Met het volgende script `SearchlogView` wordt een weergave gemaakt die is aangeroepen in de standaarddatabase en het schema:

```
DROP VIEW IF EXISTS SearchlogView;

CREATE VIEW SearchlogView AS  
    EXTRACT UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string
    FROM "/Samples/Data/SearchLog.tsv"
USING Extractors.Tsv();
```

In het volgende script wordt het gebruik van de gedefinieerde weergave aangetoond:

```
@res =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchlogView
GROUP BY Region
HAVING SUM(Duration) > 200;

OUTPUT @res
    TO "/output/Searchlog-use-view.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

## <a name="create-tables"></a>Tabellen maken
Net als bij relationele databasetabellen u met U-SQL een tabel maken met een vooraf gedefinieerd schema of een tabel maken die het schema afleidt uit de query die de tabel vult (ook wel TABEL MAKEN ALS SELECT of CTAS genoemd).

Maak een database en twee tabellen met het volgende script:

```
DROP DATABASE IF EXISTS SearchLogDb;
CREATE DATABASE SearchLogDb;
USE DATABASE SearchLogDb;

DROP TABLE IF EXISTS SearchLog1;
DROP TABLE IF EXISTS SearchLog2;

CREATE TABLE SearchLog1 (
            UserId          int,
            Start           DateTime,
            Region          string,
            Query           string,
            Duration        int?,
            Urls            string,
            ClickedUrls     string,

            INDEX sl_idx CLUSTERED (UserId ASC)
                DISTRIBUTED BY HASH (UserId)
);

INSERT INTO SearchLog1 SELECT * FROM master.dbo.Searchlog() AS s;

CREATE TABLE SearchLog2(
    INDEX sl_idx CLUSTERED (UserId ASC)
            DISTRIBUTED BY HASH (UserId)
) AS SELECT * FROM master.dbo.Searchlog() AS S; // You can use EXTRACT or SELECT here
```

## <a name="query-tables"></a>Querytabellen
U tabellen, zoals die welke in het vorige script zijn gemaakt, op dezelfde manier opvragen als de gegevensbestanden. In plaats van een rijset te maken met BEHULP van EXTRACT, u nu verwijzen naar de tabelnaam.

Als u uit de tabellen wilt lezen, wijzigt u het transformatiescript dat u eerder hebt gebruikt:

```
@rs1 =
    SELECT
        Region,
        SUM(Duration) AS TotalDuration
    FROM SearchLogDb.dbo.SearchLog2
GROUP BY Region;

@res =
    SELECT *
    FROM @rs1
    ORDER BY TotalDuration DESC
    FETCH 5 ROWS;

OUTPUT @res
    TO "/output/Searchlog-query-table.csv"
    ORDER BY TotalDuration DESC
    USING Outputters.Csv();
```

 >[!NOTE]
 >Momenteel u geen SELECT uitvoeren op een tabel in hetzelfde script als die waarin u de tabel hebt gemaakt.

## <a name="next-steps"></a>Volgende stappen
* [Overzicht van Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [U-SQL-scripts ontwikkelen met Data Lake Tools voor Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Azure Data Lake Analytics-taken bewaken en problemen oplossen met Azure Portal](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
