---
title: 'Zelf studie: integratie Azure Active Directory met SAManage | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Samanage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 741efbae84e18c811beb6c0579c1949c5ddf619c
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73160111"
---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Zelf studie: integratie Azure Active Directory met SAManage

In deze zelfstudie leert u hoe u Samanage kunt integreren met Azure Active Directory (Azure AD).
De integratie van Samanage met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Samanage.
* U kunt uw gebruikers zich automatisch laten aanmelden bij Samanage (eenmalige aanmelding) met hun Azure AD-account.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Samanage hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Samanage waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Samanage ondersteunt door **SP** geïnitieerde eenmalige aanmelding (SSO)

## <a name="adding-samanage-from-the-gallery"></a>Samanage toevoegen vanuit de galerie

Voor het configureren van de integratie van Samanage in Azure AD moet u Samanage uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Samanage toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de  **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Samanage** in het zoekvak, selecteer **Samanage** in het resultaatvenster en klik op de knop **Toevoegen** om de toepassing toe te voegen.

     ![Samanage in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding met Samanage configureren en testen met behulp van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Samanage tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding met Samanage wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor Samanage configureren](#configure-samanage-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Een testgebruiker maken in Samanage](#create-samanage-test-user)** : om in Samanage een tegenhanger van Britta Simon te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met Samanage moet u de volgende stappen uitvoeren:

1. In de [Azure-portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **Samanage**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Samanage](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke URL voor eenmalige aanmelding en de id, zoals later in de zelfstudie wordt uitgelegd. Neem voor meer informatie contact op met het [ondersteuningsteam van Samanage](https://www.samanage.com/support). U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

4. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **Samanage instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-samanage-single-sign-on"></a>Eenmalige aanmelding voor Samanage configureren

1. Meld u in een ander browservenster aan bij uw Samanage-bedrijfssite als beheerder.

2. Klik op **Dashboard** en selecteer **Setup** in het linker navigatievenster.
   
    ![Dashboard](./media/samanage-tutorial/tutorial_samanage_001.png "Dashboard")

3. Klik op **Single Sign-On**.
   
    ![Eenmalige aanmelding](./media/samanage-tutorial/tutorial_samanage_002.png "Eenmalige aanmelding")

4. Navigeer naar de sectie **Login using SAML** en voer de volgende stappen uit:
   
    ![Aanmelden met SAML](./media/samanage-tutorial/tutorial_samanage_003.png "Aanmelden met SAML")
 
    a. Klik op **Enable Single Sign-On with SAML**.  
 
    b. Plak de waarde van **Azure Ad-id**, die u hebt gekopieerd uit de Azure-portal, in het tekstvak **Identity Provider URL**.    
 
    c. Bevestig dat de **Login URL** overeenkomt met de **Aanmeldings-URL** van de sectie **Standaard SAML-configuratie** in de Azure-portal.
 
    d. Plak in het tekstvak **Logout URL** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit de Azure-portal.
 
    e. Typ in het tekstvak **SAML Issuer** de app-id-URI die is ingesteld in uw id-provider.
 
    f. Open in Kladblok het met Base 64 gecodeerde certificaat dat u hebt gedownload uit de Azure-portal, kopieer de inhoud ervan naar het Klembord en plak deze vervolgens in het tekstvak **Paste your Identity Provider x.509 Certificate below**.
 
    g. Klik op **Create users if they do not exist in Samanage**.
 
    h. Klik op **Update**.

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Samanage.

1. Selecteer **Bedrijfstoepassingen** in Azure Portal, selecteer **Alle toepassingen** en selecteer vervolgens **Samanage**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Samanage** in de lijst met toepassingen.

    ![De koppeling Samanage in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-samanage-test-user"></a>Een testgebruiker maken in Samanage

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Samanage, moeten ze worden ingericht in Samanage.  
In het geval van Samanage is inrichten een handmatige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u bij uw Samanage-bedrijfssite aan als beheerder.

2. Klik op **Dashboard** en selecteer **Setup** in het linker navigatievenster.
   
    ![Instellen](./media/samanage-tutorial/tutorial_samanage_001.png "Instellen")

3. Klik op het tabblad **Users**
   
    ![Gebruikers](./media/samanage-tutorial/tutorial_samanage_006.png "Gebruikers")

4. Klik op **New User**.
   
    ![Nieuwe gebruiker](./media/samanage-tutorial/tutorial_samanage_007.png "Nieuwe gebruiker")

5. Typ bij **Name** en **Email Address** de naam en het e-mailadres van een Azure Active Directory-account dat u wilt inrichten, en klik op **Create user**.
   
    ![Gebruiker maken](./media/samanage-tutorial/tutorial_samanage_008.png "Gebruiker maken")
   
   >[!NOTE]
   >De houder van het Azure Active Directory-account ontvangt een e-mail en volgt een koppeling om zijn account te bevestigen voordat het actief wordt. U kunt ook alle andere hulpprogramma's voor het creëren van Samanage-gebruikersaccounts of API's van Samanage gebruiken om Azure Active Directory-gebruikersaccounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de Samanage-tegel in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van Samanage waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

