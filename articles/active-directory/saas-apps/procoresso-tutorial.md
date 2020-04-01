---
title: 'Zelfstudie: Azure Active Directory-integratie met Procore SSO | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Procore SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca6863a6b02e867afd732ce1662136051b8afec8
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093668"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Zelfstudie: Azure Active Directory-integratie met Procore SSO

In deze zelfstudie leert u hoe u Procore SSO integreert met Azure Active Directory (Azure AD).
De integratie van Procore SSO met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Procore SSO.
* U uw gebruikers automatisch laten aanmelden bij Procore SSO (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Procore SSO wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Procore SSO-abonnement met één aanmelding

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Procore SSO ondersteunt **IDP** geïnitieerde SSO

## <a name="adding-procore-sso-from-the-gallery"></a>Procore SSO toevoegen vanuit de galerie

Als u de integratie van Procore SSO in Azure AD wilt configureren, moet u Procore SSO vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Procore SSO vanuit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Selecteer **Procore**SSO in het zoekvak , selecteer **Procore SSO** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

    ![Procore SSO in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Procore SSO op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppeling slinken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Procore SSO.

Als u Azure AD-singlesign-aan met Procore SSO wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Procore SSO Single Sign-On](#configure-procore-sso-single-sign-on)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak Procore SSO-testgebruiker](#create-procore-sso-test-user)** - om een tegenhanger van Britta Simon in Procore SSO te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met Procore SSO te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Procore SSO-toepassingsintegratie** de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **SAML-basisconfiguratie** hoeft de gebruiker geen enkele stap uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

    ![Informatie over Procore SSO-domein en URL's met eenmalige aanmelding](common/preintegrated.png)

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **Procore SSO instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-procore-sso-single-sign-on"></a>Procore SSO-aanmelding configureren

1. Als u eenmalige aanmelding wilt configureren aan **de Procore SSO-zijde,** meldt u zich aan bij uw procore-bedrijfssite als beheerder.

2. Klik in de vervolgkeuzelijst op **Beheerder** om de pagina SSO-instellingen te openen.

    ![Eenmalige aanmelding configureren](./media/procoresso-tutorial/procore_tool_admin.png)

3. Plak de waarden in de onderstaande vakken.

    ![Eenmalige aanmelding configureren](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. Plak in het **tekstvak URL van een enkele aanmelding op emittent** de waarde van **azure AD-id** die u hebt gekopieerd uit de Azure-portal.

    b. Plak in het vak **SAML-aanmeldings-URL** de waarde van **de aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    c. Open nu de XML **met federatiemetagegevens** die hierboven zijn gedownload van de Azure-portal en kopieer het certificaat in de tag met de naam **X509Certificate**. Plak de gekopieerde waarde in het vak **Enkelteken op x509-certificaat.**

4. Klik op **Save Changes** (Wijzigingen opslaan).

5. Na deze instellingen moet u de **domeinnaam** (bijvoorbeeld **contoso.com)** waarmee u zich aanmeldt bij Procore naar het [Procore Support-team](https://support.procore.com/) sturen en ze activeren federatieve SSO voor dat domein.

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

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Procore SSO.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Vervolgens Procore SSO**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Procore SSO**in de lijst met toepassingen .

    ![De Procore SSO-koppeling in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-procore-sso-test-user"></a>Procore SSO-testgebruiker maken

Volg de onderstaande stappen om een Procore-testgebruiker aan De Kant van Procore SSO te maken.

1. Meld u aan bij uw procore-bedrijfssite als beheerder.    

2. Klik in de vervolgkeuzelijst op **Directory** om de pagina met de bedrijfsmap te openen.

    ![Eenmalige aanmelding configureren](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Klik op De optie **Een persoon toevoegen** om het formulier te openen en de volgende opties uit te voeren -

    ![Eenmalige aanmelding configureren](./media/procoresso-tutorial/Procore_user_add.png)

    a. Typ in het tekstvak **Voornaam** de voornaam van de gebruiker zoals **Britta**.

    b. Typ in het tekstvak **Achternaam** de achternaam van de gebruiker zoals **Simon**.

    c. Typ in het tekstvak **E-mailadres** het BrittaSimon@contoso.come-mailadres van de gebruiker als .

    d. Selecteer **Machtigingssjabloon** als **machtigingssjabloon later toepassen**.

    e. Klik **op Maken**.

4. Controleer en werk de details voor de nieuw toegevoegde contactpersoon in en werk deze bij.

    ![Eenmalige aanmelding configureren](./media/procoresso-tutorial/Procore_user_check.png)

5. Klik op **Uitnodiging opslaan en verzenden** (als een uitnodiging via e-mail vereist is) of **Opslaan** (direct opslaan) om de gebruikersregistratie te voltooien.
    
    ![Eenmalige aanmelding configureren](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de Procore SSO-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Procore SSO waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

