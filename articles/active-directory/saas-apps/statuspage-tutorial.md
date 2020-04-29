---
title: 'Zelf studie: integratie Azure Active Directory met StatusPage | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en StatusPage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: d947f610e6a753ce2ed349917640b07a55bbb735
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67089879"
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Zelf studie: integratie Azure Active Directory met StatusPage

In deze zelf studie leert u hoe u StatusPage integreert met Azure Active Directory (Azure AD).
Het integreren van StatusPage met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot StatusPage.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij StatusPage (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met StatusPage wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* Abonnement voor eenmalige aanmelding StatusPage ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* StatusPage ondersteunt door **IDP** GEÏNITIEERDe SSO

## <a name="adding-statuspage-from-the-gallery"></a>StatusPage toevoegen uit de galerie

Als u de integratie van StatusPage in azure AD wilt configureren, moet u StatusPage uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om StatusPage toe te voegen uit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **StatusPage**in het zoekvak, selecteer **StatusPage** in het resultaten paneel en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![StatusPage in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met StatusPage op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in StatusPage tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met StatusPage, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[StatusPage eenmalige aanmelding configureren](#configure-statuspage-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een StatusPage-test gebruiker](#create-statuspage-test-user)** -om een equivalent van Julia Simon in StatusPage te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met StatusPage:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **StatusPage** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor StatusPage domein en Url's](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: 
    
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/`|
    | `https://<subdomain>.statuspage.io/`|

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: 
    
    | |
    |--|
    | `https://<subdomain>.statuspagestaging.com/sso/saml/consume`|
    | `https://<subdomain>.statuspage.io/sso/saml/consume`|

    > [!NOTE]
    > Neem contact op met het ondersteunings team van [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io)StatusPage om meta gegevens aan te vragen die nodig zijn om eenmalige aanmelding te configureren. 
    >
    > a. Kopieer de waarde van de verlener uit de meta gegevens en plak deze in het tekstvak **id** .
    >
    > b. Kopieer de antwoord-URL van de meta gegevens en plak deze in het tekstvak **antwoord-URL** .

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer op de sectie **StatusPage instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-statuspage-single-sign-on"></a>Eenmalige aanmelding voor StatusPage configureren

1. Meld u in een ander browser venster aan bij uw StatusPage-bedrijfs site als beheerder.

1. Klik in de hoofdwerk balk op **account beheren**.

    ![Eenmalige aanmelding configureren](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Klik op het tabblad **eenmalige aanmelding** .

    ![Eenmalige aanmelding configureren](./media/statuspage-tutorial/tutorial_statuspage_07.png)

1. Voer de volgende stappen uit op de pagina SSO-installatie:

    ![Eenmalige aanmelding configureren](./media/statuspage-tutorial/tutorial_statuspage_08.png)

    ![Eenmalige aanmelding configureren](./media/statuspage-tutorial/tutorial_statuspage_09.png)

    a. Plak in het tekstvak **URL voor SSO-doel** de waarde van de AANMELDINGS- **URL**die u van Azure Portal hebt gekopieerd.

    b. Open het gedownloade certificaat in Klad blok, kopieer de inhoud en plak deze in het tekstvak **certificaat** .

    c. Klik op **configuratie opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u `brittasimon@yourcompanydomain.extension`.  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan StatusPage.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **StatusPage**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **StatusPage**.

    ![De koppeling StatusPage in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-statuspage-test-user"></a>StatusPage-test gebruiker maken

Het doel van deze sectie is het maken van een gebruiker met de naam Julia Simon in StatusPage.

StatusPage biedt ondersteuning voor Just-in-time-inrichting. U hebt dit al ingeschakeld in de [eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-single-sign-on).

**Als u een gebruiker met de naam Julia Simon in StatusPage wilt maken, voert u de volgende stappen uit:**

1. Meld u aan bij de StatusPage-bedrijfs site als beheerder.

1. Klik in het menu aan de bovenkant op **account beheren**.

    ![Eenmalige aanmelding configureren](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Klik op het tabblad **team leden** .
  
    ![Een Azure AD-testgebruiker maken](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. Klik op **teamlid toevoegen**.
  
    ![Een Azure AD-testgebruiker maken](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Typ het **e-mail adres**, de voor **Surname** -en **Achternaam**van een geldige gebruiker die u wilt inrichten in de bijbehorende tekst vakken. 

    ![Een Azure AD-testgebruiker maken](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. Als **rol**, kiest u **client beheerder**.

1. Klik op **account maken**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel StatusPage in het toegangs venster klikt, moet u automatisch worden aangemeld bij de StatusPage waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
