---
title: T-SQL-lussen gebruiken
description: Tips voor het gebruik van T-SQL-lussen en het vervangen van cursors in Azure SQL Data Warehouse voor het ontwikkelen van oplossingen.
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
ms.openlocfilehash: afb2160cb9b4e34d3d17db86bac9cd3be79886d0
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80351595"
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>T-SQL-lussen gebruiken in SQL Data Warehouse
Tips voor het gebruik van T-SQL-lussen en het vervangen van cursors in Azure SQL Data Warehouse voor het ontwikkelen van oplossingen.

## <a name="purpose-of-while-loops"></a>Doel van WHILE-lussen

SQL Data Warehouse ondersteunt de [WHILE-lus](/sql/t-sql/language-elements/while-transact-sql) voor het herhaaldelijk uitvoeren van instructieblokken. Deze WHILE-lus gaat door zolang de opgegeven voorwaarden waar zijn of totdat de code de lus specifiek beëindigt met het trefwoord BREAK. Lussen zijn handig voor het vervangen van cursors die zijn gedefinieerd in SQL-code. Gelukkig zijn bijna alle cursors die in SQL-code zijn geschreven van de fast forward, alleen-lezen variëteit. Daarom zijn [WHILE] lussen een geweldig alternatief voor het vervangen van cursors.

## <a name="replacing-cursors-in-sql-data-warehouse"></a>Cursors vervangen in SQL Data Warehouse
Echter, voordat duiken in het hoofd eerst moet je jezelf de volgende vraag stellen: "Kan deze cursor worden herschreven om set-based operaties te gebruiken?." In veel gevallen is het antwoord ja en is het vaak de beste aanpak. Een set-based bewerking presteert vaak sneller dan een iteratieve, rij voor rij aanpak.

Fast forward read-only cursors kunnen eenvoudig worden vervangen door een lusconstructie. Het volgende is een eenvoudig voorbeeld. In dit codevoorbeeld worden de statistieken voor elke tabel in de database bijgewerkt. Door over de tabellen in de lus te herhalen, voert elke opdracht in opeenvolging uit.

Maak eerst een tijdelijke tabel met een uniek rijnummer dat wordt gebruikt om de afzonderlijke instructies te identificeren:

```
CREATE TABLE #tbl
WITH
( DISTRIBUTION = ROUND_ROBIN
)
AS
SELECT  ROW_NUMBER() OVER(ORDER BY (SELECT NULL)) AS Sequence
,       [name]
,       'UPDATE STATISTICS '+QUOTENAME([name]) AS sql_code
FROM    sys.tables
;
```

Ten tweede de variabelen initialiseren die nodig zijn om de lus uit te voeren:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Loop nu over verklaringen die ze één voor één uitvoeren:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Ten slotte laten vallen de tijdelijke tabel gemaakt in de eerste stap

```
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Volgende stappen
Zie voor meer ontwikkelingstips [het ontwikkelingsoverzicht.](sql-data-warehouse-overview-develop.md)

