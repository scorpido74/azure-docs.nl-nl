---
title: RAND in Azure Cosmos DB-querytaal
description: Meer informatie over SQL-systeemfunctie RAND in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/16/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: e44878b6d65725f08aeca4eb07088315ae2bb78a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78302216"
---
# <a name="rand-azure-cosmos-db"></a>RAND (Azure Cosmos DB)
 Geeft als resultaat een willekeurig gegenereerde numerieke waarde van [0,1).
 
## <a name="syntax"></a>Syntaxis
  
```sql
RAND ()  
```  

## <a name="return-types"></a>Retourtypen

  Geeft als resultaat een numerieke expressie.

## <a name="remarks"></a>Opmerkingen

  `RAND`is een niet-deterministische functie. Repetitieve `RAND` oproepen van niet dezelfde resultaten terug te keren.

## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld wordt een willekeurig gegenereerde numerieke waarde geretourneerd.
  
```sql
SELECT RAND() AS rand 
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"rand": 0.87860053195618093}]  
``` 

## <a name="remarks"></a>Opmerkingen

Deze systeemfunctie maakt geen gebruik van de index.

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
