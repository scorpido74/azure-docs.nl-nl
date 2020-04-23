---
title: Werken met beveiligingsbeleid | Microsoft Documenten
description: In dit artikel wordt beschreven hoe u werken met beveiligingsbeleid in Azure Security Center.
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
ms.openlocfilehash: c98ae7c95ac3fc186786612dd3d8d8bd55fa816f
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024877"
---
# <a name="working-with-security-policies"></a>Werken met beveiligingsbeleid

In dit artikel wordt uitgelegd hoe beveiligingsbeleid is geconfigureerd en hoe u deze bekijken in Het Beveiligingscentrum. 

## <a name="introduction-to-security-policies"></a>Inleiding tot het beveiligingsbeleid

Een beveiligingsbeleid definieert de gewenste configuratie van uw workloads en zorgt ervoor dat u voldoet aan de beveiligingsvereisten van uw bedrijf of regelgevers.

Azure Security Center voert beveiligingsaanbevelingen uit op basis van het door u gekozen beleid. Het beleid voor beveiligingscentrum is gebaseerd op beleidsinitiatieven die zijn gemaakt in Azure Policy. U [Azure Policy](../governance/policy/overview.md) gebruiken om uw beleid te beheren en om beleidsregels in te stellen voor beheergroepen en voor meerdere abonnementen.

Security Center biedt de volgende opties voor het werken met beveiligingsbeleid:

* **Het ingebouwde standaardbeleid weergeven en bewerken** - Wanneer u Beveiligingscentrum inschakelt, wordt een ingebouwd initiatief met de naam 'ASC default' automatisch toegewezen aan alle geregistreerde abonnementen van het Beveiligingscentrum (gratis of standaardlagen). Als u dit initiatief wilt aanpassen, u afzonderlijke beleidsregels binnen het initiatief in- of uitschakelen. Zie de lijst met [ingebouwde beveiligingsbeleidsregels](security-center-policy-definitions.md) om inzicht te krijgen in de beschikbare opties.

* **Voeg uw eigen aangepaste beleidsregels toe** - Als u de beveiligingsinitiatieven die op uw abonnement zijn toegepast, wilt aanpassen, u dit doen binnen Security Center. U ontvangt dan aanbevelingen als uw machines niet voldoen aan het beleid dat u maakt. Zie [Aangepast beveiligingsbeleid](custom-security-policies.md)gebruiken voor instructies voor het maken en toewijzen van aangepaste beleidsregels .

* **Nalevingsbeleid voor regelgeving toevoegen** - Het dashboard voor naleving van de regelgeving van het Security Center toont de status van alle beoordelingen binnen uw omgeving in de context van een bepaalde standaard of regelgeving (zoals Azure CIS, NIST SP 800-53 R4, SWIFT CSP CSCF-v2020). Zie [Uw naleving van de regelgeving verbeteren](security-center-compliance-dashboard.md)voor meer informatie.


## <a name="managing-your-security-policies"></a>Uw beveiligingsbeleid beheren

Ga als volgt te werk als u uw beveiligingsbeleidsregels wilt weergeven in Security Center:

1. Selecteer **beveiligingsbeleid**in het dashboard **Beveiligingscentrum** .

    ![Het deelvenster Beleidsbeheer](./media/security-center-policies/security-center-policy-mgt.png)

   In het scherm **Beleidsbeheer** ziet u het aantal beheergroepen, abonnementen en werkruimten en uw beheergroepstructuur.

1. Selecteer de abonnements- of beheergroep waarvan u het beleid wilt weergeven.

1. De pagina beveiligingsbeleid voor dat abonnement of beheergroep wordt weergegeven. Het toont het beschikbare en toegewezen beleid.

   ![beleidsscherm](./media/tutorial-security-policy/security-policy-page.png)

    > [!NOTE]
    > Als er naast uw standaardbeleid een label 'MG overgenomen' is, betekent dit dat het beleid is toegewezen aan een beheergroep en is overgenomen door het abonnement dat u bekijkt.


1. Kies uit de beschikbare opties op deze pagina:

    1. Als u wilt werken met industriebeleid, selecteert u **Meer standaarden toevoegen**. Zie [Bijwerken naar dynamische compliancepakketten voor](update-regulatory-compliance-packages.md)meer informatie.

    1. Als u aangepaste initiatieven wilt toewijzen en beheren, selecteert u **Aangepaste initiatieven toevoegen**. Zie [Aangepast beveiligingsbeleid gebruiken](custom-security-policies.md)voor meer informatie .

    1. Als u het standaardbeleid wilt weergeven en bewerken, selecteert u **Effectief beleid weergeven** en gaat u verder zoals hieronder beschreven. 

       ![beleidsscherm](./media/security-center-policies/policy-screen.png)
       
       Dit **scherm met beveiligingsbeleid** geeft de actie weer die is ondernomen door het beleid dat is toegewezen aan de abonnements- of beheergroep die u hebt geselecteerd.
       
       * Gebruik de links bovenaan om een **beleidstoewijzing** te openen die van toepassing is op de abonnements- of beheergroep. Met deze koppelingen u toegang krijgen tot de toewijzing en het beleid bewerken of uitschakelen. Als u bijvoorbeeld ziet dat een bepaalde beleidstoewijzing de bescherming van eindpunten effectief ontzegt, gebruikt u de koppeling om het beleid te bewerken of uit te schakelen.
       
       * In de lijst met beleidsregels ziet u de effectieve toepassing van het beleid op uw abonnements- of beheergroep. De instellingen van elk beleid die van toepassing zijn op het toepassingsgebied worden in aanmerking genomen en het cumulatieve resultaat van de acties die door het beleid worden ondernomen, wordt weergegeven. Als bijvoorbeeld in de ene toewijzing van het beleid is uitgeschakeld, maar in een andere opdracht is ingesteld op AuditIfNotExist, wordt het cumulatieve effect auditifnotexist van toepassing. Het actievere effect heeft altijd voorrang.
       
       * Het effect van het beleid kan zijn: Toevoegen, Controleren, AuditIfNotExists, Deny, DeployIfNotExists, Disabled. Zie [Beleidseffecten begrijpen](../governance/policy/concepts/effects.md)voor meer informatie over de manier waarop effecten worden toegepast.

       > [!NOTE]
       > Wanneer u toegewezen beleidsregels bekijkt, u meerdere toewijzingen zien en u zien hoe elke toewijzing op zichzelf is geconfigureerd.


