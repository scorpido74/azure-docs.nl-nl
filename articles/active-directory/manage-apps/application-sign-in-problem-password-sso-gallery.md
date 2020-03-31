---
title: Probleem met aanmelden bij Azure AD-galerie-app geconfigureerd voor SSO | Microsoft Documenten
description: Problemen oplossen met een Azure AD Gallery-toepassing die is geconfigureerd voor eenmalige aanmelding met wachtwoord.
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
ms.openlocfilehash: e9fd17d9e066be6a1abff5165436a09b8921184e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "68381310"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>Aanmeldingsproblemen met een Azure AD-galerie-app die is geconfigureerd voor SSO

Access Panel is een webportal. Hiermee kunnen gebruikers die Azure Active Directory -werk of schoolaccounts hebben, toegang krijgen tot cloudgebaseerde apps waarvoor ze machtigingen hebben. Gebruikers met Azure AD-edities kunnen ook gebruikmaken van mogelijkheden voor selfservicegroepen en app-beheer via het Access Panel.

Het toegangspaneel staat los van de Azure-portal. Gebruikers hebben geen Azure-abonnement nodig om access panel te gebruiken.

Als u een op eenwachtwoord gebaseerde single sign-on (SSO) wilt gebruiken in het Access Panel, moet de extensie Toegangspaneel in uw browser worden geïnstalleerd. De extensie wordt automatisch gedownload wanneer u een app selecteert die is geconfigureerd voor sso op basis van wachtwoorden.

## <a name="browser-requirements-for-access-panel"></a>Browservereisten voor toegangspaneel

Voor het Access Panel is een browser vereist die JavaScript ondersteunt en CSS heeft ingeschakeld.

De volgende browsers ondersteunen sso op basis van wachtwoorden:

- Internet Explorer 8, 9, 10 en 11 op Windows 7 of hoger

- Chrome op Windows 7 of hoger of op MacOS X of hoger

- Firefox 26.0 of hoger op Windows XP SP2 of hoger of hoger of op Mac OS X 10.6 of hoger

>[!NOTE]
>De op wachtwoorden gebaseerde SSO-extensie wordt beschikbaar voor Microsoft Edge in Windows 10 wanneer ondersteuning voor browserextensies werd toegevoegd aan Microsoft Edge.

## <a name="install-the-access-panel-browser-extension"></a>De extensie Access Panel Browser installeren

Volg deze stappen:

