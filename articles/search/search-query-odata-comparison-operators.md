---
title: Verwijzing naar oData-vergelijkingsoperator
titleSuffix: Azure Cognitive Search
description: Syntaxis- en referentiedocumentatie voor het gebruik van OData-vergelijkingsoperatoren (eq, ne, gt, lt, ge en le) in Azure Cognitive Search-query's.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113217"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>OData-vergelijkingsoperatoren in `eq` `ne`Azure `gt` `lt`Cognitive `ge`Search - , , ,`le`

De meest elementaire bewerking in een [OData-filterexpressie](query-odata-filter-orderby-syntax.md) in Azure Cognitive Search is het vergelijken van een veld met een bepaalde waarde. Twee soorten vergelijkingen zijn mogelijk -- gelijkheidsvergelijking en bereikvergelijking. U de volgende operatoren gebruiken om een veld te vergelijken met een constante waarde:

Exploitanten van gelijkheid:

- `eq`: Testen of een veld gelijk is **aan** een constante waarde
- `ne`: Test of een veld **niet gelijk** is aan een constante waarde

Exploitanten van het bereik:

- `gt`: Test of een veld groter is **dan** een constante waarde
- `lt`: Test of een veld **minder dan** een constante waarde heeft
- `ge`: Test of een veld **groter is dan of gelijk is aan** een constante waarde
- `le`: Test of een veld **kleiner is dan of gelijk is aan** een constante waarde

U de bereikoperatoren gebruiken in combinatie met de [logische operatoren](search-query-odata-logical-operators.md) om te testen of een veld zich binnen een bepaald bereik van waarden bevindt. Zie de [voorbeelden](#examples) later in dit artikel.

> [!NOTE]
> Als u dat liever hebt, u de constante waarde aan de linkerkant van de operator en de veldnaam aan de rechterkant plaatsen. Voor range operators wordt de betekenis van de vergelijking omgekeerd. Als de constante waarde zich bijvoorbeeld `gt` aan de linkerkant bevindt, wordt getest of de constante waarde groter is dan het veld. U de vergelijkingsoperatoren ook gebruiken om `geo.distance`het resultaat van een functie te vergelijken, zoals, met een waarde. Voor Booleaanse `search.ismatch`functies zoals , `true` het `false` vergelijken van het resultaat met of is optioneel.

## <a name="syntax"></a>Syntaxis

In de volgende EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) wordt de grammatica gedefinieerd van een OData-expressie die gebruikmaakt van de vergelijkingsoperatoren.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

Er is ook een interactief syntaxisdiagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxisdiagram OData voor Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Zie [Syntaxisverwijzing oData-expressie voor Azure Cognitive Search](search-query-odata-syntax-reference.md) voor de volledige EBNF.

Er zijn twee vormen van vergelijkingsuitdrukkingen. Het enige verschil tussen hen is of de constante verschijnt aan de linker- of rechterkant van de operator. De expressie aan de andere kant van de operator moet een **variabele** of een functieaanroep zijn. Een variabele kan een veldnaam zijn, of een bereikvariabele in het geval van een [lambda-expressie.](search-query-odata-collection-operators.md)

## <a name="data-types-for-comparisons"></a>Gegevenstypen voor vergelijkingen

De gegevenstypen aan beide zijden van een vergelijkingsoperator moeten compatibel zijn. Als de linkerkant bijvoorbeeld een veld `Edm.DateTimeOffset`van type is, moet de rechterkant een datumtijdconstante zijn. Numerieke gegevenstypen zijn flexibeler. U variabelen en functies van elk numeriek type vergelijken met constanten van een ander numeriek type, met een paar beperkingen, zoals beschreven in de volgende tabel.

