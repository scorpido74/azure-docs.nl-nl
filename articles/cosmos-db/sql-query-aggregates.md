---
title: Statistische functies in Azure Cosmos DB
description: Meer informatie over de syntaxis van de statistische SQL-functie, typen statistische functies die door Azure Cosmos DB worden ondersteund.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: tisande
ms.openlocfilehash: df9700dd51c8915ff28c34cf0a29c2f5e48baa44
ms.sourcegitcommit: 668b3480cb637c53534642adcee95d687578769a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/07/2020
ms.locfileid: "78897830"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Statistische functies in Azure Cosmos DB

Statistische functies voeren een berekening uit op een reeks waarden in de component SELECT en retour neren een enkele waarde. Met de volgende query wordt bijvoorbeeld het aantal items in de `Families` container geretourneerd:

## <a name="examples"></a>Voorbeelden

```sql
    SELECT COUNT(1)
    FROM Families f
```

De resultaten zijn:

```json
    [{
        "$1": 2
    }]
```

U kunt ook alleen de scalaire waarde van het aggregatie retour neren met behulp van het sleutel woord VALUE. De volgende query retourneert bijvoorbeeld het aantal waarden als één getal:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

De resultaten zijn:

```json
    [ 2 ]
```

U kunt ook aggregaties met filters combi neren. De volgende query retourneert bijvoorbeeld het aantal items met de adres status `WA`.

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

De resultaten zijn:

```json
    [ 1 ]
```

## <a name="types-of-aggregate-functions"></a>Typen statistische functies

De SQL-API biedt ondersteuning voor de volgende statistische functies. SUM en AVG functioneren op numerieke waarden en aantal, MIN en MAX werk voor getallen, teken reeksen, Booleaanse waarden en nullen.

| Functie | Beschrijving |
|-------|-------------|
| COUNT | Retourneert het aantal items in de expressie. |
| SUM   | Retourneert de som van alle waarden in de expressie. |
| MIN   | Retourneert de minimumwaarde in de expressie. |
| MAX   | Retourneert de maximumwaarde in de expressie. |
| AVG   | Retourneert het gemiddelde van de waarden in de expressie. |

U kunt ook aggregatie over de resultaten van een matrix herhaling.

> [!NOTE]
> In de Data Explorer van de Azure Portal kunnen aggregatie query's gedeeltelijke resultaten op slechts één query pagina verzamelen. De SDK produceert één cumulatieve waarde op alle pagina's. Als u aggregatie query's wilt uitvoeren met behulp van code, hebt u .NET SDK 1.12.0, .NET Core SDK 1.1.0 of Java SDK 1.9.5 of hoger nodig.

## <a name="next-steps"></a>Volgende stappen

- [Inleiding tot Azure Cosmos DB](introduction.md)
- [Systeem functies](sql-query-system-functions.md)
- [Door de gebruiker gedefinieerde functies](sql-query-udfs.md)