---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met 8X8 | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en 8X8.
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
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968660"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-8x8"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met 8X8

In deze zelf studie leert u hoe u 8X8 integreert met Azure Active Directory (Azure AD). Wanneer u 8X8 integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot 8X8.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij 8X8 met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Een 8X8-abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* 8X8 ondersteunt SSO die door **SP en IDP** is geïnitieerd

* Nadat u 8X8 hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie beheer is uitgebreid met voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> De id van deze toepassing is een vaste teken reeks waarde zodat slechts één exemplaar in één Tenant kan worden geconfigureerd.

## <a name="adding-8x8-from-the-gallery"></a>8X8 toevoegen uit de galerie

Als u de integratie van 8X8 in azure AD wilt configureren, moet u 8X8 uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **8X8** in het zoekvak.
1. Selecteer **8X8** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-8x8"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor 8X8

Azure AD SSO met 8X8 configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in 8X8.

Als u Azure AD SSO wilt configureren en testen met 8X8, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[8X8 SSO configureren](#configure-8x8-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een 8X8-test gebruiker](#create-8x8-test-user)** -om een equivalent van B. Simon in 8X8 te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **8X8** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Id** typt u een URL: `https://sso.8x8.com/saml2`

    b. In het tekstvak **Antwoord-URL** typt u een URL: `https://sso.8x8.com/saml2`

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan. U gebruikt het certificaat later in de zelf studie in de sectie **8X8 SSO configureren** .

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer de URL ('s) in het gedeelte **8X8 instellen** en gebruik deze URL-waarden verderop in de zelf studie.

    ![Configuratie-URL's kopiëren](./media/8x8virtualoffice-tutorial/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan 8X8.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **8X8**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-8x8-sso"></a>8X8 SSO configureren

Het volgende deel van de zelf studie is afhankelijk van het soort abonnement dat u hebt met 8X8.

* Zie [8x8 Configuration Manager configureren](#configure-8x8-configuration-manager)voor 8X8-edities en klanten van de X-reeks die Configuration Manager gebruiken voor beheer.
* Raadpleeg [Configure 8X8 account manager](#configure-8x8-account-manager)voor virtuele Office-klanten die account manager gebruiken voor beheer.

### <a name="configure-8x8-configuration-manager"></a>8X8 configureren Configuration Manager

1. Meld u aan bij 8X8 [Configuration Manager](https://vo-cm.8x8.com/).

1. Klik op de start pagina op **Identity Management**.

    ![8X8 Configuration Manager](./media/8x8virtualoffice-tutorial/configure1.png)

1. Controleer **eenmalige aanmelding (SSO)** en selecteer vervolgens **Microsoft Azure AD**.

    ![8X8 Configuration Manager](./media/8x8virtualoffice-tutorial/configure2.png)

1. Kopieer de drie Url's en het handtekening certificaat van de pagina **eenmalige aanmelding met SAML** configureren in azure AD in het gedeelte **Microsoft Azure AD SAML-instellingen** in 8X8 Configuration Manager.

    ![8X8 Configuration Manager](./media/8x8virtualoffice-tutorial/configure3.png)

    a. Kopieer de **aanmeldings-URL** naar de AANMELDINGS-URL van **IDP**.

    b. Kopieer de **Azure ad-id** naar de **URL/urn van de IDP-Uitgever**.

    c. Kopieer de **Afmeldings-URL** naar **IDP afmeldings-URL**.

    d. Down load het **certificaat (base64)** en upload het naar het **certificaat**.

    e. Klik op **Opslaan**.

### <a name="configure-8x8-account-manager"></a>8X8-account beheer configureren

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

### <a name="create-8x8-test-user"></a>8X8-test gebruiker maken

In deze sectie maakt u een gebruiker met de naam Julia Simon in 8X8. Werk samen met [8X8-ondersteunings team](https://www.8x8.com/about-us/contact-us) om de gebruikers toe te voegen in het 8X8-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel 8X8 in het toegangs venster klikt, moet u automatisch worden aangemeld bij de 8X8 waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer 8X8 met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [8X8 beveiligen met geavanceerde zicht baarheid en controles](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)