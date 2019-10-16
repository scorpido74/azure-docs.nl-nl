---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met PureCloud door Genesys | Microsoft Docs'
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
ms.openlocfilehash: dfaa5a4ebb8bc633dc49db08698aec31de9436d4
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373125"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met PureCloud door Genesys

In deze zelf studie leert u hoe u PureCloud kunt integreren met behulp van Genesys met Azure Active Directory (Azure AD). Wanneer u PureCloud integreert door Genesys met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot PureCloud door Genesys.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij PureCloud door Genesys met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* PureCloud door Genesys-abonnement met eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* PureCloud by Genesys ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> De id van deze toepassing is een vaste teken reeks waarde zodat slechts één exemplaar in één Tenant kan worden geconfigureerd.

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>PureCloud by Genesys toevoegen vanuit de galerie

Om de integratie van PureCloud by Genesys in Azure AD te configureren, moet u PureCloud by Genesys vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ **PureCloud by Genesys** in het zoekvak van de sectie **toevoegen vanuit de galerie** .
1. Selecteer **PureCloud by Genesys** in het resultaten paneel en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purecloud-by-genesys"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor PureCloud door Genesys

Azure AD SSO configureren en testen met PureCloud door Genesys met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in PureCloud door Genesys.

Als u Azure AD SSO wilt configureren en testen met PureCloud door Genesys, voert u de volgende bouw stenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[PureCloud configureren via GENESYS SSO](#configure-purecloud-by-genesys-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak PureCloud door Genesys-test gebruiker](#create-purecloud-by-genesys-test-user)** : als u een equivalent van B. Simon in PureCloud wilt hebben door Genesys dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina **PureCloud by Genesys** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. Typ in het tekstvak **Id** een URL overeenkomstig uw regio:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. Typ in het tekstvak **Antwoord-URL** een URL overeenkomstig uw regio:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml`|

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ in het tekstvak **Aanmeldings-URL** een URL overeenkomstig uw regio:
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

1. In de PureCloud by Genesys-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Daarnaast verwacht PureCloud door Genesys toepassing nog maar weinig kenmerken die kunnen worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    | Naam | Bronkenmerk|
    | ---------------| --------------- |
    | E-mail | user.userprinicipalname |
    | OrganizationName | `Your organization name` |

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer op de sectie **PureCloud by Genesys instellen** de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam** `B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extension in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan PureCloud door Genesys.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **PureCloud by Genesys** in de lijst met toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-purecloud-by-genesys-sso"></a>PureCloud configureren via Genesys SSO

1. Meld u in een ander browser venster aan bij PureCloud door Genesys als beheerder.

1. Klik op **Admin** in de rechterbovenhoek en ga vervolgens naar **Single Sign-on** onder **Integrations**.

    ![Eenmalige aanmelding configureren](./media/purecloud-by-genesys-tutorial/configure01.png)

1. Selecteer het tabblad **ADFS/Azure AD(Premium)** en voer de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Klik op **Browse** om het met base-64 gecodeerde certificaat dat u hebt gedownload uit de Azure-portal te uploaden naar het **ADFS-certificaat**.

    b. Plak in het tekstvak **ADFS Issuer URI** de waarde van **Azure AD-id**, die u hebt gekopieerd uit de Azure-portal.

    c. Plak in het tekstvak **Target URI** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    d. Om de waarde voor **Relying Party Identifier** te verkrijgen, gaat u naar de Azure-portal. Klik op de integratiepagina voor de **PureCloud by Genesys**-toepassing op het tabblad **Eigenschappen** en kopieer de waarde van **Toepassings-id**. Plak deze in het tekstvak **Relying Party Identifier**. 

    ![Eenmalige aanmelding configureren](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. Klik op **Opslaan**.

### <a name="create-purecloud-by-genesys-test-user"></a>PureCloud by Genesys-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij PureCloud door Genesys, moeten ze worden ingericht in PureCloud van Genesys. In het geval van PureCloud by Genesys is dat een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan bij PureCloud by Genesys als beheerder.

1. Klik op **Admin** in de rechterbovenhoek en ga vervolgens naar **People** onder **People & Permissions**.

    ![Eenmalige aanmelding configureren](./media/purecloud-by-genesys-tutorial/configure03.png)

1. Klik op de pagina People op **Add Person**.

    ![Eenmalige aanmelding configureren](./media/purecloud-by-genesys-tutorial/configure04.png)

1. Voer de volgende stappen uit in het pop-upvenster **Add People to the Organization**:

    ![Eenmalige aanmelding configureren](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. Voer in het tekstvak **volledige naam** de naam van de gebruiker in zoals **B. Simon**.

    b. Voer in het tekstvak **e-mail** het e-mail adres van de gebruiker in, zoals **b.Simon\@contoso.com**.

    c. Klik op **Maken**.

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel PureCloud by Genesys klikt, wordt u automatisch aangemeld bij de instantie van PureCloud by Genesys waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer PureCloud door Genesys met Azure AD](https://aad.portal.azure.com/)