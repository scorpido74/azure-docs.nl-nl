---
title: 'Zelf studie: integratie Azure Active Directory met 8X8 Virtual Office | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en 8x8 Virtual Office.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: b34a6edf-e745-4aec-b0b2-7337473d64c5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87728a5ff9690df6f47a2721ad4a51b5c2388877
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73154587"
---
# <a name="tutorial-azure-active-directory-integration-with-8x8-virtual-office"></a>Zelf studie: integratie met 8X8 Virtual Office Azure Active Directory

In deze zelfstudie leert u hoe u 8x8 Virtual Office integreert met Azure Active Directory (Azure AD).
Het integreren van 8x8 Virtual Office met Azure AD biedt de volgende voordelen:

* U bepaalt in Azure AD wie er toegang heeft tot 8x8 Virtual Office.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij 8x8 Virtual Office (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met 8x8 Virtual Office hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een 8x8 Virtual Office-abonnement waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.


* 8x8 Virtual Office ondersteunt door **IDP** geïnitieerde SSO

* 8x8 Virtual Office biedt ondersteuning voor het **Just-In-Time** inrichten van gebruikers

## <a name="adding-8x8-virtual-office-from-the-gallery"></a>8x8 Virtual Office toevoegen vanuit de galerie

Als u de integratie van 8x8 Virtual Office met Azure AD wilt configureren, moet u 8x8 Virtual Office vanuit de galerie toevoegen aan uw beheerde SaaS-apps.

**Voer de volgende stappen uit als u 8x8 Virtual Office wilt toevoegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **8x8 Virtual Office**, selecteer **8x8 Virtual Office** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![8x8 Virtual Office in de lijst resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding van Azure AD configureren en testen met 8x8 Virtual Office op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in 8x8 Virtual Office tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD wilt configureren en testen met 8x8 Virtual Office, moet u de volgende bouwstenen uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[8x8 Virtual Office-eenmalige aanmelding configureren](#configure-8x8-virtual-office-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[8x8 Virtual Office-testgebruiker maken](#create-8x8-virtual-office-test-user)** : als u een equivalent van Britta Simon in 8x8 Virtual Office wilt hebben dat is gekoppeld aan de Azure AD-versie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met 8x8 Virtual Office, moet u de volgende stappen uitvoeren:

1. In de [Azure-portal](https://portal.azure.com/) selecteert u op de integratiepagina van de **8x8 Virtual Office**-toepassing de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in het dialoogvenster **Standaard SAML-configuratie** de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor het 8x8 Virtual Office-domein en -URL's](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://sso.8x8.com/saml2`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://sso.8x8.com/saml2`

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Raw)** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificateraw.png)

6. In de sectie **8x8 Virtual Office instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-8x8-virtual-office-single-sign-on"></a>Eenmalige aanmelding configureren voor 8x8 Virtual Office

1. Meld u als beheerder aan bij uw 8x8 Virtual Office-tenant.

1. Selecteer **Virtual Office-accountbeheer** in het toepassingsvenster.

    ![Configureren aan de toepassingszijde](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_001.png)

1. Selecteer het **bedrijfs**account dat u wilt beheren en klik op **Aanmelden**.

    ![Configureren aan de toepassingszijde](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_002.png)

1. Klik op het tabblad **ACCOUNTS** in de menulijst.

   ![Configureren aan de toepassingszijde](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_003.png)

1. Klik op **Eenmalige aanmelding** in de lijst met accounts.
  
   ![Configureren aan de toepassingszijde](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_004.png)

1. Selecteer **Eenmalige aanmelding** bij de verificatiemethoden en klik op **SAML**.

   ![Configureren aan de toepassingszijde](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_005.png)

1. In het gedeelte voor **SAML-eenmalige aanmelding** voert u de volgende stappen uit:

   ![Configureren aan de toepassingszijde](./media/8x8virtualoffice-tutorial/tutorial_8x8virtualoffice_006.png)

   a. Plak in het tekstvak voor de **URL voor eenmalige aanmelding** de waarde van de **aanmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

   b. Plak in het tekstvak voor de **afmeldings-URL** de waarde van de **afmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

   c. Plak in het tekstvak voor de **certificaatverlener** de waarde van de **Azure AD-id** die u uit de Azure-portal hebt gekopieerd.

   d. Klik op **Bladeren** om het certificaat te uploaden dat u uit de Azure-portal hebt gedownload.

   e. Klik op de knop **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon**in.
  
    b. Typ in het veld **gebruikers naam** **brittasimon\@yourcompanydomain. extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie zorgt u ervoor dat Britta Simon van eenmalige aanmelding met Azure{b> <b}gebruik kan maken door haar toegang te verlenen tot 8x8 Virtual Office.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen**, selecteer **Alle toepassingen** en selecteer vervolgens **8x8 Virtual Office**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Typ en selecteer **8x8 Virtual Office** in de lijst met toepassingen.

    ![De koppeling voor 8x8 Virtual Office in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-8x8-virtual-office-test-user"></a>Een 8x8 Virtual Office-testgebruiker maken

In dit gedeelte wordt een gebruiker met de naam Britta Simon gemaakt in 8x8 Virtual Office. 8x8 Virtual Office biedt ondersteuning voor **Just-In-Time-inrichting van gebruikers**. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker bestaat in 8x8 Virtual Office, wordt er een nieuwe gemaakt na de verificatie.

> [!NOTE]
> Als u een gebruiker handmatig moet maken, neem dan contact op met het [8x8 Virtual Office-ondersteuningsteam](https://www.8x8.com/about-us/contact-us).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de 8x8 Virtual Office-tegel in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van 8x8 Virtual Office waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

