---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met PureCloud by Genesys | Microsoft Documenten'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en PureCloud by Genesys.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16a46db-5de2-4681-b7e0-94c670e3e54e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dac8e0f2e10906f2cc56ecf86e0cc70947cb7e85
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78897770"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met PureCloud by Genesys

In deze zelfstudie leert u hoe u PureCloud by Genesys integreren met Azure Active Directory (Azure AD). Nadat je dat hebt gedaan, kun je:

* Gebruik Azure AD om te bepalen welke gebruikers toegang hebben tot PureCloud by Genesys.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij PureCloud door Genesys met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen account hebt kun je je [gratis](https://azure.microsoft.com/free/)registreren.
* Een PureCloud van Genesys single sign-on (SSO)-enabled abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* PureCloud by Genesys ondersteunt **SP en IDP**–geïnitieerd SSO.

> [!NOTE]
> Omdat de id voor deze toepassing een waarde met vaste tekenreeksen is, kan slechts één instantie in één tenant worden geconfigureerd.

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>PureCloud by Genesys toevoegen vanuit de galerie

Als u de integratie van PureCloud by Genesys in Azure AD wilt configureren, moet u PureCloud by Genesys uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps. Voer de volgende stappen uit om dit te doen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of met een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Ga naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **PureCloud by Genesys** in het zoekvak in de sectie **Toevoegen vanuit de galerie.**
1. Selecteer **PureCloud by Genesys** in het resultatenpaneel en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purecloud-by-genesys"></a>Azure AD-singlesign-on voor PureCloud by Genesys configureren en testen

Azure AD SSO configureren en testen met PureCloud van Genesys met behulp van een testgebruiker met de naam **B.Simon**. Als SSO wil werken, moet u een koppeling smaken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in PureCloud by Genesys.

Als u Azure AD SSO wilt configureren en testen met PureCloud by Genesys, voert u de volgende bouwstenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om Azure AD-enkele aanmelding met B.Simon te testen.
    1. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer PureCloud by Genesys SSO](#configure-purecloud-by-genesys-sso)** om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Maak een PureCloud by Genesys-testgebruiker](#create-purecloud-by-genesys-test-user)** om een tegenhanger van B.Simon te hebben in PureCloud van Genesys die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Voer de volgende stappen uit om Azure AD SSO in te schakelen in de Azure-portal:

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **PureCloud by Genesys-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding.**
1. Selecteer SAML op de pagina **Een malige aanmeldingsmethode** selecteren . **SAML**
1. Selecteer op de pagina **Eén aanmelding instellen met SAML** het penpictogram voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden in als u de toepassing in **de IDP-modus**wilt configureren:

    a. Voer in het vak **Id** een URL in die overeenkomt met uw regio:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. Voer in het vak **URL beantwoorden** een URL in die overeenkomt met uw regio:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml`|

1. Selecteer **Extra URL's instellen** en neem de volgende stap als u de toepassing wilt configureren in de gestarte **SP-modus:**

    Voer in het vak **AANmeldings-URL** een URL in die overeenkomt met uw regio:
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

1. In de PureCloud by Genesys-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven:

    ![installatiekopie](common/default-attributes.png)

1. Daarnaast verwacht purecloud by Genesys applicatie dat er nog een paar attributen worden teruggegeven in de SAML-respons, zoals in de volgende tabel wordt weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze naar behoefte bekijken.

    | Name | Bronkenmerk|
    | ---------------| --------------- |
    | Email | user.userprincipalname |
    | OrganizationName | `Your organization name` |

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **PureCloud by Genesys instellen** de juiste URL (of URL's) op basis van uw vereisten.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker met de naam B.Simon in de Azure-portal:

1. Selecteer in het linkerdeelvenster van de Azure-portal **Azure Active Directory**, selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld Gebruikersnaam de gebruikersnaam in username@companydomain.extensionde volgende indeling in: . Bijvoorbeeld: `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u B.Simon in om Azure single sign-on te gebruiken door toegang te verlenen tot PureCloud door Genesys.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **PureCloud by Genesys** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en kies de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en kiest u de knop **Selecteren** onder aan het scherm.
1. Selecteer in het dialoogvenster **Toewijzing toevoegen** de knop **Toewijzen.**

## <a name="configure-purecloud-by-genesys-sso"></a>PureCloud configureren door Genesys SSO

1. Meld je in een ander browservenster aan bij PureCloud van Genesys als beheerder.

1. Selecteer **Beheerder** bovenaan en ga vervolgens naar **Eén aanmelding** onder **Integraties**.

    ![Eenmalige aanmelding configureren](./media/purecloud-by-genesys-tutorial/configure01.png)

1. Ga naar het tabblad **ADFS/Azure AD(Premium)** en voer de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Selecteer **Bladeren** om het basis-64 gecodeerde certificaat dat u hebt gedownload van de Azure-portal te uploaden naar het **ADFS-certificaat.**

    b. Plak in het vak **Uri van ADFS-uitgever** de waarde van **azure AD-id** die u hebt gekopieerd van de Azure-portal.

    c. Plak in het vak **Doel-URI** de waarde van **de aanmeldings-URL** die u hebt gekopieerd vanuit de Azure-portal.

    d. Ga voor de waarde **relying party-id** naar de Azure-portal en selecteer vervolgens op de pagina **PureCloud by Genesys-toepassingsintegratie** het tabblad **Eigenschappen** en kopieer de waarde **toepassings-id.** Plak deze in het vak **Relying Party Identifier.**

    ![Eenmalige aanmelding configureren](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. Selecteer **Opslaan**.

### <a name="create-purecloud-by-genesys-test-user"></a>PureCloud by Genesys-testgebruiker maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij PureCloud door Genesys, moeten ze door Genesys in PureCloud worden ingericht. In het geval van PureCloud by Genesys is dat een handmatige taak.

**Voer de volgende stappen uit om een gebruikersaccount in te richten:**

1. Log in bij PureCloud by Genesys als beheerder.

1. Selecteer **Beheerder** bovenaan en ga naar **Personen** onder Personen **& machtigingen**.

    ![Eenmalige aanmelding configureren](./media/purecloud-by-genesys-tutorial/configure03.png)

1. Selecteer persoon **toevoegen**op de pagina **Personen** .

    ![Eenmalige aanmelding configureren](./media/purecloud-by-genesys-tutorial/configure04.png)

1. Voer **in het** dialoogvenster Personen toevoegen aan de organisatie de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. Voer in het vak **Volledige naam** de naam van een gebruiker in. Bijvoorbeeld: **B.simon**.

    b. Voer in het vak **E-mail** de e-mail van de gebruiker in. Bijvoorbeeld: **b.simon\@contoso.com**.

    c. Selecteer **Maken**.

## <a name="test-sso"></a>Test SSO

In dit gedeelte test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u de tegel **PureCloud by Genesys** selecteert in het Access Panel, moet u automatisch worden aangemeld bij het PureCloud by Genesys-account waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure AD?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer PureCloud by Genesys met Azure AD](https://aad.portal.azure.com/)
