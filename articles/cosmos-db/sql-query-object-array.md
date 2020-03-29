---
title: Werken met arrays en objecten in Azure Cosmos DB
description: Leer de SQL-syntaxis om arrays en objecten te maken in Azure Cosmos DB. In dit artikel worden ook enkele voorbeelden gegeven om bewerkingen uit te voeren op arrayobjecten
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 5b2801b0a71f04803955e9d8bc18a97133019996
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246550"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Werken met arrays en objecten in Azure Cosmos DB

Een belangrijk kenmerk van de Azure Cosmos DB SQL API is array en objectcreatie.

## <a name="arrays"></a>Matrixen

U arrays maken, zoals in het volgende voorbeeld wordt weergegeven:

```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

U ziet deze uitvoer:

```json
    [
      {
        "CityState": [
          "Seattle",
          "WA"
        ]
      },
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]
```

U de [arrayexpressie](sql-query-subquery.md#array-expression) ook gebruiken om een array te construeren op basis van de resultaten [van subquery.You](sql-query-subquery.md) can also use the ARRAY expression to construct an array from subquery's results. Deze query krijgt alle verschillende gegeven namen van kinderen in een array.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a name="iteration"></a><a id="Iteration"></a>Iteratie

De SQL API biedt ondersteuning voor het herhalen van JSON-arrays, met een nieuwe constructie die is toegevoegd via het [IN-trefwoord](sql-query-keywords.md#in) in de FROM-bron. In het volgende voorbeeld:

```sql
    SELECT *
    FROM Families.children
```

U ziet deze uitvoer:

```json
    [
      [
        {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        }, 
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]
```

De volgende query `children` voert `Families` iteratie uit in de container. De uitvoerarray verschilt van de vorige query. In dit `children`voorbeeld worden de resultaten opgesplitst en afgevlakt in één array:  

```sql
    SELECT *
    FROM c IN Families.children
```

U ziet deze uitvoer:

```json
    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]
```

U verder filteren op elke afzonderlijke vermelding van de array, zoals in het volgende voorbeeld wordt weergegeven:

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

U ziet deze uitvoer:

```json
    [{
      "givenName": "Lisa"
    }]
```

U ook aggregaat over het resultaat van een array-iteratie. De volgende query telt bijvoorbeeld het aantal kinderen onder alle gezinnen:

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

U ziet deze uitvoer:

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a name="next-steps"></a>Volgende stappen

- [Slag](sql-query-getting-started.md)
- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Joins](sql-query-join.md)