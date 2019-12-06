---
title: Scalaire expressies in Azure Cosmos DB SQL-query's
description: Meer informatie over de SQL-syntaxis van scalaire expressies voor Azure Cosmos DB. In dit artikel wordt ook beschreven hoe u scalaire expressies kunt combi neren in complexe expressies met behulp van Opera tors.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: f8c98915ad3b682af00492acc7bc51672ec874a8
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74870731"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Scalaire expressies in Azure Cosmos DB SQL-query's

De [component SELECT](sql-query-select.md) ondersteunt scalaire expressies. Een scalaire expressie die is een combinatie van tekens en operators die kunnen worden geëvalueerd om te verkrijgen van een enkele waarde. Voor beelden van scalaire expressies zijn: constanten, eigenschaps verwijzingen, matrix element verwijzingen, alias verwijzingen of functie aanroepen. Scalaire expressies kunnen worden gecombineerd tot complexe expressies met behulp van Opera tors.

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
  
   Hiermee geeft u een constante waarde. Zie [constanten](sql-query-constants.md) sectie voor meer informatie.  
  
- `input_alias`  
  
   Hiermee geeft u een waarde die is gedefinieerd door de `input_alias` die is geïntroduceerd in de `FROM` component.  
  Deze waarde kan niet worden gegarandeerd **niet-gedefinieerde** –**niet-gedefinieerde** waarden in de invoer worden overgeslagen.  
  
- `<scalar_expression>.property_name`  
  
   Hiermee geeft u een waarde van de eigenschap van een object. Als de eigenschap niet bestaat of als er wordt verwezen naar de eigenschap in een waarde die geen object is, wordt de expressie geëvalueerd als niet- **gedefinieerde** waarde.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Vertegenwoordigt een waarde van de eigenschap met de naam `property_name` of het matrix element met de index `array_index` van een matrix. Als de eigenschap/matrixindex niet bestaat of index van de eigenschap/array wordt verwezen op een waarde die is geen object/matrix, wordt de expressie wordt geëvalueerd als niet-gedefinieerde waarde.  
  
- `unary_operator <scalar_expression>`  
  
   Hiermee geeft u een operator die wordt toegepast op een enkele waarde. Zie [Operators](sql-query-operators.md) sectie voor meer informatie.  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   Een operator die wordt toegepast op de twee waarden vertegenwoordigt. Zie [Operators](sql-query-operators.md) sectie voor meer informatie.  
  
- `<scalar_function_expression>`  
  
   Hiermee geeft u een waarde die is gedefinieerd door een resultaat van een aanroep van de functie.  
  
- `udf_scalar_function`  
  
   De naam van de gebruiker gedefinieerde scalaire functie.  
  
- `builtin_scalar_function`  
  
   Naam van de ingebouwde scalaire functie.  
  
- `<create_object_expression>`  
  
   Hiermee geeft u een waarde die is verkregen door het maken van een nieuw object met de opgegeven eigenschappen en hun waarden.  
  
- `<create_array_expression>`  
  
   Hiermee geeft u een waarde die is verkregen door het maken van een nieuwe matrix met de opgegeven waarden als elementen  
  
- `parameter_name`  
  
   Vertegenwoordigt een waarde van de opgegeven parameternaam. Namen van parameters ze beschikken over één \@ als het eerste teken.  
  
## <a name="remarks"></a>Opmerkingen
  
  Wanneer u een ingebouwde of door de gebruiker gedefinieerde scalaire functie aanroept, moeten alle argumenten worden gedefinieerd. Als een van de argumenten niet is gedefinieerd, wordt de functie niet wordt aangeroepen en wordt het resultaat is niet gedefinieerd.  
  
  Bij het maken van een object, wordt elke eigenschap die niet-gedefinieerde waarde is toegewezen overgeslagen en niet opgenomen in het gemaakte object.  
  
  Wanneer het maken van een matrix, een elementwaarde die is toegewezen **niet-gedefinieerde** waarde wordt overgeslagen en niet opgenomen in het gemaakte object. Dit zorgt ervoor dat de volgende gedefinieerde element moet de plaatsvinden zodanig dat de gemaakte matrix wordt niet hebt overgeslagen indexen.  

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

In de volgende query is het resultaat van de scalaire expressie een Booleaanse waarde:

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