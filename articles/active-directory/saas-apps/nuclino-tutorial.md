---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Nuclino | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Nuclino.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 74bbab82-5581-4dcf-8806-78f77c746968
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a75869c257e6b875a00036218b05db5521e8d0b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "72532963"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nuclino"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Nuclino

In deze zelf studie leert u hoe u Nuclino integreert met Azure Active Directory (Azure AD). Wanneer u Nuclino integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Nuclino.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Nuclino met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Nuclino-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Nuclino ondersteunt SSO die door **SP en IDP** is geïnitieerd
* Nuclino ondersteunt **just-in-time** -gebruikers inrichting

## <a name="adding-nuclino-from-the-gallery"></a>Nuclino toevoegen uit de galerie

Als u de integratie van Nuclino in azure AD wilt configureren, moet u Nuclino uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Nuclino** in het zoekvak.
1. Selecteer **Nuclino** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-nuclino"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Nuclino

Azure AD SSO met Nuclino configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Nuclino.

Als u Azure AD SSO wilt configureren en testen met Nuclino, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[NUCLINO SSO configureren](#configure-nuclino-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een Nuclino-test gebruiker](#create-nuclino-test-user)** -om een equivalent van B. Simon in Nuclino te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **Nuclino** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. Typ in het tekstvak **id** een URL met het volgende patroon:`https://api.nuclino.com/api/sso/<UNIQUE-ID>/metadata`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://api.nuclino.com/api/sso/<UNIQUE-ID>/acs`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de daad werkelijke id en de antwoord-URL van de sectie **authenticatie** , die later in deze zelf studie wordt beschreven.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://app.nuclino.com/<UNIQUE-ID>/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteunings team van Nuclino-clients](mailto:contact@nuclino.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. De Nuclino-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/edit-attribute.png)

7. Daarnaast verwacht Nuclino toepassing nog maar weinig kenmerken die worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    | Naam |  Bronkenmerk|
    | ---------------| --------- |
    | first_name | user.givenname |
    | last_name | user.surname |

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **Nuclino instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Nuclino.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Nuclino**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-nuclino-sso"></a>Nuclino SSO configureren

1. Als u de configuratie wilt automatiseren in Nuclino, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie hebt toegevoegd aan de browser, klikt u op **Nuclino instellen** gaat u naar de Nuclino-toepassing. Geef de beheerders referenties op om u aan te melden bij Nuclino. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3-7 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

3. Als u Nuclino hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij uw Nuclino-bedrijfs site als beheerder en voert u de volgende stappen uit:

4. Klik op het **pictogram**.

    ![Nuclino-configuratie](./media/nuclino-tutorial/configure1.png)

5. Klik op de **Azure AD-SSO** en selecteer **team instellingen** in de vervolg keuzelijst.

    ![Nuclino-configuratie](./media/nuclino-tutorial/configure2.png)

6. Selecteer **verificatie** vanuit het navigatie deel venster links.

    ![Nuclino-configuratie](./media/nuclino-tutorial/configure3.png)

7. Voer de volgende stappen uit in de sectie **verificatie** :

    ![Nuclino-configuratie](./media/nuclino-tutorial/configure4.png)

    a. Selecteer **eenmalige aanmelding op basis van SAML (SSO)**.

    b. Kopieer de **ACS-URL (u moet deze kopiëren en plakken naar uw SSO-provider)** en plak deze in het tekstvak **antwoord-URL** van het gedeelte basis-SAML- **configuratie** in de Azure Portal.

    c. Kopieer **de entiteits-id (u moet deze kopiëren en plakken naar uw SSO-provider)** en plak deze in het tekstvak **id** van de **basis-SAML-configuratie** sectie in de Azure Portal.

    d. Plak in het tekstvak **SSO-URL** de waarde voor de **aanmeldings-URL** die u hebt gekopieerd uit de Azure Portal.

    e. Plak in het tekstvak **Entiteits-ID** de waarde van de **Azure ad-id** die u hebt gekopieerd uit de Azure Portal.

    f. Open het gedownloade bestand **Certificate(Base64)** in Kladblok. Kopieer de inhoud van het bestand naar het klem bord en plak het vervolgens in het tekstvak van het **open bare certificaat** .

    g. Klik op **WIJZIGINGEN OPSLAAN**.

### <a name="create-nuclino-test-user"></a>Nuclino-test gebruiker maken

In deze sectie wordt een gebruiker met de naam B. Simon gemaakt in Nuclino. Nuclino biedt ondersteuning voor Just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Nuclino, wordt er een nieuwe gemaakt na verificatie.

> [!Note]
> Als u hand matig een gebruiker moet maken, neemt u contact op met het [ondersteunings team van Nuclino](mailto:contact@nuclino.com).

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Nuclino in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Nuclino waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Nuclino met Azure AD](https://aad.portal.azure.com/)

