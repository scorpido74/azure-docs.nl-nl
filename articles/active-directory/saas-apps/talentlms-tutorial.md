---
title: 'Zelf studie: integratie Azure Active Directory met TalentLMS | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en TalentLMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c903d20d-18e3-42b0-b997-6349c5412dde
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 970ab9de270b1227884a13ac578d4c439043b20c
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74233367"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Zelf studie: integratie Azure Active Directory met TalentLMS

In deze zelf studie leert u hoe u TalentLMS integreert met Azure Active Directory (Azure AD).
Het integreren van TalentLMS met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot TalentLMS.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij TalentLMS (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met TalentLMS wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* Abonnement voor eenmalige aanmelding TalentLMS ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* TalentLMS ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-talentlms-from-the-gallery"></a>TalentLMS toevoegen uit de galerie

Als u de integratie van TalentLMS in azure AD wilt configureren, moet u TalentLMS uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om TalentLMS toe te voegen uit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **TalentLMS**in het zoekvak, selecteer **TalentLMS** in het resultaten paneel en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![TalentLMS in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met TalentLMS op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in TalentLMS tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met TalentLMS, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[TalentLMS eenmalige aanmelding configureren](#configure-talentlms-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een TalentLMS-test gebruiker](#create-talentlms-test-user)** -om een equivalent van Julia Simon in TalentLMS te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met TalentLMS:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **TalentLMS** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor TalentLMS domein en Url's](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<tenant-name>.TalentLMSapp.com`

    b. In het tekstvak **Id (entiteits-id)** typt u een URL met het volgende patroon: `http://<tenant-name>.talentlms.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [ondersteunings team van TalentLMS-clients](https://www.talentlms.com/contact) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

6. Kopieer in de sectie **SAML-handtekeningcertificaat** de waarde voor **VINGERAFDRUK** en sla deze op de computer op.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

7. Kopieer op de sectie **TalentLMS instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-talentlms-single-sign-on"></a>Eenmalige aanmelding voor TalentLMS configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij uw TalentLMS-bedrijfs site.

1. Klik in de sectie **Account & instellingen** op het tabblad **gebruikers** .

    ![Instellingen voor account &](./media/talentlms-tutorial/IC777296.png "Instellingen voor account &")

1. Klik op **eenmalige aanmelding (SSO)** ,

1. Voer in de sectie Eenmalige aanmelding de volgende stappen uit:

    ![Eenmalige aanmelding](./media/talentlms-tutorial/IC777297.png "Eenmalige aanmelding")

    a. Selecteer **SAML 2,0**in de lijst **type SSO-integratie** .

    b. Plak in het tekstvak **ID-provider (IDP)** de waarde van de **Azure ad-id**die u van Azure Portal hebt gekopieerd.

    c. Plak de waarde van de **vinger afdruk** van Azure Portal in het tekstvak **certificaat vingerafdruk** .

    d.  Plak in het tekstvak **URL voor externe aanmelding** de waarde van de **aanmeldings-URL**die u van Azure Portal hebt gekopieerd.

    e. Plak in het tekstvak **URL voor externe aanmelding** de waarde van de **afmeldings-URL**die u van Azure Portal hebt gekopieerd.

    f. Vul het volgende in:

    * Typ in het tekstvak **TargetedID** `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`

    * In het tekstvak **voor de voor naam** typt u `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    * In het tekstvak **Achternaam** typt u `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    * In het tekstvak **e-mail** typt u `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

1. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon**in.
  
    b. Typ `brittasimon@yourcompanydomain.extension`in het veld **gebruikers naam** . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan TalentLMS.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **TalentLMS**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **TalentLMS**.

    ![De koppeling TalentLMS in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-talentlms-test-user"></a>TalentLMS-test gebruiker maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij TalentLMS, moeten ze worden ingericht in TalentLMS. In het geval van TalentLMS is inrichting een hand matige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan bij uw **TalentLMS** -Tenant.

1. Klik op **gebruikers**en klik vervolgens op **gebruiker toevoegen**.

1. Voer de volgende stappen uit op de pagina **gebruikers toevoegen** :

    ![Gebruiker toevoegen](./media/talentlms-tutorial/IC777299.png "Gebruiker toevoegen")  

    a. Voer in het tekstvak **First name** de voornaam van de gebruiker in, zoals **Britta**.

    b. Voer in het tekstvak **Last name** de achternaam van de gebruiker in, zoals **Simon**.
 
    c. Voer in het tekstvak **e-mail adres** het e-mail bericht van de gebruiker in, zoals `brittasimon\@contoso.com`.

    d. Klik op **Add User**.

> [!NOTE]
> U kunt alle andere hulpprogram ma's voor het maken van TalentLMS-gebruikers accounts of Api's die worden geleverd door TalentLMS, gebruiken om Azure AD-gebruikers accounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel TalentLMS in het toegangs venster klikt, moet u automatisch worden aangemeld bij de TalentLMS waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

