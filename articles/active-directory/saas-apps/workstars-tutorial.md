---
title: 'Zelf studie: integratie Azure Active Directory met Workstars | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Workstars.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 51a4a4e4-ff60-4971-b3f8-a0367b70d220
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: 43fbc158c9ed0bbe87a75cd845433c7a7be2e6d1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81867901"
---
# <a name="tutorial-azure-active-directory-integration-with-workstars"></a>Zelf studie: integratie Azure Active Directory met Workstars

In deze zelf studie leert u hoe u Workstars integreert met Azure Active Directory (Azure AD).
Het integreren van Workstars met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot Workstars.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij Workstars (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Workstars wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Abonnement voor eenmalige aanmelding Workstars ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Workstars ondersteunt door **IDP** GEÏNITIEERDe SSO

## <a name="adding-workstars-from-the-gallery"></a>Workstars toevoegen uit de galerie

Als u de integratie van Workstars in azure AD wilt configureren, moet u Workstars uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Workstars toe te voegen uit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Workstars**in het zoekvak, selecteer **Workstars** in het resultaten paneel en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

     ![Workstars in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Workstars op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Workstars tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met Workstars, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Workstars eenmalige aanmelding configureren](#configure-workstars-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een Workstars-test gebruiker](#create-workstars-test-user)** -om een equivalent van Julia Simon in Workstars te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met Workstars:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **Workstars** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor Workstars domein en Url's](common/idp-intiated.png)

    a. Typ een URL in het tekstvak **id** :`https://workstars.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<subdomain>.workstars.com/saml/login_check`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke antwoord-URL. Neem contact op met het [ondersteunings team van Workstars](http://support.workstars.com/) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer op de sectie **Workstars instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-workstars-single-sign-on"></a>Eenmalige aanmelding voor Workstars configureren

1. Meld u in een ander browser venster aan bij de Workstars-bedrijfs site als beheerder.

2. Klik op de hoofdwerk balk op **instellingen**.

    ![Workstars-instellingen](./media/workstars-tutorial/tutorial_workstars_sett.png)

3. Ga naar **Sign On** > **instellingen**voor aanmelden.

    ![Workstars aanmelding](./media/workstars-tutorial/tutorial_workstars_signon.png)

    ![Workstars-instellingen](./media/workstars-tutorial/tutorial_workstars_settings.png)

4. Voer de volgende stappen uit op de pagina voor **eenmalige aanmelding (SAML)-instellingen** :
    
    ![Workstars SAML](./media/workstars-tutorial/tutorial_workstars_saml.png)

    a. Typ in het tekstvak naam van de **identiteits provider** de tekst **Office 365**.

    b. Plak de waarde van de **Azure ad-id**die u van Azure Portal hebt gekopieerd in het tekstvak id **-provider entiteits-** id's.

    c. Kopieer de inhoud van het gedownloade certificaat bestand in Klad blok en plak het in het tekstvak **x509-certificaat** . 

    d. Plak in het tekstvak **SAML SSO URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.
    
    e. Plak in het tekstvak **URL voor externe afmelding** de waarde van de **afmeldings-URL**, die u van Azure Portal hebt gekopieerd. 

    f. Selecteer **naam-id** als **e-mail adres (standaard)**.

    g. Klik op **Bevestigen**.

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

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Workstars.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **Workstars**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Workstars**.

    ![De koppeling Workstars in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-workstars-test-user"></a>Workstars-test gebruiker maken

In deze sectie maakt u een gebruiker met de naam Julia Simon in Workstars. Werk samen met [Workstars-ondersteunings team](http://support.workstars.com) om de gebruikers toe te voegen in het Workstars-platform.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Workstars in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Workstars waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

