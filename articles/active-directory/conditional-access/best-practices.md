---
title: Aanbevolen procedures voor voorwaardelijke toegang in Azure Active Directory | Microsoft Documenten
description: Meer informatie over dingen die u moet weten en wat u moet vermijden bij het configureren van beleid voor voorwaardelijke toegang.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 01/25/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: f4560a514ddb9949c8cc07864b2319a5878b245e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295360"
---
# <a name="best-practices-for-conditional-access-in-azure-active-directory"></a>Aanbevolen procedures voor voorwaardelijke toegang in Azure Active Directory

Met [voorwaardelijke toegang voor Azure Active Directory (Azure AD)](../active-directory-conditional-access-azure-portal.md)u bepalen hoe geautoriseerde gebruikers toegang krijgen tot uw cloud-apps. In dit artikel vindt u informatie over:

- Dingen die je moet weten 
- Wat het is, moet voorkomen dat u doet bij het configureren van beleid voor voorwaardelijke toegang. 

In dit artikel wordt ervan uitgegaan dat u bekend bent met de concepten en de terminologie die wordt beschreven in [Wat is voorwaardelijke toegang in Azure Active Directory?](../active-directory-conditional-access-azure-portal.md)

## <a name="whats-required-to-make-a-policy-work"></a>Wat is er nodig om een beleid te laten werken?

Wanneer u een nieuw beleid maakt, zijn er geen gebruikers, groepen, apps of toegangsbesturingselementen geselecteerd.

![Cloud-apps](./media/best-practices/02.png)

Als u uw beleid wilt laten werken, moet u het als:

| Wat           | Procedure                                  | Waarom |
| :--            | :--                                  | :-- |
| **Cloud-apps** |Selecteer een of meer apps.  | Het doel van een beleid voor voorwaardelijke toegang is om u in staat te stellen te bepalen hoe geautoriseerde gebruikers toegang hebben tot cloud-apps.|
| **Gebruikers en groepen** | Selecteer ten minste één gebruiker of groep die gemachtigd is om toegang te krijgen tot uw geselecteerde cloud-apps. | Een beleid voor voorwaardelijke toegang waaraan geen gebruikers en groepen zijn toegewezen, wordt nooit geactiveerd. |
| **Besturingselementen voor toegang** | Selecteer ten minste één toegangscontrole. | Als aan uw voorwaarden is voldaan, moet uw beleidsverwerker weten wat hij moet doen. |

## <a name="what-you-should-know"></a>Wat u moet weten

### <a name="how-are-conditional-access-policies-applied"></a>Hoe worden beleid voor voorwaardelijke toegang toegepast?

Er kan meer dan één beleid voor voorwaardelijke toegang van toepassing zijn wanneer u een cloud-app opent. In dit geval moet aan alle beleidsregels die van toepassing zijn, worden voldaan. Als het ene beleid bijvoorbeeld multi-factor authenticatie (MFA) vereist en een ander beleid vereist, moet u MFA voltooien en een compatibel apparaat gebruiken. 

Alle beleidsregels worden in twee fasen afgedwongen:

- Fase 1: 
   - Detailverzameling: verzamel details om beleid te identificeren waaraan al zou worden voldaan.
   - Tijdens deze fase kunnen gebruikers een certificaatprompt zien als de naleving van het apparaat deel uitmaakt van uw beleid voor voorwaardelijke toegang. Deze prompt kan optreden voor browser-apps wanneer het besturingssysteem van het apparaat geen Windows 10 is.
   - Fase 1 van beleidsevaluatie vindt plaats voor alle ingeschakelde beleidslijnen en beleidsvormen in [de modus alleen rapporteren](concept-conditional-access-report-only.md).
- Fase 2:
   - Handhaving: Rekening houdend met de gegevens die in fase 1 zijn verzameld, moet de gebruiker worden verzocht te voldoen aan eventuele aanvullende vereisten waaraan niet is voldaan.
   - Resultaten toepassen op sessie. 
   - Fase 2 van de beleidsevaluatie vindt plaats voor alle ingeschakelde beleidsregels.

