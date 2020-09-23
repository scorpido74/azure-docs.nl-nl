---
title: Uw back-up regelen met behulp van Back-upcentrum
description: Meer informatie over het beheren van uw Azure-omgeving om ervoor te zorgen dat al uw resources compatibel zijn met een Back-upcentrum met backup Center.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 009ee461b0372a3fb73ffb3b0ee5151b77bd8ef8
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90995766"
---
# <a name="govern-your-backup-estate-using-backup-center"></a>Uw back-up regelen met behulp van Back-upcentrum

Back-upcentrum helpt u bij het beheren van uw Azure-omgeving om ervoor te zorgen dat al uw resources compatibel zijn met een back-upperspectief. Hieronder vindt u een aantal beheer mogelijkheden van Back-upcentrum:

* Azure-beleid voor back-ups weer geven en toewijzen

* Alle gegevens bronnen weer geven die niet zijn geconfigureerd voor back-up.

## <a name="supported-scenarios"></a>Ondersteunde scenario's

* Raadpleeg de [ondersteunings matrix](backup-center-support-matrix.md) voor een gedetailleerde lijst met ondersteunde en niet-ondersteunde scenario's.

## <a name="azure-policies-for-backup"></a>Azure-beleid voor back-up

Als u alle beschik bare [Azure-beleids regels](https://docs.microsoft.com/azure/governance/policy/overview) voor back-ups wilt weer geven, selecteert u het menu **-item Azure-beleids regels voor back-up** . Hiermee worden alle ingebouwde en aangepaste [Azure-beleids definities weer gegeven voor back-ups](policy-reference.md) die beschikbaar zijn voor toewijzing aan uw abonnementen en resource groepen.

Als u een van de definities selecteert, kunt u [het beleid toewijzen](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage#assign-a-policy) aan een bereik.

![Azure Policy definities selecteren](./media/backup-center-govern-environment/azure-policy-definitions.png)

## <a name="protectable-datasources"></a>Beveilig bare gegevens bronnen

Als u het menu-item **Beveilig bare gegevens bronnen** selecteert, kunt u alle gegevens bronnen weer geven die niet zijn geconfigureerd voor back-up. U kunt de lijst filteren op Data Source-abonnement, resource groep, locatie, type en tags. Zodra u een gegevens bron hebt geïdentificeerd waarvan een back-up moet worden gemaakt, kunt u met de rechter muisknop op het bijbehorende raster item klikken en **back-up** selecteren om een back-up te configureren voor de resource.

![Menu Beveilig bare gegevens bronnen](./media/backup-center-govern-environment/protectable-datasources.png)

## <a name="next-steps"></a>Volgende stappen

* [Back-ups controleren en uitvoeren](backup-center-monitor-operate.md)
* [Acties uitvoeren met behulp van Back-upcentrum](backup-center-actions.md)
* [Inzichten op uw back-ups verkrijgen](backup-center-obtain-insights.md)
