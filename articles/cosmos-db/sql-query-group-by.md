---
title: GROEP BY-component in Azure Cosmos DB
description: Meer informatie over de GROEP BY-component voor Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: tisande
ms.openlocfilehash: 8a3cbbafc066747b62f79934f2cd12301aa1ba17
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261598"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>GROEP BY-component in Azure Cosmos DB

De component GROEPEREN door verdeelt de resultaten van de query op basis van de waarden van een of meer opgegeven eigenschappen.

> [!NOTE]
> Azure Cosmos DB ondersteunt momenteel GROUP BY in .NET SDK 3.3 en hoger, evenals JavaScript SDK 3.4 en hoger.
> Ondersteuning voor andere talen SDK's is momenteel niet beschikbaar, maar is gepland.

## <a name="syntax"></a>Syntaxis

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>Argumenten

- `<scalar_expression_list>`

   Hiermee geeft u de expressies op die worden gebruikt om queryresultaten te verdelen.

- `<scalar_expression>`
  
   Elke scalaire expressie is toegestaan, behalve voor scalaire subquery's en scalaire aggregaten. Elke scalaire expressie moet ten minste één eigenschapsverwijzing bevatten. Er is geen limiet aan het aantal afzonderlijke expressies of de kardinaliteit van elke expressie.

## <a name="remarks"></a>Opmerkingen
  
  Wanneer een query een GROEP BY-component gebruikt, kan de SELECT-component alleen de subset van eigenschappen en systeemfuncties bevatten die zijn opgenomen in de GROEP BY-component. Een uitzondering is [geaggregeerde systeemfuncties](sql-query-aggregates.md), die in de SELECT-clausule kunnen worden opgenomen zonder in de GROUP BY-clausule te zijn opgenomen. U ook altijd letterlijke waarden opnemen in de SELECT-component.

  De GROUP BY-clausule moet na de SELECT-, FROM- en WHERE-clausule en vóór de COMPENSATIE-LIMIETclausule zijn. U GROUP BY momenteel niet gebruiken met een ORDER BY-clausule, maar dit is gepland.

  De GROUP BY-clausule staat geen van de volgende gegevens toe:
  
- Eigenschappen van aliasing of aliasingsysteemfuncties (aliasing is nog steeds toegestaan binnen de SELECT-component)
- Subquery's
- Geaggregeerde systeemfuncties (deze zijn alleen toegestaan in de SELECT-component)

Query's met een geaggregeerde systeemfunctie en een subquery met `GROUP BY` worden niet ondersteund. De volgende query wordt bijvoorbeeld niet ondersteund:

```sql
SELECT COUNT(UniqueLastNames) FROM (SELECT AVG(f.age) FROM f GROUP BY f.lastName) AS UniqueLastNames
```

## <a name="examples"></a>Voorbeelden

In deze voorbeelden wordt gebruik gemaakt van de voedingsgegevensset die beschikbaar is via de [Azure Cosmos DB Query Playground.](https://www.documentdb.com/sql/demo)

Hier vindt u bijvoorbeeld een query die het totale aantal artikelen in elke foodGroup retourneert:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

Sommige resultaten zijn (TOP trefwoord wordt gebruikt om de resultaten te beperken):

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

Deze query heeft twee expressies die worden gebruikt om resultaten te verdelen:

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

Deze query heeft een systeemfunctie in de GROEP BY-component:

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

Deze query gebruikt zowel trefwoorden als systeemfuncties in de eigenschapitemexpressie:

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

U ziet deze uitvoer:

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
- [Samengevoegde functies](sql-query-aggregates.md)
