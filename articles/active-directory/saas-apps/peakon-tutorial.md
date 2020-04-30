---
title: 'Zelf studie: integratie Azure Active Directory met Peakon | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Peakon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a944c397-ed3f-4d45-b9b2-6d4bcb6b0a09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: b093a26848701254ad674081037c266f1fb012b2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67094726"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>Zelf studie: integratie Azure Active Directory met Peakon

In deze zelf studie leert u hoe u Peakon integreert met Azure Active Directory (Azure AD).
Het integreren van Peakon met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot Peakon.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij Peakon (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Peakon wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement voor eenmalige aanmelding Peakon ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Peakon ondersteunt SSO die door **SP** en **IDP** is geïnitieerd

## <a name="adding-peakon-from-the-gallery"></a>Peakon toevoegen uit de galerie

Als u de integratie van Peakon in azure AD wilt configureren, moet u Peakon uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Peakon toe te voegen uit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Peakon**in het zoekvak, selecteer **Peakon** in het resultaten paneel en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

     ![Peakon in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Peakon op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Peakon tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met Peakon, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Peakon eenmalige aanmelding configureren](#configure-peakon-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een Peakon-test gebruiker](#create-peakon-test-user)** -om een equivalent van Julia Simon in Peakon te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met Peakon:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **Peakon** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Als u de toepassing in de gestarte modus van **IDP** wilt configureren, voert u de volgende stappen uit in de sectie **basis configuratie van SAML** :

    ![Informatie over eenmalige aanmelding voor Peakon domein en Url's](common/idp-intiated.png)

    a. Typ in het tekstvak **id** een URL met het volgende patroon:`https://app.peakon.com/saml/<companyid>/metadata`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://app.peakon.com/saml/<companyid>/assert`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Informatie over eenmalige aanmelding voor Peakon domein en Url's](common/metadata-upload-additional-signon.png)

    Typ een URL in het tekstvak **URL voor aanmelding** :`https://app.peakon.com/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de daad werkelijke id en de antwoord-URL die verderop in de zelf studie wordt uitgelegd. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Raw)** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificateraw.png)

7. Kopieer op de sectie **Peakon instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-peakon-single-sign-on"></a>Eenmalige aanmelding voor Peakon configureren

1. Meld u in een ander browser venster aan bij Peakon als beheerder.

2. Klik in de menu balk aan de linkerkant van de pagina op **configuratie**en navigeer vervolgens naar **integraties**.

    ![De configuratie](./media/peakon-tutorial/tutorial_peakon_config.png)

3. Klik op de pagina **integraties** op **eenmalige aanmelding**.

    ![De enkele](./media/peakon-tutorial/tutorial_peakon_single.png)

4. Klik onder de sectie **eenmalige aanmelding** op **inschakelen**.

    ![Het inschakelen](./media/peakon-tutorial/tutorial_peakon_enable.png)

5. Voer de volgende stappen uit op de **eenmalige aanmelding voor werk nemers met** de sectie SAML:

    ![De SAML](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. Plak in het tekstvak **aanmeldings-URL voor SSO** de waarde van de AANMELDINGS- **URL**die u hebt gekopieerd van de Azure Portal.

    b. Plak in het tekstvak **SSO-Afmeldings-URL** de waarde van de **afmeldings-URL**die u hebt gekopieerd van de Azure Portal.

    c. Klik op **bestand kiezen** om het certificaat dat u hebt gedownload van de Azure Portal, in het vak certificaat te uploaden.

    d. Klik op het **pictogram** om de **Entiteits-ID** te kopiëren en in het tekstvak **id** plakken in de sectie **basis configuratie van SAML** op Azure Portal.

    e. Klik op het **pictogram** om de **antwoord-URL (ACS)** te kopiëren en te plakken in het tekstvak **antwoord-URL** in de sectie **basis configuratie van SAML** op Azure Portal.

    f. Klik op **Opslaan**

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon@yourcompanydomain.extension**.  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Peakon.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **Peakon**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Peakon**.

    ![De koppeling Peakon in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-peakon-test-user"></a>Peakon-test gebruiker maken

Voor het inschakelen van Azure AD-gebruikers om zich aan te melden bij Peakon, moeten ze worden ingericht in Peakon.  
In het geval van Peakon is inrichting een hand matige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u als beheerder aan bij de Peakon-bedrijfs site.

2. Klik in de menu balk aan de linkerkant van de pagina op **configuratie**en navigeer vervolgens naar **werk nemers**.

    ![De werk nemer](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. Klik rechtsboven op de pagina op **werk nemer toevoegen**.

      ![De werk nemer toevoegen](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. Voer de volgende stappen uit op de pagina **nieuwe werk nemers** :

     ![De nieuwe werk nemer](./media/peakon-tutorial/tutorial_peakon_create.png)

    a. Typ in het tekstvak **naam** de voor naam als **Julia** en achternaam als **Simon**.

    b. Typ in het tekstvak **e-mail** het e-mail adres **zoals\@Brittasimon contoso.com**.

    c. Klik op **werk nemer maken**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Peakon in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Peakon waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

