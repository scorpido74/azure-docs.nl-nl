---
title: 'Zelf studie: integratie Azure Active Directory met Igloo-software | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory-en Igloo-software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/06/2019
ms.author: jeedes
ms.openlocfilehash: df1d70f895e2e0a81344cf2a4e8e2d9963c951fa
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67100584"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Zelf studie: integratie Azure Active Directory met Igloo-software

In deze zelf studie leert u hoe u Igloo-software integreert met Azure Active Directory (Azure AD).
Het integreren van Igloo-software met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot Igloo-software.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld voor Igloo-software (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Igloo-software wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement voor eenmalige aanmelding van Igloo-software

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Igloo-software ondersteunt door **SP** GEÏNITIEERDe SSO
* Igloo-software ondersteunt **just-in-time** -gebruikers inrichting

## <a name="adding-igloo-software-from-the-gallery"></a>Igloo-software toevoegen vanuit de galerie

Als u de integratie van Igloo-software in azure AD wilt configureren, moet u Igloo-software uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Igloo-software toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Igloo software**in het zoekvak, selecteer **Igloo software** van result panel en klik vervolgens op knop **toevoegen** om de toepassing toe te voegen.

     ![Igloo-software in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Igloo-software op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Igloo-software tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met Igloo-software, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding van Igloo-software configureren](#configure-igloo-software-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een Igloo-gebruikers test gebruiker](#create-igloo-software-test-user)** -om een equivalent van Julia Simon in Igloo-software te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met Igloo-software:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina integratie van **Igloo-software** toepassing de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor Igloo-software domein en Url's](common/sp-identifier-reply.png)

    a. Typ in het tekstvak **URL voor aanmelding** een URL met het volgende patroon:`https://<company name>.igloocommmunities.com`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<company name>.igloocommmunities.com/saml.digest`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteunings team](https://www.igloosoftware.com/services/support) van de Igloo-software om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer op de sectie **Igloo-software instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-igloo-software-single-sign-on"></a>Eenmalige aanmelding voor Igloo-software configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij uw Igloo-software-bedrijfs site.

2. Ga naar het **configuratie scherm**.

     ![Configuratiescherm](./media/igloo-software-tutorial/ic799949.png "Configuratiescherm")

3. Klik op het tabblad **lidmaatschap** op **aanmeldings instellingen**.

    ![Aanmeldings instellingen](./media/igloo-software-tutorial/ic783968.png "Aanmeldings instellingen")

4. Klik in de sectie SAML-configuratie op **SAML-verificatie configureren**.

    ![SAML-configuratie](./media/igloo-software-tutorial/ic783969.png "SAML-configuratie")

5. Voer de volgende stappen uit in de sectie **algemene configuratie** :

    ![Algemene configuratie](./media/igloo-software-tutorial/ic783970.png "Algemene configuratie")

    a. Typ in het tekstvak naam van de **verbinding** een aangepaste naam voor uw configuratie.

    b. Plak in het tekstvak **IDP aanmeld-URL** de waarde van de **aanmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    c. Plak in het tekstvak **Afmeldings-URL van IDP** de waarde van de **afmeldings-URL** die u van Azure Portal hebt gekopieerd.

    d. Selecteer **antwoord afmelden en HTTP-type aanvragen** als **post**.

    e. Open uw met **base 64** versleutelde certificaat in Klad blok dat u hebt gedownload van Azure Portal, kopieer de inhoud ervan naar het klem bord en plak het in het tekstvak **openbaar certificaat** .

6. Voer de volgende stappen uit in de **configuratie voor antwoorden en authenticatie**:

    ![Configuratie van antwoorden en verificatie](./media/igloo-software-tutorial/IC783971.png "Configuratie van antwoorden en verificatie")
  
    a. Selecteer **micro soft ADFS**als **ID-provider**.

    b. Selecteer **e-mail adres**als **ID-type**. 

    c. Typ **EmailAddress**in het tekstvak **e-mail kenmerk** .

    d. **Typ voor**naam in het tekstvak **voor het kenmerk First name** .

    e. Typ in het tekstvak **Achternaam kenmerk** naam **.**

7. Voer de volgende stappen uit om de configuratie te volt ooien:

    ![Gebruikers maken bij aanmelden](./media/igloo-software-tutorial/IC783972.png "Gebruikers maken bij aanmelden") 

    a. Selecteer bij het **maken van een gebruiker bij aanmelden**de optie **een nieuwe gebruiker in uw site maken wanneer deze zich aanmeldt**.

    b. Als **aanmeldings instellingen**selecteert u de **knop SAML gebruiken op het scherm aanmelden**.

    c. Klik op **Opslaan**.

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

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Igloo-software.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **Igloo software**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Igloo software**.

    ![De koppeling naar de Igloo-software in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-igloo-software-test-user"></a>Igloo software test gebruiker maken

Er is geen actie-item waarmee u gebruikers inrichten kunt configureren voor Igloo-software.  

Wanneer een toegewezen gebruiker zich probeert aan te melden bij Igloo-software met behulp van het toegangs venster, controleert Igloo software of de gebruiker bestaat.  Als er nog geen gebruikers account beschikbaar is, wordt deze automatisch gemaakt door Igloo-software.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Igloo software in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Igloo-software waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)