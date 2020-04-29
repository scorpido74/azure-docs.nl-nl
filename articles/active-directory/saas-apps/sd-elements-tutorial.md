---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met SD-elementen | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SD-elementen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f0386307-bb3b-4810-8d4b-d0bfebda04f4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a387659e2375444fd32cf731ab4bccc210b669a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74081685"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sd-elements"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met SD-elementen

In deze zelf studie leert u hoe u SD-elementen integreert met Azure Active Directory (Azure AD). Wanneer u SD-elementen integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot SD-elementen.
* Uw gebruikers in staat stellen om automatisch te worden aangemeld bij SD-elementen met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* SD-elementen waarvoor het abonnement is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* SD-elementen ondersteunen **IDP** GEÏNITIEERDe SSO

## <a name="adding-sd-elements-from-the-gallery"></a>SD-elementen uit de galerie toevoegen

Als u de integratie van SD-elementen wilt configureren in azure AD, moet u SD-elementen uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **SD-elementen** in het zoekvak.
1. Selecteer **SD-elementen** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-sd-elements"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor SD-elementen

Azure AD SSO configureren en testen met SD-elementen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SD-elementen.

Als u Azure AD SSO wilt configureren en testen met SD-elementen, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Configureren van SD-elementen SSO](#configure-sd-elements-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak SD-elementen test gebruiker](#create-sd-elements-test-user)** -om een soort equivalent van B. Simon te hebben in SD-elementen die zijn gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina **SD-elementen** voor de integratie van de toepassing en selecteer de sectie voor het **beheren** van **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **eenmalige aanmelding met SAML instellen** de waarden in voor de volgende velden:

    a. Typ in het tekstvak **id** een URL met het volgende patroon:`https://<tenantname>.sdelements.com/sso/saml2/metadata`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<tenantname>.sdelements.com/sso/saml2/acs/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met [SD-elementen client ondersteunings team](mailto:support@sdelements.com) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De toepassing SD-elementen verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Behalve hierboven verwachtte de toepassing SD-elementen nog maar weinig kenmerken om te worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    | Naam |  Bronkenmerk|
    | --- | --- |
    | e-mail |user.mail |
    | firstname |user.givenname |
    | lastname |user.surname |

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **SD-elementen instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot SD-elementen.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **SD-elementen**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-sd-elements-sso"></a>SSO-elementen configureren

1. Als u eenmalige aanmelding wilt inschakelen, neemt u contact op met het [ondersteunings team](mailto:support@sdelements.com) van de SD-elementen en geeft u het gedownloade certificaat bestand.

1. Meld u in een ander browser venster aan bij uw SD-elementen Tenant als beheerder.

1. Klik in het menu aan de bovenkant op **systeem**en vervolgens op **eenmalige aanmelding**.

    ![Eenmalige aanmelding configureren](./media/sd-elements-tutorial/tutorial_sd-elements_09.png)

1. Voer de volgende stappen uit in het dialoog venster **instellingen voor eenmalige aanmelding** :

    ![Eenmalige aanmelding configureren](./media/sd-elements-tutorial/tutorial_sd-elements_10.png)

    a. Selecteer **SAML**als **SSO-type**.

    b. Plak de waarde van de **Azure ad-id**die u van Azure Portal hebt gekopieerd in het tekstvak id **-provider entiteits-** id's.

    c. Plak in het tekstvak **ID-provider single sign-on Service** de waarde van de **aanmeldings-URL**die u van Azure Portal hebt gekopieerd.

    d. Klik op **Opslaan**.

### <a name="create-sd-elements-test-user"></a>Test gebruiker van SD-elementen maken

Het doel van deze sectie is het maken van een gebruiker met de naam B. Simon in SD-elementen. In het geval van SD-elementen maken gebruikers van SD-elementen een hand matige taak.

**Als u B. Simon wilt maken in SD-elementen, voert u de volgende stappen uit:**

1. Meld u in een webbrowser venster aan bij de bedrijfs site van de SD-elementen als beheerder.

1. Klik in het menu aan de bovenkant op **gebruikers beheer**en vervolgens op **gebruikers**.

    ![Een test gebruiker met SD-elementen maken](./media/sd-elements-tutorial/tutorial_sd-elements_11.png) 

1. Klik op **Add New User**.

    ![Een test gebruiker met SD-elementen maken](./media/sd-elements-tutorial/tutorial_sd-elements_12.png)

1. Voer de volgende stappen uit in het dialoog venster **nieuwe gebruiker toevoegen** :

    ![Een test gebruiker met SD-elementen maken](./media/sd-elements-tutorial/tutorial_sd-elements_13.png) 

    a. Voer in het tekstvak **e-mail** het e-mail adres van **b.simon@contoso.com**de gebruiker in.

    b. Voer in het tekstvak **voor de voor naam** de voor naam van de gebruiker in, zoals **B.**.

    c. Voer in het tekstvak **Achternaam** de achternaam van de gebruiker in, zoals **Simon**.

    d. Als **rol**selecteert u **gebruiker**.

    e. Klik op **Gebruiker maken**.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SD-elementen in het toegangs venster klikt, moet u automatisch worden aangemeld bij de SD-elementen waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer SD-elementen uit met Azure AD](https://aad.portal.azure.com/)