### <a name="how-are-assignments-evaluated"></a>Hoe worden opdrachten geëvalueerd?

Alle opdrachten zijn **logischerwijs ANDed**. Als u meer dan één toewijzing hebt geconfigureerd, moeten alle toewijzingen tevreden zijn om een beleid te activeren.  

Als u een locatievoorwaarde moet configureren die van toepassing is op alle verbindingen die van buiten het netwerk van uw organisatie zijn gemaakt:

- Alle **locaties** opnemen
- Alle **vertrouwde IP-gebruikers uitsluiten**

### <a name="what-to-do-if-you-are-locked-out-of-the-azure-ad-admin-portal"></a>Wat moet u doen als u bent uitgesloten van de Azure AD-beheerportal?

Als u bent uitgesloten van de Azure AD-portal vanwege een onjuiste instelling in een beleid voor voorwaardelijke toegang:

- Controleer is dat er andere beheerders in uw organisatie zijn die nog niet zijn geblokkeerd. Een beheerder met toegang tot de Azure-portal kan het beleid uitschakelen dat van invloed is op uw aanmelding. 
- Als geen van de beheerders in uw organisatie het beleid kan bijwerken, moet u een ondersteuningsverzoek indienen. Microsoft-ondersteuning kan beleid voor voorwaardelijke toegang controleren en bijwerken dat de toegang verhindert.

### <a name="what-happens-if-you-have-policies-in-the-azure-classic-portal-and-azure-portal-configured"></a>Wat gebeurt er als u beleidsregels hebt in de klassieke Azure-portal en azure-portal?  

Beide beleidsregels worden afgedwongen door Azure Active Directory en de gebruiker krijgt alleen toegang wanneer aan alle vereisten is voldaan.

### <a name="what-happens-if-you-have-policies-in-the-intune-silverlight-portal-and-the-azure-portal"></a>Wat gebeurt er als u beleidsregels hebt in de Intune Silverlight-portal en de Azure-portal?

Beide beleidsregels worden afgedwongen door Azure Active Directory en de gebruiker krijgt alleen toegang wanneer aan alle vereisten is voldaan.

### <a name="what-happens-if-i-have-multiple-policies-for-the-same-user-configured"></a>Wat gebeurt er als ik meerdere beleidsregels heb voor dezelfde gebruiker die is geconfigureerd?  

Voor elke aanmelding evalueert Azure Active Directory alle beleidsregels en zorgt ervoor dat aan alle vereisten wordt voldaan voordat de gebruiker toegang krijgt. Bloktoegang overtroeft alle andere configuratie-instellingen. 

### <a name="does-conditional-access-work-with-exchange-activesync"></a>Werkt Voorwaardelijke toegang met Exchange ActiveSync?

Ja, u Exchange ActiveSync gebruiken in een beleid voor voorwaardelijke toegang.

Sommige cloud-apps zoals SharePoint Online en Exchange Online ondersteunen ook verouderde verificatieprotocollen. Wanneer een client-app een verouderd verificatieprotocol kan gebruiken om toegang te krijgen tot een cloud-app, kan Azure AD geen beleid voor voorwaardelijke toegang afdwingen voor deze toegangspoging. Om te voorkomen dat een client-app de handhaving van het beleid omzeilt, moet u controleren of het mogelijk is om alleen moderne verificatie in te schakelen op de betreffende cloud-apps.

### <a name="how-should-you-configure-conditional-access-with-office-365-apps"></a>Hoe moet u Voorwaardelijke toegang configureren met Office 365-apps?

Omdat Office 365-apps met elkaar verbonden zijn, raden we u aan veelgebruikte apps samen toe te voegen bij het maken van beleid.

Veelvoorkomende onderling verbonden toepassingen zijn Microsoft Flow, Microsoft Planner, Microsoft Teams, Office 365 Exchange Online, Office 365 SharePoint Online en Office 365 Yammer.

