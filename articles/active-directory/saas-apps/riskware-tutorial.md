---
title: 'Zelfstudie: Integratie met riskware Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en riskware.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 81866167-b163-4695-8978-fd29a25dac7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 6eaa1be81d3ac0733c0829bc45e1b62f8aae5755
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72027113"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Zelfstudie: Integratie met riskware Azure Active Directory

In deze zelf studie leert u hoe u riskware integreert met Azure Active Directory (Azure AD).
Het integreren van riskware met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot Riskware.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij riskware (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts in één centrale locatie - Azure portal beheren.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met riskware wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* Abonnement voor eenmalige aanmelding riskware ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Riskware ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-riskware-from-the-gallery"></a>Riskware toevoegen uit de galerie

Als u de integratie van riskware in azure AD wilt configureren, moet u riskware uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om riskware toe te voegen uit de galerie:**

1. In de **[Azure-portal](https://portal.azure.com)** , klik in het navigatievenster aan de linkerkant op **Azure Active Directory** pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Nieuwe toepassing toevoegen, klikt u op **nieuwe toepassing** knop boven aan het dialoogvenster.

    ![De knop nieuwe toepassing](common/add-new-app.png)

4. Typ **riskware**in het zoekvak, selecteer **riskware** in het resultaten paneel en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![Riskware in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Configureren en Azure AD eenmalige aanmelding testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met riskware op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in riskware tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met Riskware, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**  : als u wilt dat uw gebruikers kunnen deze functie gebruiken.
2. **[Riskware eenmalige aanmelding configureren](#configure-riskware-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)**  - voor het testen van Azure AD eenmalige aanmelding met Britta Simon.
4. **[Toewijzen van de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)**  - Britta Simon gebruik van Azure AD eenmalige aanmelding inschakelen.
5. **[Maak een Riskware-test gebruiker](#create-riskware-test-user)** -om een equivalent van Julia Simon in riskware te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**  : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met riskware:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **riskware** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling voor eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor riskware domein en Url's](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon:
    
    | Omgeving| URL-patroon|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | REGEL| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | DEMO| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. Typ in het tekstvak **id (Entiteits-ID)** de URL:
    
    | Omgeving| URL-patroon|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | REGEL| `https://riskcloud.net/prod` |
    | DEMO| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > De waarde van de aanmeldings-URL is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteunings team van riskware](mailto:support@pansoftware.com.au) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De downloadkoppeling certificaat](common/metadataxml.png)

6. Kopieer op de sectie **riskware instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. URL voor afmelden

### <a name="configure-riskware-single-sign-on"></a>Eenmalige aanmelding voor riskware configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij uw riskware-bedrijfs site.

1. Klik rechtsboven op **onderhoud** om de pagina onderhoud te openen.

    ![Riskware-configuraties onderhouden](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Klik op de pagina onderhoud op **verificatie**.

    ![Riskware-configuratie authen](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. Voer de volgende stappen uit op de pagina **verificatie configuratie** :

    ![Riskware-configuratie authenconfig](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Selecteer **type** als **SAML** voor authenticatie.

    b. Typ in het tekstvak **code** de code zoals AZURE_UAT.

    c. Typ in het tekstvak **Beschrijving** uw beschrijving zoals Azure-configuratie voor SSO.

    d. Plak de waarde voor de **aanmeldings-URL** die u hebt gekopieerd van Azure Portal in het tekstvak voor de **pagina met eenmalige aanmelding** .

    e. Plak in tekstvak **Afmelden** de waarde van de **afmeldings-URL** , die u hebt gekopieerd uit Azure Portal.

    f. Typ in het tekstvak **bericht formulier veld** de naam van het veld in een post-antwoord met SAML zoals SAMLResponse

    g. Typ in het tekstvak **XML-identiteits code naam** het kenmerk, dat de unieke id bevat in het SAML-antwoord, zoals NameID.

    h. Open de gedownloade **meta gegevens-XML** van Azure Portal in Klad blok, kopieer het certificaat uit het meta gegevensbestand en plak het in het tekstvak **certificaat**

    i. Plak in het tekstvak **URL van consument** de waarde van de **antwoord-URL**, die u van het ondersteunings team ontvangt.

    j. Plak in het tekstvak van de **Uitgever** de waarde van **id**, die u van het ondersteunings team ontvangt.

    > [!Note]
    > Neem contact op met het [ondersteunings team van riskware](mailto:support@pansoftware.com.au) om deze waarden te verkrijgen

    k. Schakel het selectie vakje **post gebruiken** in.

    l. Selecteer het selectie vakje **SAML-aanvraag gebruiken** .

    m. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Maak een testgebruiker Azure AD

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon**in.
  
    b. Typ in het veld **gebruikers naam** `brittasimon@yourcompanydomain.extension`  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Riskware.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **riskware**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **riskware**.

    ![De koppeling riskware in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-riskware-test-user"></a>Riskware-test gebruiker maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij Riskware, moeten ze worden ingericht in Riskware. In riskware is inrichten een hand matige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan bij riskware als een beveiligings beheerder.

1. Klik rechtsboven op **onderhoud** om de pagina onderhoud te openen. 

    ![Riskware-configuratie houdt](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Klik op de pagina onderhoud op **personen**.

    ![Riskware-configuratie personen](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Selecteer het tabblad **Details** en voer de volgende stappen uit:

    ![Details van riskware-configuratie](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Selecteer **persoons type** zoals werk nemer.

    b. Voer in het tekstvak **voor voor naam** de voor naam van de gebruiker in, zoals **Julia**.

    c. Voer in het tekstvak **naam** de achternaam van de gebruiker in, zoals **Simon**.

1. Voer op het tabblad **beveiliging** de volgende stappen uit:

    ![Beveiliging van riskware-configuratie](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. Onder **verificatie** , selecteer de **verificatie** modus, die u hebt ingesteld als Azure-configuratie voor SSO.

    b. Ga naar het gedeelte **aanmeldings gegevens** en voer in het tekstvak **gebruikers-id** het e-mail adres van de gebruiker in, bijvoorbeeld `brittasimon@contoso.com`.

    c. Voer in het tekstvak **wacht** woord het wacht woord van de gebruiker in.

1. Voer de volgende stappen uit op het tabblad **organisatie** :

    ![Riskware-configuratie org](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Selecteer de optie als **Level1** -organisatie.

    b. Typ uw locatie in het tekstvak **locatie** van de **primaire werk plek** van de persoon.

    c. Onder sectie **werk nemer** selecteert u **status van werk nemers** , zoals inform.

    d. Klik op **Opslaan**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie maakt testen u uw Azure AD eenmalige aanmelding configuratie met behulp van het toegangsvenster.

Wanneer u op de tegel riskware in het toegangs venster klikt, moet u automatisch worden aangemeld bij de riskware waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
