---
title: Voorwaardelijke toegang - MFA vereisen voor alle gebruikers - Azure Active Directory
description: Een aangepast beleid voor voorwaardelijke toegang maken om van alle gebruikers te eisen dat ze multifactorauthenticatie uitvoeren
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
ms.openlocfilehash: c3e7b1a656c92e37a709b57dae463f6644003e42
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755185"
---
# <a name="conditional-access-require-mfa-for-all-users"></a>Voorwaardelijke toegang: MFA vereisen voor alle gebruikers

Zoals Alex Weinert, de Directory of Identity Security bij Microsoft, vermeldt in zijn blog post [Your Pa$$word maakt niet uit:](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)

> Uw wachtwoord maakt niet uit, maar MFA wel! Op basis van onze studies is uw account meer dan 99,9% minder kans om te worden gecompromitteerd als u MFA gebruikt.

De richtlijnen in dit artikel helpen uw organisatie een evenwichtig MFA-beleid voor uw omgeving te maken.

## <a name="user-exclusions"></a>Gebruikersuitsluitingen

Beleid voor voorwaardelijke toegang zijn krachtige tools, we raden u aan de volgende accounts uit uw beleid uit te sluiten:

* **Noodtoegang** of **break-glass-accounts** om tenant-brede accountlock-out te voorkomen. In het onwaarschijnlijke scenario dat alle beheerders zijn uitgesloten van uw tenant, kan uw beheeraccount met noodtoegang worden gebruikt om zich aan te melden bij de tenant om de toegang te herstellen.
   * Meer informatie vindt u in het artikel, [Accounts voor noodtoegang beheren in Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Serviceaccounts** en **serviceprincipals,** zoals het Azure AD Connect Sync-account. Serviceaccounts zijn niet-interactieve accounts die niet aan een bepaalde gebruiker zijn gekoppeld. Ze worden normaal gesproken gebruikt door back-endservices die programmatische toegang tot toepassingen mogelijk maken, maar worden ook gebruikt om in te loggen op systemen voor administratieve doeleinden. Serviceaccounts zoals deze moeten worden uitgesloten, omdat MFA niet programmatisch kan worden voltooid. Oproepen van serviceprincipals worden niet geblokkeerd door Voorwaardelijke toegang.
   * Als uw organisatie deze accounts in scripts of code heeft gebruikt, u overwegen deze te vervangen door [beheerde identiteiten.](../managed-identities-azure-resources/overview.md) Als tijdelijke tijdelijke oplossing u deze specifieke accounts uitsluiten van het basislijnbeleid.

## <a name="application-exclusions"></a>Uitsluitingen van aanvragen

Organisaties kunnen veel cloudtoepassingen in gebruik hebben. Niet al deze toepassingen kunnen een gelijke beveiliging vereisen. Bijvoorbeeld, de payroll en aanwezigheid aanvragen kunnen vereisen MFA, maar de cafetaria waarschijnlijk niet. Beheerders kunnen ervoor kiezen om specifieke toepassingen uit te sluiten van hun beleid.

## <a name="create-a-conditional-access-policy"></a>Beleid voor voorwaardelijke toegang maken

Met de volgende stappen u een beleid voor voorwaardelijke toegang maken om de toegewezen beheerdersrollen te verplichten multifactorauthenticatie uit te voeren.

1. Meld u aan bij de **Azure-portal** als globale beheerder, beveiligingsbeheerder of beheerder van voorwaardelijke toegang.
1. Blader naar voorwaardelijke**toegang**voor Azure Active**Directory-beveiliging** >  **Azure Active Directory** > .
1. Selecteer **Nieuw beleid**.
1. Geef uw polis een naam. We raden organisaties aan een zinvolle standaard te maken voor de namen van hun beleid.
1. Selecteer **Gebruikers en groepen** onder **Toewijzingen**
   1. Selecteer Alle **gebruikers** **selecteren onder Opnemen**
   1. Selecteer **onder Uitsluiten**de optie Gebruikers en **groepen** en kies de noodtoegangs- of breakglas-accounts van uw organisatie. 
   1. Selecteer **Done**.
1. Selecteer **Onder Cloud-apps of acties** > **Omvatten**alle **cloud-apps**.
   1. Selecteer **onder Uitsluiten**alle toepassingen waarvoor geen meervoudige verificatie vereist is.
1. Stel **Configureren** in op **Ja**en selecteer **Gereed**onder **Apps** > **voor client (voorbeeld)** instellen .
1. Selecteer **onder Access-besturingselementen** > **Grant**de optie **Toegang verlenen**, Meervoudige verificatie **vereisen**en selecteer **Selecteren**.
1. Bevestig uw instellingen en stel **Beleid inschakelen** in **op Aan**.
1. Selecteer **Maken** om te maken om uw beleid in te schakelen.

### <a name="named-locations"></a>Benoemde locaties

Organisaties kunnen ervoor kiezen om bekende netwerklocaties die bekend staan als **Benoemde locaties** op te nemen in hun beleid voor voorwaardelijke toegang. Deze genoemde locaties kunnen vertrouwde IPv4-netwerken bevatten, zoals die voor een hoofdkantoorlocatie. Zie het artikel [Wat is de locatievoorwaarde in Azure Active Directory Conditional Access voor](location-condition.md) meer informatie over het configureren van benoemde locaties?

In het bovenstaande voorbeeldbeleid kan een organisatie ervoor kiezen om geen meervoudige verificatie nodig te hebben als ze toegang krijgen tot een cloud-app vanuit hun bedrijfsnetwerk. In dit geval kunnen ze de volgende configuratie toevoegen aan het beleid:

1. Selecteer **onder Toewijzingen** **voorwaardenlocaties** > **Locations**.
   1. **Configureren Ja**.
   1. Voeg **elke locatie toe.**
   1. Alle **vertrouwde locaties**uitsluiten .
   1. Selecteer **Done**.
1. Selecteer **Done**.
1. Sla uw beleidswijzigingen **op.**

## <a name="next-steps"></a>Volgende stappen

[Gemeenschappelijk beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Impact bepalen met de modus Alleen voor rapportvoorwaardelijke toegang](howto-conditional-access-report-only.md)

[Aanmeldingsgedrag simuleren met het gereedschap Welke als voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)
