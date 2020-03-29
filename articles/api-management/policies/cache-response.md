---
title: Voorbeeld van API-beheerbeleid - Mogelijkheden toevoegen aan backendservice
titleSuffix: Azure API Management
description: Voorbeeld van azure API-beheerbeleid - toont aan hoe u mogelijkheden toevoegt aan een backendservice. Bijvoorbeeld, een naam van de locatie accepteren in plaats van de breedtegraad en lengtegraad in een weerprognose-API.
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
ms.openlocfilehash: e286f77009a17870332ed5caf1c66c18dd21d020
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442473"
---
# <a name="add-capabilities-to-a-backend-service"></a>Mogelijkheden toevoegen aan een backendservice

In dit artikel wordt een voorbeeld van een Azure API-beheerbeleidsvoorbeeld weergegeven waarin wordt uitgelegd hoe u mogelijkheden toevoegt aan een backendservice. Bijvoorbeeld, een naam van de locatie accepteren in plaats van de breedtegraad en lengtegraad in een weerprognose-API. Als u een beleidscode wilt instellen of bewerken, volgt u de stappen die zijn beschreven in [Een beleid instellen of bewerken](../set-edit-policies.md). Zie [Beleidsvoorbeelden](../policy-samples.md)voor andere voorbeelden .

## <a name="policy"></a>Beleid

Plak de code in het **binnenkomende** blok.

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over APIM-beleid:

+ [Transformatiebeleid](../api-management-transformation-policies.md)
+ [Beleidsvoorbeelden](../policy-samples.md)

