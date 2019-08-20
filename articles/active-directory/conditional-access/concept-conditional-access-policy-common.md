---
title: Algemene beleids regels voor voorwaardelijke toegang-Azure Active Directory
description: Veelgebruikte beleids regels voor voorwaardelijke toegang voor organisaties
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
ms.openlocfilehash: abcca0d2712a462e4d2ecf9c8023d0cb0e68ad6c
ms.sourcegitcommit: 5ded08785546f4a687c2f76b2b871bbe802e7dae
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69576678"
---
# <a name="common-conditional-access-policies"></a>Algemeen beleid voor voorwaardelijke toegang

Basis beveiligings beleid is geweldig, maar veel organisaties hebben meer flexibiliteit nodig dan ze bieden. Veel organisaties hebben bijvoorbeeld de mogelijkheid om specifieke accounts uit te sluiten, zoals de accounts voor toegang in nood gevallen of het afbreken van een account van het beleid voor voorwaardelijke toegang waarvoor multi-factor Authentication is vereist. De algemene beleids regels waarnaar in dit artikel wordt verwezen, kunnen worden gebruikt voor deze organisaties.

![Beleid voor voorwaardelijke toegang in de Azure Portal](./media/concept-conditional-access-policy-common/conditional-access-policies-azure-ad-listing.png)

## <a name="emergency-access-accounts"></a>Accounts voor toegang in nood gevallen

Meer informatie over accounts voor toegang in nood gevallen en waarom ze belang rijk zijn, vindt u in de volgende artikelen: 

* [Accounts voor nood toegang beheren in azure AD](../users-groups-roles/directory-emergency-access.md)
* [Maak een flexibele toegangs beheer strategie met Azure Active Directory](../authentication/concept-resilient-controls.md)

## <a name="typical-policies-deployed-by-organizations"></a>Typische beleids regels die door organisaties worden geïmplementeerd

* [MFA vereisen voor beheerders](howto-conditional-access-policy-admin-mfa.md)
* [MFA vereisen voor Azure-beheer](howto-conditional-access-policy-azure-management.md)
* [Verouderde verificatie blok keren](howto-conditional-access-policy-block-legacy.md)
* [Voorwaardelijke toegang op basis van een risico (vereist Azure AD Premium P2)](howto-conditional-access-policy-risk.md)
* [Vertrouwde locatie vereisen voor MFA-registratie](howto-conditional-access-policy-registration.md)
* [Toegang op locatie blok keren](howto-conditional-access-policy-location.md)
* [Compatibel apparaat vereisen](howto-conditional-access-policy-compliant-device.md)

## <a name="next-steps"></a>Volgende stappen

[Aanmeld gedrag simuleren met het What If hulp programma voor voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)
