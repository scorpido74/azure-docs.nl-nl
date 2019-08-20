---
title: 'Voorwaardelijke toegang: MFA vereisen voor Azure Management-Azure Active Directory'
description: Een aangepast beleid voor voorwaardelijke toegang maken om multi-factor Authentication voor Azure-beheer taken te vereisen
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 884d96fae226497991f5642072d040ccd81134b0
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576743"
---
# <a name="conditional-access-require-mfa-for-azure-management"></a>Voorwaardelijke toegang: MFA vereisen voor Azure-beheer

Organisaties gebruiken verschillende Azure-Services en beheren ze via Azure Resource Manager op basis van hulpprogram ma's zoals:

* Azure Portal
* Azure PowerShell
* Azure-CLI

Deze hulpprogram ma's kunnen uiterst privileged toegang bieden tot bronnen, waarmee u de volledige configuratie van abonnementen, Service-instellingen en abonnements facturering kunt wijzigen. Voor het beveiligen van deze geprivilegieerde resources raadt micro soft aan om multi-factor Authentication te vereisen voor elke gebruiker die toegang tot deze bronnen heeft.

## <a name="user-exclusions"></a>Gebruikers uitsluitingen

Beleids regels voor voorwaardelijke toegang zijn krachtige hulp middelen. u wordt aangeraden de volgende accounts uit te sluiten van het beleid:

* **Nood toegang** of **afbreek glazen** om te voor komen dat accounts voor tenants worden vergrendeld. In het onwaarschijnlijke scenario zijn alle beheerders vergrendeld van uw Tenant. u kunt uw beheer account voor nood toegang gebruiken om u aan te melden bij de Tenant stappen nemen om de toegang te herstellen.
   * Meer informatie vindt u in het artikel [manage accounts voor nood toegang in azure AD](../users-groups-roles/directory-emergency-access.md).
* **Service accounts** en **service principes**, zoals het Azure AD Connect Sync-account. Service accounts zijn niet-interactieve accounts die niet zijn gekoppeld aan een bepaalde gebruiker. Ze worden normaal gesp roken gebruikt door back-end-services en kunnen programmatische toegang tot toepassingen bieden. Service accounts moeten worden uitgesloten omdat MFA niet programmatisch kan worden voltooid.
   * Als uw organisatie deze accounts in gebruik heeft in scripts of code, kunt u overwegen deze te vervangen door [beheerde identiteiten](../managed-identities-azure-resources/overview.md). Als tijdelijke oplossing kunt u deze specifieke accounts uitsluiten van het basislijn beleid.

## <a name="create-a-conditional-access-policy"></a>Beleid voor voorwaardelijke toegang maken

De volgende stappen helpen u bij het maken van een beleid voor voorwaardelijke toegang om te vereisen dat aan deze toegewezen beheerders rollen multi-factor Authentication wordt uitgevoerd.

1. Meld u aan bij de **Azure Portal** als globale beheerder, beveiligings beheerder of beheerder van de voorwaardelijke toegang.
1. Blader naar **Azure Active Directory** > **voorwaardelijke toegang**.
1. Selecteer **Nieuw beleid**.
1. Geef uw beleid een naam. Het is raadzaam dat organisaties een zinvolle norm maken voor de namen van hun beleid.
1. Onder **toewijzingen**selecteert u **gebruikers en groepen**
   1. Onder **insluiten**selecteert u **alle gebruikers**.
   1. Onder **uitsluiten**selecteert u **gebruikers en groepen** en kiest u de accounts voor nood toegang of het afbreek glas van uw organisatie. 
   1. Selecteer **Done**.
1. Onder **Cloud-apps of acties** > , selecteert u **apps selecteren**, kiest u **Microsoft Azure beheer**en selecteert u vervolgens **op** **gereed**.
1. Onder **toegangs beheer** > **toekennen**selecteert u **toegang verlenen**, **multi-factor Authentication vereisen**en selecteert u **selecteren**.
1. Bevestig de instellingen en stel **beleid inschakelen** in **op aan**.
1. Selecteer **maken** om uw beleid in te stellen.

## <a name="next-steps"></a>Volgende stappen

[Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Aanmeld gedrag simuleren met het What If hulp programma voor voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)
