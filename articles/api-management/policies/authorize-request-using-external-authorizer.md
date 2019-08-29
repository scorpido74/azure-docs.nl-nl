---
title: Voor beeld van Azure API management-beleid-aanvraag toestaan met externe goed keurder | Microsoft Docs
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
ms.openlocfilehash: 92836e0bfe43a41ad6547c68bc0b9a326528862c
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074157"
---
# <a name="authorize-requests-using-external-authorizer"></a>Aanvragen met externe autorisatie toestaan

In dit artikel wordt een voor beeld gegeven van een Azure API management-beleid dat laat zien hoe u de toegang tot de API kunt beveiligen met behulp van een externe autoriseer die aangepaste verificatie/autorisatie logica inkapselt. Volg de stappen die worden beschreven in [een beleid instellen of bewerken](../set-edit-policies.md)om een beleids code in te stellen of te bewerken. Zie voor andere voor beelden [beleids voorbeelden](../policy-samples.md).

## <a name="policy"></a>Beleid

Plak de code in het **binnenkomende** blok.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over APIM-beleid:

+ [Beleid voor toegangs beperkingen](../api-management-access-restriction-policies.md)
+ [Voor beelden van beleid](../policy-samples.md)
