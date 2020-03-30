---
title: Op eenpersoonsaanmelding op basis van wachtwoorden (SSO) in het toegangspaneel | Microsoft Documenten
description: Bespreekt probleemgebieden die richtlijnen bieden om problemen op te lossen die verband houden met het aanmelden bij Azure AD Gallery-toepassingen die zijn geconfigureerd voor eenmalige aanmelding met wachtwoord.
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
ms.openlocfilehash: 9ca192c28757df189e531aee0ba2d8da288ba7e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68381235"
---
# <a name="problems-signing-in-to-an-azure-ad-gallery-application-configured-for-password-single-sign-on"></a>Problemen met het aanmelden bij een Azure AD Gallery-toepassing die is geconfigureerd voor eenmalige aanmelding met wachtwoord

Het Access-paneel is een webportal waarmee een gebruiker met een werk- of schoolaccount in Azure Active Directory (Azure AD) cloudtoepassingen kan weergeven en starten waartoe de Azure AD-beheerder hem toegang heeft verleend. Een gebruiker met Azure AD-edities kan ook gebruikmaken van mogelijkheden voor selfservicegroepen en app-beheer via het Access-paneel. Het Access-paneel staat los van de Azure-portal en vereist niet dat gebruikers een Azure-abonnement hebben.

Als u een op eenwachtwoord gebaseerde aanmelding (SSO) wilt gebruiken in het toegangspaneel, moet de extensie Toegangspaneel in de browser van de gebruiker worden geïnstalleerd. Deze extensie wordt automatisch gedownload wanneer een gebruiker een toepassing selecteert die is geconfigureerd voor sso op basis van wachtwoorden.

## <a name="meeting-browser-requirements-for-the-access-panel"></a>Voldoen aan browservereisten voor het Toegangspaneel

Het Access Panel vereist een browser die JavaScript ondersteunt en CSS heeft ingeschakeld. Als u een op eenwachtwoord gebaseerde aanmelding (SSO) wilt gebruiken in het toegangspaneel, moet de extensie Toegangspaneel in de browser van de gebruiker worden geïnstalleerd. Deze extensie wordt automatisch gedownload wanneer een gebruiker een toepassing selecteert die is geconfigureerd voor sso op basis van wachtwoorden.

Voor sso op basis van wachtwoorden kunnen de browsers van de eindgebruiker zijn:

-   Internet Explorer 8, 9, 10, 11 - op Windows 7 of hoger

-   Chrome - op Windows 7 of hoger, en op MacOS X of hoger

-   Firefox 26.0 of hoger , op Windows XP SP2 of hoger, en op Mac OS X 10.6 of hoger

>[!NOTE]
>De op wachtwoorden gebaseerde SSO-extensie wordt beschikbaar voor Microsoft Edge in Windows 10 wanneer browserextensies worden ondersteund voor Microsoft Edge.
>
>

## <a name="how-to-install-the-access-panel-browser-extension"></a>De extensie Access Panel Browser installeren

Voer de onderstaande stappen uit om de extensie Access Panel Browser te installeren:

