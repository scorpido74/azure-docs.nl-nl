---
title: 'Zelfstudie: Azure Active Directory-integratie met Riskware | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Riskware.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72027113"
---
# <a name="tutorial-azure-active-directory-integration-with-riskware"></a>Zelfstudie: Azure Active Directory-integratie met Riskware

In deze zelfstudie leert u hoe u Riskware integreren met Azure Active Directory (Azure AD).
Het integreren van Riskware met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Riskware.
* U uw gebruikers automatisch laten inlogen bij Riskware (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Riskware wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Riskware single sign-on enabled subscription

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Riskware ondersteunt **SP** geïnitieerde SSO

## <a name="adding-riskware-from-the-gallery"></a>Riskware toevoegen vanuit de galerie

Als u de integratie van Riskware in Azure AD wilt configureren, moet u Riskware uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Riskware uit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Riskware**in het zoekvak , selecteer **Riskware** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

    ![Riskware in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Riskware op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding om te werken, moet een koppeling slinken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Riskware.

Als u Azure AD single sign-on met Riskware wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Riskware Single Sign-On](#configure-riskware-single-sign-on)** - om de instellingen voor eenmalig aanmelden aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak de testgebruiker van Riskware](#create-riskware-test-user)** - om een tegenhanger van Britta Simon in Riskware te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met Riskware te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Riskware-toepassingsintegratie** de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over risicoware-domeinen en URL's met eenmalige aanmelding](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: 
    
    | Omgeving| URL-patroon|
    |--|--|
    | UAT|  `https://riskcloud.net/uat?ccode=<COMPANYCODE>` |
    | Prod| `https://riskcloud.net/prod?ccode=<COMPANYCODE>` |
    | Demo| `https://riskcloud.net/demo?ccode=<COMPANYCODE>` |
    |||

    b. Typ in het tekstvak **Id (Entiteits-id)** de volgende URL: 
    
    | Omgeving| URL-patroon|
    |--|--|
    | UAT| `https://riskcloud.net/uat` |
    | Prod| `https://riskcloud.net/prod` |
    | Demo| `https://riskcloud.net/demo` |
    |||

    > [!NOTE]
    > De waarde van de aanmeldings-URL is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [het supportteam van Riskware Client](mailto:support@pansoftware.com.au) om de waarde te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **Riskware instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-riskware-single-sign-on"></a>Riskware Single Sign-On configureren

1. Meld u in een ander browservenster aan bij uw Riskware-bedrijfssite als beheerder.

1. Klik rechtsboven op **Onderhoud** om de onderhoudspagina te openen.

    ![Riskware-configuraties onderhouden](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Klik op de onderhoudspagina op **Verificatie**.

    ![Riskware Configuratie authen](./media/riskware-tutorial/tutorial_riskware_authen.png)

1. Voer in de pagina **Verificatieconfiguratie** de volgende stappen uit:

    ![Riskware Configuratie authenconfig](./media/riskware-tutorial/tutorial_riskware_config.png)

    a. Selecteer **Tekst** als **SAML** voor verificatie.

    b. Typ uw code in het tekstvak **Code** als AZURE_UAT.

    c. Typ **in het** tekstvak Beschrijving uw beschrijving zoals AZURE-configuratie voor SSO.

    d. Plak in het tekstvak **Op één bord op pagina** de **url-waarde aanmelden,** die u hebt gekopieerd vanuit azure-portal.

    e. Plak **in het tekstvak Pagina afmelden** de **URL-waarde van afmelden,** die u hebt gekopieerd vanuit azure-portal.

    f. Typ in het tekstvak **Formulierformulier plaatsen** de veldnaam die aanwezig is in Post-antwoord dat SAML zoals SAMLResponse bevat

    g. Typ kenmerk, dat de unieke id bevat in het SAML-antwoord zoals NameID, in het tekstvak **XML-identiteitstagnaam.**

    h. Open de gedownloade **Xml metagegevens** van Azure-portal in kladblok, kopieer het certificaat uit het bestand Metagegevens en plak het in het tekstvak **Certificaat**

    i. Plak in het tekstvak **van de URL** van consumenten de waarde van de URL van **antwoord**, die u van het ondersteuningsteam krijgt.

    j. Plak **in het** tekstvak Uitgever de waarde van **Id**, die u van het ondersteuningsteam krijgt.

    > [!Note]
    > Neem contact op met [het supportteam van Riskware Client](mailto:support@pansoftware.com.au) om deze waarden te krijgen

    k. Schakel selectievakje **POST gebruiken** in.

    l. Schakel het selectievakje **SAML-aanvraag gebruiken** in.

    m. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u `brittasimon@yourcompanydomain.extension`.  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Riskware.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Vervolgens Riskware**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Riskware**in de lijst met toepassingen .

    ![De Riskware-koppeling in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-riskware-test-user"></a>Riskware-testgebruiker maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij Riskware, moeten ze worden ingericht in Riskware. In Riskware is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij Riskware als beveiligingsbeheerder.

1. Klik rechtsboven op **Onderhoud** om de onderhoudspagina te openen. 

    ![Riskware Configuration handhaaft](./media/riskware-tutorial/tutorial_riskware_maintain.png)

1. Klik op de onderhoudspagina op **Personen**.

    ![Gebruikers van Riskware-configuratie](./media/riskware-tutorial/tutorial_riskware_people.png)

1. Selecteer het tabblad **Details** en voer de volgende stappen uit:

    ![Details van Riskware Configuration](./media/riskware-tutorial/tutorial_riskware_details.png)

    a. Selecteer **Persoonstype** als werknemer.

    b. Voer in het tekstvak **Voornaam** de voornaam van de gebruiker in, zoals **Britta.**

    c. Voer in **het tekstvak Achternaam** de achternaam van de gebruiker in, zoals **Simon.**

1. Voer op het tabblad **Beveiliging** de volgende stappen uit:

    ![Beveiliging van Riskware-configuratie](./media/riskware-tutorial/tutorial_riskware_security.png)

    a. Selecteer **onder sectie Verificatie** de **verificatiemodus,** die u hebt ingesteld, zoals AZURE-configuratie voor SSO.

    b. Voer onder de sectie **Aanmeldingsgegevens** in het tekstvak `brittasimon@contoso.com` **Gebruikersnaam** de e-mail van de gebruiker in, zoals .

    c. Voer **in** het tekstvak Wachtwoord het wachtwoord van de gebruiker in.

1. Voer op het tabblad **Organisatie** de volgende stappen uit:

    ![Riskware Configuration org](./media/riskware-tutorial/tutorial_riskware_org.png)

    a. Selecteer de optie als **Level1-organisatie.**

    b. Typ **onder de sectie Primaire werkplek** van persoon in het tekstvak **Locatie** uw locatie.

    c. Selecteer onder de sectie **Werknemer** de optie **Werknemersstatus** zoals Casual.

    d. Klik op **Opslaan**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Riskware in het Access-paneel klikt, moet u automatisch worden aangemeld bij de Riskware waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
