---
title: 'Zelfstudie: Azure Active Directory-integratie met Peakon | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Peakon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a944c397-ed3f-4d45-b9b2-6d4bcb6b0a09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: b093a26848701254ad674081037c266f1fb012b2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094726"
---
# <a name="tutorial-azure-active-directory-integration-with-peakon"></a>Zelfstudie: Azure Active Directory-integratie met Peakon

In deze zelfstudie leert u hoe u Peakon integreert met Azure Active Directory (Azure AD).
De integratie van Peakon met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Peakon.
* U uw gebruikers automatisch laten aanmelden bij Peakon (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie met Peakon wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement op single sign-on van Peakon

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Peakon ondersteunt **SP** en **IDP** gestart met SSO

## <a name="adding-peakon-from-the-gallery"></a>Peakon toevoegen vanuit de galerie

Als u de integratie van Peakon in Azure AD wilt configureren, moet u Peakon vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Peakon vanuit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Peakon**in het zoekvak , selecteer **Piek in** het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![Peakon in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Peakon op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Peakon.

Als u Azure AD-singlesign-aan met Peakon wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Peakon Single Sign-On](#configure-peakon-single-sign-on)** - om de instellingen voor eenmalig aanmelden aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak Peakon-testgebruiker](#create-peakon-test-user)** - om een tegenhanger van Britta Simon in Peakon te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met Peakon te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Peakon-toepassingsintegratie** de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **BasisSAML-configuratie** de volgende stappen uit als u de toepassing in de **idp-modus** wilt configureren:

    ![Informatie over peakon-domein en URL's met eenmalige aanmelding](common/idp-intiated.png)

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://app.peakon.com/saml/<companyid>/metadata`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://app.peakon.com/saml/<companyid>/assert`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Informatie over peakon-domein en URL's met eenmalige aanmelding](common/metadata-upload-additional-signon.png)

    Typ een URL in het tekstvak **AANmeldings-URL:**`https://app.peakon.com/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id- en antwoord-URL die later in de zelfstudie wordt uitgelegd. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Raw)** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificateraw.png)

7. Kopieer in de sectie **Peakon instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-peakon-single-sign-on"></a>Peakon Single Sign-On configureren

1. Meld u in een ander browservenster aan bij Peakon als beheerder.

2. Klik op de menubalk aan de linkerkant van de pagina op **Configuratie**en navigeer vervolgens naar **Integraties**.

    ![De Config](./media/peakon-tutorial/tutorial_peakon_config.png)

3. Klik op de pagina **Integraties** op **Enkele aanmelding**.

    ![De Single](./media/peakon-tutorial/tutorial_peakon_single.png)

4. Klik onder de sectie Aanmelding voor **één aanmelding** op **Inschakelen**.

    ![De enable](./media/peakon-tutorial/tutorial_peakon_enable.png)

5. Voer in de sectie **Eén aanmelding voor werknemers die SAML gebruiken** de volgende stappen uit:

    ![De saml](./media/peakon-tutorial/tutorial_peakon_saml.png)

    a. Plak in het tekstvak van de URL van **SSO-aanmelding** de waarde van **de aanmeldings-URL**, die u hebt gekopieerd van de Azure-portal.

    b. Plak in het tekstvak **URL-logboeken van SSO** de waarde van **de URL van afmelden**, die u hebt gekopieerd uit de Azure-portal.

    c. Klik **op Bestand kiezen** om het certificaat dat u hebt gedownload van de Azure-portal te uploaden naar het vak Certificaat.

    d. Klik **op** het pictogram om de **entiteits-id** te kopiëren en in het tekstvak-Id te plakken in de sectie **BasisSAML-configuratie** op azure-portal. **Identifier**

    e. Klik **op** het pictogram om de **URL van antwoord (ACS)** te kopiëren en plak in het tekstvak **Van de URL van antwoord** in de sectie **BasisSAML-configuratie** op azure-portal.

    f. Klik **op Opslaan**

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon@yourcompanydomain.extension**.  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u Britta Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Peakon.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Vervolgens Peakon**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Peakon**in de lijst met toepassingen .

    ![De koppeling Peakon in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-peakon-test-user"></a>Peakon-testgebruiker maken

Als u Azure AD-gebruikers instaat wilt stellen zich aan te melden bij Peakon, moeten ze zijn ingericht in Peakon.  
In het geval van Peakon is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij uw Peakon-bedrijfssite als beheerder.

2. Klik op de menubalk aan de linkerkant van de pagina op **Configuratie**en navigeer vervolgens naar **Werknemers**.

    ![De werknemer](./media/peakon-tutorial/tutorial_peakon_employee.png)

3. Klik rechtsboven op de pagina op **Werknemer toevoegen**.

      ![De werknemer toevoegen](./media/peakon-tutorial/tutorial_peakon_addemployee.png)

3. Voer op de pagina **Nieuw werknemers** de volgende stappen uit:

     ![De nieuwe werknemer](./media/peakon-tutorial/tutorial_peakon_create.png)

    a. Typ in het **tekstvak Naam** voornaam als **Britta** en achternaam als **simon**.

    b. Typ in het **tekstvak E-mail** het e-mailadres zoals **Brittasimon\@contoso.com**.

    c. Klik **op Werknemer maken**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Peakon in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Peakon waarvoor u SSO instelt. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

