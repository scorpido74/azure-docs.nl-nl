---
title: Probleem aanmelden bij de website van het toegangspaneel | Microsoft Documenten
description: Richtlijnen voor het oplossen van problemen die u tegenkomen tijdens het aanmelden om het toegangspaneel te gebruiken
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: mimart
ms.reviwer: japere,asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: a7c6a9c3f26c8939176197a2ecf2fcd6026e9928
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65784309"
---
# <a name="problem-signing-in-to-the-access-panel-website"></a>Probleem met aanmelden bij de website van het toegangspaneel

Het Access-paneel is een webportal waarmee een gebruiker met een werk- of schoolaccount in Azure Active Directory (Azure AD) cloudtoepassingen kan weergeven en starten waartoe de Azure AD-beheerder hem toegang heeft verleend. Een gebruiker met Azure AD-edities kan ook gebruikmaken van mogelijkheden voor selfservicegroepen en app-beheer via het Access-paneel. Het Access-paneel staat los van de Azure-portal en vereist niet dat gebruikers een Azure-abonnement hebben.

Gebruikers kunnen zich aanmelden bij het Toegangspaneel als ze een werk- of schoolaccount hebben in Azure AD.

-   Gebruikers kunnen rechtstreeks door Azure AD worden geverifieerd.

-   Gebruikers kunnen worden geverifieerd met behulp van Active Directory Federation Services (AD FS).

-   Gebruikers kunnen worden geverifieerd door Windows Server Active Directory.

Als een gebruiker een abonnement heeft voor Azure of Office 365 en de Azure-portal of een Office 365-toepassing heeft gebruikt, kan hij het Access Panel naadloos gebruiken zonder dat hij zich opnieuw hoeft aan te melden. Gebruikers die niet zijn geverifieerd, wordt gevraagd zich aan te melden met de gebruikersnaam en het wachtwoord voor hun account in Azure AD. Als de organisatie federatie heeft geconfigureerd, is het typen van de gebruikersnaam voldoende.

## <a name="general-issues-to-check-first"></a>Algemene problemen om eerst te controleren 

-   Zorg ervoor dat de gebruiker zich aanmeldt bij de **juiste URL:**<https://myapps.microsoft.com>

-   Zorg ervoor dat de browser van de gebruiker de URL heeft toegevoegd aan zijn **vertrouwde sites**

-   Controleer of het account van de gebruiker is **ingeschakeld** voor aanmeldingen.

-   Zorg ervoor dat het account van de gebruiker niet is **vergrendeld.**

-   Zorg ervoor dat het wachtwoord van de gebruiker **niet is verlopen of vergeten.**

-   Controleer of **multi-factorauthenticatie** de toegang van gebruikers niet blokkeert.

-   Zorg ervoor dat een **beleid voor voorwaardelijke toegang** of beleid voor **identiteitsbescherming** de toegang van gebruikers niet blokkeert.

-   Zorg ervoor dat de contactgegevens van een gebruiker de **verificatie-contactgegevens** up-to-date zijn, zodat beleid voor meervoudige verificatie of voorwaardelijke toegang kan worden afgedwongen.

-   Zorg ervoor dat u ook probeert de cookies van uw browser te wissen en opnieuw te proberen in te loggen.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Voldoen aan browservereisten voor het Toegangspaneel

Het Access Panel vereist een browser die JavaScript ondersteunt en CSS heeft ingeschakeld. Als u een op eenwachtwoord gebaseerde aanmelding (SSO) wilt gebruiken in het toegangspaneel, moet de extensie Toegangspaneel in de browser van de gebruiker worden geïnstalleerd. Deze extensie wordt automatisch gedownload wanneer een gebruiker een toepassing selecteert die is geconfigureerd voor sso op basis van wachtwoorden.

Voor sso op basis van wachtwoorden kunnen de browsers van de eindgebruiker zijn:

-   Internet Explorer 8, 9, 10, 11 - op Windows 7 of hoger

-   Microsoft Edge op Windows 10 Anniversary Edition of hoger 

-   Chrome - op Windows 7 of hoger, en op MacOS X of hoger

-   Firefox 26.0 of hoger , op Windows XP SP2 of hoger, en op Mac OS X 10.6 of hoger


## <a name="problems-with-the-users-account"></a>Problemen met het account van de gebruiker

