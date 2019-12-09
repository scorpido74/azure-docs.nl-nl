---
title: Multi-Factor Authentication voor uw organisatie inschakelen-Azure Active Directory
description: Azure MFA voor uw organisatie inschakelen op basis van uw licentie
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 12/06/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe0d79f228ee2b84c00a35d4836cbda6a4847a42
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/08/2019
ms.locfileid: "74932448"
---
# <a name="enable-multi-factor-authentication-for-your-organization"></a>Multi-Factor Authentication inschakelen voor uw organisatie

Er zijn meerdere manieren om Azure Multi-Factor Authentication (MFA) in te scha kelen voor uw Azure Active Directory (AD)-gebruikers op basis van de licenties die uw organisatie bezit. 

![Signalen onderzoeken en MFA afdwingen, indien nodig](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Op basis van onze studies is uw account meer dan 99,9% minder kans op schade als u MFA gebruikt.

Hoe schakelt uw organisatie multi-factor Authentication zelfs gratis in, voordat u een statistiek krijgt?

## <a name="free-option"></a>Gratis optie

Klanten die gebruikmaken van de gratis voor delen van Azure AD, kunnen gebruikmaken van de [standaard instellingen](../fundamentals/concept-fundamentals-security-defaults.md) om multi-factor Authentication in hun omgeving in te scha kelen.

## <a name="office-365"></a>Office 365

Voor klanten met Office 365 zijn er twee opties:

- [Standaard instellingen voor beveiliging](concept-fundamentals-security-defaults.md) kunnen worden ingeschakeld via Azure AD om al uw gebruikers te beschermen met Azure multi-factor Authentication.
- Als uw organisatie meer granulatie vereist in het bieden van multi-factor Authentication, bevatten uw Office-licenties [per gebruiker MFA](../authentication/howto-mfa-userstates.md) -mogelijkheden. MFA per gebruiker is ingeschakeld en wordt afgedwongen op elke gebruiker afzonderlijk door beheerders.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Voor klanten met Azure AD Premium P1 of soort gelijke licenties die deze functionaliteit bevatten, zoals Enterprise Mobility + Security E3, Microsoft 365 F1 of Microsoft 365 E3: 

Het is raadzaam om het [beleid voor voorwaardelijke toegang](../conditional-access/concept-conditional-access-policy-common.md) te gebruiken voor de beste gebruikers ervaring.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Voor klanten met Azure AD Premium P2 of vergelijk bare licenties die deze functionaliteit bevatten, zoals Enterprise Mobility + Security E5 of Microsoft 365 E5: 

Het verdient aanbeveling om [beleid voor voorwaardelijke toegang](../conditional-access/concept-conditional-access-policy-common.md) samen met beleids regels voor [identiteits beveiliging](../identity-protection/overview-v2.md) te gebruiken voor de beste gebruikers ervaring en handhavings flexibiliteit.

## <a name="authentication-methods"></a>Authenticatiemethoden

|   | Standaardinstellingen voor de beveiliging | Alle andere methoden |
| --- | --- | --- |
| Melding via mobiele app | X | X |
| Verificatiecode van mobiele app of hardwaretoken |   | X |
| Sms-bericht naar telefoon |   | X |
| Bellen naar telefoon |   | X |
| App-wachtwoorden |   | X * * |

\* * App-wacht woorden zijn alleen beschikbaar in MFA per gebruiker met verouderde verificatie scenario's als deze zijn ingeschakeld door beheerders.

## <a name="next-steps"></a>Volgende stappen

[Pagina met prijzen voor Azure AD](https://azure.microsoft.com/pricing/details/active-directory/)
