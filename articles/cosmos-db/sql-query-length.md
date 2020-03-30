---
title: LENGTE in Azure Cosmos DB-querytaal
description: Meer informatie over SQL-systeemfunctie LENGTE in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e020555b0c706b5577bd20ac9bd537604d43ba3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78303712"
---
# <a name="length-azure-cosmos-db"></a>LENGTE (Azure Cosmos DB)
 Geeft als resultaat het aantal tekens van de opgegeven tekenreeksexpressie.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
LENGTH(<str_expr>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*str_expr*  
   Is de tekenreeksexpressie die moet worden geëvalueerd.  
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld wordt de lengte van een tekenreeks geretourneerd.  
  
```sql
SELECT LENGTH("abc") AS len 
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"len": 3}]  
```  

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Tekenreeksfuncties Azure Cosmos DB](sql-query-string-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