De toegang tot het toegangspaneel kan worden geblokkeerd vanwege een probleem met het account van de gebruiker. Hieronder volgen enkele manieren waarop u problemen met gebruikers en hun accountinstellingen oplossen en oplossen:

-   [Controleren of er een gebruikersaccount bestaat in Azure Active Directory](#check-if-a-user-account-exists-in-azure-active-directory)

-   [De accountstatus van een gebruiker controleren](#check-a-users-account-status)

-   [Het wachtwoord van een gebruiker opnieuw instellen](#reset-a-users-password)

-   [Selfservice voor wachtwoordherstel inschakelen](#enable-self-service-password-reset)

-   [De multifactorauthenticatiestatus van een gebruiker controleren](#check-a-users-multi-factor-authentication-status)

-   [Contactgegevens van een gebruiker controleren](#check-a-users-authentication-contact-info)

-   [Groepslidmaatschappen van een gebruiker controleren](#check-a-users-group-memberships)

-   [De toegewezen licenties van een gebruiker controleren](#check-a-users-assigned-licenses)

-   [Een gebruiker een licentie toewijzen](#assign-a-user-a-license)

### <a name="check-if-a-user-account-exists-in-azure-active-directory"></a>Controleren of er een gebruikersaccount bestaat in Azure Active Directory

Voer de volgende stappen uit om te controleren of het account van een gebruiker aanwezig is:

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Gebruikers en groepen** in het navigatiemenu.

5.  klik op **Alle gebruikers**.

6.  **Zoek naar** de gebruiker waarin u geïnteresseerd bent en **klik op de rij** om te selecteren.

7.  Controleer de eigenschappen van het object van de gebruiker om er zeker van te zijn dat ze eruit zien zoals u verwacht en dat er geen gegevens ontbreken.

### <a name="check-a-users-account-status"></a>De accountstatus van een gebruiker controleren

Voer de volgende stappen uit om de accountstatus van een gebruiker te controleren:

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Gebruikers en groepen** in het navigatiemenu.

5.  klik op **Alle gebruikers**.

6.  **Zoek naar** de gebruiker waarin u geïnteresseerd bent en **klik op de rij** om te selecteren.

7.  klik op **Profiel**.

8.  Controleer **onder Instellingen** of Aanmelden voor **blokkeren** is ingesteld op **Nee**.

### <a name="reset-a-users-password"></a>Het wachtwoord van een gebruiker opnieuw instellen

Voer de volgende stappen uit om het wachtwoord van een gebruiker opnieuw in te stellen:

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Gebruikers en groepen** in het navigatiemenu.

5.  klik op **Alle gebruikers**.

6.  **Zoek naar** de gebruiker waarin u geïnteresseerd bent en **klik op de rij** om te selecteren.

7.  klik op de knop **Wachtwoord opnieuw instellen** boven aan het gebruikersvenster.

8.  klik op de knop **Wachtwoord opnieuw instellen** in het deelvenster Wachtwoord opnieuw **instellen** dat wordt weergegeven.

9.  Kopieer het **tijdelijke wachtwoord** of voer een **nieuw wachtwoord** in voor de gebruiker.

10. Ze moeten dit wachtwoord aan de gebruiker meedelen, maar moeten dit wachtwoord wijzigen tijdens hun volgende aanmelding in Azure Active Directory.

### <a name="enable-self-service-password-reset"></a>Selfservice voor wachtwoordherstel inschakelen

Voer de volgende implementatiestappen uit om het opnieuw instellen van selfservicewachtwoorden in te schakelen:

-   [Gebruikers in staat stellen hun Azure Active Directory-wachtwoorden opnieuw te instellen](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

-   [Gebruikers in staat stellen hun on-premises wachtwoorden van Active Directory opnieuw in te stellen of te wijzigen](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started)

### <a name="check-a-users-multi-factor-authentication-status"></a>De multifactorauthenticatiestatus van een gebruiker controleren

Voer de volgende stappen uit om de multifactorauthenticatiestatus van een gebruiker te controleren:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Gebruikers en groepen** in het navigatiemenu.

5. klik op **Alle gebruikers**.

6. klik op de knop **Multifactorverificatie** boven aan het deelvenster.

7. Zodra de **Multi-Factor Authentication Administration Portal** is geladen, moet u ervoor zorgen dat u zich op het tabblad **Gebruikers** bevindt.

8. Zoek de gebruiker in de lijst met gebruikers door te zoeken, te filteren of te sorteren.

9. Selecteer de gebruiker in de lijst met gebruikers en **Schakel**multifactorauthenticatie naar wens **in, schakel uit**of dwing multifactorauthenticatie **af.**

   >[!NOTE]
   >Als een gebruiker zich in **een afgedwongen** toestand bevindt, u deze tijdelijk instellen **op Uitgeschakeld** om hem of haar terug te laten keren naar zijn account. Zodra ze weer binnen zijn, u hun status opnieuw wijzigen **in Ingeschakeld** om te eisen dat ze hun contactgegevens opnieuw registreren tijdens hun volgende aanmelding. U ook de stappen volgen in de [contactgegevens van de verificatiegegevens van een gebruiker](#check-a-users-authentication-contact-info) controleren om deze gegevens voor hen te verifiëren of in te stellen.
   >
   >

### <a name="check-a-users-authentication-contact-info"></a>Contactgegevens van een gebruiker controleren

Voer de volgende stappen uit om de contactgegevens van een gebruiker te controleren die worden gebruikt voor multifactorauthenticatie, voorwaardelijke toegang, identiteitsbeveiliging en wachtwoordreset:

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Gebruikers en groepen** in het navigatiemenu.

5.  klik op **Alle gebruikers**.

6.  **Zoek naar** de gebruiker waarin u geïnteresseerd bent en **klik op de rij** om te selecteren.

7.  klik op **Profiel**.

8.  Schuif omlaag naar **Contactgegevens voor verificatie**.

9.  **Controleer** de gegevens die zijn geregistreerd voor de gebruiker en werk indien nodig bij.

### <a name="check-a-users-group-memberships"></a>Groepslidmaatschappen van een gebruiker controleren

Voer de volgende stappen uit om groepslidmaatschappen van een gebruiker te controleren:

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Gebruikers en groepen** in het navigatiemenu.

5.  klik op **Alle gebruikers**.

6.  **Zoek naar** de gebruiker waarin u geïnteresseerd bent en **klik op de rij** om te selecteren.

7.  klik op **Groepen** om te zien van welke groepen de gebruiker lid is.

### <a name="check-a-users-assigned-licenses"></a>De toegewezen licenties van een gebruiker controleren

Voer de volgende stappen uit om de toegewezen licenties van een gebruiker te controleren:

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Gebruikers en groepen** in het navigatiemenu.

5.  klik op **Alle gebruikers**.

6.  **Zoek naar** de gebruiker waarin u geïnteresseerd bent en **klik op de rij** om te selecteren.

7.  klik op **Licenties** om te zien welke licenties de gebruiker momenteel heeft toegewezen.

### <a name="assign-a-user-a-license"></a>Een gebruiker een licentie toewijzen 

Voer de volgende stappen uit om een licentie aan een gebruiker toe te wijzen:

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Gebruikers en groepen** in het navigatiemenu.

5.  klik op **Alle gebruikers**.

6.  **Zoek naar** de gebruiker waarin u geïnteresseerd bent en **klik op de rij** om te selecteren.

7.  klik op **Licenties** om te zien welke licenties de gebruiker momenteel heeft toegewezen.

8.  klik op de knop **Toewijzen.**

9.  Selecteer **een of meer producten** uit de lijst met beschikbare producten.

10. **Optioneel** klikt u op het item **toewijzingsopties** om producten gedetailleerd toe te wijzen. Klik **op Ok** wanneer dit is voltooid.

11. Klik **op** de knop Toewijzen om deze licenties aan deze gebruiker toe te wijzen.

## <a name="if-these-troubleshooting-steps-do-not-resolve-the-issue"></a>Als deze stappen voor het oplossen van problemen het probleem niet oplossen

open een support ticket met de volgende informatie indien beschikbaar:

-   Correlatiefout-id

-   UPN (e-mailadres van de gebruiker)

-   Tenant-id

-   Browsertype

-   Tijdzone en tijd/tijdsbestek tijdens fout treedt op

-   Fiddler sporen

## <a name="next-steps"></a>Volgende stappen
[Eén aanmelding bij uw apps bieden met Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)
