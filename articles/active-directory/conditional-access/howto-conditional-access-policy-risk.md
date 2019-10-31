---
title: Voorwaardelijke toegang-op Risico's gebaseerde voorwaardelijke toegang-Azure Active Directory
description: Beleid voor voorwaardelijke toegang maken om verbeteringen van de identiteits beveiliging van beleid in te scha kelen
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 102371f8af45a1a51715dbfb11afc3f0f4e457d7
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73150701"
---
# <a name="conditional-access-risk-based-conditional-access"></a>Voorwaardelijke toegang: voorwaardelijke toegang op basis van een risico

Organisaties met Azure AD Premium P2-licenties kunnen beleids regels voor voorwaardelijke toegang maken met Azure AD Identity Protection risico detecties. Er zijn drie standaard beleidsregels die kunnen worden ingeschakeld. 

* Vereisen dat alle gebruikers zich registreren voor Azure Multi-Factor Authentication.
* Een wachtwoord wijziging vereisen voor gebruikers met een hoog risico.
* Multi-factor Authentication vereisen voor gebruikers met gemiddeld of hoog aanmeldings risico.

## <a name="require-all-users-to-register-for-azure-multi-factor-authentication"></a>Vereisen dat alle gebruikers zich registreren voor Azure Multi-Factor Authentication

Als u dit beleid inschakelt, moeten alle gebruikers binnen 14 dagen worden geregistreerd voor Azure Multi-Factor Authentication. 

1. Meld u aan bij de **Azure-portal**.
1. Klik op **Alle services** en blader vervolgens naar **Azure AD Identity Protection**.
1. Klik op **Registratie bij MFA**.
1. Onder **toewijzingen**selecteert u **gebruikers**.
   1. Onder **insluiten**selecteert u **alle gebruikers**.
   1. Onder **uitsluiten**selecteert u **uitgesloten gebruikers selecteren**, kiest u de accounts voor nood toegang of het account van uw organisatie en selecteert u **selecteren**. 
   1. Selecteer **Done**.
1. Stel **beleid afdwingen** in **op aan**.
1. Klik op **Opslaan**.

## <a name="require-a-password-change-high-risk-users"></a>Vereisen dat gebruikers met een hoog risico hun wacht woord wijzigen

Microsoft werkt samen met onderzoekers, justitie en politie, diverse beveiligingsteams bij Microsoft en andere betrouwbare bronnen om naar paren van gebruikersnamen en wachtwoorden te zoeken. Wanneer een van deze paren overeenkomt met een account in uw omgeving, kan een op risicogebeurtenissen gebaseerde wachtwoordwijziging worden geactiveerd met behulp van het volgende beleid.

1. Meld u aan bij de **Azure-portal**.
1. Klik op **Alle services** en blader vervolgens naar **Azure AD Identity Protection**.
1. Klik op **beleid voor gebruikers Risico's**.
1. Onder **toewijzingen**selecteert u **gebruikers**
   1. Onder **insluiten**selecteert u **alle gebruikers**.
   1. Onder **uitsluiten**selecteert u **uitgesloten gebruikers selecteren**, kiest u de accounts voor nood toegang of het account van uw organisatie en selecteert u **selecteren**.
   1. Selecteer **Done**.
1. Selecteer onder **voor waarden** **gebruikers risico**en kies **hoog**.
   1. Klik op **selecteren** en vervolgens op **gereed**.
1. Kies onder **controls** > **Access**de optie **toegang toestaan**, en selecteer vervolgens **wachtwoord wijziging vereisen**.
   1. Klik op **Selecteren**.
1. Stel **beleid afdwingen** in **op aan**.
1. Klik op **Opslaan**.

## <a name="require-mfa-medium-or-high-sign-in-risk-users"></a>Gebruikers met een gemiddeld MFA of een hoog aanmeld risico vereisen

De meeste gebruikers vertonen normaal gedrag dat kan worden getraceerd. Wanneer ze buiten de norm hiervoor vallen, zou het riskant kunnen zijn om hen toe te staan zich zomaar aan te melden. Mogelijk wilt u die gebruiker blok keren of kan hij of zij vragen om multi-factor Authentication uit te voeren om te bewijzen dat ze in werkelijkheid zijn. Als u een beleid wilt inschakelen waarbij MFA is vereist als er een risicovolle aanmelding wordt gedetecteerd, schakelt u het volgende beleid in.

1. Meld u aan bij de **Azure-portal**.
1. Klik op **Alle services** en blader vervolgens naar **Azure AD Identity Protection**.
1. Klik op **beleid voor aanmeldings Risico's**
1. Onder **toewijzingen**selecteert u **gebruikers**
   1. Onder **insluiten**selecteert u **alle gebruikers**.
   1. Onder **uitsluiten**selecteert u **uitgesloten gebruikers selecteren**, kiest u de accounts voor nood toegang of het account van uw organisatie en selecteert u **selecteren**.
   1. Selecteer **Done**.
1. Selecteer onder **voor waarden** **aanmeldings risico**en kies vervolgens **medium en hoger**.
   1. Klik op **selecteren** en vervolgens op **gereed**.
1. Kies onder **controls** > **Access**de optie **toegang toestaan**en selecteer vervolgens **multi-factor Authentication vereisen**.
   1. Klik op **Selecteren**.
1. Stel **beleid afdwingen** in **op aan**.
1. Klik op **Opslaan**.

## <a name="next-steps"></a>Volgende stappen

[Algemeen beleid voor voorwaardelijke toegang](concept-conditional-access-policy-common.md)

[Aanmeld gedrag simuleren met het What If hulp programma voor voorwaardelijke toegang](troubleshoot-conditional-access-what-if.md)

[Hoe werkt het - Azure Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md)

[Wat is Azure Active Directory Identity Protection?](../identity-protection/overview.md)
