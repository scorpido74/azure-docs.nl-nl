---
title: 'Zelf studie: integratie Azure Active Directory met CloudPassage | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en CloudPassage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bfe1f14e-74e4-4680-ac9e-f7355e1c94cc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e73861f9ce6ff5cd5bfcd260a8865755ccc3ec71
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73157354"
---
# <a name="tutorial-azure-active-directory-integration-with-cloudpassage"></a>Zelf studie: integratie Azure Active Directory met CloudPassage

In deze zelfstudie leert u hoe u CloudPassage kunt integreren met Azure Active Directory (Azure AD).
De integratie van CloudPassage met Azure AD biedt de volgende voordelen:

* U kunt in Azure AD bepalen wie er toegang heeft tot CloudPassage.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij CloudPassage (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met CloudPassage hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op CloudPassage waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* CloudPassage ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-cloudpassage-from-the-gallery"></a>CloudPassage toevoegen vanuit de galerie

Om de integratie van CloudPassage in Azure AD te configureren, moet u CloudPassage vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

**Ga als volgt te werk om CloudPassage vanuit de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de  **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **CloudPassage** in het zoekvak, selecteer **CloudPassage** in het deelvenster met resultaten en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

     ![CloudPassage toevoegen vanuit de galerie](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In dit gedeelte configureert en test u eenmalige aanmelding van Azure AD met CloudPassage op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tot stand is gebracht tussen een Azure AD-gebruiker en de gerelateerde gebruiker in CloudPassage.

Als u eenmalige aanmelding van Azure AD met CloudPassage wilt configureren en testen, moet u de volgende procedures uitvoeren:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor CloudPassage configureren](#configure-cloudpassage-single-sign-on)** : de instellingen voor eenmalige aanmelding aan de clientzijde configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor CloudPassage maken](#create-cloudpassage-test-user)** : als u een tegenhanger van Britta Simon in CloudPassage wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD te configureren voor CloudPassage:

1. Ga in de [Azure-portal](https://portal.azure.com/) naar de pagina van de integratie van **CloudPassage** en selecteer **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Gegevens van domein en URL's voor eenmalige aanmelding bij CloudPassage](common/sp-reply.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://portal.cloudpassage.com/saml/init/accountid`

    b. In het tekstvak **Antwoord-URL** typt u een URL en gebruikt u het volgende patroon: `https://portal.cloudpassage.com/saml/consume/accountid`. U kunt de waarde voor dit kenmerk opvragen door te klikken op **SSO Setup documentation** in het gedeelte **Single Sign-on Settings** van de CloudPassage-portal.

    ![Eenmalige aanmelding configureren](./media/cloudpassage-tutorial/tutorial_cloudpassage_05.png)

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en antwoord-URL. Neem contact op met het [klantondersteuningsteam van CloudPassage](https://www.cloudpassage.com/company/contact/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. De toepassing CloudPassage verwacht dat de SAML-asserties een specifieke indeling hebben. Configureer de volgende claims voor deze toepassing. U kunt de waarden van deze kenmerken vanuit de sectie **Gebruikerskenmerken** op de integratiepagina van de toepassing-beheren. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op de knop **Bewerken** om het dialoogvenster **Gebruikerskenmerken** te openen.

    ![image](common/edit-attribute.png)

6. Bewerk in het gedeelte **Gebruikersclaims** in het dialoogvenster **Gebruikerskenmerken** de claims met het **pictogram Bewerken** of voeg de claims toe door met **Nieuwe claim toevoegen** het kenmerk van het SAML-token te configureren, zoals wordt weergegeven in de bovenstaande afbeelding. Hierna voert u de volgende stappen uit: 

    | Naam | Bronkenmerk|
    | ---------------| --------------- |
    | firstname |user.givenname |
    | lastname |user.surname |
    | e-mail |user.mail |

    a. Klik op **Nieuwe claim toevoegen** om het dialoogvenster **Gebruikersclaims beheren** te openen.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. In het tekstvak **Naam** typt u de naam van het kenmerk die voor die rij wordt weergegeven.

    c. Laat **Naamruimte** leeg.

    d. Selecteer Bron bij **Kenmerk**.

    e. Typ de kenmerkwaarde voor die rij in de lijst met **bronkenmerken**.

    f. Klik op **OK**.

    g. Klik op **Opslaan**.

7. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

8. Kopieer in het gedeelte **CloudPassage instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-cloudpassage-single-sign-on"></a>Eenmalige aanmelding voor CloudPassage configureren

1. Meld u in een ander browservenster als beheerder aan bij de bedrijfssite van CloudPassage.

1. Klik in de menubalk bovenaan op **Settings** en klik vervolgens op **Site Administration**. 
   
    ![Eenmalige aanmelding configureren][12]

1. Klik op het tabblad **Authentication Settings**. 
   
    ![Eenmalige aanmelding configureren][13]

1. Voer de volgende stappen uit in het gedeelte **Single Sign-on Settings**: 
   
    ![Eenmalige aanmelding configureren][14]

    a. Schakel het selectievakje **Enable Single Sign-on (SSO) (SSO Setup Documentation)** in.
    
    b. Plak de **id van Azure AD** in het tekstvak **SAML issuer URL**.
  
    c. Plak de **aanmeldings-URL** in het tekstvak **SAML endpoint URL**.
  
    d. Plak de **afmeldings-URL** in het tekstvak **Logout landing page**.
  
    e. Open het gedownloade certificaat in Kladblok, kopieer de inhoud van het certificaat naar het Klembord en plak de inhoud vervolgens in het tekstvak **x.509 certificate**.
  
    f. Klik op **Opslaan**.

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

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot CloudPassage.

1. Selecteer in de Azure-portal achtereenvolgens **Bedrijfstoepassingen**, **Alle toepassingen** en **CloudPassage**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **CloudPassage** in de lijst met toepassingen.

    ![De koppeling naar CloudPassage in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-cloudpassage-test-user"></a>Een testgebruiker voor CloudPassage maken

Het doel van dit gedeelte is het maken van een gebruiker met de naam Britta Simon in CloudPassage.

**Voer de volgende stappen uit om in CloudPassage een gebruiker met de naam Britta Simon te maken:**

1. Meld u als beheerder aan bij de bedrijfssite van **CloudPassage**. 

1. Klik in de werkbalk bovenaan op **Settings** en klik vervolgens op **Site Administration**. 
   
    ![Een testgebruiker voor CloudPassage maken][22] 

1. Klik op het tabblad **Users** en klik vervolgens op **Add New User**. 
   
    ![Een testgebruiker voor CloudPassage maken][23]

1. Voer in het gedeelte **Add New User** de volgende stappen uit: 
   
    ![Een testgebruiker voor CloudPassage maken][24]
    
    a. Typ Britta in het tekstvak **First Name**. 
  
    b. Typ Simon in het tekstvak **Last Name**.
  
    c. Typ in de tekstvakken **Username**, **Email** en **Retype Email** de gebruikersnaam van Britta uit Azure AD.
  
    d. Selecteer **Enable Halo Portal Access** bij **Access Type**.
  
    e. Klik op **Add**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel van CloudPassage klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van CloudPassage waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[12]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_07.png
[13]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_08.png
[14]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_09.png
[15]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_10.png
[22]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_15.png
[23]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_16.png
[24]: ./media/cloudpassage-tutorial/tutorial_cloudpassage_17.png

