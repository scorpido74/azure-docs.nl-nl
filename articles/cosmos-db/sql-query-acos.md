---
title: ACOS in Azure Cosmos DB-querytaal
description: Meer informatie over hoe het SQL-systeem (arccosice) in Azure Cosmos DB de hoek retourneert in radialen, waarvan de cosine de opgegeven numerieke expressie is
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 733d6b009f03d61c37170cc506a3b2ec842d7c47
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300958"
---
# <a name="acos-azure-cosmos-db"></a>ACOS (Azure Cosmos DB)
 Retourneert de hoek, in radialen, waarvan de cosinus de opgegeven numerieke expressie is. Dit wordt ook wel de arccosinus genoemd.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
ACOS(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Is een numerieke expressie.  
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende `ACOS` voorbeeld wordt de van -1 geretourneerd.  
  
```sql
SELECT ACOS(-1) AS acos 
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"acos": 3.1415926535897931}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
