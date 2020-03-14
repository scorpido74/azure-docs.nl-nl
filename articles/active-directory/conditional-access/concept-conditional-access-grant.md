---
title: Besturings elementen toekennen in het beleid voor voorwaardelijke toegang-Azure Active Directory
description: Wat zijn besturings elementen verlenen in een beleid voor voorwaardelijke toegang van Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0716c2d4475bb538c06b9a591521fbdcfc0c80e4
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79263437"
---
# <a name="conditional-access-grant"></a>Voorwaardelijke toegang: verlenen

Binnen een beleid voor voorwaardelijke toegang kan een beheerder gebruik maken van toegangs beheer om toegang tot resources toe te kennen of te blok keren.

![Beleid voor voorwaardelijke toegang met een granting Control waarvoor multi-factor Authentication is vereist](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Toegang blok keren

De blok kering houdt rekening met eventuele toewijzingen en voor komt toegang op basis van de configuratie van het beleid voor voorwaardelijke toegang.

Blok is een krachtig besturings element dat moet worden beheerd met de juiste kennis. Het is aan te raden dat beheerders de [modus alleen rapport](concept-conditional-access-report-only.md) gebruiken om te testen voordat ze worden ingeschakeld.

## <a name="grant-access"></a>Toegang verlenen

Beheerders kunnen ervoor kiezen om een of meer besturings elementen af te dwingen bij het verlenen van toegang. Deze besturings elementen bevatten de volgende opties: 

- [Multi-factor Authentication (Azure Multi-Factor Authentication) vereisen](../authentication/concept-mfa-howitworks.md)
- [Vereisen dat het apparaat wordt gemarkeerd als compatibel (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [Hybride Azure AD-aangesloten apparaat vereisen](../devices/concept-azure-ad-join-hybrid.md)
- [Goedgekeurde client-app vereisen](app-based-conditional-access.md)
- [Beveiligings beleid voor apps vereisen](app-protection-based-conditional-access.md)

Wanneer beheerders ervoor kiezen deze opties te combi neren, kunnen ze de volgende methoden kiezen:

- Alle geselecteerde besturings elementen vereisen (besturings element **en** besturings element)
- Een van de geselecteerde besturings elementen vereisen (besturings element **of** besturings element)

Voor voorwaardelijke toegang is standaard alle geselecteerde besturings elementen vereist.

### <a name="require-multi-factor-authentication"></a>Multi-factor Authentication vereisen

Als u dit selectie vakje inschakelt, moeten gebruikers Azure Multi-Factor Authentication uitvoeren. Meer informatie over het implementeren van Azure Multi-Factor Authentication vindt u in het artikel [planning van een Azure-implementatie op basis van de cloud multi-factor Authentication](../authentication/howto-mfa-getstarted.md).

### <a name="require-device-to-be-marked-as-compliant"></a>Vereisen dat het apparaat wordt gemarkeerd als compatibel

Organisaties die Microsoft Intune hebben geïmplementeerd, kunnen de informatie gebruiken die wordt geretourneerd door hun apparaten om apparaten te identificeren die aan specifieke nalevings vereisten voldoen. Deze informatie over beleids naleving wordt vanuit intune doorgestuurd naar Azure AD, waarbij voorwaardelijke toegang kan besluiten om toegang tot resources toe te kennen of te blok keren. Zie voor meer informatie over nalevings beleid het artikel [regels instellen op apparaten om toegang tot resources in uw organisatie in te stellen met intune](/intune/protect/device-compliance-get-started).

Een apparaat kan worden gemarkeerd als compatibel door intune (voor elk besturings systeem van een apparaat) of door een MDM-systeem van derden voor Windows 10-apparaten. Jamf Pro is het enige ondersteunde MDM-systeem van derden. Meer informatie over integratie vindt u in het artikel, [Integreer Jamf Pro met intune voor naleving](/intune/protect/conditional-access-integrate-jamf).

Apparaten moeten worden geregistreerd in azure AD voordat ze kunnen worden gemarkeerd als compatibel. Meer informatie over apparaatregistratie vindt u in het artikel, [wat een apparaat-id is](../devices/overview.md).

### <a name="require-hybrid-azure-ad-joined-device"></a>Hybride Azure AD-aangesloten apparaat vereisen

Organisaties kunnen ervoor kiezen om de apparaat-id te gebruiken als onderdeel van het beleid voor voorwaardelijke toegang. Organisaties kunnen vereisen dat apparaten hybride Azure AD zijn toegevoegd met dit selectie vakje. Zie het artikel [Wat is een apparaat-id?](../devices/overview.md)voor meer informatie over apparaat-id's.

### <a name="require-approved-client-app"></a>Goedgekeurde client-apps vereisen

Organisaties kunnen vereisen dat een toegangs poging tot de geselecteerde Cloud-apps moet worden uitgevoerd vanuit een goedgekeurde client-app. Deze goedgekeurde client-apps ondersteunen het [intune-beveiligings beleid voor apps](/intune/app-protection-policy) onafhankelijk van een MDM-oplossing (Mobile-Device Management).

Deze instelling is van toepassing op de volgende iOS-en Android-apps:

- Microsoft Azure Information Protection
- Micro soft-boekingen
- Micro soft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Facturering van Microsoft
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft Office
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Micro soft Skype voor bedrijven
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Micro soft Yammer

**Opmerkingen**

- De goedgekeurde client-apps ondersteunen de functie intune-Mobile Application Management.
- De vereiste **goedgekeurde client-app vereisen** :
   - Biedt alleen ondersteuning voor de voor waarde iOS en Android voor Device platform.
- Voorwaardelijke toegang kan niet worden beschouwd als micro soft Edge in de InPrivate-modus van een goedgekeurde client-app.

Zie het artikel [How to: goedgekeurde client-apps vereisen voor toegang tot Cloud app met voorwaardelijke toegang](app-based-conditional-access.md) voor configuratie voorbeelden.

### <a name="require-app-protection-policy"></a>Beleid voor app-beveiliging vereisen

In het beleid voor voorwaardelijke toegang kunt u vereisen dat er een [intune-app-beveiligings beleid](/intune/app-protection-policy) aanwezig is op de client-app voordat toegang beschikbaar is voor de geselecteerde Cloud-apps. 

Deze instelling is van toepassing op de volgende client-apps:

- Micro soft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**Opmerkingen**

- Apps voor het app-beveiligings beleid ondersteunen de functie intune-Mobile Application Management met beleids beveiliging.
- De vereisten voor het **beveiligings beleid voor apps vereisen** :
    - Biedt alleen ondersteuning voor de voor waarde iOS en Android voor Device platform.

Zie het artikel [procedure: app-beveiligings beleid en een goedgekeurde client-app vereisen voor toegang tot Cloud-apps met voorwaardelijke toegang](app-protection-based-conditional-access.md) voor configuratie voorbeelden.

### <a name="terms-of-use"></a>Gebruiksvoorwaarden

Als uw organisatie gebruiks voorwaarden heeft gemaakt, kunnen er aanvullende opties worden weer gegeven onder besturings elementen verlenen. Met deze opties kunnen beheerders bevestigings voorwaarden vereisen als voor waarde voor het openen van de bronnen die door het beleid worden beveiligd. Meer informatie over de gebruiks voorwaarden vindt u in het artikel [Azure Active Directory gebruiks voorwaarden](terms-of-use.md).

## <a name="next-steps"></a>Volgende stappen

- [Voorwaardelijke toegang: sessie besturings elementen](concept-conditional-access-session.md)

- [Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

- [Modus alleen rapport](concept-conditional-access-report-only.md)
