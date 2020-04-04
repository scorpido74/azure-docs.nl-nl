---
title: App-beveiligingsbeleid met voorwaardelijke toegang - Azure Active Directory
description: Meer informatie over het vereisen van app-beveiligingsbeleid voor toegang tot cloud-apps met voorwaardelijke toegang in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b282962cc713487b8ee5113b02b8533a1538fff
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631900"
---
# <a name="how-to-require-app-protection-policy-and-an-approved-client-app-for-cloud-app-access-with-conditional-access"></a>How to: App-beveiligingsbeleid en een goedgekeurde client-app vereisen voor toegang tot cloud-apps met voorwaardelijke toegang

Mensen gebruiken hun mobiele apparaten regelmatig voor zowel persoonlijke als werktaken. Hoewel organisaties ervoor zorgen dat het personeel productief kan zijn, willen ze ook voorkomen dat gegevensverlies mogelijk onveilige toepassingen krijgt. Met Voorwaardelijke toegang kunnen organisaties de toegang tot goedgekeurde (moderne verificatie-compatibele) client-apps beperken met intune-app-beveiligingsbeleid dat op hen wordt toegepast.

In dit artikel worden twee scenario's gepresenteerd voor het configureren van beleid voor voorwaardelijke toegang voor bronnen zoals Office 365, Exchange Online en SharePoint Online.

