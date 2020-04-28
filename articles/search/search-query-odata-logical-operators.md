---
title: Verwijzing naar logische operator van OData
titleSuffix: Azure Cognitive Search
description: Syntaxis en referentie documentatie voor het gebruik van logische OData-Opera Tors, en, of en niet in azure Cognitive Search query's.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74113180"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Logische OData-Opera tors in azure `and`Cognitive Search `or`-,,`not`

[OData-filter expressies](query-odata-filter-orderby-syntax.md) in azure Cognitive Search zijn Booleaanse expressies die de `true` waarde `false`van of bepalen. U kunt een complex filter schrijven door een reeks [eenvoudiger filters](search-query-odata-comparison-operators.md) te schrijven en deze samen te stellen met behulp van de logische Opera tors van [Boole algebra](https://en.wikipedia.org/wiki/Boolean_algebra):

- `and`: Een binaire operator waarmee wordt geëvalueerd `true` of de linker-en rechter-sub-expressies worden `true`geëvalueerd.
- `or`: Een binaire operator die evalueert `true` of een van de linker-of rechter-sub-expressies resulteert in. `true`
- `not`: Een unaire operator die evalueert `true` of de sub-expressie resulteert in `false`, en omgekeerd.

Met deze combi natie van de [verzamelings operators `any` en `all` ](search-query-odata-collection-operators.md)kunt u filters maken die zeer complexe zoek criteria kunnen uitdrukken.

## <a name="syntax"></a>Syntaxis

De volgende EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definieert de grammatica van een OData-expressie die gebruikmaakt van de logische Opera tors.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

Er is ook een interactief syntaxis diagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxis diagram van OData voor Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Zie [OData-expressie syntaxis referentie voor Azure Cognitive Search](search-query-odata-syntax-reference.md) voor de volledige ebnf.

Er zijn twee soorten logische`and`/`or`expressies: binary (), waar er twee subexpressies en Unair (`not`) zijn, waarbij er slechts één wordt. De subexpressies kunnen Booleaanse expressies van elk soort zijn:

- Velden of bereik variabelen van het type`Edm.Boolean`
- Functies die waarden van het type `Edm.Boolean`retour neren, `geo.intersects` zoals of`search.ismatch`
- [Vergelijkings expressies](search-query-odata-comparison-operators.md), zoals`rating gt 4`
- [Verzamelings expressies](search-query-odata-collection-operators.md), zoals`Rooms/any(room: room/Type eq 'Deluxe Room')`
- De letterlijke Boole `true` - `false`waarden of.
- Andere logische expressies die zijn `and`gemaakt `or`met, `not`en.

> [!IMPORTANT]
> Er zijn enkele situaties waarin niet alle soorten Subexpressies kunnen worden gebruikt met `and` / `or`, met name binnen lambda-expressies. Zie [OData-verzamelings operatoren in Azure Cognitive Search](search-query-odata-collection-operators.md#limitations) voor meer informatie.

### <a name="logical-operators-and-null"></a>Logische Opera tors en`null`

De meeste Booleaanse expressies zoals functies en vergelijkingen kunnen geen waarden `null` produceren en de logische Opera tors kunnen niet rechtstreeks op de `null` letterlijke waarde worden toegepast ( `x and null` bijvoorbeeld is niet toegestaan). Boole-velden kunnen echter wel `null`zijn, dus u moet weten hoe de `and`Opera Tors, `or`en `not` zich gedragen in de aanwezigheid van Null. Dit wordt in de volgende tabel samenvatten, `b` waarbij een veld van het `Edm.Boolean`type is:

| Expressie | Resultaat wanneer `b` is`null` |
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

Wanneer een Boole- `b` veld in een filter expressie wordt weer gegeven, wordt gereageerd alsof het is geschreven `b eq true` `b` `false`. als dat het `null`geval is, wordt de expressie geëvalueerd. Dit `not b` werkt op dezelfde manier `not (b eq true)`als, zodat deze kan worden `true`geëvalueerd. Op deze manier werken `null` velden hetzelfde als `false`. Dit is consistent met de manier waarop ze zich gedragen wanneer ze worden gecombineerd `and` met `or`andere expressies die gebruikmaken van en, zoals wordt weer gegeven in de bovenstaande tabel. Ondanks dit geldt dat er nog steeds `false` een`b eq false`directe vergelijking naar () `false`wordt geëvalueerd. Met andere woorden, `null` is niet gelijk aan `false`, hoewel het zich gedraagt als in Boole-expressies.

## <a name="examples"></a>Voorbeelden

Overeenkomende documenten waarbij het `rating` veld tussen 3 en 5 ligt:

    rating ge 3 and rating le 5

Overeenkomende documenten waarbij alle elementen van het `ratings` veld kleiner zijn dan 3 of groter dan 5:

    ratings/all(r: r lt 3 or r gt 5)

Vergelijkt documenten `location` waarbij het veld binnen de gegeven veelhoek ligt en het document niet de term ' openbaar ' bevat.

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

Documenten voor hotels zoeken in Vancouver, Canada waar zich een luxe ruimte bevindt met een basis tempo van minder dan 160:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Volgende stappen  

- [Filters in azure Cognitive Search](search-filters.md)
- [Overzicht van de OData-expressie taal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Naslag informatie voor de syntaxis van OData-expressies voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoeken naar documenten &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
