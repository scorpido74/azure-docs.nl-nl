---
title: IS_ARRAY in Azure Cosmos DB-query taal
description: Meer informatie over de SQL-functie IS_ARRAY in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 11ff29dcae7bf5c2e532eee658ee39d39b964e35
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350972"
---
# <a name="is_array-azure-cosmos-db"></a>IS_ARRAY (Azure Cosmos DB)
 Retourneert een Booleaanse waarde die aangeeft of het type van de opgegeven expressie een matrix is.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
IS_ARRAY(<expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*expr*  
   Is een expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een Booleaanse expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld worden objecten gecontroleerd van JSON-Boole-, getal-, teken reeks-, null-, object-, matrix-en niet-gedefinieerde typen met behulp van de functie `IS_ARRAY`.  
  
```sql
SELECT   
 IS_ARRAY(true) AS isArray1,   
 IS_ARRAY(1) AS isArray2,  
 IS_ARRAY("value") AS isArray3,  
 IS_ARRAY(null) AS isArray4,  
 IS_ARRAY({prop: "value"}) AS isArray5,   
 IS_ARRAY([1, 2, 3]) AS isArray6,  
 IS_ARRAY({prop: "value"}.prop2) AS isArray7  
```  
  
 Hier volgt de resultatenset.  
  
```json
[{"isArray1":false,"isArray2":false,"isArray3":false,"isArray4":false,"isArray5":false,"isArray6":true,"isArray7":false}]
```  

## <a name="next-steps"></a>Volgende stappen

- [Type controleren van functies Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
