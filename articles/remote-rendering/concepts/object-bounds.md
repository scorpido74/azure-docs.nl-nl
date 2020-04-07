---
title: Objectgrenzen
description: Legt uit hoe ruimtelijke objectgrenzen kunnen worden opgevraagd
author: florianborn71
ms.author: flborn
ms.date: 02/03/2020
ms.topic: conceptual
ms.openlocfilehash: 1d2dfdb203b05f2f6b7de740718d7407bd88066c
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681712"
---
# <a name="object-bounds"></a>Objectgrenzen

Objectgrenzen vertegenwoordigen het volume dat een [entiteit](entities.md) en haar kinderen bezetten. In Azure Remote Rendering worden objectgrenzen altijd weergegeven als *as uitgelijnde selectievakken* (AABB). Objectgrenzen kunnen zich in de *lokale ruimte* of in *de wereldruimte begeven.* Hoe dan ook, ze zijn altijd as-uitgelijnd, wat betekent dat de omvang en het volume kunnen verschillen tussen de lokale en wereld ruimte vertegenwoordiging.

## <a name="querying-object-bounds"></a>Objectgrenzen opvragen

De lokale AABB van een [net](meshes.md) kan rechtstreeks vanuit de mesh-bron worden opgevraagd. Deze grenzen kunnen worden omgezet in de lokale ruimte of wereldruimte van een entiteit met behulp van de transformatie van de entiteit.

Het is mogelijk om de grenzen van een hele objecthiërarchie op deze manier te berekenen, maar dat vereist het doorlopen van de hiërarchie, het opvragen van de grenzen voor elk net en deze handmatig combineren. Deze operatie is zowel vervelend als inefficiënt.

Een betere manier `QueryLocalBoundsAsync` is `QueryWorldBoundsAsync` om te bellen of op een entiteit. De berekening wordt vervolgens ontladen naar de server en met minimale vertraging geretourneerd.

``` cs
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

## <a name="next-steps"></a>Volgende stappen

* [Ruimtelijke query's](../overview/features/spatial-queries.md)
