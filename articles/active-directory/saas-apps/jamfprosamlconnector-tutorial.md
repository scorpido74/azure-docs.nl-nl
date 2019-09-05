---
title: 'Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Jamf Pro | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Splunk Enterprise en Jamf Pro.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 24f0d067351ab2e24b103624c8534ac8d6f2e757
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70305442"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-jamf-pro"></a>Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Jamf Pro

In deze zelf studie leert u hoe u Jamf Pro integreert met Azure Active Directory (Azure AD). Wanneer u Jamf Pro integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Jamf Pro.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Jamf Pro met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Eenmalige aanmelding (SSO) met Jamf Pro-abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Jamf Pro ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-jamf-pro-from-the-gallery"></a>Jamf Pro uit de galerie toevoegen

Voor het configureren van de integratie van Jamf Pro in Azure AD, moet u Jamf Pro uit de galerie aan uw lijst met beheerde SaaS-apps toevoegen.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Jamf Pro** in het zoekvak.
1. Selecteer **Jamf Pro** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-jamf-pro"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Jamf Pro

Azure AD SSO met Jamf Pro configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Jamf Pro.

Als u Azure AD SSO wilt configureren en testen met Jamf Pro, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Jamf Pro SSO configureren](#configure-jamf-pro-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een Jamf Pro-test gebruiker](#create-jamf-pro-test-user)** -om een soort tegen te brengen van B. Simon in Jamf Pro dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **Jamf Pro** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<subdomain>.jamfcloud.com/saml/SSO`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en aanmeldings-URL. U vindt de werkelijke id-waarde van de sectie **eenmalige aanmelding** in de Jamf Pro-portal, op de manier die verderop in de zelfstudie wordt uitgelegd. U kunt de werkelijke **subdomein**-waarde uit de id-waarde extraheren en deze **subdomein**-informatie gebruiken in de aanmeldings-URL en antwoord-URL. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Klik op de pagina **eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekening certificaat** op de knop kopiëren om de URL van de **app Federation-meta gegevens** te kopiëren en op uw computer op te slaan.

    ![De downloadkoppeling certificaat](common/copy-metadataurl.png)

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Jamf Pro.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst met toepassingen **Jamf Pro**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling 'Gebruikers en groepen'](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-jamf-pro-sso"></a>Jamf Pro SSO configureren

1. Als u de configuratie met Jamf Pro wilt automatiseren, moet u **My Apps-browserextensie voor veilig aanmelden** installeren door op **De extensie installeren** te klikken.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

2. Als u op **Jamf Pro instellen** klikt nadat u de extensie aan de browser hebt toegevoegd, wordt u doorgestuurd naar de Jamf Pro-toepassing. Geef hier de referenties voor de beheerder op om u aan te melden bij Jamf Pro. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3-7 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

3. Als u Jamf Pro hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij uw Jamf Pro-bedrijfs site als beheerder en voert u de volgende stappen uit:

4. Klik op het pictogram **Instellen** in de rechterbovenhoek van de pagina.

    ![Configuratie van Jamf Pro](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Klik op **Eenmalige aanmelding**.

    ![Configuratie van Jamf Pro](./media/jamfprosamlconnector-tutorial/configure2.png)

6. Voer op de pagina **Eenmalige aanmelding** de volgende stappen uit:

    ![Configuratie van Jamf Pro](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Schakel de **verificatie voor eenmalige aanmelding inschakelen in**.

    b. Selecteer **anders** als een optie in de vervolg keuzelijst **ID-provider** .

    c. Voer in het tekstvak **Andere provider** **Azure AD** in.

    d. Kopieer de waarde van de **entiteit-id** en plak deze in het tekstvak **id (Entiteits-ID)** in de sectie **basis configuratie van SAML** op Azure Portal.

    > [!NOTE]
    > Hier `<SUBDOMAIN>` moet u deze waarde gebruiken om de aanmeldings-URL en de antwoord-URL te volt ooien in het gedeelte **basis configuratie van SAML** op Azure Portal.

    e. **URL voor meta gegevens** selecteren als een optie in de vervolg keuzelijst van de **meta gegevens bron van de identiteits provider** en in het volgende tekstvak, plak de URL-waarde van de **app Federation-meta gegevens** die u hebt gekopieerd uit de Azure Portal.

7. Ga op dezelfde pagina omlaag naar de sectie **gebruikers toewijzing** en voer de volgende stappen uit: 

    ![Eenmalige aanmelding bij Jamf Pro](./media/jamfprosamlconnector-tutorial/tutorial_jamfprosamlconnector_single.png)

    a. Selecteer de optie **NameID** voor de **gebruikers toewijzing**van de ID-provider. Deze instelling is standaard ingesteld op **NameID**, maar u kunt een aangepast attribuut definiëren.

    b. Selecteer **e-mail** voor **JAMF Pro-gebruikers toewijzing**. Jamf Pro wijst SAML-kenmerken die door de IdP zijn verzonden op de volgende manieren toe: aan de gebruikers en aan groepen. Wanneer een gebruiker probeert toegang te krijgen tot Jamf Pro, krijgt Jamf Pro standaard informatie over de gebruiker van de id-provider en vergelijkt deze met de Jamf Pro-gebruikersaccounts. Als het binnenkomende gebruikersaccount niet in Jamf Pro bestaat, wordt de bijpassende groepsnaam gezocht.

    c. Plak de waarde `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` in het tekstvak **kenmerk naam van ID-provider** .

    d. Als u **gebruikers toestaan wilt overs Laan de authenticatie gebruikers voor eenmalige aanmelding** niet worden omgeleid naar de aanmeldings pagina van de identiteits provider voor verificatie, maar u kunt zich rechtstreeks aanmelden bij Jamf Pro. Wanneer een gebruiker toegang probeert te krijgen tot Jamf Pro via de id-provider, vindt IdP-geïnitieerde SSO-verificatie en -autorisatie plaats.

    e. Klik op **Opslaan**.

### <a name="create-jamf-pro-test-user"></a>Testgebruiker voor Jamf Pro maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij Jamf Pro, moeten ze worden ingericht in Jamf Pro. In het geval van Jamf Pro is inrichten een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan bij uw Jamf Pro-bedrijfs site als beheerder.

2. Klik op het pictogram **Instellen** in de rechterbovenhoek van de pagina.

    ![Werknemer toevoegen](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Klik op **Jamf Pro-gebruikersaccounts en groepen**.

    ![Werknemer toevoegen](./media/jamfprosamlconnector-tutorial/user1.png)

4. Klik op **Nieuw**.

    ![Werknemer toevoegen](./media/jamfprosamlconnector-tutorial/user2.png)

5. Selecteer **Standaard account maken**.

    ![Werknemer toevoegen](./media/jamfprosamlconnector-tutorial/user3.png)

6. Klik in het dialoogvenster **Nieuw account** en voer de volgende stappen uit:

    ![Werknemer toevoegen](./media/jamfprosamlconnector-tutorial/user4.png)

    a. Typ in het tekstvak **Gebruikersnaam** de volledige naam van BrittaSimon.

    b. Selecteer de voor uw organisatie toepasselijke opties voor **toegangsniveau**, **bevoegdheden**  en **toegangsstatus**.

    c. Typ in het tekstvak **Volledige naam** de volledige naam van Britta Simon.

    d. Typ in het tekstvak **E-mailadres** het e-mailadres van het account van Britta Simon.

    e. Typ in het tekstvak **Wachtwoord** het wachtwoord van de gebruiker.

    f. Typ in het tekstvak **Wachtwoord bevestigen** het wachtwoord van de gebruiker.

    g. Klik op **Opslaan**.

## <a name="test-sso"></a>SSO testen 

In deze sectie maakt testen u uw Azure AD eenmalige aanmelding configuratie met behulp van het toegangsvenster.

Wanneer u op de tegel Jamf Pan in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Jamf Pro waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Jamf Pro met Azure AD](https://aad.portal.azure.com/)

