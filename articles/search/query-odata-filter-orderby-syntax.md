---
title: OData taaloverzicht
titleSuffix: Azure Cognitive Search
description: OData-taaloverzicht voor filters, selecteren en order-by voor Azure Cognitive Search-query's.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
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
ms.openlocfilehash: f3a1be435e297ab4a9ba7f8bfbd5f3ce3451d8a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77153873"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-cognitive-search"></a>OData-taaloverzicht `$filter` `$orderby`voor `$select` , en in Azure Cognitive Search

Azure Cognitive Search ondersteunt een subset van de syntaxis van de OData-expressie voor **$filter,** **$orderby**en **$select** expressies. Filterexpressies worden geëvalueerd tijdens queryparsing, waardoor zoeken wordt beperkt tot specifieke velden of wedstrijdcriteria worden toegevoegd die worden gebruikt tijdens indexscans. Volgorde-door-expressies worden toegepast als een stap na bewerking boven een resultaatset om de geretourneerde documenten te sorteren. Selecteer expressies bepalen welke documentvelden zijn opgenomen in de resultatenset. De syntaxis van deze expressies onderscheidt zich van de [eenvoudige](query-simple-syntax.md) of [volledige](query-lucene-syntax.md) querysyntaxis die wordt gebruikt in de **zoekparameter,** hoewel er enige overlap is in de syntaxis voor verwijzingen naar velden.

In dit artikel vindt u een overzicht van de OData-expressietaal die wordt gebruikt in filters, order-by- en selectieexpressies. De taal wordt gepresenteerd "bottom-up", te beginnen met de meest elementaire elementen en voortbouwend op hen. De syntaxis op het hoogste niveau voor elke parameter wordt beschreven in een apart artikel:

- [$filter syntaxis](search-query-odata-filter.md)
- [$orderby syntaxis](search-query-odata-orderby.md)
- [$select syntaxis](search-query-odata-select.md)

OData-expressies variëren van eenvoudig tot zeer complex, maar ze delen allemaal gemeenschappelijke elementen. De meest elementaire onderdelen van een OData-expressie in Azure Cognitive Search zijn:

- **Veldpaden**, die verwijzen naar specifieke velden van uw index.
- **Constanten**, die letterlijke waarden van een bepaald gegevenstype zijn.

