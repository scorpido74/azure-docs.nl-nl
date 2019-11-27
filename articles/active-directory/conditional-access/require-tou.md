---
title: Voor de voorwaardelijke toegang zijn de gebruiks voorwaarden-Azure Active Directory vereist
description: In deze Quick Start leert u hoe u kunt vereisen dat uw gebruiks voorwaarden worden geaccepteerd voordat toegang tot geselecteerde Cloud-apps wordt verleend door Azure Active Directory voorwaardelijke toegang.
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
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74380104"
---
# <a name="quickstart-require-terms-of-use-to-be-accepted-before-accessing-cloud-apps"></a>Quick Start: vereisen dat gebruiks voorwaarden worden geaccepteerd voor toegang tot Cloud-apps

Voordat u bepaalde Cloud-apps in uw omgeving kunt openen, kunt u toestemming geven voor gebruikers in de vorm van het accepteren van uw gebruiksrecht overeenkomst. Azure Active Directory (Azure AD) voorwaardelijke toegang biedt u het volgende:

- Een eenvoudige methode voor het configureren van gebruiks voorwaarden
- De optie voor het accepteren van uw gebruiks voorwaarden via een beleid voor voorwaardelijke toegang  

In deze Quick start ziet u hoe u een [beleid voor voorwaardelijke toegang voor Azure AD](../active-directory-conditional-access-azure-portal.md) configureert waarvoor een gebruiks voorwaarden moeten worden geaccepteerd voor een geselecteerde Cloud-app in uw omgeving.

![Beleid maken](./media/require-tou/5555.png)

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van het scenario in deze Quick Start hebt u het volgende nodig:

