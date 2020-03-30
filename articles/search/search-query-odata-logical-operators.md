---
title: OData logische operator referentie
titleSuffix: Azure Cognitive Search
description: Syntaxis- en referentiedocumentatie voor het gebruik van logische operatoren van OData en, en, en en niet, in Azure Cognitive Search-query's.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 2d3952f7d2adc26892cbebcd962f2ea25b86de7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113180"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>OData logische operatoren in `and` `or`Azure Cognitive Search - ,`not`

[OData-filterexpressies](query-odata-filter-orderby-syntax.md) in Azure Cognitive Search `true` zijn `false`Booleaanse expressies die evalueren op of . U een complex filter schrijven door een reeks [eenvoudigere filters te](search-query-odata-comparison-operators.md) schrijven en ze samen te stellen met behulp van de logische operatoren uit [booleaanse algebra:](https://en.wikipedia.org/wiki/Boolean_algebra)

- `and`: Een binaire operator `true` die evalueert als zowel de `true`linker- als de rechter subexpressies evalueren op .
- `or`: Een binaire operator `true` die evalueert als een van de linker- of rechtersubexpressies evalueert op `true`.
- `not`: Een unary operator `true` die evalueert of de `false`subexpressie evalueert, en vice versa.

Deze, samen met de [collectie operators `any` en `all` ](search-query-odata-collection-operators.md), u filters die zeer complexe zoekcriteria kan uitdrukken construeren.

## <a name="syntax"></a>Syntaxis

De volgende EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definieert de grammatica van een OData-expressie die de logische operatoren gebruikt.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

Er is ook een interactief syntaxisdiagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxisdiagram OData voor Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Zie [Syntaxisverwijzing oData-expressie voor Azure Cognitive Search](search-query-odata-syntax-reference.md) voor de volledige EBNF.

Er zijn twee vormen van`and`/`or`logische expressies: binaire ( ), waar`not`er twee sub-expressies, en unary ( ), waar er slechts een. De subexpressies kunnen Booleaanse uitdrukkingen van welke aard dan ook zijn:

- Velden of bereikvariabelen van het type`Edm.Boolean`
- Functies die waarden `Edm.Boolean`van het `geo.intersects` type retourneren, zoals of`search.ismatch`
- [Vergelijkingsuitdrukkingen](search-query-odata-comparison-operators.md), zoals`rating gt 4`
- [Verzamelingsexpressies](search-query-odata-collection-operators.md), zoals`Rooms/any(room: room/Type eq 'Deluxe Room')`
- De Booleaanse `true` `false`literals of.
- Andere logische expressies `and` `or`die `not`zijn opgebouwd met , en .

> [!IMPORTANT]
> Er zijn enkele situaties waarin niet alle soorten `and` / `or`sub-expressie kunnen worden gebruikt met , met name binnen lambda uitdrukkingen. Zie [OData-verzamelingsoperatoren in Azure Cognitive Search](search-query-odata-collection-operators.md#limitations) voor meer informatie.

### <a name="logical-operators-and-null"></a>Logische operatoren en`null`

De meeste Booleaanse expressies zoals `null` functies en vergelijkingen kunnen geen `null` waarden produceren en `x and null` de logische operatoren kunnen niet rechtstreeks op de letterlijke worden toegepast (is bijvoorbeeld niet toegestaan). Booleaanse velden kunnen `null`echter worden , dus u `and` `or`moet `not` zich bewust zijn van hoe de , , en operators zich gedragen in de aanwezigheid van null. Dit wordt samengevat in de `b` volgende tabel, `Edm.Boolean`waar een veld van het type is:

| Expressie | Resultaat `b` wanneer is`null` |
| --- | --- |
| `b` | `false` |
| `not b` | `true` |
| `b eq true` | `false` |
| `b eq false` | `false` |
| `b eq null` | `true` |
| `b ne true` | `true` |
| `b ne false` | `true` |
| `b ne null` | `false` |
| `b and true` | `false` |
| `b and false` | `false` |
| `b or true` | `true` |
| `b or false` | `false` |

Wanneer een `b` Booleaanse veld op zichzelf wordt weergegeven in een filterexpressie, gedraagt het zich alsof het is geschreven, `b eq true`dus als `b` dat zo is, `null`evalueert de expressie tot `false`. Op dezelfde `not b` manier gedraagt zich als `not (b eq true)` `true`, dus het evalueert om . Op deze `null` manier gedragen velden `false`zich hetzelfde als . Dit komt overeen met hoe ze zich `and` `or`gedragen in combinatie met andere expressies die gebruik maken en , zoals in de bovenstaande tabel. Desondanks zal een `false` directe`b eq false`vergelijking met `false`( ) nog steeds evalueren om . Met andere `null` woorden, is `false`niet gelijk aan , ook al gedraagt het zich als het in Booleaanse uitdrukkingen.

## <a name="examples"></a>Voorbeelden

Overeenkomen met `rating` documenten waarbij het veld tussen 3 en 5 ligt, inclusief:

    rating ge 3 and rating le 5

Overeenkomen met documenten waarin `ratings` alle elementen van het veld kleiner zijn dan 3 of meer dan 5:

    ratings/all(r: r lt 3 or r gt 5)

Documenten overeenkomen `location` waar het veld zich binnen de opgegeven veelhoek bevindt en het document bevat niet de term 'openbaar'.

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

Match documenten voor hotels in Vancouver, Canada, waar er een deluxe kamer met een basistarief van minder dan 160:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Volgende stappen  

- [Filters in Azure Cognitive Search](search-filters.md)
- [Overzicht van OData-expressietaal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Syntaxisverwijzing oData-expressie voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoekdocumenten &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
