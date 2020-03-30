---
title: Verwijzing naar de georuimtelijke functie van OData
titleSuffix: Azure Cognitive Search
description: Syntaxis- en referentiedocumentatie voor het gebruik van odata-georuimtelijke functies, geo.distance en geo.intersects, in Azure Cognitive Search queries.
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
ms.openlocfilehash: 902996c1813931638012c78f81bd65c400bee7a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113174"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>Georuimtelijke functies van OData in `geo.distance` Azure Cognitive Search - en`geo.intersects`

Azure Cognitive Search ondersteunt georuimtelijke query's in `geo.distance` `geo.intersects` [OData-filterexpressies](query-odata-filter-orderby-syntax.md) via de en functies. De `geo.distance` functie retourneert de afstand in kilometers tussen twee punten, een is een veld of bereik variabele, en een is een constante doorgegeven als onderdeel van het filter. De `geo.intersects` functie `true` wordt geretourneerd als een bepaald punt zich binnen een bepaalde veelhoek bevindt, waarbij het punt een veld- of bereikvariabele is en de veelhoek wordt opgegeven als een constante die als onderdeel van het filter wordt doorgegeven.

De `geo.distance` functie kan ook worden gebruikt in de [ **parameter $orderby** ](search-query-odata-orderby.md) om zoekresultaten te sorteren op afstand van een bepaald punt. De syntaxis voor `geo.distance` in **$orderby** is dezelfde als in **$filter**. Bij `geo.distance` gebruik in **$orderby**moet het veld waarop `Edm.GeographyPoint` het van toepassing is van het type zijn en moet het ook **sorteerbaar**zijn .

> [!NOTE]
> Bij `geo.distance` gebruik in de **parameter $orderby** mag het veld dat u aan de functie doorgeeft slechts één geopunt bevatten. Met andere woorden, het `Edm.GeographyPoint` moet `Collection(Edm.GeographyPoint)`van het type zijn en niet. Het is niet mogelijk om te sorteren op verzamelingsvelden in Azure Cognitive Search.

## <a name="syntax"></a>Syntaxis

De volgende EBNF ([Extended Backus-Naur](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)Form `geo.distance` `geo.intersects` ) definieert de grammatica van de en functies, evenals de geo-ruimtelijke waarden waarop ze werken:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*
```

Er is ook een interactief syntaxisdiagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxisdiagram OData voor Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Zie [Syntaxisverwijzing oData-expressie voor Azure Cognitive Search](search-query-odata-syntax-reference.md) voor de volledige EBNF.

### <a name="geodistance"></a>geo.afstand

De `geo.distance` functie neemt twee `Edm.GeographyPoint` parameters `Edm.Double` van het type en geeft een waarde die de afstand tussen hen in kilometers. Dit verschilt van andere services die odata geo-ruimtelijke bewerkingen ondersteunen, die doorgaans afstanden in meters retourneren.

Een van de `geo.distance` parameters moet een aardrijkskundepuntconstante zijn en de andere moet een veldpad zijn (of een bereikvariabele `Collection(Edm.GeographyPoint)`in het geval van een filter dat over een veld van het type wordt getand). De volgorde van deze parameters maakt niet uit.

De constante van het aardrijkskundepunt is van de vorm `geography'POINT(<longitude> <latitude>)'`, waar de lengte en de breedtegraad numerieke constanten zijn.

> [!NOTE]
> Bij `geo.distance` gebruik in een filter moet u de door de `lt` `le`functie `gt`geretourneerde afstand vergelijken met een constante met behulp van , , of `ge`. De `eq` operatoren en `ne` worden niet ondersteund bij het vergelijken van afstanden. Dit is bijvoorbeeld een correct `geo.distance` `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5`gebruik van : .

### <a name="geointersects"></a>geo.intersects

De `geo.intersects` functie neemt een `Edm.GeographyPoint` variabele `Edm.GeographyPolygon` van het `Edm.Boolean`  --  `true` type en een constante en geeft `false` een als het punt is binnen de grenzen van de veelhoek, anders.

De veelhoek is een tweedimensionaal oppervlak dat is opgeslagen als een reeks punten die een grenzende ring definiëren (zie de [voorbeelden](#examples) hieronder). De veelhoek moet worden gesloten, wat betekent dat de eerste en laatste puntsets hetzelfde moeten zijn. [De punten in een veelhoek moeten tegen de klok in zijn.](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Geo-ruimtelijke query's en polygonen verspreid over de 180e meridiaan

Voor veel geo-ruimtelijke querybibliotheken is het formuleren van een query die de 180e meridiaan (in de buurt van de datumlijn) bevat, of is het verboden of vereist een tijdelijke oplossing, zoals het splitsen van de veelhoek in twee, één aan weerszijden van de meridiaan.

In Azure Cognitive Search werken georuimtelijke query's met 180-graden lengtegraad zoals verwacht als de queryvorm rechthoekig is en uw coördinaten `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'`worden uitgelijnd op een rasterindeling langs lengte en breedtegraad (bijvoorbeeld). Anders, voor niet-rechthoekige of niet-uitgelijnde vormen, overweeg dan de gesplitste veelhoekbenadering.  

### <a name="geo-spatial-functions-and-null"></a>Georuimtelijke functies en`null`

Net als alle andere niet-verzamelvelden in `Edm.GeographyPoint` Azure `null` Cognitive Search kunnen typevelden waarden bevatten. Wanneer Azure Cognitive `geo.intersects` Search evalueert `null`voor een veld `false`dat is, zal het resultaat altijd . Het gedrag `geo.distance` van in dit geval hangt af van de context:

- In filters `geo.distance` resulteert `null` van `null`een veld in . Dit betekent dat het `null` document niet overeenkomt omdat in `false`vergelijking met een niet-null-waarde evalueert tot .
- Bij het sorteren van `geo.distance` resultaten `null` met behulp van **$orderby**, van een veld resulteert in de maximaal mogelijke afstand. Documenten met een dergelijk veld sorteren lager dan `asc` alle andere wanneer de sorteerrichting wordt gebruikt `desc`(de standaardinstelling) en hoger dan alle andere wanneer de richting is .

## <a name="examples"></a>Voorbeelden

### <a name="filter-examples"></a>Voorbeelden van filter

Vind alle hotels binnen 10 kilometer van een bepaald referentiepunt `Edm.GeographyPoint`(waar de locatie is een veld van het type):

    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10

Vind alle hotels binnen een bepaalde viewport beschreven als een `Edm.GeographyPoint`veelhoek (waar de locatie is een veld van het type ). Houd er rekening mee dat de veelhoek is gesloten (de eerste en laatste puntsets moeten hetzelfde zijn) en [dat de punten tegen de klok in moeten worden vermeld.](https://docs.microsoft.com/rest/api/searchservice/supported-data-types#Anchor_1)

    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')

### <a name="order-by-examples"></a>Voorbeelden van bestelling en volgorde

Sorteer hotels `rating`aflopend op , dan oplopend op afstand van de opgegeven coördinaten:

    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

Sorteer hotels in `search.score` aflopende volgorde op en `rating`, en vervolgens in oplopende volgorde op afstand van de gegeven coördinaten, zodat tussen twee hotels met identieke beoordelingen, de dichtstbijzijnde wordt eerst vermeld:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Volgende stappen  

- [Filters in Azure Cognitive Search](search-filters.md)
- [Overzicht van OData-expressietaal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Syntaxisverwijzing oData-expressie voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoekdocumenten &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
