---
title: GetCurrentTicks in Azure Cosmos DB-query taal
description: Meer informatie over de SQL-functie GetCurrentTicks in Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 6b3cd5ab5849c33172e4a629c79fb792b82f1255
ms.sourcegitcommit: 152c522bb5ad64e5c020b466b239cdac040b9377
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/14/2020
ms.locfileid: "88227177"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB)

Retourneert de huidige datum en tijd, gemeten in Ticks.
  
## <a name="syntax"></a>Syntaxis
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>Retour typen

Retourneert een positieve integerwaarde.

## <a name="remarks"></a>Opmerkingen

Deze systeem functie maakt geen gebruik van de index.

## <a name="examples"></a>Voorbeelden

Hier volgt een voor beeld waarin de huidige tijd wordt geretourneerd, gemeten in maat streepjes:

```sql
SELECT GetCurrentTicks() AS CurrentTimeInTicks
```

```json
[
    {
        "CurrentTimeInTicks": 15973607943002652
    }
]
```

## <a name="next-steps"></a>Volgende stappen

- [Datum-en tijd functies Azure Cosmos DB](sql-query-date-time-functions.md)
- [Systeem functies Azure Cosmos DB](sql-query-system-functions.md)
- [Inleiding tot Azure Cosmos DB](introduction.md)
