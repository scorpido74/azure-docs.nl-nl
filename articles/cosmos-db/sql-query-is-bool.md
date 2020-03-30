---
title: IS_BOOL in Azure Cosmos DB-querytaal
description: Meer informatie over sql-systeemfunctie IS_BOOL in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b7f1cfb09121309e246b314d57a5e4e475bd0983
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303865"
---
# <a name="is_bool-azure-cosmos-db"></a>IS_BOOL (Azure Cosmos DB)
 Geeft als resultaat een Booleaanse waarde die aangeeft of het type van de opgegeven expressie een Booleaan is.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
IS_BOOL(<expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*Expr*  
   Is elke uitdrukking.  
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een Booleaanse expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld worden objecten van JSON Booleaan, getal, tekenreeks, `IS_BOOL` null, object, array en niet-gedefinieerde typen gecontroleerd met behulp van de functie.  
  
```sql
SELECT   
    IS_BOOL(true) AS isBool1,   
    IS_BOOL(1) AS isBool2,  
    IS_BOOL("value") AS isBool3,   
    IS_BOOL(null) AS isBool4,  
    IS_BOOL({prop: "value"}) AS isBool5,   
    IS_BOOL([1, 2, 3]) AS isBool6,  
    IS_BOOL({prop: "value"}.prop2) AS isBool7  
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"isBool1":true,"isBool2":false,"isBool3":false,"isBool4":false,"isBool5":false,"isBool6":false,"isBool7":false}]
```  

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie zal profiteren van een [bereikindex](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Volgende stappen

- [Typecontrolefuncties Azure Cosmos DB](sql-query-type-checking-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
