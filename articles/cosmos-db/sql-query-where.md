---
title: WHERE-component in Azure Cosmos DB
description: Meer informatie over SQL WHERE-component voor Azure Cosmos DB
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/10/2019
ms.author: mjbrown
ms.openlocfilehash: cd5643d8be06afcd43c5bfe38d6f5e9caa6f906e
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72326645"
---
# <a name="where-clause-in-azure-cosmos-db"></a>WHERE-component in Azure Cosmos DB

De optionele component WHERE (`WHERE <filter_condition>`) geeft voor waarde (n) aan waaraan de bron-JSON-items moeten voldoen om ze in de resultaten op te vragen. Voor een JSON-item moet de opgegeven voor waarden worden geëvalueerd om `true` te kunnen worden overwogen voor het resultaat. De index laag maakt gebruik van de WHERE-component om de kleinste subset van bron items te bepalen die deel kunnen uitmaken van het resultaat.
  
## <a name="syntax"></a>Syntaxis
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argumenten

- `<filter_condition>`  
  
   Hiermee geeft u de voor waarde op waaraan moet worden voldaan om de documenten te retour neren.  
  
- `<scalar_expression>`  
  
   Expressie die de waarde vertegenwoordigt die moet worden berekend. Zie [scalaire expressies](sql-query-scalar-expressions.md) voor meer informatie.  
  

## <a name="remarks"></a>Opmerkingen
  
  Om het document als resultaat te retour neren moet een expressie worden geretourneerd die is opgegeven als filter voorwaarde, waarna resulteert in waar. Alleen Boole-waarde waar wordt voldaan aan de voor waarde, een andere waarde: niet gedefinieerd, null, onwaar, getal, matrix of object voldoet niet aan de voor waarde. 

## <a name="examples"></a>Voorbeelden

Met de volgende query worden items opgevraagd die een eigenschap `id` bevatten waarvan de waarde `AndersenFamily` is. Hiermee wordt een item uitgesloten dat geen eigenschap `id` heeft of waarvan de waarde niet overeenkomt met `AndersenFamily`.

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

U kunt ook eigenschaps verwijzingen gebruiken in query's. @No__t-0 retourneert bijvoorbeeld het JSON-item met de eigenschap `isRegistered` met een waarde die gelijk is aan `true`. Elke andere waarde, zoals `false`, `null`, `Undefined`, `<number>`, `<string>`, `<object>` of `<array>`, sluit het item uit van het resultaat. 

## <a name="next-steps"></a>Volgende stappen

- [Aan de slag](sql-query-getting-started.md)
- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [FROM-component](sql-query-from.md)