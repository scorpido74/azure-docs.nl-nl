---
title: Azure Cosmos DB query taal inschakelen
description: Meer informatie over de kracht van de functie voor SQL-systeem functies in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 683c53c369f136ad4b917b93e9a92a71072d05e0
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "71349629"
---
# <a name="power-azure-cosmos-db"></a>ENERGIE (Azure Cosmos DB)
 Retourneert de waarde van de opgegeven expressie naar de opgegeven macht.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
POWER (<numeric_expr1>, <numeric_expr2>)  
```  
  
## <a name="arguments"></a>Argumenten
  
*numeric_expr1*  
   Is een numerieke expressie.  
  
*numeric_expr2*  
   Is de macht waarmee *numeric_expr1*worden verhoogd.  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld wordt gedemonstreerd dat een getal wordt verhoogd naar de macht 3 (de kubus van het getal).  
  
```sql
SELECT POWER(2, 3) AS pow1, POWER(2.5, 3) AS pow2  
```  
  
 Dit is de resultatenset.  
  
```json
[{pow1: 8, pow2: 15.625}]  
```  

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
