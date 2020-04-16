---
title: T-SQL-lussen gebruiken
description: Tips voor het gebruik van T-SQL-lussen, het vervangen van cursors en het ontwikkelen van gerelateerde oplossingen met SQL-pool in Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: baff2806b1a8c3c99546365c2496238c24b2b243
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81429952"
---
# <a name="using-t-sql-loops-in-synapse-sql"></a>T-SQL-lussen gebruiken in Synapse SQL
Dit artikel biedt u essentiële tips voor het gebruik van T-SQL-lussen, het vervangen van cursors en het ontwikkelen van gerelateerde oplossingen met SQL-pool in Synapse SQL.

## <a name="purpose-of-while-loops"></a>Doel van WHILE-lussen

Synapse SQL ondersteunt de [WHILE-lus](https://docs.microsoft.com/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver15) voor het herhaaldelijk uitvoeren van instructieblokken. Deze WHILE-lus gaat door zolang de opgegeven voorwaarden waar zijn of totdat de code de lus specifiek beëindigt met het trefwoord BREAK. 

Lussen in SQL-groep zijn handig voor het vervangen van cursors die zijn gedefinieerd in SQL-code. Gelukkig zijn bijna alle cursors die in SQL-code zijn geschreven van de fast forward, alleen-lezen variëteit. Dus, [WHILE] lussen zijn een geweldig alternatief voor het vervangen van cursors.

## <a name="replacing-cursors-in-sql-pool"></a>Cursors vervangen in SQL-groep

Voordat u induikt, moet de volgende vraag worden overwogen: "Kan deze cursor worden herschreven om ingerichte bewerkingen te gebruiken?" In veel gevallen is het antwoord ja en vaak de beste aanpak. Een set-based bewerking wordt vaak sneller uitgevoerd dan een iteratieve, rij voor rij benadering.

Fast forward read-only cursors zijn eenvoudig te vervangen door een lusconstructie. De volgende code is een eenvoudig voorbeeld. In dit codevoorbeeld worden de statistieken voor elke tabel in de database bijgewerkt. Door over de tabellen in de lus te herhalen, voert elke opdracht in opeenvolging uit.

Maak eerst een tijdelijke tabel met een uniek rijnummer dat wordt gebruikt om de afzonderlijke instructies te identificeren:

```sql
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

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Loop nu over verklaringen die ze één voor één uitvoeren:

```sql
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

Ten slotte laten vallen de tijdelijke tabel gemaakt in de eerste stap

```sql
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer ontwikkelingstips [het ontwikkelingsoverzicht.](develop-overview.md)