Het is belangrijk voor beleid waarvoor gebruikersinteracties nodig zijn, zoals meervoudige verificatie, wanneer de toegang wordt beheerd aan het begin van een sessie of taak. Als u dit niet doet, kunnen gebruikers bepaalde taken in een app niet voltooien. Als u bijvoorbeeld multi-factor authenticatie vereist op onbeheerde apparaten om toegang te krijgen tot SharePoint, maar niet tot e-mail, kunnen gebruikers die in hun e-mail werken geen SharePoint-bestanden aan een bericht koppelen. Meer informatie vindt u in het artikel, [Wat zijn serviceafhankelijkheden in Azure Active Directory Conditional Access?](service-dependencies.md).

## <a name="what-you-should-avoid-doing"></a>Wat je moet vermijden doen

Het Conditional Access framework biedt u een grote configuratieflexibiliteit. Echter, grote flexibiliteit betekent ook dat u zorgvuldig moet controleren elk configuratiebeleid voordat het vrijgeven van ongewenste resultaten. In deze context moet u speciale aandacht besteden aan opdrachten die betrekking hebben op complete sets, zoals **alle gebruikers / groepen / cloud-apps.**

In uw omgeving moet u de volgende configuraties vermijden:

**Voor alle gebruikers zijn alle cloud-apps:**

- **Toegang blokkeren** - Deze configuratie blokkeert uw hele organisatie, wat zeker geen goed idee is.
- **Apparaat dat compatibel is vereist** - Voor gebruikers die hun apparaten nog niet hebben ingeschreven, blokkeert dit beleid alle toegang, inclusief toegang tot de Intune-portal. Als u een beheerder bent zonder een ingeschreven apparaat, blokkeert dit beleid u om terug te keren naar de Azure-portal om het beleid te wijzigen.
- **Domeinlid deelnemen vereisen** - Deze toegang tot beleidsblokkering heeft ook de mogelijkheid om de toegang voor alle gebruikers in uw organisatie te blokkeren als u nog geen apparaat hebt dat is verbonden aan het domein.
- **Beleid voor app-beveiliging vereisen** - Deze toegang tot beleidsblokkering heeft ook de mogelijkheid om de toegang voor alle gebruikers in uw organisatie te blokkeren als u geen Intune-beleid hebt. Als u een beheerder bent zonder clienttoepassing met een Intune-appbeveiligingsbeleid, u met dit beleid niet meer in portals zoals Intune en Azure terecht.

**Voor alle gebruikers, alle cloud-apps, alle apparaatplatforms:**

- **Toegang blokkeren** - Deze configuratie blokkeert uw hele organisatie, wat zeker geen goed idee is.

## <a name="how-should-you-deploy-a-new-policy"></a>Hoe moet u een nieuw beleid implementeren?

Als eerste stap moet u uw beleid evalueren met behulp van het [wat als-tool.](what-if-tool.md)

Wanneer nieuw beleid klaar is voor uw omgeving, implementeert u deze gefaseerd:

1. Pas een beleid toe op een kleine groep gebruikers en controleer of het zich gedraagt zoals verwacht. 
1. Wanneer u een beleid uitbreidt met meer gebruikers. Blijf alle beheerders uitsluiten van het beleid om ervoor te zorgen dat ze nog steeds toegang hebben en kan een beleid bijwerken als een wijziging vereist is.
1. Pas een beleid alleen toe op alle gebruikers indien nodig. 

Maak als aanbevolen praktijk een gebruikersaccount dat is:

- Toegewijd aan beleidsbeheer 
- Uitgesloten van al uw beleid

## <a name="policy-migration"></a>Beleidsmigratie

Overweeg het beleid dat u niet hebt gemaakt in de Azure-portal te migreren omdat:

- U nu scenario's aanpakken die u voorheen niet aankon.
- U het aantal beleidsregels dat u moet beheren verminderen door ze te consolideren.   
- U al uw beleid voor voorwaardelijke toegang op één centrale locatie beheren.
- De klassieke Azure-portal is uitgeschakeld.   

Zie [Klassiek beleid migreren in Azure Portal](policy-migration.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Als je wilt weten:

- Zie [MFA vereisen voor specifieke apps met Voorwaardelijke toegang in Azure Active Directory](app-based-mfa.md).
- Zie [Uw voorwaardelijke toegangsimplementatie plannen in Azure Active Directory.](plan-conditional-access.md)
