---
author: brjohnstmsft
ms.service: cognitive-search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: 998d0f1a84dc9cb2a07fb55286c1089787a263e1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "80272608"
---
| Gegevenstype | Functies toegestaan in lambda-expressies met`any` | Functies toegestaan in lambda-expressies met`all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | Alles `search.ismatch` behalve en`search.ismatchscoring` | Hetzelfde |
| `Collection(Edm.String)` | Vergelijkingen `eq` met of`search.in` <br/><br/> Subexpressies combineren met`or` | Vergelijkingen `ne` met of`not search.in()` <br/><br/> Subexpressies combineren met`and` |
| `Collection(Edm.Boolean)` | Vergelijkingen `eq` met of`ne` | Hetzelfde |
| `Collection(Edm.GeographyPoint)` | Gebruiken `geo.distance` `lt` met of`le` <br/><br/> `geo.intersects` <br/><br/> Subexpressies combineren met`or` | Gebruiken `geo.distance` `gt` met of`ge` <br/><br/> `not geo.intersects(...)` <br/><br/> Subexpressies combineren met`and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | Vergelijkingen `eq`met `ne` `lt`behulp `gt` `le`van , , , , of`ge` <br/><br/> Vergelijkingen combineren met andere subexpressies met behulp van`or` <br/><br/> Vergelijkingen combineren, `ne` behalve met andere subexpressies met`and` <br/><br/> Expressies met `and` `or` combinaties van en in [Disjunctief Normale Vorm (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) | Vergelijkingen `eq`met `ne` `lt`behulp `gt` `le`van , , , , of`ge` <br/><br/> Vergelijkingen combineren met andere subexpressies met behulp van`and` <br/><br/> Vergelijkingen combineren, `eq` behalve met andere subexpressies met`or` <br/><br/> Expressies met `and` `or` combinaties van en in [conjunctief normale vorm (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
