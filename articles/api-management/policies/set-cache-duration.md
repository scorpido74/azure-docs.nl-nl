---
title: Voor beeld-API management-beleid-de duur van de antwoord cache instellen
titleSuffix: Azure API Management
description: 'Voor beeld van Azure API management-beleid: demonstreert hoe u de cache duur van de respons instelt met behulp van maxAge-waarde in Cache-Control header die door de back-end wordt verzonden.'
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
ms.openlocfilehash: 3101c5695272e8fa6b577ad313897cbc1fa29629
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75442399"
---
# <a name="set-response-cache-duration"></a>Duur antwoordcache instellen

In dit artikel wordt een voor beeld gegeven van een Azure API management-beleid dat laat zien hoe u de respons cache duur instelt met behulp van maxAge-waarde in Cache-Control header die door de back-end wordt verzonden. Volg de stappen die worden beschreven in [een beleid instellen of bewerken](../set-edit-policies.md)om een beleids code in te stellen of te bewerken. Zie voor andere voor beelden [beleids voorbeelden](../policy-samples.md).

## <a name="policy"></a>Beleid

Plak de code in het **binnenkomende** blok.

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over APIM-beleid:

+ [Transformatiebeleid](../api-management-transformation-policies.md)
+ [Voor beelden van beleid](../policy-samples.md)

