---
title: Problemen bij het aanmelden bij een toepassing met behulp van een deeplink | Microsoft Documenten
description: Problemen oplossen met toegang tot een toepassing vanuit een deeplink-URL met Azure AD
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
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 44825f32a13db0a221252c042dc9f23ec43a9c8f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65825417"
---
# <a name="problems-signing-in-to-an-application-using-a-deeplink"></a>Problemen met het aanmelden bij een toepassing met behulp van een deeplink

Het Access-paneel is een webportal waarmee een gebruiker met een werk- of schoolaccount in Azure Active Directory (Azure AD) cloudtoepassingen kan weergeven en starten waartoe de Azure AD-beheerder hem toegang heeft verleend. 

Deze toepassingen zijn geconfigureerd namens de gebruiker in de Azure AD-portal. De toepassing moet correct zijn geconfigureerd en toegewezen aan de gebruiker of een groep waarvan de gebruiker lid is om de toepassing in het toegangspaneel te zien.

Deep links of URL's voor gebruikerstoegang zijn koppelingen die uw gebruikers kunnen gebruiken om rechtstreeks vanuit hun URL-balken van hun browsers toegang te krijgen tot hun wachtwoord-SSO-toepassingen. Door naar deze koppeling te navigeren, worden gebruikers automatisch aangemeld bij de toepassing zonder eerst naar het toegangspaneel te hoeven gaan. Dit is dezelfde koppeling die gebruikers gebruiken om toegang te krijgen tot deze toepassingen via het startprogramma voor Office 365-toepassingen.

## <a name="general-issues-to-check-first"></a>Algemene problemen om eerst te controleren

-   Zorg ervoor dat u een **browser** gebruikt die voldoet aan de minimumvereisten voor het Toegangspaneel.

-   Zorg ervoor dat de browser van de gebruiker de URL van de toepassing heeft toegevoegd aan zijn **vertrouwde sites.**

-   Controleer of de toepassing correct is **geconfigureerd.**

-   Controleer of het account van de gebruiker is **ingeschakeld** voor aanmeldingen.

-   Zorg ervoor dat het account van de gebruiker niet is **vergrendeld.**

-   Zorg ervoor dat het wachtwoord van de gebruiker **niet is verlopen of vergeten.**

-   Controleer of **multi-factorauthenticatie** de toegang van gebruikers niet blokkeert.

-   Zorg ervoor dat een **beleid voor voorwaardelijke toegang** of beleid voor **identiteitsbescherming** de toegang van gebruikers niet blokkeert.

-   Zorg ervoor dat de contactgegevens van een gebruiker op de hoogte zijn van de gegevens over de **verificatie,** zodat het beleid voor meervoudige verificatie of voorwaardelijke toegang kan worden afgedwongen.

-   Zorg ervoor dat u ook probeert de cookies van uw browser te wissen en opnieuw te proberen in te loggen.

## <a name="checking-the-deeplink"></a>Het controleren van de deeplink

Voer de volgende stappen uit om te controleren of u de juiste deeplink hebt:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5. klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen.**

6. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

7. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

8. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

9. klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

10. klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

    * Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen.**

11. Selecteer de toepassing waarvoor u de deeplink wilt controleren.

12. Zoek de **URL voor gebruikerstoegang van**het label . Uw deeplink moet overeenkomen met deze URL.

## <a name="how-to-install-the-access-panel-browser-extension"></a>De extensie Access Panel Browser installeren

Voer de volgende stappen uit om de extensie Access Panel Browser te installeren:

