---
title: LINKS in Azure Cosmos DB-querytaal
description: Meer informatie over SQL-systeemfunctie LEFT in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 0eac35a91e4d5158335d6797d49a09f8f6f391e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303746"
---
# <a name="left-azure-cosmos-db"></a>LINKS (Azure Cosmos DB)
 Retourneert het linkerdeel van een tekenreeks met het opgegeven aantal tekens.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
LEFT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is de tekenreeksexpressie om tekens uit te extraheren.  
  
*num_expr*  
   Is een numerieke expressie die het aantal tekens opgeeft.  
  
## <a name="return-types"></a>Retourtypen
  
  Retourneert een tekenreeksexpressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld wordt het linkergedeelte van "abc" geretourneerd voor verschillende lengtewaarden.  
  
```sql
SELECT LEFT("abc", 1) AS l1, LEFT("abc", 2) AS l2 
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"l1": "a", "l2": "ab"}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie zal profiteren van een [bereikindex](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Volgende stappen

- [Tekenreeksfuncties Azure Cosmos DB](sql-query-string-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
