---
title: Voorbeeld van Azure API-beheerbeleid - X-CSRF-patroon implementeren | Microsoft Documenten
description: Voorbeeld van azure API-beheerbeleid - laat zien hoe u x-CSRF-patroon implementeert dat door veel API's wordt gebruikt. Dit voorbeeld is specifiek voor SAP-Gateway.
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
ms.openlocfilehash: 14ea9113bf5712d6ffce356d02abb7224c21771a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70067729"
---
# <a name="implement-x-csrf-pattern"></a>X-CSRF-patroon implementeren

In dit artikel wordt een voorbeeld van een Azure API-beheerbeleid weergegeven waarin wordt uitgelegd hoe u x-CSRF-patroon implementeren dat door veel API's wordt gebruikt. Dit voorbeeld is specifiek voor SAP-Gateway. Als u een beleidscode wilt instellen of bewerken, volgt u de stappen die zijn beschreven in [Een beleid instellen of bewerken](../set-edit-policies.md). Zie [Beleidsvoorbeelden](../policy-samples.md)voor andere voorbeelden .

## <a name="policy"></a>Beleid

Plak de code in het **binnenkomende** blok.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get X-CSRF token from SAP gateway using send request.policy.xml)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over APIM-beleid:

+ [Transformatiebeleid](../api-management-transformation-policies.md)
+ [Beleidsvoorbeelden](../policy-samples.md)

