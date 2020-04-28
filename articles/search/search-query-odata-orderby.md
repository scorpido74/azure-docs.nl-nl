---
title: OData-order-by-verwijzing
titleSuffix: Azure Cognitive Search
description: Documentatie over syntaxis en naslag informatie voor het gebruik van order-by in azure Cognitive Search query's.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "74113160"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>OData-$orderby syntaxis in azure Cognitive Search

 U kunt de [OData- **$OrderBy** para meter](query-odata-filter-orderby-syntax.md) gebruiken om een aangepaste sorteer volgorde voor zoek resultaten in azure Cognitive Search toe te passen. In dit artikel wordt de syntaxis van **$OrderBy** uitvoerig beschreven. Zie [How to work with Search Results in Azure Cognitive Search](search-pagination-page-layout.md)voor meer algemene informatie over het gebruik van **$OrderBy** bij het presen teren van zoek resultaten.

## <a name="syntax"></a>Syntaxis

De para meter **$OrderBy** accepteert een door komma's gescheiden lijst met maxi maal 32 **order by-componenten**. De syntaxis van een component order-by wordt beschreven door de volgende EBNF ([Extended Backus-Naur Form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

Er is ook een interactief syntaxis diagram beschikbaar:

> [!div class="nextstepaction"]
> [Syntaxis diagram van OData voor Azure Cognitive Search](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Zie [OData-expressie syntaxis referentie voor Azure Cognitive Search](search-query-odata-syntax-reference.md) voor de volledige ebnf.

Elke component heeft Sorteer criteria, eventueel gevolgd door een sorteer richting (`asc` voor oplopend of `desc` aflopend). Als u geen richting opgeeft, wordt de standaard waarde Oplopend. De sorteer criteria kunnen het pad van een `sortable` veld zijn of een aanroep van de [`geo.distance`](search-query-odata-geo-spatial-functions.md) -of- [`search.score`](search-query-odata-search-score-function.md) functies.

Als meerdere documenten dezelfde Sorteer criteria hebben en de functie `search.score` niet wordt gebruikt (bijvoorbeeld als u op een numeriek `Rating` veld sorteert en drie documenten met een classificatie van vier), worden de punten door de document Score in aflopende volg orde afgebroken. Wanneer de document scores hetzelfde zijn (bijvoorbeeld wanneer er geen zoek opdracht voor volledige tekst in de aanvraag is opgegeven), is de relatieve volg orde van de gekoppelde documenten onbepaald.

U kunt meerdere Sorteer criteria opgeven. De volg orde van expressies bepaalt de uiteindelijke sorteer volgorde. Als u bijvoorbeeld aflopend op Score wilt sorteren, gevolgd door classificatie, zou de syntaxis `$orderby=search.score() desc,Rating desc`zijn.

De syntaxis voor `geo.distance` in **$OrderBy** is hetzelfde als in **$filter**. Wanneer u `geo.distance` in **$OrderBy**gebruikt, moet het veld waarop het van toepassing is, `Edm.GeographyPoint` van het type zijn en `sortable`het moet ook zijn.

De syntaxis voor `search.score` in **$OrderBy** is `search.score()`. De functie `search.score` voert geen para meters uit.

## <a name="examples"></a>Voorbeelden

Hotels sorteren oplopend op basis van base-rate:

    $orderby=BaseRate asc

Hotels sorteren aflopend op waardering en vervolgens oplopend per basis tarief (Houd er rekening mee dat oplopend is de standaard instelling):

    $orderby=Rating desc,BaseRate

Hotels sorteren aflopend op waardering en vervolgens oplopend op afstand van de opgegeven coördinaten:

    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

Sorteer hotels in aflopende volg orde door te zoeken. Score en waardering en vervolgens in oplopende volg orde op afstand van de opgegeven coördinaten. Tussen twee hotels met identieke relevantie scores en beoordelingen wordt het dichtstbijzijnde item als eerste weer gegeven:

    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Volgende stappen  

- [Werken met zoek resultaten in azure Cognitive Search](search-pagination-page-layout.md)
- [Overzicht van de OData-expressie taal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Naslag informatie voor de syntaxis van OData-expressies voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoeken naar documenten &#40;Azure Cognitive Search REST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
