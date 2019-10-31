---
title: 'Zelf studie: integratie Azure Active Directory met 123ContactForm | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en 123ContactForm.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5211910a-ab96-4709-959a-524c4d57c43e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3e8328ac4008950d67cf5645c6a48c48d1196085
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73154920"
---
# <a name="tutorial-azure-active-directory-integration-with-123contactform"></a>Zelf studie: integratie Azure Active Directory met 123ContactForm

In deze zelfstudie leert u hoe u 123ContactForm integreert met Azure Active Directory (Azure AD).
De integratie van 123ContactForm met Azure AD heeft de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot 123ContactForm.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij 123ContactForm (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om Azure AD-integratie met 123ContactForm te configureren:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op 123ContactForm waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* 123ContactForm biedt ondersteuning voor met **SP en IDP** geïnitieerde eenmalige aanmelding
* 123ContactForm biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers

## <a name="adding-123contactform-from-the-gallery"></a>123ContactForm toevoegen vanuit de galerie

Als u de integratie van 123ContactForm met Azure AD wilt configureren, moet u 123ContactForm vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u 123ContactForm wilt toevoegen vanuit de galerie, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de  **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **123ContactForm** in het zoekvak, selecteer **123ContactForm** in het deelvenster met resultaten en klik op de knop **Toevoegen** om de app toe te voegen.

     ![123ContactForm in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met 123ContactForm op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in 123ContactForm tot stand is gebracht.

Als u eenmalige aanmelding met Azure AD wilt configureren en testen met 123ContactForm, moet u aan de volgende vereisten voldoen:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor 123ContactForm configureren](#configure-123contactform-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor 123ContactForm maken](#create-123contactform-test-user)** : als u een tegenhanger van Britta Simon in 123ContactForm wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met 123ContactForm te configureren:

1. In de [Azure-portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **123ContactForm**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Informatie over eenmalige aanmelding voor 123ContactForm-domein en -URL's](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://www.123contactform.com/saml/azure_ad/<tenant_id>/metadata`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://www.123contactform.com/saml/azure_ad/<tenant_id>/acs`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Informatie over eenmalige aanmelding voor 123ContactForm-domein en -URL's](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://www.123contactform.com/saml/azure_ad/<tenant_id>/sso`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke URL's en id, zoals later in de zelfstudie wordt uitgelegd.

6. Ga op de pagina **Eenmalige aanmelding met SAML instellen** naar de sectie **SAML-handtekeningcertificaat** en klik op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

7. Kopieer in de sectie **123ContactForm instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-123contactform-single-sign-on"></a>Eenmalige aanmelding configureren voor 123ContactForm

1. Als u eenmalige aanmelding aan de zijde van **123ContactForm** wilt configureren, gaat u naar [https://www.123contactform.com/form-2709121/](https://www.123contactform.com/form-2709121/) en voert u de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/123contactform-tutorial/submit.png) 

    a. Typ in het tekstvak **e-mail** het e-mail adres van de gebruiker, zoals **BrittaSimon\@contoso.com**.

    b. Klik op **Upload** en blader naar het gedownloade XML-bestand met metagegevens dat u hebt gedownload vanuit de Azure-portal.

    c. Klik op **SUBMIT FORM**.

2. Voer in **Microsoft Azure AD - Eenmalige aanmelding - App-instellingen configureren** de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/123contactform-tutorial/url3.png)

    a. Als u de toepassing wilt configureren in de **door IDP geïnitieerde modus**, kopieert u de waarde van **IDENTIFIER** voor uw exemplaar en plakt u deze in het tekstvak **Id** in de sectie **SAML-basisconfiguratie** van de Azure-portal.

    b. Als u de toepassing wilt configureren in de **door IDP geïnitieerde modus**, kopieert u de waarde van **REPLY URL** voor uw exemplaar en plakt u deze in het tekstvak **Antwoord-URL** in de sectie **SAML-basisconfiguratie** van de Azure-portal.

    c. Als u de toepassing wilt configureren in de **door SP geïnitieerde modus**, kopieert u de waarde van **SIGN ON URL** voor uw exemplaar en plakt u deze in het tekstvak **Aanmeldings-URL** in de sectie **SAML-basisconfiguratie** van de Azure-portal.

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

In deze sectie zorgt u ervoor dat Britta Simon eenmalige aanmelding met Azure kan gebruiken door haar toegang te geven tot 123ContactForm.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en selecteer vervolgens **123ContactForm**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **123ContactForm** in de lijst met toepassingen.

    ![De koppeling naar 123ContactForm in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-123contactform-test-user"></a>123ContactForm-testgebruiker maken

In deze sectie wordt een gebruiker met de naam Britta Simon gemaakt in 123ContactForm. 123ContactForm biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Dit is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in 123ContactForm, wordt er na verificatie een nieuwe gemaakt.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel 123ContactForm in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van 123ContactForm waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)