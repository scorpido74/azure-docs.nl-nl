---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met LinkedIn-verhoging | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en LinkedIn-bevoegdheden.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ad9941b-c574-42c3-bd0f-5d6ec68537ef
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 03efae5f9dec904f141a6776766850aa1f328892
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74892121"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-elevate"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met LinkedIn-uitbrei ding

In deze zelf studie leert u hoe u de verhoging van LinkedIn kunt integreren met Azure Active Directory (Azure AD). Wanneer u LinkedIn-verhoging met Azure AD integreert, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot LinkedIn-bevoegdheden.
* Uw gebruikers in staat stellen om automatisch te worden aangemeld bij LinkedIn met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* LinkedIn-abonnement voor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.



* De LinkedIn-uitbrei ding ondersteunt SSO **met SP en IDP**
* Het niveau van LinkedIn-bevoegdheden ondersteunt **just-in-time** -gebruikers inrichting
* LinkedIn-uitbrei ding ondersteunt [ **geautomatiseerde** gebruikers inrichting](linkedinelevate-provisioning-tutorial.md)

## <a name="adding-linkedin-elevate-from-the-gallery"></a>LinkedIn-verhoging toevoegen vanuit de galerie

Als u de integratie van LinkedIn-bevoegdheden wilt configureren in azure AD, moet u een uitbrei ding op LinkedIn toevoegen vanuit de galerie naar uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** de tekst **LinkedIn-verhoging** in het zoekvak.
1. Selecteer **LinkedIn-uitbrei ding** vanuit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-elevate"></a>Eenmalige aanmelding van Azure AD voor LinkedIn-uitbrei ding configureren en testen

Azure AD SSO configureren en testen met LinkedIn-verhoging met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in LinkedIn-bevoegdheden.

Als u Azure AD SSO wilt configureren en testen met LinkedIn-uitbrei ding, voert u de volgende bouw stenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[LinkedIn-Elevate-SSO configureren](#configure-linkedin-elevate-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een gebruiker met een verhoogde bevoegdheid](#create-linkedin-elevate-test-user)** voor het maken van een gebruikers-naar-een-een-op-een-op-een-of
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina voor het **uitbreiden** van de LinkedIn-toepassing, zoek de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. Voer in het tekstvak **id** de waarde voor de **entiteits-** id in, die u in de LinkedIn-Portal kunt kopiëren. dit wordt verderop in deze zelf studie uitgelegd.

    b. In het tekstvak **antwoord-URL** voert u de waarde voor de **Assertion Consumer Access (ACS)** -URL in. u kopieert de URL-waarde voor Assertion Consumer Access (ACS) van de LinkedIn-Portal verderop in deze zelf studie.

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=elevate&applicationInstanceId=<InstanceId>`

1. Voor de toepassing LinkedIn Elevation wordt de SAML-beweringen in een specifieke indeling verwacht. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. Voor de toepassing LinkedIn Elevation wordt verwacht dat nameidentifier wordt toegewezen aan **User. mail**, dus u moet de kenmerk toewijzing bewerken door op het pictogram bewerken te klikken en de kenmerk toewijzing te wijzigen.

    ![installatiekopie](common/edit-attribute.png)

1. Naast de bovenstaande voor LinkedIn-toepassingen met een verhoogde bevoegdheid, verwacht u nog enkele kenmerken te worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereiste.

    | Naam | Bronkenmerk|
    | -------| -------------|
    | department | user.department |

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Op de sectie **LinkedIn-uitbrei ding instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang toe te kennen aan de LinkedIn-verhoging.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **LinkedIn-verhoging**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-linkedin-elevate-sso"></a>Een LinkedIn-Elevate-SSO configureren

1. Meld u in een ander browser venster aan bij uw LinkedIn-Tenant met verhoogde bevoegdheden als beheerder.

1. Klik in **Accountcentrum** onder **Instellingen** op **Algemene instellingen**. Selecteer ook de optie voor het uitbreiden van de **Aad-test** in de vervolg keuzelijst.

    ![Eenmalige aanmelding configureren](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_01.png)

1. Klik op **of klik hier om afzonderlijke velden uit het formulier te laden en te kopiëren** en de volgende stappen uit te voeren:

    ![Eenmalige aanmelding configureren](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_03.png)

    a. Kopieer de **Entiteits-ID** en plak deze in het tekstvak **id** in de basis- **SAML-configuratie** in de Azure Portal.

    b. Kopieer de URL van de **Assertion Consumer toegang (ACS)** en plak deze in het tekstvak **antwoord-URL** in de **basis-SAML-configuratie** in de Azure Portal.

1. Ga naar het gedeelte **met beheerdersinstellingen voor LinkedIn**. Upload het XML-bestand dat u hebt gedownload van de Azure Portal door te klikken op de optie XML-bestand uploaden.

    ![Eenmalige aanmelding configureren](./media/linkedinelevate-tutorial/tutorial_linkedin_metadata_03.png)

1. Klik op **Aan** om SSO in te schakelen. De SSO-status wordt gewijzigd van **niet verbonden** met **verbonden**

    ![Eenmalige aanmelding configureren](./media/linkedinelevate-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-elevate-test-user"></a>Test gebruiker voor LinkedIn-bevoegdheden maken

De coactieve toepassing voor LinkedIn ondersteunt just-in-time-gebruikers inrichting en nadat gebruikers met verificatie automatisch in de toepassing worden gemaakt. Op de pagina beheer instellingen van de LinkedIn-Portal uitbrei ding spie gelen de switch **Automatisch licenties toewijzen** aan actief, net zo lang het inrichten is. er wordt ook een licentie aan de gebruiker toegewezen. Meer informatie over het verbeteren van de gebruikers ondersteuning wordt door LinkedIn-bevoegdheden ondersteund. u kunt [hier](linkedinelevate-provisioning-tutorial.md) meer details vinden over het configureren van automatische gebruikers inrichting.

   ![Een Azure AD-testgebruiker maken](./media/linkedinelevate-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel LinkedIn-verhoging in het toegangs venster klikt, moet u automatisch worden aangemeld bij de LinkedIn-verhoging waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer LinkedIn te verhogen met Azure AD](https://aad.portal.azure.com/)

