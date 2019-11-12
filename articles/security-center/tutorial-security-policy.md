---
title: Werken met beveiligings beleid | Microsoft Docs
description: In dit artikel wordt beschreven hoe u kunt werken met beveiligings beleid in Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2d248817-ae97-4c10-8f5d-5c207a8019ea
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 45a27be400753b56c42310a340334feba8a420c5
ms.sourcegitcommit: 6dec090a6820fb68ac7648cf5fa4a70f45f87e1a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/11/2019
ms.locfileid: "73906880"
---
# <a name="working-with-security-policies"></a>Werken met beveiligingsbeleid

In dit artikel wordt uitgelegd hoe beveiligings beleid wordt geconfigureerd en hoe u deze in Security Center kunt weer geven. 

## <a name="introduction-to-security-policies"></a>Inleiding tot beveiligings beleid

Een beveiligings beleid definieert de gewenste configuratie van uw workloads en zorgt ervoor dat u voldoet aan de beveiligings vereisten van uw bedrijf of regel.

Azure Security Center worden de beveiligings aanbevelingen gebaseerd op uw gekozen beleid. Security Center beleid is gebaseerd op beleids initiatieven die zijn gemaakt in Azure Policy. U kunt [Azure Policy](../governance/policy/overview.md) gebruiken om uw beleid te beheren en beleid in te stellen tussen beheer groepen en meerdere abonnementen.

Security Center biedt de volgende opties voor het werken met beveiligings beleid:

* **Het ingebouwde standaard beleid weer geven en bewerken** : wanneer u Security Center inschakelt, wordt er automatisch een ingebouwd initiatief met de naam ' ASC default ' toegewezen aan alle Security Center geregistreerde abonnementen (lagen gratis of standaard). Als u dit initiatief wilt aanpassen, kunt u afzonderlijk beleids regels in-of uitschakelen. Bekijk de lijst met [ingebouwde beveiligings beleidsregels](security-center-policy-definitions.md) om inzicht te krijgen in de beschik bare opties.

* **Uw eigen aangepaste beleids regels toevoegen** : als u de beveiligings initiatieven wilt aanpassen die op uw abonnement zijn toegepast, kunt u dit doen in Security Center. U ontvangt dan aanbevelingen als uw computers niet voldoen aan het beleid dat u maakt. Zie [aangepaste beveiligings beleidsregels gebruiken](custom-security-policies.md)voor instructies voor het maken en toewijzen van aangepaste beleids regels.

* **Nalevings beleid voor regelgeving toevoegen** -het nalevings Dashboard van de Security Center van de regelgeving toont de status van alle evaluaties in uw omgeving in de context van een bepaalde standaard of verordening (zoals Azure CIS, NIST SP 800-53 R4, Swift CSP CSCF-v2020). Zie [Uw regelnaleving verbeteren](security-center-compliance-dashboard.md) voor meer informatie.


## <a name="managing-your-security-policies"></a>Uw beveiligings beleid beheren

Ga als volgt te werk als u uw beveiligingsbeleidsregels wilt weergeven in Security Center:

1. Selecteer in het **Security Center** -dash board **beveiligings beleid**.

    ![Het deelvenster Beleidsbeheer](./media/security-center-policies/security-center-policy-mgt.png)

   In het scherm **beleids beheer** ziet u het aantal beheer groepen, abonnementen en werk ruimten, evenals de structuur van uw beheer groep.

1. Selecteer het abonnement of de beheer groep waarvan u het beleid wilt weer geven.

1. De pagina beveiligings beleid voor het abonnement of de beheer groep wordt weer gegeven. Het beschik bare en toegewezen beleid wordt weer gegeven.

   ![scherm beleid](./media/tutorial-security-policy/security-policy-page.png)

    > [!NOTE]
    > Als er naast het standaard beleid een label "MG inherited" is, betekent dit dat het beleid is toegewezen aan een beheer groep en dat is overgenomen door het abonnement dat u bekijkt.


