---
title: 'Zelf studie: integratie Azure Active Directory met SAML SSO voor bamboo door Resolution GmbH | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SAML SSO voor bamboo door Resolution GmbH.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f00160c7-f4cc-43bf-af18-f04168d3767c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 044a2f9a9ecd0ccceb99ce7999af7e2c8578950d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67106538"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-bamboo-by-resolution-gmbh"></a>Zelf studie: integratie Azure Active Directory met SAML SSO voor bamboo by Solution GmbH

In deze zelf studie leert u hoe u SAML SSO kunt integreren voor bamboo door Solution GmbH met Azure Active Directory (Azure AD).
Het integreren van SAML SSO voor bamboo door Solution GmbH met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot SAML SSO voor bamboo door Solution GmbH.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij SAML SSO voor bamboo door de oplossing GmbH (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met SAML SSO wilt configureren voor bamboo door Solution GmbH, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* SAML SSO voor bamboo-abonnement met eenmalige aanmelding.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SAML SSO voor bamboo by Solution GmbH ondersteunt door **SP en IDP** GEÏNITIEERDe SSO
* SAML SSO voor bamboo by Solution GmbH ondersteunt **just-in-time** -gebruikers inrichting

## <a name="adding-saml-sso-for-bamboo-by-resolution-gmbh-from-the-gallery"></a>SAML SSO voor bamboo toevoegen door de oplossing van de galerie

Als u de integratie van SAML SSO voor bamboo wilt configureren door de oplossing GmbH in azure AD, moet u de SAML SSO voor bamboo toevoegen door de oplossing van de galerie naar uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om de SAML SSO voor bamboo toe te voegen door de oplossing van de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **SAML SSO voor bamboo by Solution GmbH**, selecteer **SAML SSO for Bamboo by Solution GmbH** from result panel en klik vervolgens op **add** button om de toepassing toe te voegen.

    ![SAML SSO voor bamboo by Solution GmbH in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met SAML SSO voor bamboo by Solution GmbH op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SAML SSO voor bamboo door de oplossing GmbH tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met SAML SSO voor bamboo door Resolution GmbH, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[SAML SSO voor bamboo configureren door eenmalige aanmelding van de oplossing GmbH](#configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on)** , voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak SAML SSO voor bamboo door de Solution GmbH-test gebruiker](#create-saml-sso-for-bamboo-by-resolution-gmbh-test-user)** : als u een equivalent van Julia Simon in SAML SSO voor bamboo wilt hebben door de oplossing GmbH die is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de eenmalige aanmelding van Azure AD te configureren met SAML SSO voor bamboo door Solution GmbH:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **SAML-SSO voor de oplossing** van de toepassing voor het oplossen van de Bamboo-aanvraag voor de integratie met **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Als u de toepassing in de gestarte modus van **IDP** wilt configureren, voert u de volgende stappen uit in de sectie **basis configuratie van SAML** :

    ![SAML SSO voor bamboo per oplossing GmbH domein en Url's eenmalige aanmelding gegevens](common/idp-intiated.png)

    a. Typ in het tekstvak **id** een URL met het volgende patroon:`https://<server-base-url>/plugins/servlet/samlsso`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<server-base-url>/plugins/servlet/samlsso`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![SAML SSO voor bamboo per oplossing GmbH domein en Url's eenmalige aanmelding gegevens](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met de [SAML-SSO voor bamboo door de oplossing GmbH client support team](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

7. Kopieer op de sectie **SAML SSO voor bamboo by Solution GmbH instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-saml-sso-for-bamboo-by-resolution-gmbh-single-sign-on"></a>SAML SSO configureren voor bamboo door eenmalige aanmelding met de resolutie GmbH

1. Meld u aan bij uw SAML SSO voor bamboo door de bedrijfs site van de oplossing GmbH als beheerder.

1. Klik aan de rechter kant van de hoofdwerk balk op **instellingen** > **-invoeg toepassingen**.

    ![De instellingen](./media/bamboo-tutorial/tutorial_bamboo_setings.png)

1. Ga naar de sectie Beveiliging en klik op **SAML-SingleSignOn** in de menu balk.

    ![De eenmalige aanmelding met SAML](./media/bamboo-tutorial/tutorial_bamboo_samlsingle.png)

1. Op de **pagina SAML SIngleSignOn Plugin Configuration** (Configuratie van plug-in voor eenmalige aanmelding met SAML) klikt u op **Add idp** (IdP toevoegen).

    ![De IdP toevoegen](./media/bamboo-tutorial/tutorial_bamboo_addidp.png)

1. Voer op de pagina **Choose your SAML Identity Provider** (Uw SAML-id-provider kiezen) de volgende stappen uit:

    ![De id-provider](./media/bamboo-tutorial/tutorial_bamboo_identityprovider.png)

    a. Selecteer bij **Idp Type** (Type IdP) de optie **AZURE AD**.

    b. Voer in het tekstvak **Name** (Naam) de naam in.

    c. Voer in het tekstvak **Description** (Omschrijving) de omschrijving in.

    d. Klik op **Volgende**.

1. Klik op de pagina configuratie van de **identiteits provider** op **volgende**.

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

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. Typ `brittasimon@yourcompanydomain.extension`in het veld **gebruikers naam** . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om gebruik te maken van eenmalige aanmelding van Azure door toegang te verlenen aan SAML SSO voor bamboo door de oplossing GmbH.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **SAML SSO voor bamboo by resolution GmbH**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **SAML SSO voor bamboo by resolution GmbH**.

    ![De SAML SSO-koppeling voor bamboo by Solution GmbH in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-saml-sso-for-bamboo-by-resolution-gmbh-test-user"></a>SAML-SSO maken voor bamboo door de Solution GmbH-test gebruiker

Het doel van deze sectie is het maken van een gebruiker met de naam Julia Simon in SAML SSO voor bamboo door de oplossing GmbH. SAML SSO voor bamboo by Solution GmbH biedt ondersteuning voor Just-in-time-inrichting en ook gebruikers kunnen hand matig worden gemaakt. Neem contact op met de [SAML-SSO voor bamboo door de oplossing van het client ondersteuning van de klanten service](https://marketplace.atlassian.com/plugins/com.resolution.atlasplugins.samlsso-bamboo/server/support) conform uw vereiste.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SAML SSO voor bamboo by Solution GmbH in het toegangs venster klikt, moet u automatisch worden aangemeld bij de SAML SSO voor bamboo door de oplossing GmbH waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
