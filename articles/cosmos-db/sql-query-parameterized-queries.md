---
title: Geparameteriseerde query's in Azure Cosmos DB
description: Ontdek hoe SQL-parametere query's een robuuste afhandeling en ontsnaping van gebruikersinvoer bieden en onbedoelde blootstelling van gegevens via SQL-injectie voorkomen.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: tisande
ms.openlocfilehash: e15a8236723c1efd80f27f2d253e9bbc44af4b0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870816"
---
# <a name="parameterized-queries-in-azure-cosmos-db"></a>Geparameteriseerde query's in Azure Cosmos DB

Cosmos DB ondersteunt query's met parameters die worden uitgedrukt door de bekende @ notatie. Parameterized SQL biedt robuuste handling en ontsnappen van gebruikersinvoer, en voorkomt onbedoelde blootstelling van gegevens via SQL-injectie.

## <a name="examples"></a>Voorbeelden

U bijvoorbeeld een query `lastName` schrijven `address.state` die en als parameters wordt `lastName` `address.state` uitgevoerd, en deze uitvoeren voor verschillende waarden van en op basis van gebruikersinvoer.

```sql
    SELECT *
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState
```

U dit verzoek vervolgens naar Cosmos DB verzenden als een json-query met parameterals volgt:

```sql
    {
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",
        "parameters": [
            {"name": "@lastName", "value": "Wakefield"},
            {"name": "@addressState", "value": "NY"},
        ]
    }
```

In het volgende voorbeeld wordt het argument TOP ingesteld met een geparameteriseerde query: 

```sql
    {
        "query": "SELECT TOP @n * FROM Families",
        "parameters": [
            {"name": "@n", "value": 10},
        ]
    }
```

Parameterwaarden kunnen elke geldige JSON zijn: tekenreeksen, getallen, Booleaanse, null, zelfs arrays of geneste JSON. Aangezien Cosmos DB schemaloos is, worden parameters niet gevalideerd op elk type.


## <a name="next-steps"></a>Volgende stappen

- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Documentgegevens modelleren](modeling-data.md)
