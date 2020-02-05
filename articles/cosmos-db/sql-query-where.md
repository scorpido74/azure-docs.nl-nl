---
title: WHERE-component in Azure Cosmos DB
description: Meer informatie over SQL WHERE-component voor Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: tisande
ms.openlocfilehash: 31653b598f0f3a79bf7f9c09231b1d111f167a16
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76982226"
---
# <a name="where-clause-in-azure-cosmos-db"></a>WHERE-component in Azure Cosmos DB

De optionele component WHERE (`WHERE <filter_condition>`) geeft voor waarde (n) aan waaraan de bron-JSON-items moeten voldoen om ze in de resultaten op te vragen. Een JSON-item moet de opgegeven voor waarden evalueren om te `true` dat voor het resultaat wordt overwogen. De index laag maakt gebruik van de WHERE-component om de kleinste subset van bron items te bepalen die deel kunnen uitmaken van het resultaat.
  
## <a name="syntax"></a>Syntaxis
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argumenten

- `<filter_condition>`  
  
   Hiermee geeft u de voorwaarde moet worden voldaan voor de documenten die moeten worden geretourneerd.  
  
- `<scalar_expression>`  
  
   Expressie voor de waarde die moet worden berekend. Zie [scalaire expressies](sql-query-scalar-expressions.md) voor meer informatie.  
  

## <a name="remarks"></a>Opmerkingen
  
  Opdat het document dat moet worden geretourneerd van een expressie die is opgegeven als filter moet voorwaarde resulteren in waar. Alleen Booleaanse waarde ' True ', voldoen aan de voorwaarde, een andere waarde: niet-gedefinieerde, null, ingesteld op false, getal, matrix of Object zal voldoen niet aan de voorwaarde. 

## <a name="examples"></a>Voorbeelden

Met de volgende query worden items opgevraagd die een `id` eigenschap bevatten waarvan de waarde `AndersenFamily`is. Hiermee wordt een item uitgesloten dat geen `id`-eigenschap heeft of waarvan de waarde niet overeenkomt met `AndersenFamily`.

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

De resultaten zijn:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>Scalaire expressies in de component WHERE

Het vorige voorbeeld bevatte een eenvoudige gelijkheidsquery. De SQL-API ondersteunt ook verschillende [scalaire expressies](sql-query-scalar-expressions.md). Binaire en unaire expressies worden het meest gebruikt. Eigenschapsverwijzingen vanaf het JSON-bronobject zijn ook geldige expressies.

U kunt de volgende ondersteunde binaire Opera tors gebruiken:  

|**Operatortype**  | **Waarden** |
|---------|---------|
|Rekenkundig | +, -, *, /, % |
|Bitsgewijs    | \|, &, ^, <<, >>, >>> (opvulling met nullen shift-rechts) |
|Logisch    | EN, OF, NIET      |
|Vergelijking | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|Tekenreeks     |  \|\| (samenvoegen) |

De volgende query's gebruiken binaire Opera tors:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

U kunt ook de unaire Opera Tors +,-, ~ en niet in query's gebruiken, zoals wordt weer gegeven in de volgende voor beelden:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

U kunt ook eigenschaps verwijzingen gebruiken in query's. `SELECT * FROM Families f WHERE f.isRegistered` retourneert bijvoorbeeld het JSON-item met de eigenschap `isRegistered` met een waarde die gelijk is aan `true`. Elke andere waarde, zoals `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>`of `<array>`, sluit het item uit van het resultaat.

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag](sql-query-getting-started.md)
- [IN sleutel woord](sql-query-keywords.md#in)
- [FROM-component](sql-query-from.md)