---
title: Voorwaardelijke toegang vereist gebruiksvoorwaarden - Azure Active Directory
description: In deze quickstart leert u hoe u eisen dat uw gebruiksvoorwaarden worden geaccepteerd voordat toegang tot geselecteerde cloud-apps wordt verleend door voorwaardelijke toegang van Azure Active Directory.
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
ms.openlocfilehash: 3dd1b4cf554e773f49a15ac5cedcbcc5b3e710b9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74380104"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>Snelstart: vereisen dat gebruiksvoorwaarden worden geaccepteerd voordat u toegang krijgt tot cloud-apps

Voordat u toegang krijgt tot bepaalde cloud-apps in uw omgeving, wilt u mogelijk toestemming krijgen van gebruikers in de vorm van het accepteren van uw gebruiksvoorwaarden (ToU). Voorwaardelijke toegang voor Azure Active Directory (Azure AD) biedt u:

- Een eenvoudige methode om ToU te configureren
- De optie om uw gebruiksvoorwaarden te accepteren via een beleid voor voorwaardelijke toegang  

In deze snelstart ziet u hoe u een [Azure AD Conditional Access-beleid](../active-directory-conditional-access-azure-portal.md) configureert waarvoor een ToU moet worden geaccepteerd voor een geselecteerde cloud-app in uw omgeving.

![Beleid maken](./media/require-tou/5555.png)

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u het scenario in deze snelle start wilt voltooien, moet u het:

