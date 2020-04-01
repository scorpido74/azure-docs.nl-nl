---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met LinkedIn Learning | Microsoft Documenten'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en LinkedIn Learning.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d5857070-bf79-4bd3-9a2a-4c1919a74946
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 15509866980cdf85f54fc03cb77eed36f83c982f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76983364"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-learning"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met LinkedIn Learning

In deze zelfstudie leert u hoe u LinkedIn Learning integreren met Azure Active Directory (Azure AD). Wanneer u LinkedIn Learning integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot LinkedIn Learning.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij LinkedIn Learning met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* LinkedIn Learning single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* LinkedIn Learning biedt ondersteuning voor door **SP en IDP** geïnitieerde eenmalige aanmelding
* LinkedIn Learning biedt ondersteuning voor **Just In Time**-gebruikersinrichting
* Zodra u LinkedIn Learning hebt geconfigureerd, u sessiebeheer afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebeheer strekt zich uit van Voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-linkedin-learning-from-the-gallery"></a>LinkedIn Learning vanuit de galerie toevoegen

Als u de integratie van LinkedIn Learning met Azure AD wilt configureren, moet u LinkedIn Learning vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **LinkedIn Leren** in het zoekvak in de sectie **Toevoegen vanuit de galerie.**
1. Selecteer **LinkedIn Learning** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-learning"></a>Azure AD-aanmelding voor LinkedIn Learning configureren en testen

Azure AD SSO configureren en testen met LinkedIn Learning met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in LinkedIn Learning.

Voer de volgende bouwstenen uit om Azure AD SSO te configureren en te testen met LinkedIn Learning:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[LinkedIn Learning SSO configureren](#configure-linkedin-learning-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[LinkedIn Learning-testgebruiker maken](#create-linkedin-learning-test-user)** - om een tegenhanger van B.Simon in LinkedIn Learning te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **LinkedIn** Learning-toepassingsintegratie de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

     a. Voer in het tekstvak **id** de **Entiteits-id** in die u hebt gekopieerd uit LinkedIn Portal. 

    b. Voer in het tekstvak **Antwoord-URL** de **ACS-URL (Assertion Consumer Service)** in die u uit LinkedIn Portal hebt gekopieerd.

    c. Als u de toepassing in de **SP geïnitieerde** modus wilt configureren, klikt u in het gedeelte **Standaard SAML-configuratie** waarin u de aanmeldings-URL opgeeft op **Extra URL's instellen**. Kopieer de **ACS-URL (Assertion Consumer Service)** en vervang /saml/ door /login/ om een kopie van de aanmeldings-URL te maken. Zodra dit is gebeurd, zou de aanmeldings-URL het volgende patroon moeten hebben:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Informatie over eenmalige aanmelding voor LinkedIn Learning-domein en -URL's](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Deze waarden zijn geen werkelijke waarden. U werkt deze waarden bij met de werkelijke URL-id en antwoord, die later wordt uitgelegd in de sectie **LinkedIn Learning SSO** configureren van de zelfstudie.

1. De LinkedIn Learning-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. In de toepassing LinkedIn Learning wordt verwacht dat **nameidentifier** is toegewezen met **user.mail**. Daarom moet u de toewijzing van het kenmerk bewerken door op het pictogram **Bewerken** te klikken en de toewijzing van het kenmerk te wijzigen.

    ![installatiekopie](common/edit-attribute.png)

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **LinkedIn-leren instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot LinkedIn Learning.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **LinkedIn Learning**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-linkedin-learning-sso"></a>LinkedIn Learning SSO configureren

1. Meld u in een ander browservenster als beheerder aan bij uw LinkedIn Learning-tenant.

2. Klik in **Accountcentrum** onder **Instellingen** op **Algemene instellingen**. Selecteer ook **Learning - standaard** uit de vervolgkeuzelijst.

    ![Eenmalige aanmelding configureren](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

3. Klik op **Of klik hier om afzonderlijke velden van het formulier te laden en kopiëren** en kopieer **Entiteits-id** en **ACS-URL (Assertion Consumer Service)** en plak deze in het gedeelte **Standaard SAML-configuratie** in Azure Portal.

    ![Eenmalige aanmelding configureren](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

4. Ga naar het gedeelte **met beheerdersinstellingen voor LinkedIn**. Upload het XML-bestand dat u hebt gedownload uit Azure Portal door op de optie **XML-bestand uploaden** te klikken.

    ![Eenmalige aanmelding configureren](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

5. Klik op **Aan** om SSO in te schakelen. De SSO-status verandert van **Niet verbonden** naar **Verbonden**

    ![Eenmalige aanmelding configureren](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-learning-test-user"></a>Testgebruiker voor LinkedIn Learning maken

De LinkedIn Learning-toepassing biedt ondersteuning voor Just in time-gebruikersinrichting. Na verificatie worden gebruikers automatisch aangemaakt in de toepassing. Ga naar het tabblad met beheerdersinstellingen in de LinkedIn Learning-portal en zet de schakelaar voor **automatisch toewijzen van licenties** op actieve Just in time-inrichting. Hiermee wordt ook een licentie aan de gebruiker toegewezen.

   ![Een Azure AD-testgebruiker maken](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel LinkedIn Learning in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van LinkedIn Learning waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer LinkedIn Learning met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)