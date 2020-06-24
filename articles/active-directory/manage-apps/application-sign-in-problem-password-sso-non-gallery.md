---
title: Eenmalige aanmelding op basis van wacht woorden (SSO) in het toegangs venster | Microsoft Docs
description: Hierin worden probleem gebieden beschreven die hulp bieden bij het oplossen van problemen met het aanmelden bij Azure AD Gallery-toepassingen die zijn geconfigureerd voor eenmalige aanmelding met een wacht woord.
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
ms.openlocfilehash: bd4a645038dfb49e7c79995b90b159bdc07bea4f
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763768"
---
# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-password-single-sign-on"></a>Problemen bij het aanmelden bij een Azure AD Gallery-toepassing die is geconfigureerd voor eenmalige aanmelding met een wacht woord

Het toegangs venster is een portal op Internet waarmee een gebruiker met een werk-of school account in Azure Active Directory (Azure AD) Cloud toepassingen kan weer geven en starten waartoe de Azure AD-beheerder hen toegang heeft verleend. Een gebruiker met Azure AD-edities kan ook selfservice-en app-beheer mogelijkheden gebruiken via het toegangs venster. Het toegangs venster is gescheiden van de Azure Portal en vereist niet dat gebruikers een Azure-abonnement hebben.

Als u eenmalige aanmelding (SSO) op basis van wacht woorden in het toegangs venster wilt gebruiken, moet de uitbrei ding van het toegangs paneel worden geïnstalleerd in de browser van de gebruiker. Deze uitbrei ding wordt automatisch gedownload wanneer een gebruiker een toepassing selecteert die is geconfigureerd voor SSO op basis van een wacht woord.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Browser vereisten voor de vergadering voor het toegangs venster

Het toegangs venster vereist een browser die Java script ondersteunt en waarvoor CSS is ingeschakeld. Als u eenmalige aanmelding (SSO) op basis van wacht woorden in het toegangs venster wilt gebruiken, moet de uitbrei ding van het toegangs paneel worden geïnstalleerd in de browser van de gebruiker. Deze uitbrei ding wordt automatisch gedownload wanneer een gebruiker een toepassing selecteert die is geconfigureerd voor SSO op basis van een wacht woord.

Voor SSO op basis van een wacht woord kunnen de browsers van de eind gebruiker het volgende zijn:

-   Internet Explorer 8, 9, 10, 11, op Windows 7 of hoger

-   Chrome--op Windows 7 of hoger en op MacOS X of hoger

-   Firefox 26,0 of hoger, op Windows XP SP2 of hoger, en op Mac OS X 10,6 of hoger

>[!NOTE]
>De SSO-uitbrei ding op basis van wacht woorden is beschikbaar voor micro soft Edge in Windows 10 wanneer browser uitbreidingen worden ondersteund voor micro soft Edge.
>
>

## <a name="how-to-install-the-access-panel-browser-extension"></a>De browser uitbreiding van het toegangs venster installeren

Volg de onderstaande stappen om de browser uitbreiding van het toegangs venster te installeren:

