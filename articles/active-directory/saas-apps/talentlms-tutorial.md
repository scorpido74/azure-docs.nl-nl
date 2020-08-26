---
title: 'Zelfstudie: Azure Active Directory-integratie met TalentLMS | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding tussen Azure Active Directory en TalentLMS configureert.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.openlocfilehash: 57445d1010e9775e3a1735fd64722c44db8e0a4e
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88546580"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Zelfstudie: Azure Active Directory-integratie met TalentLMS

In deze zelfstudie leert u hoe u TalentLMS integreert met Azure Active Directory (Azure AD).
De integratie van TalentLMS met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot TalentLMS.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij TalentLMS (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie te configureren met TalentLMS:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op TalentLMS waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* TalentLMS ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-talentlms-from-the-gallery"></a>TalentLMS toevoegen vanuit de galerie

Als u de integratie van TalentLMS in Azure AD wilt configureren, moet u TalentLMS vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

**Ga als volgt te werk om TalentLMS vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **TalentLMS**, selecteer **TalentLMS** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![TalentLMS in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte gaat u eenmalige aanmelding van Azure AD met TalentLMS configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure Active Directory-gebruiker en de daaraan gerelateerde gebruiker in TalentLMS tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met TalentLMS, moet u de volgende procedures uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor TalentLMS configureren](#configure-talentlms-single-sign-on)** : de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor TalentLMS maken](#create-talentlms-test-user)** : als u een tegenhanger van Britta Simon in TalentLMS wilt hebben die is gekoppeld aan de Azure Active Directory-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren met TalentLMS:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina met de integratie van de toepassing **TalentLMS** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij TalentLMS](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<tenant-name>.TalentLMSapp.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `http://<tenant-name>.talentlms.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [clientondersteuningsteam van TalentLMS](https://www.talentlms.com/contact) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

6. Kopieer in de sectie **SAML-handtekeningcertificaat** de waarde voor **VINGERAFDRUK** en sla deze op de computer op.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

7. In het gedeelte **TalentLMS instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-talentlms-single-sign-on"></a>Eenmalige aanmelding voor TalentLMS configureren

1. Meld u in een ander browservenster als beheerder aan bij uw TalentLMS-bedrijfssite.

1. Klik in de sectie **Account en instellingen** op het tabblad **Gebruikers**.

    ![Account en instellingen](./media/talentlms-tutorial/IC777296.png "Account en instellingen")

1. Klik op **Eenmalige aanmelding**.

1. Voer in de sectie Eenmalige aanmelding de volgende stappen uit:

    ![Single Sign-On](./media/talentlms-tutorial/IC777297.png "Eenmalige aanmelding")

    a. Selecteer **SAML 2.0**in de lijst **Type SSO-integratie**.

    b. Plak de waarde van **Azure Ad-id**, die u hebt gekopieerd uit de Azure-portal, in het tekstvak **Identity Provider (IDP)** .

    c. Plak de waarde van de **Vingerafdruk** uit de Azure-portal in het tekstvak **Vingerafdrukcertificaat**.

    d.  Plak in het tekstvak **URL voor externe aanmelding** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    e. Plak in het tekstvak **URL voor externe afmelding** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit Azure Portal.

    f. Vul het volgende in:

    * Typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` in het tekstvak **TargetedID**

    * Typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` in het vak **Voornaam**

    * Typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` in het vak **Achternaam**

    * Typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` in het tekstvak **E-mailadres**

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
  
    b. In het veld **Gebruikersnaam** typt u `brittasimon@yourcompanydomain.extension`. Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot TalentLMS.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en selecteer vervolgens **TalentLMS**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **TalentLMS** in de lijst met toepassingen.

    ![De koppeling TalentLMS in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-talentlms-test-user"></a>TalentLMS-testgebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij TalentLMS, moeten ze worden ingericht in TalentLMS. In het geval van TalentLMS moet dit handmatig gebeuren.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u aan bij uw **TalentLMS**-tenant.

1. Klik op **Gebruikers** en klik vervolgens op **Gebruiker toevoegen**.

1. Voer de volgende stappen uit in het dialoogvenster **Add a User**:

    ![Gebruiker toevoegen](./media/talentlms-tutorial/IC777299.png "Gebruiker toevoegen")  

    a. Voer in het tekstvak **First name** de voornaam van de gebruiker in, zoals **Britta**.

    b. Voer in het tekstvak **Last name** de achternaam van de gebruiker in, zoals **Simon**.
 
    c. Typ in het tekstvak **Email-adres** het e-mailadres van de gebruiker, bijvoorbeeld `brittasimon\@contoso.com`.

    d. Klik op **Add User**.

> [!NOTE]
> U kunt ook alle andere hulpprogramma's voor het creëren van TalentLMS-gebruikersaccounts of API's van TalentLMS gebruiken om Azure AD-gebruikersaccounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel TalentLMS in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van TalentLMS waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

