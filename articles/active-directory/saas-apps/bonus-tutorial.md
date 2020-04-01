---
title: 'Zelfstudie: Azure Active Directory-integratie met Bonusly | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Bonusly.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 29fea32a-fa20-47b2-9e24-26feb47b0ae6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea9c88f8eb8ac7b72f11ff286d2294df8cb70860
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74232064"
---
# <a name="tutorial-azure-active-directory-integration-with-bonusly"></a>Zelfstudie: Azure Active Directory-integratie met Bonusly

In deze zelfstudie leert u hoe u Bonusly integreert met Azure Active Directory (Azure AD).
Het integreren van Bonusly met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Bonusly.
* U uw gebruikers automatisch laten inloggen op Bonusly (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie met Bonusly wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Bonusly enkel aanmeldingingeschakeld abonnement

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Bonusly ondersteunt **IDP** geïnitieerde SSO

## <a name="adding-bonusly-from-the-gallery"></a>Bonusly toevoegen vanuit de galerij

Als u de integratie van Bonusly in Azure AD wilt configureren, moet u Bonusly vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Bonusly vanuit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ In het zoekvak **Bonus,** selecteer **Bonusly** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

    ![Bonusly in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Bonusly op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppeling slinken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Bonusly.

Als u Azure AD-single sign-on met Bonusly wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer bonusly single sign-on](#configure-bonusly-single-sign-on)** - om de instellingen voor eenmalig aanmelden aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak bonusly test gebruiker](#create-bonusly-test-user)** - om een tegenhanger van Britta Simon in Bonusly die is gekoppeld aan de Azure AD vertegenwoordiging van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met Bonusly te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Integratie van extra** toepassingen de optie Eén **aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Bonusly Domain en URL's single sign-on informatie](common/idp-reply.png)

    Typ in het tekstvak **URL beantwoorden** een URL met het volgende patroon:`https://Bonus.ly/saml/<tenant-name>`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke antwoord-URL. Neem contact op met [bonusly client support team](https://bonus.ly/contact) om de waarde te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

6. Kopieer in de sectie **SAML-handtekeningcertificaat** de waarde voor **VINGERAFDRUK** en sla deze op de computer op.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

7. Kopieer in de sectie **Bonusly instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-bonusly-single-sign-on"></a>Bonus- aanmelding configureren

1. Meld u in een ander browservenster aan bij uw **Bonusly-tenant.**

1. Klik op de werkbalk bovenaan op **Instellingen** en selecteer **Vervolgens Integraties en apps**.

    ![Bonusly Sociale Sectie](./media/bonus-tutorial/ic773686.png "Bonusly")
1. Selecteer **SAML**onder **Eenmalig aanmelden**.

1. Voer op de pagina **SAML** de volgende stappen uit:

    ![Bonusly Saml Dialog-pagina](./media/bonus-tutorial/ic773687.png "Bonusly")

    a. Plak in het **url-tekstvak van IdP SSO-doel** de waarde van **de aanmeldings-URL**, die u hebt gekopieerd van azure-portal.

    b. Plak in het tekstvak **URL-login-aanmelding** van IdP de waarde van **de aanmeldings-URL**, die u hebt gekopieerd van azure-portal.

    c. Plak in het tekstvak **idp-uitgever** de waarde van **Azure AD-id**, die u hebt gekopieerd van Azure-portal.
    
    d. Plak de **thumbprint-waarde** die is gekopieerd van azure-portal in het tekstvak **Cert Fingerprint.**
    
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
  
    b. In **User name** het veld `brittasimon@yourcompanydomain.extension`type gebruikersnaam . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u Britta Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Bonusly.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer Vervolgens **Bonus .**

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Bonus .**

    ![De bonuslink in de lijst Met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-bonusly-test-user"></a>Bonustestgebruiker maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij Bonusly, moeten ze worden ingericht in Bonusly. In het geval van Bonusly is inrichten een handmatige taak.

> [!NOTE]
> U alle andere tools voor het maken van gebruikersaccounts voor extra gebruikers gebruiken of API's die door Bonusly worden verstrekt om Azure AD-gebruikersaccounts in te richten. 

**Voer de volgende stappen uit om de inrichting van gebruikers te configureren:**

1. Meld u in een webbrowservenster aan bij uw Bonusly-tenant.

1. Klik op **Instellingen**.

    ![Instellingen](./media/bonus-tutorial/ic781041.png "Instellingen")

1. Klik op het tabblad **Gebruikers en bonussen.**

    ![Gebruikers en bonussen](./media/bonus-tutorial/ic781042.png "Gebruikers en bonussen")

1. Klik **op Gebruikers beheren**.

    ![Gebruikers beheren](./media/bonus-tutorial/ic781043.png "Gebruikers beheren")

1. Klik **op Gebruiker toevoegen**.

    ![Gebruiker toevoegen](./media/bonus-tutorial/ic781044.png "Gebruiker toevoegen")

1. Voer in het dialoogvenster **Add User** de volgende stappen uit:

    ![Gebruiker toevoegen](./media/bonus-tutorial/ic781045.png "Gebruiker toevoegen")  

    a. Voer in het tekstvak **Voornaam** de voornaam van de gebruiker in, zoals **Britta.**

    b. Voer in het tekstvak **Last name** de achternaam van de gebruiker in, zoals **Simon**.

    c. Voer in het tekstvak **Email** het e-mailadres van de gebruiker in, bijvoorbeeld `brittasimon\@contoso.com`.

    d. Klik op **Opslaan**.

    > [!NOTE]
    > De houder van het Azure AD-account ontvangt een e-mail met een koppeling om het account te bevestigen voordat het actief wordt.  

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de bonustegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Bonusly waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
