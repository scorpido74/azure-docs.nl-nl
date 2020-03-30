---
title: IS_OBJECT in Azure Cosmos DB-querytaal
description: Meer informatie over sql-systeemfunctie IS_OBJECT in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 43d8237d859249317d7b581e25ed9a2b34d39d00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303797"
---
# <a name="is_object-azure-cosmos-db"></a>IS_OBJECT (Azure Cosmos DB)
 Geeft als resultaat een Booleaanse waarde die aangeeft of het type van de opgegeven expressie een JSON-object is.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
IS_OBJECT(<expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*Expr*  
   Is elke uitdrukking.  
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een Booleaanse expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld worden objecten van JSON Booleaan, getal, tekenreeks, `IS_OBJECT` null, object, array en niet-gedefinieerde typen gecontroleerd met behulp van de functie.  
  
```sql
SELECT   
    IS_OBJECT(true) AS isObj1,   
    IS_OBJECT(1) AS isObj2,  
    IS_OBJECT("value") AS isObj3,   
    IS_OBJECT(null) AS isObj4,  
    IS_OBJECT({prop: "value"}) AS isObj5,   
    IS_OBJECT([1, 2, 3]) AS isObj6,  
    IS_OBJECT({prop: "value"}.prop2) AS isObj7  
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"isObj1":false,"isObj2":false,"isObj3":false,"isObj4":false,"isObj5":true,"isObj6":false,"isObj7":false}]
```  

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie zal profiteren van een [bereikindex](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Volgende stappen

- [Typecontrolefuncties Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
