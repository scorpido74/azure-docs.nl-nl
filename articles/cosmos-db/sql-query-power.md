---
title: POWER in Azure Cosmos DB-querytaal
description: Meer informatie over SQL-systeemfunctie POWER in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 683c53c369f136ad4b917b93e9a92a71072d05e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349629"
---
# <a name="power-azure-cosmos-db"></a>POWER (Azure Cosmos DB)
 Geeft als resultaat de waarde van de opgegeven expressie aan de opgegeven bevoegdheid.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
POWER (<numeric_expr1>, <numeric_expr2>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr1*  
   Is een numerieke expressie.  
  
*numeric_expr2*  
   Is de macht om *numeric_expr1*te verhogen.  
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld wordt aangetoond dat een getal wordt verhoogd tot de kracht van 3 (de kubus van het getal).  
  
```sql
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{pow1: 8, pow2: 15.625}]  
```  

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
