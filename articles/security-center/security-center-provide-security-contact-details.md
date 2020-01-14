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
ms.openlocfilehash: 1a66ea200082f60a3a763c6a4e2bdea62ec473d8
ms.sourcegitcommit: f34165bdfd27982bdae836d79b7290831a518f12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/13/2020
ms.locfileid: "75920989"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Geef beveiligings contact gegevens op in Azure Security Center
Azure Security Center wordt aangeraden om de contact gegevens van de beveiliging voor uw Azure-abonnement te verstrekken als u dat nog niet hebt gedaan. Deze informatie wordt gebruikt door Microsoft om contact met u op te nemen als door Microsoft Security Response Center (MSRC) wordt gedetecteerd dat uw klantgegevens zijn geopend door een illegale of niet-geautoriseerde derde. MSRC voert een speciale beveiligingscontrole van het Azure-netwerk en de Azure-infrastructuur uit en ontvangt bedreigingsinformatie en klachten van derden over misbruik.

Er wordt een melding per e-mail verzonden de eerste keer dat een waarschuwing plaatsvindt en alleen voor waarschuwingen met een hoge urgentie. E-mailvoorkeuren kunnen alleen worden geconfigureerd voor abonnementsbeleid. Resourcegroepen binnen een abonnement nemen deze instellingen over. Waarschuwingen zijn alleen beschikbaar in de laag standaard van Azure Security Center.

E-mailmeldingen voor waarschuwingen worden verzonden volgens de volgende richtlijnen:
- Alleen voor waarschuwingen met hoge urgentie
- Aan één e-mailontvanger per type waarschuwing, per dag  
- Er worden niet meer dan drie e-mail berichten verzonden naar één ontvanger op één dag
- Elk e-mailbericht bevat een afzonderlijke waarschuwing, geen verzameling waarschuwingen
 
Als er bijvoorbeeld al een e-mailbericht is verstuurd om u te waarschuwen voor een RDP-aanval, krijgt u op dezelfde dag niet nog een e-mailbericht over een RDP-aanval, zelfs niet als hierdoor een nieuwe waarschuwing wordt geactiveerd. 

> [!NOTE]
> In dit document wordt de service geïntroduceerd aan de hand van een voorbeeldimplementatie.  Dit is geen stapsgewijze handleiding.

## E-mail meldingen voor waarschuwingen instellen<a name="email"></a>

1. Selecteer in de portal **prijzen & instellingen**.
1. Klik op het abonnement.
1. Klik op **E-mailmeldingen**.

> [!NOTE]
> Als u een aanbeveling implementeert, selecteert u onder **aanbevelingen**de optie **beveiligings contact gegevens opgeven**, selecteert u het Azure-abonnement om contact gegevens op te geven. Hiermee opent u **e-mail meldingen**.

   ![Contactgegevens voor beveiliging verstrekken][2]

   * Voer het e-mail adres of de adressen van het beveiligings contact in, gescheiden door komma's. Er is geen limiet voor het aantal e-mail adressen dat u kunt invoeren.
   * Geef een internationaal telefoon nummer voor de contact persoon voor beveiliging op.
   * Schakel de optie **e-mail berichten over waarschuwingen verzenden**in als u e-mails wilt ontvangen over waarschuwingen met een hoge urgentie.
   * U hebt de optie om e-mail meldingen te verzenden naar abonnements eigenaren (klassieke service beheerder en mede beheerders, plus de rol van de RBAC-eigenaar bij het abonnements bereik).
   * Selecteer **Opslaan** om de contact gegevens van de beveiliging toe te passen op uw abonnement.

## <a name="see-also"></a>Zie ook
Zie de volgende onderwerpen voor meer informatie over Security Center:

* [Setting security policies in Azure Security Center](tutorial-security-policy.md) (Beveiligingsbeleid instellen in Azure Security Center): leer hoe u beveiligingsbeleid voor uw Azure-abonnementen en -resourcegroepen configureert.
* [Aanbevelingen voor beveiliging in azure Security Center](security-center-recommendations.md) : Ontdek hoe aanbevelingen u helpen uw Azure-resources te beveiligen.
* [Beveiligings status controleren in azure Security Center](security-center-monitoring.md) --meer informatie over het controleren van de status van uw Azure-resources.
* [Managing and responding to security alerts in Azure Security Center](security-center-managing-and-responding-alerts.md) (Beveiligingswaarschuwingen beheren en erop reageren in Azure Security Center): ontdek hoe u beveiligingswaarschuwingen kunt beheren en erop kunt reageren.
* [Partneroplossingen bewaken met Azure Security Center](security-center-partner-solutions.md): leer hoe u de integriteitsstatus van uw partneroplossingen kunt bewaken.
* [Azure Security Center FAQ](security-center-faq.md) (Veelgestelde vragen over Azure Security Center): raadpleeg veelgestelde vragen over het gebruik van de service.
* [Azure-beveiligings blog](https://blogs.msdn.com/b/azuresecurity/) : krijg het meest recente Azure-beveiligings nieuws en-informatie.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
