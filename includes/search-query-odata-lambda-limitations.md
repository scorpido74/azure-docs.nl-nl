---
author: brjohnstmsft
ms.service: cognitive-search
ms.topic: include
ms.date: 06/13/2018
ms.author: brjohnst
ms.openlocfilehash: 998d0f1a84dc9cb2a07fb55286c1089787a263e1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80272608"
---
| Gegevenstype | Functies die zijn toegestaan in lambda-expressies met`any` | Functies die zijn toegestaan in lambda-expressies met`all` |
|---|---|---|
| `Collection(Edm.ComplexType)` | Alles behalve `search.ismatch` en`search.ismatchscoring` | Hetzelfde |
| `Collection(Edm.String)` | Vergelijkingen met `eq` of`search.in` <br/><br/> Combi neren van subexpressies met`or` | Vergelijkingen met `ne` of`not search.in()` <br/><br/> Combi neren van subexpressies met`and` |
| `Collection(Edm.Boolean)` | Vergelijkingen met `eq` of`ne` | Hetzelfde |
| `Collection(Edm.GeographyPoint)` | Gebruiken `geo.distance` met `lt` of`le` <br/><br/> `geo.intersects` <br/><br/> Combi neren van subexpressies met`or` | Gebruiken `geo.distance` met `gt` of`ge` <br/><br/> `not geo.intersects(...)` <br/><br/> Combi neren van subexpressies met`and` |
| `Collection(Edm.DateTimeOffset)`, `Collection(Edm.Double)`, `Collection(Edm.Int32)`, `Collection(Edm.Int64)` | `eq`Vergelijkingen met, `ne`, `lt` `gt` `le`,, of`ge` <br/><br/> Combi neren van vergelijkingen met andere subexpressies met`or` <br/><br/> Vergelijkingen combi neren, `ne` behalve met andere subexpressies met`and` <br/><br/> Expressies die gebruikmaken van `and` combi `or` Naties van en in [disjunctive normaal formulier (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form) | `eq`Vergelijkingen met, `ne`, `lt` `gt` `le`,, of`ge` <br/><br/> Combi neren van vergelijkingen met andere subexpressies met`and` <br/><br/> Vergelijkingen combi neren, `eq` behalve met andere subexpressies met`or` <br/><br/> Expressies die gebruikmaken van `and` combi `or` Naties van en in [Conjunctive normaal formulier (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form) |
