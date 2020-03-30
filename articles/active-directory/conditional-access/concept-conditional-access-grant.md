---
title: Besturingselementen verlenen in beleid voor voorwaardelijke toegang - Azure Active Directory
description: Wat zijn subsidiebesturingselementen in een Azure AD Conditional Access-beleid
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 02ec8dace971cd4dc1407c9e8d20839504c9ecc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80331840"
---
# <a name="conditional-access-grant"></a>Voorwaardelijke toegang: subsidie

Binnen een beleid voor voorwaardelijke toegang kan een beheerder gebruik maken van toegangsbesturingselementen om toegang tot bronnen te verlenen of te blokkeren.

![Voorwaardelijke toegangsbeleid met een subsidiebeheer dat meervoudige verificatie vereist](./media/concept-conditional-access-grant/conditional-access-grant.png)

## <a name="block-access"></a>Toegang blokkeren

Block houdt rekening met eventuele toewijzingen en voorkomt toegang op basis van de configuratie van het beleid voorwaardelijke toegang.

Block is een krachtige controle die moet worden gehanteerd met de juiste kennis. Het is iets wat beheerders moeten gebruiken [Report-only modus](concept-conditional-access-report-only.md) om te testen voordat het inschakelen.

## <a name="grant-access"></a>Toegang verlenen

Beheerders kunnen ervoor kiezen om een of meer besturingselementen af te dwingen bij het verlenen van toegang. Deze besturingselementen omvatten de volgende opties: 

- [Multifactorauthenticatie vereisen (Azure Multi-Factor Authentication)](../authentication/concept-mfa-howitworks.md)
- [Vereisen dat het apparaat is gemarkeerd als compatibel (Microsoft Intune)](/intune/protect/device-compliance-get-started)
- [Hybride Azure AD-apparaat vereisen](../devices/concept-azure-ad-join-hybrid.md)
- [Goedgekeurde client-app vereisen](app-based-conditional-access.md)
- [Beleid voor app-beveiliging vereisen](app-protection-based-conditional-access.md)

Wanneer beheerders ervoor kiezen deze opties te combineren, kunnen ze de volgende methoden kiezen:

- Alle geselecteerde besturingselementen vereisen (besturingselement **en** besturingselement)
- Een van de geselecteerde besturingselementen vereisen (besturingselement **OF** besturingselement)

Voor voorwaardelijke toegang zijn standaard alle geselecteerde besturingselementen vereist.

### <a name="require-multi-factor-authentication"></a>Meervoudige verificatie vereisen

Als u dit selectievakje inschakelt, moeten gebruikers Azure Multi-Factor Authentication uitvoeren. Meer informatie over het implementeren van Azure Multi-Factor Authentication vindt u in het artikel [Planning a cloud-based Azure Multi-Factor Authentication deployment](../authentication/howto-mfa-getstarted.md).

### <a name="require-device-to-be-marked-as-compliant"></a>Apparaat vereisen dat het als compatibel is gemarkeerd

Organisaties die Microsoft Intune hebben geïmplementeerd, kunnen de vanaf hun apparaten geretourneerde informatie gebruiken om apparaten te identificeren die voldoen aan specifieke nalevingsvereisten. Deze nalevingsgegevens van het beleid worden doorgestuurd van Intune naar Azure AD, waar voorwaardelijke toegang beslissingen kan nemen om toegang tot bronnen te verlenen of te blokkeren. Zie het artikel Regels instellen [op apparaten om toegang te krijgen tot bronnen in uw organisatie met Intune](/intune/protect/device-compliance-get-started)voor meer informatie over nalevingsbeleid.

Een apparaat kan worden gemarkeerd als compatibel met Intune (voor elk apparaatbesturingssysteem) of door een MDM-systeem van derden voor Windows 10-apparaten. Jamf pro is het enige ondersteunde MDM-systeem van derden. Meer informatie over integratie is te vinden in het artikel, [Integreer Jamf Pro met Intune voor compliance.](/intune/protect/conditional-access-integrate-jamf)

Apparaten moeten zijn geregistreerd in Azure AD voordat ze als compatibel kunnen worden gemarkeerd. Meer informatie over apparaatregistratie is te vinden in het artikel, [Wat is een apparaatidentiteit.](../devices/overview.md)

### <a name="require-hybrid-azure-ad-joined-device"></a>Hybride Azure AD-apparaat vereisen

Organisaties kunnen ervoor kiezen om de apparaatidentiteit te gebruiken als onderdeel van hun beleid voor voorwaardelijke toegang. Organisaties kunnen vereisen dat apparaten hybride Azure AD zijn die zijn samengevoegd met dit selectievakje. Zie het artikel [Wat is een apparaatidentiteit?](../devices/overview.md)

### <a name="require-approved-client-app"></a>Goedgekeurde client-app vereisen

