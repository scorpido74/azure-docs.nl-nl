---
title: 'Zelf studie: integratie met saus Labs Azure Active Directory-Mobile en Web testen | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en saus Labs-mobiele en webtests.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67091562"
---
# <a name="tutorial-azure-active-directory-integration-with-sauce-labs---mobile-and-web-testing"></a>Zelf studie: integratie met saus Labs Azure Active Directory-Mobile en Web testen

In deze zelf studie leert u hoe u saus Labs kunt integreren: mobiele en webtests met Azure Active Directory (Azure AD).
Het integreren van saus Labs: mobiele en webtests met Azure AD bieden de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot saus Labs-mobiele en webtests.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij saus Labs: mobiele en webtest (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met saus Labs: mobiele en webtesten hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* Saus Labs-abonnement voor eenmalige aanmelding voor mobiele en Internet tests

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Saus Labs: mobiele en Web-tests bieden ondersteuning voor **IDP** GEÏNITIEERDe SSO
* Saus Labs: mobiele en Internet tests bieden ondersteuning **voor Just-in-time** -gebruikers inrichting

## <a name="adding-sauce-labs---mobile-and-web-testing-from-the-gallery"></a>Saus Labs toevoegen-mobiele en webtesten vanuit de galerie

Als u de integratie van saus Labs-mobiele en webtests wilt configureren in azure AD, moet u saus Labs-mobiele en webtests toevoegen vanuit de galerie naar uw lijst met beheerde SaaS-apps.

**Als u saus Labs-mobiele en webtests wilt toevoegen vanuit de galerie, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **saus Labs-mobiele en Webtesten**, selecteer **saus Labs-mobiel en webtesten** in het paneel resultaten en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

    ![Saus Labs: mobiele en webtests in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met saus Labs: mobiele en webtests op basis van een test gebruiker met de naam **Julia Simon**.
Voor eenmalige aanmelding moet een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in saus Labs worden ingesteld.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met saus Labs-mobiele en webtesten, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Saus Labs configureren: mobiele en webtest eenmalige aanmelding](#configure-sauce-labs---mobile-and-web-testing-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak saus Labs-test gebruiker voor mobiele en webtest](#create-sauce-labs---mobile-and-web-testing-test-user)** , om een soort Julia Simon te hebben in saus Labs: mobiele en webtests die zijn gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met saus Labs: mobiele en webtests.

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **saus Labs-Mobile en web test** voor toepassings integratie de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **SAML-basisconfiguratie** hoeft de gebruiker geen enkele stap uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

    ![Saus Labs: mobiele en webtest domein-en URL-gegevens voor eenmalige aanmelding](common/preintegrated.png)

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer de gewenste URL ('s) op basis van uw vereisten in de sectie **installatie van saus Labs-mobiele en Web testen** .

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-sauce-labs---mobile-and-web-testing-single-sign-on"></a>Saus Labs configureren: eenmalige aanmelding voor mobiele en Internet tests

1. Meld u in een ander browser venster aan bij uw saus Labs-mobiele en web-test site als beheerder.

2. Klik op het **pictogram gebruiker** en selecteer tabblad **team beheer** .

    ![Eenmalige aanmelding configureren](./media/saucelabs-mobileandwebtesting-tutorial/configure1.png)

3. Voer uw **domein naam** in het tekstvak in.

    ![Eenmalige aanmelding configureren](./media/saucelabs-mobileandwebtesting-tutorial/configure2.png)

4. Klik op het tabblad **configureren** .

    ![Eenmalige aanmelding configureren](./media/saucelabs-mobileandwebtesting-tutorial/configure3.png)

5. Voer de volgende stappen uit in de sectie **eenmalige aanmelding configureren** .

    ![Eenmalige aanmelding configureren](./media/saucelabs-mobileandwebtesting-tutorial/configure4.png)

    a. Klik op **Bladeren** en upload het gedownloade meta gegevensbestand vanuit Azure AD.

    b. Selecteer het selectie vakje **just-in-time inrichting toestaan** .

    c. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u `brittasimon@yourcompanydomain.extension`.  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om gebruik te maken van eenmalige aanmelding van Azure door toegang te verlenen tot saus Labs-mobiele en webtests.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **saus Labs-mobiele en webtests**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **saus Labs-Mobile en Web testen**.

    ![The saus Labs: Mobile and web test link in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-sauce-labs---mobile-and-web-testing-test-user"></a>Saus Labs maken-test gebruiker voor mobiele en webtest

In deze sectie wordt een gebruiker met de naam Julia Simon gemaakt in saus Labs: Mobile en Web testen. Saus Labs: mobiele en Internet tests bieden ondersteuning voor Just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker niet al bestaat in saus Labs: mobiele en webtest, wordt er na verificatie een nieuwe gemaakt.

> [!Note]
> Als u hand matig een gebruiker moet maken, neemt u contact op met [saus Labs-Mobile en web test ondersteunings team](mailto:support@saucelabs.com).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de achtergrond van saus Labs-Mobile en Web testen in het toegangs venster klikt, moet u automatisch worden aangemeld bij de saus Labs: mobiele en webtest waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

