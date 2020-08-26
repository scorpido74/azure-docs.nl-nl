---
title: Problemen met kostenweergaven van Azure Enterprise oplossen
description: Leer hier meer over het oplossen van eventuele problemen met kostenweergaven van een organisatie in de Azure-portal.
author: bandersmsft
ms.reviewer: amberb
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: troubleshooting
ms.date: 08/20/2019
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: bd7f1ff20189b971ba32e5c4b40b380fc22611b2
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88686850"
---
# <a name="troubleshoot-enterprise-cost-views"></a>Problemen met kostenweergaven van Enterprise oplossen

Enterprise-inschrijvingen hebben verschillende instellingen die tot gevolg kunnen hebben dat gebruikers geen kosten zien binnen de inschrijving.  Deze instellingen worden beheerd door de inschrijvingsbeheerder. Als de inschrijving niet rechtstreeks via Microsoft is gekocht, worden de instellingen beheerd door de partner.  Dit artikel beschrijft wat deze instellingen zijn en hoe deze van invloed zijn op de inschrijving. Deze instellingen staan los van de Azure-rollen.

## <a name="enable-access-to-costs"></a>Toegang tot kosten inschakelen

Ziet u een bericht dat u niet bent geautoriseerd of dat *kostenweergaven zijn uitgeschakeld in uw inschrijving* wanneer u kosteninformatie wilt bekijken?
![Schermopname met 'Niet-geautoriseerd' in het veld Huidige kosten voor abonnement.](./media/enterprise-mgmt-grp-troubleshoot-cost-view/unauthorized.png)

Als u geen kosten ziet, kan dit een van de volgende oorzaken hebben:

1. U hebt Azure gekocht via een Enterprise-partner en de partner heeft nog geen prijsgegevens gepubliceerd. Neem contact op met uw partner om de prijsinstelling in de [Enterprise-portal](https://ea.azure.com) bij te werken.
2. Als u een EA Direct-klant bent, zijn er een aantal mogelijkheden:
    * U bent een accounteigenaar en uw inschrijvingsbeheerder heeft de instelling **AO view charges** uitgeschakeld.  
    * U bent een afdelingsbeheerder en uw inschrijvingsbeheerder heeft de instelling **DA view charges** uitgeschakeld.
    * Neem contact op met uw inschrijvingsbeheerder voor toegang. De inschrijvingsbeheerder kan de instellingen bijwerken in de [Enterprise-portal](https://ea.azure.com/manage/enrollment).

      ![Schermopname met de instellingen voor kostenweergave in de Enterprise-portal.](./media/enterprise-mgmt-grp-troubleshoot-cost-view/ea-portal-settings.png)

## <a name="asset-is-unavailable"></a>Asset is niet beschikbaar

Als er een foutbericht wordt weergegeven dat een **asset niet beschikbaar** is wanneer u probeert een abonnement of beheergroep te openen, hebt u niet de juiste rol om dit item weer te geven.  

![Schermopname met het bericht dat een asset niet beschikbaar is.](./media/enterprise-mgmt-grp-troubleshoot-cost-view/asset-not-found.png)

Vraag uw Azure-abonnementsbeheerder of de beheerder van beheergroep om toegang. Zie [Toegang beheren met behulp van RBAC en de Azure-portal](../../role-based-access-control/role-assignments-portal.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen
- Als u vragen hebt of hulp nodig hebt, [kunt u een ondersteuningsaanvraag maken](https://go.microsoft.com/fwlink/?linkid=2083458).