> [!NOTE]
> Terminologie in Azure Cognitive Search verschilt op een aantal manieren van de [OData-standaard.](https://www.odata.org/documentation/) Wat we een **veld** in Azure Cognitive Search noemen, wordt een **eigenschap** in OData genoemd, en op dezelfde manier voor **veldpad** versus **eigenschappenpad.** Een **index** met **documenten** in Azure Cognitive Search wordt in OData meer in het algemeen aangeduid als een **entiteitset** met **entiteiten**. De azure cognitive search-terminologie wordt tijdens deze verwijzing gebruikt.

## <a name="field-paths"></a>Veldpaden

In de volgende EBNF ([Extended Backus-Naur Form)](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)wordt de grammatica van veldpaden gedefinieerd.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

Er is ook een interactief syntaxisdiagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxisdiagram OData voor Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> Zie [Syntaxisverwijzing oData-expressie voor Azure Cognitive Search](search-query-odata-syntax-reference.md) voor de volledige EBNF.

Een veldpad bestaat uit een of meer **id's** die worden gescheiden door schuine strepen. Elke id is een reeks tekens die moet beginnen met een ASCII-letter of underscore en alleen ASCII-letters, cijfers of underscores bevatten. De letters kunnen hoofdletters zijn.

Een id kan verwijzen naar de naam van een veld of naar een`any` `all` **bereikvariabele** in de context van een [verzamelingsexpressie](search-query-odata-collection-operators.md) (of ) in een filter. Een bereikvariabele is als een lusvariabele die het huidige element van de verzameling vertegenwoordigt. Voor complexe verzamelingen vertegenwoordigt deze variabele een object, daarom u veldpaden gebruiken om naar subvelden van de variabele te verwijzen. Dit is analoog aan puntnotatie in veel programmeertalen.

Voorbeelden van veldpaden worden weergegeven in de volgende tabel:

| Veldpad | Beschrijving |
| --- | --- |
| `HotelName` | Verwijst naar een veld op het hoogste niveau van de index |
| `Address/City` | Verwijst naar `City` het subveld van een complex veld in de index; `Address` is van `Edm.ComplexType` type in dit voorbeeld |
| `Rooms/Type` | Verwijst naar `Type` het subveld van een complex verzamelingsveld in de index; `Rooms` is van `Collection(Edm.ComplexType)` type in dit voorbeeld |
| `Stores/Address/Country` | Verwijst naar `Country` het subveld `Address` van het subveld van een complex verzamelingsveld in de index; `Stores` is van `Collection(Edm.ComplexType)` `Address` type en `Edm.ComplexType` is van type in dit voorbeeld |
| `room/Type` | Verwijst naar `Type` het subveld `room` van de bereikvariabele, bijvoorbeeld in de filterexpressie`Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | Verwijst naar `Country` het subveld `Address` van het `store` subveld van de bereikvariabele, bijvoorbeeld in de filterexpressie`Stores/any(store: store/Address/Country eq 'Canada')` |

De betekenis van een veldpad verschilt afhankelijk van de context. In filters verwijst een veldpad naar de waarde van *één instantie* van een veld in het huidige document. In andere contexten, zoals **$orderby,** **$select**of in [veldzoekopdrachten in de volledige syntaxis van Lucene,](query-lucene-syntax.md#bkmk_fields)verwijst een veldpad naar het veld zelf. Dit verschil heeft enkele gevolgen voor de manier waarop u veldpaden in filters gebruikt.

Denk aan `Address/City`het veldpad . In een filter verwijst dit naar één stad voor het huidige document, zoals "San Francisco". In tegenstelling, `Rooms/Type` verwijst `Type` naar het sub-veld voor veel kamers (zoals "standaard" voor de eerste kamer, "deluxe" voor de tweede kamer, en ga zo maar door). Aangezien `Rooms/Type` niet verwijst naar een *enkele instantie* `Type`van het subveld, kan het niet direct worden gebruikt in een filter. In plaats daarvan, om te filteren op kamertype, zou je een [lambda-expressie](search-query-odata-collection-operators.md) gebruiken met een bereikvariabele, zoals deze:

    Rooms/any(room: room/Type eq 'deluxe')

In dit voorbeeld wordt `room` de `room/Type` bereikvariabele weergegeven in het veldpad. Op die `room/Type` manier verwijst naar het type van de huidige ruimte in het huidige document. Dit is een enkele `Type` instantie van het subveld, zodat het direct in het filter kan worden gebruikt.

### <a name="using-field-paths"></a>Veldpaden gebruiken

Veldpaden worden gebruikt in veel parameters van de [Azure Cognitive Search REST API's](https://docs.microsoft.com/rest/api/searchservice/). In de volgende tabel worden alle plaatsen weergegeven waar ze kunnen worden gebruikt, plus eventuele beperkingen op het gebruik ervan:

| API | Parameternaam | Beperkingen |
| --- | --- | --- |
| [Index maken](https://docs.microsoft.com/rest/api/searchservice/create-index) of [bijwerken](https://docs.microsoft.com/rest/api/searchservice/update-index) | `suggesters/sourceFields` | Geen |
| [Index maken](https://docs.microsoft.com/rest/api/searchservice/create-index) of [bijwerken](https://docs.microsoft.com/rest/api/searchservice/update-index) | `scoringProfiles/text/weights` | Kan alleen verwijzen naar **doorzoekbare** velden |
| [Index maken](https://docs.microsoft.com/rest/api/searchservice/create-index) of [bijwerken](https://docs.microsoft.com/rest/api/searchservice/update-index) | `scoringProfiles/functions/fieldName` | Kan alleen verwijzen naar **filterbare** velden |
| [Zoeken](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `search`wanneer `queryType` is`full` | Kan alleen verwijzen naar **doorzoekbare** velden |
| [Zoeken](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `facet` | Kan alleen verwijzen naar **facetable** velden |
| [Zoeken](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `highlight` | Kan alleen verwijzen naar **doorzoekbare** velden |
| [Zoeken](https://docs.microsoft.com/rest/api/searchservice/search-documents) | `searchFields` | Kan alleen verwijzen naar **doorzoekbare** velden |
| [Voorstellen](https://docs.microsoft.com/rest/api/searchservice/suggestions) en [automatisch aanvullen](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `searchFields` | Kan alleen verwijzen naar velden die deel uitmaken van een [suggestie](index-add-suggesters.md) |
| [Zoeken](https://docs.microsoft.com/rest/api/searchservice/search-documents), [Voorstellen](https://docs.microsoft.com/rest/api/searchservice/suggestions)en [Automatisch aanvullen](https://docs.microsoft.com/rest/api/searchservice/autocomplete) | `$filter` | Kan alleen verwijzen naar **filterbare** velden |
| [Zoeken](https://docs.microsoft.com/rest/api/searchservice/search-documents) en [voorstellen](https://docs.microsoft.com/rest/api/searchservice/suggestions) | `$orderby` | Kan alleen verwijzen naar **sorteerbare** velden |
| [Zoeken](https://docs.microsoft.com/rest/api/searchservice/search-documents), [Voorstellen](https://docs.microsoft.com/rest/api/searchservice/suggestions)en [opzoeken](https://docs.microsoft.com/rest/api/searchservice/lookup-document) | `$select` | Kan alleen verwijzen naar **opvraagbare** velden |

## <a name="constants"></a>Constanten

Constanten in OData zijn letterlijke waarden van een bepaald [Entiteitsgegevensmodel](https://docs.microsoft.com/dotnet/framework/data/adonet/entity-data-model) (EDM) type. Zie [Ondersteunde gegevenstypen](https://docs.microsoft.com/rest/api/searchservice/supported-data-types) voor een lijst met ondersteunde typen in Azure Cognitive Search. Constanten van verzamelingstypen worden niet ondersteund.

In de volgende tabel worden voorbeelden weergegeven van constanten voor elk van de gegevenstypen die worden ondersteund door Azure Cognitive Search:

| Gegevenstype | Voorbeeldconstanten |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

### <a name="escaping-special-characters-in-string-constants"></a>Ontsnappen aan speciale tekens in tekenreeksconstanten

Tekenreeksconstanten in OData worden afgebakend door enkele aanhalingstekens. Als u een query moet maken met een tekenreeksconstante die zelf enkele aanhalingstekens zou kunnen bevatten, u aan de ingesloten aanhalingstekens ontsnappen door ze te verdubbelen.

Bijvoorbeeld, een zin met een niet-geformatteerde apostrof als "Alice's auto" `'Alice''s car'`zou worden vertegenwoordigd in OData als de string constante .

> [!IMPORTANT]
> Bij het bouwen van filters programmatisch, is het belangrijk om te onthouden om string constanten die afkomstig zijn van input van de gebruiker te ontsnappen. Dit is om de mogelijkheid van [injectieaanvallen](https://wikipedia.org/wiki/SQL_injection)te beperken, vooral bij het gebruik van filters om [beveiligingstrimmen](search-security-trimming-for-azure-search.md)te implementeren.

### <a name="constants-syntax"></a>Syntaxis van Constanten

De volgende EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definieert de grammatica voor de meeste constanten in de bovenstaande tabel. De grammatica voor georuimtelijke typen is te vinden in [oData-georuimtelijke functies in Azure Cognitive Search](search-query-odata-geo-spatial-functions.md).

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'
```

Er is ook een interactief syntaxisdiagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxisdiagram OData voor Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> Zie [Syntaxisverwijzing oData-expressie voor Azure Cognitive Search](search-query-odata-syntax-reference.md) voor de volledige EBNF.

## <a name="building-expressions-from-field-paths-and-constants"></a>Expressies bouwen van veldpaden en constanten

Veldpaden en constanten zijn het meest elementaire onderdeel van een OData-expressie, maar ze zijn zelf al volledig expressies. In feite is de **$select** parameter in Azure Cognitive Search niets anders dan een door komma's gescheiden lijst met veldpaden en **$orderby** is niet veel ingewikkelder dan **$select.** Als u toevallig een veld `Edm.Boolean` van type in uw index hebt, u zelfs een filter schrijven dat niets anders is dan het pad van dat veld. De `true` constanten `false` en zijn eveneens geldige filters.

Echter, de meeste van de tijd die je nodig hebt meer complexe uitdrukkingen die verwijzen naar meer dan een veld en constant. Deze expressies zijn op verschillende manieren gebouwd, afhankelijk van de parameter.

In de volgende EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) wordt de grammatica voor de **parameters $filter,** **$orderby**en **$select** gedefinieerd. Deze zijn opgebouwd uit eenvoudigere expressies die verwijzen naar veldpaden en constanten:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

