---
title: 'Zelfstudie: Azure Active Directory-integratie met Citrix ShareFile | Microsoft Documenten'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Citrix ShareFile configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: e14fc310-bac4-4f09-99ef-87e5c77288b6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71d2fe97a4af07f48038ffd88de648247b56bb80
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160031"
---
# <a name="tutorial-azure-active-directory-integration-with-citrix-sharefile"></a>Zelfstudie: Azure Active Directory-integratie met Citrix ShareFile

In deze zelfstudie leert u hoe u Citrix ShareFile integreert met Azure Active Directory (Azure AD).
De integratie van Citrix ShareFile met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot Citrix ShareFile.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Citrix ShareFile (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie te configureren met Citrix ShareFile:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement op Citrix ShareFile met eenmalige aanmelding (SSO) ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Citrix ShareFile ondersteunt door **SP** geïnitieerde SSO

## <a name="adding-citrix-sharefile-from-the-gallery"></a>Citrix ShareFile toevoegen vanuit de galerie

Om de integratie van Citrix ShareFile in Azure AD te configureren, moet u Citrix ShareFile vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Ga als volgt te werk om Citrix ShareFile vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Citrix ShareFile** in het zoekvak, selecteer **Citrix ShareFile** in de lijst met resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Citrix ShareFile in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte gaat u eenmalige aanmelding van Azure AD met Citrix ShareFile configureren en testen op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Citrix ShareFile tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met Citrix ShareFile, moet u de volgende procedures uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Citrix ShareFile configureren](#configure-citrix-sharefile-single-sign-on)**: de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor Citrix ShareFile maken](#create-citrix-sharefile-test-user)**: een tegenhanger voor Britta Simon maken in Citrix ShareFile die wordt gekoppeld aan de Azure AD-voorstelling van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren met Citrix ShareFile:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina met de integratie van de toepassing **Citrix ShareFile** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Gegevens voor domein en URL's voor eenmalige aanmelding bij Citrix ShareFile](common/sp-identifier-reply.png)

    a. Typ in het tekstvak **AANmeldings-URL** een URL met het volgende patroon:`https://<tenant-name>.sharefile.com/saml/login`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon:

    | |
    |---|
    | `https://<tenant-name>.sharefile.com`|
    | `https://<tenant-name>.sharefile.com/saml/info`|
    | `https://<tenant-name>.sharefile1.com/saml/info`|
    | `https://<tenant-name>.sharefile1.eu/saml/info`|
    | `https://<tenant-name>.sharefile.eu/saml/info`|
    | |
    
    c. In het tekstvak **Antwoord-URL** typt u een URL met behulp van het volgende patroon: 
    
    | |
    |---|
    | `https://<tenant-name>.sharefile.com/saml/acs`|
    | `https://<tenant-name>.sharefile.eu/saml/<URL path>`|
    | `https://<tenant-name>.sharefile.com/saml/<URL path>`|

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteuningsteam van Citrix ShareFile](https://www.citrix.co.in/products/citrix-content-collaboration/support.html) om deze waarden op te vragen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In het gedeelte **Citrix ShareFile instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-citrix-sharefile-single-sign-on"></a>Eenmalige aanmelding voor Citrix ShareFile configureren

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van **Citrix ShareFile**.

2. Klik in de werkbalk bovenaan op **Admin**.

3. Selecteer in het navigatievenster links **Configure Single Sign-On**.
   
    ![Accountbeheer](./media/sharefile-tutorial/ic773627.png "Accountbeheer")

4. Voer in het dialoogvenster **Single sign-on / SAML 2.0 Configuration** de volgende stappen uit onder **Basic Settings**:
   
    ![Eenmalige aanmelding](./media/sharefile-tutorial/ic773628.png "Eenmalige aanmelding")
   
    a. Klik op **Enable SAML**.
    
    b. Plak in het tekstvak **Your IDP Issuer/ Entity ID** de waarde van **Azure AD-id** die u hebt gekopieerd uit de Azure-portal.

    c. Klik op **Change** naast het veld **X.509 Certificate** en upload het certificaat dat u hebt gedownload uit de Azure-portal.
    
    d. Plak in het tekstvak **Login URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.
    
    e. Plak in het tekstvak **VAN de URL van afmelden** de waarde van de URL van **afmelden** die u hebt gekopieerd uit azure-portal.

5. Klik op **Save** in de beheerportal van Citrix ShareFile.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In het **veld Type Gebruikersnaam** **brittasimon\@yourcompanydomain.extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Citrix ShareFile.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en selecteer vervolgens **Citrix ShareFile**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ en selecteer **Citrix ShareFile** in de lijst met toepassingen.

    ![De koppeling Citrix ShareFile in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-citrix-sharefile-test-user"></a>Testgebruiker voor Citrix ShareFile maken

Als gebruikers van Azure AD zich moeten kunnen aanmelden bij Citrix ShareFile, moet u ze eerst toevoegen aan Citrix ShareFile. In het geval van Citrix ShareFile moet dit handmatig gebeuren.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij uw **Citrix ShareFile**-tenant.

2. Klik op **Manage Users \> Manage Users Home \> + Create Employee**.
   
    ![Werknemer maken](./media/sharefile-tutorial/IC781050.png "Werknemer maken")

3. Voer in het gedeelte **Basic Information** onderstaande stappen uit:
   
    ![Basisinformatie](./media/sharefile-tutorial/IC799951.png "Basisinformatie")
   
    a. Typ in het tekstvak **E-mailadres** het e-mailadres van Britta Simon als **\@brittasimon contoso.com**.
   
    b. Typ in het tekstvak **First Name** de **voornaam** van de gebruiker, in dit geval **Britta**.
   
    c. Typ in het tekstvak **Last Name** de **achternaam** van de gebruiker, in dit geval **Simon**.

4. Klik **op Gebruiker toevoegen**.
  
    >[!NOTE]
    >De houder van het Azure AD-account krijgt een e-mailbericht met een koppeling om het account te bevestigen voordat dit actief wordt. U kunt ieder hulpprogramma voor het maken van Citrix ShareFile-gebruikersaccounts of API's van Citrix ShareFile gebruiken voor het inrichten van Azure AD-gebruikersaccounts.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Citrix ShareFile klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van in het toegangsvenster waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