## <a name="who-can-edit-security-policies"></a>Wie kan het beveiligingsbeleid bewerken?

U beveiligingsbeleid bewerken via de Azure Policy portal, via REST API of met Windows PowerShell.

Security Center gebruikt op rollen gebaseerd toegangsbeheer (RBAC), dat ingebouwde rollen biedt die kunnen worden toegewezen aan gebruikers, groepen en services in Azure. Wanneer gebruikers Beveiligingscentrum openen, zien ze alleen informatie die gerelateerd is aan bronnen waartoe ze toegang hebben. Dat betekent dat gebruikers de rol van *eigenaar,* *bijdrager*of *lezer* toegewezen krijgen aan het abonnement van de resource. Naast deze rollen zijn er twee specifieke functies van het Beveiligingscentrum:

- **Beveiligingslezer**: Heb weergaverechten op Security Center, dat aanbevelingen, waarschuwingen, beleid en gezondheid bevat, maar ze kunnen geen wijzigingen aanbrengen.
- **Beveiligingsbeheerder**: heb dezelfde weergaverechten als *beveiligingslezer*en ze kunnen ook het beveiligingsbeleid bijwerken en aanbevelingen en waarschuwingen afwijzen.


## <a name="disable-security-policies"></a>Beveiligingsbeleid uitschakelen
Als het standaardbeveiligingsbeleid een aanbeveling genereert die niet relevant is voor uw omgeving, u deze stoppen door de beleidsdefinitie uit te schakelen die de aanbeveling verzendt.
Zie [Beveiligingsaanbevelingen beheren](security-center-recommendations.md)voor meer informatie over aanbevelingen.

1. Selecteer in Security Center in de sectie **Beleid & compliance** de optie **Beveiligingsbeleid**.

   ![beleidsbeheer](./media/tutorial-security-policy/policy-management.png)

2. Selecteer de abonnements- of beheergroep waarvoor u de aanbeveling wilt uitschakelen.

   > [!NOTE]
   > Houd er rekening mee dat in een beheergroep het beleid wordt toepast op alle abonnementen. Als u dus het beleid van een abonnement uitschakelt en het abonnement behoort tot een beheergroep waarin hetzelfde beleid wordt gebruikt, blijft u de beleidsaanbevelingen ontvangen. Het beleid wordt nog steeds toegepast op beheerniveau en de aanbevelingen worden nog steeds gegenereerd.

1. Selecteer **Effectief beleid weergeven**.

   ![beleid uitschakelen](./media/tutorial-security-policy/view-effective-policy.png)

1. Selecteer het toegewezen beleid.

   ![beleid uitschakelen](./media/tutorial-security-policy/security-policy.png)

1. Zoek in de sectie **PARAMETERS** naar het beleid dat de aanbeveling aanroept die u wilt uitschakelen en selecteer in de vervolgkeuzelijst **Uitgeschakeld**

   ![beleid uitschakelen](./media/tutorial-security-policy/disable-policy.png)

1. Selecteer **Opslaan**.

   > [!NOTE]
   > Het kan tot 12 uur duren voordat beleidswijzigingen worden uitgeschakeld.



## <a name="next-steps"></a>Volgende stappen
In dit artikel leert u over beveiligingsbeleid. Zie voor gerelateerde informatie de volgende artikelen:

* Zie [Snelstart: Een beleidstoewijzing maken om niet-compatibele resources te identificeren met behulp van de Azure PowerShell-module voor](../governance/policy/assign-policy-powershell.md) instructies voor het instellen van beleid met PowerShell: Een beleidstoewijzing maken om niet-compatibele resources te identificeren met behulp van de Azure PowerShell-module

* Zie [Beleid maken en beheren om naleving af te dwingen voor](../governance/policy/tutorials/create-and-manage.md)instructies voor het bewerken van een beveiligingsbeleid in Azure-beleid.

* Zie Wat is Azure Policy voor instructies over het instellen van een beleid voor abonnementen of beheergroepen met Azure [Policy?](../governance/policy/overview.md)