---
title: T-SQL-lussen gebruiken
description: Tips voor het gebruik van T-SQL-lussen en het vervangen van cursors in Azure SQL Data Warehouse voor het ontwikkelen van oplossingen.
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: b57358e32bda83ef51fe67aa1057411d51773fa6
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/06/2019
ms.locfileid: "73685823"
---
# <a name="using-t-sql-loops-in-sql-data-warehouse"></a>T-SQL-lussen gebruiken in SQL Data Warehouse
Tips voor het gebruik van T-SQL-lussen en het vervangen van cursors in Azure SQL Data Warehouse voor het ontwikkelen van oplossingen.

## <a name="purpose-of-while-loops"></a>Doel van WHILE-lussen

SQL Data Warehouse ondersteunt de [while](/sql/t-sql/language-elements/while-transact-sql) -lus voor het herhaaldelijk uitvoeren van instructie blokken. Deze lus wordt voortgezet zolang de opgegeven voor waarden waar zijn of totdat de code de lus met behulp van het sleutel woord desplit verbreekt. Lussen zijn handig voor het vervangen van cursors die zijn gedefinieerd in SQL-code. Gelukkig zijn bijna alle cursors die in SQL-code zijn geschreven van het besnelde, alleen-lezen RAS. Daarom zijn [WHILE] lussen een geweldig alternatief voor het vervangen van cursors.

## <a name="replacing-cursors-in-sql-data-warehouse"></a>Cursors in SQL Data Warehouse vervangen
Voordat u eerst aan de slag gaat, moet u echter de volgende vraag stellen: ' kan deze cursor herschrijven om op sets gebaseerde bewerkingen te gebruiken? ' In veel gevallen is het antwoord ja en is het vaak de beste aanpak. Een op sets gebaseerde bewerking voert vaak sneller uit dan een iteratieve, rij-by-rij-benadering.

Fast Forward-alleen-lezen cursors kunnen eenvoudig worden vervangen door een recursieve constructie. Hier volgt een eenvoudig voor beeld. In dit code voorbeeld worden de statistieken voor elke tabel in de data base bijgewerkt. Door de tabellen in de lus te herhalen, wordt elke opdracht in volg orde uitgevoerd.

Maak eerst een tijdelijke tabel die een uniek rijnummer bevat dat wordt gebruikt voor het identificeren van de afzonderlijke-instructies:

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

Initialiseer vervolgens de variabelen die vereist zijn voor het uitvoeren van de lus:

```
DECLARE @nbr_statements INT = (SELECT COUNT(*) FROM #tbl)
,       @i INT = 1
;
```

Instructies worden nu één voor een per keer uitgevoerd:

```
WHILE   @i <= @nbr_statements
BEGIN
    DECLARE @sql_code NVARCHAR(4000) = (SELECT sql_code FROM #tbl WHERE Sequence = @i);
    EXEC    sp_executesql @sql_code;
    SET     @i +=1;
END
```

De tijdelijke tabel die in de eerste stap is gemaakt, definitief verwijderen

```
DROP TABLE #tbl;
```

## <a name="next-steps"></a>Volgende stappen
Zie [ontwikkelings overzicht](sql-data-warehouse-overview-develop.md)voor meer tips voor ontwikkel aars.

