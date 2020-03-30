---
title: OData order-by referentie
titleSuffix: Azure Cognitive Search
description: Syntaxis- en taalnaslagdocumentatie voor het gebruik van order-by in Azure Cognitive Search-query's.
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
ms.openlocfilehash: 99ec639b88f3334530243242aadfa0ab52a40df0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113160"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>OData $orderby syntaxis in Azure Cognitive Search

 U de [parameter OData **$orderby** ](query-odata-filter-orderby-syntax.md) gebruiken om een aangepaste sorteervolgorde toe te passen voor zoekresultaten in Azure Cognitive Search. In dit artikel worden de syntaxis van **$orderby** in detail beschreven. Zie [Hoe u met zoekresultaten werken in Azure Cognitive Search](search-pagination-page-layout.md)voor meer algemene informatie over het gebruik van **$orderby** bij het presenteren van zoekresultaten.

## <a name="syntax"></a>Syntaxis

De **parameter $orderby** accepteert een door komma's gescheiden lijst van maximaal 32 **order-door-clausules**. De syntaxis van een order-by-clausule wordt beschreven door het volgende EBNF ([Extended Backus-Naur Form):](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

Er is ook een interactief syntaxisdiagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxisdiagram OData voor Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Zie [Syntaxisverwijzing oData-expressie voor Azure Cognitive Search](search-query-odata-syntax-reference.md) voor de volledige EBNF.

Elke clausule heeft sorteercriteria, eventueel gevolgd`asc` door een `desc` sorteerrichting (voor oplopend of aflopend). Als u geen richting opgeeft, wordt de standaardinstelling oplopend. De sorteercriteria kunnen het pad `sortable` van een veld [`geo.distance`](search-query-odata-geo-spatial-functions.md) of [`search.score`](search-query-odata-search-score-function.md) een aanroep naar de of de functies zijn.

Als meerdere documenten dezelfde sorteercriteria `search.score` hebben en de functie niet wordt gebruikt `Rating` (bijvoorbeeld als u sorteert op een numeriek veld en drie documenten hebben allemaal een classificatie van 4), worden de banden in aflopende volgorde verbroken door documentscore. Wanneer documentscores hetzelfde zijn (bijvoorbeeld wanneer er geen zoekopdracht in volledige tekst is opgegeven in de aanvraag), is de relatieve volgorde van de gekoppelde documenten onbepaald.

U meerdere sorteercriteria opgeven. De volgorde van expressies bepaalt de uiteindelijke sorteervolgorde. Als u bijvoorbeeld aflopend wilt sorteren op score, `$orderby=search.score() desc,Rating desc`gevolgd door Beoordeling, is de syntaxis .

De syntaxis voor `geo.distance` in **$orderby** is dezelfde als in **$filter**. Bij `geo.distance` gebruik in **$orderby**moet het veld waarop `Edm.GeographyPoint` het van `sortable`toepassing is van het type zijn en moet het ook zijn .

De syntaxis `search.score` voor `search.score()`in **$orderby** is . De `search.score` functie neemt geen parameters.

## <a name="examples"></a>Voorbeelden

Sorteer hotels die stijgen op basistarief:

    $orderby=BaseRate asc

Sorteer hotels die aflopend op beoordeling en vervolgens stijgend op basistarief (vergeet niet dat opgaan de standaard is):

    $orderby=Rating desc,BaseRate

Sorteer hotels die aflopend op beoordeling en vervolgens oplopend op afstand van de opgegeven coördinaten:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

Sorteer hotels in aflopende volgorde op search.score en rating, en vervolgens in oplopende volgorde op afstand van de opgegeven coördinaten. Tussen twee hotels met identieke relevantiescores en beoordelingen wordt de dichtstbijzijnde als eerste vermeld:

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Volgende stappen  

- [Werken met zoekresultaten in Azure Cognitive Search](search-pagination-page-layout.md)
- [Overzicht van OData-expressietaal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Syntaxisverwijzing oData-expressie voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoekdocumenten &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
