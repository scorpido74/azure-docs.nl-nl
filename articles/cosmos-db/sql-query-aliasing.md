---
title: Aliasing in Azure Cosmos DB
description: Meer informatie over het gebruik van aliasing in Azure Cosmos DB SQL-query's om twee eigenschappen met dezelfde naam te onderscheiden
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: mjbrown
ms.openlocfilehash: 74849eec4c5808a584894321269c49c41f0b8a5c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74873468"
---
# <a name="aliasing-in-azure-cosmos-db"></a>Aliasing in Azure Cosmos DB

U kunt expliciet alias waarden in query's. Als een query twee eigenschappen met dezelfde naam heeft, gebruikt u aliasing om de naam van een of beide eigenschappen te wijzigen, zodat ze in het verwachte resultaat worden disambiguated.

## <a name="examples"></a>Voorbeelden

Het sleutel woord als dat wordt gebruikt voor aliasing is optioneel, zoals wordt weer gegeven in het volgende voor beeld wanneer `NameInfo`de tweede waarde wordt geprojecteerd als:

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
- [FROM-component](sql-query-from.md)
