---
title: Een beleid voor voorwaardelijke toegang maken - Azure Active Directory
description: Wat zijn alle opties beschikbaar om een beleid voor voorwaardelijke toegang op te bouwen en wat betekenen ze?
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
ms.openlocfilehash: 907ad8705742e4b2e38b13c3c675ebd333bd27d2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295326"
---
# <a name="building-a-conditional-access-policy"></a>Een beleid voor voorwaardelijke toegang bouwen

Zoals uitgelegd in het artikel [Wat is voorwaardelijke toegang](overview.md), een voorwaardelijke toegang beleid is een als-dan-instructie, van Toewijzingen en Access **besturingselementen**. **Assignments** Een beleid voor voorwaardelijke toegang brengt signalen samen, om beslissingen te nemen en organisatorisch beleid af te dwingen.

Hoe maakt een organisatie dit beleid? Wat is er nodig?

![Voorwaardelijke toegang (signalen + beslissingen + handhaving = beleid)](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

## <a name="assignments"></a>Toewijzingen

Het gedeelte Toewijzingen bepaalt het wie, wat en waar van het beleid voorwaardelijke toegang.

### <a name="users-and-groups"></a>Gebruikers en groepen

[Gebruikers en groepen](concept-conditional-access-users-groups.md) wijzen toe wie het beleid zal bevatten of uitsluiten. Deze toewijzing kan alle gebruikers, specifieke groepen gebruikers, directoryrollen of externe gastgebruikers omvatten. 

### <a name="cloud-apps-or-actions"></a>Cloud-apps of acties

[Cloud-apps of -acties](concept-conditional-access-cloud-apps.md) kunnen cloudtoepassingen of gebruikersacties bevatten of uitsluiten die onder het beleid vallen.

### <a name="conditions"></a>Voorwaarden

Een beleid kan meerdere [voorwaarden](concept-conditional-access-conditions.md)bevatten.

#### <a name="sign-in-risk"></a>Aanmeldingsrisico

Voor organisaties met [Azure AD Identity Protection](../identity-protection/overview.md)kunnen de risicodetecties die daar worden gegenereerd, uw beleid voor voorwaardelijke toegang beïnvloeden.

#### <a name="device-platforms"></a>Apparaatplatformen

Organisaties met platforms voor meerdere apparaten besturingssysteem kunnen specifiek beleid op verschillende platforms afdwingen. 

De informatie die wordt gebruikt om het apparaatplatform te berekenen, is afkomstig van niet-geverifieerde bronnen, zoals tekenreeksen van gebruikersagenten die kunnen worden gewijzigd.

#### <a name="locations"></a>Locaties

Locatiegegevens worden verstrekt door IP-geolocatiegegevens. Beheerders kunnen ervoor kiezen om locaties te definiëren en ervoor kiezen om sommige te markeren als vertrouwde zoals die voor de netwerklocaties van hun organisatie.

#### <a name="client-apps"></a>Client-apps

Standaard is het beleid voor voorwaardelijke toegang van toepassing op browser-apps, mobiele apps en desktopclients die moderne verificatie ondersteunen. 

Met deze toewijzingsvoorwaarde kan het beleid voor voorwaardelijke toegang specifieke clienttoepassingen targeten die geen moderne verificatie gebruiken. Deze toepassingen omvatten Exchange ActiveSync-clients, oudere Office-toepassingen die geen moderne verificatie gebruiken en e-mailprotocollen zoals IMAP, MAPI, POP en SMTP.

#### <a name="device-state"></a>Apparaatstatus

Dit besturingselement wordt gebruikt om apparaten uit te sluiten waarvan hybride Azure AD is toegetreden of om een compatibele in Intune te hebben gemarkeerd. Deze uitsluiting kan worden gedaan om onbeheerde apparaten te blokkeren. 

## <a name="access-controls"></a>Besturingselementen voor toegang

Het gedeelte met toegangscontroles van het beleid voor voorwaardelijke toegang bepaalt hoe een beleid wordt afgedwongen.

### <a name="grant"></a>Verlenen

[Grant](concept-conditional-access-grant.md) biedt beheerders een middel voor beleidshandhaving waar ze toegang kunnen blokkeren of verlenen.

#### <a name="block-access"></a>Toegang blokkeren

Blok toegang doet precies dat, zal het blokkeren van de toegang onder de opgegeven toewijzingen. De blokcontrole is krachtig en moet met de juiste kennis worden gehanteerd.

#### <a name="grant-access"></a>Toegang verlenen

De subsidiecontrole kan leiden tot handhaving van een of meer controles. 

- Multifactorauthenticatie vereisen (Azure Multi-Factor Authentication)
- Apparaat vereisen dat is gemarkeerd als compatibel (Intune)
- Hybride Azure AD-apparaat vereisen
- Goedgekeurde client-app vereisen
- Beleid voor app-beveiliging vereisen

Beheerders kunnen ervoor kiezen om een van de vorige besturingselementen of alle geselecteerde besturingselementen te vereisen met behulp van de volgende opties. De standaardinstelling voor meerdere besturingselementen is om alles nodig te hebben.

- Alle geselecteerde besturingselementen vereisen (besturingselement en besturing)
- Een van de geselecteerde besturingselementen vereisen (besturingselement of besturingselement)

### <a name="session"></a>Sessie

[Sessiebesturingselementen](concept-conditional-access-session.md) kunnen de ervaring beperken 

- Beperkingen voor app-afgedwongen beperkingen gebruiken
   - Werkt momenteel alleen met Exchange Online en SharePoint Online.
      - Geeft apparaatinformatie door om controle te krijgen over de ervaring die volledige of beperkte toegang verleent.
- Beheer van voorwaardelijke toegang-apps gebruiken
   - Gebruikt signalen van Microsoft Cloud App Security om bijvoorbeeld te doen: 
      - Download, knippen, kopiëren en afdrukken van gevoelige documenten blokkeren.
      - Controleer risicovol sessiegedrag.
      - Vereisen etikettering van gevoelige bestanden.
- Aanmeldingsfrequentie
   - Mogelijkheid om de standaardaanmeldingsfrequentie voor moderne verificatie te wijzigen.
- Permanente browsersessie
   - Hiermee kunnen gebruikers ingelogd blijven na het sluiten en heropenen van hun browservenster.

## <a name="simple-policies"></a>Eenvoudig beleid

Een beleid voor voorwaardelijke toegang moet ten minste het volgende bevatten dat moet worden afgedwongen:

- **Naam** van het beleid.
- **Toewijzingen**
   - **Gebruikers en/of groepen** om het beleid op toe te passen.
   - **Cloud-apps of -acties** om het beleid op toe te passen.
- **Besturingselementen voor toegang**
   - **Subsidie-** of **blokkeringsbesturingselementen**

![Beleid voor voorwaardelijke toegang leeg](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

Het artikel [Common Conditional Access-beleid](concept-conditional-access-policy-common.md) bevat een aantal beleidsregels waarvan we denken dat het nuttig zou zijn voor de meeste organisaties.

## <a name="next-steps"></a>Volgende stappen

[Aanmeldingsgedrag simuleren met het gereedschap Welke als voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)

[Planning van een cloudgebaseerde Azure Multi-Factor Authentication-implementatie](../authentication/howto-mfa-getstarted.md)

[Apparaatnaleving met Intune beheren](/intune/device-compliance-get-started)

[Beveiliging en voorwaardelijke toegang van Microsoft Cloud-apps](/cloud-app-security/proxy-intro-aad)
