---
title: OData Search. Score functie referentie-Azure Search
description: OData Search. Score-functie in Azure Search query's.
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
ms.openlocfilehash: b6bf56a61ca685b306a15e474623336216ba531b
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2019
ms.locfileid: "69647520"
---
# <a name="odata-searchscore-function-in-azure-search"></a>OData `search.score` -functie in azure Search

Wanneer u een query verzendt naar Azure Search zonder de [para meter **$OrderBy** ](search-query-odata-orderby.md), worden de resultaten die terugkomen in aflopende volg orde gesorteerd op relevantie score. Zelfs wanneer u **$OrderBy**gebruikt, wordt de relevantie score standaard gebruikt om bindingen te verstoren. Soms is het echter handig om de relevantie score te gebruiken als eerste Sorteer criterium en enkele andere criteria als de afbreeker. Met `search.score` de functie kunt u dit doen.

## <a name="syntax"></a>Syntaxis

De syntaxis voor `search.score` in **$OrderBy** is `search.score()`. De functie `search.score` voert geen para meters uit. Deze kan worden gebruikt in combi `asc` natie `desc` met de specificatie of de sorteer volgorde, net als bij elke andere component in de para meter **$OrderBy** . Dit kan ergens in de lijst met Sorteer criteria worden weer gegeven.

## <a name="example"></a>Voorbeeld

Sorteer hotels in aflopende Volg `search.score` orde `rating`op en, en vervolgens in oplopende volg orde op afstand van de opgegeven coördinaten, zodat tussen twee hotels met een identieke classificatie, het dichtstbijzijnde item bovenaan wordt weer gegeven:

    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc

## <a name="next-steps"></a>Volgende stappen  

- [Overzicht van de OData-expressie taal voor Azure Search](query-odata-filter-orderby-syntax.md)
- [Verwijzing naar de syntaxis van de OData-expressie voor Azure Search](search-query-odata-syntax-reference.md)
- [Zoeken naar &#40;documenten Azure Search service rest API&#41;](https://docs.microsoft.com/rest/api/searchservice/Search-Documents)