- **Toegang tot een Azure AD Premium Edition** : voorwaardelijke toegang voor Azure AD is een Azure AD Premium mogelijkheid.
- **Een test account met de naam Isabella Simonsen** : als u niet weet hoe u een test account moet maken, raadpleegt u [Cloud gebruikers toevoegen](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

## <a name="test-your-sign-in"></a>Uw aanmelding testen

Het doel van deze stap is om een indruk te krijgen van de aanmeldings ervaring zonder beleid voor voorwaardelijke toegang.

**Uw aanmelding testen:**

1. Meld u aan bij uw [Azure Portal](https://portal.azure.com/) als Isabella Simonsen.
1. Meld u af.

## <a name="create-your-terms-of-use"></a>Uw gebruiks voorwaarden maken

In deze sectie vindt u de stappen voor het maken van een voor beeld van gebruiks voorwaarden. Wanneer u een gebruiks voorwaarden maakt, selecteert u een waarde voor **afdwingen met beleids sjablonen voor voorwaardelijke toegang**. Als u **aangepast beleid** selecteert, wordt het dialoog venster geopend om een nieuw beleid voor voorwaardelijke toegang te maken zodra uw gebruiks voorwaarden zijn gemaakt.

**U kunt als volgt uw gebruiks voorwaarden maken:**

1. Maak een nieuw document in micro soft Word.
1. Typ **mijn gebruiks voorwaarden**en sla het document op uw computer op als **mytou. PDF**.
1. Meld u aan bij uw [Azure Portal](https://portal.azure.com) als globale beheerder, beveiligings beheerder of een beheerder voor voorwaardelijke toegang.
1. Klik in het Azure Portal op de linkernavigatiebalk op **Azure Active Directory**.

   ![Azure Active Directory](./media/require-tou/02.png)

1. Klik op de pagina **Azure Active Directory** in de sectie **beveiliging** op **voorwaardelijke toegang**.

   ![Voorwaardelijke toegang](./media/require-tou/03.png)

1. Klik in de sectie **beheren** op **Gebruiksvoorwaarden**.

   ![Gebruiksvoorwaarden](./media/require-tou/04.png)

1. Klik in het menu aan de bovenkant op **nieuwe voor waarden**.

   ![Gebruiksvoorwaarden](./media/require-tou/05.png)

1. Op de pagina **nieuwe gebruiks voorwaarden** :

   ![Gebruiksvoorwaarden](./media/require-tou/112.png)

   1. Typ **mijn gebruiks voorwaarden**in het tekstvak **naam** .
   1. In het tekstvak **weergave naam** typt u **mijn gebruiks voorwaarden**.
   1. Upload uw voor waarden van het PDF-bestand voor gebruik.
   1. Als **taal**, selecteer **Engels**.
   1. Selecteer **aan**als **gebruikers de gebruiks voorwaarden moeten uitbreiden**.
   1. Als **afdwingen met beleids sjablonen voor voorwaardelijke toegang**, selecteert u **aangepast beleid**.
   1. Klik op **Create**.

## <a name="create-your-conditional-access-policy"></a>Het beleid voor voorwaardelijke toegang maken

In deze sectie wordt beschreven hoe u het vereiste beleid voor voorwaardelijke toegang maakt. In het scenario in deze Snelstartgids wordt het volgende gebruikt:

- De Azure Portal als tijdelijke aanduiding voor een Cloud-app waarvoor uw gebruiks voorwaarden moeten worden geaccepteerd. 
- Uw voorbeeld gebruiker om het beleid voor voorwaardelijke toegang te testen.  

Stel in uw beleid het volgende in:

| Instelling | Waarde |
| --- | --- |
| Gebruikers en groepen | Isabella Simonsen |
| Cloud-apps | Microsoft Azure beheer |
| Toegang verlenen | Mijn gebruiks voorwaarden |

![Beleid maken](./media/require-tou/1234.png)

**Het beleid voor voorwaardelijke toegang configureren:**

1. Op de pagina **Nieuw** , in het tekstvak **naam** , typt u gebruiks **voorwaarden vereisen voor Isabella**.

   ![Naam](./media/require-tou/71.png)

1. Klik in de sectie **toewijzing** op **gebruikers en groepen**.

   ![Gebruikers en groepen](./media/require-tou/06.png)

1. Op de pagina **gebruikers en groepen** :

   ![Gebruikers en groepen](./media/require-tou/24.png)

   1. Klik op **gebruikers en groepen selecteren**en selecteer vervolgens **gebruikers en groepen**.
   1. Klik op **Selecteren**.
   1. Selecteer op de pagina **selecteren** de optie **Isabella Simonsen**en klik vervolgens op **selecteren**.
   1. Klik op de pagina **gebruikers en groepen** op **gereed**.
1. Klik op **Cloud-apps**.

   ![Cloud-apps](./media/require-tou/08.png)

1. Op de pagina **Cloud-apps** :

   ![Cloud-apps selecteren](./media/require-tou/26.png)

   1. Klik op **apps selecteren**.
   1. Klik op **Selecteren**.
   1. Selecteer op de pagina **selecteren** de optie **Microsoft Azure beheer**en klik vervolgens op **selecteren**.
   1. Klik op de pagina **Cloud-apps** op **gereed**.
1. Klik in de sectie **toegangs beheer** op **verlenen**.

   ![Toegangs beheer](./media/require-tou/10.png)

1. Op de pagina **Grant** :

   ![Geef](./media/require-tou/111.png)

   1. Selecteer **toegang verlenen**.
   1. Selecteer **mijn gebruiks voorwaarden**.
   1. Klik op **Selecteren**.
1. Klik in de sectie **beleid inschakelen** op **aan**.

   ![Beleid inschakelen](./media/require-tou/18.png)

1. Klik op **Create**.

## <a name="evaluate-a-simulated-sign-in"></a>Een gesimuleerde aanmelding evalueren

Nu u het beleid voor voorwaardelijke toegang hebt geconfigureerd, wilt u waarschijnlijk weten of het werkt zoals verwacht. Als eerste stap gebruikt u de functie voor voorwaardelijke toegang wat als-beleid voor het simuleren van een aanmelding van uw test gebruiker. De simulatie schat de impact van deze aanmelding op uw beleid in en genereert een simulatierapport.  

Stel het hulp programma voor het evalueren van **What if** -beleid in door het volgende in te stellen:

- **Isabella Simonsen** als gebruiker
- **Microsoft Azure beheer** als Cloud-app

Als u op **What if** klikt, wordt er een simulatie rapport gemaakt waarin wordt getoond:

- Gebruiks **voorwaarden voor Isabella vereisen** onder **beleids regels die van toepassing zijn**
- **Mijn gebruiks voorwaarden** voor **granting Controls**.

![Wat als beleids programma](./media/require-tou/79.png)

**Het beleid voor voorwaardelijke toegang evalueren:**

1. Klik op de pagina [voorwaardelijke toegang-beleids regels](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) in het menu aan de bovenkant op **What if**.  

   ![What If](./media/require-tou/14.png)

1. Klik op **gebruikers**, selecteer **Isabella Simonsen**en klik vervolgens op **selecteren**.

   ![Gebruiker](./media/require-tou/15.png)

1. Een Cloud-app selecteren:

   ![Cloud-apps](./media/require-tou/16.png)

   1. Klik op **Cloud-apps**.
   1. Klik op de **pagina Cloud-apps**op **apps selecteren**.
   1. Klik op **Selecteren**.
   1. Selecteer op de pagina **selecteren** de optie **Microsoft Azure beheer**en klik vervolgens op **selecteren**.
   1. Klik op de pagina Cloud-apps op **gereed**.
1. Klik op **What if**.

## <a name="test-your-conditional-access-policy"></a>Het beleid voor voorwaardelijke toegang testen

In de vorige sectie hebt u geleerd hoe u een gesimuleerde aanmelding kunt evalueren. Naast een simulatie moet u ook het beleid voor voorwaardelijke toegang testen om er zeker van te zijn dat het werkt zoals verwacht.

Als u uw beleid wilt testen, probeert u zich aan te melden bij uw [Azure Portal](https://portal.azure.com) met behulp van uw **Isabella Simonsen** -test account. Er verschijnt een dialoog venster waarin u moet akkoord gaan met de gebruiks voorwaarden.

![Gebruiksvoorwaarden](./media/require-tou/57.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, verwijdert u de test gebruiker en het beleid voor voorwaardelijke toegang:

- Zie [gebruikers uit Azure ad verwijderen](../fundamentals/add-users-azure-active-directory.md#delete-a-user)als u niet weet hoe u een Azure AD-gebruiker verwijdert.
- Als u uw beleid wilt verwijderen, selecteert u uw beleid en klikt u vervolgens op **verwijderen** op de werk balk snelle toegang.

    ![Multi-Factor Authentication](./media/require-tou/33.png)

- Als u de gebruiks voorwaarden wilt verwijderen, selecteert u deze en klikt u vervolgens op **voor waarden verwijderen** in de werk balk bovenaan.

    ![Multi-Factor Authentication](./media/require-tou/29.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [MFA vereisen voor specifieke apps](app-based-mfa.md)
> [toegang blok keren als er een sessie risico wordt gedetecteerd](app-sign-in-risk.md)
