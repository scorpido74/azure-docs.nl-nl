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
ms.openlocfilehash: e525029ae8eab086d11126a4e18958423e207aa1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "70067508"
---
# <a name="add-a-forwarded-header"></a>Een doorgestuurde header toevoegen

In dit artikel wordt een voor beeld van een Azure API management-beleid weer gegeven waarin wordt getoond hoe u een doorgestuurde header kunt toevoegen aan de inkomende aanvraag zodat de back-end-API juiste Url's kan maken. Volg de stappen die worden beschreven in [een beleid instellen of bewerken](../set-edit-policies.md)om een beleids code in te stellen of te bewerken. Zie voor andere voor beelden [beleids voorbeelden](../policy-samples.md).

## <a name="code"></a>Code

Plak de code in het **binnenkomende** blok.

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over APIM-beleid:

+ [Transformatiebeleid](../api-management-transformation-policies.md)
+ [Voor beelden van beleid](../policy-samples.md)