1.  Open het [toegangs venster](https://myapps.microsoft.com) in een van de ondersteunde browsers en meld u aan als een **gebruiker** in uw Azure AD.

2.  Klik op een **wacht woord-SSO-toepassing** in het toegangs venster.

3.  Selecteer **nu installeren**bij de vraag of u de software wilt installeren.

4.  Op basis van uw browser wordt u omgeleid naar de download koppeling. **Voeg** de uitbrei ding toe aan uw browser.

5.  Als uw browser vraagt, selecteert u de extensie **inschakelen** of **toestaan** .

6.  Nadat de installatie is voltooid, start u de browser sessie **opnieuw** .

7.  Meld u aan bij het toegangs venster en controleer of u uw wacht woord-SSO-toepassingen kunt **starten** .

U kunt de uitbrei ding voor Chrome en Firefox ook downloaden via de onderstaande rechtstreekse koppelingen:

-   [Uitbrei ding Chrome toegangs paneel](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensie voor het toegangs paneel van Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Instellen van groeps beleid voor Internet Explorer

U kunt een groeps beleid instellen waarmee u de uitbrei ding van het toegangs paneel voor Internet Explorer op afstand installeert op de computers van uw gebruikers.

De vereisten zijn onder andere:

-   U hebt [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)ingesteld en u hebt de computers van uw gebruikers toegevoegd aan uw domein.

-   U moet beschikken over de machtiging instellingen bewerken om het groepsbeleid object (GPO) te bewerken. Standaard hebben leden van de volgende beveiligings groepen deze machtiging: domein beheerders, ondernemings Administrators en groepsbeleid Maker eigen aren. [Meer informatie](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Volg de zelf studie [hoe u de uitbrei ding voor het toegangs paneel voor Internet Explorer kunt implementeren met behulp van Groepsbeleid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy) voor stapsgewijze instructies voor het configureren van groeps beleid en het implementeren ervan voor gebruikers.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Problemen met het toegangs paneel in Internet Explorer oplossen

Volg de richt lijnen voor [het openen van de uitbrei ding van het toegangs paneel voor Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting) voor toegang tot een diagnostisch hulp programma en stapsgewijze instructies voor het configureren van de uitbrei ding voor IE.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Eenmalige aanmelding met een wacht woord configureren voor een toepassing buiten de galerie

Als u een toepassing vanuit de Azure AD-galerie wilt configureren, moet u het volgende doen:

-   [Een niet-galerie toepassing toevoegen](#add-a-non-gallery-application)

-   [De toepassing configureren voor eenmalige aanmelding met een wacht woord](#configure-the-application-for-password-single-sign-on)

-   [Gebruikers toewijzen aan een toepassing](#assign-users-to-the-application)

### <a name="add-a-non-gallery-application"></a>Een niet-galerie toepassing toevoegen

Voer de volgende stappen uit om een toepassing toe te voegen vanuit de Azure AD-galerie:

1.  Open de [Azure Portal](https://portal.azure.com) en meld u aan als **globale beheerder** of **co-** beheerder

2.  Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3.  Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4.  Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5.  Klik op de knop **toevoegen** in de rechter bovenhoek van het deel venster **bedrijfs toepassingen** .

6.  Klik op **toepassing voor niet-galerie.**

7.  Voer de naam van uw toepassing in het tekstvak **naam** in. Selecteer **toevoegen.**

Na een korte periode kunt u het configuratie venster van de toepassing bekijken.

### <a name="configure-the-application-for-password-single-sign-on"></a>De toepassing configureren voor eenmalige aanmelding met een wacht woord

Volg de onderstaande stappen voor het configureren van eenmalige aanmelding voor een toepassing:

1. Open de [**Azure Portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **uitbrei ding Azure Active Directory** door te klikken op **alle services** boven aan het hoofd navigatie menu aan de linkerkant.

3. Typ **' Azure Active Directory**' in het vak Zoek opdracht filteren en selecteer het **Azure Active Directory** item.

4. Klik op **bedrijfs toepassingen** in het navigatie menu Azure Active Directory aan de linkerkant.

5. Klik op **alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de toepassing die u wilt weer geven hier niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen** en stelt u de optie voor het **weer geven** van **alle toepassingen in.**

6. Selecteer de toepassing waarvoor u eenmalige aanmelding wilt configureren

7. Zodra de toepassing is geladen, klikt u op de **eenmalige aanmelding** in het navigatie menu aan de linkerkant van de toepassing.

8. Selecteer de **aanmeldings modus op basis van wacht woord.**

9. Voer de **aanmeldings-URL**in. Dit is de URL waar gebruikers hun gebruikers naam en wacht woord invoeren om zich aan te melden. Zorg ervoor dat de aanmeldings velden zichtbaar zijn op de URL.

10. Gebruikers toewijzen aan de toepassing.

11. Daarnaast kunt u ook referenties namens de gebruiker opgeven door de rijen van de gebruikers te selecteren en op **referenties bijwerken** te klikken en de gebruikers naam en het wacht woord namens de gebruikers in te voeren. Anders wordt gebruikers gevraagd de referenties zelf in te voeren bij het starten.

### <a name="assign-users-to-the-application"></a>Gebruikers toewijzen aan een toepassing

Volg de onderstaande stappen om een of meer gebruikers rechtstreeks toe te wijzen aan een toepassing:

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

11. Beweeg de muis aanwijzer over de **gebruiker** in de lijst om een **selectie vakje**weer te geven. Klik op het selectie vakje naast de foto of het logo van het profiel van de gebruiker om uw gebruiker toe te voegen aan de **geselecteerde** lijst.

12. **Optioneel:** Als u **meer dan één gebruiker wilt toevoegen**, typt u een andere **volledige naam** of een ander **e-mail adres** in het zoekvak **zoeken op naam of e-mail adres** en klikt u op het selectie vakje om deze gebruiker aan de **geselecteerde** lijst toe te voegen.

13. Wanneer u klaar bent met het selecteren van gebruikers, klikt u op de knop **selecteren** om ze toe te voegen aan de lijst met gebruikers en groepen die aan de toepassing moeten worden toegewezen.

14. **Optioneel:** Klik op het selectie vakje **rol** selecteren in het deel venster **toewijzing toevoegen** om een rol te selecteren die u wilt toewijzen aan de gebruikers die u hebt geselecteerd.

15. Klik op de knop **toewijzen** om de toepassing aan de geselecteerde gebruikers toe te wijzen.

Na een korte periode kunnen de gebruikers die u hebt geselecteerd deze toepassingen in het toegangs venster starten.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Als deze stappen voor probleem oplossing niet het probleem oplossen

Open een ondersteunings ticket met de volgende informatie, indien beschikbaar:

-   ID correlatie fout

-   UPN (e-mail adres van gebruiker)

-   TenantID

-   Browsertype

-   Tijd zone en tijd/tijds duur tijdens fout

-   Fiddler traceringen

## <a name="next-steps"></a>Volgende stappen
[Eenmalige aanmelding bieden voor uw apps met toepassings proxy](application-proxy-configure-single-sign-on-with-kcd.md)

