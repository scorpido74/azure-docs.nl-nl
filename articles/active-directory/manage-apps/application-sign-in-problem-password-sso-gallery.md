---
title: Probleem bij het aanmelden bij de Azure AD Gallery-app die is geconfigureerd voor SSO | Microsoft Docs
description: Problemen oplossen met een Azure AD Gallery-toepassing die is geconfigureerd voor eenmalige aanmelding met een wacht woord.
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
ms.openlocfilehash: 6eebde3a7c6163b7faf92be193fe442cd5b74d2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84759110"
---
# <a name="sign-in-problems-with-an-azure-ad-gallery-app-configured-for-sso"></a>Problemen met aanmelden met een Azure AD Gallery-app die is geconfigureerd voor SSO

Het toegangs venster is een portal op het web. Hiermee kunnen gebruikers met Azure Active Directory (Azure AD) werk-of school accounts toegang krijgen tot Cloud-apps waarvoor ze machtigingen hebben. Gebruikers met Azure AD-edities kunnen ook gebruikmaken van selfservice groep en mogelijkheden voor app-beheer via het toegangs venster.

Het toegangs venster is gescheiden van de Azure Portal. Gebruikers hebben geen Azure-abonnement nodig voor het gebruik van het toegangs venster.

Als u eenmalige aanmelding (SSO) op basis van wacht woorden in het toegangs venster wilt gebruiken, moet de uitbrei ding van het toegangs paneel in uw browser zijn geïnstalleerd. De extensie wordt automatisch gedownload wanneer u een app selecteert die is geconfigureerd voor SSO op basis van een wacht woord.

## <a name="browser-requirements-for-access-panel"></a>Browser vereisten voor het toegangs venster

Voor het toegangs venster is een browser vereist die Java script ondersteunt en waarvoor CSS is ingeschakeld.

De volgende browsers ondersteunen SSO op basis van een wacht woord:

- Internet Explorer 8, 9, 10 en 11 op Windows 7 of hoger

- Chrome op Windows 7 of hoger of op MacOS X of hoger

- Firefox 26,0 of hoger op Windows XP SP2 of hoger of op Mac OS X 10,6 of hoger

>[!NOTE]
>De SSO-uitbrei ding op basis van wacht woorden is beschikbaar voor micro soft Edge in Windows 10 wanneer ondersteuning voor browser uitbreidingen is toegevoegd aan micro soft Edge.

## <a name="install-the-access-panel-browser-extension"></a>De browser uitbreiding van het toegangs venster installeren

Volg deze stappen:

