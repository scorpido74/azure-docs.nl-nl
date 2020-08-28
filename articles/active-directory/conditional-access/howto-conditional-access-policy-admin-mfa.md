---
title: 'Voorwaardelijke toegang: MFA vereisen voor beheerders-Azure Active Directory'
description: Een aangepast beleid voor voorwaardelijke toegang maken zodat beheerders multi-factor Authentication kunnen uitvoeren
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 08/03/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 45263ca0448042aa972ee53093b51dd47bd51190
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89049346"
---
# <a name="conditional-access-require-mfa-for-administrators"></a>Voorwaardelijke toegang: MFA vereisen voor beheerders

Accounts waaraan beheerders rechten zijn toegewezen, zijn gericht op aanvallers. Het vereisen van multi-factor Authentication (MFA) voor deze accounts is een eenvoudige manier om het risico te verkleinen dat deze accounts worden aangetast.

Micro soft raadt u aan MFA verplicht te stellen voor de volgende rollen ten minste:

* Factureringsbeheerder
* Beheerder van voorwaardelijke toegang
* Exchange-beheerder
* Globale beheerder
* Help Desk (wacht woord) beheerder
* Wachtwoordbeheerder
* Beveiligingsbeheerder
* SharePoint-beheerder
* Gebruikersbeheerder

Organisaties kunnen ervoor kiezen om rollen op te nemen of uit te sluiten zoals ze passen.

## <a name="user-exclusions"></a>Gebruikers uitsluitingen

Beleids regels voor voorwaardelijke toegang zijn krachtige hulp middelen. u wordt aangeraden de volgende accounts uit te sluiten van het beleid:

* **Nood toegang** of **afbreek glazen** om te voor komen dat accounts voor tenants worden vergrendeld. In het onwaarschijnlijke scenario zijn alle beheerders vergrendeld van uw Tenant. u kunt uw beheer account voor nood toegang gebruiken om u aan te melden bij de Tenant stappen nemen om de toegang te herstellen.
   * Meer informatie vindt u in het artikel [manage accounts voor nood toegang in azure AD](../users-groups-roles/directory-emergency-access.md).
* **Service accounts** en **service-principals**, zoals het Azure AD Connect Sync-account. Service accounts zijn niet-interactieve accounts die niet zijn gekoppeld aan een bepaalde gebruiker. Ze worden normaal gesp roken gebruikt door back-end-services die programmatische toegang tot toepassingen toestaan, maar worden ook gebruikt om u aan te melden bij systemen voor administratieve doel einden. Service accounts zoals deze moeten worden uitgesloten omdat MFA niet programmatisch kan worden voltooid. Aanroepen van service-principals worden niet geblokkeerd door voorwaardelijke toegang.
   * Als uw organisatie deze accounts in gebruik heeft in scripts of code, kunt u overwegen deze te vervangen door [beheerde identiteiten](../managed-identities-azure-resources/overview.md). Als tijdelijke oplossing kunt u deze specifieke accounts uitsluiten van het basislijn beleid.

## <a name="create-a-conditional-access-policy"></a>Beleid voor voorwaardelijke toegang maken

De volgende stappen helpen u bij het maken van een beleid voor voorwaardelijke toegang om te vereisen dat aan deze toegewezen beheerders rollen multi-factor Authentication wordt uitgevoerd.

1. Meld u aan bij de **Azure Portal** als globale beheerder, beveiligings beheerder of beheerder van de voorwaardelijke toegang.
1. Blader naar **Azure Active Directory**  >  **beveiligings**  >  **voorwaardelijke toegang**.
1. Selecteer **Nieuw beleid**.
1. Geef uw beleid een naam. Het is raadzaam dat organisaties een zinvolle norm maken voor de namen van hun beleid.
1. Onder **toewijzingen**selecteert u **gebruikers en groepen**
   1. Onder **insluiten**selecteert u **Directory rollen (preview)** en kiest u ten minste de volgende rollen:
      * Verificatie beheerder
      * Factureringsbeheerder
      * Beheerder van voorwaardelijke toegang
      * Exchange-beheerder
      * Globale beheerder
      * Helpdesk beheerder
      * Wachtwoordbeheerder
      * Beveiligingsbeheerder
      * SharePoint-beheerder
      * Gebruikersbeheerder
   
      > [!WARNING]
      > Het beleid voor voorwaardelijke toegang biedt geen ondersteuning voor gebruikers die zijn toegewezen aan een directory-rol [binnen een administratieve eenheid](../users-groups-roles/roles-admin-units-assign-roles.md) of Directory-rollen die rechtstreeks aan een object zijn gekoppeld, zoals via [aangepaste rollen](../users-groups-roles/roles-create-custom.md).

   1. Onder **uitsluiten**selecteert u **gebruikers en groepen** en kiest u de accounts voor nood toegang of het afbreek glas van uw organisatie. 
   1. Selecteer **Gereed**.
1. Onder **Cloud-apps of acties**  >  **Include**, selecteert u **alle Cloud-apps**en selecteert u **gereed**.
1. Onder **voor waarden**  >  **client-apps (preview)**, onder **Selecteer de client-apps waarop dit beleid van toepassing is om** alle geselecteerde standaard instellingen te behouden en selecteer **gereed**.
1. Onder **toegangs beheer**  >  **toekennen**selecteert u **toegang verlenen**, **multi-factor Authentication vereisen**en selecteert u **selecteren**.
1. Bevestig de instellingen en stel **beleid inschakelen** in **op aan**.
1. Selecteer **maken** om uw beleid in te stellen.

## <a name="next-steps"></a>Volgende stappen

[Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Effect bepalen met de modus alleen rapport-alleen voor voorwaardelijke toegang](howto-conditional-access-insights-reporting.md)

[Aanmeld gedrag simuleren met het What If hulp programma voor voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)
