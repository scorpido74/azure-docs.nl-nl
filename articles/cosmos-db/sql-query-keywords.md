---
title: SQL-trefwoorden voor Azure Cosmos DB
description: Meer informatie over SQL-zoekwoorden voor Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: tisande
ms.openlocfilehash: 069548b9b69ef6f7f6bde85ede830d97f3d312db
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261564"
---
# <a name="keywords-in-azure-cosmos-db"></a>Trefwoorden in Azure Cosmos DB

In dit artikel worden trefwoorden beschreven die kunnen worden gebruikt in AZURE Cosmos DB SQL-query's.

## <a name="between"></a>BETWEEN

U `BETWEEN` het trefwoord gebruiken om query's uit te drukken op basis van reeksen tekenreeks- of numerieke waarden. Met de volgende query worden bijvoorbeeld alle items geretourneerd waarin het cijfer van het eerste kind 1-5 is, inclusief.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

U het `BETWEEN` zoekwoord `SELECT` ook in de clausule gebruiken, zoals in het volgende voorbeeld.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

In SQL API u, in tegenstelling tot ANSI SQL, bereikquery's uitdrukken op basis van eigenschappen van gemengde typen. Het kan `grade` bijvoorbeeld een `5` getal zijn zoals in `grade4` sommige items en een tekenreeks zoals in andere. In deze gevallen, zoals in JavaScript, resulteert de `Undefined`vergelijking tussen de twee verschillende typen in , zodat het item wordt overgeslagen.

> [!TIP]
> Maak voor snellere queryuitvoeringstijden een indexeringsbeleid dat een bereikindextype gebruikt `BETWEEN` voor numerieke eigenschappen of paden die de component filtert.

## <a name="distinct"></a>DISTINCT

Het `DISTINCT` trefwoord elimineert duplicaten in de projectie van de query.

In dit voorbeeld worden de querywaarden voor elke achternaam uitgevoerd:

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

U ook unieke objecten projecteren. In dit geval bestaat het veld lastName niet in een van de twee documenten, dus de query retourneert een leeg object.

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

Met deze query wordt een array uitgevoerd die de givenName van elk kind bevat en duplicaten is verwijderd. Deze array wordt aliasals ChildNames en geprojecteerd in de buitenste query.

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

Query's met een geaggregeerde systeemfunctie en een subquery met `DISTINCT` worden niet ondersteund. De volgende query wordt bijvoorbeeld niet ondersteund:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="in"></a>IN

Gebruik het trefwoord IN om te controleren of een opgegeven waarde overeenkomt met een waarde in een lijst. Met de volgende query worden bijvoorbeeld `id` alle `WakefieldFamily` `AndersenFamily`gezinsitems geretourneerd waar de is of .

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

In het volgende voorbeeld worden alle items geretourneerd waarbij de status een van de opgegeven waarden is:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

De SQL API biedt ondersteuning voor [het herhalen van JSON-arrays,](sql-query-object-array.md#Iteration)met een nieuwe constructie toegevoegd via het in-trefwoord in de FROM-bron.

Als u de partitiesleutel `IN` in het filter opneemt, wordt uw query automatisch gefilterd op alleen de relevante partities.

## <a name="top"></a>Boven

Het trefwoord TOP `N` retourneert het eerste aantal queryresultaten in een niet-gedefinieerde volgorde. Gebruik TOP als best practice `ORDER BY` om de resultaten `N` te beperken tot het eerste aantal bestelde waarden. Het combineren van deze twee clausules is de enige manier om voorspelbaar aan te geven welke rijen TOP beïnvloedt.

U TOP gebruiken met een constante waarde, zoals in het volgende voorbeeld, of met een variabele waarde met behulp van geparameteriseerde query's.

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