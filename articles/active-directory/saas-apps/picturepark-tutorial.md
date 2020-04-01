---
title: 'Zelfstudie: Azure Active Directory-integratie met Picturepark | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Picturepark.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31c21cd4-9c00-4cad-9538-a13996dc872f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 08c5bd8da0dda74156b2d44c8106ed345ef749dd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73177012"
---
# <a name="tutorial-azure-active-directory-integration-with-picturepark"></a>Zelfstudie: Azure Active Directory-integratie met Picturepark

In deze zelfstudie leert u hoe u Picturepark integreert met Azure Active Directory (Azure AD).
De integratie van Picturepark met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Picturepark.
* U uw gebruikers automatisch laten inloggen op Picturepark (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Picturepark wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Abonnement met eenmalige aanmelding in Picturepark

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Picturepark steunt **door SP** geïnitieerde SSO

## <a name="adding-picturepark-from-the-gallery"></a>Picturepark toevoegen vanuit de galerie

Als u de integratie van Picturepark in Azure AD wilt configureren, moet u Picturepark vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Picturepark vanuit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Picturepark**in het zoekvak , selecteer **afbeeldingspark** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![Picturepark in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Picturepark op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding aan het werk moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Picturepark.

Als u Azure AD-single sign-on met Picturepark wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Picturepark Single Sign-On](#configure-picturepark-single-sign-on)** - om de instellingen voor eenmalig aanmelden aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak Picturepark-testgebruiker](#create-picturepark-test-user)** - om een tegenhanger van Britta Simon in Picturepark te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de volgende stappen uit te voeren voor het configureren van Azure AD AD single sign-on met Picturepark:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Integratie van de Picturepark-toepassing** de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over Picturepark-domein en URL's met eenmalige aanmelding](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.picturepark.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: 

    |  |
    |--|
    | `https://<companyname>.current-picturepark.com`|
    | `https://<companyname>.picturepark.com`|
    | `https://<companyname>.next-picturepark.com`|
    | |

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met [het ondersteuningsteam van Picturepark Client](https://picturepark.com/company/picturepark-customer-support) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Klik in de sectie **SAML-handtekeningcertificaat** op de knop **Bewerken** om het dialoogvenster **SAML-handtekeningcertificaat** te openen.

    ![SAML-handtekeningcertificaat bewerken](common/edit-certificate.png)

6. Kopieer in de sectie **SAML-ondertekeningscertificaat** de **duimafdruk** en sla deze op uw computer op.

    ![Waarde van vingerafdruk kopiëren](common/copy-thumbprint.png)

7. Kopieer in de sectie **Picturepark instellen** de juiste URL(s) volgens uw vereiste. Gebruik de waarde bij **Aanmelding**strekken met het volgende patroon:`https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ is de tenant-id van het Azure AD-abonnement.

    ![Configuratie-URL's kopiëren](./media/picturepark-tutorial/configurls.png)

    a. Azure AD-id

    b. Afmeldings-URL

### <a name="configure-picturepark-single-sign-on"></a>Picturepark Single Sign-On configureren

1. Meld u in een ander browservenster aan op uw site van het Bedrijf Picturepark als beheerder.

2. Klik op de werkbalk bovenaan op **Systeembeheer**en klik vervolgens op **Beheerconsole**.
   
    ![Beheerconsole](./media/picturepark-tutorial/ic795062.png "Beheerconsole")

3. Klik **op Verificatie**en klik vervolgens op **Identiteitsproviders**.
   
    ![Verificatie](./media/picturepark-tutorial/ic795063.png "Verificatie")

4. Voer in de sectie **Configuratie van de identiteitsprovider** de volgende stappen uit:
   
    ![Configuratie van identiteitsprovider](./media/picturepark-tutorial/ic795064.png "Configuratie van identiteitsprovider")
   
    a. Klik op**toevoegen**.
  
    b. Typ een naam voor uw configuratie.
   
    c. Selecteer **Instellen als standaard**.
   
    d. Plak in het tekstvak **Issuer URI** de waarde van **de aanmeldings-URL** die u hebt gekopieerd vanuit azure-portal.
   
    e. Plak in het tekstvak Van De **duimafdruk** van Trusted Issuer Thumb De waarde van **Thumbprint** die u hebt gekopieerd uit de sectie **SAML-ondertekeningscertificaat.** 

5. Klik **op JoinDefaultUsersGroup**.

6. Als u het kenmerk **E-mailadres** wilt `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` instellen in het tekstvak **Claim,** typt u en klikt u op **Opslaan**.

      ![Configuratie](./media/picturepark-tutorial/ic795065.png "Configuratie")

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

In deze sectie u Britta Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Picturepark.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Vervolgens Picturepark**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Picturepark**in de lijst met toepassingen .

    ![De koppeling Picturepark in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-picturepark-test-user"></a>Picturepark-testgebruiker maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij Picturepark, moeten ze worden ingericht in Picturepark. In het geval van Picturepark is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij uw **Picturepark-huurder.**

1. Klik op de werkbalk bovenaan op **Systeembeheer**en klik vervolgens op **Gebruikers**.
   
    ![Gebruikers](./media/picturepark-tutorial/ic795067.png "Gebruikers")

1. Klik op het **tabblad Gebruikersoverzicht** op **Nieuw**.
   
    ![Gebruikersbeheer](./media/picturepark-tutorial/ic795068.png "Gebruikersbeheer")

1. Voer in het dialoogvenster **Gebruiker maken** de volgende stappen uit van een geldige Azure Active Directory-gebruiker die u wilt inrichten:
   
    ![Gebruiker maken](./media/picturepark-tutorial/ic795069.png "Gebruiker maken")
   
    a. Typ in het tekstvak **E-mailadres** het `BrittaSimon@contoso.com` **e-mailadres** van de gebruiker.  
   
    b. Typ in de tekstvakken **Wachtwoord** en **Wachtwoord bevestigen** het **wachtwoord** van BrittaSimon. 
   
    c. Typ in het tekstvak **Voornaam** de **voornaam** van de gebruiker **Britta**. 
   
    d. Typ in het tekstvak **Achternaam** de **achternaam** van de gebruiker **Simon**.
   
    e. Typ in het tekstvak **Bedrijf** de **bedrijfsnaam** van de gebruiker. 
   
    f. Selecteer **in het** tekstvak Land de **land/regio** van de gebruiker.
  
    g. Typ in het **postcodevak** de **postcode** van de stad.
   
    h. Typ in het tekstvak **Stad** de **naam Plaats** van de gebruiker.

    i. Selecteer een **taal**.
   
    j. Klik **op Maken**.

>[!NOTE]
>U alle andere tools voor het maken van gebruikersaccounts van Picturepark of API's die door Picturepark worden verstrekt, gebruiken om Azure AD-gebruikersaccounts in te richten.
>

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de Picturepark-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij het Picturepark waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

