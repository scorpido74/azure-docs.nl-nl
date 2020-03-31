---
title: GetCurrentTimestamp in Azure Cosmos DB-querytaal
description: Meer informatie over SQL-systeemfunctie GetCurrentTimestamp in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b816c63da56025fe6e9cbaece2cde5dcd01585d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "71351009"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
 Geeft als resultaat het aantal milliseconden dat is verstreken sinds donderdag 1 januari 1970 00:00:00 uur. 
  
## <a name="syntax"></a>Syntaxis
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Retourtypen
  
  Geeft als resultaat een numerieke waarde, het huidige aantal milliseconden dat is verstreken sinds het Unix-tijdperk, d.w.z. het aantal milliseconden dat is verstreken sinds donderdag 1 januari 1970 00:00:00.

## <a name="remarks"></a>Opmerkingen

  GetCurrentTimestamp() is een niet-deterministische functie.
  
  Het geretourneerde resultaat is UTC (Coordinated Universal Time).

## <a name="examples"></a>Voorbeelden
  
  In het volgende voorbeeld ziet u hoe u de huidige tijdstempel krijgen met de ingebouwde functie GetCurrentTimestamp().
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Hier is een voorbeeldresultaatset.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>Volgende stappen

- [Datum- en tijdfuncties Azure Cosmos DB](sql-query-date-time-functions.md)
- [Systeemfuncties Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
