---
title: STARTSWITH in Azure Cosmos DB-querytaal
description: Meer informatie over SQL-systeemfunctie STARTSWITH in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 9ed49c067946186f8b79f67bad0a460113eacb73
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78295705"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH (Azure Cosmos DB)
 Retourneert een Booleaan die aangeeft of de eerste tekenreeksexpressie begint met de tweede.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
STARTSWITH(<str_expr1>, <str_expr2>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr1*  
   Is een tekenreeksexpressie.
  
*str_expr2*  
   Is een tekenreeksexpressie te vergelijken met het begin van *str_expr1*.

## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een Booleaanse expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld wordt gecontroleerd of de tekenreeks "abc" begint met "b" en "a".  
  
```sql
SELECT STARTSWITH("abc", "b") AS s1, STARTSWITH("abc", "a") AS s2  
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"s1": false, "s2": true}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie zal profiteren van een [bereikindex](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Volgende stappen

- [Tekenreeksfuncties Azure Cosmos DB](sql-query-string-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
