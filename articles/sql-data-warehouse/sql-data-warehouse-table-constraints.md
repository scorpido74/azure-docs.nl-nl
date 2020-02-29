---
title: Primaire, refererende en unieke sleutels
description: Ondersteuning voor tabel beperkingen in SQL Analytics in azure Synapse Analytics
services: sql-data-warehouse
author: XiaoyuMSFT
manager: craigg
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: development
ms.date: 09/05/2019
ms.author: xiaoyul
ms.reviewer: nibruno; jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 0379bed08c3ee6931e931a78a2d2c91664535250
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/29/2020
ms.locfileid: "78198130"
---
# <a name="primary-key-foreign-key-and-unique-key-in-sql-analytics"></a>Primaire sleutel, refererende sleutel en unieke sleutel in SQL Analytics

Meer informatie over tabel beperkingen in SQL Analytics, waaronder primaire sleutel, refererende sleutel en unieke sleutel.

## <a name="table-constraints"></a>Tabelbeperkingen 
SQL Analytics ondersteunt deze tabel beperkingen: 
- De primaire sleutel wordt alleen ondersteund als niet-geclusterd en niet afgedwongen worden gebruikt.    
- EEN unieke beperking wordt alleen ondersteund als er geen afgedwongen wordt toegepast.   

REFERERende-sleutel beperking wordt niet ondersteund in SQL Analytics.  

## <a name="remarks"></a>Opmerkingen
Met een primaire sleutel en/of unieke sleutel kan SQL Analytics engine een optimaal uitvoerings plan voor een query genereren.  Alle waarden in een primaire-sleutel kolom of een kolom met unieke beperkingen moeten uniek zijn. 

Na het maken van een tabel met een primaire sleutel of unieke beperking in SQL Analytics, moeten gebruikers ervoor zorgen dat alle waarden in die kolommen uniek zijn.  Een schending van dat kan ertoe leiden dat de query onnauwkeurig resultaat retourneert.  In dit voor beeld ziet u hoe een query onnauwkeurig resultaat kan retour neren als de kolom Primary Key of unique constraint dubbele waarden bevat.  

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
Maak een SQL Analytics-tabel met een primaire sleutel: 

```sql 
CREATE TABLE mytable (c1 INT PRIMARY KEY NONCLUSTERED NOT ENFORCED, c2 INT);
```
Maak een SQL Analytics-tabel met een unieke beperking:

```sql
CREATE TABLE t6 (c1 INT UNIQUE NOT ENFORCED, c2 INT);
```

## <a name="next-steps"></a>Volgende stappen

Nadat u de tabellen voor uw SQL Analytics-Data Base hebt gemaakt, is de volgende stap het laden van gegevens in de tabel. Zie [gegevens laden in SQL Analytics-data bases](load-data-wideworldimportersdw.md)voor een zelf studie die u kunt laden.
