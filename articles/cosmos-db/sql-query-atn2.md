---
title: ATN2 in Azure Cosmos DB-querytaal
description: Meer informatie over hoe de ATN2 SQL-systeemfunctie in Azure Cosmos DB de hoofdwaarde van de boograaklijn van y/x retourneert, uitgedrukt in radialen
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 696e14e75998ead04c99fab2b84fc4c742d5f54a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302658"
---
# <a name="atn2-azure-cosmos-db"></a>ATN2 (Azure Cosmos DB)
 Geeft als resultaat de hoofdwaarde van de boograaklijn van y/x, uitgedrukt in radialen.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
ATN2(<numeric_expr>, <numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Is een numerieke expressie.  
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld wordt de ATN2 berekend voor de opgegeven x- en y-componenten.  
  
```sql
SELECT ATN2(35.175643, 129.44) AS atn2  
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"atn2": 1.3054517947300646}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
