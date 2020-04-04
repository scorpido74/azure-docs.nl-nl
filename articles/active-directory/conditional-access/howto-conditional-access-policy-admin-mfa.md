---
title: Voorwaardelijke toegang - MFA vereisen voor beheerders - Azure Active Directory
description: Een aangepast beleid voor voorwaardelijke toegang maken om beheerders te verplichten multifactorauthenticatie uit te voeren
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6d00c80fe679f1e104d27ff16ead306f555f467d
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631866"
---
# <a name="conditional-access-require-mfa-for-administrators"></a>Voorwaardelijke toegang: MFA vereisen voor beheerders

Accounts die beheerdersrechten toegewezen hebben, zijn het doelwit van aanvallers. Het vereisen van multi-factor authenticatie (MFA) op deze accounts is een eenvoudige manier om het risico van deze accounts worden aangetast te verminderen.

Microsoft raadt u aan minimaal MFA voor de volgende rollen te vereisen:

* Factureringsbeheerder
* Beheerder van voorwaardelijke toegang
* Exchange-beheerder
* Globale beheerder
* Helpdesk (Wachtwoord) beheerder
* Wachtwoordbeheerder
* Beveiligingsbeheerder
* SharePoint-beheerder
* Gebruikersbeheerder

Organisaties kunnen ervoor kiezen om rollen op te nemen of uit te sluiten zoals ze dat nodig achten.

## <a name="user-exclusions"></a>Gebruikersuitsluitingen

Beleid voor voorwaardelijke toegang zijn krachtige tools, we raden u aan de volgende accounts uit uw beleid uit te sluiten:

* **Noodtoegang** of **break-glass-accounts** om tenant-brede accountlock-out te voorkomen. In het onwaarschijnlijke scenario dat alle beheerders zijn uitgesloten van uw tenant, kan uw beheeraccount met noodtoegang worden gebruikt om zich aan te melden bij de tenant om de toegang te herstellen.
   * Meer informatie vindt u in het artikel, [Accounts voor noodtoegang beheren in Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Serviceaccounts** en **serviceprincipals,** zoals het Azure AD Connect Sync-account. Serviceaccounts zijn niet-interactieve accounts die niet aan een bepaalde gebruiker zijn gekoppeld. Ze worden normaal gesproken gebruikt door back-endservices die programmatische toegang tot toepassingen mogelijk maken, maar worden ook gebruikt om in te loggen op systemen voor administratieve doeleinden. Serviceaccounts zoals deze moeten worden uitgesloten, omdat MFA niet programmatisch kan worden voltooid.
   * Als uw organisatie deze accounts in scripts of code heeft gebruikt, u overwegen deze te vervangen door [beheerde identiteiten.](../managed-identities-azure-resources/overview.md) Als tijdelijke tijdelijke oplossing u deze specifieke accounts uitsluiten van het basislijnbeleid.

## <a name="create-a-conditional-access-policy"></a>Beleid voor voorwaardelijke toegang maken

Met de volgende stappen u een beleid voor voorwaardelijke toegang maken om de toegewezen beheerdersrollen te verplichten multifactorauthenticatie uit te voeren.

1. Meld u aan bij de **Azure-portal** als globale beheerder, beveiligingsbeheerder of beheerder van voorwaardelijke toegang.
1. Blader naar voorwaardelijke**toegang**voor Azure Active**Directory-beveiliging** >  **Azure Active Directory** > .
1. Selecteer **Nieuw beleid**.
1. Geef uw polis een naam. We raden organisaties aan een zinvolle standaard te maken voor de namen van hun beleid.
1. Selecteer **Gebruikers en groepen** onder **Toewijzingen**
   1. Selecteer **onder Opnemen** **directoryrollen (voorbeeld)** en kies minimaal de volgende rollen:
      * Verificatiebeheerder
      * Factureringsbeheerder
      * Beheerder van voorwaardelijke toegang
      * Exchange-beheerder
      * Globale beheerder
      * Helpdeskbeheerder
      * Wachtwoordbeheerder
      * Beveiligingsbeheerder
      * SharePoint-beheerder
      * Gebruikersbeheerder
   1. Selecteer **onder Uitsluiten**de optie Gebruikers en **groepen** en kies de noodtoegangs- of breakglas-accounts van uw organisatie. 
   1. Selecteer **Done**.
1. Selecteer Onder **Cloud-apps of -acties** > **Opnemen** **alle cloud-apps**en selecteer **Gereed**.
1. Stel **Configureren** in op **Ja**en selecteer **Gereed**onder **Apps** > **voor client (voorbeeld)** instellen .
1. Selecteer **onder Access-besturingselementen** > **Grant**de optie **Toegang verlenen**, Meervoudige verificatie **vereisen**en selecteer **Selecteren**.
1. Bevestig uw instellingen en stel **Beleid inschakelen** in **op Aan**.
1. Selecteer **Maken** om te maken om uw beleid in te schakelen.

## <a name="next-steps"></a>Volgende stappen

[Gemeenschappelijk beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Impact bepalen met de modus Alleen voor rapportvoorwaardelijke toegang](howto-conditional-access-report-only.md)

[Aanmeldingsgedrag simuleren met het gereedschap Welke als voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)
