---
title: bestand opnemen
description: bestand opnemen
author: HeidiSteen
ms.service: cognitive-search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: ee430241173a6c19e2a32e176f28411631d9cb19
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80272900"
---
U kunt meerdere services binnen een abonnement maken. Elk abonnement kan worden ingericht op een specifieke laag. U bent alleen beperkt tot het aantal services dat per laag is toegestaan. U kunt bijvoorbeeld Maxi maal 12 Services maken in de laag basis en een andere 12 Services in de S1-laag binnen hetzelfde abonnement. Zie [een SKU of laag kiezen voor Azure Cognitive Search](../articles/search/search-sku-tier.md)voor meer informatie over lagen.

De maximale service limieten kunnen worden verhoogd op aanvraag. Als u meer services nodig hebt binnen hetzelfde abonnement, neemt u contact op met de ondersteuning van Azure.

| Resource            | Gratis<sup>1</sup> | Basic | S1  | S2 | S3 | S3 &nbsp; HD | L1 | L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Maximum aantal services    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Maximale schaal in zoek eenheden (SU)<sup>2</sup> |N.v.t. |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> vrij is gebaseerd op gedeelde, niet-toegewezen, resources. Omhoog schalen wordt niet ondersteund voor gedeelde resources.

<sup>2</sup> Zoek eenheden zijn facturerings eenheden, toegewezen als een *replica* of een *partitie*. U hebt beide resources nodig voor opslag-, indexerings-en query bewerkingen. Zie voor meer informatie over SU-berekeningen [resource niveaus schalen voor de werk belasting van query's en indexen](../articles/search/search-capacity-planning.md). 