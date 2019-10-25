---
title: Verwijzing naar logische operator van OData
titleSuffix: Azure Cognitive Search
description: Logische OData-Opera Tors, en, of, en niet, in azure Cognitive Search query's.
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
ms.openlocfilehash: 4e016047d66e49f17c08d4b92a1c865f4b63e39b
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793312"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Logische OData-Opera tors in azure Cognitive Search-`and`, `or`, `not`

[OData-filter expressies](query-odata-filter-orderby-syntax.md) in azure Cognitive Search zijn Booleaanse expressies die `true` of `false`evalueren. U kunt een complex filter schrijven door een reeks [eenvoudiger filters](search-query-odata-comparison-operators.md) te schrijven en deze samen te stellen met behulp van de logische Opera tors van [Boole algebra](https://en.wikipedia.org/wiki/Boolean_algebra):

- `and`: een binaire operator die `true` evalueert als de linker-en rechter-sub-expressies `true`worden geëvalueerd.
- `or`: een binaire operator die `true` evalueert als een van de linker-of rechter-sub-expressies wordt geëvalueerd als `true`.
- `not`: een unaire operator die als resultaat `true` als de bijbehorende subexpressie van `false`en vice versa wordt geëvalueerd.

Met deze combi natie van de [verzamelings operators `any` en `all`](search-query-odata-collection-operators.md)kunt u filters maken die zeer complexe zoek criteria kunnen uitdrukken.

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

Er zijn twee soorten logische expressies: binary (`and`/`or`), waarbij er twee subexpressies en Unair (`not`) zijn, waarbij er slechts één wordt. De subexpressies kunnen Booleaanse expressies van elk soort zijn:

- Velden of bereik variabelen van het type `Edm.Boolean`
- Functies die waarden van het type `Edm.Boolean`retour neren, zoals `geo.intersects` of `search.ismatch`
- [Vergelijkings expressies](search-query-odata-comparison-operators.md), zoals `rating gt 4`
- [Verzamelings expressies](search-query-odata-collection-operators.md), zoals `Rooms/any(room: room/Type eq 'Deluxe Room')`
- De letterlijke Booleaanse waarden `true` of `false`.
- Andere logische expressies die zijn gemaakt met behulp van `and`, `or`en `not`.

> [!IMPORTANT]
> Er zijn enkele situaties waarin niet alle soorten Subexpressies kunnen worden gebruikt met `and`/`or`, met name in lambda-expressies. Zie [OData-verzamelings operatoren in Azure Cognitive Search](search-query-odata-collection-operators.md#limitations) voor meer informatie.

### <a name="logical-operators-and-null"></a>Logische Opera tors en `null`

De meeste Booleaanse expressies zoals functies en vergelijkingen kunnen geen `null` waarden produceren en de logische Opera tors kunnen niet rechtstreeks worden toegepast op de `null` letterlijke teken reeks (`x and null` bijvoorbeeld niet is toegestaan). Boole-velden kunnen echter wel worden `null`, dus u moet weten hoe de Opera tors `and`, `or`en `not` zich gedragen in de aanwezigheid van Null. Dit wordt in de volgende tabel samenvatten, waarbij `b` een veld van het type `Edm.Boolean`is:

| Expressie | Resultaat als `b` is `null` |
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

Wanneer een Boole-veld `b` wordt weer gegeven in een filter expressie, gedraagt het zich alsof het is geschreven `b eq true`, dus als `b` wordt `null`, wordt de expressie geëvalueerd naar `false`. Op dezelfde manier wordt `not b` gedraagt als `not (b eq true)`, zodat deze kan worden geëvalueerd `true`. Op deze manier werken `null` velden hetzelfde als `false`. Dit is consistent met de manier waarop ze zich gedragen wanneer ze worden gecombineerd met andere expressies die gebruikmaken van `and` en `or`, zoals wordt weer gegeven in de bovenstaande tabel. Ondanks dit geldt dat een rechtstreekse vergelijking met `false` (`b eq false`) nog steeds tot `false`wordt geëvalueerd. Met andere woorden, `null` is niet gelijk aan `false`, zelfs als het zich gedraagt als in Boole-expressies.

## <a name="examples"></a>Voorbeelden

Vergelijkt documenten waarbij het `rating` veld tussen 3 en 5 ligt:

    rating ge 3 and rating le 5

Vergelijkt documenten waarbij alle elementen van het veld `ratings` kleiner zijn dan 3 of groter dan 5:

    ratings/all(r: r lt 3 or r gt 5)

Vergelijkt documenten waarin het `location` veld binnen de opgegeven veelhoek ligt en het document niet de term ' openbaar ' bevat.

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')

Documenten voor hotels zoeken in Vancouver, Canada waar zich een luxe ruimte bevindt met een basis tempo van minder dan 160:

    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)

## <a name="next-steps"></a>Volgende stappen  

- [Filters in azure Cognitive Search](search-filters.md)
- [Overzicht van de OData-expressie taal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Naslag informatie voor de syntaxis van OData-expressies voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoeken in &#40;documenten Azure Cognitive Search rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
