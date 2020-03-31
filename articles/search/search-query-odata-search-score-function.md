---
title: Functiereferentie oData search.score
titleSuffix: Azure Cognitive Search
description: Syntaxis- en referentiedocumentatie voor het gebruik van de functie search.score in Azure Cognitive Search-query's.
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
ms.openlocfilehash: 2439d4f03184f8dbb85b229b3908dff95013b4bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74113136"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>OData, `search.score` functie in Azure Cognitive Search

Wanneer u een query naar Azure Cognitive Search verzendt zonder de [ **parameter $orderby,** ](search-query-odata-orderby.md)worden de resultaten die terugkomen in aflopende volgorde gesorteerd op relevantiescore. Zelfs wanneer u **$orderby**gebruikt, wordt de relevantiescore standaard gebruikt om banden te verbreken. Soms is het echter handig om de relevantiescore te gebruiken als een eerste sorteercriteria en enkele andere criteria als de tie-breaker. Met `search.score` de functie u dit doen.

## <a name="syntax"></a>Syntaxis

De syntaxis `search.score` voor `search.score()`in **$orderby** is . De `search.score` functie neemt geen parameters aan. Het kan worden `asc` gebruikt `desc` met de of sort-order specifier, net als elke andere clausule in de **$orderby** parameter. Het kan overal in de lijst met sorteercriteria worden weergegeven.

## <a name="example"></a>Voorbeeld

Sorteer hotels in `search.score` aflopende volgorde op en `rating`, en vervolgens in oplopende volgorde op afstand van de gegeven coördinaten, zodat tussen twee hotels met identieke beoordelingen, de dichtstbijzijnde wordt eerst vermeld:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Volgende stappen  

- [Overzicht van OData-expressietaal voor Azure Cognitive Search](query-odata-filter-orderby-syntax.md)
- [Syntaxisverwijzing oData-expressie voor Azure Cognitive Search](search-query-odata-syntax-reference.md)
- [Zoek documenten &#40;Azure Cognitive Search EST API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
