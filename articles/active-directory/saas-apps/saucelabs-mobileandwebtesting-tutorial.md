---
title: 'Zelfstudie: Azure Active Directory-integratie met Sauce Labs - Mobiel en webtesten | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Sauce Labs - Mobiel en Webtesten.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3142d947-70e5-4345-8a30-b92d8715fac9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: 8933cb90672e49305cd0fb7dc5e4f8f04f94093e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67091562"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Zelfstudie: Azure Active Directory-integratie met Sauce Labs - Mobiel en webtesten

In deze zelfstudie leert u hoe u Sauce Labs - Mobile and Web Testing integreert met Azure Active Directory (Azure AD).
Het integreren van Sauce Labs - Mobiel en webtesten met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Sauce Labs - Mobile en Web Testing.
* U uw gebruikers automatisch laten aanmelden bij Sauce Labs - Mobile en Web Testing (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie wilt configureren met Sauce Labs - Mobiel en Webtesten, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Sauce Labs - Mobile en Web Testing single sign-on enabled subscription Sauce Labs - Mobile and Web Testing single sign-on enabled subscription Sauce Labs - Mobile and Web Testing single sign-on enabled subscription Sauce Labs

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Sauce Labs - Mobile en Web Testing ondersteunt **IDP** geïnitieerde SSO
* Sauce Labs - Mobile en Web Testing ondersteunt **Just In Time** gebruikersinrichting

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Het toevoegen van Sauce Labs - Mobile en Web Testing vanuit de galerie

Als u de integratie van Sauce Labs - Mobile en Web Testing in Azure AD wilt configureren, moet u Sauce Labs - Mobile en Web Testing vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Sauce Labs - Mobile en Web Testing vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Sauce Labs - Mobile and Web Testing**, selecteer Sauce Labs - Mobile en Web **Testing** van het resultatenpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

    ![Sauce Labs - Mobile en Web Testing in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Sauce Labs - Mobile en Web Testing op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding op het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Sauce Labs - Mobiel en Webtesten.

Als u Azure AD single sign-on wilt configureren en testen met Sauce Labs - Mobile en Web Testing, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Sauce Labs - Mobile and Web Testing Single Sign-On](#configure-sauce-labs---mobile-and-web-testing-single-sign-on)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak Sauce Labs - Mobile en Web Testing test gebruiker](#create-sauce-labs---mobile-and-web-testing-test-user)** - om een tegenhanger van Britta Simon in Sauce Labs - Mobile en Web Testing die is gekoppeld aan de Azure AD vertegenwoordiging van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om azure AD single sign-on te configureren met Sauce Labs - Mobile en Web Testing:

1. Selecteer in de [azure-portal](https://portal.azure.com/)op de pagina **Sauce Labs - Mobile and Web Testing-toepassingsintegratie** de optie Enkele **aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **SAML-basisconfiguratie** hoeft de gebruiker geen enkele stap uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

    ![Sauce Labs - Mobile en Web Testing Domain en URL's single sign-on informatie](common/preintegrated.png)

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **Sauce Labs - Mobile and Web Testing** de juiste URL(s) naar uw behoefte.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-sauce-labs---mobile-and-web-testing-single-sign-on"></a>Sauce Labs configureren - Mobile en Web Testing Single Sign-On

1. Meld je in een ander browservenster aan bij je Sauce Labs - Mobile en Web Testing bedrijfssite als beheerder.

2. Klik op het **pictogram Gebruiker** en selecteer het tabblad **Teambeheer.**

    ![Eenmalige aanmelding configureren](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

3. Voer uw **domeinnaam in** het tekstvak in.

    ![Eenmalige aanmelding configureren](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

4. Klik **op tabblad Configureren.**

    ![Eenmalige aanmelding configureren](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

5. Voer in de sectie **Eén aanmelding configureren** de volgende stappen uit.

    ![Eenmalige aanmelding configureren](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. Klik **op Bladeren** en upload het gedownloade metagegevensbestand vanuit het Azure AD.

    b. Schakel het selectievakje **JUST-IN-TIME PROVISIONING TOESTAAN** in.

    c. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u `brittasimon@yourcompanydomain.extension`.  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Sauce Labs - Mobile en Web Testing.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, Selecteer **Alle toepassingen**en selecteer vervolgens Sauce Labs - Mobiel **en Webtesten**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen **Sauce Labs - Mobile en Web Testing.**

    ![De Sauce Labs - Mobile en Web Testing link in de lijst toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-sauce-labs---mobile-and-web-testing-test-user"></a>Maak Sauce Labs - Mobile en Web Testing test gebruiker

In deze sectie, een gebruiker genaamd Britta Simon is gemaakt in Sauce Labs - Mobile en Web Testing. Sauce Labs - Mobile en Web Testing ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Sauce Labs - Mobile en Web Testing, wordt er een nieuwe gemaakt na verificatie.

> [!Note]
> Als u handmatig een gebruiker wilt maken, neemt u contact op met [het ondersteuningsteam van Sauce Labs - Mobile en Web Testing.](mailto:support@saucelabs.com)

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Sauce Labs - Mobile en Web Testing klikt in het toegangspaneel, moet u automatisch worden aangemeld bij de Sauce Labs - Mobile en Web Testing waarvoor u SSO instelt. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

