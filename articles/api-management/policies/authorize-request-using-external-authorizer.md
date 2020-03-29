---
title: Voorbeeld-API-beheerbeleid - Aanvraag autoriseren met externe geautoriseerder
titleSuffix: Azure API Management
description: 'Voorbeeld van azure API-beheerbeleid : toont aan hoe u aanvragen autorisert met behulp van externe geautoriseerder die een aangepaste of verouderde verificatie/autorisatielogica inkapselt.'
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
ms.openlocfilehash: 99bf1068042eb7ab0c43e2a683ca7116d2e426f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442502"
---
# <a name="authorize-requests-using-external-authorizer"></a>Aanvragen via externe authorizer autoriseren

In dit artikel wordt een voorbeeld van een Azure API-beheerbeleid weergegeven waarin wordt uitgelegd hoe api-toegang kan worden beveiligd met behulp van een externe geautoriseerder die aangepaste verificatie-/autorisatielogica inkapselt. Als u een beleidscode wilt instellen of bewerken, volgt u de stappen die zijn beschreven in [Een beleid instellen of bewerken](../set-edit-policies.md). Zie [Beleidsvoorbeelden](../policy-samples.md)voor andere voorbeelden .

## <a name="policy"></a>Beleid

Plak de code in het **binnenkomende** blok.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over APIM-beleid:

+ [Beleid voor toegangsbeperkingen](../api-management-access-restriction-policies.md)
+ [Beleidsvoorbeelden](../policy-samples.md)
