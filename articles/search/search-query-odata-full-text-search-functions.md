---
title: Referentie voor zoek functie in volledige tekst van OData-Azure Search
description: OData-functies voor zoeken in volledige tekst, Search. ismatch en Search. ismatchscoring, in Azure Search query's.
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
ms.openlocfilehash: c3b28c8799b09ddfe008df8539709c5a704ac6b4
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648017"
---
# <a name="odata-full-text-search-functions-in-azure-search---searchismatch-and-searchismatchscoring"></a>OData-functies voor zoeken in volledige tekst in `search.ismatch` Azure Search-en`search.ismatchscoring`

Azure Search ondersteunt zoeken in volledige tekst in de context van [OData-filter expressies](query-odata-filter-orderby-syntax.md) via `search.ismatch` de `search.ismatchscoring` functies en. Met deze functies kunt u Zoek opdrachten in volledige tekst combi neren met strikte Booleaanse filtering op manieren die niet mogelijk zijn alleen met behulp `search` van de para meter op het hoogste niveau van de [zoek-API](https://docs.microsoft.com/rest/api/searchservice/search-documents).

> [!NOTE]
> De `search.ismatch` functies `search.ismatchscoring` en worden alleen ondersteund in filters in de [zoek-API](https://docs.microsoft.com/rest/api/searchservice/search-documents). Ze worden niet ondersteund in de api's [suggesties](https://docs.microsoft.com/rest/api/searchservice/suggestions) of [automatisch aanvullen](https://docs.microsoft.com/rest/api/searchservice/autocomplete) .

## <a name="syntax"></a>Syntaxis

De volgende ebnf ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definieert de grammatica van de `search.ismatch` functies `search.ismatchscoring` en:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

Er is ook een interactief syntaxis diagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxis diagram van OData voor Azure Search](https://azuresearch.github.io/odata-syntax-diagram/#search_is_match_call)

> [!NOTE]
> Zie de [syntaxis van de OData-expressie voor Azure Search](search-query-odata-syntax-reference.md) voor de volledige ebnf.

### <a name="searchismatch"></a>Search. ismatch

De `search.ismatch` functie evalueert een zoek opdracht in volledige tekst als onderdeel van een filter expressie. De documenten die overeenkomen met de zoek query worden geretourneerd in de resultatenset. De volgende Overloads van deze functie zijn beschikbaar:

- `search.ismatch(search)`
- `search.ismatch(search, searchFields)`
- `search.ismatch(search, searchFields, queryType, searchMode)`

De para meters worden gedefinieerd in de volgende tabel:

| Parameternaam | type | Description |
| --- | --- | --- |
| `search` | `Edm.String` | De zoek query (in een [eenvoudige](query-simple-syntax.md) of [volledige](query-lucene-syntax.md) lucene-query syntaxis). |
| `searchFields` | `Edm.String` | Een door komma's gescheiden lijst met Doorzoek bare velden waarnaar moet worden gezocht; wordt standaard ingesteld op alle Doorzoek bare velden in de index. Bij gebruik van een [Zoek opdracht](query-lucene-syntax.md#bkmk_fields) in `search` de para meter, overschrijven de veld aanduidingen in de Lucene-query alle velden die zijn opgegeven in deze para meter. |
| `queryType` | `Edm.String` | `'simple'`of `'full'`; standaard ingesteld `'simple'`op. Hiermee geeft u op welke query taal in `search` de para meter is gebruikt. |
| `searchMode` | `Edm.String` | `'any'`of `'all'`is standaard ingesteld `'any'`op. Hiermee wordt aangegeven of een of meer zoek termen in de `search` para meter moeten worden afgestemd om het document als een overeenkomst te tellen. Wanneer u de [lucene Booleaanse Opera tors](query-lucene-syntax.md#bkmk_boolean) in `search` de para meter gebruikt, hebben ze prioriteit boven deze para meter. |

Alle bovenstaande para meters zijn gelijk aan de overeenkomstige [para meters voor zoek aanvragen in de zoek-API](https://docs.microsoft.com/rest/api/searchservice/search-documents).

De `search.ismatch` functie retourneert een waarde van het `Edm.Boolean`type, zodat u deze kunt samen stellen met andere filter subexpressies met behulp van de [logische booleaanse Opera tors](search-query-odata-logical-operators.md).

> [!NOTE]
> Azure Search biedt geen ondersteuning voor `search.ismatch` het `search.ismatchscoring` gebruik van of binnen lambda-expressies. Dit betekent dat het niet mogelijk is om filters te schrijven over verzamelingen objecten die kunnen correleren met Zoek opdrachten in volledige tekst met strikte filter overeenkomsten voor hetzelfde object. Zie [problemen met verzamelings filters in azure Search oplossen](search-query-troubleshoot-collection-filters.md)voor meer informatie over deze beperking en voor beelden. Zie informatie over het [verzamelen van filters in azure Search](search-query-understand-collection-filters.md)voor meer gedetailleerde informatie over de redenen waarom deze beperking bestaat.


### <a name="searchismatchscoring"></a>Search. ismatchscoring

De `search.ismatchscoring` functie, zoals de `search.ismatch` functie, retourneert `true` voor documenten die overeenkomen met de query voor zoeken in volledige tekst, door gegeven als een para meter. Het verschil ertussen is dat de relevantie Score van documenten die overeenkomen `search.ismatchscoring` met de query, bijdraagt aan de totale document Score, in het `search.ismatch`geval van, de document score niet wordt gewijzigd. De volgende overbelastingen van deze functie zijn beschikbaar met para meters die identiek `search.ismatch`zijn aan die van:

- `search.ismatchscoring(search)`
- `search.ismatchscoring(search, searchFields)`
- `search.ismatchscoring(search, searchFields, queryType, searchMode)`

Zowel de `search.ismatch` als `search.ismatchscoring` -functies kunnen in dezelfde filter expressie worden gebruikt.

## <a name="examples"></a>Voorbeelden

Vind documenten met het woord ' afgebakend '. Deze filter query is identiek aan een [Zoek opdracht](https://docs.microsoft.com/rest/api/searchservice/search-documents) met `search=waterfront`.

    search.ismatchscoring('waterfront')

Vind documenten met het woord ' Hostel ' en classificatie groter of gelijk aan 4, of documenten met het woord ' Motel ' en de classificatie is gelijk aan 5. Opmerking: deze aanvraag kan niet worden aangegeven zonder de `search.ismatchscoring` functie.

    search.ismatchscoring('hostel') and Rating ge 4 or search.ismatchscoring('motel') and Rating eq 5

Documenten zonder het woord "luxe" zoeken.

    not search.ismatch('luxury')

Documenten zoeken met de woord weer gave ' Oceaan ' of de classificatie gelijk aan 5. De `search.ismatchscoring` query wordt alleen uitgevoerd op velden `HotelName` en `Rooms/Description`.

Documenten die overeenkomen met alleen de tweede component van de schei ding, retour neren te veel- `Rating` Hotels met een waarde die gelijk is aan 5. Om het duidelijk te maken dat deze documenten niet overeenkomen met een van de gescoorde delen van de expressie, worden deze geretourneerd met een score die gelijk is aan nul.

    search.ismatchscoring('"ocean view"', 'Rooms/Description,HotelName') or Rating eq 5

Vind documenten waar de termen "Hotel" en "lucht haven" binnen vijf woorden van elkaar zijn genoteerd in de beschrijving van het hotel en wanneer roken niet in ten minste een deel van de kamers is toegestaan. Deze query maakt gebruik van de [volledige lucene-query taal](query-lucene-syntax.md).

    search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and Rooms/any(room: not room/SmokingAllowed)

## <a name="next-steps"></a>Volgende stappen  

- [Filters in Azure Search](search-filters.md)
- [Overzicht van de OData-expressie taal voor Azure Search](query-odata-filter-orderby-syntax.md)
- [Verwijzing naar de syntaxis van de OData-expressie voor Azure Search](search-query-odata-syntax-reference.md)
- [Zoeken naar &#40;documenten Azure Search service rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