1. Open het [toegangs venster](https://myapps.microsoft.com) in een ondersteunde browser en meld u aan als een gebruiker in azure AD.

2. Selecteer een app waarvoor een wacht woord is ingeschakeld in het toegangs venster.

3. Selecteer **nu installeren**wanneer u hierom wordt gevraagd.

4. U wordt omgeleid naar een download koppeling op basis van uw browser. Selecteer **toevoegen** om de browser uitbreiding te installeren.

5. Als u hierom wordt gevraagd, selecteert u **inschakelen** of **toestaan**.

6. Nadat de installatie is gestart, start u de browser opnieuw.

7.  Meld u aan bij het toegangs venster en controleer of u uw apps met een wacht woord-SSO-functionaliteit kunt starten.

U kunt de uitbrei dingen voor Chrome en Firefox ook rechtstreeks downloaden via de volgende koppelingen:

-   [Uitbrei ding Chrome toegangs paneel](https://chrome.google.com/webstore/detail/access-panel-extension/ggjhpefgjjfobnfoldnjipclpcfbgbhl)

-   [Extensie voor het toegangs paneel van Firefox](https://addons.mozilla.org/firefox/addon/access-panel-extension/)

## <a name="set-up-a-group-policy-for-internet-explorer"></a>Een groeps beleid instellen voor Internet Explorer

U kunt een groeps beleid instellen waarmee u de uitbrei ding van het toegangs paneel voor Internet Explorer op afstand kunt installeren op de computers van uw gebruikers.

Dit zijn de vereisten:

-   [Active Directory Domain Services](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx) moet worden ingesteld en de computers van uw gebruikers moeten lid zijn van uw domein.

-   U hebt de machtiging instellingen bewerken om het groepsbeleid object (GPO) te bewerken. Standaard hebben leden van de volgende beveiligings groepen deze machtiging: domein beheerders, ondernemings Administrators en groepsbeleid Maker eigen aren. [Meer informatie](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx).

Zie [de uitbrei ding van het toegangs paneel voor Internet Explorer implementeren met behulp van groeps beleid](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-group-policy)om het groeps beleid te configureren en te implementeren voor gebruikers.

## <a name="troubleshoot-access-panel-in-internet-explorer"></a>Problemen met het toegangs venster in Internet Explorer oplossen

Zie [problemen met de uitbrei ding van het toegangs paneel voor Internet Explorer oplossen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ie-troubleshooting)voor een diagnostisch hulp programma en instructies voor het configureren van de uitbrei ding.

## <a name="configure-password-sso-for-an-azure-ad-gallery-app"></a>Wachtwoord-SSO configureren voor een Azure AD Gallery-app

Als u een app wilt configureren vanuit de Azure AD-galerie, moet u de volgende handelingen uitvoeren:

-   De app toevoegen vanuit de Azure AD-galerie
-   [De app configureren voor eenmalige aanmelding met een wacht woord](#configure-the-app-for-password-sso)
-   [Gebruikers aan de app toewijzen](#assign-users-to-the-app)

### <a name="add-the-app-from-the-azure-ad-gallery"></a>De app toevoegen vanuit de Azure AD-galerie

Volg deze stappen:

1. Open de [Azure Portal](https://portal.azure.com) en meld u aan als globale beheerder of co-beheerder.

2. Selecteer **alle services** boven aan het navigatie deel venster aan de linkerkant om de Azure AD-extensie te openen.

3. Typ **Azure Active Directory** in het vak Zoek opdracht filteren en selecteer vervolgens **Azure Active Directory**.

4. Selecteer **bedrijfs toepassingen** in het navigatie deel venster van Azure AD.

5. Selecteer **toevoegen** in de rechter bovenhoek van het deel venster **ondernemings toepassingen** .

6. Typ in de sectie **toevoegen vanuit de galerie** de naam van de app in het vak **Geef een naam** op.

7. Selecteer de app die u wilt configureren voor eenmalige aanmelding.

8. *Optioneel:* Voordat u de app toevoegt, kunt u de naam wijzigen in het vak **naam** .

9. Klik op **Toevoegen** om de app toe te voegen.

   Na een korte vertraging kunt u het configuratie deel venster van de app bekijken.

### <a name="configure-the-app-for-password-sso"></a>De app configureren voor wacht woord-SSO

Volg deze stappen:

1. Open de [Azure Portal](https://portal.azure.com/) en meld u aan als globale beheerder of co-beheerder.

2. Selecteer **alle services** boven aan het navigatie deel venster aan de linkerkant om de Azure AD-extensie te openen.

3. Typ **Azure Active Directory** in het vak Zoek opdracht filteren en selecteer vervolgens **Azure Active Directory**.

4. Selecteer **bedrijfs toepassingen** in het navigatie deel venster van Azure AD.

5. Selecteer **alle toepassingen** om een lijst met uw apps weer te geven.

   > [!NOTE]
   > Als u de gewenste app niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen**. Stel de optie **weer geven** in op alle toepassingen.

6. Selecteer de app die u wilt configureren voor eenmalige aanmelding.

7. Nadat de app is geladen, selecteert u **eenmalige aanmelding** in het deel venster aan de linkerkant van de app.

8. Selecteer **aanmeldings modus op basis van wacht woorden** .

9. Gebruikers toewijzen aan de app.

10. U kunt ook referenties voor gebruikers opgeven. (Anders wordt gebruikers gevraagd om referenties in te voeren bij het starten van de app.) Als u dit wilt doen, selecteert u de rijen van de gebruikers. Selecteer vervolgens **referenties bijwerken** en voer de gebruikers namen en wacht woorden in.

### <a name="assign-users-to-the-app"></a>Gebruikers aan de app toewijzen

Voer de volgende stappen uit om gebruikers rechtstreeks toe te wijzen aan een app:

1. Open de [Azure Portal](https://portal.azure.com/) en meld u aan als globale beheerder.

2. Selecteer **alle services** in het navigatie scherm aan de linkerkant om de Azure AD-extensie te openen.

3. Typ **Azure Active Directory** in het vak Zoek opdracht filteren en selecteer vervolgens **Azure Active Directory**.

4. Selecteer **bedrijfs toepassingen** in het navigatie deel venster van Azure AD.

5. Selecteer **alle toepassingen** om een lijst met uw toepassingen weer te geven.

   > [!NOTE]
   > Als u de gewenste app niet ziet, gebruikt u het **filter** besturings element boven aan de **lijst alle toepassingen**. Stel de optie **weer geven** in op alle toepassingen.

6. Selecteer in de lijst de app waaraan u een gebruiker wilt toewijzen.

7. Nadat de toepassing is geladen, selecteert u **gebruikers en groepen** in het navigatie deel venster van de app aan de linkerkant.

8. Selecteer **toevoegen** boven aan de lijst **gebruikers en groepen** om het deel venster **toewijzing toevoegen** te openen.

9. Selecteer **gebruikers en groepen** in het deel venster **toewijzing toevoegen** .

10. Typ in het vak **zoeken op naam of e-mail adres** de volledige naam of het e-mail adres van de gebruiker die u wilt toewijzen.

11. Beweeg de muis aanwijzer over de gebruiker in de lijst. Schakel het selectie vakje in naast de foto of het logo van het profiel van de gebruiker om die gebruiker toe te voegen aan de **geselecteerde** lijst.

12. *Optioneel:* Als u nog een gebruiker wilt toevoegen, typt u een andere naam of een e-mail adres in het vak **zoeken op naam of e-mail adres** en schakelt u het selectie vakje in om die gebruiker toe te voegen aan de **geselecteerde** lijst.

13. Wanneer u klaar bent met het selecteren van gebruikers, klikt u op **selecteren** om ze toe te voegen aan de lijst met gebruikers en groepen die zijn toegewezen aan de app.

14. *Optioneel:* Klik op **rol selecteren** in het deel venster **toewijzing toevoegen** om een rol te selecteren die u wilt toewijzen aan de gebruikers die u hebt geselecteerd.

15. Selecteer **toewijzen** om de app toe te wijzen aan de geselecteerde gebruikers.

    Na een korte vertraging kunnen gebruikers toegang krijgen tot deze apps vanuit het toegangs venster.

## <a name="request-support"></a>Ondersteuning aanvragen 
Als er een fout bericht wordt weer gegeven wanneer u SSO instelt en gebruikers toewijst, opent u een ondersteunings ticket. Voeg zo veel mogelijk van de volgende gegevens toe:

-   ID correlatie fout
-   UPN (e-mail adres van gebruiker)
-   TenantID
-   Browsertype
-   Tijd zone en tijd/tijd-frame wanneer de fout is opgetreden
-   Fiddler traceringen

## <a name="next-steps"></a>Volgende stappen
[Eenmalige aanmelding bieden voor uw apps met toepassings proxy](application-proxy-configure-single-sign-on-with-kcd.md)
