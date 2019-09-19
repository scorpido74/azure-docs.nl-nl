---
title: bestand opnemen
description: bestand opnemen
services: search
author: HeidiSteen
ms.service: search
ms.topic: include
ms.date: 05/06/2019
ms.author: heidist
ms.custom: include file
ms.openlocfilehash: 1e147e8bd9260cd1ece60b70641968a229995ec1
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/18/2019
ms.locfileid: "67176414"
---
U kunt meerdere services binnen een abonnement maken. Elk abonnement kan worden ingericht op een specifieke laag. U bent alleen beperkt tot het aantal services dat per laag is toegestaan. U kunt bijvoorbeeld Maxi maal 12 Services maken in de laag basis en een andere 12 Services in de S1-laag binnen hetzelfde abonnement. Zie [een SKU of laag kiezen voor Azure Search](../articles/search/search-sku-tier.md)voor meer informatie over lagen.

De maximale service limieten kunnen worden verhoogd op aanvraag. Als u meer services nodig hebt binnen hetzelfde abonnement, neemt u contact op met de ondersteuning van Azure.

| Resource            | Gratis<sup>1</sup> | Basic | S1  | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| ------------------- | ---- | ----- | --- | -- | -- | ----- | -- | -- |
| Maximum aantal services    |1     | 16    | 16  | 8  | 6  | 6     | 6  | 6  |
| Maximale schaal in zoek eenheden (SU)<sup>2</sup> |N/A |3 SU |36 SU |36 SU |36 SU |36 SU |36 SU |36 SU |

<sup>1</sup> vrij is gebaseerd op gedeelde, niet-toegewezen, resources. Omhoog schalen wordt niet ondersteund voor gedeelde resources.

<sup>2</sup> Zoek eenheden zijn facturerings eenheden, toegewezen als een *replica* of een *partitie*. U hebt beide resources nodig voor opslag-, indexerings-en query bewerkingen. Zie voor meer informatie over SU-berekeningen [resource niveaus schalen voor de werk belasting van query's en indexen](../articles/search/search-capacity-planning.md). 