1. Kies uit de beschik bare opties op deze pagina:

    1. Als u wilt werken met beleids regels voor de branche, klikt u op **meer standaarden toevoegen**. Zie [bijwerken naar dynamische nalevings pakketten](update-regulatory-compliance-packages.md)voor meer informatie.

    1. Als u aangepaste initiatieven wilt toewijzen en beheren, klikt u op **aangepaste initiatieven toevoegen**. Zie [aangepaste beveiligings beleidsregels gebruiken](custom-security-policies.md)voor meer informatie.

    1. Als u het standaard beleid wilt weer geven en bewerken, klikt u op **effectief beleid weer geven** en gaat u verder, zoals hieronder wordt beschreven. 

       ![scherm beleid](./media/security-center-policies/policy-screen.png)
       
       Dit scherm van het **beveiligings beleid** toont de actie die wordt uitgevoerd door het beleid dat is toegewezen aan het abonnement of de beheer groep die u hebt geselecteerd.
       
       * Gebruik de koppelingen aan de bovenkant om een beleids **toewijzing** te openen die van toepassing is op het abonnement of de beheer groep. Met deze koppelingen krijgt u toegang tot de toewijzing en kunt u het beleid bewerken of uitschakelen. Als u bijvoorbeeld ziet dat een bepaalde beleids toewijzing de eindpunt beveiliging effectief weigert, gebruikt u de koppeling om het beleid te bewerken of uit te scha kelen.
       
       * In de lijst met beleids regels kunt u de juiste toepassing van het beleid voor uw abonnement of beheer groep bekijken. De instellingen van elk beleid dat van toepassing is op het bereik, worden in aanmerking genomen en het cumulatieve resultaat van de acties die door het beleid worden uitgevoerd, wordt weer gegeven. Als bijvoorbeeld in één toewijzing van het beleid is uitgeschakeld, maar in een andere methode is ingesteld op AuditIfNotExist, wordt met het cumulatieve effect AuditIfNotExist toegepast. Het meer actieve effect heeft altijd voor rang.
       
       * Het effect van het beleid kan zijn: Append, audit, AuditIfNotExists, deny, DeployIfNotExists, disabled. Zie [beleids effecten begrijpen](../governance/policy/concepts/effects.md)voor meer informatie over hoe effecten worden toegepast.

       > [!NOTE]
       > Wanneer u toegewezen beleids regels bekijkt, kunt u meerdere toewijzingen zien en kunt u zien hoe elke toewijzing zelf is geconfigureerd.


## <a name="who-can-edit-security-policies"></a>Wie kan beveiligings beleid bewerken?

U kunt beveiligings beleid via de Azure Policy Portal bewerken via REST API of met behulp van Windows Power shell.

Security Center gebruikt op rollen gebaseerd Access Control (RBAC), dat ingebouwde rollen biedt die kunnen worden toegewezen aan gebruikers, groepen en services in Azure. Wanneer gebruikers Security Center openen, zien ze alleen informatie die betrekking heeft op de resources waartoe ze toegang hebben. Dit betekent dat gebruikers de rol van *eigenaar*, *bijdrager*of *lezer* aan het abonnement van de resource hebben toegewezen. Naast deze rollen zijn er twee specifieke Security Center rollen:

- **Beveiligings lezer**: hebben weergave rechten voor Security Center, waaronder aanbevelingen, waarschuwingen, beleid en status, maar ze kunnen geen wijzigingen aanbrengen.
- **Beveiligings beheerder**: hebben dezelfde weergave rechten als de *beveiligings lezer*, en ze kunnen ook het beveiligings beleid bijwerken en aanbevelingen en waarschuwingen negeren.


## <a name="disable-security-policies"></a>Beveiligings beleid uitschakelen
Als het standaard beveiligings beleid een aanbeveling genereert die niet relevant is voor uw omgeving, kunt u deze stoppen door de beleids definitie die de aanbeveling verzendt, uit te scha kelen.
Zie [beveiligings aanbevelingen beheren](security-center-recommendations.md)voor meer informatie over aanbevelingen.

1. Klik in het Security Center, in de sectie **beleids & naleving** , op **beveiligings beleid**.

   ![beleids beheer](./media/tutorial-security-policy/policy-management.png)

2. Klik op het abonnement of de beheer groep waarvoor u de aanbeveling wilt uitschakelen.

   > [!NOTE]
   > Houd er rekening mee dat een beheer groep het beleid toepast op de abonnementen. Als u dus het beleid van een abonnement uitschakelt en het abonnement behoort tot een beheer groep die nog steeds hetzelfde beleid gebruikt, blijft de aanbevelingen voor het beleid worden ontvangen. Het beleid wordt nog steeds toegepast vanuit het beheer niveau en de aanbevelingen worden nog steeds gegenereerd.

1. Klik op **effectief beleid weer geven**.

   ![beleid uitschakelen](./media/tutorial-security-policy/view-effective-policy.png)

1. Klik op het toegewezen beleid.

   ![beleid uitschakelen](./media/tutorial-security-policy/security-policy.png)

1. Zoek in de sectie **para meters** naar het beleid dat de aanbeveling aanroept die u wilt uitschakelen, en selecteer in de vervolg keuzelijst de optie **uitgeschakeld**

   ![beleid uitschakelen](./media/tutorial-security-policy/disable-policy.png)

1. Klik op **Opslaan**.

   > [!NOTE]
   > Het kan tot 12 uur duren voordat de wijzigingen van het beleid uitschakelen zijn doorgevoerd.



## <a name="next-steps"></a>Volgende stappen
In dit artikel hebt u geleerd over het beveiligings beleid. Raadpleeg de volgende artikelen voor verwante informatie:

* Zie [Quick Start: een beleids toewijzing maken om niet-compatibele resources te identificeren met behulp van de module Azure PowerShell](../governance/policy/assign-policy-powershell.md) voor instructies over het instellen van beleids regels met behulp van Power shell

* Zie [beleid maken en beheren om naleving af te dwingen](../governance/policy/tutorials/create-and-manage.md)voor instructies over het bewerken van een beveiligings beleid in azure Policy.

* Zie [Wat is Azure Policy?](../governance/policy/overview.md) voor instructies over het instellen van een beleid in abonnementen of op beheer groepen met behulp van Azure Policy.