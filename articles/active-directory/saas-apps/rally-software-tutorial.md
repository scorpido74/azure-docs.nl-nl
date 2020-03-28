---
title: 'Zelfstudie: Azure Active Directory-integratie met Rally-software | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Rally Software.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ba25fade-e152-42dd-8377-a30bbc48c3ed
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: de84d03c3e0e433dbe7bc24c47b1766b32ad7bc4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093181"
---
# <a name="tutorial-azure-active-directory-integration-with-rally-software"></a>Zelfstudie: Azure Active Directory-integratie met Rally-software

In deze zelfstudie leert u hoe u RallySoftware integreert met Azure Active Directory (Azure AD).
Het integreren van Rally Software met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Rally Software.
* U uw gebruikers automatisch laten aanmelden bij Rally Software (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie met Rally Software wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement met één aanmelding voor Rally Software

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Rally Software ondersteunt **SP** geïnitieerde SSO

## <a name="adding-rally-software-from-the-gallery"></a>Rallysoftware toevoegen vanuit de galerij

Als u de integratie van Rally Software in Azure AD wilt configureren, moet u Rally Software uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u Rally Software vanuit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Rallysoftware**in het zoekvak , selecteer **Rallysoftware** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![Rally Software in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Rally Software op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding op het werk moet een koppeling slinken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Rally Software.

Als u Azure AD single sign-on met Rally Software wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Rally Software Single Sign-On](#configure-rally-software-single-sign-on)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak Rally Software test gebruiker](#create-rally-software-test-user)** - om een tegenhanger van Britta Simon in Rally Software die is gekoppeld aan de Azure AD vertegenwoordiging van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met Rally Software te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Rally Software-toepassingsintegratie** de optie Eén **aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Rally Software Domain en URL's single sign-on informatie](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<tenant-name>.rally.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<tenant-name>.rally.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met [rally software client support team](https://help.rallydev.com/) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **Rally Software instellen** de juiste URL(s) volgens uw eis.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-rally-software-single-sign-on"></a>Verzamelsoftware Eenmalig aanmelden configureren

1. Meld u aan bij uw **Rally Software** tenant.

2. Klik op de werkbalk bovenaan op **Instellen**en selecteer **Vervolgens Abonnement**.
   
    ![Abonnement](./media/rally-software-tutorial/ic769531.png "Abonnement")

3. Klik op de knop **Actie.** Selecteer **Abonnement bewerken** aan de rechterbovenzijde van de werkbalk.

4. Voer **op** de pagina Abonnement de volgende stappen uit en klik vervolgens op **Opslaan & Sluiten:**
   
    ![Verificatie](./media/rally-software-tutorial/ic769542.png "Authentication")
   
    a. Selecteer **Rally- of SSO-verificatie** in de vervolgkeuzelijst Verificatie.

    b. Plak in het **tekstvak URL van de identiteitsprovider** de waarde van **Azure AD-id**, die u hebt gekopieerd van Azure-portal. 

    c. Plak in het tekstvak **SSO Logout** de waarde van de URL van **Afmelden**, die u hebt gekopieerd van azure-portal.

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

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Rally Software.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Rallysoftware**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Rally Software**in de lijst met toepassingen .

    ![De Link Rally Software in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-rally-software-test-user"></a>Testgebruiker RallySoftware maken

Als Azure AD-gebruikers zich kunnen aanmelden, moeten ze zijn ingericht bij de Rally-softwaremet hun Azure Active Directory-gebruikersnamen.

**Voer de volgende stappen uit om de inrichting van gebruikers te configureren:**

1. Meld u aan bij uw Rally Software tenant.

2. Ga naar **Gebruikers instellen \> **en klik op + Nieuw **toevoegen**.
   
    ![Gebruikers](./media/rally-software-tutorial/ic781039.png "Gebruikers")

3. Typ de naam in het tekstvak Nieuwe gebruiker en klik op **Toevoegen met details**.

4. Voer in de sectie **Create User** de volgende stappen uit:
   
    ![Gebruiker maken](./media/rally-software-tutorial/ic781040.png "Gebruiker maken")

    a. Typ in het tekstvak **Gebruikersnaam** de naam van de gebruiker zoals **Brittsimon**.
   
    b. Voer in het tekstvak **E-mailadres** de brittasimon@contoso.come-mail van de gebruiker in, zoals .

    c. Voer in het tekstvak **Voornaam** de voornaam van de gebruiker in, zoals **Britta**.

    d. Voer in het tekstvak **Achternaam** de achternaam van de gebruiker in, zoals **Simon**.

    e. Klik op **Opslaan en sluiten**.

   >[!NOTE]
   >U alle andere tools voor het maken van gebruikersaccounts voor rallysoftware of API's van Rally Software gebruiken om Azure AD-gebruikersaccounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel RallySoftware in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Rally-software waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

