---
title: Voorbeeld van API-beheerbeleid - Contextgegevens van aanvragen verzenden naar backendservice
titleSuffix: Azure API Management
description: 'Voorbeeld van azure API-beheerbeleid : toont aan hoe u contextgegevens van aanvragen naar de backendservice verzendt.'
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
ms.openlocfilehash: 8cef989ca7ce8ee649c4f4aeb579c43bf8a8d6ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442410"
---
# <a name="send-request-context-information-to-the-backend-service"></a>Aanvraag contextinformatie sturen naar de back-endservice

In dit artikel wordt een voorbeeld van een Azure API-beheerbeleidsvoorbeeld weergegeven waarin wordt uitgelegd hoe u contextgegevens van aanvragen naar de backendservice verzenden. Als u een beleidscode wilt instellen of bewerken, volgt u de stappen die zijn beschreven in [Een beleid instellen of bewerken](../set-edit-policies.md). Zie [Beleidsvoorbeelden](../policy-samples.md)voor andere voorbeelden .

## <a name="policy"></a>Beleid

Plak de code in het **binnenkomende** blok.

[!code-xml[Main](../../../api-management-policy-samples/examples/Send request context information to the backend service.policy.xml)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over APIM-beleid:

+ [Transformatiebeleid](../api-management-transformation-policies.md)
+ [Beleidsvoorbeelden](../policy-samples.md)

