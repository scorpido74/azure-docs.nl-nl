---
title: 'Voor beeld van Azure API management-beleid: de aanvraag routeren op basis van de grootte van de hoofd tekst | Microsoft Docs'
description: 'Voor beeld van Azure API management-beleid: laat zien hoe u aanvragen routeert op basis van de grootte van hun instanties.'
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
ms.openlocfilehash: f8f282597004dc73d9fe0f49bf4a41e6a80fc37f
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072010"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>De aanvraag door sturen op basis van de grootte van de hoofd tekst

In dit artikel wordt een voor beeld van een Azure API management-beleid weer gegeven waarin wordt getoond hoe u aanvragen routeert op basis van de grootte van hun eigen organisaties. Volg de stappen die worden beschreven in [een beleid instellen of bewerken](../set-edit-policies.md)om een beleids code in te stellen of te bewerken. Zie voor andere voor beelden [beleids voorbeelden](../policy-samples.md).

## <a name="policy"></a>Beleid

Plak de code in het **binnenkomende** blok.

[!code-xml[Main](../../../api-management-policy-samples/examples/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over APIM-beleid:

+ [Transformatiebeleid](../api-management-transformation-policies.md)
+ [Voor beelden van beleid](../policy-samples.md)

