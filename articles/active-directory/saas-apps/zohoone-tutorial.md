---
title: 'Zelf studie: integratie Azure Active Directory met Zoho One | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Zoho.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bbc3038c-0d8b-45dd-9645-368bd3d01a0f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: jeedes
ms.openlocfilehash: 0a37789e7c7efeb71770ff0e8061d57e6603b6c4
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67086238"
---
# <a name="tutorial-azure-active-directory-integration-with-zoho-one"></a>Zelf studie: integratie Azure Active Directory met Zoho One

In deze zelf studie leert u hoe u Zoho kunt integreren met Azure Active Directory (Azure AD).
Het integreren van Zoho met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot Zoho.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld voor Zoho één (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Zoho één wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* Zoho één abonnement ingeschakeld voor eenmalige aanmelding

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Zoho One ondersteunt door **SP** en **IDP** geïnitieerde SSO

## <a name="adding-zoho-one-from-the-gallery"></a>Zoho één toevoegen uit de galerie

Als u de integratie van Zoho in azure AD wilt configureren, moet u Zoho een uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Zoho één van de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. In het zoekvak typt u **Zoho One**, selecteert u **Zoho One** van result panel en klikt u vervolgens op knop **toevoegen** om de toepassing toe te voegen.

     ![Zoho één in de resultaten lijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Zoho één op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Zoho tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met Zoho One, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Zoho eenmalige aanmelding configureren](#configure-zoho-one-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak Zoho één test gebruiker](#create-zoho-one-test-user)** -als u een equivalent van Julia Simon wilt hebben in Zoho een item dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met Zoho:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **Zoho één** toepassings integratie de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Als u de toepassing in de gestarte modus van **IDP** wilt configureren, voert u de volgende stappen uit in de sectie **basis configuratie van SAML** :

    ![Zoho Eén domein en Url's eenmalige aanmelding](common/idp-relay.png)

    a. Typ een URL in het tekstvak **id** :`one.zoho.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://accounts.zoho.com/samlresponse/<saml-identifier>`

    > [!NOTE]
    > De waarde van de voor gaande **antwoord-URL** is niet real. U krijgt de `<saml-identifier>` waarde van #step4 van **Zoho één enkele aanmelding configureren** , die verderop in de zelf studie wordt beschreven.

    c. Klik op **Extra URL's instellen**.

    d. In het tekstvak **Relaystatus** typt u een URL: `https://one.zoho.com`

5. Als u de toepassing in de modus door **SP** gestart wilt configureren, voert u de volgende stap uit:


    ![Zoho Eén domein en Url's eenmalige aanmelding](common/both-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://accounts.zoho.com/samlauthrequest/<domain_name>?serviceurl=https://one.zoho.com` 

    > [!NOTE] 
    > De waarde van de voor gaande **aanmeldings-URL** is niet echt. U werkt de waarde bij met de werkelijke aanmeldings-URL uit de sectie **Zoho één eenmalige aanmelding configureren** , die verderop in de zelf studie wordt beschreven. 

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. Kopieer de gewenste URL ('s) volgens uw vereiste in het gedeelte **Stel Zoho één** in.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-zoho-one-single-sign-on"></a>Zoho eenmalige aanmelding configureren

1. Meld u in een ander webbrowser venster aan bij uw Zoho één bedrijfs site als beheerder.

2. Klik op het tabblad **organisatie** op **instellen** onder **SAML-verificatie**.

    ![Zoho één org](./media/zohoone-tutorial/tutorial_zohoone_setup.png)

3. Voer de volgende stappen uit op de pop-uppagina:

    ![Zoho één sig](./media/zohoone-tutorial/tutorial_zohoone_save.png)

    a. Plak in het tekstvak **URL** voor aanmelding de waarde van de **aanmeldings-URL**die u van Azure Portal hebt gekopieerd.

    b. Plak in het tekstvak voor de **Afmeldings-URL** de waarde van de **afmeldings-URL**, die u van Azure Portal hebt gekopieerd.

    c. Klik op **Bladeren** om het **certificaat (base64)** te uploaden dat u hebt gedownload van Azure Portal.

    d. Klik op **Opslaan**.

4. Nadat u de SAML-verificatie-instellingen hebt opgeslagen, kopieert u de waarde voor de **SAML-id** en voegt u `<saml-identifier>`deze toe `https://accounts.zoho.com/samlresponse/one.zoho.com` met de **antwoord-URL** in plaats van, zoals en plakt u de gegenereerde waarde in het tekstvak **antwoord-URL** onder **basis configuratie** van de SAML.

    ![Zoho één SAML](./media/zohoone-tutorial/tutorial_zohoone_samlidenti.png)

5. Ga naar het tabblad **domeinen** en klik vervolgens op **domein toevoegen**.

    ![Zoho één domein](./media/zohoone-tutorial/tutorial_zohoone_domain.png)

6. Voer de volgende stappen uit op de pagina **domein toevoegen** :

    ![Zoho één domein toevoegen](./media/zohoone-tutorial/tutorial_zohoone_adddomain.png)

    a. In het tekstvak **domein naam** typt u domein, zoals contoso.com.

    b. Klik op **Add**.

    >[!Note]
    >Nadat u het domein hebt toegevoegd, volgt u [deze](https://www.zoho.com/one/help/admin-guide/domain-verification.html) stappen om uw domein te verifiëren. Nadat het domein is geverifieerd, gebruikt u de domein naam in de **URL voor aanmelden** in de sectie **basis configuratie van SAML** in azure Portal.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. Typ `brittasimon@yourcompanydomain.extension`in het veld **gebruikers naam** . Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Zoho One.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **Zoho One**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Zoho One**.

    ![De Zoho één koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-zoho-one-test-user"></a>Zoho één test gebruiker maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij Zoho, moeten ze worden ingericht in Zoho One. In Zoho wordt een hand matige taak ingericht.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij Zoho met een beveiligings beheerder.

2. Klik op het tabblad **gebruikers** op **gebruikers logo**.

    ![Eén gebruiker Zoho](./media/zohoone-tutorial/tutorial_zohoone_users.png)

3. Voer op de pagina **gebruiker toevoegen** de volgende stappen uit:

    ![Zoho één gebruiker toevoegen](./media/zohoone-tutorial/tutorial_zohoone_adduser.png)
    
    a. Voer in het tekstvak **naam** de naam van de gebruiker in, zoals **Julia Simon**.
    
    b. Voer in het tekstvak **Email Address** het e-mailadres van de gebruiker in, zoals brittasimon@contoso.com.

    >[!Note]
    >Selecteer uw geverifieerde domein in de lijst domein.

    c. Klik op **Add**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de Zoho één tegel in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Zoho waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

