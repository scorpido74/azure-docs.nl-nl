---
title: Algemene beleids regels voor voorwaardelijke toegang-Azure Active Directory
description: Veelgebruikte beleids regels voor voorwaardelijke toegang voor organisaties
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5cfe6b790d5eb2877e46845317779c89cf33884
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75978008"
---
# <a name="common-conditional-access-policies"></a>Algemeen beleid voor voorwaardelijke toegang

De [standaard instellingen voor beveiliging](../fundamentals/concept-fundamentals-security-defaults.md) zijn handig voor sommige organisaties, maar er zijn meer flexibiliteit nodig dan ze bieden. Zo hebben veel de mogelijkheid om specifieke accounts uit te sluiten, zoals de accounts voor toegang via nood gevallen of het verbreken van verbindingen van beleids regels voor voorwaardelijke toegang waarvoor multi-factor Authentication is vereist. De algemene beleids regels waarnaar in dit artikel wordt verwezen, kunnen worden gebruikt voor deze organisaties.

![Beleid voor voorwaardelijke toegang in de Azure Portal](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Accounts voor toegang in nood gevallen

Meer informatie over accounts voor toegang in nood gevallen en waarom ze belang rijk zijn, vindt u in de volgende artikelen: 

* [Accounts voor nood toegang beheren in azure AD](../users-groups-roles/directory-emergency-access.md)
* [Maak een flexibele toegangs beheer strategie met Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>Typische beleids regels die door organisaties worden geïmplementeerd

* [MFA vereisen voor beheerders](howto-conditional-access-policy-admin-mfa.md)\*
* [MFA vereisen voor Azure-beheer](howto-conditional-access-policy-azure-management.md)\*
* [MFA vereisen voor alle gebruikers](howto-conditional-access-policy-all-users-mfa.md)\*
* [Verouderde verificatie\* blok keren](howto-conditional-access-policy-block-legacy.md)
* [Voorwaardelijke toegang op basis van een risico (vereist Azure AD Premium P2)](howto-conditional-access-policy-risk.md)
* [Vertrouwde locatie vereisen voor MFA-registratie](howto-conditional-access-policy-registration.md)
* [Toegang op locatie blok keren](howto-conditional-access-policy-location.md)
* [Compatibel apparaat vereisen](howto-conditional-access-policy-compliant-device.md)

\* deze vier beleids regels wanneer ze samen worden geconfigureerd, imiteert de functionaliteit ingeschakeld door de [standaard instellingen voor beveiliging](../fundamentals/concept-fundamentals-security-defaults.md).

## <a name="next-steps"></a>Volgende stappen

- [Simuleer aanmeldings gedrag met het hulp programma voor voorwaardelijke toegang What If.](troubleshoot-conditional-access-what-if.md)
- [Gebruik de modus alleen rapport voor voorwaardelijke toegang om de impact van nieuwe beleids beslissingen te bepalen.](concept-conditional-access-report-only.md)
