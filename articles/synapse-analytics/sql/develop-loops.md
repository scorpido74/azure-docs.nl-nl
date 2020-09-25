---
title: T-SQL-lussen gebruiken
description: Tips voor het gebruik van T-SQL-lussen, het vervangen van cursors en het ontwikkelen van gerelateerde oplossingen met SQL-pool in Synapse SQL.
services: synapse-analytics
author: filippopovic
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 04/15/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 33e1ebc2269ef1db6bb0646f845b09be1a01c724
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91289052"
---
# <a name="use-t-sql-loops-in-synapse-sql"></a>T-SQL-lussen gebruiken in Synapse SQL
In dit artikel vindt u belang rijke tips voor het gebruik van T-SQL-lussen, het vervangen van cursors en het ontwikkelen van gerelateerde oplossingen met SQL-pool in Synapse SQL.

## <a name="purpose-of-while-loops"></a>Doel van WHILE-lussen

Synapse SQL ondersteunt de [while](https://docs.microsoft.com/sql/t-sql/language-elements/while-transact-sql?view=sql-server-ver15&preserve-view=true) -lus voor het herhaaldelijk uitvoeren van instructie blokken. Deze lus wordt voortgezet zolang de opgegeven voor waarden waar zijn of totdat de code de lus met behulp van het sleutel woord desplit verbreekt. 

Lussen in SQL-groep zijn handig voor het vervangen van cursors die zijn gedefinieerd in SQL-code. Gelukkig zijn bijna alle cursors die in SQL-code zijn geschreven van het besnelde, alleen-lezen RAS. En lussen zijn dus een geweldig alternatief voor het vervangen van cursors.

## <a name="replace-cursors-in-sql-pool"></a>Cursors in SQL-groep vervangen

Voordat u aan de slag gaat, moet u rekening houden met de volgende vraag: ' kan deze cursor opnieuw schrijven voor het gebruik van op sets gebaseerde bewerkingen? ' In veel gevallen is het antwoord ja en is het vaak de beste aanpak. Een op sets gebaseerde bewerking voert vaak sneller uit dan een iteratieve, rij-by-rij-benadering.

Fast Forward-alleen-lezen cursors kunnen eenvoudig worden vervangen door een recursieve constructie. De volgende code is een eenvoudig voor beeld. In dit code voorbeeld worden de statistieken voor elke tabel in de data base bijgewerkt. Door de tabellen in de lus te herhalen, wordt elke opdracht in volg orde uitgevoerd.

Maak eerst een tijdelijke tabel die een uniek rijnummer bevat dat wordt gebruikt voor het identificeren van de afzonderlijke-instructies:

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

Initialiseer vervolgens de variabelen die vereist zijn voor het uitvoeren van de lus:

```sql
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Instructies worden nu één voor een per keer uitgevoerd:

```sql
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

De tijdelijke tabel die in de eerste stap is gemaakt, definitief verwijderen

```sql
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Volgende stappen

Zie [ontwikkelings overzicht](develop-overview.md)voor meer tips voor ontwikkel aars.
