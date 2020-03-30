---
title: CONCAT in Azure Cosmos DB-querytaal
description: Meer informatie over hoe de FUNCTIE CONCAT SQL-systeem in Azure Cosmos DB een tekenreeks retourneert die het resultaat is van het gelijktijdig maken van twee of meer tekenreekswaarden
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c8a0941376ed74d7f8cb819d78df43eb9f0b7bd1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302607"
---
# <a name="concat-azure-cosmos-db"></a>CONCAT (Azure Cosmos DB)
 Retourneert een tekenreeks die het resultaat is van het samenvoegen van twee of meer tekenreekswaarden.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is een tekenreeksexpressie die moet worden samengevoegd met de andere waarden. De `CONCAT` functie vereist ten minste twee *str_expr* argumenten.  
  
## <a name="return-types"></a>Retourtypen
  
  Retourneert een tekenreeksexpressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld wordt de samengevoegde tekenreeks van de opgegeven waarden geretourneerd.  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"concat": "abcdef"}]  
```  
  
## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Tekenreeksfuncties Azure Cosmos DB](sql-query-string-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
