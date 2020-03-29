---
title: Scalaire expressies in Azure Cosmos DB SQL-query's
description: Meer informatie over de SQL-syntaxis van scalaire expressie voor Azure Cosmos DB. In dit artikel wordt ook beschreven hoe scalaire expressies kunnen worden gecombineerd in complexe expressies met behulp van operatoren.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: f8c98915ad3b682af00492acc7bc51672ec874a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74870731"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Scalaire expressies in Azure Cosmos DB SQL-query's

De [SELECT-component](sql-query-select.md) ondersteunt scalaire expressies. Een scalaire expressie is een combinatie van symbolen en operatoren die kunnen worden geëvalueerd om één waarde te verkrijgen. Voorbeelden van scalaire expressies zijn: constanten, eigenschapsverwijzingen, verwijzingen naar arrayelementen, aliasverwijzingen of functieaanroepen. Scalaire expressies kunnen worden gecombineerd tot complexe expressies met behulp van operatoren.

## <a name="syntax"></a>Syntaxis
  
```sql  
<scalar_expression> ::=  
       <constant>
     | input_alias
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>
     | <create_array_expression>  
     | (<scalar_expression>)
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```

## <a name="arguments"></a>Argumenten
  
- `<constant>`  
  
   Geeft een constante waarde weer. Zie [sectie Constanten](sql-query-constants.md) voor meer informatie.  
  
- `input_alias`  
  
   Vertegenwoordigt een waarde gedefinieerd `input_alias` door de `FROM` geïntroduceerde in de clausule.  
  Deze waarde is gegarandeerd niet **ongedefinieerd** –**ongedefinieerde** waarden in de invoer worden overgeslagen.  
  
- `<scalar_expression>.property_name`  
  
   Hiermee vertegenwoordigt u een waarde van de eigenschap van een object. Als de eigenschap niet bestaat of eigenschap wordt verwezen naar een waarde, die geen object is, evalueert de expressie naar **ongedefinieerde** waarde.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Hiermee vertegenwoordigt u een `property_name` waarde van de `array_index` eigenschap met naam of arrayelement met index van een array. Als de eigenschap/array-index niet bestaat of als de eigenschap/array-index wordt verwezen naar een waarde die geen object/array is, wordt de expressie geëvalueerd op niet-gedefinieerde waarde.  
  
- `unary_operator <scalar_expression>`  
  
   Vertegenwoordigt een operator die op één waarde wordt toegepast. Zie [Sectie Operators](sql-query-operators.md) voor meer informatie.  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   Vertegenwoordigt een operator die wordt toegepast op twee waarden. Zie [Sectie Operators](sql-query-operators.md) voor meer informatie.  
  
- `<scalar_function_expression>`  
  
   Vertegenwoordigt een waarde die wordt gedefinieerd door een resultaat van een functieaanroep.  
  
- `udf_scalar_function`  
  
   Naam van de door de gebruiker gedefinieerde scalaire functie.  
  
- `builtin_scalar_function`  
  
   Naam van de ingebouwde scalaire functie.  
  
- `<create_object_expression>`  
  
   Hiermee vertegenwoordigt u een waarde die wordt verkregen door een nieuw object te maken met opgegeven eigenschappen en hun waarden.  
  
- `<create_array_expression>`  
  
   Vertegenwoordigt een waarde die wordt verkregen door een nieuwe array te maken met opgegeven waarden als elementen  
  
- `parameter_name`  
  
   Hiermee vertegenwoordigt u een waarde van de opgegeven parameternaam. Parameternamen moeten een \@ enkel als eerste teken hebben.  
  
## <a name="remarks"></a>Opmerkingen
  
  Wanneer u een ingebouwde of door de gebruiker gedefinieerde scalaire functie aanroept, moeten alle argumenten worden gedefinieerd. Als een van de argumenten niet is gedefinieerd, wordt de functie niet aangeroepen en wordt het resultaat niet gedefinieerd.  
  
  Bij het maken van een object wordt elke eigenschap die ongedefinieerde waarde is toegewezen, overgeslagen en niet opgenomen in het gemaakte object.  
  
  Bij het maken van een array wordt elke elementwaarde die **ongedefinieerde** waarde is toegewezen, overgeslagen en niet opgenomen in het gemaakte object. Hierdoor neemt het volgende gedefinieerde element zijn plaats in zodanig dat de gemaakte array geen overgeslagen indexen heeft.  

## <a name="examples"></a>Voorbeelden

```sql
    SELECT ((2 + 11 % 7)-2)/3
```

U ziet deze uitvoer:

```json
    [{
      "$1": 1.33333
    }]
```

In de volgende query is het resultaat van de scalaire expressie een Booleaan:

```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

U ziet deze uitvoer:

```json
    [
      {
        "AreFromSameCityState": false
      },
      {
        "AreFromSameCityState": true
      }
    ]
```

## <a name="next-steps"></a>Volgende stappen

- [Inleiding tot Azure Cosmos DB](introduction.md)
- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Subquery's](sql-query-subquery.md)