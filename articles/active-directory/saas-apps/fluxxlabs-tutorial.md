---
title: 'Zelfstudie: Azure Active Directory-integratie met Fluxx Labs | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Fluxx Labs.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d8fac770-bb57-4e1f-b50b-9ffeae239d07
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: e624520a9d1f39bc8115ac72e9df0398065928f1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67102393"
---
# <a name="tutorial-azure-active-directory-integration-with-fluxx-labs"></a>Zelfstudie: Azure Active Directory-integratie met Fluxx Labs

In deze zelfstudie leert u hoe u Fluxx Labs integreert met Azure Active Directory (Azure AD).
De integratie van Fluxx Labs met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Fluxx Labs.
* U uw gebruikers automatisch laten inloggen bij Fluxx Labs (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie met Fluxx Labs wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Fluxx Labs single sign-on enabled subscription

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Fluxx Labs ondersteunt **IDP** geïnitieerde SSO

## <a name="adding-fluxx-labs-from-the-gallery"></a>Fluxx Labs toevoegen vanuit de galerie

Als u de integratie van Fluxx Labs in Azure AD wilt configureren, moet u Fluxx Labs vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Fluxx Labs vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Selecteer **Fluxx Labs**in het zoekvak en selecteer **Fluxx Labs** in het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![Fluxx Labs in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on bij Fluxx Labs op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Fluxx Labs.

Als u Azure AD-singlesign-aan met Fluxx Labs wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Fluxx Labs Single Sign-On](#configure-fluxx-labs-single-sign-on)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak De testgebruiker](#create-fluxx-labs-test-user)** van Fluxx Labs - om een tegenhanger van Britta Simon in Fluxx Labs te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om azure AD single sign-on te configureren met Fluxx Labs:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Fluxx** Labs-toepassingsintegratie de optie **Enkele aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    ![Fluxx Labs Domain en URL's single sign-on informatie](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: 

    | Omgeving | URL-patroon|
    |-------------|------------|
    | Productie | `https://<subdomain>.fluxx.io` |
    | Voorproductie | `https://<subdomain>.preprod.fluxxlabs.com`|

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: 

    | Omgeving | URL-patroon|
    |-------------|------------|
    | Productie | `https://<subdomain>.fluxx.io/auth/saml/callback` |
    | Voorproductie | `https://<subdomain>.preprod.fluxxlabs.com/auth/saml/callback`|

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met [het ondersteuningsteam van Fluxx Labs](mailto:travis@fluxxlabs.com) Om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **Fluxx Labs instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-fluxx-labs-single-sign-on"></a>Fluxx Labs-aanmelding configureren

1. Meld u in een ander browservenster aan bij uw bedrijfsite van Fluxx Labs als beheerder.

2. Selecteer **Beheerder** onder de sectie **Instellingen.**

    ![Fluxx Labs-configuratie](./media/fluxxlabs-tutorial/config1.png)

3. Selecteer in het beheerpaneel **Integraties van plug-ins** > **Integrations** en selecteer **SAML SSO(Disabled)**

    ![Fluxx Labs-configuratie](./media/fluxxlabs-tutorial/config2.png)

4. Voer in de sectie kenmerk de volgende stappen uit:

    ![Fluxx Labs-configuratie](./media/fluxxlabs-tutorial/config3.png)

    a. Schakel het **selectievakje SAML SSO** in.

    b. Typ **/auth/saml**in het tekstvak **Pad aanvragen** .

    c. Typ in het tekstvak **Callback Path** **/auth/saml/callback**.

    d. Voer in het tekstvak Url van de bevestigingsservice van de **consumentenservice (URL voor aanmelding smaken)** de **URL-waarde van antwoord** in, die u hebt ingevoerd in de Azure-portal.

    e. Voer in het tekstvak **Doelgroep(SP Entity ID)** de **id-waarde** in die u hebt ingevoerd in de Azure-portal.

    f. Plak in het tekstvak **URL-url van de identiteitsprovider SSO Target** de **waarde van de inlog-URL,** die u hebt gekopieerd van de Azure-portal.

    g. Open uw basis-64 gecodeerd certificaat in kladblok, kopieer de inhoud ervan in uw klembord en plak het vervolgens in het textbox van het **identiteitsprovidercertificaat.**

    h. Voer in **tekstvak Naam-id-indeling** de waarde `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`in .

    i. Klik op **Opslaan**.

    > [!NOTE]
    > Zodra de inhoud is opgeslagen, wordt het veld leeg weergegeven voor beveiliging, maar is de waarde opgeslagen in de configuratie.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In **User name** het veld brittasimon@yourcompanydomain.extensiontype gebruikersnaam . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Fluxx Labs.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Vervolgens Fluxx Labs**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Fluxx Labs**in de lijst met toepassingen .

    ![De Fluxx Labs-koppeling in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-fluxx-labs-test-user"></a>Fluxx Labs-testgebruiker maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij Fluxx Labs, moeten ze worden ingericht in Fluxx Labs. In het geval van Fluxx Labs is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij uw bedrijf Fluxx Labs als beheerder.

2. Klik op het onderstaande weergegeven **pictogram**.

    ![Fluxx Labs-configuratie](./media/fluxxlabs-tutorial/config6.png)

3. Klik op het dashboard op het onderstaande weergegeven pictogram om de **Nieuwe PERSONEN-kaart** te openen.

    ![Fluxx Labs-configuratie](./media/fluxxlabs-tutorial/config4.png)

4. Voer in de sectie **NIEUWE MENSEN** de volgende stappen uit:

    ![Fluxx Labs-configuratie](./media/fluxxlabs-tutorial/config5.png)

    a. Fluxx Labs gebruiken e-mail als de unieke id voor SSO logins. Vul het **SSO UID-veld** in met het e-mailadres van de gebruiker, dat overeenkomt met het e-mailadres dat ze gebruiken als login bij SSO.

    b. Klik op **Opslaan**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Fluxx Labs in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Fluxx Labs waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

