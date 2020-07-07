---
title: GetCurrentTimestamp in Azure Cosmos DB-query taal
description: Meer informatie over de SQL-functie GetCurrentTimestamp in Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: b816c63da56025fe6e9cbaece2cde5dcd01585d4
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "71351009"
---
# <a name="getcurrenttimestamp-azure-cosmos-db"></a>GetCurrentTimestamp (Azure Cosmos DB)
 Retourneert het aantal milliseconden dat is verstreken sinds 00:00:00 donderdag, 1 januari 1970. 
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentTimestamp ()  
```  
  
## <a name="return-types"></a>Retour typen
  
  Retourneert een numerieke waarde, het huidige aantal milliseconden dat is verstreken sinds de UNIX-epoche, dat wil zeggen het aantal milliseconden dat is verstreken sinds 00:00:00 donderdag, 1 januari 1970.

## <a name="remarks"></a>Opmerkingen

  GetCurrentTimestamp () is een niet-deterministische functie.
  
  Het geretourneerde resultaat is UTC (Coordinated Universal Time).

## <a name="examples"></a>Voorbeelden
  
  In het volgende voor beeld ziet u hoe u de huidige tijds tempel kunt ophalen met behulp van de ingebouwde functie GetCurrentTimestamp ().
  
```sql
SELECT GetCurrentTimestamp() AS currentUtcTimestamp
```  
  
 Hier volgt een voor beeld van een resultatenset.
  
```json
[{
  "currentUtcTimestamp": 1556916469065
}]  
```

## <a name="next-steps"></a>Volgende stappen

- [Datum-en tijd functies Azure Cosmos DB](sql-query-date-time-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
