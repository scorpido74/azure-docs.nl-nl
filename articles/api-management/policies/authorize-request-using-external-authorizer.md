---
title: Voor beeld van API management-beleid-aanvraag toestaan met externe goed keurder
titleSuffix: Azure API Management
description: 'Voor beeld van Azure API management-beleid: demonstreert hoe aanvragen worden geautoriseerd met een externe autorisatie die een aangepaste of verouderde logica voor verificatie/autorisatie inkapselt.'
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: e38d92a13c9a66defc2d5090990b44a889cfd21c
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076229"
---
# <a name="authorize-requests-using-external-authorizer"></a>Aanvragen via externe authorizer autoriseren

In dit artikel wordt een voor beeld gegeven van een Azure API management-beleid dat laat zien hoe u de toegang tot de API kunt beveiligen met behulp van een externe autoriseer die aangepaste verificatie/autorisatie logica inkapselt. Volg de stappen die worden beschreven in [een beleid instellen of bewerken](../set-edit-policies.md)om een beleids code in te stellen of te bewerken. Zie voor andere voor beelden [beleids voorbeelden](../policy-reference.md).

## <a name="policy"></a>Beleid

Plak de code in het **binnenkomende** blok.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over APIM-beleid:

+ [Beleid voor toegangs beperkingen](../api-management-access-restriction-policies.md)
+ [Voor beelden van beleid](../policy-reference.md)