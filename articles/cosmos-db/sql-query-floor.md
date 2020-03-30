---
title: FLOOR in Azure Cosmos DB-querytaal
description: Meer informatie over de FUNCTIE FLOOR SQL-systeem in Azure Cosmos DB om het grootste gehele getal te retourneren dat kleiner is dan of gelijk is aan de opgegeven numerieke expressie
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 04dfa6a028cf7c44bf99c665b396d51d8a0f3cef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303185"
---
# <a name="floor-azure-cosmos-db"></a>FLOOR (Azure Cosmos DB)
 Retourneert het grootste gehele getal dat kleiner is dan of gelijk is aan de opgegeven numerieke expressie.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
FLOOR (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Is een numerieke expressie.  
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld worden positieve numerieke, `FLOOR` negatieve en nulwaarden met de functie weergegeven.  
  
```sql
SELECT FLOOR(123.45) AS fl1, FLOOR(-123.45) AS fl2, FLOOR(0.0) AS fl3  
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{fl1: 123, fl2: -124, fl3: 0}]  
```

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie zal profiteren van een [bereikindex](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
