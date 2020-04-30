---
title: Geef beveiligings contact gegevens op in Azure Security Center | Microsoft Docs
description: In dit document wordt beschreven hoe u contact gegevens van de beveiliging kunt opgeven in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/09/2019
ms.author: memildin
ms.openlocfilehash: 08ad761e81909e6ab23c7c07f5ce05865136bc47
ms.sourcegitcommit: 67bddb15f90fb7e845ca739d16ad568cbc368c06
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82204097"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Geef beveiligings contact gegevens op in Azure Security Center
Azure Security Center zal adviseren om gegevens in te voeren voor de contactpersoon voor beveiliging van uw Azure-abonnement, als u dat nog niet hebt gedaan. Deze informatie wordt gebruikt door Microsoft om contact met u op te nemen als door Microsoft Security Response Center (MSRC) wordt gedetecteerd dat uw klantgegevens zijn geopend door een illegale of niet-geautoriseerde derde. MSRC voert een speciale beveiligingscontrole van het Azure-netwerk en de Azure-infrastructuur uit en ontvangt bedreigingsinformatie en klachten van derden over misbruik.

Er wordt een melding per e-mail verzonden de eerste keer dat een waarschuwing plaatsvindt en alleen voor waarschuwingen met een hoge urgentie. E-mailvoorkeuren kunnen alleen worden geconfigureerd voor abonnementsbeleid. Resourcegroepen binnen een abonnement nemen deze instellingen over. Waarschuwingen zijn alleen beschikbaar in de laag standaard van Azure Security Center.

E-mailmeldingen voor waarschuwingen worden verzonden volgens de volgende richtlijnen:
- Aan één e-mailontvanger per type waarschuwing, per dag  
- Er worden niet meer dan drie e-mail berichten verzonden naar één ontvanger op één dag
- Elk e-mailbericht bevat een afzonderlijke waarschuwing, geen verzameling waarschuwingen
- Alleen voor waarschuwingen met hoge urgentie

> [!TIP]
> Voor waarschuwingen met andere Ernst niveaus maakt u een [werk stroom automatisering](workflow-automation.md) voor het gebruik van een logische app die e-mail berichten naar het relevante personeel verzendt.
 
Als er bijvoorbeeld al een e-mailbericht is verstuurd om u te waarschuwen voor een RDP-aanval, krijgt u op dezelfde dag niet nog een e-mailbericht over een RDP-aanval, zelfs niet als hierdoor een nieuwe waarschuwing wordt geactiveerd. 

> [!IMPORTANT]
> In dit document wordt de service geïntroduceerd aan de hand van een voorbeeldimplementatie.  Dit is geen stapsgewijze handleiding.

## <a name="set-up-email-notifications-for-alerts"></a>E-mail meldingen voor waarschuwingen instellen<a name="email"></a>

1. Open de pagina **e-mail meldingen** als gebruiker met de rol beveiligings beheerder of eigenaar van het abonnement:

    - Voor waarschuwingen, open **prijzen & instellingen**, selecteer het relevante abonnement en selecteer **e-mail meldingen**.

    - Als u een aanbeveling implementeert, selecteert u onder **aanbevelingen**de optie **beveiligings contact gegevens opgeven**, selecteert u het Azure-abonnement om contact gegevens op te geven. Hiermee opent u **e-mail meldingen**.

   ![Contactgegevens voor beveiliging verstrekken][2]

1. Voer het e-mail adres of de adressen van het beveiligings contact in, gescheiden door komma's. Er is geen limiet voor het aantal e-mail adressen dat u kunt invoeren.

1. Schakel de optie **e-mail berichten over waarschuwingen verzenden**in als u e-mails wilt ontvangen over waarschuwingen met een hoge urgentie. Voor andere Ernst niveaus gebruikt u een logische app zoals uitgelegd in [werk stroom automatisering](workflow-automation.md).

1. U kunt e-mail meldingen verzenden naar abonnements eigenaren (klassieke service beheerder en mede beheerders, plus de rol van de RBAC-eigenaar bij het abonnements bereik).

1. Selecteer **Opslaan**om de beveiligings gegevens van de contact persoon toe te passen op uw abonnement.

## <a name="see-also"></a>Zie ook
Zie de volgende onderwerpen voor meer informatie over Security Center:

* [Setting security policies in Azure Security Center](tutorial-security-policy.md) (Beveiligingsbeleid instellen in Azure Security Center): leer hoe u beveiligingsbeleid voor uw Azure-abonnementen en -resourcegroepen configureert.
* [Aanbevelingen voor beveiliging in azure Security Center](security-center-recommendations.md) : Ontdek hoe aanbevelingen u helpen uw Azure-resources te beveiligen.
* [Beveiligings status controleren in azure Security Center](security-center-monitoring.md) --meer informatie over het controleren van de status van uw Azure-resources.
* [Beveiligings waarschuwingen beheren en erop reageren in azure Security Center](security-center-managing-and-responding-alerts.md) --meer informatie over het beheren en reageren op beveiligings waarschuwingen.
* [Partner oplossingen bewaken met Azure Security Center](security-center-partner-solutions.md) --meer informatie over het bewaken van de integriteits status van uw partner oplossingen.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
