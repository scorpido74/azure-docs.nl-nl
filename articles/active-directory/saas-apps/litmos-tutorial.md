---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Litmos | Microsoft Documenten'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Litmos configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cfaae4bb-e8e5-41d1-ac88-8cc369653036
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/26/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a935ef6d14b4de67964c555e0ffa610bbe992459
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "70171564"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-litmos"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Litmos

In deze zelfstudie leert u hoe u Litmos integreren met Azure Active Directory (Azure AD). Wanneer u Litmos integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Litmos.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Litmos met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Abonnement op enkele aanmelding (SSO) van Litmos.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Litmos biedt ondersteuning voor door **IDP** geïnitieerde eenmalige aanmelding
* Litmos ondersteunt het **Just-In-Time** inrichten van gebruikers

## <a name="adding-litmos-from-the-gallery"></a>Litmos toevoegen vanuit de galerie

Om de integratie van Litmos te configureren in Azure AD, moet u Litmos vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Litmos** in het zoekvak in de sectie **Toevoegen in de galeriesectie.**
1. Selecteer **Litmos** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-litmos"></a>Azure AD-aanmelding voor Litmos configureren en testen

Azure AD SSO met Litmos configureren en testen met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Litmos.

Als u Azure AD SSO met Litmos wilt configureren en testen, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Litmos SSO](#configure-litmos-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Litmos-testgebruiker maken](#create-litmos-test-user)** - om een tegenhanger van B.Simon in Litmos te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Litmos-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **Eén aanmelding instellen met SAML** de waarden in voor de volgende velden:

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://<companyname>.litmos.com/account/Login`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<companyname>.litmos.com/integration/samllogin`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Deze waarden worden verderop in deze zelfstudie beschreven. U kunt ook contact opnemen met het [klantondersteuningsteam van Litmos](https://www.litmos.com/contact-us) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Litmos instellen** de juiste URL(s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Litmos.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Litmos** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-litmos-sso"></a>Litmos SSO configureren

1. Meld u in een andere browser als beheerder aan bij de bedrijfssite van Litmos.

2. Klik in de navigatiebalk aan de linkerkant op **Accounts**.

    ![De sectie Accounts in Litmos][22]

3. Klik op het tabblad **Integrations**.

    ![Het tabblad Integrations][23]

4. Blader op het tabblad **Integrations** omlaag naar **3rd Party Integrations** en klik op het tabblad **SAML 2.0**.

    ![De sectie SAML 2.0][24]

5. Kopieer de waarde onder **The SAML endpoint for litmos is:** en plak deze in het tekstvak **Antwoord-URL** in de sectie **Litmos-domein en -URL's** in de Azure-portal.

    ![SAML-eindpunt][26]

6. Voer de volgende stappen uit in **Litmos**:

    ![Stappen in Litmos][25]

    a. Klik op **Enable SAML**.

    b. Open in Kladblok het met Base 64 gecodeerde certificaat, kopieer de inhoud ervan naar het Klembord en plak deze vervolgens in het tekstvak **SAML x.509 Certificate**.

    c. Klik **op Wijzigingen opslaan**.

### <a name="create-litmos-test-user"></a>Een testgebruiker maken voor Litmos

Het doel van deze sectie is het maken van een gebruiker met de naam Britta Simon in Litmos. Litmos ondersteunt just-in-time inrichten. Dit betekent dat er indien nodig automatisch een gebruikersaccount wordt gemaakt wanneer er wordt geprobeerd om via het toegangsvenster toegang te krijgen tot de toepassing.

**Voer de volgende stappen uit om een gebruiker met de naam Britta Simon te maken in Litmos:**

1. Meld u in een andere browser als beheerder aan bij de bedrijfssite van Litmos.

2. Klik in de navigatiebalk aan de linkerkant op **Accounts**.

    ![De sectie Accounts in Litmos][22]

3. Klik op het tabblad **Integrations**.

    ![Het tabblad Integrations][23]

4. Blader op het tabblad **Integrations** omlaag naar **3rd Party Integrations** en klik op het tabblad **SAML 2.0**.

    ![SAML 2.0][24]

5. Selecteer **Autogenerate Users**.
  
    ![Automatisch gebruikers genereren][27]

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Litmos klikt, wordt u automatisch aangemeld bij de instantie van Litmos waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Litmos met Azure AD](https://aad.portal.azure.com/)

[21]: ./media/litmos-tutorial/tutorial_litmos_60.png
[22]: ./media/litmos-tutorial/tutorial_litmos_61.png
[23]: ./media/litmos-tutorial/tutorial_litmos_62.png
[24]: ./media/litmos-tutorial/tutorial_litmos_63.png
[25]: ./media/litmos-tutorial/tutorial_litmos_64.png
[26]: ./media/litmos-tutorial/tutorial_litmos_65.png
[27]: ./media/litmos-tutorial/tutorial_litmos_66.png