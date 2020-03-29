---
title: SQL-constanten in Azure Cosmos DB
description: Meer informatie over hoe de SQL-queryconstanten in Azure Cosmos DB worden gebruikt om een specifieke gegevenswaarde weer te geven
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: cca62c358037dbe99fd16746ee081b1540161df2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873417"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>Azure Cosmos DB SQL-queryconstanten  

 Een constante, ook wel een letterlijke of een scalaire waarde genoemd, is een symbool dat een specifieke gegevenswaarde vertegenwoordigt. De indeling van een constante is afhankelijk van het gegevenstype van de waarde die wordt weergegeven.  
  
 **Ondersteunde scalaire gegevenstypen:**  
  
|**Type**|**Volgorde waarden**|  
|-|-|  
|**Undefined**|Enkele waarde: **niet gedefinieerd**|  
|**Null**|Enkelvoudige waarde: **null**|  
|**Booleaanse**|Waarden: **vals,** **waar**.|  
|**Nummer**|Een dubbel-precisie floating-point nummer, IEEE 754 standaard.|  
|**Tekenreeks**|Een reeks van nul of meer Unicode-tekens. Tekenreeksen moeten worden ingesloten in enkele of dubbele aanhalingstekens.|  
|**Array**|Een reeks van nul of meer elementen. Elk element kan een waarde zijn van elk scalaire gegevenstype, behalve **Ongedefinieerd.**|  
|**Object**|Een ongeordende set van nul of meer naam/waardeparen. Naam is een Unicode-tekenreeks, de waarde kan van elk scalaire gegevenstype zijn, behalve **Niet gedefinieerd**.|  
  
## <a name="syntax"></a><a name="bk_syntax"></a>Syntaxis
  
```sql  
<constant> ::=  
   <undefined_constant>  
     | <null_constant>   
     | <boolean_constant>   
     | <number_constant>   
     | <string_constant>   
     | <array_constant>   
     | <object_constant>   
  
<undefined_constant> ::= undefined  
  
<null_constant> ::= null  
  
<boolean_constant> ::= false | true  
  
<number_constant> ::= decimal_literal | hexadecimal_literal  
  
<string_constant> ::= string_literal  
  
<array_constant> ::=  
    '[' [<constant>][,...n] ']'  
  
<object_constant> ::=   
   '{' [{property_name | "property_name"} : <constant>][,...n] '}'  
  
```  
  
##  <a name="arguments"></a><a name="bk_arguments"></a>Argumenten
  
* `<undefined_constant>; Undefined`  
  
  Vertegenwoordigt de niet-gedefinieerde waarde van het type Ongedefinieerd.  
  
* `<null_constant>; null`  
  
  Vertegenwoordigt **null-waarde** van type **Null**.  
  
* `<boolean_constant>`  
  
  Vertegenwoordigt constante van het type Boolean.  
  
* `false`  
  
  Vertegenwoordigt **valse** waarde van het type Boolean.  
  
* `true`  
  
  Vertegenwoordigt **de werkelijke** waarde van het type Boolean.  
  
* `<number_constant>`  
  
  Vertegenwoordigt een constante.  
  
* `decimal_literal`  
  
  Decimale literals zijn getallen die worden weergegeven met behulp van decimale notatie of wetenschappelijke notatie.  
  
* `hexadecimal_literal`  
  
  Hexadecimale literals zijn getallen die worden weergegeven met behulp van voorvoegsel '0x' gevolgd door een of meer hexadecimale cijfers.  
  
* `<string_constant>`  
  
  Hiermee vertegenwoordigt u een constante van de tekenreeks.  
  
* `string _literal`  
  
  String literalals zijn Unicode-tekenreeksen die worden vertegenwoordigd door een reeks van nul of meer Unicode-tekens of vluchtreeksen. String literalals zijn ingesloten in enkele aanhalingstekens (apostrof: ' ) of dubbele aanhalingstekens (aanhalingsteken: ").  
  
  Volgende ontsnappingssequenties zijn toegestaan:  
  
|**Ontsnappingsvolgorde**|**Beschrijving**|**Unicode-teken**|  
|-|-|-|  
|\\'|apostrof (')|U+0027|  
|\\"|aanhalingsteken (")|U+0022|  
|\\\ |omgekeerde solidus\\( )|U+005C|  
|\\/|solidus (/)|U+002F|  
|\b|Backspace|U+0008|  
|\f|formulierfeed|U+000C|  
|\n|lijnfeed|U+000A|  
|\r|Regelterugloop|U+000D|  
|\t|Tab|U+0009|  
|\uXXXX|Een Unicode-teken gedefinieerd door 4 hexadecimale cijfers.|U+XXXX|  

## <a name="next-steps"></a>Volgende stappen

- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Documentgegevens modelleren](modeling-data.md)
