---
title: 'Zelf studie: integratie Azure Active Directory met Ziflow | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Ziflow.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84e60fa4-36fb-49c4-a642-95538c78f926
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: d9745bdb1cb6de86a96946564865958433d49732
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67086196"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Zelf studie: integratie Azure Active Directory met Ziflow

In deze zelf studie leert u hoe u Ziflow integreert met Azure Active Directory (Azure AD).
Het integreren van Ziflow met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot Ziflow.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij Ziflow (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Ziflow wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* Abonnement voor eenmalige aanmelding Ziflow ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Ziflow ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-ziflow-from-the-gallery"></a>Ziflow toevoegen uit de galerie

Als u de integratie van Ziflow in azure AD wilt configureren, moet u Ziflow uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Ziflow toe te voegen uit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Ziflow**in het zoekvak, selecteer **Ziflow** in het resultaten paneel en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

     ![Ziflow in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Ziflow op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Ziflow tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met Ziflow, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Ziflow eenmalige aanmelding configureren](#configure-ziflow-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een Ziflow-test gebruiker](#create-ziflow-test-user)** -om een equivalent van Julia Simon in Ziflow te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met Ziflow:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **Ziflow** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor Ziflow domein en Url's](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://ziflow-production.auth0.com/login/callback?connection=<UniqueID>`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `urn:auth0:ziflow-production:<UniqueID>`

    > [!NOTE]
    > De bovenstaande waarden zijn niet echt. U gaat de unieke ID-waarde in de id en de aanmeldings-URL bijwerken met de werkelijke waarde, die verderop in de zelf studie wordt uitgelegd.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer op de sectie **Ziflow instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-ziflow-single-sign-on"></a>Eenmalige aanmelding voor Ziflow configureren

1. Meld u in een ander browser venster aan bij Ziflow als een beveiligings beheerder.

2. Klik in de rechter bovenhoek op Avatar en klik vervolgens op **account beheren**.

    ![Ziflow-configuratie beheren](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

3. Klik linksboven op **eenmalige aanmelding**.

    ![Ziflow-configuratie teken](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

4. Voer op de pagina **Single Sign-On** de volgende stappen uit:

    ![Ziflow-configuratie enkelvoudig](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. Selecteer **type** als **SAML 2.0**.

    b. Plak in het tekstvak **URL voor aanmelden** de waarde van de **aanmeldings-URL**die u hebt gekopieerd van de Azure Portal.

    c. Upload het met base 64 gecodeerde certificaat dat u hebt gedownload van de Azure Portal, naar het **x509-handtekening certificaat**.

    d. Plak in het tekstvak afmeldings- **URL** de waarde van de **afmeldings-URL**die u hebt gekopieerd van de Azure Portal.

    e. Kopieer de waarde van de gemarkeerde unieke ID uit de sectie **configuratie-instellingen voor uw ID-provider** en voeg deze toe met de id en de AANMELDINGS-URL in de **basis-SAML-configuratie** op Azure Portal.

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

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Ziflow.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **Ziflow**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Ziflow**.

    ![De koppeling Ziflow in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-ziflow-test-user"></a>Ziflow-test gebruiker maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij Ziflow, moeten ze worden ingericht in Ziflow. In Ziflow is inrichten een hand matige taak.

Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:

1. Meld u aan bij Ziflow als een beveiligings beheerder.

2. Navigeer naar **personen** aan de bovenkant.

    ![Ziflow-configuratie personen](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. Klik op **toevoegen** en klik vervolgens op **gebruiker toevoegen**.

    ![Ziflow-configuratie gebruiker toevoegen](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. Voer de volgende stappen uit op de pop-up **gebruiker toevoegen** :

    ![Ziflow-configuratie gebruiker toevoegen](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. Voer in het tekstvak **Email** het e-mailadres van de gebruiker in, zoals brittasimon@contoso.com.

    b. Voer in het tekstvak **voor naam** de voor naam van de gebruiker in, zoals Julia.

    c. Voer in het tekstvak **laatste naam** de achternaam van de gebruiker in, zoals Simon.

    d. Selecteer uw Ziflow-rol.

    e. Klik op **1 gebruiker toevoegen**.

    > [!NOTE]
    > De houder van het Azure Active Directory-account ontvangt een e-mail en volgt een koppeling om het account te bevestigen voordat het actief wordt.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Ziflow in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Ziflow waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

