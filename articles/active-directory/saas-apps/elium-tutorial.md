---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Elium | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Elium.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: fae344b3-5bd9-40e2-9a1d-448dcd58155f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0900f730c287586725722f0b8baaeb0c22f850c2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "72791226"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-elium"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Elium

In deze zelf studie leert u hoe u Elium integreert met Azure Active Directory (Azure AD). Wanneer u Elium integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Elium.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Elium met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Elium-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Elium ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding (SSO)
* Elium ondersteunt het **Just-In-Time** inrichten van gebruikers

## <a name="adding-elium-from-the-gallery"></a>Elium toevoegen vanuit de galerie

Om de integratie van Elium in Azure AD te configureren, moet u Elium uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Elium** in het zoekvak.
1. Selecteer **Elium** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-elium"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Elium

Azure AD SSO met Elium configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Elium.

Als u Azure AD SSO wilt configureren en testen met Elium, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[ELIUM SSO configureren](#configure-elium-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een Elium-test gebruiker](#create-elium-test-user)** -om een equivalent van B. Simon in Elium te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **Elium** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. Typ in het tekstvak **id** een URL met het volgende patroon:`https://<platform-domain>.elium.com/login/saml2/metadata`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<platform-domain>.elium.com/login/saml2/acs`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<platform-domain>.elium.com/login/saml2/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. U verkrijgt deze waarden uit het **SP-metagegevensbestand** dat u kunt downloaden van `https://<platform-domain>.elium.com/login/saml2/metadata`, zoals later in deze zelfstudie wordt uitgelegd.

1. De Elium-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Daarnaast verwacht Elium toepassing nog maar weinig kenmerken die worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    | Naam | Bronkenmerk|
    | ---------------| ----------------|
    | e-mail   |user.mail |
    | first_name| user.givenname |
    | last_name| user.surname|
    | job_title| user.jobtitle|
    | bedrijf| user.companyname|

    > [!NOTE]
    > Dit zijn de standaardclaims. **Alleen de email-claim is vereist**. Voor JIT-inrichting is ook alleen de email-claim verplicht. Andere aangepaste claims kunnen variëren per klantenplatform.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Op de sectie **Elium instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Elium.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **Elium** in de lijst met toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-elium-sso"></a>Elium SSO configureren

1. Als u de configuratie wilt automatiseren in Elium, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

1. Nadat u de extensie hebt toegevoegd aan de browser, klikt u op **Elium instellen** gaat u naar de Elium-toepassing. Geef de beheerders referenties op om u aan te melden bij Elium. Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-6 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

1. Als u Elium hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij uw Elium-bedrijfs site als beheerder en voert u de volgende stappen uit:

1. Klik op het **User profile** in de rechterbovenhoek en selecteer **Administration**.

    ![Eenmalige aanmelding configureren](./media/elium-tutorial/user1.png)

1. Selecteer het tabblad **Security**.

    ![Eenmalige aanmelding configureren](./media/elium-tutorial/user2.png)

1. Schuif omlaag naar de sectie **Single sign-on (SSO)** en voer de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/elium-tutorial/user3.png)

    a. Kopieer de waarde van **Verify that SAML2 authentication works for your account** en plak deze in het tekstvak **Aanmeldings-URL** in de sectie **Standaard SAML-configuratie** van de Azure-portal.

    > [!NOTE]
    > Nadat u SSO hebt geconfigureerd, hebt u altijd toegang tot de standaardpagina voor extern aanmelden op de volgende URL: `https://<platform_domain>/login/regular/login` 

    b. Selecteer het selectievakje **Enable SAML2 federation**.

    c. Selecteer het selectievakje **JIT Provisioning**.

    d. Open de **SP Metadata** door op de knop **Download** te klikken.

    e. Zoek naar de **entityID** in het **SP Metadata**-bestand, kopieer de waarde van **entityID** en plak deze in het tekstvak **ID** van de sectie **Standaard SAML-configuratie** van de Azure-portal. 

    ![Eenmalige aanmelding configureren](./media/elium-tutorial/user4.png)

    f. Zoek naar de **AssertionConsumerService** in het **SP Metadata**-bestand, kopieer de waarde van **Location** en plak deze in het tekstvak **Antwoord-URL** van de sectie **Standaard SAML-configuratie** van de Azure-portal.

    ![Eenmalige aanmelding configureren](./media/elium-tutorial/user5.png)

    g. Open het uit de Azure-portal gedownloade metagegevensbestand in Kladblok, kopieer de inhoud en plak deze in het tekstvak **IdP Metadata**.

    h. Klik op **Opslaan**.

### <a name="create-elium-test-user"></a>Elium-testgebruiker maken

In deze sectie wordt een gebruiker met de naam B. Simon gemaakt in Elium. Elium ondersteunt **Just-In-Time-inrichting**; deze optie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Elium bestaat, wordt er een nieuwe gemaakt wanneer u Elium probeert te openen.

> [!Note]
> Als u handmatig een gebruiker wilt maken, neem dan contact op met het [ondersteuningsteam van Elium](mailto:support@elium.com).

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Elium in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Elium waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Elium met Azure AD](https://aad.portal.azure.com/)