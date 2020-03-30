---
title: Voorwaardelijke toegang - Voorwaardelijke toegang op basis van risico 's - Azure Active Directory
description: Beleid voor voorwaardelijke toegang maken om verbeteringen in identiteitsbescherming in te schakelen voor beleid
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
ms.openlocfilehash: 8054d8985596095db32d9262322d7fb0f4aab8c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295156"
---
# <a name="conditional-access-risk-based-conditional-access"></a>Voorwaardelijke toegang: voorwaardelijke toegang op basis van risico's

Organisaties met Azure AD Premium P2-licenties kunnen beleid voor voorwaardelijke toegang maken met risicodetecties van Azure AD-identiteitsbeveiliging. Er zijn drie standaardbeleidsregels die uit het vak kunnen worden ingeschakeld. 

* Alle gebruikers moeten zich registreren voor Azure Multi-Factor Authentication.
* Een wachtwoordwijziging vereisen voor gebruikers met een hoog risico.
* Vereist multi-factor authenticatie voor gebruikers met een gemiddeld of hoog aanmeldingsrisico.

## <a name="require-all-users-to-register-for-azure-multi-factor-authentication"></a>Alle gebruikers verplichten zich te registreren voor Azure Multi-Factor Authentication

Als u dit beleid inschakelt, moeten alle gebruikers zich binnen 14 dagen registreren voor Azure Multi-Factor Authentication. 

1. Meld u aan bij **Azure Portal**.
1. Klik op **Alle services** en blader vervolgens naar **Azure AD Identity Protection**.
1. Klik op **Registratie bij MFA**.
1. Selecteer **Gebruikers**onder **Toewijzingen**.
   1. Selecteer Alle **gebruikers** **onder Opnemen**.
   1. Selecteer **onder Uitsluiten**uitsluiten uitgesloten **gebruikers**selecteren, kies de noodtoegangs- of breakglas-accounts van uw organisatie en selecteer **Selecteren**. 
   1. Selecteer **Done**.
1. **Beleid afdwingen** instellen op **Op**.
1. Klik op **Opslaan**.

## <a name="require-a-password-change-high-risk-users"></a>Een wachtwoordwijziging vereisen voor gebruikers met een hoog risico

Microsoft werkt samen met onderzoekers, justitie en politie, diverse beveiligingsteams bij Microsoft en andere betrouwbare bronnen om naar paren van gebruikersnamen en wachtwoorden te zoeken. Wanneer een van deze paren overeenkomt met een account in uw omgeving, kan een op risicogebeurtenissen gebaseerde wachtwoordwijziging worden geactiveerd met behulp van het volgende beleid.

1. Meld u aan bij **Azure Portal**.
1. Klik op **Alle services** en blader vervolgens naar **Azure AD Identity Protection**.
1. Klik op **Gebruikersrisicobeleid**.
1. Selecteer **Gebruikers** onder **Toewijzingen**
   1. Selecteer Alle **gebruikers** **onder Opnemen**.
   1. Selecteer **onder Uitsluiten**uitsluiten uitgesloten **gebruikers**selecteren, kies de noodtoegangs- of breakglas-accounts van uw organisatie en selecteer **Selecteren**.
   1. Selecteer **Done**.
1. Selecteer **onder Voorwaarden** **gebruikersrisico**en kies **Hoog**.
   1. Klik **op Selecteren** en vervolgens **gereed**.
1. Kies **onder Toegang voor besturingselementen** > **Access**de optie **Toegang toestaan**en selecteer **Wachtwoordwijzigen vereisen**.
   1. Klik **op Selecteren**.
1. **Beleid afdwingen** instellen op **Op**.
1. Klik op **Opslaan**.

## <a name="require-mfa-medium-or-high-sign-in-risk-users"></a>MFA-gebruikers met een gemiddeld of hoog inlogrisico vereisen

De meeste gebruikers vertonen normaal gedrag dat kan worden getraceerd. Wanneer ze buiten de norm hiervoor vallen, zou het riskant kunnen zijn om hen toe te staan zich zomaar aan te melden. U die gebruiker blokkeren of misschien gewoon vragen om multi-factor authenticatie uit te voeren om te bewijzen dat ze echt zijn wie ze zeggen dat ze zijn. Als u een beleid wilt inschakelen waarbij MFA is vereist als er een risicovolle aanmelding wordt gedetecteerd, schakelt u het volgende beleid in.

1. Meld u aan bij **Azure Portal**.
1. Klik op **Alle services** en blader vervolgens naar **Azure AD Identity Protection**.
1. Klik op **Aanmeldingsrisicobeleid**
1. Selecteer **Gebruikers** onder **Toewijzingen**
   1. Selecteer Alle **gebruikers** **onder Opnemen**.
   1. Selecteer **onder Uitsluiten**uitsluiten uitgesloten **gebruikers**selecteren, kies de noodtoegangs- of breakglas-accounts van uw organisatie en selecteer **Selecteren**.
   1. Selecteer **Done**.
1. Selecteer **Onder Voorwaarden** **Aanmeldingsrisico**en kies vervolgens **Gemiddeld en hoger**.
   1. Klik **op Selecteren** en vervolgens **gereed**.
1. Kies **onder Toegang voor besturingselementen** > **Access**de optie **Toegang toestaan**en selecteer **Multi-factor authenticatie vereisen**.
   1. Klik **op Selecteren**.
1. **Beleid afdwingen** instellen op **Op**.
1. Klik op **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

[Gemeenschappelijk beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Impact bepalen met de modus Alleen voor rapportvoorwaardelijke toegang](howto-conditional-access-report-only.md)

[Aanmeldingsgedrag simuleren met het gereedschap Welke als voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)

[Hoe werkt het - Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)

[Wat is Azure Active Directory Identity Protection](../identity-protection/overview.md)
