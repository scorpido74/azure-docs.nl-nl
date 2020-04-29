---
title: ARRAY_CONCAT in Azure Cosmos DB query taal
description: Meer informatie over hoe de SQL-systeem functie van de matrix in Azure Cosmos DB een matrix retourneert die het resultaat is van het samen voegen van twee of meer matrix waarden
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8f2b37181e5d743809bb1f60be4056cb4442a8d2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
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
   Is een matrix expressie die moet worden samengevoegd met de andere waarden. Voor `ARRAY_CONCAT` de functie zijn ten minste twee *arr_expr* argumenten vereist.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een matrix expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt uitgelegd hoe u twee matrices kunt samen voegen.  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Dit is de resultatenset.  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  
## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Matrix functies Azure Cosmos DB](sql-query-array-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
