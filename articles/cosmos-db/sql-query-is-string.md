---
title: IS_STRING in Azure Cosmos DB query taal
description: Meer informatie over de functie IS_STRING van SQL-systeem in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 17832704b69d35ab9800f06558fac50db2577241
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097935"
---
# <a name="is_string-azure-cosmos-db"></a>IS_STRING (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Retourneert een Booleaanse waarde die aangeeft of het type van de opgegeven expressie een teken reeks is.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
IS_STRING(<expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*expressie*  
   Is een expressie.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een Boole-expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld worden objecten van JSON-Boole, getal, teken reeks, null, object, matrix en niet-gedefinieerde typen gecontroleerd met behulp van de `IS_STRING` functie.  
  
```sql
SELECT   
       IS_STRING(true) AS isStr1,   
       IS_STRING(1) AS isStr2,  
       IS_STRING("value") AS isStr3,   
       IS_STRING(null) AS isStr4,  
       IS_STRING({prop: "value"}) AS isStr5,   
       IS_STRING([1, 2, 3]) AS isStr6,  
       IS_STRING({prop: "value"}.prop2) AS isStr7  
```  
  
 Dit is de resultatenset.  
  
```json
[{"isStr1":false,"isStr2":false,"isStr3":true,"isStr4":false,"isStr5":false,"isStr6":false,"isStr7":false}] 
```  

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt deel uit van een [bereik index](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Volgende stappen

- [Type controleren van functies Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
