---
title: Risico detecties gebruiken met voorwaardelijke toegang Azure Active Directory
description: In deze Quick Start leert u hoe u een beleid voor voorwaardelijke toegang van Azure Active Directory (Azure AD) kunt configureren om aanmeldingen te blok keren op basis van sessie Risico's.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: quickstart
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9338c1a42737180ec5395f30060b4eed35ce5eda
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381075"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-conditional-access"></a>Quick Start: toegang blok keren wanneer er een sessie risico wordt gedetecteerd met Azure Active Directory voorwaardelijke toegang  

Als u uw omgeving wilt beveiligen, wilt u mogelijk verdachte gebruikers blok keren om zich aan te melden. [Azure Active Directory (Azure AD) Identity Protection](../active-directory-identityprotection.md) analyseert elke aanmelding en berekent de kans dat een aanmeldings poging niet is uitgevoerd door de rechtmatige eigenaar van een gebruikers account. De kans (laag, gemiddeld, hoog) wordt aangegeven in de vorm van een berekende waarde met de naam [aanmeldings risico niveaus](conditions.md#sign-in-risk). Door de aanmeldings risico voorwaarde in te stellen, kunt u een beleid voor voorwaardelijke toegang configureren om te reageren op specifieke risico niveaus.

In deze Quick start ziet u hoe u een [beleid voor voorwaardelijke toegang](../active-directory-conditional-access-azure-portal.md) kunt configureren dat een aanmelding blokkeert wanneer een geconfigureerd risico niveau voor aanmelden is gedetecteerd.

![Beleid maken](./media/app-sign-in-risk/1000.png)

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van het scenario in deze zelfstudie hebt u het volgende nodig:

- **Toegang tot een Azure AD Premium P2-editie** : Hoewel voorwaardelijke toegang een Azure AD Premium P1-mogelijkheid is, hebt u een P2-editie nodig, omdat het scenario in deze Snelstartgids identiteits beveiliging vereist.
- **Identiteits beveiliging** : voor het scenario in deze Snelstartgids moet identiteits beveiliging zijn ingeschakeld. Als u niet weet hoe u identiteits beveiliging kunt inschakelen, raadpleegt u [Azure Active Directory Identity Protection](../identity-protection/overview-identity-protection.md)in te scha kelen.
- **Tor** -browser: de [Tor-Browser](https://www.torproject.org/projects/torbrowser.html.en) is ontworpen om u te helpen uw privacy online te bewaren. Identiteits beveiliging detecteert een aanmelding vanuit een Tor-browser als aanmeldingen vanaf anonieme IP-adressen, die een gemiddeld risico niveau hebben. Zie [Azure Active Directory-risico detectie](../reports-monitoring/concept-risk-events.md)voor meer informatie.  
- **Een test account met de naam Alain Charon** : als u niet weet hoe u een test account moet maken, raadpleegt u [Cloud gebruikers toevoegen](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Uw aanmelding testen

Het doel van deze stap is om ervoor te zorgen dat uw test account toegang heeft tot uw Tenant met behulp van de Tor-browser.

**Uw aanmelding testen:**

1. Meld u aan bij uw [Azure Portal](https://portal.azure.com) als **Alain Charon**.
1. Meld u af.

## <a name="create-your-conditional-access-policy"></a>Het beleid voor voorwaardelijke toegang maken

In het scenario in deze Snelstartgids wordt een aanmelding van een Tor-browser gebruikt om een gedetecteerde **aanmeldingen te genereren op basis van een risico detectie voor anonieme IP-adressen** . Het risico niveau van deze risico detectie is gemiddeld. Als u wilt reageren op deze risico detectie, stelt u de risico voorwaarde voor aanmelden in op gemiddeld. In een productie omgeving moet u de risico voorwaarde voor het aanmelden instellen op hoog of op gemiddeld en hoog.

In deze sectie wordt beschreven hoe u het vereiste beleid voor voorwaardelijke toegang maakt. Stel in uw beleid het volgende in:

| Instelling | Waarde |
| --- | --- |
| Gebruikers en groepen | Alain Charon  |
| Cloud-apps | Alle Cloud-apps |
| Aanmeldings risico | Middelgroot |
| Geef | Toegang blok keren |

![Beleid maken](./media/app-sign-in-risk/130.png)

**Het beleid voor voorwaardelijke toegang configureren:**

1. Meld u aan bij uw [Azure Portal](https://portal.azure.com) als globale beheerder, beveiligings beheerder of een beheerder voor voorwaardelijke toegang.
1. Klik in het Azure Portal op de linkernavigatiebalk op **Azure Active Directory**.

   ![Azure Active Directory](./media/app-sign-in-risk/02.png)

1. Klik op de pagina **Azure Active Directory** in de sectie **beveiliging** op **voorwaardelijke toegang**.

   ![Voorwaardelijke toegang](./media/app-sign-in-risk/03.png)

1. Klik op de pagina **voorwaardelijke toegang** in de werk balk aan de bovenkant op **toevoegen**.

   ![Naam](./media/app-sign-in-risk/108.png)

1. Typ op de pagina **Nieuw** , in het tekstvak **naam** , **toegang blok keren voor gemiddeld risico niveau**.

   ![Naam](./media/app-sign-in-risk/104.png)

1. Klik in de sectie **toewijzing** op **gebruikers en groepen**.

   ![Gebruikers en groepen](./media/app-sign-in-risk/06.png)

1. Op de pagina **gebruikers en groepen** :

   ![Voorwaardelijke toegang](./media/app-sign-in-risk/107.png)

   1. Klik op **gebruikers en groepen selecteren**en selecteer vervolgens **gebruikers en groepen**.
   1. Klik op **Selecteren**.
   1. Selecteer op de pagina **selecteren** de optie **Alain Charon**en klik vervolgens op **selecteren**.
   1. Klik op de pagina **gebruikers en groepen** op **gereed**.
1. Klik op **Cloud-apps**.

   ![Cloud-apps](./media/app-sign-in-risk/08.png)

1. Op de pagina **Cloud-apps** :

   ![Voorwaardelijke toegang](./media/app-sign-in-risk/109.png)

   1. Klik op **alle Cloud-apps**.
   1. Klik op **Gereed**.
1. Klik op **voor waarden**.

   ![Toegangs beheer](./media/app-sign-in-risk/19.png)

1. Op de pagina **voor waarden** :

   ![Risico niveau voor aanmelden](./media/app-sign-in-risk/21.png)

   1. Klik op **aanmeldings risico**.
   1. Als **configureren**, klikt u op **Ja**.
   1. Selecteer **gemiddeld**als aanmeldings risico niveau.
   1. Klik op **Selecteren**.
   1. Klik op **gereed**op de pagina **voor waarden** .
1. Klik in de sectie **toegangs beheer** op **verlenen**.

   ![Toegangs beheer](./media/app-sign-in-risk/10.png)

1. Op de pagina **Grant** :

   ![Voorwaardelijke toegang](./media/app-sign-in-risk/105.png)

   1. Selecteer **toegang blok keren**.
   1. Klik op **Selecteren**.
1. Klik in de sectie **beleid inschakelen** op **aan**.

   ![Beleid inschakelen](./media/app-sign-in-risk/18.png)

1. Klik op **Create**.

## <a name="evaluate-a-simulated-sign-in"></a>Een gesimuleerde aanmelding evalueren

Nu u het beleid voor voorwaardelijke toegang hebt geconfigureerd, wilt u waarschijnlijk weten of het werkt zoals verwacht. Als eerste stap gebruikt u de functie voor voorwaardelijke toegang **Wat als-beleid** voor het simuleren van een aanmelding van uw test gebruiker. De simulatie schat de impact van deze aanmelding op uw beleid in en genereert een simulatierapport.  

Wanneer u het **hulp programma What if-beleid** uitvoert voor dit scenario, moet het **niveau toegang blok keren voor gemiddeld risico** worden vermeld onder het **beleid dat van toepassing is**.

![Gebruiker](./media/app-sign-in-risk/117.png)

**Het beleid voor voorwaardelijke toegang evalueren:**

1. Klik op de pagina [voorwaardelijke toegang-beleids regels](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) in het menu aan de bovenkant op **What if**.  

   ![What If](./media/app-sign-in-risk/14.png)

1. Klik op **gebruiker**, selecteer **Alan Charon** op de pagina **gebruikers** en klik vervolgens op **selecteren**.

   ![Gebruiker](./media/app-sign-in-risk/116.png)

1. Selecteer **gemiddeld**als **aanmeldings risico**.

   ![Gebruiker](./media/app-sign-in-risk/119.png)

1. Klik op **What if**.

## <a name="test-your-conditional-access-policy"></a>Het beleid voor voorwaardelijke toegang testen

In de vorige sectie hebt u geleerd hoe u een gesimuleerde aanmelding kunt evalueren. Naast een simulatie moet u ook het beleid voor voorwaardelijke toegang testen om er zeker van te zijn dat het werkt zoals verwacht.

Als u uw beleid wilt testen, probeert u zich aan te melden bij uw [Azure Portal](https://portal.azure.com) als **Alan Charon** met behulp van de Tor-browser. Uw aanmeldings poging moet worden geblokkeerd door het beleid voor voorwaardelijke toegang.

![Multi-Factor Authentication](./media/app-sign-in-risk/118.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, verwijdert u de test gebruiker, de Tor-browser en het beleid voor voorwaardelijke toegang:

- Zie [gebruikers uit Azure ad verwijderen](../fundamentals/add-users-azure-active-directory.md#delete-a-user)als u niet weet hoe u een Azure AD-gebruiker verwijdert.
- Als u uw beleid wilt verwijderen, selecteert u uw beleid en klikt u vervolgens op **verwijderen** op de werk balk snelle toegang.

   ![Multi-Factor Authentication](./media/app-sign-in-risk/33.png)

- Zie [installatie ongedaan maken](https://tb-manual.torproject.org/uninstalling/)voor instructies voor het verwijderen van de Tor-browser.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Vereisen dat gebruiks voorwaarden worden geaccepteerd](require-tou.md)
> [MFA vereisen voor specifieke apps](app-based-mfa.md)
