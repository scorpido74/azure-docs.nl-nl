---
title: SQL-constanten in Azure Cosmos DB
description: Meer informatie over SQL-constanten in Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/31/2019
ms.author: tisande
ms.openlocfilehash: 41cafeb76b7b9481a78ffcff179676d0aa7bf6fd
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002034"
---
# <a name="azure-cosmos-db-sql-query-constants"></a>Azure Cosmos DB SQL-query constanten  

 Een constante, ook wel bekend als een letterlijke tekenreeks of een scalaire waarde is een symbool waarmee een specifieke waarde vertegenwoordigt. De indeling van een constante is afhankelijk van het gegevenstype van de waarde vertegenwoordigt.  
  
 **Ondersteunde scalaire gegevenstypen:**  
  
|**Type**|**Volgorde van waarden**|  
|-|-|  
|**Undefined**|Enkele waarde: **niet gedefinieerd**|  
|**Null**|Enkele waarde: **null**|  
|**Boolean**|Waarden: **false**, **waar**.|  
|**Number**|Een getal met dubbele precisie getal met drijvende komma, IEEE 754 standard.|  
|**String**|Een reeks van nul of meer Unicode-tekens. Tekenreeksen moeten tussen enkele of dubbele aanhalingstekens worden geplaatst.|  
|**Array**|Een reeks van nul of meer elementen. Elk element kan een waarde van elk scalair gegevens type zijn, behalve niet **gedefinieerd**.|  
|**Object**|Een niet-geordende set van nul of meer naam/waarde-paren. Naam is een Unicode-tekenreeks, de waarde kan zijn van elk gegevenstype scalaire, behalve **Undefined**.|  
  
## <a name="bk_syntax"></a>Syntaxis
  
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
  
##  <a name="bk_arguments"></a>Opmerkingen
  
* `<undefined_constant>; Undefined`  
  
  Geeft een niet-gedefinieerde waarde van het type niet gedefinieerd.  
  
* `<null_constant>; null`  
  
  Hiermee geeft u **null** waarde van het type **Null**.  
  
* `<boolean_constant>`  
  
  Constante van het type Boolean-waarde vertegenwoordigt.  
  
* `false`  
  
  Hiermee geeft u **false** waarde van het type Booleaanse waarde.  
  
* `true`  
  
  Hiermee geeft u **waar** waarde van het type Booleaanse waarde.  
  
* `<number_constant>`  
  
  Hiermee geeft u een constante zijn.  
  
* `decimal_literal`  
  
  Decimale letterlijke waarden zijn getallen weergegeven met behulp van de decimale notatie of wetenschappelijke notatie.  
  
* `hexadecimal_literal`  
  
  Hexadecimale letterlijke waarden zijn getallen weergegeven met behulp van voorvoegsel '0 x' gevolgd door een of meer hexadecimale cijfers.  
  
* `<string_constant>`  
  
  Hiermee geeft u een constante van het type tekenreeks.  
  
* `string _literal`  
  
  Letterlijke tekenreeks zijn vertegenwoordigd door een reeks van nul of meer Unicode-tekens of escapereeksen Unicode-tekenreeksen. Letterlijke tekenreeks zijn ingesloten in enkele aanhalingstekens (apostrof: ') of dubbele aanhalingstekens (aanhalingsteken: ").  
  
  Volgende escapereeksen zijn toegestaan:  
  
|**Escape-reeks**|**Beschrijving**|**Unicode-teken**|  
|-|-|-|  
|\\'|enkel aanhalingsteken (')|U+0027|  
|\\"|dubbel aanhalingsteken (")|U+0022|  
|\\\ |omgekeerde schuine streep\\()|U + 005C|  
|\\/|schuine streep (/)|U+002F|  
|\b|BACKSPACE|U+0008|  
|\f|formulier-feed|U + 000C|  
|\n|regelinvoer|U + 000A|  
|\r|regelterugloop|U + 000D|  
|\t|tab|U+0009|  
|\uXXXX|Een Unicode-teken wordt gedefinieerd door 4 hexadecimale cijfers.|U + XXXX|  

## <a name="next-steps"></a>Volgende stappen

- [.NET-voorbeelden voor Azure Cosmos DB](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Document gegevens model leren](modeling-data.md)
