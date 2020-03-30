---
title: VERVANGEN in Azure Cosmos DB-querytaal
description: Meer informatie over SQL-systeemfunctie VERVANGEN in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 758ac13530752df481d27e7e253f025f5c8d6430
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302199"
---
# <a name="replace-azure-cosmos-db"></a>VERVANGEN (Azure Cosmos DB)
 Vervangt alle exemplaren van een opgegeven tekenreekswaarde door een andere tekenreekswaarde.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
REPLACE(<str_expr1>, <str_expr2>, <str_expr3>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr1*  
   Moet de tekenreeksexpressie worden doorzocht.  
  
*str_expr2*  
   Is de tekenreeksexpressie te vinden.  
  
*str_expr3*  
   Is de tekenreeksexpressie ter vervanging *van* str_expr2 in *str_expr1*.  
  
## <a name="return-types"></a>Retourtypen
  
  Retourneert een tekenreeksexpressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld `REPLACE` ziet u hoe u deze in een query gebruiken.  
  
```sql
SELECT REPLACE("This is a Test", "Test", "desk") AS replace
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"replace": "This is a desk"}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Tekenreeksfuncties Azure Cosmos DB](sql-query-string-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
