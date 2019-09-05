---
title: 'Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met DocuSign | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en DocuSign.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a691288b-84c1-40fb-84bd-5b06878865f0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe1f018612e889f49993895d88cdaf9ad732b393
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70306301"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-docusign"></a>Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met DocuSign

In deze zelf studie leert u hoe u DocuSign integreert met Azure Active Directory (Azure AD). Wanneer u DocuSign integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot DocuSign.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij DocuSign met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* DocuSign-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* DocuSign ondersteunt door **SP** GEÏNITIEERDe SSO

* DocuSign ondersteunt **just-in-time** -gebruikers inrichting

* DocuSign ondersteunt [automatische gebruikers inrichting](https://docs.microsoft.com/azure/active-directory/saas-apps/docusign-provisioning-tutorial)

## <a name="adding-docusign-from-the-gallery"></a>DocuSign toevoegen uit de galerie

Als u de integratie van DocuSign in azure AD wilt configureren, moet u DocuSign uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **DocuSign** in het zoekvak.
1. Selecteer **DocuSign** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-docusign"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor DocuSign

Azure AD SSO met DocuSign configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in DocuSign.

Als u Azure AD SSO wilt configureren en testen met DocuSign, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[DOCUSIGN SSO configureren](#configure-docusign-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een DocuSign-test gebruiker](#create-docusign-test-user)** -om een equivalent van B. Simon in DocuSign te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **DocuSign** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2/login/sp/<IDPID>`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<subdomain>.docusign.com/organizations/<OrganizationID>/saml2`

    > [!NOTE]
    > Dit zijn geen echte waarden. Als u deze waarden bijwerkt met de werkelijke aanmeldings-URL en de id, die later wordt uitgelegd, wordt de sectie **SAML 2,0-eind punten weer gegeven** in de zelf studie.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De downloadkoppeling certificaat](common/certificatebase64.png)

1. Op de sectie **DocuSign instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan DocuSign.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **DocuSign**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling 'Gebruikers en groepen'](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-docusign-sso"></a>DocuSign SSO configureren

1. Als u de configuratie wilt automatiseren in DocuSign, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **Setup DocuSign** gaat u naar de DocuSign-toepassing. Geef de beheerders referenties op om u aan te melden bij DocuSign. Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-5 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

3. Als u DocuSign hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij uw DocuSign-bedrijfs site als beheerder en voert u de volgende stappen uit:

4. Klik in de rechter bovenhoek van de pagina op profiel **logo** en klik vervolgens op **Ga naar beheerder**.
  
    ![Eenmalige aanmelding configureren][51]

5. Klik op de pagina domein oplossingen op **domeinen**

    ![Eenmalige aanmelding configureren][50]

6. Klik onder de sectie **domeinen** op **claim domein**.

    ![Eenmalige aanmelding configureren][52]

7. Typ in het tekstvak **domein naam** van het dialoog venster **een domein claim** het domein van uw bedrijf en klik vervolgens op **claim**. Zorg ervoor dat u het domein verifieert en of de status actief is.

    ![Eenmalige aanmelding configureren][53]

8. Klik op de pagina domein oplossingen op **id-providers**.
  
    ![Eenmalige aanmelding configureren][54]

9. Klik onder **id-providers** op **ID-provider toevoegen**. 

    ![Eenmalige aanmelding configureren][55]

10. Voer de volgende stappen uit op de pagina instellingen van de **identiteits provider** :

    ![Eenmalige aanmelding configureren][56]

    a. Typ in het tekstvak **naam** een unieke naam voor uw configuratie. Gebruik geen spaties.

    b. Plak in het tekstvak voor de uitgever van de **identiteits provider**de waarde van de **Azure ad-id**die u van Azure Portal hebt gekopieerd.

    c. Plak in het tekstvak **aanmeldings-URL van ID-provider** de waarde van de AANMELDINGS- **URL**die u hebt gekopieerd uit Azure Portal.

    d. Plak in het tekstvak **Afmeldings-URL van de identiteits provider** de waarde van de **afmeldings-URL**, die u van Azure Portal hebt gekopieerd.

    e. Selecteer **authn-aanvraag ondertekenen**.

    f. Selecteer als **verificatie aanvraag verzenden door** **post**.

    g. Selecteer **ophalen**bij **afmeldings aanvraag verzenden door**.

    h. Klik in de sectie **toewijzing van aangepast kenmerk** op **nieuwe toewijzing toevoegen**.

    ![Eenmalige aanmelding configureren][62]

    i. Kies het veld dat u wilt toewijzen aan de Azure AD-claim. In dit voor beeld wordt de claim **EmailAddress** toegewezen aan de waarde van **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress** . Het is de standaard naam van de claim van Azure AD voor de e-mail claim en klik vervolgens op **Opslaan**.

    ![Eenmalige aanmelding configureren][57]

    > [!NOTE]
    > Gebruik de juiste **gebruikers-id** om de gebruiker toe te wijzen vanuit Azure AD aan DocuSign-gebruikers toewijzing. Selecteer het juiste veld en voer de juiste waarde in op basis van de instellingen van uw organisatie.

    j. Klik in de sectie **identiteits provider certificaten** op **certificaat toevoegen**en upload het certificaat dat u hebt gedownload van de Azure AD-Portal en klik op **Opslaan**.

    ![Eenmalige aanmelding configureren][58]

    k. Klik in de sectie **id-providers** op **acties**en klik vervolgens op **eind punten**.

    ![Eenmalige aanmelding configureren][59]

    l. Voer de volgende stappen uit in de sectie **SAML 2,0-eind punten weer geven** in de **Beheer Portal van DocuSign**:

    ![Eenmalige aanmelding configureren][60]

    * Kopieer de URL van de uitgever van de **service provider**en plak deze in het tekstvak **id** in de sectie **basis configuratie van SAML** op het Azure Portal.

    * Kopieer de **aanmeldings-URL van de service provider**en plak deze in het TEKSTVAK **aanmeld URL** in het gedeelte **basis configuratie van SAML** in het Azure Portal.

    * Klik op **sluiten**

### <a name="create-docusign-test-user"></a>DocuSign-test gebruiker maken

In deze sectie wordt een gebruiker met de naam B. Simon gemaakt in DocuSign. DocuSign biedt ondersteuning voor Just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in DocuSign, wordt er een nieuwe gemaakt na verificatie.

>[!Note]
>Als u hand matig een gebruiker moet maken, neemt u contact op met het [ondersteunings team van DocuSign](https://support.docusign.com/).

## <a name="test-sso"></a>SSO testen 

In deze sectie maakt testen u uw Azure AD eenmalige aanmelding configuratie met behulp van het toegangsvenster.

Wanneer u op de tegel DocuSign in het toegangs venster klikt, moet u automatisch worden aangemeld bij de DocuSign waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer DocuSign met Azure AD](https://aad.portal.azure.com/)

<!--Image references-->

[50]: ./media/docusign-tutorial/tutorial_docusign_18.png
[51]: ./media/docusign-tutorial/tutorial_docusign_21.png
[52]: ./media/docusign-tutorial/tutorial_docusign_22.png
[53]: ./media/docusign-tutorial/tutorial_docusign_23.png
[54]: ./media/docusign-tutorial/tutorial_docusign_19.png
[55]: ./media/docusign-tutorial/tutorial_docusign_20.png
[56]: ./media/docusign-tutorial/tutorial_docusign_24.png
[57]: ./media/docusign-tutorial/tutorial_docusign_25.png
[58]: ./media/docusign-tutorial/tutorial_docusign_26.png
[59]: ./media/docusign-tutorial/tutorial_docusign_27.png
[60]: ./media/docusign-tutorial/tutorial_docusign_28.png
[61]: ./media/docusign-tutorial/tutorial_docusign_29.png
[62]: ./media/docusign-tutorial/tutorial_docusign_30.png