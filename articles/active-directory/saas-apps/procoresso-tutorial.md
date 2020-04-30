---
title: 'Zelf studie: integratie Azure Active Directory met procore-SSO | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en procore SSO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9818edd3-48c0-411d-b05a-3ec805eafb2e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ca6863a6b02e867afd732ce1662136051b8afec8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67093668"
---
# <a name="tutorial-azure-active-directory-integration-with-procore-sso"></a>Zelf studie: integratie met procore-SSO Azure Active Directory

In deze zelf studie leert u hoe u procore-SSO integreert met Azure Active Directory (Azure AD).
Het integreren van procore-SSO met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot procore SSO.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij procore-SSO (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met procore SSO wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* Abonnement voor eenmalige aanmelding bij procore SSO ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Procore SSO ondersteunt **IDP** GEÏNITIEERDe SSO

## <a name="adding-procore-sso-from-the-gallery"></a>Een procore-SSO toevoegen vanuit de galerie

Als u de integratie van procore-SSO wilt configureren in azure AD, moet u procore-SSO van de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om procore-SSO van de galerie toe te voegen:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **procore SSO**, selecteer **procore-SSO** in het paneel resultaten en klik vervolgens op **toevoegen** knop om de toepassing toe te voegen.

    ![SSO van procore in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met procore SSO op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in procore-SSO tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met procore SSO, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Single Core SSO](#configure-procore-sso-single-sign-on)** -eenmalige aanmelding configureren: Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een procore-SSO-test gebruiker](#create-procore-sso-test-user)** -om een equivalent van Julia Simon in procore-SSO te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD met procore SSO te configureren:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **procore SSO** -toepassings integratie de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **SAML-basisconfiguratie** hoeft de gebruiker geen enkele stap uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

    ![Informatie over eenmalige aanmelding voor SSO-domeinen en Url's voor procores](common/preintegrated.png)

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer de gewenste URL ('s) volgens uw vereiste in de sectie **SSO van procore instellen** .

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-procore-sso-single-sign-on"></a>Eenmalige aanmelding voor procore SSO configureren

1. Als u eenmalige aanmelding wilt configureren op **procore SSO** -zijde, meldt u zich aan bij uw procore-bedrijfs site als beheerder.

2. Klik in de vervolg keuzelijst werkset op **beheerder** om de pagina SSO-instellingen te openen.

    ![Eenmalige aanmelding configureren](./media/procoresso-tutorial/procore_tool_admin.png)

3. Plak de waarden in de vakken zoals hieronder wordt beschreven:

    ![Eenmalige aanmelding configureren](./media/procoresso-tutorial/procore_setting_admin.png)  

    a. Plak in het tekstvak **URL voor eenmalige aanmelding** de waarde van de **Azure ad-id** die u hebt gekopieerd uit de Azure Portal.

    b. Plak in het dialoog venster **doel-URL voor SAML** - **aanmeldingen** de waarde van de aanmeldings-URL die u hebt gekopieerd van de Azure Portal.

    c. Open nu de hierboven gedownloade **XML voor federatieve meta gegevens** uit het Azure Portal en kopieer het certificaat in de tag met de naam **X509Certificate**. Plak de gekopieerde waarde in het vak **eenmalige aanmelding x509-certificaat** .

4. Klik op **Save Changes** (Wijzigingen opslaan).

5. Na deze instellingen moet u de **domein naam** (bijvoorbeeld **contoso.com**) verzenden waarmee u zich aanmeldt bij procore naar het [procore-ondersteunings team](https://support.procore.com/) en de federatieve SSO voor dat domein wordt geactiveerd.

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

In deze sectie schakelt u Julia Simon in om gebruik te maken van eenmalige aanmelding van Azure door toegang te verlenen tot procore-SSO.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **procore SSO**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen **procore SSO**.

    ![De SSO-koppeling voor procore in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-procore-sso-test-user"></a>Procore-SSO-test gebruiker maken

Volg de onderstaande stappen om een procore-test gebruiker te maken op procore SSO-zijde.

1. Meld u als beheerder aan bij uw procore-bedrijfs site.    

2. Klik in de vervolg keuzelijst werkset op **Directory** om de pagina bedrijfs adreslijst te openen.

    ![Eenmalige aanmelding configureren](./media/procoresso-tutorial/Procore_sso_directory.png)

3. Klik op **een persoon toevoegen** optie om het formulier te openen en voer de volgende opties in:

    ![Eenmalige aanmelding configureren](./media/procoresso-tutorial/Procore_user_add.png)

    a. In het tekstvak **voor naam** typt u de voor naam van de gebruiker, zoals **Julia**.

    b. Typ in het tekstvak **Achternaam** de achternaam van de gebruiker, zoals **Simon**.

    c. Typ in het tekstvak **e-mail adres** het e-mail BrittaSimon@contoso.comadres van de gebruiker, zoals.

    d. Selecteer de machtigings **sjabloon** **op een later tijdstip**.

    e. Klik op **maken**.

4. Controleer de details van de toegevoegde contact persoon en werk deze bij.

    ![Eenmalige aanmelding configureren](./media/procoresso-tutorial/Procore_user_check.png)

5. Klik op **opslaan en uitnodiging verzenden** (als een uitnodiging via e-mail is vereist) of **Sla** (rechtstreeks opslaan) op om de registratie van de gebruiker te volt ooien.
    
    ![Eenmalige aanmelding configureren](./media/procoresso-tutorial/Procore_user_save.png)

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel procore SSO in het toegangs venster klikt, moet u automatisch worden aangemeld bij de procore-SSO waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

