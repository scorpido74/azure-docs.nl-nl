---
title: 'Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Litmos | Microsoft Docs'
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
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70171564"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-litmos"></a>Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Litmos

In deze zelf studie leert u hoe u Litmos integreert met Azure Active Directory (Azure AD). Wanneer u Litmos integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Litmos.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Litmos met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Litmos-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Litmos biedt ondersteuning voor door **IDP** geïnitieerde eenmalige aanmelding
* Litmos ondersteunt het **Just-In-Time** inrichten van gebruikers

## <a name="adding-litmos-from-the-gallery"></a>Litmos toevoegen vanuit de galerie

Om de integratie van Litmos te configureren in Azure AD, moet u Litmos vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Litmos** in het zoekvak.
1. Selecteer **Litmos** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-litmos"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Litmos

Azure AD SSO met Litmos configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Litmos.

Als u Azure AD SSO wilt configureren en testen met Litmos, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[LITMOS SSO configureren](#configure-litmos-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een Litmos-test gebruiker](#create-litmos-test-user)** -om een equivalent van B. Simon in Litmos te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **Litmos** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **eenmalige aanmelding met SAML instellen** de waarden in voor de volgende velden:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<companyname>.litmos.com/account/Login`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://<companyname>.litmos.com/integration/samllogin`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Deze waarden worden verderop in deze zelfstudie beschreven. U kunt ook contact opnemen met het [klantondersteuningsteam van Litmos](https://www.litmos.com/contact-us) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De downloadkoppeling certificaat](common/certificatebase64.png)

1. Op de sectie **Litmos instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Litmos.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **Litmos** in de lijst met toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling 'Gebruikers en groepen'](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

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

    c. Klik op **Wijzigingen opslaan**.

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

## <a name="test-sso"></a>SSO testen 

In deze sectie maakt testen u uw Azure AD eenmalige aanmelding configuratie met behulp van het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Litmos klikt, wordt u automatisch aangemeld bij de instantie van Litmos waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

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