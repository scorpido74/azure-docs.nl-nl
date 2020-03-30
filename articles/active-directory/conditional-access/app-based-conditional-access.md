---
title: Goedgekeurde client-apps met voorwaardelijke toegang - Azure Active Directory
description: Meer informatie over het vereisen van goedgekeurde client-apps voor toegang tot cloud-apps met voorwaardelijke toegang in Azure Active Directory.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: article
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: spunukol, rosssmi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7a215e2bb7d9d1cf9013414037383590456296cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480892"
---
# <a name="how-to-require-approved-client-apps-for-cloud-app-access-with-conditional-access"></a>How to: Goedgekeurde client-apps vereisen voor toegang tot cloud-apps met voorwaardelijke toegang

Mensen gebruiken hun mobiele apparaten regelmatig voor zowel persoonlijke als werktaken. Hoewel organisaties ervoor zorgen dat het personeel productief kan zijn, willen ze ook voorkomen dat gegevensverlies mogelijk onveilige toepassingen krijgt. Met Voorwaardelijke toegang kunnen organisaties de toegang tot goedgekeurde (moderne verificatie-compatibele) client-apps beperken.

In dit artikel worden twee scenario's gepresenteerd voor het configureren van beleid voor voorwaardelijke toegang voor bronnen zoals Office 365, Exchange Online en SharePoint Online.

- [Scenario 1: Voor Office 365-apps is een goedgekeurde client-app vereist](#scenario-1-office-365-apps-require-an-approved-client-app)
- [Scenario 2: Exchange Online en SharePoint Online vereisen een goedgekeurde client-app](#scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app)

In Voorwaardelijke toegang wordt deze functionaliteit bekend als een goedgekeurde client-app. Zie [vereiste goedgekeurde client-app](concept-conditional-access-grant.md#require-approved-client-app)voor een lijst met goedgekeurde client-apps .

> [!NOTE]
> Om goedgekeurde client-apps voor iOS- en Android-apparaten te vereisen, moeten deze apparaten zich eerst registreren in Azure AD.

## <a name="scenario-1-office-365-apps-require-an-approved-client-app"></a>Scenario 1: Voor Office 365-apps is een goedgekeurde client-app vereist

In dit scenario heeft Contoso besloten dat gebruikers die mobiele apparaten gebruiken toegang hebben tot alle Office 365-services, zolang ze goedgekeurde client-apps gebruiken, zoals Outlook Mobile, OneDrive en Microsoft Teams. Al hun gebruikers melden zich al aan met Azure AD-referenties en hebben licenties toegewezen die Azure AD Premium P1 of P2 en Microsoft Intune bevatten.

Organisaties moeten de volgende drie stappen uitvoeren om het gebruik van een goedgekeurde client-app op mobiele apparaten te vereisen.

**Stap 1: Beleid voor op Android en iOS gebaseerde moderne verificatieclients die het gebruik van een goedgekeurde clienttoepassing vereisen bij toegang tot Exchange Online.**

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
1. Selecteer onder **Access-besturingselementen** > **Grant**de optie **Toegang verlenen**, **Goedgekeurde client-app vereisen**en selecteer **Selecteren**.
1. Bevestig uw instellingen en stel **Beleid inschakelen** in **op Aan**.
1. Selecteer **Maken** om uw beleid te maken en in te schakelen.

**Stap 2: Een Azure AD Conditional Access-beleid configureren voor Exchange Online met ActiveSync (EAS)**

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
1. Selecteer onder **Access-besturingselementen** > **Grant**de optie **Toegang verlenen**, **Goedgekeurde client-app vereisen**en selecteer **Selecteren**.
1. Bevestig uw instellingen en stel **Beleid inschakelen** in **op Aan**.
1. Selecteer **Maken** om uw beleid te maken en in te schakelen.

**Stap 3: Configureer het intune-app-beveiligingsbeleid voor iOS- en Android-clienttoepassingen.**

Lees het artikel [Hoe u app-beveiligingsbeleid maakt en toewijst,](/intune/apps/app-protection-policies)voor stappen om beleid voor app-beveiliging voor Android en iOS te maken. 

## <a name="scenario-2-exchange-online-and-sharepoint-online-require-an-approved-client-app"></a>Scenario 2: Exchange Online en SharePoint Online vereisen een goedgekeurde client-app

In dit scenario heeft Contoso besloten dat gebruikers alleen toegang hebben tot e-mail- en SharePoint-gegevens op mobiele apparaten, zolang ze een goedgekeurde client-app zoals Outlook Mobile gebruiken. Al hun gebruikers melden zich al aan met Azure AD-referenties en hebben licenties toegewezen die Azure AD Premium P1 of P2 en Microsoft Intune bevatten.

Organisaties moeten de volgende drie stappen uitvoeren om het gebruik van een goedgekeurde client-app op mobiele apparaten en Exchange ActiveSync-clients te vereisen.

**Stap 1: Beleid voor op Android en iOS gebaseerde moderne verificatieclients die het gebruik van een goedgekeurde clienttoepassing vereisen bij toegang tot Exchange Online en SharePoint Online.**

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
1. Selecteer onder **Access-besturingselementen** > **Grant**de optie **Toegang verlenen**, **Goedgekeurde client-app vereisen**en selecteer **Selecteren**.
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
1. Selecteer onder **Access-besturingselementen** > **Grant**de optie **Toegang verlenen**, **Goedgekeurde client-app vereisen**en selecteer **Selecteren**.
1. Bevestig uw instellingen en stel **Beleid inschakelen** in **op Aan**.
1. Selecteer **Maken** om uw beleid te maken en in te schakelen.

**Stap 3: Configureer het intune-app-beveiligingsbeleid voor iOS- en Android-clienttoepassingen.**

Lees het artikel [Hoe u app-beveiligingsbeleid maakt en toewijst,](/intune/apps/app-protection-policies)voor stappen om beleid voor app-beveiliging voor Android en iOS te maken. 

## <a name="next-steps"></a>Volgende stappen

[Wat is voorwaardelijke toegang?](overview.md)

[Onderdelen voor voorwaardelijke toegang](concept-conditional-access-policies.md)

[Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)
