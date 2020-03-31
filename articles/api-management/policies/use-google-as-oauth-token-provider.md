---
title: Voorbeeld-API-beheerbeleid - Toegang autoriseren met Google OAuth-token
titleSuffix: Azure API Management
description: Voorbeeld van azure API-beheerbeleid - Laat zien hoe u toegang tot uw eindpunten autoriseren met Google als OAuth-tokenprovider.
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
ms.openlocfilehash: d606d29d84cd5917c74efe188ae02627ad55d4ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442377"
---
# <a name="authorize-access-using-google-oauth-token"></a>Toestaan van toegang met behulp van Google-OAuth-token

In dit artikel wordt een voorbeeld van een Azure API-beheerbeleid weergegeven waarin wordt uitgelegd hoe u de toegang tot uw eindpunten autoriseren met Google als OAuth-tokenprovider. Als u een beleidscode wilt instellen of bewerken, volgt u de stappen die zijn beschreven in [Een beleid instellen of bewerken](../set-edit-policies.md). Zie [Beleidsvoorbeelden](../policy-samples.md)voor andere voorbeelden .

## <a name="policy"></a>Beleid

Plak de code in het **binnenkomende** blok.

[!code-xml[Main](../../../api-management-policy-samples/examples/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over APIM-beleid:

+ [Transformatiebeleid](../api-management-transformation-policies.md)
+ [Beleidsvoorbeelden](../policy-samples.md)