| Variabel of functietype | Type constante waarde | Beperkingen |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | Voor vergelijking gelden [speciale `NaN` regels voor](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | Constante wordt `Edm.Double`omgezet in , wat resulteert in een verlies van precisie voor waarden van grote omvang |
| `Edm.Double` | `Edm.Int32` | N.v.t. |
| `Edm.Int64` | `Edm.Double` | Vergelijkingen `NaN`met `-INF`, `INF` of zijn niet toegestaan |
| `Edm.Int64` | `Edm.Int64` | N.v.t. |
| `Edm.Int64` | `Edm.Int32` | Constant wordt `Edm.Int64` geconverteerd naar voor vergelijking |
| `Edm.Int32` | `Edm.Double` | Vergelijkingen `NaN`met `-INF`, `INF` of zijn niet toegestaan |
| `Edm.Int32` | `Edm.Int64` | N.v.t. |
| `Edm.Int32` | `Edm.Int32` | N.v.t. |

Voor vergelijkingen die niet zijn toegestaan, zoals het `Edm.Int64` `NaN`vergelijken van een veld van type naar , de Azure Cognitive Search REST API zal een "HTTP 400: Bad Request" fout.

> [!IMPORTANT]
> Hoewel numerieke typevergelijkingen flexibel zijn, raden we u ten zeerste aan vergelijkingen in filters te schrijven, zodat de constante waarde van hetzelfde gegevenstype is als de variabele of functie waarmee deze wordt vergeleken. Dit is vooral belangrijk bij het mengen van floating-point en integer waarden, waar impliciete conversies die precisie verliezen mogelijk zijn.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Bijzondere gevallen `null` voor en`NaN`

Bij het gebruik van vergelijkingsoperatoren is het belangrijk om te onthouden `null`dat alle niet-verzamelingsvelden in Azure Cognitive Search mogelijk . In de volgende tabel worden alle mogelijke uitkomsten `null`weergegeven voor een vergelijkingsuitdrukking waarbij beide zijden kunnen zijn:

| Operator | Resultaat wanneer alleen het veld of de variabele`null` | Resultaat wanneer alleen de constante`null` | Resultaat wanneer zowel het veld als de variabele als de constante`null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: Fout slecht verzoek | HTTP 400: Fout slecht verzoek |
| `lt` | `false` | HTTP 400: Fout slecht verzoek | HTTP 400: Fout slecht verzoek |
| `ge` | `false` | HTTP 400: Fout slecht verzoek | HTTP 400: Fout slecht verzoek |
| `le` | `false` | HTTP 400: Fout slecht verzoek | HTTP 400: Fout slecht verzoek |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

Samengevat, `null` is gelijk aan zichzelf, en is niet minder of groter dan enige andere waarde.

Als uw index velden `Edm.Double` van `NaN` het type heeft en u waarden uploadt naar die velden, moet u daar rekening mee houden bij het schrijven van filters. Azure Cognitive Search implementeert de IEEE `NaN` 754-standaard voor het verwerken van waarden en vergelijkingen met dergelijke waarden leveren niet voor de hand liggende resultaten op, zoals in de volgende tabel wordt weergegeven.

| Operator | Resultaat wanneer ten minste één operand`NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

Samengevat, `NaN` is niet gelijk aan enige waarde, met inbegrip van zichzelf.

### <a name="comparing-geo-spatial-data"></a>Geo-ruimtelijke gegevens vergelijken

U een veld `Edm.GeographyPoint` niet direct vergelijken met een constante `geo.distance` waarde, maar u de functie wel gebruiken. Deze functie retourneert `Edm.Double`een waarde van het type, zodat u deze vergelijken met een numerieke constante die u filteren op basis van de afstand tot constante geo-ruimtelijke coördinaten. Zie de [voorbeelden](#examples) hieronder.

### <a name="comparing-string-data"></a>Tekenreeksgegevens vergelijken

Tekenreeksen kunnen worden vergeleken in `eq` filters `ne` voor exacte overeenkomsten met behulp van de en operators. Deze vergelijkingen zijn hoofdlettergevoelig.

## <a name="examples"></a>Voorbeelden

Overeenkomen met `Rating` documenten waarbij het veld tussen 3 en 5 ligt, inclusief:

    Rating ge 3 and Rating le 5

Overeenkomen met `Location` documenten waarbij het veld zich op minder dan 2 kilometer van de opgegeven breedte- en lengtegraad bevindt:

    geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0

Documenten overeenkomen `LastRenovationDate` waarvan het veld groter is dan of gelijk is aan 1 januari 2015, utc om middernacht:

    LastRenovationDate ge 2015-01-01T00:00:00.000Z

Documenten overeenkomen `Details/Sku` wanneer het `null`veld niet :

    Details/Sku ne null

Overeenkomen met documenten voor hotels waar ten minste één kamer `Rooms/Type` het type "Deluxe Room" heeft, waarbij de tekenreeks van het veld precies overeenkomt met het filter:

    Rooms/any(room: room/Type eq 'Deluxe Room')

## <a name="next-steps"></a>Volgende stappen  

- [Filters in Azure Cognitive Search](search-filters.md)
- [Overzicht van OData-expressietaal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Syntaxisverwijzing oData-expressie voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoekdocumenten &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
