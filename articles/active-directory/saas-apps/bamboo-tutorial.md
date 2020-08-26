---
title: 'Zelfstudie: Integratie van Azure Active Directory met SAML SSO voor Bamboo van resolution GmbH | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SAML SSO voor Bamboo van resolution GmbH.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: ed587e8ee54aeb36b6790314e849f38b1bab1007
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549378"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Zelfstudie: Integratie van Azure Active Directory met SAML SSO voor Bamboo van resolution GmbH

Deze zelfstudie laat zien hoe u SAML SSO voor Bamboo van resolution GmbH integreert met Azure Active Directory (Azure AD).
Integratie van SAML SSO voor Bamboo van resolution GmbH met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot SAML SSO voor Bamboo van resolution GmbH.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij SAML SSO voor Bamboo van resolution GmbH (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie met SAML SSO voor Bamboo van resolution GmbH te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op SAML SSO voor Bamboo van resolution GmbH waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SAML SSO voor Bamboo van resolution GmbH ondersteunt door **SP en IDP** geïnitieerde eenmalige aanmelding
* SAML SSO voor Bamboo van resolution GmbH biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

## <a name="adding-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>SAML SSO voor Bamboo van resolution GmbH toevoegen vanuit de galerie

Voor het configureren van de integratie van SAML SSO voor Bamboo van resolution GmbH in Azure AD moet u SAML SSO voor Bamboo van resolution GmbH vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om SAML SSO voor Bamboo van resolution GmbH toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **SAML SSO voor Bamboo van resolution GmbH** in het zoekvak, selecteer **SAML SSO voor Bamboo van resolution GmbH** in het resultatenvenster en klik op **Toevoegen** om de toepassing toe te voegen.

    ![SAML SSO voor Bamboo van resolution GmbH in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD-eenmalige aanmelding met SAML SSO voor Bamboo van resolution GmbH op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in SAML SSO voor Bamboo van resolution GmbH tot stand is gebracht.

Voltooi de volgende bouwstenen om Azure AD-eenmalige aanmelding met SAML SSO voor Bamboo van resolution GmbH te configureren en testen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding configureren voor SAML SSO voor Bamboo van resolution GmbH](#configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor SAML SSO voor Bamboo van resolution GmbH maken](#create-saml-sso-for-bamboo-by-resolution-gmbh-test-user)** : als u een tegenhanger van Britta Simon in SAML SSO voor Bamboo van resolution GmbH wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om Azure AD-eenmalige aanmelding met SAML SSO voor Bamboo van resolution GmbH te configureren:

1. In [Azure Portal](https://portal.azure.com/), op de integratiepagina voor de toepassing **SAML SSO voor Bamboo van resolution GmbH**, selecteert u **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In het gedeelte **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij SAML SSO voor Bamboo van resolution GmbH](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<server-base-url>/plugins/servlet/samlsso`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<server-base-url>/plugins/servlet/samlsso`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij SAML SSO voor Bamboo van resolution GmbH](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [klantondersteuningsteam voor SAML SSO voor Bamboo van resolution GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

7. In de sectie **SAML SSO voor Bamboo van resolution GmbH instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on"></a>SAML SSO voor Bamboo van resolution GmbH configureren voor eenmalige aanmelding

1. Meld u aan bij de bedrijfssite van SAML SSO voor Bamboo van resolution GmbH als een beheerder.

1. Klik aan de rechterkant van de hoofdwerkbalk op **Settings** > **Add-ons**.

    ![De instellingen](./media/bamboo-tutorial/tutorial_bamboo_setings.png)

1. Ga naar het gedeelte SECURITY en klik in de menubalk op **SAML SingleSignOn**.

    ![De eenmalige aanmelding met SAML](./media/bamboo-tutorial/tutorial_bamboo_samlsingle.png)

1. Op de **pagina SAML SIngleSignOn Plugin Configuration** (Configuratie van plug-in voor eenmalige aanmelding met SAML) klikt u op **Add idp** (IdP toevoegen).

    ![De IdP toevoegen](./media/bamboo-tutorial/tutorial_bamboo_addidp.png)

1. Voer op de pagina **Choose your SAML Identity Provider** (Uw SAML-id-provider kiezen) de volgende stappen uit:

    ![De id-provider](./media/bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. Selecteer bij **Idp Type** (Type IdP) de optie **AZURE AD**.

    b. Voer in het tekstvak **Name** (Naam) de naam in.

    c. Voer in het tekstvak **Description** (Omschrijving) de omschrijving in.

    d. Klik op **Volgende**.

1. Klik op de pagina **Identity provider configuration** op **Next**.

    ![De id-configuratie](./media/bamboo-tutorial/tutorial_bamboo_identityconfig.png)

1. Klik op de pagina **Import SAML Idp Metadata** (Metagegevens voor SAML-IdP importeren) op de optie **Load File** (Bestand laden) om het bestand **METADATA XML** te uploaden die u uit de Azure-portal hebt gedownload.

    ![De IdP-metagegevens](./media/bamboo-tutorial/tutorial_bamboo_idpmetadata.png)

1. Klik op **Volgende**.

1. Klik op **Save settings** (Instellingen opslaan).

    ![Het opslaan](./media/bamboo-tutorial/tutorial_bamboo_save.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u `brittasimon@yourcompanydomain.extension`. Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat om eenmalige aanmelding van Azure te gebruiken door haar toegangsrechten voor SAML SSO voor Bamboo van resolution GmbH te verlenen.

1. Selecteer in Azure Portal **Bedrijfstoepassingen**, selecteer **Alle toepassingen** en selecteer vervolgens **SAML SSO voor Bamboo van resolution GmbH**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ en selecteer in de lijst met toepassingen **SAML SSO voor Bamboo van resolution GmbH**.

    ![De koppeling SAML SSO voor Bamboo van resolution GmbH in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>Testgebruiker voor SAML SSO voor Bamboo van resolution GmbH maken

Het doel van dit gedeelte is het maken van een gebruiker met de naam Britta Simon in SAML SSO voor Bamboo van resolution GmbH. SSAML SSO voor Bamboo van resolution GmbH biedt ondersteuning voor Just-In-Time-inrichting en het handmatig inrichten van gebruikers. Neem contact op met [het klantondersteuningsteam van SAML SSO voor Bamboo van resolution GmbH](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) naargelang uw behoeften.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Als u in het toegangsvenster op de tegel SAML SSO voor Bamboo van resolution GmbH klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van SAML SSO voor Bamboo van resolution GmbH waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
