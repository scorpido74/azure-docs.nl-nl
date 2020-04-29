---
title: Werken met matrices en objecten in Azure Cosmos DB
description: Meer informatie over de SQL-syntaxis voor het maken van matrices en objecten in Azure Cosmos DB. Dit artikel bevat ook enkele voor beelden voor het uitvoeren van bewerkingen op Matrix objecten
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: 5b2801b0a71f04803955e9d8bc18a97133019996
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79246550"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Werken met matrices en objecten in Azure Cosmos DB

Een belang rijke functie van de Azure Cosmos DB SQL API is matrix en het maken van objecten.

## <a name="arrays"></a>Matrixen

U kunt matrices maken, zoals wordt weer gegeven in het volgende voor beeld:

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

U kunt ook de [matrix expressie](sql-query-subquery.md#array-expression) gebruiken om een matrix te maken op basis van de resultaten van de [subquery](sql-query-subquery.md) . Met deze query worden alle afzonderlijke namen van onderliggende items in een matrix opgehaald.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

## <a name="iteration"></a><a id="Iteration"></a>Iteratie

De SQL-API biedt ondersteuning voor het herhalen van JSON-matrices, waarbij een nieuwe constructie wordt toegevoegd via het [sleutel woord in](sql-query-keywords.md#in) in de bron van. In het volgende voorbeeld:

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

`children` Met de volgende query wordt iteratie uitgevoerd in `Families` de container. De uitvoer matrix wijkt af van de voor gaande query. In dit voor beeld `children`worden de resultaten gesplitst en afgevlakt in één matrix:  

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

U kunt verder filteren op elke afzonderlijke vermelding van de matrix, zoals wordt weer gegeven in het volgende voor beeld:

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

U kunt ook aggregatie over het resultaat van een matrix herhaling. Met de volgende query wordt bijvoorbeeld het aantal onderliggende items van alle families geteld:

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

- [Aan de slag](sql-query-getting-started.md)
- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Samenvoegingen](sql-query-join.md)