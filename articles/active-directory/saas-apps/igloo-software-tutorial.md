---
title: 'Zelfstudie: Azure Active Directory-integratie met Iglo-software | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Igloo Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/06/2019
ms.author: jeedes
ms.openlocfilehash: df1d70f895e2e0a81344cf2a4e8e2d9963c951fa
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67100584"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Zelfstudie: Azure Active Directory-integratie met Igloo-software

In deze zelfstudie leert u hoe u Igloo-software integreert met Azure Active Directory (Azure AD).
De integratie van Igloo Software met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Igloo Software.
* U uw gebruikers automatisch laten aanmelden bij Igloo Software (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie met Igloo Software wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Igloo Software single sign-on enabled abonnement

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Igloo Software ondersteunt **SP** geïnitieerde SSO
* Igloo Software ondersteunt **Just In Time** gebruikersinrichting

## <a name="adding-igloo-software-from-the-gallery"></a>Het toevoegen van Igloo Software uit de galerie

Als u de integratie van Igloo Software in Azure AD wilt configureren, moet u Igloo Software uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Igloo Software uit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Iglo Software**in het zoekvak , selecteer **Iglo Software** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![Igloo Software in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Igloo Software op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppeling slinken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Igloo Software.

Als u Azure AD-single sign-on wilt configureren en testen met Igloo Software, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Iglo Software Single Sign-On configureren](#configure-igloo-software-single-sign-on)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak Igloo Software test gebruiker](#create-igloo-software-test-user)** - om een tegenhanger van Britta Simon in Igloo Software die is gekoppeld aan de Azure AD vertegenwoordiging van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om azure AD single sign-on te configureren met Igloo Software:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de integratiepagina **van iglosoftwaretoepassingen** de optie **Enkele aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Igloo Software Domain en URL's single sign-on informatie](common/sp-identifier-reply.png)

    a. Typ in het tekstvak **AANmeldings-URL** een URL met het volgende patroon:`https://<company name>.igloocommmunities.com`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<company name>.igloocommmunities.com/saml.digest`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Neem contact op met [Igloo Software Client support team](https://www.igloosoftware.com/services/support) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **Iglo Software** instellen de juiste URL(s) volgens uw eis.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-igloo-software-single-sign-on"></a>Iglo Software Single Sign-On configureren

1. Log in een ander browservenster in op de bedrijfssite van Uw Igloo Software als beheerder.

2. Ga naar het **Configuratiescherm**.

     ![Configuratiescherm](./media/igloo-software-tutorial/ic799949.png "Configuratiescherm")

3. Klik op het tabblad **Lidmaatschap** op **Instellingen aanmelden**.

    ![Aanmeldingsinstellingen](./media/igloo-software-tutorial/ic783968.png "Aanmeldingsinstellingen")

4. Klik in de sectie SAML-configuratie op **SAML-verificatie configureren**.

    ![SAML-configuratie](./media/igloo-software-tutorial/ic783969.png "SAML-configuratie")

5. Voer in de sectie **Algemene configuratie** de volgende stappen uit:

    ![Algemene configuratie](./media/igloo-software-tutorial/ic783970.png "Algemene configuratie")

    a. Typ in het tekstvak **Verbindingsnaam** een aangepaste naam voor uw configuratie.

    b. Plak in het tekstvak **URL-login-aanmelding** van IdP de waarde van **de aanmeldings-URL** die u hebt gekopieerd van azure-portal.

    c. Plak in het **tekstvak URL-logboeken idp** de waarde van **de URL van afmelden** die u hebt gekopieerd uit azure-portal.

    d. Selecteer **Afmeldenantwoord en HTTP-type aanvragen** als **POST**.

    e. Open uw **basis-64** gecodeerd certificaat in notitieblok gedownload van Azure portal, kopieer de inhoud ervan in uw klembord en plak het vervolgens in het tekstvak **Openbaar certificaat.**

6. Voer in de **configuratie van respons en verificatie**de volgende stappen uit:

    ![Reactie- en verificatieconfiguratie](./media/igloo-software-tutorial/IC783971.png "Reactie- en verificatieconfiguratie")
  
    a. Selecteer **Microsoft ADFS**als **identiteitsprovider**.

    b. Selecteer **E-mailadres**als **id-type**. 

    c. Typ **e-mailadres**in het tekstvak **E-mailkenmerk** .

    d. Typ in het tekstvak **Voornaamattribuut** **de naam**.

    e. **Typ**achternaam in het tekstvak **Achternaamattribuut** .

7. Voer de volgende stappen uit om de configuratie uit te voeren:

    ![Gebruikers maken bij Aanmelden](./media/igloo-software-tutorial/IC783972.png "Gebruikers maken bij Aanmelden") 

    a. Selecteer Als **gebruiker aanmaken bij Aanmelden**de optie Een nieuwe gebruiker op uw site maken wanneer deze zich **aanmeldt.**

    b. Selecteer als **aanmeldingsinstellingen**de **knop SAML gebruiken op het scherm Aanmelden.**

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
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon@yourcompanydomain.extension**.  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Igloo Software.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Vervolgens IgloSoftware**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Igloo Software**in de lijst met toepassingen .

    ![De Iglo Software link in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-igloo-software-test-user"></a>Iglo Software test gebruiker maken

Er is geen actie-item voor u om gebruikersinrichting te configureren voor Igloo Software.  

Wanneer een toegewezen gebruiker probeert in te loggen op Igloo Software via het toegangspaneel, controleert Igloo Software of de gebruiker bestaat.  Als er nog geen gebruikersaccount beschikbaar is, wordt het automatisch gemaakt door Igloo Software.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de iglosoftwaretegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Igloo Software waarvoor u SSO instelt. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)