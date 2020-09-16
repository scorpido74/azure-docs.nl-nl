---
title: Azure-Multi-Factor Authentication voor uw organisatie-Azure Active Directory
description: Meer informatie over de beschik bare functies van Azure Multi-Factor Authentication voor uw organisatie op basis van uw licentie model
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/18/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13bb7782f6396466200fbb3e2df77158bb75d8a4
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2020
ms.locfileid: "90705398"
---
# <a name="overview-of-azure-multi-factor-authentication-for-your-organization"></a>Overzicht van Azure Multi-Factor Authentication voor uw organisatie

Er zijn meerdere manieren om Azure-Multi-Factor Authentication in te scha kelen voor uw Azure Active Directory (AD)-gebruikers op basis van de licenties die uw organisatie bezit. 

![Signalen onderzoeken en MFA afdwingen, indien nodig](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Op basis van onze studies is uw account meer dan 99,9% minder risico als u gebruikmaakt van multi-factor Authentication (MFA).

Hoe schakelt uw organisatie MFA zelfs gratis in voordat u een statistiek krijgt?

## <a name="free-option"></a>Gratis optie

Klanten die gebruikmaken van de gratis voor delen van Azure AD, kunnen gebruikmaken van de [standaard instellingen](../fundamentals/concept-fundamentals-security-defaults.md) om multi-factor Authentication in hun omgeving in te scha kelen.

## <a name="microsoft-365-business-e3-or-e5"></a>Microsoft 365 Business, E3 of E5

Voor klanten met Microsoft 365 zijn er twee opties:

* Azure Multi-Factor Authentication is ingeschakeld of uitgeschakeld voor alle gebruikers, voor alle aanmeld gebeurtenissen. Het is niet mogelijk om multi-factor Authentication in te scha kelen voor een subset van gebruikers of alleen onder bepaalde scenario's. Beheer gaat via de Office 365-Portal. 
* Voor een betere gebruikers ervaring kunt u een upgrade uitvoeren naar Azure AD Premium P1 of P2 en voorwaardelijke toegang gebruiken. Zie voor meer informatie Secure Microsoft 365 resources with multi-factor Authentication (Engelstalig).

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Voor klanten met Azure AD Premium P1 of soort gelijke licenties die deze functionaliteit bevatten, zoals Enterprise Mobility + Security E3, Microsoft 365 F1 of Microsoft 365 E3: 

Gebruik [voorwaardelijke toegang van Azure AD](../authentication/tutorial-enable-azure-mfa.md) om gebruikers te vragen om multi-factor Authentication tijdens bepaalde scenario's of gebeurtenissen die aan uw bedrijfs vereisten voldoen.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Voor klanten met Azure AD Premium P2 of vergelijk bare licenties die deze functionaliteit bevatten, zoals Enterprise Mobility + Security E5 of Microsoft 365 E5: 

Biedt de sterkste beveiligings positie en verbeterde gebruikers ervaring. Voegt [op Risico's gebaseerde voorwaardelijke toegang](../conditional-access/howto-conditional-access-policy-risk.md) toe aan de Azure AD Premium P1-functies die worden aangepast aan de patronen van gebruikers en minimaliseert multi-factor Authentication-prompts.

## <a name="authentication-methods"></a>Verificatiemethoden

| Methode | Standaardinstellingen voor de beveiliging | Alle andere methoden |
| --- | --- | --- |
| Melding via mobiele app | X | X |
| Verificatie code van de mobiele app of het hardware-token |   | X |
| SMS-bericht naar telefoon |   | X |
| Bellen naar telefoon |   | X |

## <a name="next-steps"></a>Volgende stappen

Als u aan de slag wilt gaan, raadpleegt u de zelf studie voor het [beveiligen van aanmeldings gebeurtenissen van gebruikers met Azure multi-factor Authentication](../authentication/tutorial-enable-azure-mfa.md).

Zie voor meer informatie over licenties de [functies en licenties voor Azure multi-factor Authentication](../authentication/concept-mfa-licensing.md).
