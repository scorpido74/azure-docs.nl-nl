---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met SignalFx | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SignalFx.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6d5ab4b0-29bc-4b20-8536-d64db7530f32
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea81f0046d7f73d845ed49325a3d621e6b7735e7
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443270"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-signalfx"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met SignalFx

In deze zelf studie leert u hoe u SignalFx integreert met Azure Active Directory (Azure AD). Wanneer u SignalFx integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot SignalFx.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij SignalFx met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* SignalFx-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* SignalFx ondersteunt door **IDP** GEÏNITIEERDe SSO
* SignalFx ondersteunt **just-in-time** -gebruikers inrichting

## <a name="adding-signalfx-from-the-gallery"></a>SignalFx toevoegen uit de galerie

Als u de integratie van SignalFx in azure AD wilt configureren, moet u SignalFx uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **SignalFx** in het zoekvak.
1. Selecteer **SignalFx** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-signalfx"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor SignalFx

Azure AD SSO met SignalFx configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SignalFx.

Als u Azure AD SSO wilt configureren en testen met SignalFx, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[SIGNALFX SSO configureren](#configure-signalfx-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een SignalFx-test gebruiker](#create-signalfx-test-user)** -om een equivalent van B. Simon in SignalFx te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **SignalFx** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **eenmalige aanmelding met SAML instellen** de waarden in voor de volgende velden:

    a. In het tekstvak **Id** typt u een URL: `https://api.signalfx.com/v1/saml/metadata`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://api.signalfx.com/v1/saml/acs/<integration ID>`

    > [!NOTE]
    > De voor gaande waarde is geen echte waarde. U werkt de waarde bij met de daad werkelijke antwoord-URL, die verderop in de zelf studie wordt uitgelegd.

1. De SignalFx-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Daarnaast verwacht SignalFx toepassing nog maar weinig kenmerken die worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    | Name |  Bronkenmerk|
    | ------------------- | -------------------- |
    | User.FirstName  | user.givenname |
    | Gebruiker. e-mail adres  | user.mail |
    | PersonImmutableID       | user.userprincipalname    |
    | User.LastName       | user.surname    |

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **SignalFx instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan SignalFx.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **SignalFx**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-signalfx-sso"></a>SignalFx SSO configureren

1. Meld u als beheerder aan bij de SignalFx-bedrijfs site.

1. Klik in SignalFx bovenaan op **integraties** om de pagina integraties te openen.

    ![SignalFx-integratie](./media/signalfx-tutorial/tutorial_signalfx_intg.png)

1. Klik op **Azure Active Directory** tegel onder de sectie **aanmeldings Services** .

    ![SignalFx SAML](./media/signalfx-tutorial/tutorial_signalfx_saml.png)

1. Klik op **nieuwe integratie** en voer onder het tabblad **installeren** de volgende stappen uit:

    ![SignalFx samlintgpage](./media/signalfx-tutorial/tutorial_signalfx_azure.png)

    a. In het tekstvak **naam** typt u een nieuwe naam voor de integratie, zoals **OurOrgName SAML SSO**.

    b. Kopieer de waarde van de **integratie-id** en voeg deze toe aan de **antwoord-URL** op de plaats van `<integration ID>` in het tekstvak **antwoord-URL** van de sectie basis- **SAML-configuratie** in azure Portal.

    c. Klik op **Upload File** om het met **Base64 gecodeerde certificaat** te uploaden dat is gedownload van Azure Portal in het tekstvak **certificaat** .

    d. Plak in het tekstvak **URL van uitgever** de waarde van de **Azure ad-id**, die u hebt gekopieerd van de Azure Portal.

    e. Plak in het tekstvak **URL voor meta gegevens** de **aanmeldings-URL** die u hebt gekopieerd uit de Azure Portal.

    f. Klik op **Opslaan**.

### <a name="create-signalfx-test-user"></a>SignalFx-test gebruiker maken

Het doel van deze sectie is het maken van een gebruiker met de naam Julia Simon in SignalFx. SignalFx biedt ondersteuning voor Just-in-time-inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Er wordt een nieuwe gebruiker gemaakt tijdens een poging om toegang te krijgen tot SignalFx als deze nog niet bestaat.

Wanneer een gebruiker zich voor de eerste keer aanmeldt bij SignalFx van de SAML SSO, verzendt het [ondersteunings team van SignalFx](mailto:kmazzola@signalfx.com) een e-mail met een koppeling waarop ze moeten klikken om te verifiëren. Dit gebeurt alleen de eerste keer dat de gebruiker zich aanmeldt. bij volgende aanmeldings pogingen is geen e-mail validatie vereist.

> [!Note]
> Als u hand matig een gebruiker moet maken, neemt u contact op met het [ondersteunings team van SignalFx](mailto:kmazzola@signalfx.com)

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SignalFx in het toegangs venster klikt, moet u automatisch worden aangemeld bij de SignalFx waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer SignalFx met Azure AD](https://aad.portal.azure.com/)