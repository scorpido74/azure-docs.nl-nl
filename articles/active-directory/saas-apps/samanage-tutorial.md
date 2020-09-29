---
title: 'Zelfstudie: Azure Active Directory-integratie met SolarWinds Service Desk (voorheen Samanage) | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en SolarWinds Service Desk (voorheen Samanage).
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.openlocfilehash: 8d4c19e1ce10ed618cda167cd6fa7efedf4111d0
ms.sourcegitcommit: 7374b41bb1469f2e3ef119ffaf735f03f5fad484
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2020
ms.locfileid: "90707574"
---
# <a name="tutorial-azure-active-directory-integration-with-solarwinds-service-desk-previously-samanage"></a>Zelfstudie: Azure Active Directory-integratie met SolarWinds Service Desk (voorheen Samanage)

In deze zelfstudie leert u hoe u SolarWinds kunt integreren met Azure Active Directory (Azure AD).
De integratie van SolarWinds met Azure AD biedt u de volgende voordelen:

* U kunt bepalen in Azure AD wie toegang heeft tot SolarWinds.
* U kunt inschakelen dat gebruikers automatisch met hun Azure Active Directory-account worden aangemeld bij SolarWinds (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met SolarWinds Service Desk (voorheen Samanage) hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Samanage waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SolarWinds biedt ondersteuning voor met **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-solarwinds-from-the-gallery"></a>SolarWinds toevoegen uit de galerie

Om de integratie van SolarWinds te configureren in Azure AD, moet u SolarWinds vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om SolarWinds vanuit de galerie toe te voegen:**

1. Selecteer **Azure Active Directory** in **[Azure Portal](https://portal.azure.com)** in het navigatiepaneel aan de linkerkant.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **SolarWinds**, selecteer **SolarWinds** in het deelvenster met resultaten en klik vervolgens op **Toevoegen** om de toepassing toe te voegen.

     ![SolarWinds in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD-eenmalige aanmelding met SolarWinds op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Microsoft Azure Active Directory-gebruiker en de daaraan gerelateerde gebruiker in SolarWinds tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD met SolarWinds wilt configureren en testen, moet u de volgende stappen uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor SolarWinds Service Desk configureren](#configure-solarwinds-single-sign-on)** : de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een SolarWinds-testgebruiker maken](#create-solarwinds-test-user)** : als u een equivalent van B.Simon in SolarWinds wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding in Azure AD te configureren voor SolarWinds:

1. Selecteer in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **SolarWinds** de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Samanage](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke URL voor eenmalige aanmelding en de id, zoals later in de zelfstudie wordt uitgelegd. Neem voor meer informatie contact op met het [ondersteuningsteam van Samanage](https://www.samanage.com/support). U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **SolarWinds instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

<a name="configure-solarwinds-single-sign-on"></a>

### <a name="configure-solarwinds-service-desk-single-sign-on"></a>Eenmalige aanmelding voor SolarWinds Service Desk configureren

1. Meld u in een ander browservenster aan bij uw SolarWinds-bedrijfssite als beheerder.

2. Klik op **Dashboard** en selecteer **Setup** in het linker navigatievenster.
   
    ![Dashboard](./media/samanage-tutorial/tutorial_samanage_001.png "Dashboard")

3. Klik op **Single Sign-On**.
   
    ![Single Sign-On](./media/samanage-tutorial/tutorial_samanage_002.png "Eenmalige aanmelding")

4. Navigeer naar de sectie **Login using SAML** en voer de volgende stappen uit:
   
    ![Aanmelden met SAML](./media/samanage-tutorial/tutorial_samanage_003.png "Aanmelden met SAML")
 
    a. Klik op **Enable Single Sign-On with SAML**.  
 
    b. Plak de waarde van **Azure Ad-id**, die u hebt gekopieerd uit de Azure-portal, in het tekstvak **Identity Provider URL**.    
 
    c. Bevestig dat de **Login URL** overeenkomt met de **Aanmeldings-URL** van de sectie **Standaard SAML-configuratie** in de Azure-portal.
 
    d. Plak in het tekstvak **Logout URL** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit de Azure-portal.
 
    e. Typ in het tekstvak **SAML Issuer** de app-id-URI die is ingesteld in uw id-provider.
 
    f. Open in Kladblok het met Base 64 gecodeerde certificaat dat u hebt gedownload uit de Azure-portal, kopieer de inhoud ervan naar het Klembord en plak deze vervolgens in het tekstvak **Paste your Identity Provider x.509 Certificate below**.
 
    g. Klik op **Create users if they do not exist in SolarWinds**.
 
    h. Klik op **Update**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon\@yourcompanydomain.extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie zorgt u ervoor dat Britta Simon eenmalige aanmelding met Azure kan gebruiken door haar toegang te geven tot SolarWinds.

1. Selecteer in Azure Portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **SolarWinds**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **SolarWinds** in de lijst met toepassingen.

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-solarwinds-test-user"></a>Een SolarWinds-testgebruiker maken

Als u wilt dat gebruikers van Azure AD zich kunnen aanmelden bij SolarWinds, moeten ze worden ingericht voor SolarWinds.  
In het geval van SolarWinds is inrichten een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u bij uw SolarWinds-bedrijfssite aan als beheerder.

2. Klik op **Dashboard** en selecteer **Setup** in het linker navigatievenster.
   
    ![Installatie](./media/samanage-tutorial/tutorial_samanage_001.png "Instellen")

3. Klik op het tabblad **Users**
   
    ![Gebruikers](./media/samanage-tutorial/tutorial_samanage_006.png "Gebruikers")

4. Klik op **New User**.
   
    ![Nieuwe gebruiker](./media/samanage-tutorial/tutorial_samanage_007.png "Nieuwe gebruiker")

5. Typ bij **Name** en **Email Address** de naam en het e-mailadres van een Azure Active Directory-account dat u wilt inrichten, en klik op **Create user**.
   
    ![Create User](./media/samanage-tutorial/tutorial_samanage_008.png "Gebruiker maken")
   
   >[!NOTE]
   >De houder van het Azure Active Directory-account ontvangt een e-mail en volgt een koppeling om zijn account te bevestigen voordat het actief wordt. U kunt ook alle andere hulpprogramma's voor het creëren van SolarWinds-gebruikersaccounts of API's van SolarWinds gebruiken om Azure Active Directory-gebruikersaccounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel SolarWinds klikt, wordt u automatisch aangemeld bij de instantie van SolarWinds waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

