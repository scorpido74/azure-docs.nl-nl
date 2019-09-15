---
title: Aliasing in Azure Cosmos DB
description: Meer informatie over alias waarden in Azure Cosmos DB SQL-query's
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: 3b17cbc7710647b1e1875025a1db1849034ec1dc
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002071"
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

De resultaten zijn:

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
