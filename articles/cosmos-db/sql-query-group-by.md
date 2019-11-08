---
title: GROUP BY-component in Azure Cosmos DB
description: Meer informatie over de GROUP BY-component voor Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: tisande
ms.openlocfilehash: e41e81457421bfe27e3c0313fc06e39e6df4cdce
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819111"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>GROUP BY-component in Azure Cosmos DB

De GROUP BY-component deelt de resultaten van de query op basis van de waarden van een of meer opgegeven eigenschappen.

> [!NOTE]
> Azure Cosmos DB biedt momenteel ondersteuning voor GROUP BY in .NET SDK 3,3 en hoger, evenals java script SDK 3,4 en hoger.
> Ondersteuning voor andere taal-SDK is momenteel niet beschikbaar, maar is gepland.

## <a name="syntax"></a>Syntaxis

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>Argumenten

- `<scalar_expression_list>`

   Hiermee geeft u de expressies op die worden gebruikt voor het delen van query resultaten.

- `<scalar_expression>`
  
   Elke scalaire expressie is toegestaan, met uitzonde ring van scalaire subquery's en scalaire aggregaties. Elke scalaire expressie moet ten minste één eigenschaps verwijzing bevatten. Er is geen limiet voor het aantal afzonderlijke expressies of de kardinaliteit van elke expressie.

## <a name="remarks"></a>Opmerkingen
  
  Wanneer een query gebruikmaakt van een GROUP BY-component, kan de SELECT-component alleen de subset van eigenschappen en systeem functies bevatten die zijn opgenomen in de component GROUP BY. Een uitzonde ring bestaat uit [statistische systeem functies](sql-query-aggregates.md)die kunnen worden weer gegeven in de component SELECT zonder te zijn opgenomen in de component Group by. U kunt ook letterlijke waarden in de component SELECT altijd insluiten.

  De component GROUP BY moet na de component SELECT, FROM en WHERE en vóór de component OFFSET LIMIT staan. U kunt GROUP BY momenteel niet gebruiken met een ORDER BY-component, maar dit is gepland.

  De GROUP BY-component staat niet toe dat een van de volgende:
  
- Alias eigenschappen of aliasing systeem functies (aliasing is nog steeds toegestaan in de component SELECT)
- Subquery's
- Statistische systeem functies (deze zijn alleen toegestaan in de component SELECT)

## <a name="examples"></a>Voorbeelden

Deze voor beelden gebruiken de voedings gegevens die beschikbaar zijn via de [Azure Cosmos DB query Playground](https://www.documentdb.com/sql/demo).

Dit is bijvoorbeeld een query die het totale aantal items in elke foodGroup retourneert:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

Sommige resultaten zijn (bovenste sleutel woord wordt gebruikt om de resultaten te beperken):

```json
[{
  "foodGroup": "Fast Foods",
  "foodGroupCount": 371
},
{
  "foodGroup": "Finfish and Shellfish Products",
  "foodGroupCount": 267
},
{
  "foodGroup": "Meals, Entrees, and Side Dishes",
  "foodGroupCount": 113
},
{
  "foodGroup": "Sausages and Luncheon Meats",
  "foodGroupCount": 244
}]
```

Deze query heeft twee expressies die worden gebruikt om de resultaten te delen:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

Enkele resultaten zijn:

```json
[{
  "version": 1,
  "foodGroup": "Nut and Seed Products",
  "foodGroupCount": 133
},
{
  "version": 1,
  "foodGroup": "Finfish and Shellfish Products",
  "foodGroupCount": 267
},
{
  "version": 1,
  "foodGroup": "Fast Foods",
  "foodGroupCount": 371
},
{
  "version": 1,
  "foodGroup": "Sausages and Luncheon Meats",
  "foodGroupCount": 244
}]
```

Deze query heeft een systeem functie in de GROUP BY-component:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

Enkele resultaten zijn:

```json
[{
  "foodGroupCount": 371,
  "upperFoodGroup": "FAST FOODS"
},
{
  "foodGroupCount": 267,
  "upperFoodGroup": "FINFISH AND SHELLFISH PRODUCTS"
},
{
  "foodGroupCount": 389,
  "upperFoodGroup": "LEGUMES AND LEGUME PRODUCTS"
},
{
  "foodGroupCount": 113,
  "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
}]
```

Deze query gebruikt zowel tref woorden als systeem functies in de expressie van de item eigenschap:

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

De resultaten zijn:

```json
[{
  "correctVersion": true,
  "containsOrangeTag": false,
  "foodGroupCount": 8608
},
{
  "correctVersion": true,
  "containsOrangeTag": true,
  "foodGroupCount": 10
}]
```

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag](sql-query-getting-started.md)
- [SELECT-component](sql-query-select.md)
- [Statistische functies](sql-query-aggregates.md)
