---
title: RECHTS in Azure Cosmos DB-querytaal
description: Meer informatie over SQL-systeemfunctie RIGHT in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 247616d2ac4f2a5799a5896d679f6e6d5917d5a6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302148"
---
# <a name="right-azure-cosmos-db"></a>RECHTS (Azure Cosmos DB)
 Retourneert het rechterdeel van een tekenreeks met het opgegeven aantal tekens.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
RIGHT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is de tekenreeksexpressie om tekens uit te extraheren.  
  
*num_expr*  
   Is een numerieke expressie die het aantal tekens opgeeft.  
  
## <a name="return-types"></a>Retourtypen
  
  Retourneert een tekenreeksexpressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld wordt het rechterdeel van "abc" voor verschillende lengtewaarden geretourneerd.  
  
```sql
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"r1": "c", "r2": "bc"}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Tekenreeksfuncties Azure Cosmos DB](sql-query-string-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
