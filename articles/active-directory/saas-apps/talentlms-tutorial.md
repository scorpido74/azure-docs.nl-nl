---
title: 'Zelfstudie: Azure Active Directory-integratie met TalentLMS | Microsoft Documenten'
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233367"
---
# <a name="tutorial-azure-active-directory-integration-with-talentlms"></a>Zelfstudie: Azure Active Directory-integratie met TalentLMS

In deze zelfstudie leert u hoe u TalentLMS integreren met Azure Active Directory (Azure AD).
De integratie van TalentLMS met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot TalentLMS.
* U uw gebruikers automatisch laten aanmelden bij TalentLMS (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie met TalentLMS wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/) krijgen
* TalentLMS single sign-on enabled subscription

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* TalentLMS ondersteunt **SP** geïnitieerde SSO

## <a name="adding-talentlms-from-the-gallery"></a>TalentLMS toevoegen vanuit de galerie

Als u de integratie van TalentLMS in Azure AD wilt configureren, moet u TalentLMS vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om TalentLMS vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **TalentLMS**in het zoekvak , selecteer **TalentLMS** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

    ![TalentLMS in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met TalentLMS op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppeling slinken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in TalentLMS.

Als u Azure AD-singlesign-aan met TalentLMS wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer TalentLMS Single Sign-On](#configure-talentlms-single-sign-on)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak TalentLMS-testgebruiker](#create-talentlms-test-user)** - om een tegenhanger van Britta Simon in TalentLMS te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om azure AD single sign-on te configureren met TalentLMS:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **TalentLMS-toepassingsintegratie** de optie **Enkele aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over TalentLMS-domein en URL's met eenmalige aanmelding](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<tenant-name>.TalentLMSapp.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `http://<tenant-name>.talentlms.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met [talentLMS Client support team](https://www.talentlms.com/contact) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

6. Kopieer in de sectie **SAML-handtekeningcertificaat** de waarde voor **VINGERAFDRUK** en sla deze op de computer op.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

7. Kopieer in de sectie **TalentLMS instellen** de juiste URL(s) volgens uw eis.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-talentlms-single-sign-on"></a>TalentLMS-aanmelding configureren

1. Meld u in een ander browservenster aan bij uw TalentLMS-bedrijfssite als beheerder.

1. Klik in de sectie **Account &-instellingen** op het tabblad **Gebruikers.**

    ![Instellingen voor & account](./media/talentlms-tutorial/IC777296.png "Instellingen voor & account")

1. Klik **op Eenmalige aanmelding (SSO),**

1. Voer in de sectie Eenmalige aanmelding de volgende stappen uit:

    ![Eén aanmelding](./media/talentlms-tutorial/IC777297.png "Eenmalige aanmelding")

    a. Selecteer **SAML 2.0**in de lijst **met SSO-integratietypen** .

    b. Plak in het tekstvak **Identity provider (IDP)** de waarde van **Azure AD Identifier**, die u hebt gekopieerd van Azure-portal.

    c. Plak de waarde **Duimafdruk** van Azure-portal in het tekstvak **Certificaat vingerafdruk.**

    d.  Plak in het tekstvak **URL voor aanmelding op afstand** de waarde van de **aanmeldings-URL**, die u hebt gekopieerd van azure-portal.

    e. Plak in het **tekstvak URL voor afmelding op afstand** de waarde van **de URL van afmelden**, die u hebt gekopieerd van azure-portal.

    f. Vul het volgende in:

    * Typ in het tekstvak **TargetedID**`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`

    * Typ in het tekstvak **Voornaam**`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`

    * Typ in het tekstvak **Achternaam**`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`

    * Typ in het tekstvak **E-mail**`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

1. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In **User name** het veld `brittasimon@yourcompanydomain.extension`type gebruikersnaam . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot TalentLMS.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Vervolgens TalentLMS**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **TalentLMS**in de lijst met toepassingen.

    ![De TalentLMS-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-talentlms-test-user"></a>TalentLMS-testgebruiker maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij TalentLMS, moeten ze worden ingericht in TalentLMS. In het geval van TalentLMS is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij uw **TalentLMS-huurder.**

1. Klik **op Gebruikers**en klik vervolgens op Gebruiker **toevoegen.**

1. Voer op de pagina **Gebruiker toevoegen** de volgende stappen uit:

    ![Gebruiker toevoegen](./media/talentlms-tutorial/IC777299.png "Gebruiker toevoegen")  

    a. Voer in het tekstvak **Voornaam** de voornaam van de gebruiker in, zoals **Britta.**

    b. Voer in het tekstvak **Last name** de achternaam van de gebruiker in, zoals **Simon**.
 
    c. Typ in het tekstvak **Email-adres** het e-mailadres van de gebruiker, bijvoorbeeld `brittasimon\@contoso.com`.

    d. Klik **op Gebruiker toevoegen**.

> [!NOTE]
> U alle andere TalentLMS-hulpprogramma's voor het maken van gebruikersaccounts of API's van TalentLMS gebruiken om Azure AD-gebruikersaccounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel TalentLMS in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de TalentLMS waarvoor u SSO instelt. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

