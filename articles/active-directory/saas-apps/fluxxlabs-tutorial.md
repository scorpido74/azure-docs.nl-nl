---
title: 'Zelf studie: integratie met Stroomx Labs Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Stroomx Labs.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: e624520a9d1f39bc8115ac72e9df0398065928f1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67102393"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Zelf studie: integratie met Stroomx Labs Azure Active Directory

In deze zelf studie leert u hoe u Stroomx Labs kunt integreren met Azure Active Directory (Azure AD).
Het integreren van de Stroomx Labs met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot Stroomx Labs.
* U kunt uw gebruikers automatisch aanmelden bij Stroomx Labs (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Stroomx Labs wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* Abonnement voor eenmalige aanmelding bij stroomx Labs

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Stroomx Labs ondersteunt **IDP** GEÏNITIEERDe SSO

## <a name="adding-fluxx-labs-from-the-gallery"></a>Stroomx Labs toevoegen vanuit de galerie

Als u de integratie van Stroomx Labs in azure AD wilt configureren, moet u aan uw lijst met beheerde SaaS-apps een stroom diagram van de galerie toevoegen.

**Voer de volgende stappen uit om Stroomx Labs toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. In het zoekvak typt u **Labs**, selecteert u **stroomx Labs** uit het paneel resultaten en klikt u vervolgens op knop **toevoegen** om de toepassing toe te voegen.

     ![Stroomx Labs in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Stroomx Labs op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in de Stroomx Labs tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met behulp van de Stroomx Labs, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor stroomx Labs configureren](#configure-fluxx-labs-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak](#create-fluxx-labs-test-user)** een Julia-gebruiker in de stroom voor de gebruikers van de Labs-test, die is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met de Stroomx Labs:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina voor de integratie van de **stroomx Labs** **-toepassing eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    ![Gegevens over het domein en Url's voor de eenmalige aanmelding van de stroomx Labs](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: 

    | Omgeving | URL-patroon|
    |-------------|------------|
    | Productie | `https://<subdomain>.fluxx.io` |
    | Pre-productie | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: 

    | Omgeving | URL-patroon|
    |-------------|------------|
    | Productie | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Pre-productie | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [ondersteunings team van stroomx Labs](mailto:travis@fluxxlabs.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer de gewenste URL ('s) volgens uw vereiste in het gedeelte de sectie **stroomx Labs instellen** .

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-fluxx-labs-single-sign-on"></a>Eenmalige aanmelding voor Stroomx Labs configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij de bedrijfs site van uw stroom server.

2. Selecteer **beheerder** onder de sectie **instellingen** .

    ![Configuratie van de stroomx Labs](./media/fluxxlabs-tutorial/config1.png)

3. Selecteer in het deel venster beheer de optie**integraties** van invoeg **toepassingen** > en selecteer vervolgens **SAML SSO-(uitgeschakeld)**

    ![Configuratie van de stroomx Labs](./media/fluxxlabs-tutorial/config2.png)

4. Voer de volgende stappen uit in de sectie Attribute:

    ![Configuratie van de stroomx Labs](./media/fluxxlabs-tutorial/config3.png)

    a. Selecteer het selectie vakje **SAML SSO** .

    b. Typ **/auth/SAML**in het tekstvak **aanvraag pad** .

    c. Typ **/auth/SAML/callback**in het tekstvak **call back pad** .

    d. Voer in het tekstvak **Assertion Consumer Service-URL (URL voor eenmalige aanmelding)** de waarde voor de **antwoord-URL** in die u hebt ingevoerd in de Azure Portal.

    e. Voer in het tekstvak **doel groep (SP-Entiteits-ID)** de **id** -waarde in die u hebt ingevoerd in de Azure Portal.

    f. Plak in het tekstvak **ID-provider SSO doel-URL** de waarde voor de **aanmeldings-URL** , die u hebt gekopieerd uit de Azure Portal.

    g. Open uw met base 64 versleutelde certificaat in Klad blok, kopieer de inhoud ervan naar het klem bord en plak het in het tekstvak **ID-provider certificaat** .

    h. Voer in het tekstvak **naam aanduiding notatie** de waarde `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`in.

    i. Klik op **Opslaan**.

    > [!NOTE]
    > Als de inhoud is opgeslagen, is het veld leeg voor beveiliging, maar is de waarde opgeslagen in de configuratie.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. Typ brittasimon@yourcompanydomain.extensionin het veld **gebruikers naam** . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de Stroomx Labs.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **stroomx Labs**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **stroomx Labs**.

    ![De stroom verbinding in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-fluxx-labs-test-user"></a>Een test gebruiker voor de Stroomx Labs maken

Als u Azure AD-gebruikers wilt inschakelen om zich aan te melden bij Stroomx Labs, moeten ze worden ingericht in Stroomx Labs. In het geval van een stroom-x-Labs is inrichting een hand matige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij de bedrijfs site van uw Stroomx-Labs als beheerder.

2. Klik op het **pictogram**hieronder weer gegeven.

    ![Configuratie van de stroomx Labs](./media/fluxxlabs-tutorial/config6.png)

3. Klik op het dash board op het pictogram hieronder weer gegeven om de kaart **nieuwe personen** te openen.

    ![Configuratie van de stroomx Labs](./media/fluxxlabs-tutorial/config4.png)

4. Voer de volgende stappen uit in de sectie **nieuwe personen** :

    ![Configuratie van de stroomx Labs](./media/fluxxlabs-tutorial/config5.png)

    a. In stroomx Labs wordt e-mail gebruikt als de unieke id voor SSO-aanmeldingen. Vul het veld **SSO-UID** in met het e-mail adres van de gebruiker, dat overeenkomt met het e-mail adres, dat wordt gebruikt als aanmelding met SSO.

    b. Klik op **Opslaan**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangs venster op de tegel van de stroom met de Labs klikt, moet u automatisch worden aangemeld bij de stroomx Labs waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

