---
title: Verwijzing naar OData-vergelijkings operator
titleSuffix: Azure Cognitive Search
description: Syntaxis en referentie documentatie voor het gebruik van OData-vergelijkings operatoren (EQ, ne, gt, lt, ge en Le) in azure Cognitive Search query's.
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
ms.openlocfilehash: 62c8c93e07326e776cbe089042abc481544794bc
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113217"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>OData-vergelijkings operatoren in azure Cognitive Search-`eq`, `ne`, `gt`, `lt`, `ge`en `le`

De meest eenvoudige bewerking in een [OData-filter expressie](query-odata-filter-orderby-syntax.md) in azure Cognitive Search is het vergelijken van een veld met een bepaalde waarde. Er zijn twee soorten vergelijking mogelijk: gelijkheids vergelijking en bereik vergelijking. U kunt de volgende opera toren gebruiken om een veld te vergelijken met een constante waarde:

Gelijkheids operatoren:

- `eq`: testen of een veld **gelijk is aan** een constante waarde
- `ne`: testen of een veld **niet gelijk is aan** een constante waarde

Bereik operatoren:

- `gt`: testen of een veld **groter is dan** een constante waarde
- `lt`: testen of een veld **kleiner is dan** een constante waarde
- `ge`: testen of een veld **groter is dan of gelijk is aan** een constante waarde
- `le`: testen of een veld **kleiner is dan of gelijk is aan** een constante waarde

