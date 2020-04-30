---
title: 'Zelf studie: integratie met perceptie Verenigde Staten (niet-UltiPro) Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en perceptie Verenigde Staten (niet-UltiPro).
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67094837"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Zelf studie: integratie Azure Active Directory met perceptie Verenigde Staten (niet-UltiPro)

In deze zelf studie leert u hoe u perceptie Verenigde Staten (niet-UltiPro) integreert met Azure Active Directory (Azure AD).
Het integreren van perceptie Verenigde Staten (non-UltiPro) met Azure AD biedt u de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot de perceptie Verenigde Staten (niet-UltiPro).
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij een perceptie Verenigde Staten (geen UltiPro) (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met perceptie Verenigde Staten (niet-UltiPro) wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op de perceptie Verenigde Staten (non-UltiPro) waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Perceptie Verenigde Staten (non-UltiPro) ondersteunt door **IDP** GEÏNITIEERDe SSO

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Een perceptie Verenigde Staten (niet-UltiPro) toevoegen uit de galerie

Als u de integratie van perceptie Verenigde Staten (non-UltiPro) in azure AD wilt configureren, moet u een perceptie Verenigde Staten (niet-UltiPro) uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om perceptie Verenigde Staten (niet-UltiPro) toe te voegen in de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **perceptie Verenigde Staten (niet-UltiPro)**, selecteer **perceptie Verenigde Staten (niet-UltiPro)** in resultaat paneel en klik vervolgens op knop **toevoegen** om de toepassing toe te voegen.

     ![Perceptie Verenigde Staten (niet-UltiPro) in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD met perceptie Verenigde Staten (niet-UltiPro) configureren en testen op basis van een test gebruiker met de naam **Julia Simon**.
Als u eenmalige aanmelding wilt gebruiken, moet u een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in perceptie Verenigde Staten (niet-UltiPro) tot stand brengen.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met perceptie Verenigde Staten (niet-UltiPro), moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Stel een perceptie Verenigde Staten (niet-UltiPro) eenmalige aanmelding](#configure-perception-united-states-non-ultipro-single-sign-on)** in om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een perceptie Verenigde Staten test gebruiker (non-UltiPro)](#create-perception-united-states-non-ultipro-test-user)** , zodat deze een soort is van Julia Simon in perceptie Verenigde Staten (niet-UltiPro) die is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met perceptie Verenigde Staten (niet-UltiPro):

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **perceptie Verenigde Staten (niet-UltiPro)** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    ![Perceptie Verenigde Staten (non-UltiPro) domein-en URL-informatie over eenmalige aanmelding](common/idp-intiated.png)

    a. Typ een URL in het tekstvak **id** :`https://perception.kanjoya.com/sp`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://perception.kanjoya.com/sso?idp=<entity_id>`

    c. Voor de toepassing **perceptie Verenigde Staten (non-UltiPro)** is de id-waarde van **Azure AD** vereist als <entity_id>, die u in de sectie **perceptie instellen Verenigde Staten (niet-UltiPro)** kunt versleutelen. Gebruik de volgende koppeling om de URI-gecodeerde waarde te verkrijgen **http://www.url-encode-decode.com/**:.

    d. Nadat de waarde met URI-code ring is opgehaald, moet deze worden gecombineerd met de **antwoord-URL** , zoals hieronder vermeld:

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    e. Plak de bovenstaande waarde in het tekstvak **antwoord-URL** .

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer op de sectie **perceptie instellen Verenigde Staten (niet-UltiPro)** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL   

### <a name="configure-perception-united-states-non-ultipro-single-sign-on"></a>Een perceptie Verenigde Staten (non-UltiPro) configureren voor eenmalige aanmelding

1. Meld u in een ander browser venster aan bij de bedrijfs site van uw perceptie Verenigde Staten (niet UltiPro) als beheerder.

2. Klik in de hoofdwerk balk op **account instellingen**.

    ![Perceptie Verenigde Staten gebruiker (non-UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

3. Voer de volgende stappen uit op de pagina **account instellingen** :

    ![Perceptie Verenigde Staten gebruiker (non-UltiPro)](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. In het tekstvak **Bedrijfs naam** typt u de naam van het **bedrijf**.
    
    b. Typ in het tekstvak **account naam** de naam van het **account**.

    c. Typ in het tekstvak **standaard e-mail adres de waarde** geldig **e-mail**adres.

    d. Selecteer **SSO-ID-provider** als **SAML 2,0**.

4. Voer de volgende stappen uit op de pagina **SSO-configuratie** :

    ![Perceptie Verenigde Staten (niet-UltiPro) SSOConfig](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Selecteer **SAML NameID-type** als **e-mail adres**.

    b. Typ in het tekstvak **naam van SSO-configuratie** de naam van uw **configuratie**.
    
    c. Plak in het tekstvak naam van de **identiteits provider** de waarde van de **Azure ad-id**die u van Azure Portal hebt gekopieerd. 

    d. Voer in het **tekstvak SAML-domein**het domein @contoso.comin.

    e. Klik **nogmaals op uploaden** om het XML-bestand met **meta gegevens** te uploaden.

    f. Klik op **Update**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. Typ brittasimon@yourcompanydomain.extensionin het veld **gebruikers naam** . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de perceptie Verenigde Staten (niet-UltiPro).

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens de optie **perceptie Verenigde Staten (niet-UltiPro)**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **perceptie Verenigde Staten (niet-UltiPro)**.

    ![De perceptie Verenigde Staten-koppeling (non-UltiPro) in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-perception-united-states-non-ultipro-test-user"></a>Een test gebruiker met een perceptie Verenigde Staten (non-UltiPro) maken

In deze sectie maakt u een gebruiker met de naam Julia Simon in perceptie Verenigde Staten (non-UltiPro). Werk samen met het [ondersteunings team van perceptie Verenigde Staten (niet-UltiPro)](https://www.ultimatesoftware.com/Contact/ContactUs) om de gebruikers toe te voegen in het perceptie Verenigde Staten-platform (niet-UltiPro).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de perceptie Verenigde Staten (niet-UltiPro) in het toegangs venster klikt, moet u automatisch worden aangemeld bij de perceptie Verenigde Staten (niet-UltiPro) waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

