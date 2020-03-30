---
title: Azure Multi-Factor Authentication voor uw organisatie - Azure Active Directory
description: Meer informatie over de beschikbare functies van Azure Multi-Factor Authentication voor uw organisatie op basis van uw licentiemodel
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
ms.openlocfilehash: febdb708c637ac322c0ca884eae627da9bd5904c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530390"
---
# <a name="overview-of-azure-multi-factor-authentication-for-your-organization"></a>Overzicht van Azure Multi-Factor Authentication voor uw organisatie

Er zijn meerdere manieren om Azure Multi-Factor Authentication in te schakelen voor uw Azure Active Directory (AD) gebruikers op basis van de licenties die uw organisatie bezit. 

![Onderzoek signalen en dwing MFA af indien nodig](./media/concept-fundamentals-mfa-get-started/verify-signals-and-perform-mfa-if-required.png)

Op basis van onze studies is uw account meer dan 99,9% minder kans om te worden gecompromitteerd als u multi-factor authenticatie (MFA) gebruikt.

Dus hoe werkt uw organisatie turn op MFA zelfs gratis, voordat ze een statistiek?

## <a name="free-option"></a>Gratis optie

Klanten die gebruikmaken van de gratis voordelen van Azure AD, kunnen [beveiligingsstandaards](../fundamentals/concept-fundamentals-security-defaults.md) gebruiken om meervoudige verificatie in hun omgeving in te schakelen.

## <a name="office-365-business-premium-e3-or-e5"></a>Office 365 Business Premium, E3 of E5

Voor klanten met Office 365 zijn er twee opties:

* Azure Multi-Factor Authentication is ingeschakeld of uitgeschakeld voor alle gebruikers, voor alle aanmeldingsgebeurtenissen. Er is geen mogelijkheid om alleen multi-factor authenticatie in te schakelen voor een subset van gebruikers, of alleen onder bepaalde scenario's. Beheer is via de Office 365-portal. 
* Upgrade naar Azure AD Premium P1 of P2 voor een verbeterde gebruikerservaring en gebruik Voorwaardelijke toegang. Zie Secure Office 365-bronnen met meervoudige verificatie voor meer informatie.

## <a name="azure-ad-premium-p1"></a>Azure AD Premium P1

Voor klanten met Azure AD Premium P1 of soortgelijke licenties die deze functionaliteit bevatten, zoals Enterprise Mobility + Security E3, Microsoft 365 F1 of Microsoft 365 E3: 

Gebruik [voorwaardelijke toegang voor Azure AD](../conditional-access/overview.md) om gebruikers te vragen om meervoudige verificatie tijdens bepaalde scenario's of gebeurtenissen die aan uw bedrijfsvereisten voldoen.

## <a name="azure-ad-premium-p2"></a>Azure AD Premium P2

Voor klanten met Azure AD Premium P2 of soortgelijke licenties die deze functionaliteit bevatten, zoals Enterprise Mobility + Security E5 of Microsoft 365 E5: 

Biedt de sterkste beveiligingspositie en verbeterde gebruikerservaring. Hiermee voegt [voorwaardelijke toegang op basis van risico's](../conditional-access/howto-conditional-access-policy-risk.md) toe aan de Azure AD Premium P1-functies die zich aanpassen aan de patronen van de gebruiker en multi-factor authenticatieprompts minimaliseert.

## <a name="authentication-methods"></a>Verificatiemethoden

|   | Standaardinstellingen voor de beveiliging | Alle andere methoden |
| --- | --- | --- |
| Melding via mobiele app | X | X |
| Verificatiecode van mobiele app of hardwaretoken |   | X |
| Sms-bericht naar telefoon |   | X |
| Bellen naar de telefoon |   | X |

## <a name="next-steps"></a>Volgende stappen

Zie de zelfstudie om [aanmeldingsgebeurtenissen](../authentication/tutorial-enable-azure-mfa.md)voor gebruikers te beveiligen met Azure Multi-Factor Authentication om aan de slag te gaan.

Zie [Functies en licenties voor Azure Multi-Factor Authentication voor](../authentication/concept-mfa-licensing.md)meer informatie over licenties.
