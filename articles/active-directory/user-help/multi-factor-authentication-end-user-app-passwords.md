---
title: App-wachtwoorden beheren - Azure Active Directory | Microsoft Documenten
description: Meer informatie over app-wachtwoorden en waarvoor ze worden gebruikt met betrekking tot verificatie in twee stappen.
services: active-directory
author: curtand
manager: daveba
ms.reviewer: richagi
ms.assetid: 345b757b-5a2b-48eb-953f-d363313be9e5
ms.workload: identity
ms.service: active-directory
ms.subservice: user-help
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: curtand
ms.custom: user-help, seo-update-azuread-jan
ms.openlocfilehash: 5f81181ac3107307a352cdbcd0b5cc4a555deacb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253219"
---
# <a name="manage-app-passwords-for-two-step-verification"></a>App-wachtwoorden beheren voor verificatie in twee stappen

>[!Important]
>Uw beheerder staat u mogelijk niet toe app-wachtwoorden te gebruiken. Als u **App-wachtwoorden** niet als een optie ziet, zijn ze niet beschikbaar in uw organisatie.

Bij het gebruik van app-wachtwoorden is het belangrijk om te onthouden:

- App-wachtwoorden worden automatisch gegenereerd en moeten eenmaal per app worden gemaakt en ingevoerd.

