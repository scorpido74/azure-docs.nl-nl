---
title: Verwijzing naar oData-filter
titleSuffix: Azure Cognitive Search
description: OData-naslagwerk en volledige syntaxis die wordt gebruikt voor het maken van filterexpressies in Azure Cognitive Search-query's.
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
ms.openlocfilehash: b966e9cfa3ef40666dbbd62135f8f964e5eb2023
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282885"
---
# <a name="odata-filter-syntax-in-azure-cognitive-search"></a>OData $filter syntaxis in Azure Cognitive Search

Azure Cognitive Search gebruikt [OData-filterexpressies](query-odata-filter-orderby-syntax.md) om aanvullende criteria toe te passen op een zoekopdracht naast zoektermen met volledige tekst. In dit artikel worden de syntaxis van filters in detail beschreven. Zie [Filters in Azure Cognitive Search](search-filters.md)voor meer algemene informatie over welke filters zijn en hoe u deze gebruiken om specifieke queryscenario's te realiseren.

## <a name="syntax"></a>Syntaxis

Een filter in de OData-taal is een Booleaanse expressie, die op zijn beurt een van de verschillende soorten expressies kan zijn, zoals blijkt uit het volgende EBNF ([Extended Backus-Naur Form):](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path
```

Er is ook een interactief syntaxisdiagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxisdiagram OData voor Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> Zie [Syntaxisverwijzing oData-expressie voor Azure Cognitive Search](search-query-odata-syntax-reference.md) voor de volledige EBNF.

De typen Booleaanse expressies zijn:

- Verzamelingsfilterexpressies gebruiken `any` of `all`. Deze passen filtercriteria toe op verzamelingsvelden. Zie [OData-verzamelingsoperatoren in Azure Cognitive Search](search-query-odata-collection-operators.md)voor meer informatie.
- Logische expressies die andere Booleaanse expressies combineren met behulp van de operatoren `and` `or`, en `not`. Zie [OData logische operatoren in Azure Cognitive Search](search-query-odata-logical-operators.md)voor meer informatie.
- Vergelijkingsexpressies, die velden of bereikvariabelen vergelijken `eq` `ne`met `gt` `lt`constante waarden met behulp van de operatoren , , , , `ge`en `le`. Zie [OData-vergelijkingsoperatoren in Azure Cognitive Search](search-query-odata-comparison-operators.md)voor meer informatie. Vergelijkingsexpressies worden ook gebruikt om afstanden tussen `geo.distance` geo-ruimtelijke coördinaten te vergelijken met behulp van de functie. Zie [OData geo-ruimtelijke functies in Azure Cognitive Search](search-query-odata-geo-spatial-functions.md)voor meer informatie.
- De Booleaanse `true` `false`literals en. Deze constanten kunnen soms nuttig zijn bij het programmatisch genereren van filters, maar anders niet de neiging om te worden gebruikt in de praktijk.
- Oproepen naar Booleaanse functies, waaronder:
  - `geo.intersects`, die test of een bepaald punt zich binnen een bepaalde veelhoek bevindt. Zie [OData geo-ruimtelijke functies in Azure Cognitive Search](search-query-odata-geo-spatial-functions.md)voor meer informatie.
  - `search.in`, die een veld- of bereikvariabele vergelijkt met elke waarde in een lijst met waarden. Zie [OData-functie `search.in` in Azure Cognitive Search](search-query-odata-search-in-function.md)voor meer informatie.
  - `search.ismatch`en `search.ismatchscoring`, die full-text zoekbewerkingen uitvoeren in een filtercontext. Zie [OData-zoekfuncties voor volledige tekst in Azure Cognitive Search](search-query-odata-full-text-search-functions.md)voor meer informatie.
- Veldpaden of bereikvariabelen `Edm.Boolean`van type . Als uw index bijvoorbeeld een Booleaanse veld heeft aangeroepen `IsEnabled` en `true`u alle documenten wilt retourneren waar dit veld zich bevindt, kan uw filterexpressie alleen de naam `IsEnabled`zijn.
- Booleaanse uitdrukkingen tussen haakjes. Het gebruik van haakjes kan helpen om expliciet de volgorde van bewerkingen in een filter te bepalen. Zie de volgende sectie voor meer informatie over de standaardprioriteit van de OData-operatoren.

### <a name="operator-precedence-in-filters"></a>Operatorprioriteit in filters

Als u een filterexpressie schrijft zonder haakjes rond de subexpressies, evalueert Azure Cognitive Search deze op basis van een set regelregels voor operatoren. Deze regels zijn gebaseerd op de welke operatoren worden gebruikt om subexpressies te combineren. In de volgende tabel worden groepen operatoren weergegeven in volgorde van hoogste tot laagste prioriteit:

| Groep | Operator(s) |
| --- | --- |
| Logische operators | `not` |
| Vergelijkingsoperators | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Logische operators | `and` |
| Logische operators | `or` |

Een operator die hoger in de bovenstaande tabel staat, zal "strakker binden" aan zijn operands dan andere operatoren. Bijvoorbeeld, `and` heeft een hogere `or`prioriteit dan , en vergelijking exploitanten hebben een hogere prioriteit dan een van hen, dus de volgende twee uitdrukkingen zijn gelijkwaardig:

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

De `not` operator heeft de hoogste prioriteit van alle - zelfs hoger dan de vergelijking exploitanten. Dat is waarom als je probeert om een filter te schrijven als dit:

    not Rating gt 5

U krijgt deze foutmelding:

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

Deze fout treedt op omdat de `Rating` operator is gekoppeld `Edm.Int32`aan alleen het veld, dat van het type is, en niet met de hele vergelijkingsexpressie. De oplossing is om het `not` operand van tussen haakjes:

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>Beperkingen voor filtergrootte

Er zijn grenzen aan de grootte en complexiteit van filterexpressies die u naar Azure Cognitive Search verzenden. De limieten zijn grofweg gebaseerd op het aantal clausules in uw filterexpressie. Een goede richtlijn is dat als je honderden clausules hebt, je het risico loopt de limiet te overschrijden. We raden u aan uw toepassing zo te ontwerpen dat deze geen filters van onbegrensde grootte genereert.

> [!TIP]
> Het gebruik van [de `search.in` functie](search-query-odata-search-in-function.md) in plaats van lange disjunctie van gelijkheidvergelijkingen kan helpen voorkomen dat de filterclausulelimiet wordt beperkt, omdat een functieaanroep telt als één enkele clausule.

## <a name="examples"></a>Voorbeelden

Vind alle hotels met ten minste één kamer met een basistarief van minder dan $ 200 die zijn beoordeeld op of boven de 4:

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

Vind alle andere hotels dan "Sea View Motel" die sinds 2010 zijn gerenoveerd:

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

Vind alle hotels die in 2010 of later zijn gerenoveerd. De datumtijd letterlijk bevat tijdzone informatie voor Pacific Standard Time:  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

Vind alle hotels die parkeergelegenheid hebben en waar alle kamers rookvrij zijn:

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \-OF -  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

Vind alle hotels die luxe zijn of parkeergelegenheid en hebben een rating van 5:  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

Vind alle hotels met de tag "wifi" in ten minste één `Collection(Edm.String)` kamer (waar elke kamer tags heeft die in een veld zijn opgeslagen):  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

Vind alle hotels met alle kamers:  

    $filter=Rooms/any()

Vind alle hotels die geen kamers hebben:

    $filter=not Rooms/any()

Vind alle hotels binnen 10 kilometer van `Location` een bepaald `Edm.GeographyPoint`referentiepunt (waar is een veld van het type):

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

Zoek alle hotels binnen een bepaalde viewport `Location` die wordt beschreven als een veelhoek (waar is een veld van het type Edm.GeographyPoint). De veelhoek moet worden gesloten, wat betekent dat de eerste en laatste puntsets hetzelfde moeten zijn. Ook [moeten de punten worden vermeld in volgorde tegen de klok in](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

Zoek alle hotels waar het veld 'Beschrijving' null is. Het veld wordt nietig verklaard als het nooit is ingesteld of als het expliciet is ingesteld op null:  

    $filter=Description eq null

Vind alle hotels met een naam die gelijk is aan 'Sea View motel' of 'Budget hotel'). Deze zinnen bevatten spaties en ruimte is een standaardscheidingsscheiding. U een alternatieve scheidingsteken in afzonderlijke aanhalingstekens opgeven als de parameter derde tekenreeks:  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Vind alle hotels met een naam die gelijk is aan 'Sea View motel' of 'Budget hotel' gescheiden door '|'):  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Vind alle hotels waar alle kamers de tag 'wifi' of 'tub' hebben:

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

Zoek een overeenkomst op zinnen in een collectie, zoals 'verwarmde handdoek rekken' of 'haardroger inbegrepen' in tags.

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Documenten vinden met het woord "waterkant". Deze filterquery is identiek aan `search=waterfront`een [zoekaanvraag](https://docs.microsoft.com/rest/api/searchservice/search-documents) met .

    $filter=search.ismatchscoring('waterfront')

Zoek documenten met het woord "hostel" en rating groter of gelijk aan 4, of documenten met het woord "motel" en rating gelijk aan 5. Deze aanvraag kan niet worden `search.ismatchscoring` uitgedrukt zonder de functie, omdat `or`het combineert full-text zoeken met filterbewerkingen met behulp van .

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

Zoek documenten zonder het woord "luxe".

    $filter=not search.ismatch('luxury')

Documenten zoeken met de zinsnede "oceaanzicht" of beoordeling die gelijk is aan 5. De `search.ismatchscoring` query wordt alleen uitgevoerd `HotelName` `Description`op velden en . Documenten die alleen overeenkomen met de tweede clausule van het disjunctie wordt ook geretourneerd - hotels met `Rating` gelijk aan 5. Deze documenten worden geretourneerd met een score gelijk aan nul om duidelijk te maken dat ze niet overeenkomen met een van de gescoorde delen van de expressie.

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

Zoek hotels waar de termen "hotel" en "luchthaven" zijn niet meer dan vijf woorden uit elkaar in de beschrijving, en waar alle kamers zijn niet-roken. Deze query maakt gebruik van de [volledige Lucene-querytaal](query-lucene-syntax.md).

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>Volgende stappen  

- [Filters in Azure Cognitive Search](search-filters.md)
- [Overzicht van OData-expressietaal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Syntaxisverwijzing oData-expressie voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoekdocumenten &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
