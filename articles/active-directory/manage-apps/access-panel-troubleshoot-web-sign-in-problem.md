---
title: Probleem bij het aanmelden bij de website van het toegangs paneel | Microsoft Docs
description: Richt lijnen voor het oplossen van problemen die kunnen optreden tijdens het aanmelden om het toegangs venster te gebruiken
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviwer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f2ad4db231c616b3022ecafc62b12d6d81b67fc
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84760810"
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>Probleem bij het aanmelden bij de website van het toegangs venster

Het toegangs venster is een portal op Internet waarmee een gebruiker met een werk-of school account in Azure Active Directory (Azure AD) Cloud toepassingen kan weer geven en starten waartoe de Azure AD-beheerder hen toegang heeft verleend. Een gebruiker met Azure AD-edities kan ook selfservice-en app-beheer mogelijkheden gebruiken via het toegangs venster. Het toegangs venster is gescheiden van de Azure Portal en vereist niet dat gebruikers een Azure-abonnement hebben.

Gebruikers kunnen zich aanmelden bij het toegangs venster als ze een werk-of school account hebben in azure AD.

-   Gebruikers kunnen rechtstreeks worden geverifieerd door Azure AD.

-   Gebruikers kunnen worden geverifieerd met behulp van Active Directory Federation Services (AD FS).

-   Gebruikers kunnen worden geverifieerd door Windows Server Active Directory.

Als een gebruiker een abonnement heeft voor Azure of Office 365 en de Azure Portal of een Office 365-toepassing heeft gebruikt, kunnen ze het toegangs venster naadloos gebruiken zonder zich opnieuw aan te melden. Gebruikers die niet zijn geverifieerd, worden gevraagd zich aan te melden met behulp van de gebruikers naam en het wacht woord voor hun account in azure AD. Als de organisatie Federatie heeft geconfigureerd, is het typen van de gebruikers naam voldoende.

## <a name="general-issues-to-check-first"></a>Algemene problemen om eerst te controleren 

-   Zorg ervoor dat de gebruiker zich aanmeldt bij de **juiste URL**:<https://myapps.microsoft.com>

-   Zorg ervoor dat de browser van de gebruiker de URL aan de **vertrouwde sites** heeft toegevoegd

-   Zorg ervoor dat het gebruikers account is **ingeschakeld** voor aanmeldingen.

-   Controleer of het account van de gebruiker **niet is vergrendeld.**

-   Zorg ervoor dat het wacht woord van de gebruiker **niet is verlopen of is verg eten.**

-   Zorg ervoor dat **multi-factor Authentication** de gebruikers toegang niet blokkeert.

-   Zorg ervoor dat het beleid voor **voorwaardelijke toegang** of het beleid voor **identiteits beveiliging** de gebruikers toegang niet blokkeert.

-   Zorg ervoor dat de **contact gegevens** van de verificatie van een gebruiker up-to-date zijn om multi-factor Authentication of beleid voor voorwaardelijke toegang te kunnen afdwingen.

-   Probeer ook de cookies van uw browser te wissen en opnieuw aan te melden.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Browser vereisten voor de vergadering voor het toegangs venster

Het toegangs venster vereist een browser die Java script ondersteunt en waarvoor CSS is ingeschakeld. Als u eenmalige aanmelding (SSO) op basis van wacht woorden in het toegangs venster wilt gebruiken, moet de uitbrei ding van het toegangs paneel worden geïnstalleerd in de browser van de gebruiker. Deze uitbrei ding wordt automatisch gedownload wanneer een gebruiker een toepassing selecteert die is geconfigureerd voor SSO op basis van een wacht woord.

Voor SSO op basis van een wacht woord kunnen de browsers van de eind gebruiker het volgende zijn:

-   Internet Explorer 8, 9, 10, 11, op Windows 7 of hoger

-   Micro soft Edge in Windows 10 jubileum Edition of hoger 

-   Chrome--op Windows 7 of hoger en op MacOS X of hoger

-   Firefox 26,0 of hoger, op Windows XP SP2 of hoger, en op Mac OS X 10,6 of hoger