1.  Open het [Access-paneel](https://myapps.microsoft.com) in een van de ondersteunde browsers en meld u aan als **gebruiker** in uw Azure AD.

2.  klik op een **Wachtwoord-SSO-toepassing** in het Access-paneel.

3.  Selecteer **Nu installeren**in de prompt waarin u vraagt de software te installeren .

4.  Op basis van uw browser wordt u doorverwezen naar de downloadlink. **Voeg** de extensie toe aan uw browser.

5.  Als uw browser daarom vraagt, selecteert u de extensie **inschakelen** of **toestaan.**

6.  Zodra u deze **browserhebt geïnstalleerd, start u** de browsersessie opnieuw.

7.  Meld u aan bij het Toegangspaneel en kijk of u uw wachtwoord-SSO-toepassingen **starten**

U ook downloaden van de extensie voor Chrome en Firefox van de directe links hieronder:

-   [Chrome Access Panel Extension](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox Access Panel Extension](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="setting-up-a-group-policy-for-internet-explorer"></a>Een groepsbeleid instellen voor Internet Explorer

U een groepsbeleid instellen waarmee u de extensie Access Panel voor Internet Explorer op afstand installeren op de machines van uw gebruikers.

De voorwaarden zijn onder meer:

-   U hebt [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)ingesteld en u hebt de machines van uw gebruikers bij uw domein gevoegd.

-   U moet de machtiging 'Instellingen bewerken' hebben om het groepsbeleidsobject (GPO) te bewerken. Standaard hebben leden van de volgende beveiligingsgroepen deze toestemming: domeinbeheerders, bedrijfsbeheerders en eigenaren van groepsbeleidseigenaren. [Meer informatie](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Volg de zelfstudie [Hoe implementeer je de extensie van het toegangspaneel voor Internet Explorer met groepsbeleid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy) voor stapsgewijze instructies over het configureren van het groepsbeleid en het implementeren ervan naar gebruikers.

## <a name="troubleshoot-the-access-panel-in-internet-explorer"></a>Problemen met het toegangspaneel in Internet Explorer oplossen

Volg de [handleiding Problemen met het toegangspaneel voor Internet Explorer](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting) voor toegang tot een diagnosetool en stapsgewijze instructies voor het configureren van de extensie voor IE.

## <a name="how-to-configure-password-single-sign-on-for-a-non-gallery-application"></a>Eenmalige aanmelding voor een niet-galerietoepassing configureren voor een aanvraag voor een niet-galerie

Als u een toepassing wilt configureren vanuit de Azure AD-galerie, moet u het gaat doen:

-   [Een niet-galerietoepassing toevoegen](#add-a-non-gallery-application)

-   [De toepassing voor eenmalige aanmelding voor wachtwoorden configureren](#configure-the-application-for-password-single-sign-on)

-   [Gebruikers toewijzen aan een toepassing](#assign-users-to-the-application)

### <a name="add-a-non-gallery-application"></a>Een niet-galerietoepassing toevoegen

Voer de onderstaande stappen uit om een toepassing toe te voegen vanuit de Azure AD-galerie:

1.  De [Azure-portal openen](https://portal.azure.com) en u aanmelden als **globale beheerder** of **medebeheerder**

2.  Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3.  Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4.  klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5.  klik op de knop **Toevoegen** in de rechterbovenhoek in het deelvenster **Ondernemingstoepassingen.**

6.  klik **op Niet-galerietoepassing.**

7.  Voer de naam van uw toepassing in het tekstvak **Naam** in. Selecteer **Toevoegen.**

Na een korte periode u het configuratievenster van de toepassing zien.

### <a name="configure-the-application-for-password-single-sign-on"></a>De toepassing voor eenmalige aanmelding voor wachtwoorden configureren

Voer de onderstaande stappen uit om een malige aanmelding voor een toepassing te configureren:

1. Open de [**Azure-portal**](https://portal.azure.com/) en meld u aan als **globale beheerder** of **co-beheerder.**

2. Open de **Azure Active Directory Extension** door boven aan het linkernavigatiemenu op Alle **services** te klikken.

3. Typ **'Azure Active Directory'** in het zoekvak van het filter en selecteer het **Azure Active Directory-item.**

4. klik op **Bedrijfstoepassingen** in het navigatiemenu van Azure Active Directory met de linker.

5. klik op **Alle toepassingen** om een lijst met al uw toepassingen weer te geven.

   * Als u de gewenste toepassing hier niet ziet, gebruikt u het besturingselement **Filter** boven aan de **lijst Alle toepassingen** en stelt u de optie **Weergeven** in op **Alle toepassingen.**

6. Selecteer de toepassing die u wilt configureren als u één aanmelding wilt configureren

7. Zodra de toepassing is geladen, klikt u op de **aanmelding in één** keer in het linkernavigatiemenu van de toepassing.

8. Selecteer de modus **Aanmelding op basis van wachtwoord.**

9. Voer de **aanmeldings-URL in**. Dit is de URL waar gebruikers hun gebruikersnaam en wachtwoord invoeren om zich aan te melden. Zorg ervoor dat de aanmeldingsvelden zichtbaar zijn op de URL.

10. Gebruikers toewijzen aan de toepassing.

11. Bovendien u namens de gebruiker ook referenties opgeven door de rijen van de gebruikers te selecteren en op **Referenties bijwerken** te klikken en namens de gebruikers de gebruikersnaam en het wachtwoord in te voeren. Anders worden gebruikers gevraagd om de referenties zelf in te voeren bij de lancering.

### <a name="assign-users-to-the-application"></a>Gebruikers toewijzen aan een toepassing

Als u een of meer gebruikers rechtstreeks aan een toepassing wilt toewijzen, voert u de onderstaande stappen uit:

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

11. Plaats de **plaats van** de gebruiker in de lijst om een **selectievakje**te onthullen. Klik op het selectievakje naast de profielfoto of het logo van de gebruiker om uw gebruiker toe te voegen aan de **lijst Geselecteerde.**

12. **Optioneel:** Als u meer dan één gebruiker wilt **toevoegen,** typt u een andere **volledige naam** of **e-mailadres** in het zoekvak **Zoeken op naam of e-mailadres** en klikt u op het selectievakje om deze gebruiker toe te voegen aan de **lijst Geselecteerde.**

13. Wanneer u klaar bent met het selecteren van gebruikers, klikt u op de knop **Selecteren** om deze toe te voegen aan de lijst met gebruikers en groepen die aan de toepassing moeten worden toegewezen.

14. **Optioneel:** klik op de rolkiezer **selecteren** in het deelvenster **Toewijzing toevoegen** om een rol te selecteren die u wilt toewijzen aan de gebruikers die u hebt geselecteerd.

15. Klik **op** de knop Toewijzen om de toepassing toe te wijzen aan de geselecteerde gebruikers.

Na een korte periode kunnen de geselecteerde gebruikers deze toepassingen starten in het Access Panel.

## <a name="if-these-troubleshooting-steps-do-not-the-resolve-the-issue"></a>Als deze stappen voor het oplossen van problemen het probleem niet oplossen

open een support ticket met de volgende informatie indien beschikbaar:

-   Correlatiefout-id

-   UPN (e-mailadres van de gebruiker)

-   TenantID

-   Browsertype

-   Tijdzone en tijd/tijdsbestek tijdens fout treedt op

-   Fiddler sporen

## <a name="next-steps"></a>Volgende stappen
[Eén aanmelding bij uw apps bieden met Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)

