---
title: 'Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Sonarqube | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Sonarqube.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b01665e7-c3d0-4393-9286-d5bcf8cf6add
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b9e28f986f28bde6e46319ddb404d424c100726
ms.sourcegitcommit: f2771ec28b7d2d937eef81223980da8ea1a6a531
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/20/2019
ms.locfileid: "71174643"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sonarqube"></a>Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Sonarqube

In deze zelf studie leert u hoe u Sonarqube integreert met Azure Active Directory (Azure AD). Wanneer u Sonarqube integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Sonarqube.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Sonarqube met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Sonarqube-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Sonarqube ondersteunt door **SP** GEÏNITIEERDe SSO

> [!NOTE]
> De id van deze toepassing is een vaste teken reeks waarde zodat slechts één exemplaar in één Tenant kan worden geconfigureerd.

## <a name="adding-sonarqube-from-the-gallery"></a>Sonarqube toevoegen uit de galerie

Als u de integratie van Sonarqube in azure AD wilt configureren, moet u Sonarqube uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Sonarqube** in het zoekvak.
1. Selecteer **Sonarqube** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sonarqube"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Sonarqube

Azure AD SSO met Sonarqube configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Sonarqube.

Als u Azure AD SSO wilt configureren en testen met Sonarqube, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[SONARQUBE SSO configureren](#configure-sonarqube-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een Sonarqube-test gebruiker](#create-sonarqube-test-user)** -om een equivalent van B. Simon in Sonarqube te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **Sonarqube** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    Typ een URL in het tekstvak **URL voor aanmelding** :

    * **Voor productie omgeving**

        `https://servicessonar.corp.microsoft.com/`

    * **Voor de ontwikkel omgeving**

        `https://servicescode-dev.westus.cloudapp.azure.com`

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De downloadkoppeling certificaat](common/certificatebase64.png)

1. Op de sectie **Sonarqube instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Maak een testgebruiker Azure AD

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** aan de bovenkant van het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam** `B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Sonarqube.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Sonarqube**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling 'Gebruikers en groepen'](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-sonarqube-sso"></a>Sonarqube SSO configureren

1. Open een nieuw webbrowser venster en meld u aan bij de Sonarqube-bedrijfs site als beheerder.

2. Installeer de SAML-invoeg toepassing vanaf de sonarqube-markt plaats.

3. Klik in de linkerbovenhoek van de pagina op **beheerder** en navigeer vervolgens naar **SAML**.

4. Voer op de pagina **SAML** de volgende stappen uit:

    ![Sonarqube-configuratie](./media/sonarqube-tutorial/config01.png)

    a. Schakel de optie **ingeschakeld** in op **Ja**.

    b. Voer in het tekstvak **toepassings-id** de naam in, zoals **sonarqube**.

    c. Voer in het tekstvak **naam van provider** de naam in, zoals **SAML**.

    d. Plak in het tekstvak **provider-id** de waarde van de **Azure ad-id**die u van Azure Portal hebt gekopieerd.

    e. Plak in het tekstvak **SAML-aanmeldings-URL** de waarde van de AANMELDINGS- **URL**die u van Azure Portal hebt gekopieerd.

    f. Open het met base64 gecodeerde certificaat in Klad blok, kopieer de inhoud en plak het in het tekstvak **provider certificaat** .

    g. Voer in het tekstvak **SAML-gebruikers aanmelding kenmerk** de waarde `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`in.

    h. Voer in het tekstvak **gebruikers naam kenmerk SAML** de waarde `http://schemas.microsoft.com/identity/claims/displayname`in.

    i. Voer in het tekstvak **SAML-gebruikers-e-mail kenmerk** de waarde `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`in.

    j. Klik op **Opslaan**.

### <a name="create-sonarqube-test-user"></a>Sonarqube-test gebruiker maken

In deze sectie maakt u een gebruiker met de naam B. Simon in Sonarqube. Werk samen met het [Sonarqube-client ondersteunings team](https://www.sonarsource.com/support/) om de gebruikers toe te voegen in het Sonarqube-platform. Gebruikers moeten worden gemaakt en worden geactiveerd voordat u eenmalige aanmelding gebruiken. 

## <a name="test-sso"></a>SSO testen 

In deze sectie maakt testen u uw Azure AD eenmalige aanmelding configuratie met behulp van het toegangsvenster.

Wanneer u op de tegel Sonarqube in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Sonarqube waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Sonarqube met Azure AD](https://aad.portal.azure.com/)

