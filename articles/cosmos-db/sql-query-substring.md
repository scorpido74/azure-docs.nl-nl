---
title: SUBSTRING in Azure Cosmos DB-querytaal
description: Meer informatie over SUBSTRING van SQL-systeemfuncties in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d4462fc407093b23510bddfae4d9f55d68f8c0fa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303695"
---
# <a name="substring-azure-cosmos-db"></a>SUBSTRING (Azure Cosmos DB)
 Geeft als resultaat een deel van een tekenreeksexpressie die begint bij de opgegeven tekenpositie op nul en blijft naar de opgegeven lengte of tot het einde van de tekenreeks.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een tekenreeksexpressie.
  
*num_expr1*  
   Is een numerieke expressie die het beginteken aangeeft. Een waarde van 0 is het eerste teken van *str_expr*.
  
*num_expr2*  
   Is een numerieke expressie die het maximum aantal tekens van *str_expr* aangeeft dat moet worden geretourneerd. Een waarde van 0 of minder resulteert in een lege tekenreeks.

## <a name="return-types"></a>Retourtypen
  
  Retourneert een tekenreeksexpressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld wordt de subtekenreeks van "abc" geretourneerd vanaf 1 en voor een lengte van 1 teken.  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"substring": "b"}]  
```

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie profiteert van een [bereikindex](index-policy.md#includeexclude-strategy) als de startpositie is `0`.

## <a name="next-steps"></a>Volgende stappen

- [Tekenreeksfuncties Azure Cosmos DB](sql-query-string-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
