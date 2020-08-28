---
title: Objectgrenzen
description: Legt uit hoe ruimtelijke object grenzen kunnen worden opgevraagd
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.openlocfilehash: 099b0175b906e48034e663b575517e49bce7e753
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/27/2020
ms.locfileid: "89021847"
---
# <a name="object-bounds"></a>Objectgrenzen

Object grenzen vertegenwoordigen het volume dat een [entiteit](entities.md) en de onderliggende items innemen. Bij de externe rendering van Azure worden object grenzen altijd gegeven als aan elkaar *uitgelijnde selectie vakjes* (AABB). Object grenzen kunnen zich in *lokale ruimte* of in de *wereld ruimte*bevinden. In beide gevallen zijn ze altijd gecentreerd, wat betekent dat de gebieden en het volume kunnen verschillen tussen de lokale en wereld wijde ruimte weergave.

## <a name="querying-object-bounds"></a>Object grenzen opvragen

De lokale AABB van een [net](meshes.md) kan rechtstreeks vanuit de netresource worden opgevraagd. Deze grenzen kunnen worden omgezet in de lokale ruimte of wereld wijde ruimte van een entiteit met behulp van de trans formatie van de entiteit.

Het is mogelijk om de grenzen van een volledige object hiërarchie op deze manier te berekenen, maar dat moet de hiërarchie door lopen, query's uitvoeren op de grenzen voor elk net en ze hand matig combi neren. Deze bewerking is zowel omslachtig als inefficiënt.

Een betere manier is om een entiteit te bellen of aan te roepen `QueryLocalBoundsAsync` `QueryWorldBoundsAsync` . De berekening wordt vervolgens geoffload naar de server en geretourneerd met minimale vertraging.

```cs
private BoundsQueryAsync _boundsQuery = null;

public void GetBounds(Entity entity)
{
    _boundsQuery = entity.QueryWorldBoundsAsync();
    _boundsQuery.Completed += (BoundsQueryAsync bounds) =>
    {
        if (bounds.IsRanToCompletion)
        {
            Double3 aabbMin = bounds.Result.min;
            Double3 aabbMax = bounds.Result.max;
            // ...
        }
    };
}
```

```cpp
void GetBounds(ApiHandle<Entity> entity)
{
    ApiHandle<BoundsQueryAsync> boundsQuery = *entity->QueryWorldBoundsAsync();
    boundsQuery->Completed([](ApiHandle<BoundsQueryAsync> bounds)
    {
        if (bounds->GetIsRanToCompletion())
        {
            Double3 aabbMin = bounds->GetResult().min;
            Double3 aabbMax = bounds->GetResult().max;
            // ...
        }
    });
}
```

## <a name="next-steps"></a>Volgende stappen

* [Ruimtelijke query's](../overview/features/spatial-queries.md)
