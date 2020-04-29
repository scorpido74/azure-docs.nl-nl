---
title: 'Zelf studie: integratie Azure Active Directory met SmartRecruiters | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SmartRecruiters.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e96aeecd-e113-454e-89c3-58c9f44cfd4c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: f20a8a63160a498b9789f77eededad1fd6a02906
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67090112"
---
# <a name="tutorial-azure-active-directory-integration-with-smartrecruiters"></a>Zelf studie: integratie Azure Active Directory met SmartRecruiters

In deze zelf studie leert u hoe u SmartRecruiters integreert met Azure Active Directory (Azure AD).
Het integreren van SmartRecruiters met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot SmartRecruiters.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij SmartRecruiters (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met SmartRecruiters wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement voor eenmalige aanmelding SmartRecruiters ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SmartRecruiters ondersteunt SSO die door **SP en IDP** is geïnitieerd

## <a name="adding-smartrecruiters-from-the-gallery"></a>SmartRecruiters toevoegen uit de galerie

Als u de integratie van SmartRecruiters in azure AD wilt configureren, moet u SmartRecruiters uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om SmartRecruiters toe te voegen uit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **SmartRecruiters**in het zoekvak, selecteer **SmartRecruiters** in het resultaten paneel en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

     ![SmartRecruiters in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met SmartRecruiters op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SmartRecruiters tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met SmartRecruiters, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[SmartRecruiters eenmalige aanmelding configureren](#configure-smartrecruiters-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een SmartRecruiters-test gebruiker](#create-smartrecruiters-test-user)** -om een equivalent van Julia Simon in SmartRecruiters te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met SmartRecruiters:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **SmartRecruiters** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Als u de toepassing in de gestarte modus van **IDP** wilt configureren, voert u de volgende stappen uit in de sectie **basis configuratie van SAML** :

    ![Informatie over eenmalige aanmelding voor SmartRecruiters domein en Url's](common/idp-intiated.png)

    a. Typ in het tekstvak **id** een URL met het volgende patroon:`https://www.smartrecruiters.com/web-sso/saml/<companyname>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://www.smartrecruiters.com/web-sso/saml/<companyname>/callback`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![Informatie over eenmalige aanmelding voor SmartRecruiters domein en Url's](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://www.smartrecruiters.com/web-sso/saml/<companyname>/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteunings team van SmartRecruiters-clients](https://www.smartrecruiters.com/about-us/contact-us/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. Kopieer op de sectie **SmartRecruiters instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-smartrecruiters-single-sign-on"></a>Eenmalige aanmelding voor SmartRecruiters configureren

1. Meld u in een ander webbrowser venster aan bij uw SmartRecruiters-bedrijfs site als beheerder.

1. Ga naar **instellingen/beheerder**.

    ![SmartRecruiters-configuratie](./media/smartrecruiters-tutorial/configure.png)

1. Klik in de sectie **configuratie** op **web-SSO**.

    ![SmartRecruiters-configuratie](./media/smartrecruiters-tutorial/configure1.png)

1. **Schakel web-SSO**in of uit.

    ![SmartRecruiters-configuratie](./media/smartrecruiters-tutorial/configure2.png)

1. Voer de volgende stappen uit in de configuratie van de **identiteits provider**:

    ![SmartRecruiters-configuratie](./media/smartrecruiters-tutorial/configure4.png)

    a. Plak de waarde van de **aanmeldings-URL** die u hebt gekopieerd van Azure Portal in de URL van de **ID-provider** .

    b. Open **certificaat (base64)** dat u hebt gedownload van Azure Portal in het klad blok, kopieer de inhoud ervan en plak het tekstvak van het **ID-provider certificaat** .

1. Klik op **web-SSO-configuratie opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon@yourcompanydomain.extension**.  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan SmartRecruiters.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **SmartRecruiters**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **SmartRecruiters**.

    ![De koppeling SmartRecruiters in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-smartrecruiters-test-user"></a>SmartRecruiters-test gebruiker maken

In deze sectie maakt u een gebruiker met de naam Julia Simon in SmartRecruiters. Werk samen met [SmartRecruiters-ondersteunings team](https://www.smartrecruiters.com/about-us/contact-us/) om de gebruikers toe te voegen in het SmartRecruiters-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SmartRecruiters in het toegangs venster klikt, moet u automatisch worden aangemeld bij de SmartRecruiters waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

