---
title: Common Conditional Access-beleid - Azure Active Directory
description: Veelgebruikte beleid voor voorwaardelijke toegang voor organisaties
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 20b699f0672b49dd2f947e0cf00d0ffcef7961e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295312"
---
# <a name="common-conditional-access-policies"></a>Algemeen beleid voor voorwaardelijke toegang

[Beveiligingsstandaards](../fundamentals/concept-fundamentals-security-defaults.md) zijn voor sommige bedrijven ideaal, maar veel organisaties hebben meer flexibiliteit nodig dan ze bieden. Velen hebben bijvoorbeeld de mogelijkheid nodig om specifieke accounts zoals hun noodtoegangs- of break-glass-beheeraccounts uit te sluiten van beleid voor voorwaardelijke toegang waarvoor multifactorauthenticatie vereist is. Voor deze organisaties kan het algemene beleid waarnaar in dit artikel wordt verwezen, van nut zijn.

![Beleid voor voorwaardelijke toegang in de Azure-portal](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Accounts voor noodtoegang

Meer informatie over noodtoegangsaccounts en waarom deze belangrijk zijn, vindt u in de volgende artikelen: 

* [Noodtoegangsaccounts beheren in Azure AD](../users-groups-roles/directory-emergency-access.md)
* [Een beheerstrategie voor veerkrachtigtoegangsbeheer maken met Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>Typisch beleid dat door organisaties wordt geïmplementeerd

* [Verouderde verificatie blokkeren](howto-conditional-access-policy-block-legacy.md)\*
* [MFA vereisen voor beheerders](howto-conditional-access-policy-admin-mfa.md)\*
* [MFA vereisen voor Azure-beheer](howto-conditional-access-policy-azure-management.md)\*
* [MFA vereisen voor alle gebruikers](howto-conditional-access-policy-all-users-mfa.md)\*

\*Deze vier beleidsregels die samen zijn geconfigureerd, zouden functionaliteit nabootsen die is ingeschakeld door [beveiligingsstandaardinstellingen.](../fundamentals/concept-fundamentals-security-defaults.md)

## <a name="additional-policies"></a>Aanvullend beleid

* [Op risico's gebaseerde voorwaardelijke toegang (vereist Azure AD Premium P2)](howto-conditional-access-policy-risk.md)
* [Vertrouwde locatie vereisen voor MFA-registratie](howto-conditional-access-policy-registration.md)
* [Toegang per locatie blokkeren](howto-conditional-access-policy-location.md)
* [Compatibel apparaat vereisen](howto-conditional-access-policy-compliant-device.md)
* [Toegang blokkeren, behalve specifieke apps](howto-conditional-access-policy-block-access.md)

## <a name="next-steps"></a>Volgende stappen

- [Stel aanmeldingsgedrag met behulp van het gereedschap Voorwaardelijke toegang wat als.](troubleshoot-conditional-access-what-if.md)

- [Gebruik de modus alleen-rapport voor voorwaardelijke toegang om de impact van nieuwe beleidsbeslissingen te bepalen.](concept-conditional-access-report-only.md)
