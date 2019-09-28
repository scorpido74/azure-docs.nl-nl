---
title: ARRAY_CONCAT in Azure Cosmos DB-query taal
description: Meer informatie over de SQL-functie ARRAY_CONCAT in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ad973650ac205313f9045c170f99e15e385a82d1
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348712"
---
# <a name="array_concat-azure-cosmos-db"></a>ARRAY_CONCAT (Azure Cosmos DB)
 Retourneert een matrix die het resultaat is van het samenvoegen van twee of meer matrixwaarden.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
ARRAY_CONCAT (<arr_expr1>, <arr_expr2> [, <arr_exprN>])  
```  
  
## <a name="arguments"></a>Argumenten
  
*arr_expr*  
   Is een matrix expressie die moet worden samengevoegd met de andere waarden. Voor de functie `ARRAY_CONCAT` zijn ten minste twee *arr_expr* -argumenten vereist.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een matrixexpressie.  
  
## <a name="examples"></a>Voorbeelden
  
  Het volgende voorbeeld over het samenvoegen van twee matrices.  
  
```sql
SELECT ARRAY_CONCAT(["apples", "strawberries"], ["bananas"]) AS arrayConcat 
```  
  
 Hier volgt de resultatenset.  
  
```json
[{"arrayConcat": ["apples", "strawberries", "bananas"]}]  
```  
  

## <a name="next-steps"></a>Volgende stappen

- [Matrix functies Azure Cosmos DB](sql-query-array-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
