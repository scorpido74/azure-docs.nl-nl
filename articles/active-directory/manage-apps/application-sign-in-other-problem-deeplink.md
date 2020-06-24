---
title: Problemen bij het aanmelden bij een toepassing met behulp van een deeplink | Microsoft Docs
description: Problemen oplossen met toegang tot een toepassing vanuit een deeplink-URL met behulp van Azure AD
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
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c68143dabd9b047a8d6cc37ccac770b7d954656
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/14/2020
ms.locfileid: "84759484"
---
# <a name="problems-signing-in-to-an-application-using-a-deeplink"></a>Problemen bij het aanmelden bij een toepassing met behulp van een deeplink

Het toegangs venster is een portal op Internet waarmee een gebruiker met een werk-of school account in Azure Active Directory (Azure AD) Cloud toepassingen kan weer geven en starten waartoe de Azure AD-beheerder hen toegang heeft verleend. 

Deze toepassingen worden geconfigureerd namens de gebruiker in de Azure AD-Portal. De toepassing moet op de juiste wijze zijn geconfigureerd en toegewezen aan de gebruiker of een groep waarvan de gebruiker lid is om de toepassing te zien in het toegangs venster.

Diep gaande koppelingen of Url's voor gebruikers toegang zijn koppelingen die uw gebruikers kunnen gebruiken om rechtstreeks vanuit hun browser-URL-balken toegang te krijgen tot hun wacht woord-SSO-toepassingen. Als u naar deze koppeling navigeert, worden gebruikers automatisch aangemeld bij de toepassing zonder eerst naar het toegangs paneel te hoeven gaan. Dit is dezelfde koppeling die gebruikers gebruiken om toegang te krijgen tot deze toepassingen vanuit het start programma voor Office 365-toepassingen.

## <a name="general-issues-to-check-first"></a>Algemene problemen om eerst te controleren

-   Zorg ervoor dat u een **browser** gebruikt die voldoet aan de minimale vereisten voor het toegangs venster.

-   Zorg ervoor dat de browser van de gebruiker de URL van de toepassing heeft toegevoegd aan de **vertrouwde sites**.

-   Controleer of de toepassing correct is **geconfigureerd** .

-   Zorg ervoor dat het gebruikers account is **ingeschakeld** voor aanmeldingen.

-   Controleer of het account van de gebruiker **niet is vergrendeld.**

-   Zorg ervoor dat het wacht woord van de gebruiker **niet is verlopen of is verg eten.**

-   Zorg ervoor dat **multi-factor Authentication** de gebruikers toegang niet blokkeert.

-   Zorg ervoor dat het beleid voor **voorwaardelijke toegang** of het beleid voor **identiteits beveiliging** de gebruikers toegang niet blokkeert.

-   Zorg ervoor dat de **contact gegevens** van de verificatie van een gebruiker up-to-date zijn om multi-factor Authentication of beleid voor voorwaardelijke toegang te kunnen afdwingen.

-   Probeer ook de cookies van uw browser te wissen en opnieuw aan te melden.

## <a name="checking-the-deeplink"></a>De Deeplink controleren

Voer de volgende stappen uit om te controleren of u de juiste Deeplink hebt:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

7. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

8. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

9. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

10. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

    * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

11. Selecteer de toepassing waarvoor u de Deeplink wilt controleren.

12. Zoek het label **gebruikers toegangs-URL**. Uw Deeplink moet overeenkomen met deze URL.

## <a name="how-to-install-the-access-panel-browser-extension"></a>De browser uitbreiding van het toegangs venster installeren

Voer de volgende stappen uit om de browser uitbreiding van het toegangs venster te installeren:

1.  Open het [toegangs venster](https://myapps.microsoft.com) in een van de ondersteunde browsers en meld u aan als een **gebruiker** in uw Azure AD.

2.  Klik op een **wacht woord-SSO-toepassing** in het toegangs venster.

3.  Selecteer **nu installeren**bij de vraag of u de software wilt installeren.

4.  Op basis van uw browser wordt u omgeleid naar de download koppeling. **Voeg** de uitbrei ding toe aan uw browser.

5.  Als uw browser vraagt, selecteert u de extensie **inschakelen** of **toestaan** .

6.  Nadat de installatie is voltooid, start u de browser sessie **opnieuw** .

7.  Meld u aan bij het toegangs venster en controleer of u uw wacht woord-SSO-toepassingen kunt **starten** .

U kunt de uitbrei ding voor Chrome en Firefox ook downloaden via deze rechtstreekse koppelingen:

-   [Uitbrei ding Chrome toegangs paneel](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensie voor het toegangs paneel van Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="how-to-configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Eenmalige aanmelding met een wacht woord configureren voor een toepassing in de Azure AD-galerie

Als u een toepassing wilt configureren vanuit de Azure AD-galerie, moet u het volgende doen:

-   [Een toepassing toevoegen vanuit de Azure AD-galerie](#add-an-application-from-the-azure-ad-gallery)

-   [De toepassing configureren voor eenmalige aanmelding met een wacht woord](#configure-the-application-for-password-single-sign-on)

### <a name="add-an-application-from-the-azure-ad-gallery"></a>Een toepassing toevoegen vanuit de Azure AD-galerie

Voer de volgende stappen uit om een toepassing toe te voegen vanuit de Azure AD-galerie:

1.  Open de [Azure Portal](https://portal.azure.com) en meld u aan als **globale beheerder** of **co-** beheerder.

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5.  Klik op de knop **toevoegen** in de rechter bovenhoek van het deel venster **bedrijfs toepassingen** .

6.  Typ de naam van de toepassing in het tekstvak **Geef een naam** op in de sectie **toevoegen vanuit de galerie** .

7.  Selecteer de toepassing die u wilt configureren voor eenmalige aanmelding.

8.  Voordat u de toepassing toevoegt, kunt u de naam ervan wijzigen in het tekstvak **naam** .

9.  Klik op **toevoegen**om de toepassing toe te voegen.

Na een korte periode kunt u het configuratie venster van de toepassing bekijken.

### <a name="configure-the-application-for-password-single-sign-on"></a>De toepassing configureren voor eenmalige aanmelding met een wacht woord

Voer de volgende stappen uit om eenmalige aanmelding voor een toepassing te configureren:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6. Selecteer de toepassing waarvoor u eenmalige aanmelding wilt configureren.

7. Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

8. Selecteer de **aanmeldings modus op basis van wacht woord.**

9. [Gebruikers toewijzen aan de toepassing](#how-to-assign-a-user-to-an-application-directly).

10. Daarnaast kunt u ook referenties namens de gebruiker opgeven door de rijen van de gebruikers te selecteren en op **referenties bijwerken** te klikken en de gebruikers naam en het wacht woord namens de gebruikers in te voeren. Anders wordt gebruikers gevraagd de referenties zelf in te voeren bij het starten.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Eenmalige aanmelding met een wacht woord configureren voor een toepassing buiten de galerie

Als u een toepassing wilt configureren vanuit de Azure AD-galerie, moet u het volgende doen:

-   [Een niet-galerie toepassing toevoegen](#add-a-non-gallery-application)

-   [De toepassing configureren voor eenmalige aanmelding met een wacht woord](#configure-the-application-for-password-single-sign-on)

### <a name="add-a-non-gallery-application"></a>Een niet-galerie toepassing toevoegen

Voer de volgende stappen uit om een toepassing toe te voegen vanuit de Azure AD-galerie:

1.  Open de [Azure Portal](https://portal.azure.com) en meld u aan als **globale beheerder** of **co-** beheerder.

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5.  Klik op de knop **toevoegen** in de rechter bovenhoek van het deel venster **bedrijfs toepassingen** .

6.  Klik op **toepassing voor niet-galerie.**

7.  Voer de naam van uw toepassing in het tekstvak **naam** in. Selecteer **toevoegen.**

Na een korte periode kunt u het configuratie venster van de toepassing bekijken.

### <a name="configure-the-application-for-password-single-sign-on"></a>De toepassing configureren voor eenmalige aanmelding met een wacht woord

Voer de volgende stappen uit om eenmalige aanmelding voor een toepassing te configureren:

1.  Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5.  Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

    1.  Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6.  Selecteer de toepassing waarvoor u eenmalige aanmelding wilt configureren.

7.  Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

8.  Selecteer de **aanmeldings modus op basis van wacht woord.**

9.  Voer de **aanmeldings-URL**in, de URL waar gebruikers hun gebruikers naam en wacht woord invoeren om zich aan te melden. Zorg ervoor dat de aanmeldings velden zichtbaar zijn op de URL.

10. Gebruikers toewijzen aan de toepassing.

11. Daarnaast kunt u ook referenties namens de gebruiker opgeven door de rijen van de gebruikers te selecteren en op **referenties bijwerken** te klikken en de gebruikers naam en het wacht woord namens de gebruikers in te voeren. Anders wordt gebruikers gevraagd de referenties zelf in te voeren bij het starten.

## <a name="how-to-assign-a-user-to-an-application-directly"></a>Direct toewijzen van een gebruiker aan een toepassing

Voer de volgende stappen uit om een of meer gebruikers rechtstreeks toe te wijzen aan een toepassing:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6. Selecteer in de lijst de toepassing waaraan u een gebruiker wilt toewijzen.

7. Zodra de toepassing is geladen, klikt u op **gebruikers en groepen** in het navigatie menu aan de linkerkant van de toepassing.

8. Klik op de knop **toevoegen** boven aan de lijst **gebruikers en groepen** om het deel venster **toewijzing toevoegen** te openen.

9. Klik op de selector **gebruikers en groepen** in het deel venster **toewijzing toevoegen** .

10. Typ de **volledige naam** of het **e-mail adres** van de gebruiker die u wilt toewijzen in het zoekvak **zoeken op naam of e-mail adres** .

11. Beweeg de muis aanwijzer over de **gebruiker** in de lijst om een **selectie vakje**weer te geven. Als u uw gebruiker aan de **geselecteerde** lijst wilt toevoegen, klikt u op het selectie vakje naast het profiel foto of logo van de gebruiker.

12. **Optioneel:** Als u **meer dan één gebruiker wilt toevoegen**, typt u een andere **volledige naam** of een ander **e-mail adres** in het zoekvak **zoeken op naam of e-mail adres** en klikt u op het selectie vakje om deze gebruiker aan de **geselecteerde** lijst toe te voegen.

13. Wanneer u klaar bent met het selecteren van gebruikers, klikt u op de knop **selecteren** om ze toe te voegen aan de lijst met gebruikers en groepen die aan de toepassing moeten worden toegewezen.

14. **Optioneel:** Klik op het selectie vakje **rol** selecteren in het deel venster **toewijzing toevoegen** om een rol te selecteren die u wilt toewijzen aan de gebruikers die u hebt geselecteerd.

15. Klik op de knop **toewijzen** om de toepassing aan de geselecteerde gebruikers toe te wijzen.

Na een korte periode kunnen de gebruikers die u hebt geselecteerd deze toepassingen in het toegangs venster starten.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Als deze stappen voor probleem oplossing niet het probleem oplossen. 

Open een ondersteunings ticket met de volgende informatie, indien beschikbaar:

-   ID correlatie fout

-   UPN (e-mail adres van gebruiker)

-   TenantID

-   Browsertype

-   Tijd zone en tijd/tijds duur tijdens fout

-   Fiddler traceringen

## <a name="next-steps"></a>Volgende stappen
[Eenmalige aanmelding bieden voor uw apps met toepassings proxy](application-proxy-configure-single-sign-on-with-kcd.md)
