---
title: SIN in Azure Cosmos DB-querytaal
description: Meer informatie over SQL-systeemfunctie SIN in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 25e7cf66fdd55a0b641c35443e38b0a67cbe365d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303100"
---
# <a name="sin-azure-cosmos-db"></a>SIN (Azure Cosmos DB)
 Retourneert de trigonometrische sinus van de opgegeven hoek, in radialen, in de opgegeven expressie.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
SIN(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Is een numerieke expressie.  
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende `SIN` voorbeeld wordt de opgegeven hoek berekend.  
  
```sql
SELECT SIN(45.175643) AS sin  
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"sin": 0.929607286611012}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
