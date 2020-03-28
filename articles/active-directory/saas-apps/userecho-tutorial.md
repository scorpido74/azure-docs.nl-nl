---
title: 'Zelfstudie: Azure Active Directory-integratie met UserEcho | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en UserEcho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bedd916b-8f69-4b50-9b8d-56f4ee3bd3ed
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: 59d61eda7002fe46cf99fac63822b2333b2d64b5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67087772"
---
# <a name="tutorial-azure-active-directory-integration-with-userecho"></a>Zelfstudie: Azure Active Directory-integratie met UserEcho

In deze zelfstudie leert u hoe u UserEcho integreert met Azure Active Directory (Azure AD).
Het integreren van UserEcho met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot UserEcho.
* U uw gebruikers automatisch laten aanmelden bij UserEcho (Single Sign-On) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie met UserEcho wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Abonnement met één aanmelding voor UserEcho

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* UserEcho ondersteunt **door SP** geïnitieerde SSO

## <a name="adding-userecho-from-the-gallery"></a>UserEcho toevoegen vanuit de galerie

Als u de integratie van UserEcho in Azure AD wilt configureren, moet u UserEcho vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om UserEcho vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **UserEcho**in het zoekvak , selecteer **UserEcho** uit het resultaatpaneel en klik op **Knop Toevoegen** om de toepassing toe te voegen.

     ![UserEcho in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met UserEcho op basis van een testgebruiker genaamd **Britta Simon**.
Voor eenmalige aanmelding om te werken, moet een koppelingsrelatie worden ingesteld tussen een Azure AD-gebruiker en de gerelateerde gebruiker in UserEcho.

Als u Azure AD Single Sign-on wilt configureren en testen met UserEcho, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[UserEcho Single Sign-On configureren](#configure-userecho-single-sign-on)** - om de instellingen voor eenmalig aanmelden aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[UserEcho-testgebruiker maken](#create-userecho-test-user)** - om een tegenhanger van Britta Simon in UserEcho te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met UserEcho te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **UserEcho-toepassingsintegratie** de optie **Eén aanmelding .**

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![UserEcho-domein- en URL's met eenmalige aanmelding](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.userecho.com/`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<companyname>.userecho.com/saml/metadata/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met [het ondersteuningsteam van UserEcho Client](https://feedback.userecho.com/) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer in de sectie **UserEcho instellen** de juiste URL(s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-userecho-single-sign-on"></a>UserEcho Single Sign-On configureren

1. Meld u in een ander browservenster aan op uw UserEcho-bedrijfssite als beheerder.

2. Klik op de werkbalk bovenaan op uw gebruikersnaam om het menu uit te vouwen en klik vervolgens op **Instellen**.
   
    ![Eenmalige aanmelding configureren](./media/userecho-tutorial/tutorial_userecho_06.png) 

3. Klik **op Integraties**.
   
    ![Eenmalige aanmelding configureren](./media/userecho-tutorial/tutorial_userecho_07.png) 

4. Klik op **Website**en klik vervolgens op **Eén aanmelding (SAML2).**
   
    ![Eenmalige aanmelding configureren](./media/userecho-tutorial/tutorial_userecho_08.png) 

5. Voer op de **saml-pagina (Single sign-on)** de volgende stappen uit:
   
    ![Eenmalige aanmelding configureren](./media/userecho-tutorial/tutorial_userecho_09.png)
    
    a. Selecteer **SAML-enabled** **Ja**.
    
    b. Url **van aanmelding**plakken , die u hebt gekopieerd van de Azure-portal naar het tekstvak **SAML SSO URL.**
    
    c. De **URL van afmelden**plakken , die u hebt gekopieerd van de Azure-portal naar het tekstvak voor de **URL van de externe afmelding.**
    
    d. Open uw gedownloade certificaat in Kladblok, kopieer de inhoud en plak het vervolgens in het tekstvak **X.509 Certificate.**
    
    e. Klik op **Opslaan**.

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

In deze sectie stelt u Britta Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot UserEcho.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**, selecteer **Alle toepassingen**en selecteer **Vervolgens UserEcho**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **UserEcho**in de lijst met toepassingen .

    ![De koppeling UserEcho in de lijst Toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **In** het dialoogvenster Gebruikers en groepen **Britta Simon** in de lijst Gebruikers en klik je op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-userecho-test-user"></a>UserEcho-testgebruiker maken

Het doel van deze sectie is het creëren van een gebruiker genaamd Britta Simon in UserEcho.

**Voer de volgende stappen uit om een gebruiker genaamd Britta Simon in UserEcho te maken:**

1. Meld u aan bij uw UserEcho-bedrijfssite als beheerder.

2. Klik op de werkbalk bovenaan op uw gebruikersnaam om het menu uit te vouwen en klik vervolgens op **Instellen**.
   
    ![Eenmalige aanmelding configureren](./media/userecho-tutorial/tutorial_userecho_06.png)

3. Klik op **Gebruikers**om de sectie **Gebruikers** uit te vouwen.
   
    ![Eenmalige aanmelding configureren](./media/userecho-tutorial/tutorial_userecho_10.png)

4. Klik op **Users**.
   
    ![Eenmalige aanmelding configureren](./media/userecho-tutorial/tutorial_userecho_11.png)

5. Klik **op Een nieuwe gebruiker uitnodigen**.
   
    ![Eenmalige aanmelding configureren](./media/userecho-tutorial/tutorial_userecho_12.png)

6. Voer **in het** dialoogvenster Een nieuwe gebruiker uitnodigen de volgende stappen uit:
   
    ![Eenmalige aanmelding configureren](./media/userecho-tutorial/tutorial_userecho_13.png)

    a. Typ in het tekstvak **Naam** de naam van de gebruiker zoals Britta Simon.
    
    b.  Typ in het tekstvak **Email** het e-mailadres van de gebruiker, bijvoorbeeld Brittasimon@contoso.com.
    
    c. Klik op **Uitnodigen**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de userecho-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de UserEcho waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

