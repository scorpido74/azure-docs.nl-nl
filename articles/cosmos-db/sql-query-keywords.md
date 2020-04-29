---
title: SQL-tref woorden voor Azure Cosmos DB
description: Meer informatie over SQL-tref woorden voor Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: tisande
ms.openlocfilehash: 069548b9b69ef6f7f6bde85ede830d97f3d312db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81261564"
---
# <a name="keywords-in-azure-cosmos-db"></a>Tref woorden in Azure Cosmos DB

In dit artikel worden tref woorden beschreven die kunnen worden gebruikt in Azure Cosmos DB SQL-query's.

## <a name="between"></a>BETWEEN

U kunt het `BETWEEN` sleutel woord gebruiken om query's uit te drukken op bereiken van teken reeks-of numerieke waarden. Met de volgende query worden bijvoorbeeld alle items geretourneerd waarin de categorie van het eerste kind 1-5 is, inclusief.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

U kunt ook het `BETWEEN` sleutel woord in de `SELECT` -component gebruiken, zoals in het volgende voor beeld.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

In de SQL-API, in tegens telling tot ANSI SQL, kunt u de bereik query's expliciet op Eigenschappen van gemengde typen uitvoeren. Dit `grade` kan bijvoorbeeld een getal zijn zoals `5` in sommige items en een teken reeks zoals `grade4` in anderen. In deze gevallen, zoals in Java script, resulteert de vergelijking tussen de twee verschillende typen `Undefined`in, zodat het item wordt overgeslagen.

> [!TIP]
> Voor snellere query-uitvoerings tijden maakt u een indexerings beleid dat gebruikmaakt van een bereik index type voor alle numerieke eigenschappen of `BETWEEN` paden die de component filtert.

## <a name="distinct"></a>DISTINCT

Het `DISTINCT` sleutel woord elimineert dubbele waarden in de projectie van de query.

In dit voor beeld worden de waarden van de query projecten voor elke achternaam:

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

U ziet deze uitvoer:

```json
[
    "Andersen"
]
```

U kunt ook unieke objecten projecteren. In dit geval bestaat het veld lastName niet in een van de twee documenten, dus retourneert de query een leeg object.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

U ziet deze uitvoer:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

DISTINCT kan ook worden gebruikt in de projectie binnen een subquery:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Deze query projecteert een matrix die de OpgegevenNaam van elk kind bevat met dubbele items verwijderd. Deze matrix heeft een alias als ChildNames en geprojecteerd in de buitenste query.

U ziet deze uitvoer:

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

Query's met een statistische systeem functie en een subquery met `DISTINCT` worden niet ondersteund. De volgende query wordt bijvoorbeeld niet ondersteund:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>IN

Gebruik het sleutel woord IN om te controleren of een opgegeven waarde overeenkomt met een wille keurige waarde in een lijst. De volgende query retourneert bijvoorbeeld alle familie-items waarbij de `id` is `WakefieldFamily` of. `AndersenFamily`

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

In het volgende voor beeld worden alle items geretourneerd waarbij de status een van de opgegeven waarden is:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

De SQL-API biedt ondersteuning voor het [herhalen van JSON-matrices](sql-query-object-array.md#Iteration), waarbij een nieuwe constructie wordt toegevoegd via het sleutel woord in in de bron van.

Als u de partitie sleutel in het `IN` filter opneemt, wordt de query automatisch gefilterd op de relevante partities.

## <a name="top"></a>Boven

Met het sleutel woord TOP wordt `N` het eerste aantal query resultaten in een niet-gedefinieerde volg orde geretourneerd. Als best practice gebruikt u TOP met de `ORDER BY` -component om de resultaten te beperken tot `N` het eerste aantal geordende waarden. Het combi neren van deze twee componenten is de enige manier om te zoals verwacht geven op welke rijen het bovenste effect heeft.

U kunt TOP gebruiken met een constante waarde, zoals in het volgende voor beeld of met een variabele waarde met behulp van query's met para meters.

```sql
    SELECT TOP 1 *
    FROM Families f
```

U ziet deze uitvoer:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag](sql-query-getting-started.md)
- [Samenvoegingen](sql-query-join.md)
- [Subquery's](sql-query-subquery.md)