---
title: 'Zelfstudie: Azure Active Directory-integratie met kleine verbeteringen | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Kleine verbeteringen.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67090363"
---
# <a name="tutorial-azure-active-directory-integration-with-small-improvements"></a>Zelfstudie: Azure Active Directory-integratie met kleine verbeteringen

In deze zelfstudie leert u hoe u kleine verbeteringen integreren met Azure Active Directory (Azure AD).
Het integreren van kleine verbeteringen met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot kleine verbeteringen.
* U uw gebruikers automatisch laten aanmelden bij Kleine verbeteringen (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie wilt configureren met kleine verbeteringen, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement met één aanmelding voor kleine verbeteringen

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Small Improvements ondersteunt **SP** geïnitieerde SSO

## <a name="adding-small-improvements-from-the-gallery"></a>Kleine verbeteringen toevoegen vanuit de galerie

Als u de integratie van kleine verbeteringen in Azure AD wilt configureren, moet u kleine verbeteringen uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om kleine verbeteringen uit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **kleine verbeteringen**in het zoekvak en selecteer **Kleine verbeteringen** in het resultaatpaneel en klik op Knop **Toevoegen** om de toepassing toe te voegen.

     ![Kleine verbeteringen in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Kleine verbeteringen op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Kleine verbeteringen.

Als u Azure AD-enkele aanmelding wilt configureren en testen met kleine verbeteringen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureren Kleine verbeteringen Eenmalig aanmelden](#configure-small-improvements-single-sign-on)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak kleine verbeteringen test gebruiker](#create-small-improvements-test-user)** - om een tegenhanger van Britta Simon in kleine verbeteringen die is gekoppeld aan de Azure AD vertegenwoordiging van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met kleine verbeteringen te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Kleine verbeteringen** applicatie-integratie de optie **Enkele aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over kleine verbeteringen domein en URL's eenmalig aanmelden](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<subdomain>.small-improvements.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<subdomain>.small-improvements.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met [Small Improvements Client support team](mailto:support@small-improvements.com) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **Kleine verbeteringen instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-small-improvements-single-sign-on"></a>Kleine verbeteringen eenmalig aanmelden configureren

1. Meld u in een ander browservenster aan op uw bedrijfssite voor kleine verbeteringen als beheerder.

1. Klik op de hoofddashboardpagina op **Knop Beheer** aan de linkerkant.

    ![Eenmalige aanmelding configureren](./media/smallimprovements-tutorial/tutorial_smallimprovements_06.png) 

1. Klik op de **SAML SSO-knop** uit de sectie **Integraties.**

    ![Eenmalige aanmelding configureren](./media/smallimprovements-tutorial/tutorial_smallimprovements_07.png) 

1. Voer op de pagina SSO Setup de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/smallimprovements-tutorial/tutorial_smallimprovements_08.png)  

    a. Plak in het **tekstvak HTTP-eindpunt** de waarde van **de aanmeldings-URL**, die u hebt gekopieerd van azure-portal.

    b. Open het gedownloade certificaat in Kladblok, kopieer de inhoud en plak het vervolgens in het tekstvak **x509-certificaat.** 

    c. Als u de verificatieoptie SSO en Inlogformulier beschikbaar wilt hebben voor gebruikers, schakelt u de optie **Toegang via login/wachtwoord ook inschakelen in.**  

    d. Voer de juiste waarde in om de knop SSO-aanmelding te benoemen in het tekstvak **SAML Prompt.**  

    e. Klik op **Opslaan**.

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

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot kleine verbeteringen.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer Vervolgens **Kleine verbeteringen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Kleine verbeteringen**in de lijst met toepassingen .

    ![De koppeling Kleine verbeteringen in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-small-improvements-test-user"></a>Kleine verbeteringen testgebruiker maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij Kleine verbeteringen, moeten ze worden ingericht in kleine verbeteringen. In het geval van kleine verbeteringen is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij uw bedrijfssite voor kleine verbeteringen als beheerder.

1. Ga op de startpagina naar het menu aan de linkerkant, klik op **Beheer**.

1. Klik op de knop **Gebruikersmap** in de sectie Gebruikersbeheer.

    ![Een Azure AD-testgebruiker maken](./media/smallimprovements-tutorial/tutorial_smallimprovements_10.png) 

1. Klik **op Gebruikers toevoegen**.

    ![Een Azure AD-testgebruiker maken](./media/smallimprovements-tutorial/tutorial_smallimprovements_11.png) 

1. Voer **in** het dialoogvenster Gebruikers toevoegen de volgende stappen uit: 

    ![Een Azure AD-testgebruiker maken](./media/smallimprovements-tutorial/tutorial_smallimprovements_12.png)

    a. Voer de **voornaam** van de gebruiker in, zoals **Britta.**

    b. Voer de **achternaam** van de gebruiker in, zoals **Simon**.

    c. Voer de **e-mail** van de gebruiker als **brittasimon@contoso.com**.

    d. U er ook voor kiezen om het persoonlijke bericht in het **e-mailvak Bericht verzenden** in te voeren. Als u de melding niet wilt verzenden, schakelt u dit selectievakje uit.

    e. Klik **op Gebruikers maken**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Kleine verbeteringen in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de kleine verbeteringen waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)