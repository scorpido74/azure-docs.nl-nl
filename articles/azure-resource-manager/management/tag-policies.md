---
title: Beleid voor het labelen van resources
description: Hierin worden de Azure-beleids regels beschreven die u kunt toewijzen om label compatibiliteit te garanderen.
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: e3eeb28ea23b18c3492f68d2fac294fc014420c5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82147854"
---
# <a name="assign-policies-for-tag-compliance"></a>Beleid voor naleving van labels toewijzen

U gebruikt [Azure Policy](../../governance/policy/overview.md) om regels en conventies voor labelen af te dwingen. Door een beleid te maken, vermijdt u het scenario van resources die worden geïmplementeerd in uw abonnement en die niet de verwachte Tags voor uw organisatie hebben. In plaats van labels hand matig toe te passen of te zoeken naar resources die niet compatibel zijn, maakt u een beleid waarmee automatisch de benodigde Tags tijdens de implementatie worden toegepast. Labels kunnen nu ook worden toegepast op bestaande resources met het nieuwe [wijzigings](../../governance/policy/concepts/effects.md#modify) effect en een [herstel taak](../../governance/policy/how-to/remediate-resources.md). In de volgende sectie ziet u voor beelden van beleids regels voor Tags.

## <a name="policies"></a>Beleidsregels

[!INCLUDE [Tag policies](../../../includes/policy/samples/bycat/policies-tags.md)]

## <a name="next-steps"></a>Volgende stappen

* Zie [Tags gebruiken om uw Azure-resources te organiseren](tag-resources.md)voor meer informatie over het labelen van resources.
* Niet alle resource typen ondersteunen Tags. Zie [tag-ondersteuning voor Azure-resources](tag-support.md)om te bepalen of u een tag kunt Toep assen op een resource type.
