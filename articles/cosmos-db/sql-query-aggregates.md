---
title: Samengevoegde functies in Azure Cosmos DB
description: Meer informatie over de syntaxis van de SQL-samengevoegde functie, typen geaggregeerde functies die worden ondersteund door Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/16/2020
ms.author: tisande
ms.openlocfilehash: 24acd1e9c13320244ff4c27abd13abeda6f70b2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79464458"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Samengevoegde functies in Azure Cosmos DB

Geaggregeerde functies voeren een berekening uit `SELECT` op een set waarden in de clausule en geven één waarde terug. Met de volgende query wordt bijvoorbeeld het `Families` aantal items in de container geretourneerd:

## <a name="examples"></a>Voorbeelden

```sql
    SELECT COUNT(1)
    FROM Families f
```

U ziet deze uitvoer:

```json
    [{
        "$1": 2
    }]
```

U ook alleen de scalaire waarde van het aggregaat retourneren met het trefwoord WAARDE. De volgende query retourneert bijvoorbeeld het aantal waarden als één getal:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

U ziet deze uitvoer:

```json
    [ 2 ]
```

U aggregaties ook combineren met filters. Met de volgende query wordt bijvoorbeeld het aantal `WA`items met de adresstatus van .

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

U ziet deze uitvoer:

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>Typen geaggregeerde functies

De SQL API ondersteunt de volgende aggregaatfuncties. `SUM`en `AVG` werken op numerieke `COUNT` `MIN`waarden, `MAX` en , en werken aan getallen, tekenreeksen, Booleaanse en nullen.

| Functie | Beschrijving |
|-------|-------------|
| COUNT | Retourneert het aantal items in de expressie. |
| SUM   | Retourneert de som van alle waarden in de expressie. |
| MIN   | Retourneert de minimumwaarde in de expressie. |
| MAX   | Retourneert de maximumwaarde in de expressie. |
| AVG   | Retourneert het gemiddelde van de waarden in de expressie. |

U ook de resultaten van een array-iteratie samenvoegen.

> [!NOTE]
> In de Gegevensverkenner van de Azure-portal kunnen aggregatiequery's gedeeltelijke resultaten samenvoegen over slechts één querypagina. De SDK produceert één cumulatieve waarde op alle pagina's. Als u aggregatiequery's wilt uitvoeren met code, hebt u .NET SDK 1.12.0, .NET Core SDK 1.1.0 of Java SDK 1.9.5 of hoger nodig.

## <a name="remarks"></a>Opmerkingen

Deze geaggregeerde systeemfuncties zullen profiteren van een [bereikindex](index-policy.md#includeexclude-strategy). Als u verwacht `COUNT`een `SUM` `MIN`, `MAX`, `AVG` , , of op een eigenschap te doen , moet u [het relevante pad opnemen in het indexeringsbeleid](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Volgende stappen

- [Inleiding tot Azure Cosmos DB](introduction.md)
- [Systeemfuncties](sql-query-system-functions.md)
- [Door de gebruiker gedefinieerde functies](sql-query-udfs.md)