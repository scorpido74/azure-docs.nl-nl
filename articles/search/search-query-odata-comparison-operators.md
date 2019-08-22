---
title: Referentie vergelijkings operator van OData-Azure Search
description: OData-vergelijkings operatoren, EQ, ne, gt, lt, ge en Le, in Azure Search query's.
ms.date: 06/13/2019
services: search
ms.service: search
ms.topic: conceptual
author: brjohnstmsft
ms.author: brjohnst
manager: nitinme
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
ms.openlocfilehash: a8bd8b05fd874e05e5e59042d461f4a4286c81e4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648064"
---
# <a name="odata-comparison-operators-in-azure-search---eq-ne-gt-lt-ge-and-le"></a>OData-vergelijkings operatoren `eq`in `ne`Azure Search `gt`-, `ge`,, `lt`, en`le`

De meest eenvoudige bewerking in een [OData-filter expressie](query-odata-filter-orderby-syntax.md) in azure Search is het vergelijken van een veld met een bepaalde waarde. Er zijn twee soorten vergelijking mogelijk: gelijkheids vergelijking en bereik vergelijking. U kunt de volgende opera toren gebruiken om een veld te vergelijken met een constante waarde:

Gelijkheids operatoren:

- `eq`: Testen of een veld **gelijk is aan** een constante waarde
- `ne`: Testen of een veld **niet gelijk is aan** een constante waarde

Bereik operatoren:

- `gt`: Testen of een veld **groter is dan** een constante waarde
- `lt`: Testen of een veld **kleiner is dan** een constante waarde
- `ge`: Testen of een veld **groter is dan of gelijk is aan** een constante waarde
- `le`: Testen of een veld **kleiner is dan of gelijk is aan** een constante waarde

