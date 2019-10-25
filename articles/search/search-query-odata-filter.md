---
title: Verwijzing naar OData-filter
titleSuffix: Azure Cognitive Search
description: Naslag informatie over OData-taal voor filter syntaxis in azure Cognitive Search query's.
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
ms.openlocfilehash: ba1f5e8f2369d3222b3c31738e252a20b6de8906
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72793376"
---
# <a name="odata-filter-syntax-in-azure-cognitive-search"></a>OData-$filter syntaxis in azure Cognitive Search

In azure Cognitive Search worden [OData-filter expressies](query-odata-filter-orderby-syntax.md) gebruikt om aanvullende criteria toe te passen op een zoek query naast zoek termen in volledige tekst. In dit artikel wordt de syntaxis van filters in detail beschreven. Zie [filters in Azure Cognitive Search](search-filters.md)voor meer algemene informatie over welke filters zijn en hoe u deze kunt gebruiken voor het realiseren van specifieke query scenario's.

## <a name="syntax"></a>Syntaxis

Een filter in de OData-taal is een booleaanse expressie die op zijn beurt een van de verschillende typen expressies kan zijn, zoals wordt weer gegeven in de volgende EBNF ([uitgebreid Backus-Naur-formulier](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

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

Er is ook een interactief syntaxis diagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxis diagram van OData voor Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> Zie [OData-expressie syntaxis referentie voor Azure Cognitive Search](search-query-odata-syntax-reference.md) voor de volledige ebnf.

De typen Booleaanse expressies zijn onder andere:

- Verzamelings filter expressies met behulp van `any` of `all`. Deze filter criteria Toep assen op verzamelings velden. Zie [OData-verzamelings operatoren in Azure Cognitive Search](search-query-odata-collection-operators.md)voor meer informatie.
- Logische expressies waarmee andere Booleaanse expressies worden gecombineerd met behulp van de Opera tors `and`, `or`en `not`. Zie voor meer informatie [OData Logical Opera tors in Azure Cognitive Search](search-query-odata-logical-operators.md).
- Vergelijkings expressies, waarmee velden of bereik variabelen worden vergeleken met constante waarden met behulp van de Opera tors `eq`, `ne`, `gt`, `lt`, `ge`en `le`. Zie [OData-vergelijkings operatoren in Azure Cognitive Search](search-query-odata-comparison-operators.md)voor meer informatie. Vergelijkings expressies worden ook gebruikt om de afstand tussen geo-ruimtelijke coördinaten te vergelijken met behulp van de functie `geo.distance`. Zie [geo-ruimtelijke functies van OData in Azure Cognitive Search](search-query-odata-geo-spatial-functions.md)voor meer informatie.
- De letterlijke Booleaanse waarden `true` en `false`. Deze constanten kunnen soms handig zijn bij het programmatisch genereren van filters, maar anders niet in de praktijk.
- Aanroepen van Booleaanse functies, waaronder:
  - `geo.intersects`, waarmee wordt getest of een bepaald punt zich in een bepaalde veelhoek bevindt. Zie [geo-ruimtelijke functies van OData in Azure Cognitive Search](search-query-odata-geo-spatial-functions.md)voor meer informatie.
  - `search.in`, waarmee een veld-of bereik variabele wordt vergeleken met elke waarde in een lijst met waarden. Zie [OData-`search.in` functie in Azure Cognitive Search](search-query-odata-search-in-function.md)voor meer informatie.
  - `search.ismatch` en `search.ismatchscoring`, waarmee zoek bewerkingen in volledige tekst in een filter context worden uitgevoerd. Zie [OData-functies voor zoeken in volledige tekst in Azure Cognitive Search](search-query-odata-full-text-search-functions.md)voor meer informatie.
- Veld paden of bereik variabelen van het type `Edm.Boolean`. Als uw index bijvoorbeeld een Boole-veld met de naam `IsEnabled` heeft en u alle documenten wilt retour neren waarin dit veld `true`, kan de filter expressie alleen de naam `IsEnabled`hebben.
- Boole-expressies tussen haakjes. Door haakjes te gebruiken kunt u de volg orde van de bewerkingen in een filter expliciet bepalen. Zie de volgende sectie voor meer informatie over de standaard prioriteit van de OData-Opera tors.

### <a name="operator-precedence-in-filters"></a>Operator prioriteit in filters

Als u een filter expressie zonder haakjes schrijft rond de bijbehorende subexpressies, wordt deze door Azure Cognitive Search geëvalueerd op basis van een set met operator prioriteits regels. Deze regels zijn gebaseerd op de Opera tors die worden gebruikt voor het combi neren van subexpressies. De volgende tabel geeft een lijst van groepen Opera tors in volg orde van hoogste tot laagste prioriteit:

| Groep | Operator (s) |
| --- | --- |
| Logische Opera tors | `not` |
| Vergelijkingsoperators | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Logische Opera tors | `and` |
| Logische Opera tors | `or` |

Een operator die hoger in de bovenstaande tabel staat, wordt ' Binder meer ' aan de operanden dan andere opera tors. `and` heeft bijvoorbeeld een hogere prioriteit dan `or`, en vergelijkings operatoren hebben een hogere prioriteit dan een van beide, dus zijn de volgende twee expressies gelijkwaardig:

    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))

De operator `not` heeft de hoogste prioriteit van alle--zelfs hoger dan de vergelijkings operators. Daarom kunt u een filter als volgt schrijven:

    not Rating gt 5

