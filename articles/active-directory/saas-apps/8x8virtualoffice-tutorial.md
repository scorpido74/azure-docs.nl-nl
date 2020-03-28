---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met 8x8 | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en 8x8.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/20/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9c598222978a1c831be6f5e9db9eb87b2d6b6b96
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78968660"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-8x8"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met 8x8

In deze zelfstudie leert u hoe u 8x8 integreert met Azure Active Directory (Azure AD). Wanneer u 8x8 integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot 8x8.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij 8x8 met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Een 8x8 abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* 8x8 ondersteunt **SP en IDP** gestart met SSO

* Zodra u 8x8 configureert, u sessiecontrole afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebeheer strekt zich uit van Voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> Id van deze toepassing is een vaste tekenreekswaarde, zodat slechts één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-8x8-from-the-gallery"></a>8x8 toevoegen vanuit de galerie

Als u de integratie van 8x8 in Azure AD wilt configureren, moet u 8x8 uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **8x8** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **8x8** van het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-8x8"></a>Azure AD-eenmaligaanmelding configureren en testen voor 8x8

Azure AD SSO configureren en testen met 8x8 met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in 8x8.

Als u Azure AD SSO met 8x8 wilt configureren en testen, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer 8x8 SSO](#configure-8x8-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Maak 8x8-testgebruiker](#create-8x8-test-user)** - om een tegenhanger van B.Simon in 8x8 te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **8x8-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. Typ een URL in het tekstvak **Id:**`https://sso.8x8.com/saml2`

    b. Typ een URL in het tekstvak **URL beantwoorden:**`https://sso.8x8.com/saml2`

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer. U gebruikt het certificaat later in de zelfstudie in de sectie **8x8 SSO configureren.**

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **8x8 instellen** de URL(s) en u gebruikt deze URL-waarden later in de zelfstudie.

    ![Configuratie-URL's kopiëren](./media/8x8virtualoffice-tutorial/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot 8x8.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **8x8**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-8x8-sso"></a>8x8 SSO configureren

Het volgende deel van de tutorial hangt af van wat voor soort abonnement je hebt met 8x8.

* Voor klanten uit de 8x8-edities en de X-serie die Configuratiebeheer gebruiken voor beheer, raadpleegt u [8x8 configuratiebeheer configureren.](#configure-8x8-configuration-manager)
* Raadpleeg [8x8-accountmanager configureren](#configure-8x8-account-manager)voor Virtual Office-klanten die Accountmanager gebruiken voor beheer.

### <a name="configure-8x8-configuration-manager"></a>8x8-configuratiebeheer configureren

1. Log in bij 8x8 [Configuration Manager](https://vo-cm.8x8.com/).

1. Klik op de startpagina op **Identiteitsbeheer**.

    ![8x8 Configuratiebeheer](./media/8x8virtualoffice-tutorial/configure1.png)

1. Schakel **Eenmalige aanmelding (SSO)** in en selecteer **Microsoft Azure AD**.

    ![8x8 Configuratiebeheer](./media/8x8virtualoffice-tutorial/configure2.png)

1. Kopieer de drie URL's en het ondertekeningscertificaat vanaf de pagina **Eén aanmelding instellen met SAML** in Azure AD naar de sectie **Microsoft Azure AD SAML-instellingen** in 8x8 Configuration Manager.

    ![8x8 Configuratiebeheer](./media/8x8virtualoffice-tutorial/configure3.png)

    a. URL **van aanmelding kopiëren** naar de URL van **IDP-aanmelding**.

    b. **Azure AD-id kopiëren** naar **URL/URN van IDP Issuer**.

    c. URL **van afmelden** kopiëren naar **de URL van IDP-afmelding**.

    d. Download **certificaat (Base64)** en upload naar **certificaat.**

    e. Klik op **Opslaan**.

### <a name="configure-8x8-account-manager"></a>8x8-accountmanager configureren

1. Meld u als beheerder aan bij uw 8x8 Virtual Office-tenant.

1. Selecteer **Virtual Office-accountbeheer** in het toepassingsvenster.

    ![Configureren aan de toepassingszijde](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

1. Selecteer het **bedrijfs**account dat u wilt beheren en klik op **Aanmelden**.

    ![Configureren aan de toepassingszijde](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

1. Klik op het tabblad **ACCOUNTS** in de menulijst.

    ![Configureren aan de toepassingszijde](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

1. Klik op **Eenmalige aanmelding** in de lijst met accounts.

    ![Configureren aan de toepassingszijde](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

1. Selecteer **Eenmalige aanmelding** bij de verificatiemethoden en klik op **SAML**.

    ![Configureren aan de toepassingszijde](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

1. In het gedeelte voor **SAML-eenmalige aanmelding** voert u de volgende stappen uit:

    ![Configureren aan de toepassingszijde](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

    a. Plak in het tekstvak voor de **URL voor eenmalige aanmelding** de waarde van de **aanmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    b. Plak in het tekstvak voor de **afmeldings-URL** de waarde van de **afmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    c. Plak in het tekstvak voor de **certificaatverlener** de waarde van de **Azure AD-id** die u uit de Azure-portal hebt gekopieerd.

    d. Klik op **Bladeren** om het certificaat te uploaden dat u uit de Azure-portal hebt gedownload.

    e. Klik op de knop **Opslaan**.

### <a name="create-8x8-test-user"></a>8x8-testgebruiker maken

In deze sectie maakt u een gebruiker genaamd Britta Simon in 8x8. Werk samen met [8x8 support team](https://www.8x8.com/about-us/contact-us) om de gebruikers toe te voegen in het 8x8 platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de 8x8-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de 8x8 waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer 8x8 met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Hoe 8x8 te beschermen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)