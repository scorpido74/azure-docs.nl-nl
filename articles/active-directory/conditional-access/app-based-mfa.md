---
title: Azure MFA vereisen met voorwaardelijke toegang-Azure Active Directory
description: In deze Quick Start leert u hoe u uw verificatie vereisten kunt koppelen aan het type van de toegang tot de Cloud-app met behulp van Azure Active Directory (Azure AD) voorwaardelijke toegang.
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
ms.openlocfilehash: 55c9188a1320b92aafa5fc67a253b42b6b107711
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381079"
---
# <a name="quickstart-require-mfa-for-specific-apps-with-azure-active-directory-conditional-access"></a>Quick Start: MFA vereisen voor specifieke apps met voorwaardelijke toegang Azure Active Directory

Als u de aanmeld ervaring van uw gebruikers wilt vereenvoudigen, kunt u hen toestaan zich aan te melden bij uw Cloud-apps met behulp van een gebruikers naam en wacht woord. Veel omgevingen hebben echter ten minste enkele apps waarvoor wordt aanbevolen een sterkere vorm van account verificatie te vereisen, zoals MFA (multi-factor Authentication). Dit beleid kan waar zijn om toegang te krijgen tot het e-mail systeem van uw organisatie of uw HR-apps. In Azure Active Directory (Azure AD) kunt u dit doel bereiken met een beleid voor voorwaardelijke toegang.

In deze Quick start ziet u hoe u een [beleid voor voorwaardelijke toegang voor Azure AD](../active-directory-conditional-access-azure-portal.md) configureert waarvoor multi-factor Authentication is vereist voor een geselecteerde Cloud-app in uw omgeving.

![Voor beeld van beleid voor voorwaardelijke toegang in de Azure Portal](./media/app-based-mfa/32.png)

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het volt ooien van het scenario in deze Quick Start hebt u het volgende nodig:

