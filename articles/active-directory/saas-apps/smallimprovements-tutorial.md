---
title: 'Zelf studie: integratie met kleine verbeteringen Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en kleine verbeteringen.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59c8a112-41e1-4337-9ef3-3d7029780d61
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: d2d0bbc7a6e1c680434041d1b9d55e39a96b6f44
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67090363"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Zelf studie: integratie met kleine verbeteringen Azure Active Directory

In deze zelf studie leert u hoe u kleine verbeteringen integreert met Azure Active Directory (Azure AD).
Het integreren van kleine verbeteringen met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot kleine verbeteringen.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij kleine verbeteringen (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met kleine verbeteringen wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Kleine verbeteringen ingeschakeld abonnement voor eenmalige aanmelding

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Kleine verbeteringen bieden ondersteuning voor door **SP** GEÏNITIEERDe SSO

## <a name="adding-small-improvements-from-the-gallery"></a>Kleine verbeteringen toevoegen vanuit de galerie

Als u de integratie van kleine verbeteringen in azure AD wilt configureren, moet u kleine verbeteringen van de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u kleine verbeteringen wilt toevoegen uit de galerie, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **kleine verbeteringen**in het zoekvak, selecteer **kleine verbeteringen** van het deel venster resultaten en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

     ![Kleine verbeteringen in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met kleine verbeteringen op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in kleine verbeteringen worden ingesteld.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met kleine verbeteringen, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Kleine verbeteringen eenmalige aanmelding configureren](#configure-small-improvements-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een kleine verbetering](#create-small-improvements-test-user)** van de gebruiker-een soort Julia Simon in kleine verbeteringen die is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met kleine verbeteringen:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **smallity** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Kleine verbeteringen domein en Url's eenmalige aanmelding](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<subdomain>.small-improvements.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<subdomain>.small-improvements.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met het [ondersteunings team van smallity-client](mailto:support@small-improvements.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer op de sectie **kleine verbeteringen instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-small-improvements-single-sign-on"></a>Eenmalige aanmelding met kleine verbeteringen configureren

1. Meld u in een ander browser venster aan bij de bedrijfs site met kleine verbeteringen als beheerder.

1. Klik op de hoofd pagina van het dash board op de knop **beheer** aan de linkerkant.

    ![Eenmalige aanmelding configureren](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

1. Klik op de knop **SAML SSO** van de sectie **integraties** .

    ![Eenmalige aanmelding configureren](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

1. Voer de volgende stappen uit op de pagina SSO-installatie:

    ![Eenmalige aanmelding configureren](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. Plak in het tekstvak **http-eind punt** de waarde van de **aanmeldings-URL**die u van Azure Portal hebt gekopieerd.

    b. Open het gedownloade certificaat in Klad blok, kopieer de inhoud en plak het in het tekstvak **x509-certificaat** . 

    c. Als u de optie voor aanmelding voor SSO en aanmeldings formulier beschikbaar wilt stellen voor gebruikers, schakelt u de optie **toegang via aanmelden/wacht woord inschakelen** in.  

    d. Voer de juiste waarde in voor de naam van de SSO-aanmeldings knop in het tekstvak **SAML-prompt** .  

    e. Klik op **Opslaan**.

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

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot kleine verbeteringen.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **kleine verbeteringen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **kleine verbeteringen**.

    ![De koppeling kleine verbeteringen in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-small-improvements-test-user"></a>Een kleine verbetering test gebruiker maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij kleine verbeteringen, moeten ze worden ingericht in kleine verbeteringen. In het geval van kleine verbeteringen is inrichting een hand matige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u als beheerder aan bij uw kleine, zakelijke site met verbeteringen.

1. Ga vanaf de start pagina naar het menu aan de linkerkant en klik op **beheer**.

1. Klik op de knop **gebruikers lijst** in het gedeelte gebruikers beheer.

    ![Een Azure AD-testgebruiker maken](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

1. Klik op **gebruikers toevoegen**.

    ![Een Azure AD-testgebruiker maken](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

1. Voer de volgende stappen uit in het dialoog venster **gebruikers toevoegen** : 

    ![Een Azure AD-testgebruiker maken](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)

    a. Voer de **voor naam** van de gebruiker in, zoals **Julia**.

    b. Voer de **Achternaam** van de gebruiker in, zoals **Simon**.

    c. Voer het **e-mail adres** van de gebruiker in **brittasimon@contoso.com**.

    d. U kunt er ook voor kiezen om het persoonlijke bericht in te voeren in het vak **e-mail bericht verzenden** . Schakel dit selectie vakje uit als u de melding niet wilt verzenden.

    e. Klik op **gebruikers maken**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel kleine verbeteringen in het toegangs venster klikt, wordt u automatisch aangemeld bij de kleine verbeteringen waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)