- Er is een limiet van 40 wachtwoorden per gebruiker. Als u na die limiet een wachtwoord probeert te maken, wordt u gevraagd een bestaand wachtwoord te verwijderen voordat u het nieuwe wachtwoord mag maken.

    >[!Note]
    >Office 2013-clients (inclusief Outlook) ondersteunen nieuwe verificatieprotocollen en kunnen worden gebruikt met verificatie in twee stappen. Deze ondersteuning betekent dat u na verificatie in twee stappen geen app-wachtwoorden meer nodig hebt voor Office 2013-clients. Zie het artikel [Hoe moderne verificatie werkt voor Office 2013 en Office 2016 clientapps voor](https://support.office.com/article/how-modern-authentication-works-for-office-2013-and-office-2016-client-apps-e4c45989-4b1a-462e-a81b-2a13191cf517) meer informatie.

## <a name="create-new-app-passwords"></a>Nieuwe app-wachtwoorden maken

Tijdens uw eerste registratieprocedure voor twee factoren krijgt u één app-wachtwoord. Als je er meer dan één nodig hebt, moet je ze zelf maken. U app-wachtwoorden maken uit meerdere gebieden, afhankelijk van hoe tweestapsverificatie in uw organisatie is ingesteld. Zie [Overzicht voor tweestapsverificatie en uw werk- of schoolaccount](multi-factor-authentication-end-user-first-time.md) en de bijbehorende artikelen voor meer informatie over het registreren om tweestapsverificatie te gebruiken met uw werk- of schoolaccount.

### <a name="where-to-create-and-delete-your-app-passwords"></a>Waar u uw app-wachtwoorden maken en verwijderen

U app-wachtwoorden maken en verwijderen op basis van de manier waarop u tweestapsverificatie gebruikt:

- **Uw organisatie gebruikt tweestapsverificatie en de pagina Aanvullende beveiligingsverificatie.** Als u uw werk- of schoolaccount alain@contoso.com(zoals) gebruikt met tweestapsverificatie in uw organisatie, u uw app-wachtwoorden beheren via de [pagina Extra beveiligingsverificatie.](https://account.activedirectory.windowsazure.com/Proofup.aspx) Zie [App-wachtwoorden maken en verwijderen via de pagina Extra beveiligingsverificatie](#create-and-delete-app-passwords-from-the-additional-security-verification-page) in dit artikel voor gedetailleerde instructies.

- **Uw organisatie maakt gebruik van tweestapsverificatie en de Office 365-portal.** Als u uw werk- of schoolaccount alain@contoso.com(zoals), tweestapsverificatie en Office 365-apps in uw organisatie gebruikt, u uw app-wachtwoorden beheren via de [portalpagina van Office 365.](https://www.office.com) Zie [App-wachtwoorden maken en verwijderen met de Office 365-portal](#create-and-delete-app-passwords-using-the-office-365-portal) in dit artikel voor gedetailleerde instructies.

- **U gebruikt tweestapsverificatie met een persoonlijk Microsoft-account.** Als u een persoonlijk Microsoft-account alain@outlook.com(zoals) gebruikt met tweestapsverificatie, u uw app-wachtwoorden beheren via de [basispagina Beveiliging.](https://account.microsoft.com/security/) Zie [App-wachtwoorden gebruiken met apps die geen verificatie in twee stappen ondersteunen](https://support.microsoft.com/help/12409/microsoft-account-app-passwords-and-two-step-verification)voor gedetailleerde instructies.

## <a name="create-and-delete-app-passwords-from-the-additional-security-verification-page"></a>App-wachtwoorden maken en verwijderen van de pagina Extra beveiligingsverificatie

U app-wachtwoorden maken en verwijderen van de pagina **Extra beveiligingsverificatie** voor uw werk- of schoolaccount.

1. Meld u aan bij de [pagina Extra beveiligingsverificatie](https://account.activedirectory.windowsazure.com/Proofup.aspx)en selecteer **vervolgens App-wachtwoorden**.

    ![Pagina App-wachtwoorden, met het tabblad App-wachtwoorden gemarkeerd](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

2. Selecteer **Maken,** typ de naam van de app waarvoor het app-wachtwoord vereist is en selecteer **Volgende**.

    ![Pagina App-wachtwoorden maken, met de naam van de app die wachtwoord nodig heeft](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

3. Kopieer het wachtwoord van de pagina **Wachtwoord van uw app** en selecteer **Sluiten**.

    ![De wachtwoordpagina van uw app met het wachtwoord voor uw opgegeven app](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

4. Controleer op de pagina **App-wachtwoorden** of uw app wordt weergegeven.

     ![Pagina App-wachtwoorden, met nieuwe app weergegeven in de lijst](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

5. Open de app waarvoor u het app-wachtwoord hebt gemaakt (bijvoorbeeld Outlook 2010) en plak het app-wachtwoord wanneer daarom wordt gevraagd. U hoeft dit slechts één keer per app te doen.

### <a name="to-delete-an-app-password-using-the-app-passwords-page"></a>Een app-wachtwoord verwijderen met de pagina App-wachtwoorden

1. Selecteer op de pagina **App-wachtwoorden** de optie **Verwijderen** naast het app-wachtwoord dat u wilt verwijderen.

   ![Een app-wachtwoord verwijderen](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Selecteer **Ja** om te bevestigen dat u het wachtwoord wilt verwijderen en selecteer **Sluiten**.

    Het app-wachtwoord wordt verwijderd.

## <a name="create-and-delete-app-passwords-using-the-office-365-portal"></a>App-wachtwoorden maken en verwijderen met de Office 365-portal

Als u verificatie in twee stappen gebruikt met uw werk- of schoolaccount en uw Office 365-apps, u uw app-wachtwoorden maken en verwijderen via de Office 365-portal.

### <a name="to-create-app-passwords-using-the-office-365-portal"></a>App-wachtwoorden maken met de Office 365-portal

1. Meld u aan bij Office 365 en ga vervolgens naar de [pagina Mijn account,](https://portal.office.com)selecteer **Beveiliging & privacy**en vouw aanvullende **beveiligingsverificatie**uit.

    ![Office-portal met uitgebreid extra beveiligingsverificatiegebied](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-o365-my-account-page.png)

2. Selecteer de tekst die zegt: **App-wachtwoorden maken en beheren** om de pagina **App-wachtwoorden** te openen.

    ![Pagina App-wachtwoorden, met het tabblad App-wachtwoorden gemarkeerd](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page.png)

3. Selecteer **Maken,** typ de naam van de app waarvoor het app-wachtwoord vereist is en selecteer **Volgende**.

    ![Pagina App-wachtwoorden maken, met de naam van de app die wachtwoord nodig heeft](media/multi-factor-authentication-end-user-app-passwords/mfa-create-app-password-page.png)

4. Kopieer het wachtwoord van de pagina **Wachtwoord van uw app** en selecteer **Sluiten**.

    ![De wachtwoordpagina van uw app met het wachtwoord voor uw opgegeven app](media/multi-factor-authentication-end-user-app-passwords/mfa-your-app-password-page.png)

5. Controleer op de pagina **App-wachtwoorden** of uw app wordt weergegeven.

     ![Pagina App-wachtwoorden, met nieuwe app weergegeven in de lijst](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-with-new-password.png)  

6. Open de app waarvoor u het app-wachtwoord hebt gemaakt (bijvoorbeeld Outlook 2010) en plak het app-wachtwoord wanneer daarom wordt gevraagd. U hoeft dit slechts één keer per app te doen.

### <a name="to-delete-app-passwords-using-the-app-passwords-page"></a>App-wachtwoorden verwijderen via de pagina App-wachtwoorden

1. Selecteer op de pagina **App-wachtwoorden** de optie **Verwijderen** naast het app-wachtwoord dat u wilt verwijderen.

   ![Een app-wachtwoord verwijderen](media/multi-factor-authentication-end-user-app-passwords/mfa-app-passwords-page-delete.png)

2. Selecteer **Ja** in het bevestigingsvak en selecteer **Sluiten**.

    Het app-wachtwoord wordt verwijderd.

## <a name="if-your-app-passwords-arent-working-properly"></a>Als uw app-wachtwoorden niet goed werken

Zorg ervoor dat u uw wachtwoord correct hebt getypt. Als u zeker weet dat u uw wachtwoord correct hebt ingevoerd, u opnieuw proberen u aan te melden en een nieuw app-wachtwoord te maken. Als geen van deze opties uw probleem oplost, neemt u contact op met de helpdesk van uw organisatie, zodat ze uw bestaande app-wachtwoorden kunnen verwijderen, zodat u gloednieuwe wachtwoorden maken.

## <a name="next-steps"></a>Volgende stappen

- [Uw verificatie-instellingen in twee stappen beheren](multi-factor-authentication-end-user-manage-settings.md)

- Probeer de [Microsoft Authenticator-app](user-help-auth-app-download-install.md) om uw aanmeldingen te verifiëren met app-meldingen, in plaats van sms'jes of oproepen te ontvangen.
