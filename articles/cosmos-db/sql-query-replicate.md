---
title: REPLICEREN in Azure Cosmos DB-querytaal
description: Meer informatie over SQL-systeemfunctie REPLICATE in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 19fcde522c5cb0355e53a5616145f27fada7dad9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302182"
---
# <a name="replicate-azure-cosmos-db"></a>REPLICEREN (Azure Cosmos DB)
 Herhaalt een tekenreekswaarde een opgegeven aantal keren.
  
## <a name="syntax"></a>Syntaxis
  
```sql
REPLICATE(<str_expr>, <num_expr>)
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een tekenreeksexpressie.
  
*num_expr*  
   Is een numerieke expressie. Als *num_expr* negatief of niet-eindig is, is het resultaat niet gedefinieerd.
  
## <a name="return-types"></a>Retourtypen
  
  Retourneert een tekenreeksexpressie.
  
## <a name="remarks"></a>Opmerkingen
  De maximale lengte van het resultaat is 10.000 tekens, d.w.z. (lengte(*str_expr*) * *num_expr*) <= 10.000.

## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld `REPLICATE` ziet u hoe u deze in een query gebruiken.
  
```sql
SELECT REPLICATE("a", 3) AS replicate
```  
  
 Hier is het resultaat ingesteld.
  
```json
[{"replicate": "aaa"}]
```  

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Tekenreeksfuncties Azure Cosmos DB](sql-query-string-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
