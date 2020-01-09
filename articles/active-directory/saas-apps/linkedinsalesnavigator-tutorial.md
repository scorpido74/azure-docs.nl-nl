---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met LinkedIn Sales Navigator | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en LinkedIn Sales Navigator.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7a9fa8f3-d611-4ffe-8d50-04e9586b24da
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0d8293b23559860e70191576db13c3cd14f520e6
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430888"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-sales-navigator"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met LinkedIn Sales Navigator

In deze zelf studie leert u hoe u de LinkedIn Sales Navigator integreert met Azure Active Directory (Azure AD). Wanneer u LinkedIn Sales Navigator integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de LinkedIn-verkoop Navigator.
* Uw gebruikers in staat stellen om automatisch te worden aangemeld bij LinkedIn Sales Navigator met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* LinkedIn Sales Navigator-abonnement voor eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* LinkedIn Sales Navigator ondersteunt SSO die is gestart **met SP en IDP**
* LinkedIn Sales Navigator ondersteunt **just-in-time** -gebruikers inrichting
* LinkedIn Sales Navigator ondersteunt [ **geautomatiseerde** gebruikers inrichting](linkedinsalesnavigator-provisioning-tutorial.md)

## <a name="adding-linkedin-sales-navigator-from-the-gallery"></a>LinkedIn-verkoop Navigator toevoegen vanuit de galerie

Als u de integratie van LinkedIn Sales Navigator wilt configureren in azure AD, moet u de LinkedIn-verkoop Navigator vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in het gedeelte in **de galerie toevoegen** de tekst **LinkedIn Sales Navigator** in het zoekvak.
1. Selecteer **LinkedIn Sales Navigator** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-sales-navigator"></a>Eenmalige aanmelding van Azure AD voor LinkedIn Sales Navigator configureren en testen

Azure AD SSO configureren en testen met LinkedIn Sales Navigator met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in de LinkedIn Sales Navigator.

Als u Azure AD SSO wilt configureren en testen met LinkedIn Sales Navigator, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[LinkedIn Sales Navigator SSO configureren](#configure-linkedin-sales-navigator-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * Maak een andere test gebruiker voor het maken van een **[LinkedIn-verkoop Navigator](#create-linkedin-sales-navigator-test-user)** . in de LinkedIn-verkoop Navigator die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina **LinkedIn Sales Navigator** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. Voer in het tekstvak **id** de waarde voor de **entiteits-** id in, die u in de LinkedIn-Portal kunt kopiëren. dit wordt verderop in deze zelf studie uitgelegd.

    b. In het tekstvak **antwoord-URL** voert u de waarde voor de **Assertion Consumer Access (ACS)** -URL in. u kopieert de URL-waarde voor Assertion Consumer Access (ACS) van de LinkedIn-Portal verderop in deze zelf studie.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://www.linkedin.com/checkpoint/enterprise/login/<account id>?application=salesNavigator`

1. Voor de toepassing LinkedIn Sales Navigator worden de SAML-beweringen in een specifieke indeling verwacht. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Voor de toepassing LinkedIn Sales Navigator worden behalve hierboven echter nog maar weinig kenmerken door gegeven aan het SAML-antwoord dat hieronder wordt weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    | Name | Bronkenmerk|
    | --- | --- |
    | e-mail| user.mail |
    | department| user.department |
    | firstname| user.givenname |
    | lastname| user.surname |
    | Unieke gebruikers-id | user.mail |

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer op de sectie **LinkedIn-verkoop Navigator instellen** de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot de LinkedIn-verkoop Navigator.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **LinkedIn Sales Navigator**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-linkedin-sales-navigator-sso"></a>LinkedIn Sales Navigator SSO configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij uw **LinkedIn Sales Navigator** -website.

1. Klik in **Accountcentrum** onder **Instellingen** op **Algemene instellingen**. Selecteer ook **verkoop Navigator** in de vervolg keuzelijst.

    ![Eenmalige aanmelding configureren](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_01.png)

1. Klik op **of klik hier om afzonderlijke velden uit het formulier te laden en te kopiëren** en de volgende stappen uit te voeren:

    ![Eenmalige aanmelding configureren](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_031.png)

    a. Kopieer de **Entiteits-ID** en plak deze in het tekstvak **id** in de basis- **SAML-configuratie** in de Azure Portal.

    b. Kopieer de URL van de **Assertion Consumer toegang (ACS)** en plak deze in het tekstvak **antwoord-URL** in de **basis-SAML-configuratie** in de Azure Portal.

1. Ga naar het gedeelte **met beheerdersinstellingen voor LinkedIn**. Upload het XML-bestand dat u hebt gedownload van de Azure Portal door te klikken op de optie **XML-bestand uploaden** .

    ![Eenmalige aanmelding configureren](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_metadata_03.png)

1. Klik op **Aan** om SSO in te schakelen. De SSO-status verandert van **Niet verbonden** naar **Verbonden**

    ![Eenmalige aanmelding configureren](./media/linkedinsalesnavigator-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-sales-navigator-test-user"></a>Een test gebruiker voor de LinkedIn-verkoop Navigator maken

De gekoppelde verkoop Navigator-toepassing biedt ondersteuning voor Just-in-time (JIT)-gebruikers inrichten en nadat gebruikers met verificatie automatisch in de toepassing zijn gemaakt. Activeer **Automatisch licenties toewijzen** om een licentie aan de gebruiker toe te wijzen.

   ![Een Azure AD-testgebruiker maken](./media/linkedinsalesnavigator-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel verkoop Navigator LinkedIn in het toegangs venster klikt, moet u automatisch worden aangemeld bij de LinkedIn Sales Navigator waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer LinkedIn Sales Navigator met Azure AD](https://aad.portal.azure.com/)