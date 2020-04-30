---
title: 'Zelf studie: integratie Azure Active Directory met Kantega SSO voor confluence | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Kantega SSO voor confluence.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d0d99c14-a6ca-45f2-bb84-633126095e7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 27fa0567eefbb50907c0ed6952333230e874c21d
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67099042"
---
# <a name="tutorial-azure-active-directory-integration-with-kantega-sso-for-confluence"></a>Zelf studie: integratie Azure Active Directory met Kantega SSO voor confluence

In deze zelf studie leert u hoe u Kantega SSO integreert voor confluence met Azure Active Directory (Azure AD).
Het integreren van Kantega SSO voor confluence met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot Kantega SSO voor confluence.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij Kantega SSO voor confluence (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Kantega SSO voor confluence wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* Kantega SSO voor confluence-abonnement met eenmalige aanmelding

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Kantega SSO voor confluence ondersteunt **SP en IDP** GEÏNITIEERDe SSO

## <a name="adding-kantega-sso-for-confluence-from-the-gallery"></a>Kantega SSO voor confluence toevoegen vanuit de galerie

Als u de integratie van Kantega SSO voor confluence wilt configureren in azure AD, moet u Kantega SSO voor confluence vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Kantega SSO voor confluence toe te voegen aan de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **KANTEGA SSO voor confluence**in het zoekvak, selecteer **Kantega SSO voor confluence** in het deel venster resultaten en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![Kantega SSO voor confluence in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Kantega SSO voor confluence op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Kantega SSO voor confluence tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met Kantega SSO voor confluence, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[KANTEGA SSO voor confluence](#configure-kantega-sso-for-confluence-single-sign-on)** -eenmalige aanmelding configureren: Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[KANTEGA SSO maken voor confluence test gebruiker](#create-kantega-sso-for-confluence-test-user)** : als u een equivalent van Julia Simon in Kantega SSO wilt hebben voor confluence dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met Kantega SSO voor confluence:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **Kantega SSO voor confluence** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Als u de toepassing in de gestarte modus van **IDP** wilt configureren, voert u de volgende stappen uit in de sectie **basis configuratie van SAML** :

    ![Kantega SSO voor confluence-domein en Url's eenmalige aanmelding](common/idp-intiated.png)

    a. Typ in het tekstvak **id** een URL met het volgende patroon:`https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Kantega SSO voor confluence-domein en Url's eenmalige aanmelding](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<server-base-url>/plugins/servlet/no.kantega.saml/sp/<uniqueid>/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en aanmeldings-URL. Deze waarden krijgt u tijdens de configuratie van Confluence, wat later in de zelfstudie wordt uitgelegd.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

7. Kopieer op de sectie **KANTEGA SSO voor confluence instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-kantega-sso-for-confluence-single-sign-on"></a>Kantega SSO configureren voor eenmalige aanmelding voor confluence

1. Meld u in een ander webbrowser venster als beheerder aan bij uw **confluence-beheer Portal** .

1. Wijs het tandwiel aan met de muisaanwijzer en klik op **Add-ons**.

    ![Eenmalige aanmelding configureren](./media/kantegassoforconfluence-tutorial/addon1.png)

1. Op het tabblad **ATLASSIAN MARKETPLACE** klikt u op **Nieuwe invoegtoepassingen zoeken**.

    ![Eenmalige aanmelding configureren](./media/kantegassoforconfluence-tutorial/addon.png)

1. Doorzoek **KANTEGA SSO voor CONFLUENCE SAML Kerberos** en klik op de knop **installeren** om de nieuwe SAML-invoeg toepassing te installeren.

    ![Eenmalige aanmelding configureren](./media/kantegassoforconfluence-tutorial/addon2.png)

1. De installatie van de invoeg toepassing wordt gestart.

    ![Eenmalige aanmelding configureren](./media/kantegassoforconfluence-tutorial/addon3.png)

1. Zodra de installatie is voltooid. Klik op **Sluiten**.

    ![Eenmalige aanmelding configureren](./media/kantegassoforconfluence-tutorial/addon33.png)

1. Klik op **Beheren**.

    ![Eenmalige aanmelding configureren](./media/kantegassoforconfluence-tutorial/addon34.png)

1. Klik op **Configure** om de nieuwe invoegtoepassing te configureren.

    ![Eenmalige aanmelding configureren](./media/kantegassoforconfluence-tutorial/addon35.png)

1. Deze nieuwe invoegtoepassing is ook terug te vinden op het tabblad **GEBRUIKERS EN BEVEILIGING**.

    ![Eenmalige aanmelding configureren](./media/kantegassoforconfluence-tutorial/addon36.png)

1. In de sectie **SAML** . Selecteer **Azure Active Directory (Azure AD)** in de vervolg keuzelijst **ID-provider toevoegen** .

    ![Eenmalige aanmelding configureren](./media/kantegassoforconfluence-tutorial/addon4.png)

1. Selecteer abonnements niveau als **basis**.

    ![Eenmalige aanmelding configureren](./media/kantegassoforconfluence-tutorial/addon5.png)

1. Voer de volgende stappen uit in de sectie **App-eigenschappen** :

    ![Eenmalige aanmelding configureren](./media/kantegassoforconfluence-tutorial/addon6.png)

    a. Kopieer de **URI-waarde van de App-ID** en gebruik deze als **id, antwoord-URL en aanmeldings-URL** in het gedeelte **basis configuratie van SAML** in azure Portal.

    b. Klik op **Volgende**.

1. Voer de volgende stappen uit in de sectie **import van meta gegevens** : 

    ![Eenmalige aanmelding configureren](./media/kantegassoforconfluence-tutorial/addon7.png)

    a. Selecteer **het meta gegevensbestand op mijn computer**en upload het meta gegevensbestand dat u hebt gedownload van Azure Portal.

    b. Klik op **Volgende**.

1. Voer de volgende stappen uit in de sectie **naam en SSO-locatie** :

    ![Eenmalige aanmelding configureren](./media/kantegassoforconfluence-tutorial/addon8.png)

    a. Voeg de naam van de ID-provider toe in de naam van de **ID-provider** (bijvoorbeeld Azure AD).

    b. Klik op **Volgende**.

1. Controleer het handtekening certificaat en klik op **volgende**.

    ![Eenmalige aanmelding configureren](./media/kantegassoforconfluence-tutorial/addon9.png)

1. Voer de volgende stappen uit in de sectie **confluence-gebruikers accounts** :

    ![Eenmalige aanmelding configureren](./media/kantegassoforconfluence-tutorial/addon10.png)

    a. Selecteer **indien nodig gebruikers in de interne Directory van confluence maken** en voer de juiste naam van de groep voor gebruikers in (kan meerdere Nee zijn. van groepen gescheiden door komma's).

    b. Klik op **Volgende**.

1. Klik op **Voltooien**.

    ![Eenmalige aanmelding configureren](./media/kantegassoforconfluence-tutorial/addon11.png)

1. Voer de volgende stappen uit in de sectie **bekende domeinen voor Azure AD** : 

    ![Eenmalige aanmelding configureren](./media/kantegassoforconfluence-tutorial/addon12.png)

    a. Selecteer **bekende domeinen** in het linkerdeel venster van de pagina.

    b. Voer een domein naam in het tekstvak **bekende domeinen** in.

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
  
    b. In het veld **Gebruikersnaam** typt u `brittasimon@yourcompanydomain.extension`.  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Kantega SSO voor confluence.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **Kantega SSO voor confluence**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **KANTEGA SSO voor confluence**.

    ![De koppeling Kantega SSO voor confluence in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-kantega-sso-for-confluence-test-user"></a>Kantega SSO maken voor confluence test gebruiker

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij confluence, moeten ze worden ingericht in confluence. In het geval van Kantega SSO voor confluence is inrichting een hand matige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij uw Kantega SSO voor confluence-bedrijfs site als beheerder.

1. Wijs het tandwiel aan met de muisaanwijzer en klik op **User management**.

    ![Werknemer toevoegen](./media/kantegassoforconfluence-tutorial/user1.png)

1. Klik onder gebruikers op tabblad **gebruikers toevoegen** . Voer de volgende stappen uit op de dialoog pagina **een gebruiker toevoegen** :

    ![Werknemer toevoegen](./media/kantegassoforconfluence-tutorial/user2.png)

    a. In het tekstvak **Gebruikersnaam** typt u het e-mailadres van de gebruiker, bijvoorbeeld Brittasimon@contoso.com.

    b. Typ in het tekstvak **Full Name** de volledige naam van de gebruiker, zoals Britta Simon.

    c. Typ in het tekstvak **Email** het e-mailadres van de gebruiker, bijvoorbeeld Brittasimon@contoso.com.

    d. Typ in het tekstvak **wacht woord** het wacht woord voor de gebruiker.

    e. Typ het wachtwoord ter bevestiging in het vak **Confirm Password**.

    f. Klik op de knop **Add**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Kantega SSO voor confluence in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Kantega SSO voor confluence waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

