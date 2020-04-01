---
title: 'Zelfstudie: Azure Active Directory-integratie met Perception United States (Non-UltiPro) | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Perception United States (Non-UltiPro).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b4a8f026-cb5f-41eb-9680-68eddc33565e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: e9ba42f780c93486409077383750d0635637e99b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094837"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Zelfstudie: Azure Active Directory-integratie met Perception United States (Non-UltiPro)

In deze zelfstudie leert u hoe u Perception United States (Non-UltiPro) integreert met Azure Active Directory (Azure AD).
Het integreren van Perception United States (Non-UltiPro) met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Perception United States (Non-UltiPro).
* U uw gebruikers automatisch laten aanmelden bij Perception United States (Non-UltiPro) (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie wilt configureren met Perception United States (Non-UltiPro), hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Perceptie Verenigde Staten (Non-UltiPro) single sign-on enabled abonnement

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Perception United States (Non-UltiPro) ondersteunt **IDP** geïnitieerde SSO

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Het toevoegen van Perception United States (Non-UltiPro) vanuit de galerij

Als u de integratie van Perception United States (Non-UltiPro) in Azure AD wilt configureren, moet u Perception United States (Non-UltiPro) vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Perception United States (Non-UltiPro) toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ perceptie Verenigde **Staten (Non-UltiPro)** in het zoekvak en selecteer **Perception United States (Non-UltiPro)** in het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![Perceptie Verenigde Staten (Non-UltiPro) in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Perception United States (Non-UltiPro) op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppeling worden gemaakt tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Perception United States (Non-UltiPro).

Als u Azure AD-single sign-on wilt configureren en testen met Perception United States (Non-UltiPro), moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer De waarneming Verenigde Staten (Niet-UltiPro) Single Sign-On](#configure-perception-united-states-non-ultipro-single-sign-on)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak Perception United States (Non-UltiPro) testgebruiker](#create-perception-united-states-non-ultipro-test-user)** - om een tegenhanger van Britta Simon in Perception United States (Non-UltiPro) te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om azure AD single sign-on te configureren met Perception United States (Non-UltiPro):

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina Integratie van de **Waarneming Verenigde Staten (Niet-UltiPro)** toepassingsintegratie de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    ![Perceptie United States (Non-UltiPro) Domein en URL's single sign-on informatie](common/idp-intiated.png)

    a. Typ een URL in het tekstvak **Id:**`https://perception.kanjoya.com/sp`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://perception.kanjoya.com/sso?idp=<entity_id>`

    c. De **toepassing Perception United States (Non-UltiPro)** vereist de Azure AD **Identifier-waarde,** omdat <entity_id>, die u krijgt van de sectie **Perceptie Verenigde Staten (Niet-UltiPro)** instellen, om te worden gecodeerd. Gebruik de volgende koppeling om de uri-gecodeerde waarde te krijgen: **http://www.url-encode-decode.com/**.

    d. Na het krijgen van de uri gecodeerde waarde combineren met de **Reply URL** zoals hieronder vermeld-

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    e. Plak de bovenstaande waarde in het tekstvak **Url van antwoord.**

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **Perceptie Verenigde Staten instellen (Niet-UltiPro)** de juiste URL(s) volgens uw eis.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL   

### <a name="configure-perception-united-states-non-ultipro-single-sign-on"></a>Perceptie Verenigde Staten (Niet-UltiPro) Single Sign-On configureren

1. Meld u in een ander browservenster aan op de bedrijfssite Perception United States (Non-UltiPro) als beheerder.

2. Klik op de hoofdwerkbalk op **Accountinstellingen**.

    ![Perceptie Verenigde Staten (Non-UltiPro) gebruiker](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

3. Voer op de pagina **Accountinstellingen** de volgende stappen uit:

    ![Perceptie Verenigde Staten (Non-UltiPro) gebruiker](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. Typ in het tekstvak **Bedrijfsnaam** de naam van het **bedrijf**.
    
    b. Typ in het tekstvak **Accountnaam** de naam van het **account**.

    c. Typ **in het tekstvak Standaard antwoord-naar-e-mail** de geldige **e-mail**.

    d. Selecteer **SSO Identity Provider** als **SAML 2.0**.

4. Voer op de pagina **SSO-configuratie** de volgende stappen uit:

    ![Perceptie Verenigde Staten (Non-UltiPro) SSOConfig](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Selecteer **SAML-naam-id-type** als **e-mail**.

    b. Typ in het tekstvak **SSO-configuratienaam** de naam van uw **configuratie**.
    
    c. Plak in het tekstvak Naam van **de identiteitsprovider** de waarde van **azure AD-id**, die u hebt gekopieerd van Azure-portal. 

    d. Voer in **het tekstvak SAML-domein**het domein in zoals @contoso.com.

    e. Klik op **Opnieuw uploaden** om het **XML-bestand met metagegevens te uploaden.**

    f. Klik op **Update**.

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

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Perception United States (Non-UltiPro).

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer Vervolgens Perceptie Verenigde **Staten (Non-UltiPro)**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer perceptie Verenigde **Staten (Non-UltiPro)** in de lijst met toepassingen .

    ![De Perception United States (Non-UltiPro) link in de lijst toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-perception-united-states-non-ultipro-test-user"></a>Perception United States (Non-UltiPro) testgebruiker maken

In deze sectie maakt u een gebruiker genaamd Britta Simon in Perception United States (Non-UltiPro). Werk samen met [het ondersteuningsteam van Perception United States (Non-UltiPro)](https://www.ultimatesoftware.com/Contact/ContactUs) om de gebruikers toe te voegen in het Perception United States (Non-UltiPro) platform.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Perception United States (Non-UltiPro) klikt in het access panel, moet u automatisch worden aangemeld bij de Perception United States (Non-UltiPro) waarvoor u SSO instelt. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

