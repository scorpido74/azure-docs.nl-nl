---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met KnowledgeOwl | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en KnowledgeOwl.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ae30996-864d-4872-90bc-f770e1ea159a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dc7d481b757a76ba65e0c78a93bde1bc58ace7cc
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791638"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-knowledgeowl"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met KnowledgeOwl

In deze zelf studie leert u hoe u KnowledgeOwl integreert met Azure Active Directory (Azure AD). Wanneer u KnowledgeOwl integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot KnowledgeOwl.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij KnowledgeOwl met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* KnowledgeOwl-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* KnowledgeOwl ondersteunt SSO die door **SP en IDP** is geïnitieerd
* KnowledgeOwl ondersteunt **just-in-time** -gebruikers inrichting

## <a name="adding-knowledgeowl-from-the-gallery"></a>KnowledgeOwl toevoegen uit de galerie

Als u de integratie van KnowledgeOwl in azure AD wilt configureren, moet u KnowledgeOwl uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **KnowledgeOwl** in het zoekvak.
1. Selecteer **KnowledgeOwl** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-knowledgeowl"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor KnowledgeOwl

Azure AD SSO met KnowledgeOwl configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in KnowledgeOwl.

Als u Azure AD SSO wilt configureren en testen met KnowledgeOwl, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[KNOWLEDGEOWL SSO configureren](#configure-knowledgeowl-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een KnowledgeOwl-test gebruiker](#create-knowledgeowl-test-user)** -om een equivalent van B. Simon in KnowledgeOwl te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **KnowledgeOwl** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon:
    
    | | |
    |-|-|
    | `https://app.knowledgeowl.com/sp`|
    | `https://app.knowledgeowl.com/sp/id/<unique ID>`|

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon:
    
    | | |
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon:
    
    | | |
    |-|-|
    | `https://subdomain.knowledgeowl.com/help/saml-login`|
    | `https://subdomain.knowledgeowl.com/docs/saml-login`|
    | `https://subdomain.knowledgeowl.com/home/saml-login`|
    | `https://privatedomain.com/help/saml-login`|
    | `https://privatedomain.com/docs/saml-login`|
    | `https://privatedomain.com/home/saml-login`|

    > [!NOTE]
    > Dit zijn geen echte waarden. U moet deze waarde bijwerken vanuit de daad werkelijke id, de antwoord-URL en de aanmeldings-URL die verderop in de zelf studie wordt uitgelegd.

1. De KnowledgeOwl-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Daarnaast verwacht KnowledgeOwl toepassing nog maar weinig kenmerken die worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    | Naam | Bronkenmerk | Naamruimte |
    | ------------ | -------------------- | -----|
    | ssoid | user.mail | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims`|

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , **certificaat (RAW)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificateraw.png)

1. Op de sectie **KnowledgeOwl instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan KnowledgeOwl.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **KnowledgeOwl**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-knowledgeowl-sso"></a>KnowledgeOwl SSO configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij uw KnowledgeOwl-bedrijfs site.

1. Klik op **instellingen** en selecteer vervolgens **beveiliging**.

    ![KnowledgeOwl-configuratie](./media/knowledgeowl-tutorial/configure1.png)

1. Ga naar **SAML-SSO-integratie** en voer de volgende stappen uit:

    ![KnowledgeOwl-configuratie](./media/knowledgeowl-tutorial/configure2.png)

    a. Selecteer **SAML SSO inschakelen**.

    b. Kopieer de waarde van de **SP-entiteit-id** en plak deze in de **id (Entiteits-ID)** in het gedeelte **basis configuratie van SAML** op het Azure Portal.

    c. Kopieer de waarde voor de **aanmeldings-URL van SP** en plak deze in de tekst van de **aanmeldings-URL en antwoord-URL** in het gedeelte **basis-SAML-configuratie** op de Azure Portal.

    d. Plak in het tekstvak **IDP entityID** de waarde van de **Azure ad-id** , die u hebt gekopieerd uit de Azure Portal.

    e. Plak in het tekstvak **IDP aanmeld-URL** de waarde voor de **aanmeldings-URL** , die u hebt gekopieerd uit de Azure Portal.

    f. Plak in het tekstvak **Afmeldings-URL van IDP** de waarde van de **afmeldings-URL** , die u hebt gekopieerd uit de Azure Portal

    g. Upload het gedownloade certificaat formulier de Azure Portal door te klikken op het **certificaat IDP uploaden**.

    h. Klik op **SAML-kenmerken toewijzen** om kenmerken toe te wijzen en voer de volgende stappen uit:

    ![KnowledgeOwl-configuratie](./media/knowledgeowl-tutorial/configure3.png)

    * `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/ssoid` invoeren in het tekstvak **SSO-id**
    * Voer `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` in het tekstvak **gebruikers naam/e-mail adres** in.
    * Voer `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` in het tekstvak **voor de voor naam** in.
    * Voer `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` in het tekstvak **Achternaam** in.
    * Klik op **Opslaan**.

    i. Klik op **Opslaan** onder aan de pagina.

    ![KnowledgeOwl-configuratie](./media/knowledgeowl-tutorial/configure4.png)

### <a name="create-knowledgeowl-test-user"></a>KnowledgeOwl-test gebruiker maken

In deze sectie wordt een gebruiker met de naam B. Simon gemaakt in KnowledgeOwl. KnowledgeOwl biedt ondersteuning voor Just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in KnowledgeOwl, wordt er een nieuwe gemaakt na verificatie.

> [!Note]
> Als u hand matig een gebruiker moet maken, neemt u contact op met het [ondersteunings team van KnowledgeOwl](mailto:support@knowledgeowl.com).

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel KnowledgeOwl in het toegangs venster klikt, moet u automatisch worden aangemeld bij de KnowledgeOwl waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer KnowledgeOwl met Azure AD](https://aad.portal.azure.com/)