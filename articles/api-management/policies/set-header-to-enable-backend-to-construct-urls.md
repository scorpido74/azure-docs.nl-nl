---
title: Voor beeld van Azure API management-beleid-een doorgestuurde header toevoegen | Microsoft Docs
description: "Voor beeld van Azure API management-beleid: demonstreert hoe u een doorgestuurde header kunt toevoegen aan de inkomende aanvraag zodat de back-end-API juiste Url's kan maken."
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
ms.openlocfilehash: df72ae5e9a1471e1387539d2c89a1eca0b09d866
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078609"
---
# <a name="add-a-forwarded-header"></a>Een doorgestuurde header toevoegen

In dit artikel wordt een voor beeld van een Azure API management-beleid weer gegeven waarin wordt getoond hoe u een doorgestuurde header kunt toevoegen aan de inkomende aanvraag zodat de back-end-API juiste Url's kan maken. Volg de stappen die worden beschreven in [een beleid instellen of bewerken](../set-edit-policies.md)om een beleids code in te stellen of te bewerken. Zie voor andere voor beelden [beleids voorbeelden](../policy-reference.md).

## <a name="code"></a>Code

Plak de code in het **binnenkomende** blok.

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over APIM-beleid:

+ [Transformatiebeleid](../api-management-transformation-policies.md)
+ [Voor beelden van beleid](../policy-reference.md)