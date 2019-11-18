---
title: Referentie voor OData search.in-functie
titleSuffix: Azure Cognitive Search
description: Syntaxis en referentie documentatie voor het gebruik van de functie search.in in azure Cognitive Search query's.
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
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74113119"
---
# <a name="odata-searchin-function-in-azure-cognitive-search"></a>OData-`search.in` functie in azure Cognitive Search

Een veelvoorkomend scenario in [OData-filter expressies](query-odata-filter-orderby-syntax.md) is om te controleren of één veld in elk document gelijk is aan een van de vele mogelijke waarden. Dit is bijvoorbeeld hoe sommige toepassingen [beveiliging](search-security-trimming-for-azure-search.md) kunnen beperken, door een veld met een of meer Principal-id's te controleren op basis van een lijst met Principal-id's die de gebruiker die de query heeft uitgegeven. Eén manier om een query als dit te schrijven, is met behulp van de Opera tors [`eq`](search-query-odata-comparison-operators.md) en [`or`](search-query-odata-logical-operators.md) :

    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')

Er is echter een kortere manier om dit te schrijven met behulp van de functie `search.in`:

    group_ids/any(g: search.in(g, '123, 456, 789'))

> [!IMPORTANT]
> Naast het korte en eenvoudiger te lezen, biedt het gebruik van `search.in` ook [prestatie voordelen](#bkmk_performance) en voor komt u dat er bepaalde [beperkingen van filters](search-query-odata-filter.md#bkmk_limits) gelden wanneer er honderden of zelfs duizenden waarden zijn die in het filter moeten worden meegenomen. Daarom raden we u ten zeerste aan om `search.in` te gebruiken in plaats van een complexere schei ding van gelijkheids expressies.

> [!NOTE]
> Versie 4,01 van de OData-standaard heeft onlangs de [operator`in`](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230)geïntroduceerd, die vergelijkbaar gedrag heeft als de `search.in` functie in azure Cognitive Search. Azure Cognitive Search biedt echter geen ondersteuning voor deze operator, dus u moet in plaats daarvan de functie `search.in` gebruiken.

## <a name="syntax"></a>Syntaxis

De volgende EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definieert de grammatica van de functie `search.in`:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

Er is ook een interactief syntaxis diagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxis diagram van OData voor Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Zie [OData-expressie syntaxis referentie voor Azure Cognitive Search](search-query-odata-syntax-reference.md) voor de volledige ebnf.

De functie `search.in` test of een opgegeven teken reeks veld of bereik variabele gelijk is aan een van een bepaalde lijst met waarden. Gelijkheid tussen de variabele en elke waarde in de lijst wordt op een hoofdletter gevoelige manier bepaald, op dezelfde manier als voor de operator `eq`. Daarom is een expressie als `search.in(myfield, 'a, b, c')` gelijk aan `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'`, behalve dat `search.in` veel betere prestaties zal opleveren.

Er zijn twee Overloads van de functie `search.in`:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

De para meters worden gedefinieerd in de volgende tabel:

| Parameternaam | Type | Beschrijving |
| --- | --- | --- |
| `variable` | `Edm.String` | Een verwijzing naar een teken reeks veld (of een bereik variabele over een teken reeks verzamelings veld in het geval waarin `search.in` wordt gebruikt binnen een `any` of `all` expressie). |
| `valueList` | `Edm.String` | Een teken reeks met een gescheiden lijst met waarden die moet overeenkomen met de para meter `variable`. Als de para meter `delimiters` niet is opgegeven, zijn de standaard scheidings tekens spatie en komma. |
| `delimiters` | `Edm.String` | Een teken reeks waarbij elk teken wordt behandeld als een schei ding tijdens het parseren van de `valueList`-para meter. De standaard waarde van deze para meter is `' ,'` wat betekent dat alle waarden met spaties en/of komma's ertussen van elkaar worden gescheiden. Als u andere scheidings tekens dan spaties en komma's wilt gebruiken omdat uw waarden de teken reeks bevatten, kunt u in deze para meter alternatieve afscheiders opgeven, zoals `'|'`. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Prestaties van `search.in`

Als u `search.in`gebruikt, kunt u een sub-Second-reactie tijd verwachten wanneer de tweede para meter een lijst met honderden of duizenden waarden bevat. Er is geen expliciete limiet voor het aantal items dat u kunt door geven aan `search.in`, hoewel u nog steeds beperkt bent door de maximum grootte van de aanvraag. De latentie neemt echter toe naarmate het aantal waarden toeneemt.

## <a name="examples"></a>Voorbeelden

Zoek alle hotels met een naam die gelijk is aan ' Sea View Motel ' of ' budget hotel '. Zinsdelen bevatten spaties. Dit is een standaard scheidings teken. U kunt een alternatief scheidings teken opgeven tussen enkele aanhalings tekens als de derde teken reeks parameter:  

    search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Alle hotels zoeken waarvan de naam gelijk is aan ' Sea View Motel ' of ' budget hotel ', gescheiden door ' | '):

    search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Alle hotels zoeken met kamers met het label ' WiFi ' of ' tub ':

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))

Zoek een overeenkomst op zinsdelen in een verzameling, zoals ' verwarmd handdoekontwerptoepassingen-racks ' of ' hairdryer inbegrepen ' in Tags.

    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Alle hotels zoeken zonder tag ' Motel ' of ' cabin':

    Tags/all(tag: not search.in(tag, 'motel, cabin'))

## <a name="next-steps"></a>Volgende stappen  

- [Filters in azure Cognitive Search](search-filters.md)
- [Overzicht van de OData-expressie taal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Naslag informatie voor de syntaxis van OData-expressies voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoeken in &#40;documenten Azure Cognitive Search rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
