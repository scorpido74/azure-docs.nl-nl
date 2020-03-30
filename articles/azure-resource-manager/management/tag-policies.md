---
title: Beleid voor het taggen van resources
description: Beschrijft het Azure-beleid dat u toewijzen om naleving van de tag te garanderen.
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: e7febe4c833cefd0ee9a5c49b4b70f4901fea8d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80147016"
---
# <a name="assign-policies-for-tag-compliance"></a>Beleidsregels toewijzen voor naleving van tags

U gebruikt [Azure Policy](../../governance/policy/overview.md) om taggingregels en -conventies af te dwingen. Door een beleid te maken, voorkomt u het scenario dat resources worden geïmplementeerd voor uw abonnement en die niet over de verwachte tags voor uw organisatie beschikken. In plaats van handmatig tags toe te passen of te zoeken naar bronnen die niet voldoen, maakt u een beleid dat automatisch de benodigde tags toepast tijdens de implementatie. Tags kunnen nu ook worden toegepast op bestaande resources met het nieuwe [effect Wijzigen](../../governance/policy/concepts/effects.md#modify) en een [hersteltaak.](../../governance/policy/how-to/remediate-resources.md) In de volgende sectie ziet u voorbeeldbeleid voor tags.

## <a name="policies"></a>Beleidsregels

[!INCLUDE [Tag policies](../../../includes/azure-policy-samples-policies-tags.md)]

## <a name="next-steps"></a>Volgende stappen

* Zie Tags gebruiken om [uw Azure-bronnen te ordenen](tag-resources.md)voor meer informatie over het taggen van resources.
* Niet alle resourcetypen ondersteunen tags. Zie [Tagondersteuning voor Azure-bronnen](tag-support.md)als u wilt bepalen of u een tag op een resourcetype toepassen.