U kunt de bereik operatoren in combi natie met de [logische Opera tors](search-query-odata-logical-operators.md) gebruiken om te testen of een veld binnen een bepaald waardebereik ligt. Zie de [voor beelden](#examples) verderop in dit artikel.

> [!NOTE]
> Als u wilt, kunt u de constante waarde aan de linkerkant van de operator en de veld naam aan de rechter kant plaatsen. Voor bereik Opera tors wordt de betekenis van de vergelijking omgekeerd. Als de constante waarde bijvoorbeeld aan de linkerkant is, wordt `gt` getest of de constante waarde groter is dan het veld. U kunt ook de vergelijkings operators gebruiken om het resultaat van een functie, zoals `geo.distance`, te vergelijken met een waarde. Voor Booleaanse functies, zoals `search.ismatch`, wordt het resultaat vergeleken met `true` of `false` optioneel.

## <a name="syntax"></a>Syntaxis

De volgende EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definieert de grammatica van een OData-expressie die gebruikmaakt van de vergelijkings operators.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

Er is ook een interactief syntaxis diagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxis diagram van OData voor Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Zie [OData-expressie syntaxis referentie voor Azure Cognitive Search](search-query-odata-syntax-reference.md) voor de volledige ebnf.

Er zijn twee vormen van vergelijkings expressies. Het enige verschil tussen deze items is of de constante wordt weer gegeven aan de linkerkant of de rechter kant van de operator. De expressie aan de andere kant van de operator moet een **variabele** of een functie aanroep zijn. Een variabele kan een veld naam of een bereik variabele zijn in het geval van een lambda- [expressie](search-query-odata-collection-operators.md).

## <a name="data-types-for-comparisons"></a>Gegevens typen voor vergelijkingen

De gegevens typen aan beide zijden van een vergelijkings operator moeten compatibel zijn. Als de linkerkant bijvoorbeeld een veld van het type `Edm.DateTimeOffset`is, moet de rechter zijde een datum/tijd-constante zijn. Numerieke gegevens typen zijn flexibeler. U kunt variabelen en functies van elk numeriek type vergelijken met constanten van elk ander numeriek type, met enkele beperkingen, zoals beschreven in de volgende tabel.

| Variabele of functie type | Type constante waarde | Beperkingen |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | De vergelijking is onderhevig aan [speciale regels voor `NaN`](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | Constante wordt geconverteerd naar `Edm.Double`, wat resulteert in een precisie verlies voor waarden van grote grootte |
| `Edm.Double` | `Edm.Int32` | N.v.t. |
| `Edm.Int64` | `Edm.Double` | Vergelijkingen met `NaN`, `-INF`of `INF` zijn niet toegestaan |
| `Edm.Int64` | `Edm.Int64` | N.v.t. |
| `Edm.Int64` | `Edm.Int32` | Constante wordt geconverteerd naar `Edm.Int64` vóór vergelijking |
| `Edm.Int32` | `Edm.Double` | Vergelijkingen met `NaN`, `-INF`of `INF` zijn niet toegestaan |
| `Edm.Int32` | `Edm.Int64` | N.v.t. |
| `Edm.Int32` | `Edm.Int32` | N.v.t. |

Voor vergelijkingen die niet zijn toegestaan, zoals het vergelijken van een veld van het type `Edm.Int64` naar `NaN`, retourneert de Azure Cognitive Search REST API de fout ' HTTP 400: onjuiste aanvraag '.

> [!IMPORTANT]
> Hoewel numerieke type vergelijkingen flexibel zijn, raden we u aan om vergelijkingen in filters te schrijven, zodat de constante waarde van hetzelfde gegevens type is als de variabele of functie waarmee deze wordt vergeleken. Dit is vooral belang rijk bij het combi neren van drijvende-komma-en integerwaarden, waarbij impliciete conversies die nauw keurigheid kunnen verliezen, mogelijk zijn.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Speciale gevallen voor `null` en `NaN`

Wanneer vergelijkings operatoren worden gebruikt, is het belang rijk om te onthouden dat alle niet-verzamelings velden in azure Cognitive Search mogelijk kunnen worden `null`. De volgende tabel bevat alle mogelijke resultaten voor een vergelijkings expressie waarbij een van beide zijden kan worden `null`:

| Operator | Resultaat wanneer alleen het veld of de variabele wordt `null` | Resultaat wanneer alleen de constante wordt `null` | Resultaat wanneer het veld of de variabele en de constante worden `null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: fout met ongeldige aanvraag | HTTP 400: fout met ongeldige aanvraag |
| `lt` | `false` | HTTP 400: fout met ongeldige aanvraag | HTTP 400: fout met ongeldige aanvraag |
| `ge` | `false` | HTTP 400: fout met ongeldige aanvraag | HTTP 400: fout met ongeldige aanvraag |
| `le` | `false` | HTTP 400: fout met ongeldige aanvraag | HTTP 400: fout met ongeldige aanvraag |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

In samen vatting is `null` gelijk aan zichzelf en is niet kleiner of groter dan een andere waarde.

Als uw index velden van het type `Edm.Double` bevat en u `NaN` waarden naar die velden uploadt, moet u er rekening mee doen wanneer u filters schrijft. Azure Cognitive Search implementeert de IEEE 754-standaard voor het afhandelen van `NaN` waarden en vergelijkingen waarbij dergelijke waarden geen duidelijke resultaten opleveren, zoals wordt weer gegeven in de volgende tabel.

| Operator | Resultaat wanneer ten minste één operand `NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

In samen vatting is `NaN` niet gelijk aan een wille keurige waarde, met inbegrip van zichzelf.

### <a name="comparing-geo-spatial-data"></a>Geografische ruimtelijke gegevens vergelijken

Het is niet mogelijk om een veld van het type `Edm.GeographyPoint` rechtstreeks te vergelijken met een constante waarde, maar u kunt de functie `geo.distance` gebruiken. Deze functie retourneert een waarde van het type `Edm.Double`, zodat u deze kunt vergelijken met een numerieke constante om te filteren op basis van de afstand van constante geo-ruimtelijke coördinaten. Zie de onderstaande [voor beelden](#examples) .

### <a name="comparing-string-data"></a>Teken reeks gegevens vergelijken

Teken reeksen kunnen worden vergeleken in filters voor exacte overeenkomsten met behulp van de Opera tors `eq` en `ne`. Deze vergelijkingen zijn hoofdletter gevoelig.

## <a name="examples"></a>Voorbeelden

Vergelijkt documenten waarbij het `Rating` veld tussen 3 en 5 ligt:

    Rating ge 3 and Rating le 5

Vergelijkings documenten waarbij het veld `Location` kleiner is dan 2 kilo meter van de opgegeven breedte graad en lengte graad:

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

Vergelijkings documenten waarbij het veld `LastRenovationDate` groter is dan of gelijk is aan 1 januari, 2015, Midnight UTC:

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

Documenten die overeenkomen met het `Details/Sku` veld niet `null`:

    Details/Sku ne null

Vergelijkings documenten voor hotels waarbij ten minste één kamer het type ' Deluxe room ' heeft, waarbij de teken reeks van het `Rooms/Type` veld precies overeenkomt met het filter:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>Volgende stappen  

- [Filters in azure Cognitive Search](search-filters.md)
- [Overzicht van de OData-expressie taal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Naslag informatie voor de syntaxis van OData-expressies voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoeken in &#40;documenten Azure Cognitive Search rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
