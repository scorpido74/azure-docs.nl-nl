---
title: Primaire, buitenlandse en unieke toetsen
description: Ondersteuning voor tabelbeperkingen in SQL Analytics in Azure Synapse Analytics
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
ms.openlocfilehash: b9336a5e230e90e1abd7f2d40d431b988385c009
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350023"
---
# <a name="primary-key-foreign-key-and-unique-key-in-sql-analytics"></a>Primaire sleutel, buitenlandse sleutel en unieke sleutel in SQL Analytics

Meer informatie over tabelbeperkingen in SQL Analytics, waaronder primaire sleutel, externe sleutel en unieke sleutel.

## <a name="table-constraints"></a>Tabelbeperkingen 
SQL Analytics ondersteunt deze tabelbeperkingen: 
- PRIMAIRE SLEUTEL wordt alleen ondersteund wanneer NIET-GECLUSTERD en NIET AFGEDWONGEN worden beide gebruikt.    
- Unieke beperking wordt alleen ondersteund met NIET AFGEDWONGEN wordt gebruikt.   

De beperking van FOREIGN KEY wordt niet ondersteund in SQL Analytics.  

## <a name="remarks"></a>Opmerkingen
Met primaire sleutel en/of unieke sleutel kan SQL Analytics-engine een optimaal uitvoeringsplan voor een query genereren.  Alle waarden in een primaire sleutelkolom of een unieke beperkingskolom moeten uniek zijn. 

Nadat ze een tabel hebben gemaakt met primaire sleutel of unieke beperking in SQL Analytics, moeten gebruikers ervoor zorgen dat alle waarden in die kolommen uniek zijn.  Een schending daarvan kan ertoe leiden dat de query een onjuist resultaat oplevert.  In dit voorbeeld ziet u hoe een query een onjuist resultaat kan retourneren als de primaire sleutel of unieke kolom met dubbele waarden.  

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

Nadat u de tabellen voor uw SQL Analytics-database hebt hebt maken, moet u gegevens in de tabel laden. Zie Gegevens laden [naar SQL Analytics-databases voor](load-data-wideworldimportersdw.md)een zelfstudie voor het laden.
