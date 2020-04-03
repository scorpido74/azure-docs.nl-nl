---
title: T-SQL-lussen gebruiken
description: Tips voor het ontwikkelen van oplossingen met T-SQL-lussen en het vervangen van cursors in Synapse SQL-pool.
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
ms.openlocfilehash: 4cec4801f2a15ebf858f50377c9718fdacac4e29
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619004"
---
# <a name="using-t-sql-loops-in-synapse-sql-pool"></a>T-SQL-lussen gebruiken in synapssql-pool
In dit artikel zijn tips opgenomen voor sql-pooloplossingsontwikkeling met Behulp van T-SQL-lussen en het vervangen van cursors.

## <a name="purpose-of-while-loops"></a>Doel van WHILE-lussen

Synapse SQL-pool ondersteunt de [WHILE-lus](https://docs.microsoft.com/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver15) voor het herhaaldelijk uitvoeren van instructieblokken. Deze WHILE-lus gaat door zolang de opgegeven voorwaarden waar zijn of totdat de code de lus specifiek beëindigt met het trefwoord BREAK. 

Lussen zijn handig voor het vervangen van cursors die zijn gedefinieerd in SQL-code. Gelukkig zijn bijna alle cursors die in SQL-code zijn geschreven van de fast forward, alleen-lezen variëteit. Dus, terwijl lussen zijn een geweldig alternatief voor het vervangen van cursors.

## <a name="replacing-cursors-in-synapse-sql-pool"></a>Cursors vervangen in Synapse SQL-groep
Echter, voordat duiken in het hoofd eerst moet je jezelf de volgende vraag: "Kan deze cursor worden herschreven om set-based operaties te gebruiken?" 

In veel gevallen is het antwoord ja en vaak de beste aanpak. Een set-based bewerking presteert vaak sneller dan een iteratieve, rij voor rij aanpak.

Fast forward read-only cursors kunnen eenvoudig worden vervangen door een lusconstructie. Het volgende voorbeeld is een eenvoudig voorbeeld. In dit codevoorbeeld worden de statistieken voor elke tabel in de database bijgewerkt. Door over de tabellen in de lus te herhalen, voert elke opdracht in opeenvolging uit.

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

