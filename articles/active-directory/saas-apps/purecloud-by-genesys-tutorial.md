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
ms.openlocfilehash: 779328f4c21afb4392663e6f8840749ea505c529
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242437"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purecloud-by-genesys"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met PureCloud door Genesys

In deze zelf studie leert u hoe u PureCloud kunt integreren met behulp van Genesys met Azure Active Directory (Azure AD). Nadat u dit hebt gedaan, kunt u het volgende doen:

* Gebruik Azure AD om te bepalen welke gebruikers toegang hebben tot PureCloud door Genesys.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij PureCloud door Genesys met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u er nog geen hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Een PureCloud door Genesys-abonnement met eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* PureCloud door Genesys ondersteunt **SP-en IDP**-geïnitieerde SSO.

> [!NOTE]
> Omdat de ID voor deze toepassing een waarde met een vaste teken reeks is, kan slechts één exemplaar in één Tenant worden geconfigureerd.

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>PureCloud by Genesys toevoegen vanuit de galerie

Als u de integratie van PureCloud door Genesys wilt configureren in azure AD, moet u PureCloud toevoegen door Genesys vanuit de galerie aan uw lijst met beheerde SaaS-apps. Voer de volgende stappen uit om dit te doen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of met een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ **PureCloud by Genesys** in het zoekvak van de sectie **toevoegen vanuit de galerie** .
1. Selecteer **PureCloud by Genesys** in het deel venster resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purecloud-by-genesys"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor PureCloud door Genesys

Azure AD SSO configureren en testen met PureCloud door Genesys met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in PureCloud door Genesys.

Als u Azure AD SSO wilt configureren en testen met PureCloud door Genesys, voert u de volgende bouw stenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user)** eenmalige aanmelding van Azure ad te testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe om B. Simon in te scha kelen voor het gebruik van eenmalige aanmelding voor Azure AD.
1. **[Configureer PureCloud door GENESYS SSO](#configure-purecloud-by-genesys-sso)** om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
    1. **[Maak een PureCloud door Genesys-test gebruiker](#create-purecloud-by-genesys-test-user)** een tegen hanger van B. Simon in PureCloud te hebben door Genesys dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Voer de volgende stappen uit om Azure AD SSO in te scha kelen in de Azure Portal:

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina **PureCloud by Genesys** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Op de pagina **eenmalige aanmelding met SAML instellen** selecteert u het pictogram voor de pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de door **IDP**gestarte modus wilt configureren, voert u de waarden voor de volgende velden in in de sectie **basis configuratie van SAML** :

    a. Voer in het vak **id** een URL in die overeenkomt met uw regio:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. Voer in het vak **antwoord-URL** een URL in die overeenkomt met uw regio:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml`|

1. Selecteer **extra Url's instellen** en volg de volgende stap als u de toepassing in de modus door **SP** gestart wilt configureren:

    Voer in het vak **aanmeld-URL** een URL in die overeenkomt met uw regio:
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

1. In de PureCloud by Genesys-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. De volgende scherm afbeelding ziet u de lijst met standaard kenmerken:

    ![image](common/default-attributes.png)

1. Daarnaast verwacht PureCloud van Genesys-toepassing nog enkele kenmerken die in het SAML-antwoord worden door gegeven, zoals wordt weer gegeven in de volgende tabel. Deze kenmerken zijn ook vooraf ingevuld, maar u kunt ze naar behoefte bekijken.

    | Naam | Bronkenmerk|
    | ---------------| --------------- |
    | E-mail | user.userprinicipalname |
    | OrganizationName | `Your organization name` |

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **PureCloud by Genesys instellen** de juiste URL (of url's) op basis van uw vereisten.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker met de naam B. Simon in de Azure Portal:

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam** `B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de gebruikers naam in de volgende indeling in: username@companydomain.extension. Bijvoorbeeld: `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan PureCloud door Genesys.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **PureCloud by Genesys** in de lijst met toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst met gebruikers en kies vervolgens de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en kiest u vervolgens de knop **selecteren** onder aan het scherm.
1. Selecteer de knop **toewijzen** in het dialoog venster **toewijzing toevoegen** .

## <a name="configure-purecloud-by-genesys-sso"></a>PureCloud configureren via Genesys SSO

1. Meld u in een ander browser venster aan bij PureCloud door Genesys als beheerder.

1. Selecteer **beheerder** bovenaan en ga naar **eenmalige aanmelding** onder **integraties**.

    ![Eenmalige aanmelding configureren](./media/purecloud-by-genesys-tutorial/configure01.png)

1. Ga naar het tabblad **ADFS/Azure AD (Premium)** en voer vervolgens de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Selecteer **Bladeren** om het door base-64 gecodeerde certificaat dat u hebt gedownload van de Azure Portal naar het **ADFS-certificaat**te uploaden.

    b. Plak in het vak **URI van ADFS-Uitgever** de waarde van de **Azure ad-id** die u hebt gekopieerd uit de Azure Portal.

    c. Plak in het vak **doel-URI** de waarde van de **aanmeldings-URL** die u hebt gekopieerd uit de Azure Portal.

    d. Voor de waarde van de **relying** Party-id gaat u naar de Azure Portal en selecteert u op de pagina **PureCloud by Genesys** Application Integration het tabblad **Eigenschappen** en kopieert u de waarde van de **toepassings-id** . Plak deze in het vak **Relying Party Identifier** .

    ![Eenmalige aanmelding configureren](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. Selecteer **Opslaan**.

### <a name="create-purecloud-by-genesys-test-user"></a>PureCloud by Genesys-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij PureCloud door Genesys, moeten ze worden ingericht in PureCloud van Genesys. In het geval van PureCloud by Genesys is dat een handmatige taak.

**Voer de volgende stappen uit om een gebruikers account in te richten:**

1. Meld u als beheerder aan bij PureCloud door Genesys.

1. Selecteer bovenaan **beheer** en ga naar **personen** onder **personen & machtigingen**.

    ![Eenmalige aanmelding configureren](./media/purecloud-by-genesys-tutorial/configure03.png)

1. Selecteer **persoon toevoegen**op de pagina **personen** .

    ![Eenmalige aanmelding configureren](./media/purecloud-by-genesys-tutorial/configure04.png)

1. Voer in het dialoog venster **personen toevoegen aan de organisatie de** volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. Voer in het vak **volledige naam** de naam van een gebruiker in. Bijvoorbeeld: **B. Simon**.

    b. Voer in het vak **e-mail** het e-mail adres van de gebruiker in. Bijvoorbeeld: **b. simon\@contoso.com**.

    c. Selecteer **Maken**.

## <a name="test-sso"></a>SSO testen

In dit gedeelte test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u de tegel **PureCloud by Genesys** in het toegangs venster selecteert, moet u automatisch worden aangemeld bij de PureCloud op Genesys-account waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies over het integreren van SaaS-apps met Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure AD?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in azure AD?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer PureCloud door Genesys met Azure AD](https://aad.portal.azure.com/)