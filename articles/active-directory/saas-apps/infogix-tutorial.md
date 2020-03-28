---
title: 'Zelfstudie: Azure Active Directory-integratie met Infogix Data3Sixty Govern | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Infogix Data3Sixty Govern.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: aa3109b8-bdbe-45ae-933a-2eb4dc03855c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: a0b8dbb3992808bdcb94291d876192431c72f6c2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67100346"
---
# <a name="tutorial-azure-active-directory-integration-with-infogix-data3sixty-govern"></a>Zelfstudie: Azure Active Directory-integratie met Infogix Data3Sixty Govern

In deze zelfstudie leert u hoe U Infogix Data3Sixty Govern integreren met Azure Active Directory (Azure AD).
De integratie van Infogix Data3Sixty Govern met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Infogix Data3Sixty Govern.
* U uw gebruikers automatisch laten inloggen op Infogix Data3Sixty Govern (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie wilt configureren met Infogix Data3Sixty Govern, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Infogix Data3Sixty Govern single sign-on enabled subscription Infogix Data3Sixty Govern single sign-on enabled subscription Infogix Data3Sixty Govern single sign-on enabled subscription Infog

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Infogix Data3Sixty Govern ondersteunt **SP en IDP** gestart met SSO
* Infogix Data3Sixty Govern ondersteunt **Just In Time** gebruikersinrichting

## <a name="adding-infogix-data3sixty-govern-from-the-gallery"></a>Infogix Data3Sixty Govern toevoegen vanuit de galerij

Als u de integratie van Infogix Data3Sixty Govern in Azure AD wilt configureren, moet u Infogix Data3Sixty Govern vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Infogix Data3Sixty Govern vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ In het zoekvak **Infogix Data3Sixty Govern**, selecteer **Infogix Data3Sixty Govern** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![Infogix Data3Sixty Govern in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Infogix Data3Sixty Govern op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Infogix Data3Sixty Govern.

Als u Azure AD single sign-on wilt configureren en testen met Infogix Data3Sixty Govern, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Infogix Data3Sixty Govern Single Sign-On](#configure-infogix-data3sixty-govern-single-sign-on)** - om de instellingen voor eenmalig aanmelden aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak Infogix Data3Sixty Govern-testgebruiker](#create-infogix-data3sixty-govern-test-user)** - om een tegenhanger van Britta Simon te hebben in Infogix Data3Sixty Govern die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om azure AD single sign-on te configureren met Infogix Data3Sixty Govern:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Infogix Data3Sixty** Govern-toepassingsintegratie de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **BasisSAML-configuratie** de volgende stappen uit als u de toepassing in de **idp-modus** wilt configureren:

    ![Infogix Data3Sixty Bestuur domein en URL's single sign-on informatie](common/idp-intiated.png)

    a. Typ een URL in het tekstvak **Id:**`https://data3sixty.com/ui`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<subdomain>.data3sixty.com/sso/acs`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Infogix Data3Sixty Bestuur domein en URL's single sign-on informatie](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<subdomain>.data3sixty.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de echte antwoord-URL en aanmeldings-URL. Neem contact op met [infogix Data3Sixty Govern Client support team](mailto:data3sixtysupport@infogix.com) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Infogix Data3Sixty Govern applicatie verwacht de SAML beweringen in een specifiek formaat. Configureer de volgende claims voor deze toepassing. U kunt de waarden van deze kenmerken vanuit de sectie **Gebruikerskenmerken** op de integratiepagina van de toepassing-beheren. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op de knop **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen.

    ![installatiekopie](common/edit-attribute.png)

7. Bewerk in het gedeelte **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** de claims met het **pictogram Bewerken** of voeg de claims toe door met **Nieuwe claim toevoegen** het kenmerk van het SAML-token te configureren, zoals wordt weergegeven in de bovenstaande afbeelding. Hierna voert u de volgende stappen uit:

    | Name | Bronkenmerk|
    | -----------| -------------- |
    | firstname  | user.givenname |
    | lastname   | user.surname |
    | gebruikersnaam   | user.mail    |

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![installatiekopie](common/new-save-attribute.png)

    ![installatiekopie](common/new-attribute-details.png)

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    f. Klik op **OK**.

    g. Klik op **Opslaan**.

8. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Raw)** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificateraw.png)

9. Kopieer in de sectie **Infogix Data3Sixty Govern** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-infogix-data3sixty-govern-single-sign-on"></a>Infogix Data3Sixty Regel enkele aanmelding configureren

Als u eenmalige aanmelding wilt configureren aan de **infogix Data3Sixty** Govern-zijde, moet u het gedownloade **certificaat (Raw)** en de juiste gekopieerde URL's van Azure-portal naar [het ondersteuningsteam van Infogix Data3Sixty Govern](mailto:data3sixtysupport@infogix.com)verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

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

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Infogix Data3Sixty Govern.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Vervolgens Infogix Data3Sixty Govern**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Infogix Data3Sixty Govern in**de lijst met toepassingen .

    ![De koppeling Infogix Data3Sixty Govern in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-infogix-data3sixty-govern-test-user"></a>Infogix Data3Sixty Govern-testgebruiker maken

In deze sectie wordt een gebruiker genaamd Britta Simon gemaakt in Infogix Data3Sixty Govern. Infogix Data3Sixty Govern ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Infogix Data3Sixty Govern, wordt er een nieuwe gemaakt na verificatie.

> [!Note]
> Neem contact op met [infogix Data3Sixty Govern support team](mailto:data3sixtysupport@infogix.com)als u handmatig een gebruiker wilt maken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Infogix Data3Sixty Govern in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Infogix Data3Sixty Govern waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

