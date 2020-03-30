---
title: WAAR clausule in Azure Cosmos DB
description: Meer informatie over SQL WHERE-clausule voor Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: tisande
ms.openlocfilehash: 483a0533eafc81ef8698d260a753062ae074f6d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898787"
---
# <a name="where-clause-in-azure-cosmos-db"></a>WAAR clausule in Azure Cosmos DB

De optionele WHERE-component (`WHERE <filter_condition>`) geeft voorwaarde(en) op waaraan de BRON JSON-items moeten voldoen om de query in de resultaten op te nemen. Een JSON-artikel moet de `true` gespecificeerde voorwaarden evalueren die voor het resultaat in aanmerking moeten worden genomen. De indexlaag gebruikt de WHERE-component om de kleinste subset van bronitems te bepalen die deel kunnen uitmaken van het resultaat.
  
## <a name="syntax"></a>Syntaxis
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argumenten

- `<filter_condition>`  
  
   Hiermee geeft u de voorwaarde op waaraan moet worden voldaan voor de documenten die moeten worden geretourneerd.  
  
- `<scalar_expression>`  
  
   Expressie die de te berekenen waarde vertegenwoordigt. Zie [Scalar-expressies](sql-query-scalar-expressions.md) voor meer informatie.  
  
## <a name="remarks"></a>Opmerkingen
  
  Als het document kan worden geretourneerd, moet een expressie die is opgegeven als filtervoorwaarde worden geëvalueerd naar true. Alleen Booleaanse waarde `true` voldoet aan de voorwaarde, elke andere waarde: ongedefinieerd, null, false, Number, Array of Object zal niet voldoen aan de voorwaarde.

  Als u uw partitiesleutel `WHERE` in de component opneemt als onderdeel van een gelijkheidsfilter, wordt uw query automatisch gefilterd op alleen de relevante partities.

## <a name="examples"></a>Voorbeelden

In de volgende query `id` worden items `AndersenFamily`aangevraagd die een eigenschap bevatten waarvan de waarde is . Het sluit elk item uit `id` dat geen eigenschap heeft `AndersenFamily`of waarvan de waarde niet overeenkomt.

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

U ziet deze uitvoer:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>Scalar uitdrukkingen in de WHERE-clausule

Het vorige voorbeeld bevatte een eenvoudige gelijkheidsquery. De SQL API ondersteunt ook verschillende [scalaire expressies.](sql-query-scalar-expressions.md) Binaire en unaire expressies worden het meest gebruikt. Eigenschapsverwijzingen vanaf het JSON-bronobject zijn ook geldige expressies.

U de volgende ondersteunde binaire operatoren gebruiken:  

|**Operatortype**  | **Waarden** |
|---------|---------|
|Rekenkundig | +,-,*,/,% |
|Bitsgewijs    | \|, &, ^, <<, >>, >>> (opvulling met nullen shift-rechts) |
|Logisch    | EN, OF, NIET      |
|Vergelijking | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|Tekenreeks     |  \|\| (samenvoegen) |

In de volgende query's worden binaire operatoren gebruikt:

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

U ook de unary operators +,-, ~en NOT in query's gebruiken, zoals in de volgende voorbeelden wordt weergegeven:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

U ook eigenschapsverwijzingen gebruiken in query's. Als `SELECT * FROM Families f WHERE f.isRegistered` u bijvoorbeeld het JSON-item `isRegistered` retourneert `true`dat de eigenschap bevat met een waarde die gelijk is aan . Elke andere waarde, `false` `null`zoals `Undefined` `<number>`, `<string>` `<object>`, `<array>`, , of , sluit het artikel uit van het resultaat.

## <a name="next-steps"></a>Volgende stappen

- [Slag](sql-query-getting-started.md)
- [Sleutelwoord IN](sql-query-keywords.md#in)
- [VAN clausule](sql-query-from.md)