---
title: ARRAY_LENGTH in Azure Cosmos DB-querytaal
description: Meer informatie over hoe de SQL-systeemfunctie arraylengte in Azure Cosmos DB het aantal elementen van de opgegeven arrayexpressie retourneert
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 3596ce4bc702d5e54225d8c90db2f9563feab670
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303984"
---
# <a name="array_length-azure-cosmos-db"></a>ARRAY_LENGTH (Azure Cosmos DB)
 Retourneert het aantal elementen van de opgegeven matrixexpressie.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
ARRAY_LENGTH(<arr_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*arr_expr*  
   Is een arrayexpressie.  
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld hoe u `ARRAY_LENGTH`de lengte van een array krijgt met behulp van .  
  
```sql
SELECT ARRAY_LENGTH(["apples", "strawberries", "bananas"]) AS len  
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"len": 3}]  
```  
  
## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Arrayfuncties Azure Cosmos DB](sql-query-array-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
