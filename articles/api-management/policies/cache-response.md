---
title: Voor beeld-API management-beleid-mogelijkheden toevoegen aan de back-end-service
titleSuffix: Azure API Management
description: 'Voor beeld van Azure API management-beleid: demonstreert hoe u mogelijkheden kunt toevoegen aan een back-end-service. Bijvoorbeeld, een naam van de locatie accepteren in plaats van de breedtegraad en lengtegraad in een weerprognose-API.'
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75442473"
---
# <a name="add-capabilities-to-a-backend-service"></a>Mogelijkheden toevoegen aan een back-end-service

In dit artikel wordt een voor beeld van een Azure API management-beleid weer gegeven waarin wordt getoond hoe u mogelijkheden kunt toevoegen aan een back-end-service. Bijvoorbeeld, een naam van de locatie accepteren in plaats van de breedtegraad en lengtegraad in een weerprognose-API. Volg de stappen die worden beschreven in [een beleid instellen of bewerken](../set-edit-policies.md)om een beleids code in te stellen of te bewerken. Zie voor andere voor beelden [beleids voorbeelden](../policy-samples.md).

## <a name="policy"></a>Beleid

Plak de code in het **binnenkomende** blok.

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over APIM-beleid:

+ [Transformatiebeleid](../api-management-transformation-policies.md)
+ [Voor beelden van beleid](../policy-samples.md)

