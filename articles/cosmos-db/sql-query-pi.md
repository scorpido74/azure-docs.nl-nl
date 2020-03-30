---
title: PI in Azure Cosmos DB-querytaal
description: Meer informatie over SQL-systeemfunctie PI in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 27832008e8922e339a648985192a58b111555bc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71349646"
---
# <a name="pi-azure-cosmos-db"></a>PI (Azure Cosmos DB)
 Retourneert de constante waarde van PI.  
  
## <a name="syntax"></a>Syntaxis
  
```sql
PI ()  
```  
   
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een numerieke expressie.  
  
## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld `PI`wordt de waarde van .  
  
```sql
SELECT PI() AS pi 
```  
  
 Hier is het resultaat ingesteld.  
  
```json
[{"pi": 3.1415926535897931}]  
```  

## <a name="next-steps"></a>Volgende stappen

- [Wiskundige functies Azure Cosmos DB](sql-query-mathematical-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
