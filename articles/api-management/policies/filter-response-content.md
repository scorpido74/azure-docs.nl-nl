---
title: Voorbeeld van azure API-beheerbeleid - Inhoud voor filterrespons | Microsoft Documenten
description: Voorbeeld van azure API-beheerbeleid - toont aan hoe u gegevenselementen uit de responspayload filtert op basis van het product dat aan de aanvraag is gekoppeld.
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
ms.openlocfilehash: 462db76b06e5071571ae475d420a627e57dad92e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70067761"
---
# <a name="filter-response-content"></a>Antwoordinhoud filteren

In dit artikel wordt een voorbeeld van een Azure API-beheerbeleidsvoorbeeld weergegeven waarin wordt uitgelegd hoe gegevenselementen uit de responspayload kunnen worden gefilterd op basis van het product dat aan de aanvraag is gekoppeld. Als u een beleidscode wilt instellen of bewerken, volgt u de stappen die zijn beschreven in [Een beleid instellen of bewerken](../set-edit-policies.md). Zie [Beleidsvoorbeelden](../policy-samples.md)voor andere voorbeelden .

## <a name="policy"></a>Beleid

Plak de code in het **uitgaande** blok.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter response content based on product name.policy.xml)]

## <a name="next-steps"></a>Volgende stappen

Meer informatie over APIM-beleid:

+ [Transformatiebeleid](../api-management-transformation-policies.md)
+ [Beleidsvoorbeelden](../policy-samples.md)

