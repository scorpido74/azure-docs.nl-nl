---
title: GRADEN in Azure Cosmos DB-querytaal
description: Meer informatie over de SQL-systeemfunctie DEGREES in Azure Cosmos DB om de overeenkomstige hoek in graden terug te geven voor een hoek die is opgegeven in radialen
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: d175ba53a71998fc8e7812a1b761f9cd264c38a9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299467"
---
# <a name="degrees-azure-cosmos-db"></a>GRADEN (Azure Cosmos DB)
 Retourneert de overeenkomende hoek in graden voor een hoek die is opgegeven in radialen.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
DEGREES (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr*  
   Is een numerieke expressie.  
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld wordt het aantal graden geretourneerd in een hoek van PI/2-radialen.  
  
```sql
SELECT DEGREES(PI()/2) AS degrees  
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"degrees": 90}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
