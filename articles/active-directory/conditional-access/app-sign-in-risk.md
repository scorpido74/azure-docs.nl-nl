---
title: Risicodetecties gebruiken met voorwaardelijke toegang tot Azure Active Directory
description: In deze snelstart leert u hoe u een Azure Active Directory (Azure AD) conditional access-beleid configureren om aanmeldingen te blokkeren op basis van sessierisico's.
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
ms.openlocfilehash: be447b001b0b2e14999aac98ba2125f8cbfe9853
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "77186617"
---
# <a name="quickstart-block-access-when-a-session-risk-is-detected-with-azure-active-directory-conditional-access"></a>Snelstart: toegang blokkeren wanneer een sessierisico wordt gedetecteerd met voorwaardelijke toegang tot Azure Active Directory  

Als u uw omgeving beschermd wilt houden, u verdachte gebruikers blokkeren om zich aan te melden. [Azure Active Directory (Azure AD) Identiteitsbeveiliging](../active-directory-identityprotection.md) analyseert elke aanmelding en berekent de waarschijnlijkheid dat een aanmeldingspoging niet is uitgevoerd door de rechtmatige eigenaar van een gebruikersaccount. De waarschijnlijkheid (laag, gemiddeld, hoog) wordt aangegeven in de vorm van een berekende waarde die [aanmeldingsrisiconiveaus](concept-conditional-access-conditions.md#sign-in-risk)wordt genoemd . Door de aanmeldingsrisicovoorwaarde in te stellen, u een beleid voor voorwaardelijke toegang configureren om te reageren op specifieke aanmeldingsrisiconiveaus.

In deze snelstart ziet u hoe u een [beleid voor voorwaardelijke toegang](../active-directory-conditional-access-azure-portal.md) configureert dat een aanmelding blokkeert wanneer een geconfigureerd aanmeldingsrisiconiveau is gedetecteerd.

![Beleid maken](./media/app-sign-in-risk/1000.png)

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van het scenario in deze zelfstudie hebt u het volgende nodig:

- **Toegang tot een Azure AD Premium P2-editie** - Hoewel voorwaardelijke toegang een Azure AD Premium P1-mogelijkheid is, hebt u een P2-editie nodig omdat het scenario in deze quickstart identiteitsbescherming vereist.
- **Identiteitsbescherming** - Het scenario in deze quickstart vereist dat identiteitsbescherming wordt ingeschakeld. Zie [Azure Active Directory Identity Protection inschakelen](../identity-protection/overview-identity-protection.md)als u niet weet hoe u identiteitsbeveiliging inschakelen.
- **Tor Browser** - De [Tor Browser](https://www.torproject.org/projects/torbrowser.html.en) is ontworpen om u te helpen uw privacy online te behouden. Identiteitsbeveiliging detecteert een aanmelding vanuit een Tor-browser als aanmeldingen van anonieme IP-adressen, die een gemiddeld risiconiveau hebben. Zie [Azure Active Directory-risicodetecties](../reports-monitoring/concept-risk-events.md)voor meer informatie .  
- **Een testaccount genaamd Alain Charon** - Als u niet weet hoe u een testaccount moet maken, raadpleegt u [Cloudgebruikers toevoegen.](../fundamentals/add-users-azure-active-directory.md#add-a-new-user)

## <a name="test-your-sign-in"></a>Uw aanmelding testen

Het doel van deze stap is ervoor te zorgen dat uw testaccount toegang heeft tot uw tenant via de Tor-browser.

**Ga als bedoeld als het gaat om uw aanmelding te testen:**

1. Meld u aan bij uw [Azure-portal](https://portal.azure.com) als **Alain Charon**.
1. Meld u af.

## <a name="create-your-conditional-access-policy"></a>Uw beleid voor voorwaardelijke toegang maken

Het scenario in deze quickstart maakt gebruik van een aanmelding van een Tor-browser om een gedetecteerde **aanmelding van anonieme IP-adressen** risicodetectie te genereren. Het risiconiveau van deze risicodetectie is gemiddeld. Om op deze risicodetectie te reageren, stelt u de aanmeldingsrisicovoorwaarde in op medium. In een productieomgeving moet u de aanmeldingsrisicovoorwaarde instellen op hoog of gemiddeld en hoog.

In deze sectie ziet u hoe u het vereiste beleid voor voorwaardelijke toegang maakt. Stel in uw beleid het als:

| Instelling | Waarde |
| --- | --- |
| Gebruikers en groepen | Alain Charon  |
| Cloud-apps | Alle cloud-apps |
| Aanmeldingsrisico | Middelgroot |
| Verlenen | Toegang blokkeren |

![Beleid maken](./media/app-sign-in-risk/130.png)

**Ga als u uw beleid voor voorwaardelijke toegang configureert:**

1. Meld u aan bij uw [Azure-portal](https://portal.azure.com) als globale beheerder, beveiligingsbeheerder of beheerder van voorwaardelijke toegang.
1. Klik in de Azure-portal op de linkernavigatiebalk op **Azure Active Directory**.

   ![Azure Active Directory](./media/app-sign-in-risk/02.png)

1. Klik op de pagina **Azure Active Directory** in de sectie **Beveiliging** op **Voorwaardelijke toegang**.

   ![Voorwaardelijke toegang](./media/app-sign-in-risk/03.png)

1. Klik op de pagina **Voorwaardelijke toegang** op de werkbalk bovenaan op **Toevoegen**.

   ![Name](./media/app-sign-in-risk/108.png)

1. Typ Op de pagina **Nieuw** in het tekstvak **Naam** de **teksttoegang blokkeren voor een gemiddeld risiconiveau**.

   ![Name](./media/app-sign-in-risk/104.png)

1. Klik **in** de sectie Toewijzing op **Gebruikers en groepen**.

   ![Gebruikers en groepen](./media/app-sign-in-risk/06.png)

1. Op de pagina **Gebruikers en groepen:**

   ![Voorwaardelijke toegang](./media/app-sign-in-risk/107.png)

   1. Klik **op Gebruikers en groepen selecteren**en selecteer Gebruikers en **groepen**.
   1. Klik **op Selecteren**.
   1. Selecteer **alain Charon**op de pagina **Selecteren** en klik op **Selecteren**.
   1. Klik op de pagina **Gebruikers en groepen** op **Gereed**.
1. Klik op **Cloud-apps**.

   ![Cloud-apps](./media/app-sign-in-risk/08.png)

1. Op de pagina **Cloud-apps:**

   ![Voorwaardelijke toegang](./media/app-sign-in-risk/109.png)

   1. Klik op **Alle cloud-apps**.
   1. Klik op **Gereed**.
1. Klik **op Voorwaarden**.

   ![Besturingselementen voor toegang](./media/app-sign-in-risk/19.png)

1. Op de pagina **Voorwaarden:**

   ![Niveau van aanmeldingsrisico's](./media/app-sign-in-risk/21.png)

   1. Klik **op Aanmeldingsrisico**.
   1. Als **configureren**klikt u op **Ja.**
   1. Selecteer **Gemiddeld**als aanmeldingsrisiconiveau .
   1. Klik **op Selecteren**.
   1. Klik **op** de pagina Voorwaarden op **Gereed**.
1. Klik in de sectie **Toegangsbesturingselementen** op **Verlenen**.

   ![Besturingselementen voor toegang](./media/app-sign-in-risk/10.png)

1. Op de **grant** pagina:

   ![Voorwaardelijke toegang](./media/app-sign-in-risk/105.png)

   1. Selecteer **Bloktoegang**.
   1. Klik **op Selecteren**.
1. Klik **in** de sectie Beleid inschakelen op **Op .**

   ![Beleid inschakelen](./media/app-sign-in-risk/18.png)

1. Klik **op Maken**.

## <a name="evaluate-a-simulated-sign-in"></a>Een gesimuleerde aanmelding evalueren

Nu u uw beleid voor voorwaardelijke toegang hebt geconfigureerd, wilt u waarschijnlijk weten of het werkt zoals verwacht. Als eerste stap gebruikt u het beleid Voor voorwaardelijke toegang **als als het beleid** om een aanmelding van uw testgebruiker te simuleren. De simulatie schat de impact van deze aanmelding op uw beleid in en genereert een simulatierapport.  

Wanneer u het **beleidsinstrument wat als** voor dit scenario uitvoert, moet de **toegang tot het blokkeren voor een gemiddeld risiconiveau** worden weergegeven onder **Beleid dat van toepassing is.**

![Gebruiker](./media/app-sign-in-risk/117.png)

**Ga als een temeer doen met het beleid voor voorwaardelijke toegang:**

1. Klik op de pagina [Voorwaardelijke toegang - Beleid](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) in het menu bovenaan op Wat **als**.  

   ![What If](./media/app-sign-in-risk/14.png)

1. Klik **op Gebruiker,** selecteer **Alan Charon** op de pagina **Gebruikers** en klik vervolgens op **Selecteren**.

   ![Gebruiker](./media/app-sign-in-risk/116.png)

1. Selecteer **Gemiddeld**als **aanmeldingsrisico**.

   ![Gebruiker](./media/app-sign-in-risk/119.png)

1. Klik op **Wat als**.

## <a name="test-your-conditional-access-policy"></a>Uw beleid voor voorwaardelijke toegang testen

In de vorige sectie hebt u geleerd hoe u een gesimuleerde aanmelding evalueren. Naast een simulatie moet u ook uw beleid voor voorwaardelijke toegang testen om ervoor te zorgen dat het werkt zoals verwacht.

Als u uw beleid wilt testen, probeert u zich aan te melden bij uw [Azure-portal](https://portal.azure.com) als **Alan Charon** met behulp van de Tor-browser. Uw aanmeldingspoging moet worden geblokkeerd door uw beleid voor voorwaardelijke toegang.

![Multi-Factor Authentication](./media/app-sign-in-risk/118.png)

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de testgebruiker, de Tor-browser en het beleid Voorwaardelijke toegang wanneer dit niet meer nodig is:

- Zie [Gebruikers verwijderen uit Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user)als u niet weet hoe u een Azure AD-gebruiker verwijderen.
- Als u uw beleid wilt verwijderen, selecteert u uw beleid en klikt u op **Verwijderen** op de werkbalk Snelle toegang.

   ![Multi-Factor Authentication](./media/app-sign-in-risk/33.png)

- Zie [Verwijderen voor](https://tb-manual.torproject.org/uninstalling/)instructies voor het verwijderen van de Tor-browser .

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Gebruiksvoorwaarden vereisen om geaccepteerd te worden](require-tou.md)
> [Vereisen MFA voor specifieke apps](app-based-mfa.md)
