---
title: Aliasing in Azure Cosmos DB
description: Meer informatie over het gebruik van aliasing in Azure Cosmos DB SQL-query's om twee eigenschappen met dezelfde naam te onderscheiden
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 74849eec4c5808a584894321269c49c41f0b8a5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873468"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Aliasing in Azure Cosmos DB

U expliciet waarden aliasin query's. Als een query twee eigenschappen met dezelfde naam heeft, gebruikt u aliasing om de naam van een of beide eigenschappen te wijzigen, zodat deze worden gedesambigueerd in het geprojecteerde resultaat.

## <a name="examples"></a>Voorbeelden

Het AS-trefwoord dat wordt gebruikt voor aliasing is optioneel, zoals `NameInfo`in het volgende voorbeeld wordt weergegeven bij het projecteren van de tweede waarde als :

```sql
    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo,
           { "name": f.id } NameInfo
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

U ziet deze uitvoer:

```json
    [{
      "AddressInfo": {
        "state": "WA",
        "city": "Seattle"
      },
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]
```

## <a name="next-steps"></a>Volgende stappen

- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [SELECT-component](sql-query-select.md)
- [VAN clausule](sql-query-from.md)