## <a name="problems-with-the-users-account"></a>Problemen met het account van de gebruiker

Toegang tot het toegangs venster kan worden geblokkeerd vanwege een probleem met het account van de gebruiker. Hieronder vindt u enkele manieren waarop u problemen met gebruikers en hun account instellingen kunt oplossen en oplossen:

-   [Controleren of een gebruikers account bestaat in Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [De account status van een gebruiker controleren](#check-a-users-account-status)

-   [Het wacht woord van een gebruiker opnieuw instellen](#reset-a-users-password)

-   [Selfservice voor wachtwoordherstel inschakelen](#enable-self-service-password-reset)

-   [De multi-factor Authentication-status van een gebruiker controleren](#check-a-users-multi-factor-authentication-status)

-   [De contact gegevens voor de verificatie van een gebruiker controleren](#check-a-users-authentication-contact-info)

-   [De groepslid maatschappen van een gebruiker controleren](#check-a-users-group-memberships)

-   [De toegewezen licenties van een gebruiker controleren](#check-a-users-assigned-licenses)

-   [Een licentie toewijzen aan een gebruiker](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Controleren of een gebruikers account bestaat in Azure Active Directory

Voer de volgende stappen uit om te controleren of het account van een gebruiker aanwezig is:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **gebruikers en groepen** in het navigatie menu.

5.  Klik op **alle gebruikers**.

6.  **Zoek** naar de gebruiker die u wilt interesseren en **Klik op de rij om deze** te selecteren.

7.  Controleer de eigenschappen van het gebruikers object om er zeker van te zijn dat ze eruitzien zoals verwacht en er geen gegevens ontbreken.

### <a name="check-a-users-account-status"></a>De account status van een gebruiker controleren

Voer de volgende stappen uit om de account status van een gebruiker te controleren:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **gebruikers en groepen** in het navigatie menu.

5.  Klik op **alle gebruikers**.

6.  **Zoek** naar de gebruiker die u wilt interesseren en **Klik op de rij om deze** te selecteren.

7.  Klik op **profiel**.

8.  Onder **instellingen** zorgt u ervoor dat **blok keren aanmelden** is ingesteld op **Nee**.

### <a name="reset-a-users-password"></a>Het wacht woord van een gebruiker opnieuw instellen

Voer de volgende stappen uit om het wacht woord van een gebruiker opnieuw in te stellen:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **gebruikers en groepen** in het navigatie menu.

5.  Klik op **alle gebruikers**.

6.  **Zoek** naar de gebruiker die u wilt interesseren en **Klik op de rij om deze** te selecteren.

7.  Klik boven aan het deel venster van de gebruiker op de knop **wacht woord opnieuw instellen** .

8.  Klik op de knop **wacht woord opnieuw instellen** in het deel venster **wacht woord opnieuw instellen** dat wordt weer gegeven.

9.  Kopieer het **tijdelijke wacht woord** of **Voer een nieuw wacht woord** voor de gebruiker in.

10. Dit nieuwe wacht woord aan de gebruiker door geven, moet dit wacht woord tijdens de volgende aanmelding worden gewijzigd in Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Selfservice voor wachtwoordherstel inschakelen

Voer de volgende implementatie stappen uit om selfservice voor wachtwoord herstel in te scha kelen:

-   [Gebruikers in staat stellen hun Azure Active Directory wacht woorden opnieuw in te stellen](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

-   [Gebruikers in staat stellen om hun Active Directory on-premises wacht woorden opnieuw in te stellen of te wijzigen](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>De multi-factor Authentication-status van een gebruiker controleren

Voer de volgende stappen uit om de multi-factor Authentication-status van een gebruiker te controleren:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **gebruikers en groepen** in het navigatie menu.

5. Klik op **alle gebruikers**.

6. Klik boven aan het deel venster op de knop **multi-factor Authentication** .

7. Nadat de **multi-factor Authentication beheer Portal** is geladen, controleert u of u zich op het tabblad **gebruikers** bevindt.

8. Zoek de gebruiker in de lijst met gebruikers door te zoeken, te filteren of te sorteren.

9. Selecteer de gebruiker in de lijst met gebruikers en **Schakel**indien **gewenst multi-** Factor Authentication in, **uit**of af.

   >[!NOTE]
   >Als een gebruiker een **afgedwongen** status heeft, kunt u deze zo instellen dat ze tijdelijk worden **uitgeschakeld** om ze weer in hun account te laten staan. Zodra ze weer in zijn, kunt u de status wijzigen in **ingeschakeld** zodat ze hun contact gegevens opnieuw moeten registreren tijdens de volgende aanmelding. U kunt ook de stappen volgen in de [contact gegevens van een gebruiker controleren](#check-a-users-authentication-contact-info) om te controleren of deze gegevens voor hen in te stellen.
   >
   >

### <a name="check-a-users-authentication-contact-info"></a>De contact gegevens voor de verificatie van een gebruiker controleren

Voer de volgende stappen uit om de contact gegevens van een gebruiker te controleren die wordt gebruikt voor multi-factor Authentication, voorwaardelijke toegang, identiteits beveiliging en wachtwoord herstel:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **gebruikers en groepen** in het navigatie menu.

5.  Klik op **alle gebruikers**.

6.  **Zoek** naar de gebruiker die u wilt interesseren en **Klik op de rij om deze** te selecteren.

7.  Klik op **profiel**.

8.  Schuif omlaag naar de **contact gegevens**van de verificatie.

9.  **Controleer** de gegevens die voor de gebruiker zijn geregistreerd en werk deze indien nodig bij.

### <a name="check-a-users-group-memberships"></a>De groepslid maatschappen van een gebruiker controleren

Voer de volgende stappen uit om de groepslid maatschappen van een gebruiker te controleren:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **gebruikers en groepen** in het navigatie menu.

5.  Klik op **alle gebruikers**.

6.  **Zoek** naar de gebruiker die u wilt interesseren en **Klik op de rij om deze** te selecteren.

7.  Klik op **groepen** om te zien van welke groepen de gebruiker lid is.

### <a name="check-a-users-assigned-licenses"></a>De toegewezen licenties van een gebruiker controleren

Voer de volgende stappen uit om de toegewezen licenties van een gebruiker te controleren:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **gebruikers en groepen** in het navigatie menu.

5.  Klik op **alle gebruikers**.

6.  **Zoek** naar de gebruiker die u wilt interesseren en **Klik op de rij om deze** te selecteren.

7.  Klik op **licenties** om te zien welke licenties de gebruiker momenteel heeft toegewezen.

### <a name="assign-a-user-a-license"></a>Een licentie toewijzen aan een gebruiker 

Voer de volgende stappen uit om een licentie aan een gebruiker toe te wijzen:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **gebruikers en groepen** in het navigatie menu.

5.  Klik op **alle gebruikers**.

6.  **Zoek** naar de gebruiker die u wilt interesseren en **Klik op de rij om deze** te selecteren.

7.  Klik op **licenties** om te zien welke licenties de gebruiker momenteel heeft toegewezen.

8.  Klik op de knop **toewijzen** .

9.  Selecteer **een of meer producten** uit de lijst met beschik bare producten.

10. **Optioneel** klikt u op het item **toewijzings opties** om producten nauw keurig toe te wijzen. Klik op **OK** wanneer dit is voltooid.

11. Klik op de knop **toewijzen** om deze licenties aan deze gebruiker toe te wijzen.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Als deze stappen voor probleem oplossing het probleem niet oplossen

Open een ondersteunings ticket met de volgende informatie, indien beschikbaar:

-   ID correlatie fout

-   UPN (e-mail adres van gebruiker)

-   Tenant-id

-   Browsertype

-   Tijd zone en tijd/tijds duur tijdens fout

-   Fiddler traceringen

## <a name="next-steps"></a>Volgende stappen
[Eenmalige aanmelding bieden voor uw apps met toepassings proxy](application-proxy-configure-single-sign-on-with-kcd.md)
