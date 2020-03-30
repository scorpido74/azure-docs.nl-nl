---
title: Beveiligingscontactgegevens opgeven in Azure Security Center | Microsoft Documenten
description: In dit document ziet u hoe u beveiligingscontactgegevens opgeven in Azure Security Center.
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
ms.openlocfilehash: 64a9600a3014f7e85a7f924d38882bfadaf631db
ms.sourcegitcommit: d0fd35f4f0f3ec71159e9fb43fcd8e89d653f3f2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2020
ms.locfileid: "80387815"
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Beveiligingscontactgegevens opgeven in Azure Security Center
Azure Security Center zal adviseren om gegevens in te voeren voor de contactpersoon voor beveiliging van uw Azure-abonnement, als u dat nog niet hebt gedaan. Deze informatie wordt gebruikt door Microsoft om contact met u op te nemen als door Microsoft Security Response Center (MSRC) wordt gedetecteerd dat uw klantgegevens zijn geopend door een illegale of niet-geautoriseerde derde. MSRC voert een speciale beveiligingscontrole van het Azure-netwerk en de Azure-infrastructuur uit en ontvangt bedreigingsinformatie en klachten van derden over misbruik.

Er wordt een melding per e-mail verzonden de eerste keer dat een waarschuwing plaatsvindt en alleen voor waarschuwingen met een hoge urgentie. E-mailvoorkeuren kunnen alleen worden geconfigureerd voor abonnementsbeleid. Resourcegroepen binnen een abonnement nemen deze instellingen over. Waarschuwingen zijn alleen beschikbaar in de standaardlaag van Azure Security Center.

E-mailmeldingen voor waarschuwingen worden verzonden volgens de volgende richtlijnen:
- Aan één e-mailontvanger per type waarschuwing, per dag  
- Er worden niet meer dan 3 e-mailberichten op één dag naar één ontvanger verzonden
- Elk e-mailbericht bevat een afzonderlijke waarschuwing, geen verzameling waarschuwingen
- Alleen voor waarschuwingen met hoge urgentie

> [!TIP]
> Voor waarschuwingen met andere ernstniveaus maakt u een [werkstroomautomatisering](workflow-automation.md) om een Logische App te gebruiken die e-mails naar het desbetreffende personeel verzendt.
 
Als er bijvoorbeeld al een e-mailbericht is verstuurd om u te waarschuwen voor een RDP-aanval, krijgt u op dezelfde dag niet nog een e-mailbericht over een RDP-aanval, zelfs niet als hierdoor een nieuwe waarschuwing wordt geactiveerd. 

> [!NOTE]
> In dit document wordt de service geïntroduceerd aan de hand van een voorbeeldimplementatie.  Dit is geen stapsgewijze handleiding.

## <a name="set-up-email-notifications-for-alerts"></a>E-mailmeldingen instellen voor waarschuwingen<a name="email"></a>

1. Open de pagina **E-mailmeldingen:**

    - Voor waarschuwingen opent **u Prijsinstellingen & instellingen,** selecteert u het desbetreffende abonnement en selecteert **u E-mailmeldingen**.

    - Als u een aanbeveling implementeert, selecteert u onder **Aanbevelingen**de optie **Beveiligingscontactgegevens verstrekken**, selecteert u het Azure-abonnement waarop u contactgegevens wilt opgeven. Hiermee worden **e-mailmeldingen geopend.**

   ![Contactgegevens voor beveiliging verstrekken][2]

1. Voer het e-mailadres of de adressen van de beveiligingscontactpersoon in, gescheiden door komma's. Er is geen limiet aan het aantal e-mailadressen dat u invoeren.

1. Als u e-mails wilt ontvangen over waarschuwingen met hoge ernst, schakelt u de optie **Stuur mij e-mails over waarschuwingen.** Voor andere ernstniveaus gebruikt u een Logic App zoals uitgelegd in [workflowautomatisering.](workflow-automation.md)

1. U e-mailmeldingen verzenden naar eigenaren van abonnementen (klassieke servicebeheerder en co-beheerders, plus de rol Van RBAC-eigenaar bij het abonnementsbereik).

1. Als u de beveiligingscontactgegevens wilt toepassen op uw abonnement, selecteert u **Opslaan**.

## <a name="see-also"></a>Zie ook
Zie de volgende onderwerpen voor meer informatie over Security Center:

* [Setting security policies in Azure Security Center](tutorial-security-policy.md) (Beveiligingsbeleid instellen in Azure Security Center): leer hoe u beveiligingsbeleid voor uw Azure-abonnementen en -resourcegroepen configureert.
* [Beveiligingsaanbevelingen beheren in Azure Security Center:](security-center-recommendations.md) lees hoe aanbevelingen u helpen uw Azure-bronnen te beschermen.
* [Bewaking van de beveiligingsstatus in Azure Security Center:](security-center-monitoring.md) meer informatie over het bewaken van de status van uw Azure-resources.
* [Beveiligingswaarschuwingen beheren en beantwoorden in Azure Security Center:](security-center-managing-and-responding-alerts.md) meer informatie over het beheren en reageren op beveiligingswaarschuwingen.
* [Partnersoplossingen bewaken met Azure Security Center:](security-center-partner-solutions.md) meer informatie over het bewaken van de status van uw partneroplossingen.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
