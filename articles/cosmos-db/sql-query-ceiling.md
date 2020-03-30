---
title: PLAFOND in Azure Cosmos DB-querytaal
description: Meer informatie over hoe de SQL-systeemfunctie CEILING in Azure Cosmos DB de kleinste gehele waarde retourneert die groter is dan of gelijk is aan de opgegeven numerieke expressie.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 81f113aa51a7f739b506ec7e3eb5bf2cb9f49a03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302641"
---
# <a name="ceiling-azure-cosmos-db"></a>PLAFOND (Azure Cosmos DB)
 Retourneert het kleinste gehele getal dat groter is dan of gelijk is aan de opgegeven numerieke expressie.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
CEILING (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Is een numerieke expressie.  
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld worden positieve numerieke, `CEILING` negatieve en nulwaarden met de functie weergegeven.  
  
```sql
SELECT CEILING(123.45) AS c1, CEILING(-123.45) AS c2, CEILING(0.0) AS c3  
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{c1: 124, c2: -123, c3: 0}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie zal profiteren van een [bereikindex](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
