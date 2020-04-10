---
title: Primaire, buitenlandse en unieke toetsen
description: Ondersteuning voor tabelbeperkingen in Synapse SQL-groep in Azure Synapse Analytics
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f97163d02836442430037e18439bcf0724046332
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80990766"
---
# <a name="primary-key-foreign-key-and-unique-key-in-synapse-sql-pool"></a>Primaire sleutel, externe sleutel en unieke sleutel in Synapse SQL-pool

Meer informatie over tabelbeperkingen in synapssql-pool, inclusief primaire sleutel, externe sleutel en unieke sleutel.

## <a name="table-constraints"></a>Tabelbeperkingen

Synapse SQL-pool ondersteunt deze tabelbeperkingen: 
- PRIMAIRE SLEUTEL wordt alleen ondersteund wanneer NIET-GECLUSTERD en NIET AFGEDWONGEN worden beide gebruikt.    
- Unieke beperking wordt alleen ondersteund met NIET AFGEDWONGEN wordt gebruikt.

Schakel VOOR syntaxis [TABEL WIJZIGEN](https://docs.microsoft.com/sql/t-sql/statements/alter-table-transact-sql) en TABEL [MAKEN in](https://docs.microsoft.com/sql/t-sql/statements/create-table-azure-sql-data-warehouse). 

EXTERNE KEY-beperking wordt niet ondersteund in de Synapse SQL-pool.  


## <a name="remarks"></a>Opmerkingen

Met primaire sleutel en/of unieke sleutel kan Synapse SQL-poolengine een optimaal uitvoeringsplan voor een query genereren.  Alle waarden in een primaire sleutelkolom of een unieke beperkingskolom moeten uniek zijn.

Nadat u een tabel hebt gemaakt met primaire sleutel of unieke beperking in de Synapse SQL-groep, moeten gebruikers ervoor zorgen dat alle waarden in die kolommen uniek zijn.  Een schending daarvan kan ertoe leiden dat de query een onjuist resultaat oplevert.  In dit voorbeeld ziet u hoe een query een onjuist resultaat kan retourneren als de primaire sleutel of unieke kolom met dubbele waarden.  

```sql
 -- Create table t1
CREATE TABLE t1 (a1 INT NOT NULL, b1 INT) WITH (DISTRIBUTION = ROUND_ROBIN)

-- Insert values to table t1 with duplicate values in column a1.
INSERT INTO t1 VALUES (1, 100)
INSERT INTO t1 VALUES (1, 1000)
INSERT INTO t1 VALUES (2, 200)
INSERT INTO t1 VALUES (3, 300)
INSERT INTO t1 VALUES (4, 400)

-- Run this query.  No primary key or unique constraint.  4 rows returned. Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
1           2
2           1
3           1
4           1

(4 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 ADD CONSTRAINT unique_t1_a1 unique (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, count(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key constraint
ALTER TABLE t1 add CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Incorrect result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
4           1
1           1
3           1
1           1

(5 rows affected)
*/

-- Manually fix the duplicate values in a1
UPDATE t1 SET a1 = 0 WHERE b1 = 1000

-- Verify no duplicate values in column a1 
SELECT * FROM t1

/*
a1          b1
----------- -----------
2           200
3           300
4           400
0           1000
1           100

(5 rows affected)
*/

-- Add unique constraint
ALTER TABLE t1 add CONSTRAINT unique_t1_a1 UNIQUE (a1) NOT ENFORCED  

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) as total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

-- Drop unique constraint.
ALTER TABLE t1 DROP CONSTRAINT unique_t1_a1

-- Add primary key contraint
ALTER TABLE t1 ADD CONSTRAINT PK_t1_a1 PRIMARY KEY NONCLUSTERED (a1) NOT ENFORCED

-- Re-run this query.  5 rows returned.  Correct result.
SELECT a1, COUNT(*) AS total FROM t1 GROUP BY a1

/*
a1          total
----------- -----------
2           1
3           1
4           1
0           1
1           1

(5 rows affected)
*/

```

## <a name="examples"></a>Voorbeelden

Maak een Synapsische SQL-pooltabel met een primaire sleutel: 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```
Maak een Synapsische SQL-pooltabel met een unieke beperking:

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>Volgende stappen

Nadat u de tabellen voor uw Synapse SQL-groep hebt gemaakt, is de volgende stap het laden van gegevens in de tabel. Zie Gegevens laden [in de SYNapse SQL-groep](load-data-wideworldimportersdw.md)voor een zelfstudie voor het laden .
