---
title: SQL-tref woorden voor Azure Cosmos DB
description: Meer informatie over SQL-tref woorden voor Azure Cosmos DB.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: mjbrown
ms.openlocfilehash: a9de9435c0e2fb2b67733a995ff412978ea02d89
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250295"
---
# <a name="keywords-in-azure-cosmos-db"></a>Tref woorden in Azure Cosmos DB
In dit artikel worden tref woorden beschreven die kunnen worden gebruikt in Azure Cosmos DB SQL-query's.

## <a name="between"></a>VERDEELD

Net als in ANSI SQL kunt u het sleutel woord tussen gebruiken om query's uit te drukken op bereiken van teken reeks-of numerieke waarden. Met de volgende query worden bijvoorbeeld alle items geretourneerd waarin de categorie van het eerste kind 1-5 is, inclusief.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

In tegens telling tot in ANSI SQL kunt u ook de component between in de component FROM gebruiken, zoals in het volgende voor beeld.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

In de SQL-API, in tegens telling tot ANSI SQL, kunt u de bereik query's expliciet op Eigenschappen van gemengde typen uitvoeren. `grade` kan bijvoorbeeld een getal zijn zoals `5` in sommige items en een teken reeks zoals `grade4` in anderen. In deze gevallen, zoals in Java script, resulteert de vergelijking tussen de twee verschillende typen in `Undefined`, zodat het item wordt overgeslagen.

> [!TIP]
> Voor een snellere query-uitvoerings tijd maakt u een indexerings beleid dat gebruikmaakt van een bereik index type voor numerieke eigenschappen of paden die de component between filtert.

## <a name="distinct"></a>ONDERSCHEIDEN

Met het sleutel woord DISTINCT elimineert u dubbele waarden in de projectie van de query.

In dit voor beeld worden de waarden van de query projecten voor elke achternaam:

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

De resultaten zijn:

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

De resultaten zijn:

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

De resultaten zijn:

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

Query's met een statistische systeem functie en een subquery met DISTINCT worden niet ondersteund. De volgende query wordt bijvoorbeeld niet ondersteund:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>Naast

Gebruik het sleutel woord IN om te controleren of een opgegeven waarde overeenkomt met een wille keurige waarde in een lijst. Met de volgende query worden bijvoorbeeld alle familie-items geretourneerd waarbij de `id` is `WakefieldFamily` of `AndersenFamily`.

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

## <a name="top"></a>Boven

Met het sleutel woord TOP wordt het eerste `N` aantal query resultaten in een niet-gedefinieerde volg orde geretourneerd. Als best practice gebruikt u TOP met de component ORDER BY om de resultaten te beperken tot het eerste `N` aantal geordende waarden. Het combi neren van deze twee componenten is de enige manier om te zoals verwacht geven op welke rijen het bovenste effect heeft.

U kunt TOP gebruiken met een constante waarde, zoals in het volgende voor beeld of met een variabele waarde met behulp van query's met para meters.

```sql
    SELECT TOP 1 *
    FROM Families f
```

De resultaten zijn:

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
- [Joins](sql-query-join.md)
- [Subquery's](sql-query-subquery.md)