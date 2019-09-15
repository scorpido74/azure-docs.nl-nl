---
title: Werken met matrices en objecten in Azure Cosmos DB
description: Meer informatie over de SQL-syntaxis voor het maken van matrices en objecten voor Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: tisande
ms.openlocfilehash: 17a0e4ddf5acd267a4cfbb68c218fe9409a91d57
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71003935"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Werken met matrices en objecten in Azure Cosmos DB

Een belang rijke functie van de Azure Cosmos DB SQL API is matrix en het maken van objecten.

## <a name="arrays"></a>Matrices

U kunt matrices maken, zoals wordt weer gegeven in het volgende voor beeld:

```sql
    SELECT [f.address.city, f.address.state] AS CityState
    FROM Families f
```

De resultaten zijn:

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

U kunt ook de [matrix expressie](sql-query-subquery.md#array-expression) gebruiken om een matrix te maken op basis van de resultaten van de [subquery](sql-query-subquery.md) . Met deze query worden alle afzonderlijke namen van onderliggende items in een matrix opgehaald.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a id="Iteration"></a>Herhaling

De SQL-API biedt ondersteuning voor het herhalen van JSON-matrices, waarbij een nieuwe constructie wordt toegevoegd via het [sleutel woord in](sql-query-keywords.md#in) in de bron van. In het volgende voor beeld:

```sql
    SELECT *
    FROM Families.children
```

De resultaten zijn:

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

`children` Met de volgende query wordt iteratie uitgevoerd in `Families` de container. De uitvoer matrix wijkt af van de voor gaande query. In dit voor beeld `children`worden de resultaten gesplitst en afgevlakt in één matrix:  

```sql
    SELECT *
    FROM c IN Families.children
```

De resultaten zijn:

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

U kunt verder filteren op elke afzonderlijke vermelding van de matrix, zoals wordt weer gegeven in het volgende voor beeld:

```sql
    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8
```

De resultaten zijn:

```json
    [{
      "givenName": "Lisa"
    }]
```

U kunt ook aggregatie over het resultaat van een matrix herhaling. Met de volgende query wordt bijvoorbeeld het aantal onderliggende items van alle families geteld:

```sql
    SELECT COUNT(child)
    FROM child IN Families.children
```

De resultaten zijn:

```json
    [
      {
        "$1": 3
      }
    ]
```

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag](sql-query-getting-started.md)
- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Joins](sql-query-join.md)