- [Scenario 1: Voor Office 365-apps zijn goedgekeurde apps met app-beveiligingsbeleid vereist](#scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies)
- [Scenario 2: Exchange Online en SharePoint Online vereisen een goedgekeurd client-app- en app-beveiligingsbeleid](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy)

In de voorwaardelijke toegang is bekend dat deze client-apps zijn beveiligd met een app-beveiligingsbeleid. Meer informatie over app-beveiligingsbeleid vindt u in het artikel, [overzicht van beleid voor app-beveiliging](/intune/apps/app-protection-policy)

Zie [Beleidsvereiste app-beveiliging](concept-conditional-access-grant.md)voor een lijst met in aanmerking komende client-apps .

> [!NOTE]
>    De of-clausule wordt gebruikt in het beleid om gebruikers in staat te stellen apps te gebruiken die het **beleid voor app-beveiliging vereisen** of Goedgekeurde besturingselementen voor de verlening van **clientapps** vereisen. Zie [Beleidsvereiste app-beveiliging](concept-conditional-access-grant.md)voor meer informatie over welke apps het subsidiebeheer voor **app-beveiliging** vereisen ondersteunen.

## <a name="scenario-1-office-365-apps-require-approved-apps-with-app-protection-policies"></a>Scenario 1: Voor Office 365-apps zijn goedgekeurde apps met app-beveiligingsbeleid vereist

In dit scenario heeft Contoso besloten dat alle mobiele toegang tot Office 365-bronnen goedgekeurde client-apps moet gebruiken, zoals Outlook Mobile, OneDrive en Microsoft Teams die worden beschermd door een beleid voor app-beveiliging voordat ze toegang krijgen. Al hun gebruikers melden zich al aan met Azure AD-referenties en hebben licenties toegewezen die Azure AD Premium P1 of P2 en Microsoft Intune bevatten.

Organisaties moeten de volgende stappen uitvoeren om het gebruik van een goedgekeurde client-app op mobiele apparaten te vereisen.

**Stap 1: Een Azure AD-beleid voor voorwaardelijke toegang configureren voor Office 365**

1. Meld u aan bij de **Azure-portal** als globale beheerder, beveiligingsbeheerder of beheerder van voorwaardelijke toegang.
1. Blader naar voorwaardelijke**toegang**voor Azure Active**Directory-beveiliging** >  **Azure Active Directory** > .
1. Selecteer **Nieuw beleid**.
1. Geef uw polis een naam. We raden organisaties aan een zinvolle standaard te maken voor de namen van hun beleid.
1. Selecteer **Gebruikers en groepen** onder **Toewijzingen**
   1. Selecteer **onder Opnemen**alle **gebruikers** of de specifieke gebruikers en **groepen waarop** u dit beleid wilt toepassen. 
   1. Selecteer **Done**.
1. Selecteer **Office 365 (voorbeeld)** onder **Cloud-apps of acties** > **Opnemen**.
1. Selecteer **Apparaatplatforms** **onder voorwaarden**.
   1. **Configureren instellen** op **Ja**.
   1. Android **Android** en **iOS**opnemen .
1. Selecteer **Onder Voorwaarden** **client-apps (voorbeeld)**.
   1. **Configureren instellen** op **Ja**.
   1. Selecteer **Mobiele apps en bureaubladclients** en **Clients met moderne verificatie**.
1. Selecteer onder **Access controls** > **Grant**de volgende opties:
   - **Goedgekeurde client-app vereisen**
   - **Beleid voor app-beveiliging vereisen (voorbeeld)**
   - **Alle geselecteerde besturingselementen vereisen**
1. Bevestig uw instellingen en stel **Beleid inschakelen** in **op Aan**.
1. Selecteer **Maken** om uw beleid te maken en in te schakelen.

**Stap 2: Een Azure AD Conditional Access-beleid configureren voor Exchange Online met ActiveSync (EAS)**

Configureer de volgende onderdelen voor het beleid voor voorwaardelijke toegang in deze stap:

1. Blader naar voorwaardelijke**toegang**voor Azure Active**Directory-beveiliging** >  **Azure Active Directory** > .
1. Selecteer **Nieuw beleid**.
1. Geef uw polis een naam. We raden organisaties aan een zinvolle standaard te maken voor de namen van hun beleid.
1. Selecteer **Gebruikers en groepen** onder **Toewijzingen**
   1. Selecteer **onder Opnemen**alle **gebruikers** of de specifieke gebruikers en **groepen waarop** u dit beleid wilt toepassen. 
   1. Selecteer **Done**.
1. Selecteer **Office 365 Exchange Online**onder **Cloud-apps of -acties** > **Include**opnemen .
1. Onder **voorwaarden**:
   1. **Client-apps (voorbeeld)**:
      1. **Configureren instellen** op **Ja**.
      1. Selecteer **mobiele apps en desktopclients** en Exchange **ActiveSync-clients**.
1. Selecteer onder **Access-besturingselementen** > **Grant**de optie **Toegang verlenen**, Beleid voor **app-beveiliging vereisen**en selecteer **Selecteren**.
1. Bevestig uw instellingen en stel **Beleid inschakelen** in **op Aan**.
1. Selecteer **Maken** om uw beleid te maken en in te schakelen.

**Stap 3: Intune-app-beveiligingsbeleid configureren voor iOS- en Android-clienttoepassingen**

Lees het artikel [Hoe u app-beveiligingsbeleid maakt en toewijst,](/intune/apps/app-protection-policies)voor stappen om beleid voor app-beveiliging voor Android en iOS te maken. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app-and-app-protection-policy"></a>Scenario 2: Exchange Online en SharePoint Online vereisen een goedgekeurd client-app- en app-beveiligingsbeleid

In dit scenario heeft Contoso besloten dat gebruikers alleen toegang hebben tot e-mail- en SharePoint-gegevens op mobiele apparaten, zolang ze een goedgekeurde client-app zoals Outlook Mobile gebruiken die wordt beschermd door een beleid voor app-beveiliging voordat ze toegang krijgen. Al hun gebruikers melden zich al aan met Azure AD-referenties en hebben licenties toegewezen die Azure AD Premium P1 of P2 en Microsoft Intune bevatten.

Organisaties moeten de volgende drie stappen uitvoeren om het gebruik van een goedgekeurde client-app op mobiele apparaten en Exchange ActiveSync-clients te vereisen.

**Stap 1: Beleid voor op Android en iOS gebaseerde moderne verificatieclients die het gebruik van een goedgekeurd client-app- en app-beveiligingsbeleid vereisen bij toegang tot Exchange Online en SharePoint Online.**

1. Meld u aan bij de **Azure-portal** als globale beheerder, beveiligingsbeheerder of beheerder van voorwaardelijke toegang.
1. Blader naar voorwaardelijke**toegang**voor Azure Active**Directory-beveiliging** >  **Azure Active Directory** > .
1. Selecteer **Nieuw beleid**.
1. Geef uw polis een naam. We raden organisaties aan een zinvolle standaard te maken voor de namen van hun beleid.
1. Selecteer **Gebruikers en groepen** onder **Toewijzingen**
   1. Selecteer **onder Opnemen**alle **gebruikers** of de specifieke gebruikers en **groepen waarop** u dit beleid wilt toepassen. 
   1. Selecteer **Done**.
1. Selecteer **Office 365 Exchange Online** en Office **365 SharePoint Online**onder **Cloud-apps of -acties** > **Include**opnemen .
1. Selecteer **Apparaatplatforms** **onder voorwaarden**.
   1. **Configureren instellen** op **Ja**.
   1. Android **Android** en **iOS**opnemen .
1. Selecteer **Onder Voorwaarden** **client-apps (voorbeeld)**.
   1. **Configureren instellen** op **Ja**.
   1. Selecteer **Mobiele apps en bureaubladclients** en **Clients met moderne verificatie**.
1. Selecteer onder **Access controls** > **Grant**de volgende opties:
   - **Goedgekeurde client-app vereisen**
   - **Beleid voor app-beveiliging vereisen (voorbeeld)**
   - **Een van de geselecteerde besturingselementen vereisen**
1. Bevestig uw instellingen en stel **Beleid inschakelen** in **op Aan**.
1. Selecteer **Maken** om uw beleid te maken en in te schakelen.

**Stap 2: Beleid voor Exchange ActiveSync-clients waarvoor een goedgekeurde client-app moet worden gebruikt.**

1. Blader naar voorwaardelijke**toegang**voor Azure Active**Directory-beveiliging** >  **Azure Active Directory** > .
1. Selecteer **Nieuw beleid**.
1. Geef uw polis een naam. We raden organisaties aan een zinvolle standaard te maken voor de namen van hun beleid.
1. Selecteer **Gebruikers en groepen** onder **Toewijzingen**
   1. Selecteer **onder Opnemen**alle **gebruikers** of de specifieke gebruikers en **groepen waarop** u dit beleid wilt toepassen. 
   1. Selecteer **Done**.
1. Selecteer **Office 365 Exchange Online**onder **Cloud-apps of -acties** > **Include**opnemen .
1. Onder **voorwaarden**:
   1. **Client-apps (voorbeeld)**:
      1. **Configureren instellen** op **Ja**.
      1. Selecteer **mobiele apps en desktopclients** en Exchange **ActiveSync-clients**.
1. Selecteer onder **Access-besturingselementen** > **Grant**de optie **Toegang verlenen**, Beleid voor **app-beveiliging vereisen**en selecteer **Selecteren**.
1. Bevestig uw instellingen en stel **Beleid inschakelen** in **op Aan**.
1. Selecteer **Maken** om uw beleid te maken en in te schakelen.

**Stap 3: Configureer het intune-app-beveiligingsbeleid voor iOS- en Android-clienttoepassingen.**

Lees het artikel [Hoe u app-beveiligingsbeleid maakt en toewijst,](/intune/apps/app-protection-policies)voor stappen om beleid voor app-beveiliging voor Android en iOS te maken. 

## <a name="next-steps"></a>Volgende stappen

[Wat is voorwaardelijke toegang?](overview.md)

[Onderdelen voor voorwaardelijke toegang](concept-conditional-access-policies.md)

[Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

