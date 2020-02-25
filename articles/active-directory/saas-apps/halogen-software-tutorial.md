---
title: 'Zelf studie: integratie Azure Active Directory met Saba TalentSpace | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Saba TalentSpace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/20/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76c13a649e1c1888a11e02b83d969255615cdc67
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77561328"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-talentspace"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Saba TalentSpace

In deze zelf studie leert u hoe u Saba TalentSpace integreert met Azure Active Directory (Azure AD). Wanneer u Saba TalentSpace integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Saba TalentSpace.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Saba TalentSpace met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Saba TalentSpace-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Saba TalentSpace ondersteunt door **SP** GEÏNITIEERDe SSO
* Nadat u Saba TalentSpace hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie beheer is uitgebreid met voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-saba-talentspace-from-the-gallery"></a>Saba TalentSpace toevoegen vanuit de galerie

Als u de integratie van Saba TalentSpace wilt configureren in azure AD, moet u Saba TalentSpace van de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Saba TalentSpace** in het zoekvak.
1. Selecteer **Saba TalentSpace** in het resultaten paneel en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-saba-talentspace"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Saba TalentSpace

Azure AD SSO configureren en testen met Saba TalentSpace met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Saba TalentSpace.

Als u Azure AD SSO wilt configureren en testen met Saba TalentSpace, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Saba TALENTSPACE SSO configureren](#configure-saba-talentspace-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak Saba TalentSpace test User](#create-saba-talentspace-test-user)** -als u een tegen hanger van B. Simon in Saba TalentSpace wilt hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **Saba TalentSpace** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://global.hgncloud.com/[companyname]/saml/login`

    b. In het tekstvak **Id (entiteits-id)** typt u een URL met het volgende patroon: `https://global.hgncloud.com/[companyname]/saml/metadata`

    c. Typ in het tekstvak **antwoord-URL (assertion Consumer Service-URL)** een URL met het volgende patroon: `https://global.hgncloud.com/[companyname]/saml/SSO`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met het [ondersteunings team van Saba TalentSpace](https://support.saba.com/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** naar de sectie **SAML-handtekeningcertificaat** en klik op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Op de **Saba TalentSpace instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Saba TalentSpace.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Saba TalentSpace**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-saba-talentspace-sso"></a>Saba TalentSpace SSO configureren

1. Meld u in een ander browser venster aan bij uw **Saba TalentSpace** -toepassing als beheerder.

2. Klik op het tabblad **Options**.
  
    ![Wat is Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-12.png)

3. Klik in het linkernavigatiedeelvenster op **SAML Configuration**.
  
    ![Wat is Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-13.png)

4. Voer de volgende stappen uit op de pagina **SAML Configuration**:

    ![Wat is Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-14.png)

    a. Selecteer bij **Unique Identifier** de waarde **NameID**.

    b. Selecteer bij **Unique Identifier Maps To** de waarde **Username**.
  
    c. Klik op **Browse**, selecteer het gedownloade metagegevensbestand dat u wilt uploaden, en selecteer vervolgens **Upload File**.

    d. Klik op **Run Test** om de configuratie te testen.

    > [!NOTE]
    > U moet wachten op het bericht '*de SAML-test is voltooid. Sluit dit venster*. Sluit vervolgens het geopende browservenster. Het selectievakje **SAML inschakelen** is pas beschikbaar als de test is voltooid.

    e. Selecteer **SAML inschakelen**.

    f. Klik op **Wijzigingen opslaan**.

### <a name="create-saba-talentspace-test-user"></a>Saba TalentSpace-test gebruiker maken

Het doel van deze sectie is het maken van een gebruiker met de naam Julia Simon in Saba TalentSpace.

**Als u een gebruiker met de naam Julia Simon in Saba TalentSpace wilt maken, voert u de volgende stappen uit:**

1. Meld u aan bij uw **Saba TalentSpace** -toepassing als beheerder.

2. Klik op het tabblad **User Center** tabblad en klik vervolgens op **Create User**.

    ![Wat is Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-300.png)  

3. Voer de volgende stappen uit in het dialoogvenster **New User**:

    ![Wat is Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-301.png)

    a. Typ in het tekstvak **voor naam** de voor naam van de gebruiker zoals **B**.

    b. Typ in het tekstvak **Last Name** de achternaam van de gebruiker, bijvoorbeeld **Simon**.

    c. Typ in het tekstvak **username** **B. Simon**, de gebruikers naam zoals in de Azure Portal.

    d. Typ een wacht woord voor B. Simon in het tekstvak **wacht woord** .

    e. Klik op **Opslaan**.

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Saba TalentSpace in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Saba TalentSpace waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Saba TalentSpace met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)