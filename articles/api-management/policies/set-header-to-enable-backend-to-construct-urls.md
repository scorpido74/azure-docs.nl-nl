---
title: Voorbeeld van azure API-beheerbeleid - Een doorgestuurde koptekst toevoegen | Microsoft Documenten
description: Voorbeeld van azure API-beheerbeleid - toont aan hoe u een doorgestuurde koptekst toevoegt aan de binnenkomende aanvraag om de backend-API de juiste URL's te laten construeren.
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
ms.openlocfilehash: e525029ae8eab086d11126a4e18958423e207aa1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70067508"
---
# <a name="add-a-forwarded-header"></a>Een doorgestuurde koptekst toevoegen

In dit artikel wordt een voorbeeld van een Azure API-beheerbeleid weergegeven waarin wordt uitgelegd hoe u een doorgestuurde koptekst toevoegt aan de binnenkomende aanvraag om de backend-API toe te staan de juiste URL's te construeren. Als u een beleidscode wilt instellen of bewerken, volgt u de stappen die zijn beschreven in [Een beleid instellen of bewerken](../set-edit-policies.md). Zie [Beleidsvoorbeelden](../policy-samples.md)voor andere voorbeelden .

## <a name="code"></a>Code

Plak de code in het **binnenkomende** blok.

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over APIM-beleid:

+ [Transformatiebeleid](../api-management-transformation-policies.md)
+ [Beleidsvoorbeelden](../policy-samples.md)