U kunt de bereik operatoren in combi natie met de [logische Opera tors](search-query-odata-logical-operators.md) gebruiken om te testen of een veld binnen een bepaald waardebereik ligt. Zie de [voor beelden](#examples) verderop in dit artikel.

> [!NOTE]
> Als u wilt, kunt u de constante waarde aan de linkerkant van de operator en de veld naam aan de rechter kant plaatsen. Voor bereik Opera tors wordt de betekenis van de vergelijking omgekeerd. Als de constante waarde bijvoorbeeld aan de linkerkant is, `gt` wordt getest of de constante waarde groter is dan het veld. U kunt ook de vergelijkings operators gebruiken om het resultaat van een functie, `geo.distance`zoals, met een waarde te vergelijken. Voor Booleaanse functies zoals `search.ismatch`, het vergelijken van het resultaat naar `false` `true` of is optioneel.

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
> [Syntaxis diagram van OData voor Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Zie de [syntaxis van de OData-expressie voor Azure Search](search-query-odata-syntax-reference.md) voor de volledige ebnf.

Er zijn twee vormen van vergelijkings expressies. Het enige verschil tussen deze items is of de constante wordt weer gegeven aan de linkerkant of de rechter kant van de operator. De expressie aan de andere kant van de operator moet een **variabele** of een functie aanroep zijn. Een variabele kan een veld naam of een bereik variabele zijn in het geval van een lambda- [expressie](search-query-odata-collection-operators.md).

## <a name="data-types-for-comparisons"></a>Gegevens typen voor vergelijkingen

De gegevens typen aan beide zijden van een vergelijkings operator moeten compatibel zijn. Als de linkerkant bijvoorbeeld een veld van het type `Edm.DateTimeOffset`is, moet de rechter kant een datum-tijd-constante zijn. Numerieke gegevens typen zijn flexibeler. U kunt variabelen en functies van elk numeriek type vergelijken met constanten van elk ander numeriek type, met enkele beperkingen, zoals beschreven in de volgende tabel.

| Variabele of functie type | Type constante waarde | Beperkingen |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | De vergelijking is onderhevig aan [speciale `NaN` regels voor](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | Constante wordt geconverteerd naar `Edm.Double`, wat resulteert in een precisie verlies voor waarden van grote grootte |
| `Edm.Double` | `Edm.Int32` | N.v.t. |
| `Edm.Int64` | `Edm.Double` | Vergelijkingen naar `NaN`, `-INF`of `INF` zijn niet toegestaan |
| `Edm.Int64` | `Edm.Int64` | N.v.t. |
| `Edm.Int64` | `Edm.Int32` | Constante wordt geconverteerd naar `Edm.Int64` vóór vergelijking |
| `Edm.Int32` | `Edm.Double` | Vergelijkingen naar `NaN`, `-INF`of `INF` zijn niet toegestaan |
| `Edm.Int32` | `Edm.Int64` | N.v.t. |
| `Edm.Int32` | `Edm.Int32` | N.v.t. |

Voor vergelijkingen die niet zijn toegestaan, zoals het vergelijken van een veld van het `Edm.Int64` type `NaN`naar, retourneert de Azure Search rest API een ' http 400: Fout met ongeldige aanvraag.

> [!IMPORTANT]
> Hoewel numerieke type vergelijkingen flexibel zijn, raden we u aan om vergelijkingen in filters te schrijven, zodat de constante waarde van hetzelfde gegevens type is als de variabele of functie waarmee deze wordt vergeleken. Dit is vooral belang rijk bij het combi neren van drijvende-komma-en integerwaarden, waarbij impliciete conversies die nauw keurigheid kunnen verliezen, mogelijk zijn.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Speciale gevallen voor `null` en`NaN`

Wanneer vergelijkings operatoren worden gebruikt, is het belang rijk te weten dat alle niet-verzamelings velden `null`in azure Search mogelijk zijn. De volgende tabel bevat alle mogelijke resultaten voor een vergelijkings expressie waarbij beide zijden kunnen zijn `null`:

| Operator | Resultaat wanneer alleen het veld of de variabele is`null` | Resultaat wanneer alleen de constante is`null` | Resultaat wanneer het veld of de variabele en de constante worden`null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: Fout met ongeldige aanvraag | HTTP 400: Fout met ongeldige aanvraag |
| `lt` | `false` | HTTP 400: Fout met ongeldige aanvraag | HTTP 400: Fout met ongeldige aanvraag |
| `ge` | `false` | HTTP 400: Fout met ongeldige aanvraag | HTTP 400: Fout met ongeldige aanvraag |
| `le` | `false` | HTTP 400: Fout met ongeldige aanvraag | HTTP 400: Fout met ongeldige aanvraag |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

In samen vatting `null` is alleen gelijk aan zichzelf en is niet kleiner dan of groter dan een andere waarde.

Als uw index velden van het type `Edm.Double` bevat en u `NaN` waarden naar die velden uploadt, moet u er rekening mee doen wanneer u filters schrijft. Azure Search implementeert de IEEE 754-standaard voor `NaN` de afhandelings waarden en vergelijkingen met dergelijke waarden veroorzaken niet-duidelijke resultaten, zoals wordt weer gegeven in de volgende tabel.

| Operator | Resultaat wanneer ten minste één operand is`NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

Samen vatting `NaN` is niet gelijk aan een wille keurige waarde, met inbegrip van zichzelf.

### <a name="comparing-geo-spatial-data"></a>Geografische ruimtelijke gegevens vergelijken

Het is niet mogelijk om een veld van `Edm.GeographyPoint` het type rechtstreeks te vergelijken met een constante waarde, `geo.distance` maar u kunt de functie gebruiken. Deze functie retourneert een waarde van het `Edm.Double`type, zodat u deze kunt vergelijken met een numerieke constante die moet worden gefilterd op basis van de afstand van constante geo-ruimtelijke coördinaten. Zie de onderstaande [voor beelden](#examples) .

### <a name="comparing-string-data"></a>Teken reeks gegevens vergelijken

Teken reeksen kunnen worden vergeleken in filters voor exacte overeenkomsten met `eq` behulp van de `ne` Opera tors. Deze vergelijkingen zijn hoofdletter gevoelig.

## <a name="examples"></a>Voorbeelden

Overeenkomende documenten waarbij het `Rating` veld tussen 3 en 5 ligt:

    Rating ge 3 and Rating le 5

Documenten vergelijken waarbij het `Location` veld kleiner is dan 2 kilo meters van de opgegeven breedte graad en lengte graad:

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

Documenten vergelijken waarbij het `LastRenovationDate` veld groter is dan of gelijk is aan 1 januari, 2015, Midnight UTC:

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

Documenten die overeenkomen `Details/Sku` met het veld `null`:

    Details/Sku ne null

Vergelijkings documenten voor hotels waarbij ten minste één kamer het type ' Deluxe room ' heeft, waarbij de `Rooms/Type` teken reeks van het veld precies overeenkomt met het filter:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>Volgende stappen  

- [Filters in Azure Search](search-filters.md)
- [Overzicht van de OData-expressie taal voor Azure Search](query-odata-filter-orderby-syntax.md)
- [Verwijzing naar de syntaxis van de OData-expressie voor Azure Search](search-query-odata-syntax-reference.md)
- [Zoeken naar &#40;documenten Azure Search service rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