Er is ook een interactief syntaxisdiagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxisdiagram OData voor Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> Zie [Syntaxisverwijzing oData-expressie voor Azure Cognitive Search](search-query-odata-syntax-reference.md) voor de volledige EBNF.

De **$orderby-** en **$select** parameters zijn beide door komma's gescheiden lijsten met eenvoudigere expressies. De **parameter $filter** is een Booleaanse expressie die bestaat uit eenvoudigere subexpressies. Deze subexpressies worden gecombineerd met behulp van logische operatoren zoals [ `and` `or`, en `not` ](search-query-odata-logical-operators.md), vergelijkingsoperatoren zoals [ `eq`, `lt`, `gt`enzovoort](search-query-odata-comparison-operators.md), en verzamelingenoperatoren zoals [ `any` en `all` ](search-query-odata-collection-operators.md).

De **$filter,** **$orderby**en **$select** parameters worden in de volgende artikelen nader onderzocht:

- [OData $filter syntaxis in Azure Cognitive Search](search-query-odata-filter.md)
- [OData $orderby syntaxis in Azure Cognitive Search](search-query-odata-orderby.md)
- [OData $select syntaxis in Azure Cognitive Search](search-query-odata-select.md)

## <a name="see-also"></a>Zie ook  

- [Gefacetteerde navigatie in Azure Cognitive Search](search-faceted-navigation.md)
- [Filters in Azure Cognitive Search](search-filters.md)
- [Zoekdocumenten &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
- [Syntaxis van Lucene query](query-lucene-syntax.md)
- [Eenvoudige querysyntaxis in Azure Cognitive Search](query-simple-syntax.md)
