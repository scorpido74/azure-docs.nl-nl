---
title: RONDE in Azure Cosmos DB-querytaal
description: Meer informatie over SQL-systeemfunctie ROUND in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b6aac5a963d0f58a3b21b9fb0958793169a3d444
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302114"
---
# <a name="round-azure-cosmos-db"></a>AFRONDEN (Azure Cosmos DB)
 Retourneert een numerieke waarde, afgerond naar het dichtstbijzijnde gehele getal.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
ROUND(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Is een numerieke expressie.  
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een numerieke expressie.  
  
## <a name="remarks"></a>Opmerkingen
  
  De uitgevoerde afrondingsbewerking volgt op halverwege afronding van nul. Als de invoer een numerieke expressie is die precies tussen twee gehele getallen valt, is het resultaat de dichtstbijzijnde gehele getalwaarde verwijderd van nul.  
  
  |<numeric_expr numeric_expr>|Afgeronde|
  |-|-|
  |-6.5000|-7|
  |-0.5|-1|
  |0.5|1|
  |6.5000|7||
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld worden de volgende positieve en negatieve getallen afgerond op het dichtstbijzijnde gehele getal.  
  
```sql
SELECT ROUND(2.4) AS r1, ROUND(2.6) AS r2, ROUND(2.5) AS r3, ROUND(-2.4) AS r4, ROUND(-2.6) AS r5  
```  
  
  Hier is het resultaat ingesteld.  
  
```json
[{r1: 2, r2: 3, r3: 3, r4: -2, r5: -3}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie zal profiteren van een [bereikindex](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
