---
title: Een beleid voor voorwaardelijke toegang bouwen-Azure Active Directory
description: Wat zijn de beschik bare opties voor het bouwen van een beleid voor voorwaardelijke toegang en wat betekenen ze?
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80295326"
---
# <a name="building-a-conditional-access-policy"></a>Beleid voor voorwaardelijke toegang bouwen

Zoals beschreven in het artikel [Wat is voorwaardelijke toegang](overview.md), is een beleid voor voorwaardelijke toegang een instructie if-then, van **toewijzingen** en **toegangs beheer**. Een beleid voor voorwaardelijke toegang brengt signalen samen, om beslissingen te nemen en organisatie beleid af te dwingen.

Hoe maakt een organisatie dit beleid? Wat is vereist?

![Voorwaardelijke toegang (signalen + beslissingen + afdwinging = beleid)](./media/concept-conditional-access-policies/conditional-access-signal-decision-enforcement.png)

## <a name="assignments"></a>Toewijzingen

In het gedeelte toewijzingen bepaalt u de wie, wat en waar van het beleid voor voorwaardelijke toegang.

### <a name="users-and-groups"></a>Gebruikers en groepen

[Gebruikers en groepen](concept-conditional-access-users-groups.md) geven aan wie het beleid moet worden opgenomen of uitgesloten. Deze toewijzing kan alle gebruikers, specifieke groepen gebruikers, Directory rollen of externe gast gebruikers bevatten. 

### <a name="cloud-apps-or-actions"></a>Cloud-apps of acties

[Cloud-apps of-acties](concept-conditional-access-cloud-apps.md) kunnen Cloud toepassingen of gebruikers acties die voor het beleid gelden, opnemen of uitsluiten.

### <a name="conditions"></a>Voorwaarden

Een beleid kan meerdere [voor waarden](concept-conditional-access-conditions.md)bevatten.

#### <a name="sign-in-risk"></a>Aanmeldings risico

Voor organisaties met [Azure AD Identity Protection](../identity-protection/overview.md)kunnen de gegenereerde risico detecties van invloed zijn op uw beleid voor voorwaardelijke toegang.

#### <a name="device-platforms"></a>Apparaatplatformen

Organisaties met meerdere besturingssysteem platforms kunnen specifieke beleids regels afdwingen op verschillende platformen. 

De gegevens die worden gebruikt om het platform te berekenen, zijn afkomstig van niet-geverifieerde bronnen, zoals teken reeksen van gebruikers agenten die kunnen worden gewijzigd.

#### <a name="locations"></a>Locaties

Locatie gegevens worden verschaft door gegevens over de geolocatie van het IP-adres. Beheerders kunnen kiezen om locaties te definiëren en ervoor te kiezen om ze te markeren als vertrouwd, zoals die voor de netwerk locaties van hun organisatie.

#### <a name="client-apps"></a>Client-apps

Beleid voor voorwaardelijke toegang is standaard van toepassing op browser-apps, mobiele apps en desktop-clients die ondersteuning bieden voor moderne verificatie. 

Deze toewijzings voorwaarde staat beleids regels voor voorwaardelijke toegang toe om specifieke client toepassingen te richten die niet gebruikmaken van moderne verificatie. Deze toepassingen zijn onder andere Exchange ActiveSync-clients, oudere Office-toepassingen die geen gebruik maken van moderne verificatie en e-mail protocollen zoals IMAP, MAPI, POP en SMTP.

#### <a name="device-state"></a>Apparaatstatus

Dit besturings element wordt gebruikt om apparaten uit te sluiten die zijn toegevoegd aan hybride Azure AD of die compatibel zijn met intune. Deze uitsluiting kan worden uitgevoerd om onbeheerde apparaten te blok keren. 

## <a name="access-controls"></a>Besturingselementen voor toegang

In het gedeelte toegangs beheer van het beleid voor voorwaardelijke toegang wordt bepaald hoe een beleid wordt afgedwongen.

### <a name="grant"></a>Verlenen

[Grant](concept-conditional-access-grant.md) biedt beheerders een methode voor het afdwingen van beleid waarbij ze toegang kunnen blok keren of verlenen.

#### <a name="block-access"></a>Toegang blokkeren

Toegang blok keren alleen dat houdt in dat de toegang onder de opgegeven toewijzingen wordt geblokkeerd. De blok kering van het besturings element is krachtig en moet worden beheerd met de juiste kennis.

#### <a name="grant-access"></a>Toegang verlenen

De granting Control kan de afdwinging van een of meer besturings elementen activeren. 

- Multi-factor Authentication (Azure Multi-Factor Authentication) vereisen
- Vereisen dat het apparaat wordt gemarkeerd als compatibel (intune)
- Hybride Azure AD-aangesloten apparaat vereisen
- Goedgekeurde client-app vereisen
- Beleid voor app-beveiliging vereisen

Beheerders kunnen ervoor kiezen om een van de vorige besturings elementen of alle geselecteerde besturings elementen te vereisen met behulp van de volgende opties. De standaard instelling voor meerdere besturings elementen is alle nood zakelijk.

- Alle geselecteerde besturings elementen vereisen (besturings element en besturings element)
- Een van de geselecteerde besturings elementen vereisen (besturings element of besturings element)

### <a name="session"></a>Sessie

[Sessie besturings elementen](concept-conditional-access-session.md) kunnen de ervaring beperken 

- Door apps afgedwongen beperkingen gebruiken
   - Werkt momenteel alleen met Exchange Online en share point online.
      - Hiermee worden apparaatgegevens door gegeven om het beheer van de ervaring met volledige of beperkte toegang toe te staan.
- App-beheer voor voorwaardelijke toegang gebruiken
   - Gebruikt signalen van Microsoft Cloud App Security om dingen als: 
      - Het downloaden, knippen, kopiëren en afdrukken van gevoelige documenten blok keren.
      - Het gedrag van Risk ante sessies bewaken.
      - Labels van gevoelige bestanden vereisen.
- Aanmeldings frequentie
   - De mogelijkheid om de standaard aanmeldings frequentie voor moderne verificatie te wijzigen.
- Permanente browser sessie
   - Hiermee kunnen gebruikers zich blijven aanmelden nadat ze het browser venster hebben gesloten en opnieuw hebben geopend.

## <a name="simple-policies"></a>Eenvoudig beleid

Een beleid voor voorwaardelijke toegang moet mini maal het volgende bevatten om af te dwingen:

- De **naam** van het beleid.
- **Toewijzingen**
   - **Gebruikers en/of groepen** waarop het beleid moet worden toegepast.
   - **Cloud-apps of-acties** waarop het beleid moet worden toegepast.
- **Besturingselementen voor toegang**
   - Besturings elementen **toekennen** of **blok keren**

![Leeg beleid voor voorwaardelijke toegang](./media/concept-conditional-access-policies/conditional-access-blank-policy.png)

Het artikel [common Conditional Access policies](concept-conditional-access-policy-common.md) bevat enkele beleids regels die van belang zijn voor de meeste organisaties.

## <a name="next-steps"></a>Volgende stappen

[Aanmeld gedrag simuleren met het What If hulp programma voor voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)

[Planning van een cloudgebaseerde Azure Multi-Factor Authentication-implementatie](../authentication/howto-mfa-getstarted.md)

[Apparaatcompatibiliteit beheren met intune](/intune/device-compliance-get-started)

[Microsoft Cloud App Security en voorwaardelijke toegang](/cloud-app-security/proxy-intro-aad)
