---
title: Verwijzing naar de zoekfunctie oData met volledige tekst
titleSuffix: Azure Cognitive Search
description: OData full-text search functies, search.ismatch en search.ismatchscoring, in Azure Cognitive Search queries.
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
ms.openlocfilehash: 06eb29f2f3245d3f4fd047fb86b2b57fb1f0989e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72793355"
---
# <a name="odata-full-text-search-functions-in-azure-cognitive-search---searchismatch-and-searchismatchscoring"></a>OData-zoekfuncties voor volledige tekst `search.ismatch` in Azure Cognitive Search - en`search.ismatchscoring`

Azure Cognitive Search ondersteunt zoeken in volledige tekst in `search.ismatch` de `search.ismatchscoring` context van [OData-filterexpressies](query-odata-filter-orderby-syntax.md) via de en functies. Met deze functies u zoeken in volledige tekst combineren met strikte Booleaanse filtering op manieren die niet mogelijk zijn door alleen de parameter op het hoogste niveau `search` van de Search API [te](https://docs.microsoft.com/rest/api/searchservice/search-documents)gebruiken.

> [!NOTE]
> De `search.ismatch` `search.ismatchscoring` functies en functies worden alleen ondersteund in filters in de [Search API](https://docs.microsoft.com/rest/api/searchservice/search-documents). Ze worden niet ondersteund in de API's [voor voorstellen](https://docs.microsoft.com/rest/api/searchservice/suggestions) of [automatisch aanvullen.](https://docs.microsoft.com/rest/api/searchservice/autocomplete)

## <a name="syntax"></a>Syntaxis

In het volgende EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) wordt de grammatica van de `search.ismatch` functies gedefinieerd: `search.ismatchscoring`

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

Er is ook een interactief syntaxisdiagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxisdiagram OData voor Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Zie [Syntaxisverwijzing oData-expressie voor Azure Cognitive Search](search-query-odata-syntax-reference.md) voor de volledige EBNF.

### <a name="searchismatch"></a>search.ismatch

De `search.ismatch` functie evalueert een zoekopdracht met volledige tekst als onderdeel van een filterexpressie. De documenten die overeenkomen met de zoekopdracht worden geretourneerd in de resultatenset. De volgende overbelasting van deze functie zijn beschikbaar:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

De parameters worden gedefinieerd in de volgende tabel:

| Parameternaam | Type | Beschrijving |
| --- | --- | --- |
| `search` | `Edm.String` | De zoekopdracht (in [eenvoudige](query-simple-syntax.md) of [volledige](query-lucene-syntax.md) lucene query syntaxis). |
| `searchFields` | `Edm.String` | Door komma's gescheiden lijst met doorzoekbare velden om in te zoeken; standaard voor alle doorzoekbare velden in de index. Bij het gebruik van `search` [veldzoeken](query-lucene-syntax.md#bkmk_fields) in de parameter overschrijven de veldaanduidingen in de Lucene-query alle velden die in deze parameter zijn opgegeven. |
| `queryType` | `Edm.String` | `'simple'`of `'full'`; standaard ingesteld `'simple'`op . Hiermee geeft u op `search` welke querytaal in de parameter is gebruikt. |
| `searchMode` | `Edm.String` | `'any'`of `'all'`, standaard `'any'`. Geeft aan of alle zoektermen `search` in de parameter moeten worden gematcht om het document als een overeenkomst te tellen. Bij het gebruik van de [Lucene Booleaanse operatoren](query-lucene-syntax.md#bkmk_boolean) in de `search` parameter hebben ze voorrang op deze parameter. |

Alle bovenstaande parameters zijn gelijk aan de bijbehorende [zoekaanvraagparameters in de Search API](https://docs.microsoft.com/rest/api/searchservice/search-documents).

De `search.ismatch` functie retourneert `Edm.Boolean`een waarde van het type, waarmee u deze samenstellen met andere filtersubexpressies met behulp van de Booleaanse [logische operatoren.](search-query-odata-logical-operators.md)

> [!NOTE]
> Azure Cognitive Search biedt `search.ismatch` `search.ismatchscoring` geen ondersteuning voor het gebruik of binnen lambda-expressies. Dit betekent dat het niet mogelijk is om filters te schrijven over verzamelingen van objecten die full-text zoekovereenkomsten kunnen correleren met strikte filterovereenkomsten op hetzelfde object. Zie [Filters voor het oplossen van problemen oplossen in Azure Cognitive Search](search-query-troubleshoot-collection-filters.md)voor meer informatie over deze beperking en voorbeelden. Zie [Verzamelingsfilters in Azure Cognitive Search begrijpen](search-query-understand-collection-filters.md)voor meer diepgaande informatie over waarom deze beperking bestaat.


### <a name="searchismatchscoring"></a>search.ismatchscoring

De `search.ismatchscoring` functie retourneert, net als de `search.ismatch` functie, voor `true` documenten die overeenkomen met de zoekopdracht met volledige tekst die als parameter is doorgegeven. Het verschil tussen hen is dat de `search.ismatchscoring` relevantiescore van documenten die overeenkomen met de `search.ismatch`query zal bijdragen aan de totale documentscore, terwijl in het geval van , de documentscore niet wordt gewijzigd. De volgende overbelasting van deze functie zijn beschikbaar `search.ismatch`met parameters die identiek zijn aan die van :

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

Zowel `search.ismatch` de `search.ismatchscoring` functies als de functies kunnen in dezelfde filterexpressie worden gebruikt.

## <a name="examples"></a>Voorbeelden

Documenten vinden met het woord "waterkant". Deze filterquery is identiek aan `search=waterfront`een [zoekaanvraag](https://docs.microsoft.com/rest/api/searchservice/search-documents) met .

    search.ismatchscoring('waterfront')

Zoek documenten met het woord "hostel" en rating groter of gelijk aan 4, of documenten met het woord "motel" en rating gelijk aan 5. Let op, dit verzoek kan `search.ismatchscoring` niet worden uitgedrukt zonder de functie.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

Zoek documenten zonder het woord "luxe".

    not search.ismatch('luxury')

Documenten zoeken met de zinsnede "oceaanzicht" of beoordeling die gelijk is aan 5. De `search.ismatchscoring` query wordt alleen uitgevoerd `HotelName` `Rooms/Description`op velden en .

Documenten die alleen overeenkomen met de tweede clausule van het disjunctie wordt ook geretourneerd - hotels met `Rating` gelijk aan 5. Om duidelijk te maken dat deze documenten niet overeenkomen met een van de gescoorde delen van de expressie, worden ze geretourneerd met een score gelijk aan nul.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

Zoek documenten waar de termen "hotel" en "luchthaven" zijn binnen 5 woorden van elkaar in de beschrijving van het hotel, en waar roken is niet toegestaan in ten minste sommige van de kamers. Deze query maakt gebruik van de [volledige Lucene-querytaal](query-lucene-syntax.md).

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>Volgende stappen  

- [Filters in Azure Cognitive Search](search-filters.md)
- [Overzicht van OData-expressietaal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Syntaxisverwijzing oData-expressie voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoekdocumenten &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
