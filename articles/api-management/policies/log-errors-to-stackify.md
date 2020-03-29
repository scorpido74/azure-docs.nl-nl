---
title: Voorbeeld van API-beheerbeleid - Fouten naar Stackify verzenden voor logboekregistratie
titleSuffix: Azure API Management
description: Voorbeeld van azure API-beheerbeleid - toont aan hoe u een foutlogboekbeleid toevoegt om fouten naar Stackify te verzenden voor logboekregistratie..
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
ms.openlocfilehash: 6662761df005211729dffb16282b8e0a8e2a8444
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442444"
---
# <a name="send-errors-to-stackify-for-logging"></a>Fouten naar Stackify verzenden voor logboekregistratie

In dit artikel wordt een voorbeeld van een Azure API-beheerbeleids weergegeven waarin wordt uitgelegd hoe u een foutlogboekbeleid toevoegen om fouten naar Stackify te verzenden voor logboekregistratie. Als u een beleidscode wilt instellen of bewerken, volgt u de stappen die zijn beschreven in [Een beleid instellen of bewerken](../set-edit-policies.md). Zie [Beleidsvoorbeelden](../policy-samples.md)voor andere voorbeelden .

## <a name="policy"></a>Beleid

Plak de code in het blok **op de fout.**

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over APIM-beleid:

+ [Transformatiebeleid](../api-management-transformation-policies.md)
+ [Beleidsvoorbeelden](../policy-samples.md)