1.  Open het [Access-paneel](https://myapps.microsoft.com) in een van de ondersteunde browsers en meld u aan als **gebruiker** in uw Azure AD.

2.  klik op een **Wachtwoord-SSO-toepassing** in het Access-paneel.

3.  Selecteer **Nu installeren**in de prompt waarin u vraagt de software te installeren .

4.  Op basis van uw browser wordt u doorverwezen naar de downloadlink. **Voeg** de extensie toe aan uw browser.

5.  Als uw browser daarom vraagt, selecteert u de extensie **inschakelen** of **toestaan.**

6.  Zodra u deze **browserhebt geïnstalleerd, start u** de browsersessie opnieuw.

7.  Meld u aan bij het Toegangspaneel en kijk of u uw wachtwoord-SSO-toepassingen **starten**

U de extensie voor Chrome en Firefox ook downloaden van deze directe links:

-   [Chrome Access Panel Extension](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox Access Panel Extension](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Eenmalige aanmelding voor een Azure AD-galeriestoepassing configureren

Als u een toepassing wilt configureren vanuit de Azure AD-galerie, moet u het als:

-   [Een toepassing toevoegen vanuit de Azure AD-galerie](#add-an-application-from-the-azure-ad-gallery)

-   [De toepassing voor eenmalige aanmelding voor wachtwoorden configureren](#configure-the-application-for-password-single-sign-on)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Een toepassing toevoegen vanuit de Azure AD-galerie

Voer de volgende stappen uit om een toepassing toe te voegen vanuit de Azure AD-galerie:

1.  Open de [Azure-portal](https://portal.azure.com) en meld u aan als **globale beheerder** of **medebeheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5.  klik op de knop **Toevoegen** in de rechterbovenhoek in het deelvenster **Ondernemingstoepassingen.**

6.  Typ in het tekstvak **Een naam invoeren** in de sectie Toevoegen vanuit de **galeriede** de naam van de toepassing.

7.  Selecteer de toepassing die u wilt configureren voor eenmalige aanmelding.

8.  Voordat u de toepassing toevoegt, u de naam wijzigen in het tekstvak **Naam.**

9.  Als u de toepassing wilt toevoegen, klikt u op **Toevoegen**.

Na een korte periode u het configuratievenster van de toepassing zien.

### <a name="configure-the-application-for-password-single-sign-on"></a>De toepassing voor eenmalige aanmelding voor wachtwoorden configureren

Voer de volgende stappen uit om eenmalige aanmelding voor een toepassing te configureren:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5. klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen.**

6. Selecteer de toepassing die u wilt configureren als u zich wilt aanmelden.

7. Zodra de toepassing is geladen, klikt u op de **aanmelding in één** keer in het linkernavigatiemenu van de toepassing.

8. Selecteer de modus **Aanmelding op basis van wachtwoord.**

9. [Gebruikers toewijzen aan de toepassing](#how-to-assign-a-user-to-an-application-directly).

10. Bovendien u namens de gebruiker ook referenties opgeven door de rijen van de gebruikers te selecteren en op **Referenties bijwerken** te klikken en namens de gebruikers de gebruikersnaam en het wachtwoord in te voeren. Anders worden gebruikers gevraagd om de referenties zelf in te voeren bij de lancering.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Eenmalige aanmelding voor een niet-galerietoepassing configureren voor een aanvraag voor een niet-galerie

Als u een toepassing wilt configureren vanuit de Azure AD-galerie, moet u het als:

-   [Een niet-galerietoepassing toevoegen](#add-a-non-gallery-application)

-   [De toepassing voor eenmalige aanmelding voor wachtwoorden configureren](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Een niet-galerietoepassing toevoegen

Voer de volgende stappen uit om een toepassing toe te voegen vanuit de Azure AD-galerie:

1.  Open de [Azure-portal](https://portal.azure.com) en meld u aan als **globale beheerder** of **medebeheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5.  klik op de knop **Toevoegen** in de rechterbovenhoek in het deelvenster **Ondernemingstoepassingen.**

6.  klik **op Niet-galerietoepassing.**

7.  Voer de naam van uw toepassing in het tekstvak **Naam** in. Selecteer **Toevoegen.**

Na een korte periode u het configuratievenster van de toepassing zien.

### <a name="configure-the-application-for-password-single-sign-on"></a>De toepassing voor eenmalige aanmelding voor wachtwoorden configureren

Voer de volgende stappen uit om eenmalige aanmelding voor een toepassing te configureren:

1.  Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5.  klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

    1.  Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen.**

6.  Selecteer de toepassing die u wilt configureren als u zich wilt aanmelden.

7.  Zodra de toepassing is geladen, klikt u op de **aanmelding in één** keer in het linkernavigatiemenu van de toepassing.

8.  Selecteer de modus **Aanmelding op basis van wachtwoord.**

9.  Voer de **AANmeldings-URL**in, de URL waar gebruikers hun gebruikersnaam en wachtwoord invoeren om zich aan te melden. Zorg ervoor dat de aanmeldingsvelden zichtbaar zijn op de URL.

10. Gebruikers toewijzen aan de toepassing.

11. Bovendien u namens de gebruiker ook referenties opgeven door de rijen van de gebruikers te selecteren en op **Referenties bijwerken** te klikken en namens de gebruikers de gebruikersnaam en het wachtwoord in te voeren. Anders worden gebruikers gevraagd om de referenties zelf in te voeren bij de lancering.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Een gebruiker rechtstreeks aan een toepassing toewijzen

Voer de volgende stappen uit om een of meer gebruikers rechtstreeks aan een toepassing toe te wijzen:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5. klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen.**

6. Selecteer de toepassing waaraan u een gebruiker wilt toewijzen uit de lijst.

7. Zodra de toepassing wordt geladen, klikt u op **Gebruikers en groepen** in het linkernavigatiemenu van de toepassing.

8. Klik **op** de knop Toevoegen boven aan de lijst **Gebruikers en groepen** om het deelvenster Toewijzing **toevoegen** te openen.

9. klik op de selectie **voor gebruikers en groepen** in het deelvenster Toewijzing **toevoegen.**

10. Typ de **volledige naam** of het volledige **e-mailadres** van de gebruiker die u wilt toewijzen aan het zoekvak Zoeken op naam of **e-mailadres.**

11. Plaats de **plaats van** de gebruiker in de lijst om een **selectievakje**te onthullen. Als u uw gebruiker wilt toevoegen aan de **lijst Geselecteerd,** klikt u op het selectievakje naast de profielfoto of het logo van de gebruiker.

12. **Optioneel:** Als u meer dan één gebruiker wilt **toevoegen,** typt u een andere **volledige naam** of **e-mailadres** in het zoekvak **Zoeken op naam of e-mailadres** en klikt u op het selectievakje om deze gebruiker toe te voegen aan de **lijst Geselecteerde.**

13. Wanneer u klaar bent met het selecteren van gebruikers, klikt u op de knop **Selecteren** om deze toe te voegen aan de lijst met gebruikers en groepen die aan de toepassing moeten worden toegewezen.

14. **Optioneel:** klik op de rolkiezer **selecteren** in het deelvenster **Toewijzing toevoegen** om een rol te selecteren die u wilt toewijzen aan de gebruikers die u hebt geselecteerd.

15. Klik **op** de knop Toewijzen om de toepassing toe te wijzen aan de geselecteerde gebruikers.

Na een korte periode kunnen de geselecteerde gebruikers deze toepassingen starten in het Access Panel.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Als deze stappen voor het oplossen van problemen het probleem niet oplossen. 

open een support ticket met de volgende informatie indien beschikbaar:

-   Correlatiefout-id

-   UPN (e-mailadres van de gebruiker)

-   TenantID

-   Browsertype

-   Tijdzone en tijd/tijdsbestek tijdens fout treedt op

-   Fiddler sporen

## <a name="next-steps"></a>Volgende stappen
[Eén aanmelding bij uw apps bieden met Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)