Organisaties kunnen eisen dat een toegangspoging tot de geselecteerde cloud-apps moet worden uitgevoerd vanuit een goedgekeurde client-app. Deze goedgekeurde client-apps ondersteunen [intune-app-beveiligingsbeleid,](/intune/app-protection-policy) onafhankelijk van een MDM-oplossing (Mobile Device Management).

Om gebruik te kunnen maken van dit subsidiebeheer, vereist Voorwaardelijke toegang dat het apparaat wordt geregistreerd in Azure Active Directory, waarvoor het gebruik van een broker-app vereist is. De broker-app kan de Microsoft-Authenticator voor iOS of de Microsoft-bedrijfsportal voor Android-apparaten zijn. Als een broker-app niet op het apparaat is geïnstalleerd wanneer de gebruiker probeert te verifiëren, wordt de gebruiker doorgestuurd naar de app store om de broker-app te installeren.

Deze instelling is van toepassing op de volgende iOS- en Android-apps:

- Microsoft Azure-informatiebeveiliging
- Microsoft Bookings
- Microsoft Cortana
- Microsoft Dynamics 365
- Microsoft Edge
- Microsoft Excel
- Microsoft Flow
- Microsoft Intune Managed Browser
- Microsoft Invoicing
- Microsoft Kaizala
- Microsoft Launcher
- Microsoft Office
- Microsoft Office Hub
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerApps
- Microsoft Power BI
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype voor Bedrijven
- Microsoft StaffHub
- Microsoft Stream
- Microsoft Teams
- Microsoft To-Do
- Microsoft Visio
- Microsoft Word
- Microsoft Yammer
- Microsoft Whiteboard

**Opmerkingen**

- De goedgekeurde client-apps ondersteunen de functie Voor mobiel toepassingsbeheer van Intune.
- De vereiste **goedgekeurde clientapp vereisen:**
   - Ondersteunt alleen de conditie van iOS en Android voor apparaatplatform.
   - Een broker-app is vereist om het apparaat te registreren. Op iOS, de makelaar app is Microsoft Authenticator en op Android, het is Intune Company Portal app.
- Voorwaardelijke toegang kan Microsoft Edge in de InPrivate-modus niet beschouwen als een goedgekeurde client-app.

Zie het [artikel, Hoe: Goedgekeurde client-apps vereisen voor toegang tot de cloud-app met voorwaardelijke toegang](app-based-conditional-access.md) voor configuratievoorbeelden.

### <a name="require-app-protection-policy"></a>Beleid voor app-beveiliging vereisen

In uw beleid voor voorwaardelijke toegang u vereisen dat een [Intune-app-beveiligingsbeleid](/intune/app-protection-policy) aanwezig is in de client-app voordat toegang beschikbaar is voor de geselecteerde cloud-apps. 

Om gebruik te kunnen maken van dit subsidiebeheer, vereist Voorwaardelijke toegang dat het apparaat wordt geregistreerd in Azure Active Directory, waarvoor het gebruik van een broker-app vereist is. De broker-app kan de Microsoft-Authenticator voor iOS of de Microsoft-bedrijfsportal voor Android-apparaten zijn. Als een broker-app niet op het apparaat is geïnstalleerd wanneer de gebruiker probeert te verifiëren, wordt de gebruiker doorgestuurd naar de app store om de broker-app te installeren.

Deze instelling is van toepassing op de volgende client-apps:

- Microsoft Cortana
- Microsoft OneDrive
- Microsoft Outlook
- Microsoft Planner

**Opmerkingen**

- Apps voor app-beveiligingsbeleid ondersteunen de functie Voor mobiel toepassingsbeheer inTune met beleidsbeveiliging.
- De **beleidsvereisten voor app-beveiliging vereisen:**
    - Ondersteunt alleen de conditie van iOS en Android voor apparaatplatform.
    - Een broker-app is vereist om het apparaat te registreren. Op iOS, de makelaar app is Microsoft Authenticator en op Android, het is Intune Company Portal app.

Zie het [artikel, Hoe: App-beveiligingsbeleid vereisen en een goedgekeurde client-app voor toegang tot de cloud-app met voorwaardelijke toegang](app-protection-based-conditional-access.md) voor configuratievoorbeelden.

### <a name="terms-of-use"></a>Gebruiksvoorwaarden

Als uw organisatie gebruiksvoorwaarden heeft gemaakt, zijn er mogelijk extra opties zichtbaar onder subsidiebesturingselementen. Met deze opties kunnen beheerders een bevestiging van de gebruiksvoorwaarden vereisen als voorwaarde voor toegang tot de resources die door het beleid worden beschermd. Meer informatie over gebruiksvoorwaarden vindt u in het artikel, [Azure Active Directory- gebruiksvoorwaarden.](terms-of-use.md)

## <a name="next-steps"></a>Volgende stappen

- [Voorwaardelijke toegang: sessiebesturingselementen](concept-conditional-access-session.md)

- [Gemeenschappelijk beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

- [Modus Alleen rapporteren](concept-conditional-access-report-only.md)
