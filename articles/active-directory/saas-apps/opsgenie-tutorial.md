---
title: 'Zelf studie: integratie Azure Active Directory met OpsGenie | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en OpsGenie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: 8cf82827258b3888f4c30ad39a395d697a518a32
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81261092"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-opsgenie"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met OpsGenie

In deze zelf studie leert u hoe u OpsGenie integreert met Azure Active Directory (Azure AD). Wanneer u OpsGenie integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot OpsGenie.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij OpsGenie met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* OpsGenie-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* OpsGenie ondersteunt door **IDP** GEÏNITIEERDe SSO
* Nadat u OpsGenie hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessie beheer wordt uitgebreid vanuit voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-opsgenie-from-the-gallery"></a>OpsGenie toevoegen uit de galerie

Als u de integratie van OpsGenie in azure AD wilt configureren, moet u OpsGenie uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **OpsGenie** in het zoekvak.
1. Selecteer **OpsGenie** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-opsgenie"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor OpsGenie

Azure AD SSO met OpsGenie configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in OpsGenie.

Als u Azure AD SSO wilt configureren en testen met OpsGenie, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[OPSGENIE SSO configureren](#configure-opsgenie-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een OpsGenie-test gebruiker](#create-opsgenie-test-user)** -om een equivalent van B. Simon in OpsGenie te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **OpsGenie** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. Typ in het tekstvak **id** een URL met het volgende patroon:`https://app.opsginie.com/auth/saml/<UNIQUEID>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://app.opsginie.com/auth/saml?id=<UNIQUEID>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de daad werkelijke id en de antwoord-URL, die later in deze zelf studie wordt uitgelegd.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

1. Op de sectie **OpsGenie instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan OpsGenie.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **OpsGenie**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-opsgenie-sso"></a>OpsGenie SSO configureren

1. Open een ander browser exemplaar en meld u vervolgens aan bij OpsGenie als beheerder.

2. Klik op **instellingen**en klik vervolgens op het tabblad **eenmalige aanmelding** .
   
    ![OpsGenie eenmalige aanmelding](./media/opsgenie-tutorial/tutorial-opsgenie-06.png)

3. Selecteer **ingeschakeld**om SSO in te scha kelen.
   
    ![OpsGenie-instellingen](./media/opsgenie-tutorial/tutorial-opsgenie-07.png) 

4. Klik in de sectie **provider** op het tabblad **Azure Active Directory** .
   
    ![OpsGenie-instellingen](./media/opsgenie-tutorial/tutorial-opsgenie-08.png) 

5. Voer de volgende stappen uit op de pagina Azure Active Directory-dialoog venster:
   
    ![OpsGenie-instellingen](./media/opsgenie-tutorial/tutorial-opsgenie-09.png)
    
    a. Kopieer de **URI-waarde van de App-ID** en plak deze in het tekstvak **id (Entiteits-ID)** in het gedeelte **basis configuratie van SAML** in de Azure Portal.

    a. Kopieer de waarde van de **antwoord-URL** en plak deze in het tekstvak **antwoord-URL** in het gedeelte basis- **SAML-configuratie** in de Azure Portal.

    a. Plak in het tekstvak **SAML 2,0 endpoint** de waarde van de **aanmeldings-URL**die u hebt gekopieerd uit de Azure Portal.
    
    b. Plak in het tekstvak **URL voor meta** gegevens de URL-waarde voor **app federatieve meta gegevens** die u hebt gekopieerd uit de Azure Portal.
    
    c. Schakel de wissel knop **eenmalige aanmelding inschakelen** in om SSO in te scha kelen.

    d. Klik op **SSO-instellingen Toep assen**.

### <a name="create-opsgenie-test-user"></a>OpsGenie-test gebruiker maken

Het doel van deze sectie is het maken van een gebruiker met de naam B. Simon in OpsGenie. 

1. Meld u in een webbrowser venster als beheerder aan bij uw OpsGenie-Tenant.

2. Navigeer naar gebruikers lijst door te klikken op **gebruikers** in het linkerdeel venster.
   
    ![OpsGenie-instellingen](./media/opsgenie-tutorial/tutorial-opsgenie-10.png) 

3. Klik op **gebruiker toevoegen**.

4. Voer in het dialoogvenster **Add User** de volgende stappen uit:
   
    ![OpsGenie-instellingen](./media/opsgenie-tutorial/tutorial-opsgenie-11.png)
   
    a. Typ in het tekstvak **e-mail** het e-mail adres B. Simon, geadresseerd in azure Active Directory.
   
    b. Typ **B. Simon**in het tekstvak **volledige naam** .
   
    c. Klik op **Opslaan**. 

> [!NOTE]
> B. Simon ontvangt een e-mail bericht met instructies voor het instellen van het profiel.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel OpsGenie in het toegangs venster klikt, moet u automatisch worden aangemeld bij de OpsGenie waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer OpsGenie met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)