1. Open [het Access-paneel](https://myapps.microsoft.com) in een ondersteunde browser en meld u aan als gebruiker in Azure AD.

2. Selecteer een app met wachtwoord-SSO in het Access-deelvenster.

3. Wanneer u wordt gevraagd, selecteert u **Nu installeren**.

4. Je wordt doorverwezen naar een downloadlink op basis van je browser. Selecteer **Toevoegen** om de browserextensie te installeren.

5. Als u wordt gevraagd, selecteert u **Inschakelen** of **Toestaan**.

6. Start na de installatie de browser opnieuw op.

7.  Meld u aan bij het Access Panel en kijk of u uw apps met wachtwoord-SSO starten.

U de extensies voor Chrome en Firefox ook rechtstreeks downloaden via deze links:

-   [Chrome Access Panel-extensie](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Firefox Access Panel-extensie](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Een groepsbeleid instellen voor Internet Explorer

U een groepsbeleid instellen waarmee u op afstand de access panel-extensie voor Internet Explorer op de machines van uw gebruikers installeren.

Dit zijn de voorwaarden:

-   [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) moet zijn ingesteld en de machines van uw gebruikers moeten worden samengevoegd met uw domein.

-   U hebt toestemming 'Instellingen bewerken' om het groepsbeleidsobject (GPO) te bewerken. Standaard hebben leden van de volgende beveiligingsgroepen deze toestemming: domeinbeheerders, bedrijfsbeheerders en eigenaren van groepsbeleidseigenaren. [Meer informatie](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Zie De extensie Access Panel voor [Internet Explorer implementeren met groepsbeleid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy)om het groepsbeleid te configureren en te implementeren in gebruikers.

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>Toegangspaneel oplossen in Internet Explorer

Zie [Problemen met de extensie Access Panel voor Internet Explorer voor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting)het configureren van een diagnosehulpmiddel en instructies voor het configureren van de extensie .

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Wachtwoord SSO configureren voor een Azure AD-galerie-app

Als u een app wilt configureren vanuit de Azure AD-galerie, moet u de volgende dingen doen:

-   De app toevoegen vanuit de Azure AD-galerie
-   [De app configureren voor eenmalige aanmelding voor wachtwoorden](#configure-the-app-for-password-sso)
-   [Gebruikers aan de app toewijzen](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>De app toevoegen vanuit de Azure AD-galerie

Volg deze stappen:

1. Open de [Azure-portal](https://portal.azure.com) en meld u aan als globale beheerder of co-beheerder.

2. Selecteer **Alle services** boven aan het navigatiedeelvenster aan de linkerkant om de Azure AD-extensie te openen.

3. Typ **Azure Active Directory** in het zoekvak van het filter en selecteer vervolgens Azure Active **Directory**.

4. Selecteer **Bedrijfstoepassingen** in het Azure AD-navigatiedeelvenster.

5. Selecteer **Toevoegen** in de rechterbovenhoek van het deelvenster **Ondernemingstoepassingen.**

6. Typ **in** de sectie Toevoegen in de galerie de naam van de app in het vak Een **naam invoeren.**

7. Selecteer de app die u voor SSO wilt configureren.

8. *Optioneel:* Voordat u de app toevoegt, u de naam wijzigen in het vak **Naam.**

9. Klik op **Toevoegen** om de app toe te voegen.

   Na een korte vertraging u het configuratievenster van de app zien.

### <a name="configure-the-app-for-password-sso"></a>De app configureren voor wachtwoord SSO

Volg deze stappen:

1. Open de [Azure-portal](https://portal.azure.com/) en meld u aan als globale beheerder of co-beheerder.

2. Selecteer **Alle services** boven aan het navigatiedeelvenster aan de linkerkant om de Azure AD-extensie te openen.

3. Typ **Azure Active Directory** in het zoekvak van het filter en selecteer vervolgens Azure Active **Directory**.

4. Selecteer **Bedrijfstoepassingen** in het Azure AD-navigatiedeelvenster.

5. Selecteer **Alle toepassingen** om een lijst met uw apps weer te geven.

   > [!NOTE]
   > Als u de gewenste app niet ziet, gebruikt u het **besturingselement Filter** boven aan de **lijst Alle toepassingen**. Stel de optie **Weergeven** in op 'Alle toepassingen'.

6. Selecteer de app die u voor SSO wilt configureren.

7. Nadat de app is geladen, selecteert u **Eén aanmelding** in het deelvenster aan de linkerkant van de app.

8. Selecteer **de aanmeldingsmodus op basis van wachtwoorden.**

9. Gebruikers toewijzen aan de app.

10. U ook referenties voor gebruikers verstrekken. (Anders wordt gebruikers gevraagd om referenties in te voeren bij het opstarten van apps.) Selecteer hiervoor de rijen van de gebruikers. Selecteer vervolgens **Referenties bijwerken** en voer hun gebruikersnamen en wachtwoorden in.

### <a name="assign-users-to-the-app"></a>Gebruikers aan de app toewijzen

Voer de volgende stappen uit om gebruikers rechtstreeks aan een app toe te wijzen:

1. Open de [Azure-portal](https://portal.azure.com/) en meld u aan als globale beheerder.

2. Selecteer **Alle services** in de navigatiepijn aan de linkerkant om de Azure AD-extensie te openen.

3. Typ **Azure Active Directory** in het zoekvak van het filter en selecteer vervolgens Azure Active **Directory**.

4. Selecteer **Bedrijfstoepassingen** in het Azure AD-navigatiedeelvenster.

5. Selecteer **Alle toepassingen** om een lijst met uw toepassingen weer te geven.

   > [!NOTE]
   > Als u de gewenste app niet ziet, gebruikt u het **besturingselement Filter** boven aan de **lijst Alle toepassingen**. Stel de optie **Weergeven** in op 'Alle toepassingen'.

6. Selecteer in de lijst de app waaraan u een gebruiker wilt toewijzen.

7. Nadat de toepassing is geladen, selecteert u **Gebruikers en groepen** in het navigatiedeelvenster van de app aan de linkerkant.

8. Selecteer **Toevoegen** boven aan de lijst **Gebruikers en groepen** om het deelvenster Toewijzing **toevoegen** te openen.

9. Selecteer **Gebruikers en groepen** in het deelvenster Toewijzing **toevoegen.**

10. Typ in het vak **Zoeken op naam of e-mailadres** de volledige naam of het e-mailadres van de gebruiker die u wilt toewijzen.

11. Plaats de plaats in de boventoon van de gebruiker in de lijst. Schakel het selectievakje naast de profielfoto of het logo van de gebruiker in om die gebruiker toe te voegen aan de **lijst Geselecteerde.**

12. *Optioneel:* Als u een andere gebruiker wilt toevoegen, typt u een andere naam of e-mailadres in het vak **Zoeken op naam of e-mailadres** en schakelt u het selectievakje in om die gebruiker toe te voegen aan de lijst **Geselecteerd.**

13. Wanneer u klaar bent met het selecteren van gebruikers, klikt u op **Selecteren** om ze toe te voegen aan de lijst met gebruikers en groepen die aan de app zijn toegewezen.

14. *Optioneel:* Klik **op Rol selecteren** in het deelvenster Toewijzing **toevoegen** om een rol te selecteren die u wilt toewijzen aan de gebruikers die u hebt geselecteerd.

15. Selecteer **Toewijzen** om de app toe te wijzen aan de geselecteerde gebruikers.

    Na een korte vertraging hebben de gebruikers toegang tot deze apps vanuit het Access Panel.

## <a name="request-support"></a>Ondersteuning aanvragen 
Als u een foutbericht ontvangt wanneer u SSO instelt en gebruikers toewijst, opent u een ondersteuningsticket. Neem zoveel mogelijk van de volgende informatie op:

-   Correlatiefout-id
-   UPN (e-mailadres van de gebruiker)
-   TenantID
-   Browsertype
-   Tijdzone en tijd-/tijdsframe toen de fout is opgetreden
-   Fiddler sporen

## <a name="next-steps"></a>Volgende stappen
[Eén aanmelding bij uw apps bieden met Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)
