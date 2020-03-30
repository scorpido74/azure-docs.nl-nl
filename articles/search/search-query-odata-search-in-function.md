---
title: OData search.in functieverwijzing
titleSuffix: Azure Cognitive Search
description: Syntaxis- en referentiedocumentatie voor het gebruik van de functie search.in in Azure Cognitive Search-query's.
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
ms.openlocfilehash: b43c46599cbacaf40bc9583e364d088fa27a3ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113119"
---
# <a name="odata-searchin-function-in-azure-cognitive-search"></a>OData, `search.in` functie in Azure Cognitive Search

Een veelvoorkomend scenario in [OData-filterexpressies](query-odata-filter-orderby-syntax.md) is om te controleren of één veld in elk document gelijk is aan een van de vele mogelijke waarden. Dit is bijvoorbeeld hoe sommige toepassingen [beveiligingsbijsnijden](search-security-trimming-for-azure-search.md) implementeren - door een veld met een of meer hoofd-id's te controleren op een lijst met hoofd-id's die de gebruiker vertegenwoordigen die de query uitgeeft. Een manier om een query als [`eq`](search-query-odata-comparison-operators.md) deze [`or`](search-query-odata-logical-operators.md) te schrijven is het gebruik van de en operators:

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

Er is echter een kortere manier om `search.in` dit te schrijven, met behulp van de functie:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> Naast het feit dat korter `search.in` en gemakkelijker te lezen, met behulp van biedt ook [voordelen voor de prestaties](#bkmk_performance) en vermijdt bepaalde grootte beperkingen van [filters](search-query-odata-filter.md#bkmk_limits) wanneer er honderden of zelfs duizenden waarden op te nemen in het filter. Daarom raden we ten `search.in` zeerste aan om in plaats van een complexere disjunctie van gelijkheidsuitdrukkingen te gebruiken.

> [!NOTE]
> Versie 4.01 van de OData-standaard [ `in` ](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230)heeft onlangs de operator `search.in` geïntroduceerd , die vergelijkbaar gedrag vertoont als de functie in Azure Cognitive Search. Azure Cognitive Search biedt echter geen ondersteuning voor `search.in` deze operator, dus u moet de functie in plaats daarvan gebruiken.

## <a name="syntax"></a>Syntaxis

In het volgende EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) wordt de grammatica van de `search.in` functie gedefinieerd:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

Er is ook een interactief syntaxisdiagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxisdiagram OData voor Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Zie [Syntaxisverwijzing oData-expressie voor Azure Cognitive Search](search-query-odata-syntax-reference.md) voor de volledige EBNF.

De `search.in` functie test of een bepaald tekenreeksveld of bereikvariabele gelijk is aan een van een bepaalde lijst met waarden. De gelijkheid tussen de variabele en elke waarde in de lijst wordt op `eq` een case-gevoelige manier bepaald, op dezelfde manier als voor de operator. Daarom is `search.in(myfield, 'a, b, c')` een uitdrukking `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`als `search.in` gelijkwaardig aan , behalve dat veel betere prestaties zal opleveren.

Er zijn twee overbelastingen van de `search.in` functie:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

De parameters worden gedefinieerd in de volgende tabel:

| Parameternaam | Type | Beschrijving |
| --- | --- | --- |
| `variable` | `Edm.String` | Een verwijzing naar het tekenreeksveld (of een bereikvariabele `search.in` over een `any` `all` tekenreeksverzamelingsveld in het geval dat wordt gebruikt in een of expressie). |
| `valueList` | `Edm.String` | Een tekenreeks met een afgebakende lijst `variable` met waarden die overeenkomen met de parameter. Als `delimiters` de parameter niet is opgegeven, zijn de standaardscheidingstekens ruimte en komma. |
| `delimiters` | `Edm.String` | Een tekenreeks waarbij elk teken wordt behandeld als `valueList` een scheidingsteken bij het ontwijsmaken van de parameter. De standaardwaarde van `' ,'` deze parameter is wat betekent dat alle waarden met spaties en/of komma's ertussen worden gescheiden. Als u andere scheidingstekens dan spaties en komma's moet gebruiken omdat uw waarden `'|'` deze tekens bevatten, u alternatieve scheidingstekens opgeven, zoals in deze parameter. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Prestaties van`search.in`

Als u `search.in`, u een reactietijd van subseconde verwachten wanneer de tweede parameter een lijst met honderden of duizenden waarden bevat. Er is geen expliciete limiet voor het `search.in`aantal items waaraan je doorgeven, hoewel je nog steeds beperkt bent door de maximale aanvraaggrootte. De latentie zal echter toenemen naarmate het aantal waarden toeneemt.

## <a name="examples"></a>Voorbeelden

Vind alle hotels met naam gelijk aan 'Sea View motel' of 'Budget hotel'. Zinnen bevatten spaties, wat een standaardscheidingsscheiding is. U een alternatieve scheidingsteken in afzonderlijke aanhalingstekens opgeven als de parameter derde tekenreeks:  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Vind alle hotels met een naam die gelijk is aan 'Sea View motel' of 'Budget hotel' gescheiden door '|'):

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Vind alle hotels met kamers met de tag 'wifi' of 'tub':

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

Zoek een overeenkomst op zinnen in een collectie, zoals 'verwarmde handdoek rekken' of 'haardroger inbegrepen' in tags.

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Vind alle hotels zonder de tag 'motel' of 'cabin':

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>Volgende stappen  

- [Filters in Azure Cognitive Search](search-filters.md)
- [Overzicht van OData-expressietaal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Syntaxisverwijzing oData-expressie voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoekdocumenten &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
