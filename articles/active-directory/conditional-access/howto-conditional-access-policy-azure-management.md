---
title: Voorwaardelijke toegang - MFA vereisen voor Azure-beheer - Azure Active Directory
description: Een aangepast beleid voor voorwaardelijke toegang maken om meervoudige verificatie voor Azure-beheertaken te vereisen
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
ms.openlocfilehash: b92d833e6f32821ad907ff966771bbba8bbb77ce
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80755181"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>Voorwaardelijke toegang: MFA vereisen voor Azure-beheer

Organisaties gebruiken verschillende Azure-services en beheren deze vanuit azure resource beheergebaseerde hulpprogramma's zoals:

* Azure Portal
* Azure PowerShell
* Azure CLI

Deze hulpprogramma's kunnen zeer bevoorrechte toegang bieden tot bronnen, die configuraties voor het hele abonnement, service-instellingen en abonnementsfacturering kunnen wijzigen. Om deze bevoegde bronnen te beschermen, raadt Microsoft aan om meervoudige verificatie te vereisen voor elke gebruiker die toegang heeft tot deze bronnen.

## <a name="user-exclusions"></a>Gebruikersuitsluitingen

Beleid voor voorwaardelijke toegang zijn krachtige tools, we raden u aan de volgende accounts uit uw beleid uit te sluiten:

* **Noodtoegang** of **break-glass-accounts** om tenant-brede accountlock-out te voorkomen. In het onwaarschijnlijke scenario dat alle beheerders zijn uitgesloten van uw tenant, kan uw beheeraccount met noodtoegang worden gebruikt om zich aan te melden bij de tenant om de toegang te herstellen.
   * Meer informatie vindt u in het artikel, [Accounts voor noodtoegang beheren in Azure AD](../users-groups-roles/directory-emergency-access.md).
* **Serviceaccounts** en **serviceprincipals,** zoals het Azure AD Connect Sync-account. Serviceaccounts zijn niet-interactieve accounts die niet aan een bepaalde gebruiker zijn gekoppeld. Ze worden normaal gesproken gebruikt door back-endservices die programmatische toegang tot toepassingen mogelijk maken, maar worden ook gebruikt om in te loggen op systemen voor administratieve doeleinden. Serviceaccounts zoals deze moeten worden uitgesloten, omdat MFA niet programmatisch kan worden voltooid. Oproepen van serviceprincipals worden niet geblokkeerd door Voorwaardelijke toegang.
   * Als uw organisatie deze accounts in scripts of code heeft gebruikt, u overwegen deze te vervangen door [beheerde identiteiten.](../managed-identities-azure-resources/overview.md) Als tijdelijke tijdelijke oplossing u deze specifieke accounts uitsluiten van het basislijnbeleid.

## <a name="create-a-conditional-access-policy"></a>Beleid voor voorwaardelijke toegang maken

Met de volgende stappen u een beleid voor voorwaardelijke toegang maken om de toegewezen beheerdersrollen te verplichten multifactorauthenticatie uit te voeren.

1. Meld u aan bij de **Azure-portal** als globale beheerder, beveiligingsbeheerder of beheerder van voorwaardelijke toegang.
1. Blader naar voorwaardelijke**toegang**voor Azure Active**Directory-beveiliging** >  **Azure Active Directory** > .
1. Selecteer **Nieuw beleid**.
1. Geef uw polis een naam. We raden organisaties aan een zinvolle standaard te maken voor de namen van hun beleid.
1. Selecteer **Gebruikers en groepen** onder **Toewijzingen**
   1. Selecteer Alle **gebruikers** **onder Opnemen**.
   1. Selecteer **onder Uitsluiten**de optie Gebruikers en **groepen** en kies de noodtoegangs- of breakglas-accounts van uw organisatie. 
   1. Selecteer **Done**.
1. Selecteer **onder Cloud-apps of -acties** > **Include**, selecteer **Apps selecteren,** kies Microsoft Azure **Management**en selecteer **Selecteer** Vervolgens **Gereed selecteren**.
1. Stel **Configureren** in op **Ja**en selecteer **Gereed**onder **Apps** > **voor client (voorbeeld)** instellen .
1. Selecteer **onder Access-besturingselementen** > **Grant**de optie **Toegang verlenen**, Meervoudige verificatie **vereisen**en selecteer **Selecteren**.
1. Bevestig uw instellingen en stel **Beleid inschakelen** in **op Aan**.
1. Selecteer **Maken** om te maken om uw beleid in te schakelen.

## <a name="next-steps"></a>Volgende stappen

[Gemeenschappelijk beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Impact bepalen met de modus Alleen voor rapportvoorwaardelijke toegang](howto-conditional-access-report-only.md)

[Aanmeldingsgedrag simuleren met het gereedschap Welke als voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)
