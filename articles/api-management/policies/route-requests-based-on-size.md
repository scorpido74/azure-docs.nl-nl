---
title: Voorbeeld-API-beheerbeleid - Routeaanvraag op basis van de grootte van de berichttekst
titleSuffix: Azure API Management
description: Voorbeeld van azure API-beheerbeleid - toont aan hoe aanvragen kunnen worden routerd op basis van de grootte van hun instanties.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 8cee2c13386a076f0321619754468cfc1e9fb31c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442435"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>De aanvraag op basis van de grootte van de hoofdtekst routeren

In dit artikel wordt een voorbeeld van een Azure API-beheerbeleid weergegeven waarin wordt uitgelegd hoe aanvragen kunnen worden routerd op basis van de grootte van hun instanties. Als u een beleidscode wilt instellen of bewerken, volgt u de stappen die zijn beschreven in [Een beleid instellen of bewerken](../set-edit-policies.md). Zie [Beleidsvoorbeelden](../policy-samples.md)voor andere voorbeelden .

## <a name="policy"></a>Beleid

Plak de code in het **binnenkomende** blok.

[!code-xml[Main](../../../api-management-policy-samples/examples/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over APIM-beleid:

+ [Transformatiebeleid](../api-management-transformation-policies.md)
+ [Beleidsvoorbeelden](../policy-samples.md)

