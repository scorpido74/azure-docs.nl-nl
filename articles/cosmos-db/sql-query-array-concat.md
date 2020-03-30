---
title: ARRAY_CONCAT in Azure Cosmos DB-querytaal
description: Meer informatie over hoe de array concat SQL-systeemfunctie in Azure Cosmos DB een array retourneert die het resultaat is van het gelijktijdigstellen van twee of meer matrixwaarden
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8f2b37181e5d743809bb1f60be4056cb4442a8d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78295875"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
 Retourneert een matrix die het resultaat is van het samenvoegen van twee of meer matrixwaarden.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>Argumenten
  
*arr_expr*  
   Is een arrayexpressie die moet worden samengevoegd met de andere waarden. De `ARRAY_CONCAT` functie vereist ten minste twee *arr_expr* argumenten.  
  
## <a name="return-types"></a>Retourtypen
  
  Retourneert een matrixexpressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld hoe u twee arrays concaten.  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Arrayfuncties Azure Cosmos DB](sql-query-array-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