Dit fout bericht wordt weer gegeven:

    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.

Deze fout treedt op omdat de operator is gekoppeld aan alleen het `Rating` veld, van het type `Edm.Int32`en niet met de volledige vergelijkings expressie. De oplossing is om de operand van `not` tussen haakjes te plaatsen:

    not (Rating gt 5)

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>Beperkingen voor de filter grootte

Er zijn limieten voor de grootte en complexiteit van filter expressies die u kunt verzenden naar Azure Cognitive Search. De limieten zijn ongeveer gebaseerd op het aantal componenten in uw filter expressie. Een goede richt lijn is dat als u honderden componenten hebt, u een risico hebt om de limiet te overschrijden. We raden u aan uw toepassing zodanig te ontwerpen dat er geen filters van een ongebonden grootte worden gegenereerd.

> [!TIP]
> Het gebruik van [de functie `search.in`](search-query-odata-search-in-function.md) in plaats van lange schei dingen van gelijkheids vergelijkingen kan helpen om de limiet van de filter component te vermijden omdat een functie aanroep als één component telt.

## <a name="examples"></a>Voorbeelden

Zoek alle hotels met ten minste één kamer met een basis snelheid van minder dan $200 die is gewaardeerd op of boven 4:

    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4

Alle andere hotels zoeken dan ' Sea View Motel ' die sinds 2010 zijn renovated:

    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z

Alle hotels zoeken die in 2010 of hoger zijn renovated. De letterlijke datetime bevat informatie over de tijd zone voor Pacific (standaard tijd):  

    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00

Alle hotels zoeken die zijn opgenomen in parkeer plaatsen, waarbij alle kamers niet-roken zijn:

    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)

 \- of-  

    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)

Alle hotels zoeken die luxe of parkeren zijn en een classificatie van 5 hebben:  

    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5

Alle hotels met het label "WiFi" zoeken in ten minste één ruimte (waarbij elke kamer labels in een `Collection(Edm.String)` veld heeft opgeslagen):  

    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))

Alle hotels zoeken met alle kamers:  

    $filter=Rooms/any()

Alle hotels zoeken die geen kamers hebben:

    $filter=not Rooms/any()

Alle hotels zoeken binnen 10 kilo meter van een gegeven referentie punt (waarbij `Location` een veld van het type `Edm.GeographyPoint`is):

    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10

Alle hotels in een bepaalde View Port zoeken die worden beschreven als een veelhoek (waarbij `Location` een veld van het type EDM. GeographyPoint is). De veelhoek moet worden gesloten, wat betekent dat de eerste en laatste punt sets hetzelfde moeten zijn. [De punten moeten ook links in de volg orde worden weer gegeven](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1).

    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

Alle hotels zoeken waarbij het veld Beschrijving null is. Het veld is leeg als het nooit is ingesteld, of als het expliciet is ingesteld op Null:  

    $filter=Description eq null

Alle hotels zoeken waarvan de naam gelijk is aan ' Sea View Motel ' of ' budget hotel '). Deze woord groepen bevatten spaties en ruimte is een standaard scheidings teken. U kunt een alternatief scheidings teken opgeven tussen enkele aanhalings tekens als de derde teken reeks parameter:  

    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')

Alle hotels zoeken waarvan de naam gelijk is aan ' Sea View Motel ' of ' budget hotel ', gescheiden door ' | '):  

    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')

Alle hotels zoeken waarbij alle kamers het label ' WiFi ' of ' tub ' hebben:

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))

Zoek een overeenkomst op zinsdelen in een verzameling, zoals ' verwarmd handdoekontwerptoepassingen-racks ' of ' hairdryer inbegrepen ' in Tags.

    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))

Vind documenten met het woord ' afgebakend '. Deze filter query is identiek aan een [Zoek aanvraag](https://docs.microsoft.com/rest/api/searchservice/search-documents) met `search=waterfront`.

    $filter=search.ismatchscoring('waterfront')

Vind documenten met het woord ' Hostel ' en classificatie groter of gelijk aan 4, of documenten met het woord ' Motel ' en de classificatie is gelijk aan 5. Deze aanvraag kan niet worden weer gegeven zonder de `search.ismatchscoring` functie, omdat hiermee Zoek opdrachten in volledige tekst met behulp van `or`worden gecombineerd.

    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5

Documenten zonder het woord "luxe" zoeken.

    $filter=not search.ismatch('luxury')

Documenten zoeken met de woord weer gave ' Oceaan ' of de classificatie gelijk aan 5. De `search.ismatchscoring` query wordt alleen uitgevoerd op basis van velden `HotelName` en `Description`. Documenten die overeenkomen met alleen de tweede component van de schei ding, retour neren te veel-hotels met `Rating` gelijk aan 5. Deze documenten worden geretourneerd met een score die gelijk is aan nul, zodat deze duidelijk wordt gemaakt dat ze niet overeenkomen met een van de gescoorde delen van de expressie.

    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5

Zoek hotels waar de termen ' Hotel ' en ' lucht haven ' niet meer dan vijf woorden uit de beschrijving bestaan en waarbij alle kamers niet-roken zijn. Deze query maakt gebruik van de [volledige lucene-query taal](query-lucene-syntax.md).

    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)

## <a name="next-steps"></a>Volgende stappen  

- [Filters in azure Cognitive Search](search-filters.md)
- [Overzicht van de OData-expressie taal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Naslag informatie voor de syntaxis van OData-expressies voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoeken in &#40;documenten Azure Cognitive Search rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
