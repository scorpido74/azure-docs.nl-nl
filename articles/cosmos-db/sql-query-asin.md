---
title: ASIN in Azure Cosmos DB-querytaal
description: Meer informatie over hoe de SQL-systeemfunctie arcsine (ASIN) in Azure Cosmos DB de hoek retourneert in radialen, waarvan de sine de opgegeven numerieke expressie is
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 8b70738a439b6c64a84a63adf63c83995530e92e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302692"
---
# <a name="asin-azure-cosmos-db"></a>ASIN (Azure Cosmos DB)
 Retourneert de hoek, in radialen, waarvan de sinus de opgegeven numerieke expressie is. Dit wordt ook wel arcsine genoemd.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
ASIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Is een numerieke expressie.  
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende `ASIN` voorbeeld wordt de van -1 geretourneerd.  
  
```sql
SELECT ASIN(-1) AS asin  
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"asin": -1.5707963267948966}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
