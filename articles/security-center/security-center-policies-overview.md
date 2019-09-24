---
title: Instellingen voor Azure Security Center | Microsoft Docs
description: Azure Security Center-instellingen configureren.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: f24b1e4a-cc36-4542-b21e-041453cdfcd8
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/03/2018
ms.author: memildin
ms.openlocfilehash: 4a7254d4ac67ee7d1bf203baf5741638dbc8f3dd
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/23/2019
ms.locfileid: "71201607"
---
# <a name="security-center-settings"></a>Instellingen van Security Center
Dit artikel bevat een overzicht van de instellingen in Security Center.

De volgende instellingen kunnen worden bereikt onder beveiligings beleid:

- **Gegevens verzameling**: Bepaalt de instellingen voor agent inrichting en [gegevens verzameling](https://docs.microsoft.com/azure/security-center/security-center-enable-data-collection) .
- **Beveiligings beleid**: Hiermee wordt bepaald welke besturings elementen Security Center bewaakt en wordt aanbevolen. U kunt het [beveiligings beleid](tutorial-security-policy.md) bewerken in Security Center. U kunt [Azure Policy](tutorial-security-policy.md) ook gebruiken voor het maken van nieuwe definities, het definiëren van aanvullende beleids regels en het toewijzen van beleids regels voor verschillende beheer groepen. 
- **E-mail meldingen**: Hiermee worden de instellingen voor beveiligings contactpersonen en [e-mail meldingen](security-center-provide-security-contact-details.md) bepaald.
- **Prijscategorie**: Hiermee definieert u een vrije of standaard [prijs selectie](security-center-pricing.md). De laag die u kiest, bepaalt welke Security Center-functies beschikbaar zijn voor resources binnen het bereik. U kunt een laag opgeven voor abonnementen en werk ruimten.

> [!NOTE]
> U kunt al deze opties per abonnement instellen. Voor werk ruimten kunt u alleen gegevens verzameling en prijs categorie instellen.
>


## <a name="who-can-edit-security-policies"></a>Wie kan beveiligings beleid bewerken?
Security Center gebruikt op rollen gebaseerd Access Control (RBAC), dat ingebouwde rollen biedt die kunnen worden toegewezen aan gebruikers, groepen en services in Azure. Wanneer gebruikers Security Center openen, zien ze alleen informatie die betrekking heeft op de resources waartoe ze toegang hebben. Dit betekent dat gebruikers de rol van *eigenaar*, *bijdrager*of *lezer* hebben toegewezen aan het abonnement waartoe de resource behoort. Naast deze rollen zijn er twee specifieke Security Center-rollen:

- **Beveiligings lezer**: Beschikken over weergave rechten voor Security Center, waaronder aanbevelingen, waarschuwingen, beleid en status, maar ze kunnen geen wijzigingen aanbrengen.
- **Beveiligings beheerder**: Hebben dezelfde weergave rechten als de *beveiligings lezer*, en ze kunnen ook het beveiligings beleid bijwerken en aanbevelingen en waarschuwingen negeren.


## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd over het beveiligings beleid in Azure Security Center. Raadpleeg de volgende artikelen voor meer informatie over Azure Security Center:

* [Beveiligings beleid instellen in azure Security Center](tutorial-security-policy.md): Meer informatie over het configureren van beveiligings beleid voor uw Azure-abonnementen.
* [Aanbevelingen voor beveiliging in azure Security Center beheren](security-center-recommendations.md): Lees hoe u met Security Center aanbevelingen uw Azure-resources kunt beveiligen.
* [Beveiligings status controleren in azure Security Center](security-center-monitoring.md): Meer informatie over het controleren van de status van uw Azure-resources.
* [Beveiligings waarschuwingen beheren en erop reageren in azure Security Center](security-center-managing-and-responding-alerts.md): Informatie over het beheren van en reageren op beveiligingswaarschuwingen.
* [Partner oplossingen bewaken met Azure Security Center](security-center-partner-solutions.md): Meer informatie over het bewaken van de status van uw partneroplossingen.
* [Azure Security Center gegevens beveiliging](security-center-data-security.md): Meer informatie over het Security Center beheren en beveiligen van gegevens.
* [Azure Security Center Veelgestelde vragen](security-center-faq.md): Krijg antwoorden op veelgestelde vragen over het gebruik van de service.
* [Azure-beveiligings blog](https://blogs.msdn.com/b/azuresecurity/): Ontvang de meest recente Azure-beveiligings nieuws en-informatie.