- **Toegang tot een Azure AD Premium-editie** - Azure AD Conditional Access is een Azure AD Premium-mogelijkheid.
- **Een testaccount genaamd Isabella Simonsen** - Als u niet weet hoe u een testaccount moet maken, raadpleegt u [Cloudgebruikers toevoegen.](../fundamentals/add-users-azure-active-directory.md#add-a-new-user)

## <a name="test-your-sign-in"></a>Uw aanmelding testen

Het doel van deze stap is om een indruk te krijgen van de aanmeldingservaring zonder een beleid voor voorwaardelijke toegang.

**Ga als bedoeld als het gaat om uw aanmelding te testen:**

1. Meld u aan bij uw [Azure-portal](https://portal.azure.com/) als Isabella Simonsen.
1. Meld u af.

## <a name="create-your-terms-of-use"></a>Maak uw gebruiksvoorwaarden

In deze sectie vindt u de stappen om een voorbeeld-tou-to-u te maken. Wanneer u een ToU maakt, selecteert u een waarde voor **Afdwingen met beleidssjablonen voor voorwaardelijke toegang**. Als u **Aangepast beleid selecteert,** wordt het dialoogvenster geopend om een nieuw beleid voor voorwaardelijke toegang te maken zodra uw ToU is gemaakt.

**Ga als het gaat om het maken van uw gebruiksvoorwaarden:**

1. Maak in Microsoft Word een nieuw document.
1. Typ **Mijn gebruiksvoorwaarden**en sla het document op uw computer op als **mytou.pdf**.
1. Meld u aan bij uw [Azure-portal](https://portal.azure.com) als globale beheerder, beveiligingsbeheerder of beheerder van voorwaardelijke toegang.
1. Klik in de Azure-portal op de linkernavigatiebalk op **Azure Active Directory**.

   ![Azure Active Directory](./media/require-tou/02.png)

1. Klik op de pagina **Azure Active Directory** in de sectie **Beveiliging** op **Voorwaardelijke toegang**.

   ![Voorwaardelijke toegang](./media/require-tou/03.png)

1. Klik **in** de sectie Beheren op **Gebruiksvoorwaarden**.

   ![Gebruiksvoorwaarden](./media/require-tou/04.png)

1. Klik in het menu bovenaan op **Nieuwe termen**.

   ![Gebruiksvoorwaarden](./media/require-tou/05.png)

1. Ga als een nieuwe **gebruiksvoorwaardenpagina** te werk:

   ![Gebruiksvoorwaarden](./media/require-tou/112.png)

   1. Typ **Mijn TOU in**het tekstvak **Naam** .
   1. Typ **Mijn TOU**in het tekstvak **Weergavenaam** .
   1. Upload uw gebruiksvoorwaarden PDF-bestand.
   1. Selecteer **Engels**als **taal**.
   1. Als **gebruikers vereisen om de gebruiksvoorwaarden uit te breiden,** selecteert u **Op**.
   1. Selecteer **Aangepast beleid**als **Afdwingen met beleidssjablonen voor voorwaardelijke toegang**.
   1. Klik **op Maken**.

## <a name="create-your-conditional-access-policy"></a>Uw beleid voor voorwaardelijke toegang maken

In deze sectie ziet u hoe u het vereiste beleid voor voorwaardelijke toegang maakt. Het scenario in deze quickstart gebruikt:

- De Azure-portal als tijdelijke aanduiding voor een cloud-app waarvoor uw ToU moet worden geaccepteerd. 
- Uw voorbeeldgebruiker om het beleid voor voorwaardelijke toegang te testen.  

Stel in uw beleid het als:

| Instelling | Waarde |
| --- | --- |
| Gebruikers en groepen | Isabella Simonsen |
| Cloud-apps | Microsoft Azure-beheer |
| Toegang verlenen | Mijn TOU |

![Beleid maken](./media/require-tou/1234.png)

**Ga als u uw beleid voor voorwaardelijke toegang configureert:**

1. Typ **TOU vereisen voor Isabella**op de pagina **Nieuw** in het tekstvak **Naam** .

   ![Name](./media/require-tou/71.png)

1. Klik **in** de sectie Toewijzing op **Gebruikers en groepen**.

   ![Gebruikers en groepen](./media/require-tou/06.png)

1. Op de pagina **Gebruikers en groepen:**

   ![Gebruikers en groepen](./media/require-tou/24.png)

   1. Klik **op Gebruikers en groepen selecteren**en selecteer Gebruikers en **groepen**.
   1. Klik **op Selecteren**.
   1. Selecteer **Isabella** **Simonsen**op de pagina Selecteren en klik op **Selecteren**.
   1. Klik op de pagina **Gebruikers en groepen** op **Gereed**.
1. Klik op **Cloud-apps**.

   ![Cloud-apps](./media/require-tou/08.png)

1. Op de pagina **Cloud-apps:**

   ![Cloud-apps selecteren](./media/require-tou/26.png)

   1. Klik **op Apps selecteren**.
   1. Klik **op Selecteren**.
   1. Selecteer op de pagina **Selecteren** de optie **Microsoft Azure Management**en klik op **Selecteren**.
   1. Klik op de pagina **Cloud-apps** op **Gereed**.
1. Klik in de sectie **Toegangsbesturingselementen** op **Verlenen**.

   ![Besturingselementen voor toegang](./media/require-tou/10.png)

1. Op de **grant** pagina:

   ![Verlenen](./media/require-tou/111.png)

   1. Selecteer **Toegang verlenen**.
   1. Selecteer **Mijn TOU**.
   1. Klik **op Selecteren**.
1. Klik **in** de sectie Beleid inschakelen op **Op .**

   ![Beleid inschakelen](./media/require-tou/18.png)

1. Klik **op Maken**.

## <a name="evaluate-a-simulated-sign-in"></a>Een gesimuleerde aanmelding evalueren

Nu u uw beleid voor voorwaardelijke toegang hebt geconfigureerd, wilt u waarschijnlijk weten of het werkt zoals verwacht. Als eerste stap gebruikt u het beleid Voor voorwaardelijke toegang als als het beleid om een aanmelding van uw testgebruiker te simuleren. De simulatie schat de impact van deze aanmelding op uw beleid in en genereert een simulatierapport.  

Als u het hulpprogramma **voor beleidsevaluatie wilt** initialiseren, stelt u het belangrijkste in:

- **Isabella Simonsen** als gebruiker
- **Microsoft Azure Management** als cloud-app

Als u op **Wat als klikt,** wordt een simulatierapport gemaakt dat wordt weergegeven:

- **TOU vereisen voor Isabella** onder **beleid dat van toepassing is**
- **Mijn TOU** als **Grant Controls**.

![Wat als beleidstool](./media/require-tou/79.png)

**Ga als een temeer doen met het beleid voor voorwaardelijke toegang:**

1. Klik op de pagina [Voorwaardelijke toegang - Beleid](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) in het menu bovenaan op Wat **als**.  

   ![What If](./media/require-tou/14.png)

1. Klik **op Gebruikers,** selecteer **Isabella Simonsen**en klik vervolgens op **Selecteren**.

   ![Gebruiker](./media/require-tou/15.png)

1. Ga als lid van het opgaan van een cloud-app:

   ![Cloud-apps](./media/require-tou/16.png)

   1. Klik op **Cloud-apps**.
   1. Klik op de **pagina Cloud-apps**op **Apps selecteren**.
   1. Klik **op Selecteren**.
   1. Selecteer op de pagina **Selecteren** de optie **Microsoft Azure Management**en klik op **Selecteren**.
   1. Klik op de pagina cloud-apps op **Gereed**.
1. Klik op **Wat als**.

## <a name="test-your-conditional-access-policy"></a>Uw beleid voor voorwaardelijke toegang testen

In de vorige sectie hebt u geleerd hoe u een gesimuleerde aanmelding evalueren. Naast een simulatie moet u ook uw beleid voor voorwaardelijke toegang testen om ervoor te zorgen dat het werkt zoals verwacht.

Als u uw beleid wilt testen, probeert u zich aan te melden bij uw [Azure-portal](https://portal.azure.com) met uw **Isabella Simonsen-testaccount.** U ziet een dialoogvenster waarin u uw gebruiksvoorwaarden moet accepteren.

![Gebruiksvoorwaarden](./media/require-tou/57.png)

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de testgebruiker en het beleid Voorwaardelijke toegang wanneer dit niet meer nodig is:

- Zie [Gebruikers verwijderen uit Azure AD](../fundamentals/add-users-azure-active-directory.md#delete-a-user)als u niet weet hoe u een Azure AD-gebruiker verwijderen.
- Als u uw beleid wilt verwijderen, selecteert u uw beleid en klikt u op **Verwijderen** op de werkbalk Snelle toegang.

    ![Multi-Factor Authentication](./media/require-tou/33.png)

- Als u uw gebruiksvoorwaarden wilt verwijderen, selecteert u deze en klikt u op **Termen verwijderen** op de werkbalk bovenaan.

    ![Multi-Factor Authentication](./media/require-tou/29.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [MFA vereisen voor specifieke apps](app-based-mfa.md)
> [Toegang blokkeren wanneer een sessierisico wordt gedetecteerd](app-sign-in-risk.md)
