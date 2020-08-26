---
title: 'Zelfstudie: Azure Active Directory-integratie met MOVEit Transfer - Azure AD-integratie | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en MOVEit Transfer - Azure AD-integratie.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 4ec0a3082b5978e0c540a2e0a70843081201a29b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88544125"
---
# <a name="tutorial-azure-active-directory-integration-with-moveit-transfer---azure-ad-integration"></a>Zelfstudie: Azure Active Directory-integratie met MOVEit Transfer - Azure AD-integratie

In deze zelfstudie leert u hoe u MOVEit Transfer - Azure AD-integratie kunt integreren met Azure Active Directory (Azure AD).
De integratie van MOVEit Transfer - Azure AD-integratie met Azure AD biedt de volgende voordelen:

* In Azure AD beheren wie toegang heeft tot MOVEit Transfer - Azure AD-integratie.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij MOVEit Transfer - Azure AD-integratie (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met MOVEit Transfer - Azure AD-integratie hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op MOVEit Transfer - Azure AD-integratie waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* MOVEit Transfer - Azure AD-integratie biedt ondersteuning voor door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-moveit-transfer---azure-ad-integration-from-the-gallery"></a>MOVEit Transfer - Azure AD-integratie toevoegen vanuit de galerie

Als u de integratie van MOVEit Transfer - Azure AD-integratie in Azure AD wilt configureren, moet u MOVEit Transfer - Azure AD-integratie vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om MOVEit Transfer - Azure AD-integratie toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **MOVEit Transfer - Azure AD-integratie** in het zoekvak, selecteer **MOVEit Transfer - Azure AD-integratie** in het resultatenvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

     ![MOVEit Transfer - Azure AD-integratie in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met MOVEit Transfer - Azure AD-integratie op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in MOVEit Transfer - Azure AD-integratie tot stand is gebracht.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met MOVEit Transfer - Azure AD-integratie te configureren en te testen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding van MOVEit Transfer - Azure AD-integratie configureren](#configure-moveit-transfer---azure-ad-integration-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor MOVEit Transfer - Azure AD-integratie maken](#create-moveit-transfer---azure-ad-integration-test-user)** : als u een tegenhanger van Britta Simon in MOVEit Transfer - Azure AD-integratie wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u Azure AD-eenmalige aanmelding wilt configureren met MOVEit Transfer - Azure AD-integratie:

1. Ga in [Azure Portal](https://portal.azure.com/) naar de pagina voor integratie van de toepassing **MOVEit Transfer - Azure AD-integratie** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u beschikt over een **bestand met metagegevens van de serviceprovider**:

    a. Klik op **Metagegevensbestand uploaden**.

    ![Metagegevensbestand uploaden](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Metagegevensbestand kiezen](common/browse-upload-metadata.png)

    c. Nadat het bestand met metagegevens is geüpload, worden de waarden voor **Id** en **Antwoord-URL** automatisch ingevuld in de sectie **Standaard SAML-configuratie**:

    ![Domein- en URL-gegevens voor eenmalige aanmelding voor MOVEit Transfer - Azure AD-integratie](common/sp-identifier-reply.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://contoso.com`

    > [!NOTE]
    > De waarde voor de **aanmeldings-URL** is geen reële waarde. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteuningsteam voor klanten van MOVEit Transfer - Azure AD-integratie](https://community.ipswitch.com/s/support) om de waarde op te halen. U kunt het **metagegevensbestand van de serviceprovider** downloaden met de **metagegevens-URL van de serviceprovider**. Dit wordt later uitgelegd in de sectie **Eenmalige aanmelding voor MOVEit Transfer - Azure AD-integratie configureren** van de zelfstudie. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. In de sectie **MOVEit Transfer - Azure AD-integratie instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-moveit-transfer---azure-ad-integration-single-sign-on"></a>Eenmalige aanmelding voor MOVEit Transfer - Azure AD-integratie configureren

1. Meld u als beheerder aan bij uw tenant voor MOVEit Transfer - Azure AD-integratie.

2. Klik in het linkernavigatiedeelvenster op **Instellingen**.

    ![De sectie Instellingen aan de app-zijde](./media/moveittransfer-tutorial/tutorial_moveittransfer_000.png)

3. Klik op de koppeling **Eenmalige aanmelding** (onder **Beveiligingsbeleid -> Gebruikersverificatie**).

    ![Beveiligingsbeleid aan de app-zijde](./media/moveittransfer-tutorial/tutorial_moveittransfer_001.png)

4. Klik op de koppeling Metagegevens-URL om het document met metagegevens te downloaden.

    ![Metagegevens-URL van serviceprovider](./media/moveittransfer-tutorial/tutorial_moveittransfer_002.png)
    
   * Controleer of **entityID** overeenkomt met **Id** in het gedeelte **Standaard SAML-configuratie**.
   * Controleer of de **AssertionConsumerService**-locatie-URL overeenkomt met **ANTWOORD-URL**  in het gedeelte **Standaard SAML-configuratie**.
    
     ![Eenmalige aanmelding in de app configureren](./media/moveittransfer-tutorial/tutorial_moveittransfer_007.png)

5. Klik op de knop **Add Identity Provider** om een nieuwe federatieve id-provider toe te voegen.

    ![Id-provider toevoegen](./media/moveittransfer-tutorial/tutorial_moveittransfer_003.png)

6. Klik op **Bladeren...** om het metagegevensbestand te selecteren dat u hebt gedownload van Azure Portal. Klik vervolgens op **Add Identity Provider** om het gedownloade bestand te uploaden.

    ![SAML-id-provider](./media/moveittransfer-tutorial/tutorial_moveittransfer_004.png)

7. Selecteer **Yes** als **Enabled** op de pagina **Edit Federated Identity Provider Settings...** en klik op **Save**.

    ![Instellingen voor gefedereerde id-provider](./media/moveittransfer-tutorial/tutorial_moveittransfer_005.png)

8. Voer op de pagina **Edit Federated Identity Provider User Settings** de volgende handelingen uit:
    
    ![Instellingen voor gefedereerde id-provider bewerken](./media/moveittransfer-tutorial/tutorial_moveittransfer_006.png)
    
    a. Selecteer **SAML NameID** als **Login name**.
    
    b. Selecteer **Other** als **Full name** en voer in het tekstvak **Attribute name** de volgende waarde in: `http://schemas.microsoft.com/identity/claims/displayname`.
    
    c. Selecteer **Other** als **Email** en voer in het tekstvak **Attribute name** de volgende waarde in: `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.
    
    d. Selecteer **Yes** als **Auto-create account on signon**.
    
    e. Klik op de knop **Save**.

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

In deze sectie gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot MOVEit Transfer - Azure AD-integratie.

1. Selecteer in Azure Portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **MOVEit Transfer - Azure AD-integratie**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen de optie **MOVEit Transfer - Azure AD-integratie**.

    ![De koppeling MOVEit Transfer - Azure AD-integratie in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-moveit-transfer---azure-ad-integration-test-user"></a>Een testgebruiker maken voor MOVEit Transfer - Azure AD-integratie

Het doel van deze sectie is het maken van een gebruiker met de naam Britta Simon in MOVEit Transfer - Azure AD-integratie. MOVEit Transfer - Azure AD-integratie biedt ondersteuning voor Just-In-Time-inrichting, die u hebt ingeschakeld. Er is geen actie-item voor u in deze sectie. Er wordt een nieuwe gebruiker gemaakt bij een poging toegang te krijgen tot MOVEit Transfer - Azure AD-integratie als de gebruiker nog niet bestaat.

>[!NOTE]
>Als u handmatig een gebruiker moet maken, neemt u contact op met het [ondersteuningsteam van MOVEit Transfer - Azure AD-integratie](https://community.ipswitch.com/s/support).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel MOVEit Transfer - Azure AD-integratie klikt, moet u automatisch worden aangemeld bij de instantie van MOVEit Transfer - Azure AD-integratie waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