- **Toegang tot een Azure AD Premium Edition** : voorwaardelijke toegang voor Azure AD is een Azure AD Premium mogelijkheid.
- **Een test account met de naam Isabella Simonsen** : als u niet weet hoe u een test account moet maken, raadpleegt u [Cloud gebruikers toevoegen](../fundamentals/add-users-azure-active-directory.md#add-a-new-user).

Voor het scenario in deze Snelstartgids moet per gebruiker MFA niet zijn ingeschakeld voor uw test account. Zie [verificatie in twee stappen vereisen voor een gebruiker](../authentication/howto-mfa-userstates.md)voor meer informatie.

## <a name="test-your-experience"></a>Uw ervaring testen

Het doel van deze stap is om een indruk te krijgen van de ervaring zonder beleid voor voorwaardelijke toegang.

**Uw omgeving initialiseren:**

1. Meld u aan bij uw Azure Portal als Isabella Simonsen.
1. Meld u af.

## <a name="create-your-conditional-access-policy"></a>Het beleid voor voorwaardelijke toegang maken

In deze sectie wordt beschreven hoe u het vereiste beleid voor voorwaardelijke toegang maakt. In het scenario in deze Snelstartgids wordt het volgende gebruikt:

- De Azure Portal als tijdelijke aanduiding voor een Cloud-app die MFA vereist. 
- Uw voorbeeld gebruiker om het beleid voor voorwaardelijke toegang te testen.  

Stel in uw beleid het volgende in:

| Instelling | Waarde |
| --- | --- |
| Gebruikers en groepen | Isabella Simonsen |
| Cloud-apps | Microsoft Azure beheer |
| Toegang verlenen | Multi-factor Authentication vereisen |

![Uitgebreid beleid voor voorwaardelijke toegang](./media/app-based-mfa/31.png)

**Het beleid voor voorwaardelijke toegang configureren:**

1. Meld u aan bij uw [Azure Portal](https://portal.azure.com) als globale beheerder, beveiligings beheerder of een beheerder voor voorwaardelijke toegang.
1. Zoek in het Azure Portal naar en selecteer **Azure Active Directory**.

   ![Azure Active Directory](./media/app-based-mfa/02.png)

1. Klik op de pagina **Azure Active Directory** in de sectie **beveiliging** op **voorwaardelijke toegang**.

   ![Voorwaardelijke toegang](./media/app-based-mfa/03.png)

1. Klik op de pagina **voorwaardelijke toegang** in de werk balk aan de bovenkant op **Nieuw beleid**.

   ![Toevoegen](./media/app-based-mfa/04.png)

1. Typ op de pagina **Nieuw** in het TEKSTVAK **naam** **MFA vereisen voor toegang tot Azure Portal**.

   ![Naam](./media/app-based-mfa/05.png)

1. Klik in de sectie **toewijzing** op **gebruikers en groepen**.

   ![Gebruikers en groepen](./media/app-based-mfa/06.png)

1. Voer de volgende stappen uit op de pagina **gebruikers en groepen** :

   ![Gebruikers en groepen](./media/app-based-mfa/24.png)

   1. Klik op **gebruikers en groepen selecteren**en selecteer vervolgens **gebruikers en groepen**.
   1. Klik op **Selecteren**.
   1. Selecteer op de pagina **selecteren** de optie **Isabella Simonsen**en klik vervolgens op **selecteren**.
   1. Klik op de pagina **gebruikers en groepen** op **gereed**.

1. Klik op **Cloud-apps**.

   ![Cloud-apps](./media/app-based-mfa/08.png)

1. Voer de volgende stappen uit op de pagina **Cloud-apps** :

   ![Cloud-apps selecteren](./media/app-based-mfa/26.png)

   1. Klik op **apps selecteren**.
   1. Klik op **Selecteren**.
   1. Selecteer op de pagina **selecteren** de optie **Microsoft Azure beheer**en klik vervolgens op **selecteren**.
   1. Klik op de pagina **Cloud-apps** op **gereed**.

1. Klik in de sectie **toegangs beheer** op **verlenen**.

   ![Toegangs beheer](./media/app-based-mfa/10.png)

1. Voer de volgende stappen uit op de pagina **Grant** :

   ![Geef](./media/app-based-mfa/11.png)

   1. Selecteer **toegang verlenen**.
   1. Selecteer **multi-factor Authentication vereisen**.
   1. Klik op **Selecteren**.

1. Klik in de sectie **beleid inschakelen** op **aan**.

   ![Beleid inschakelen](./media/app-based-mfa/18.png)

1. Klik op **Create**.

## <a name="evaluate-a-simulated-sign-in"></a>Een gesimuleerd aanmelden evalueren

Nu u het beleid voor voorwaardelijke toegang hebt geconfigureerd, wilt u waarschijnlijk weten of het werkt zoals verwacht. Als eerste stap gebruikt u de voorwaardelijke toegang What if Policy tool om een aanmelding van uw test gebruiker te simuleren. De simulatie schat de invloed die dit aanmelden heeft op uw beleid en genereert een simulatie rapport.  

Stel het hulp programma voor het evalueren van **What if** -beleid in door het volgende in te stellen:

- **Isabella Simonsen** als gebruiker
- **Microsoft Azure beheer** als Cloud-app

Als u op **What if** klikt, wordt er een simulatie rapport gemaakt waarin wordt getoond:

- **MFA vereisen voor toegang tot Azure Portal** onder **beleids regels die van toepassing zijn**
- **Multi-factor Authentication vereisen** als **Grant-besturings elementen**.

![Wat als beleids programma](./media/app-based-mfa/23.png)

**Het beleid voor voorwaardelijke toegang evalueren:**

1. Klik op de pagina [voorwaardelijke toegang-beleids regels](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ConditionalAccessBlade/Policies) in het menu aan de bovenkant op **What if**.  

   ![What If](./media/app-based-mfa/14.png)

1. Klik op **gebruikers**, selecteer **Isabella Simonsen**en klik vervolgens op **selecteren**.

   ![Gebruiker](./media/app-based-mfa/15.png)

1. Als u een Cloud-app wilt selecteren, voert u de volgende stappen uit:

   ![Cloud-apps](./media/app-based-mfa/16.png)

   1. Klik op **Cloud-apps**.
   1. Klik op de **pagina Cloud-apps**op **apps selecteren**.
   1. Klik op **Selecteren**.
   1. Selecteer op de pagina **selecteren** de optie **Microsoft Azure beheer**en klik vervolgens op **selecteren**.
   1. Klik op de pagina Cloud-apps op **gereed**.

1. Klik op **What if**.

## <a name="test-your-conditional-access-policy"></a>Het beleid voor voorwaardelijke toegang testen

In de vorige sectie hebt u geleerd hoe u een gesimuleerd aanmelden kunt evalueren. Naast een simulatie moet u ook het beleid voor voorwaardelijke toegang testen om er zeker van te zijn dat het werkt zoals verwacht.

Als u uw beleid wilt testen, probeert u zich aan te melden bij uw [Azure Portal](https://portal.azure.com) met behulp van uw **Isabella Simonsen** -test account. Er wordt een dialoog venster weer geven waarin u uw account moet instellen voor aanvullende beveiligings verificatie.

![Multi-Factor Authentication](./media/app-based-mfa/22.png)

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u deze niet meer nodig hebt, verwijdert u de test gebruiker en het beleid voor voorwaardelijke toegang:

- Zie [gebruikers uit Azure ad verwijderen](../fundamentals/add-users-azure-active-directory.md#delete-a-user)als u niet weet hoe u een Azure AD-gebruiker verwijdert.
- Als u uw beleid wilt verwijderen, selecteert u uw beleid en klikt u vervolgens op **verwijderen** op de werk balk snelle toegang.

    ![Multi-Factor Authentication](./media/app-based-mfa/33.png)

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Vereisen dat gebruiks voorwaarden worden geaccepteerd](require-tou.md)
> [toegang blok keren als er een sessie risico wordt gedetecteerd](app-sign-in-risk.md)
