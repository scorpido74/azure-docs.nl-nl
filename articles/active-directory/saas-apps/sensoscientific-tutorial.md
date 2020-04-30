---
title: 'Zelf studie: integratie Azure Active Directory met SensoScientific Wireless-temperatuur bewakings systeem | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SensoScientific draadloze temperatuur bewakings systeem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee9a924d-ccde-45b0-ab40-877f82f5dfa2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea2f5e33859852388357526052c39fa432471efb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67091273"
---
# <a name="tutorial-azure-active-directory-integration-with-sensoscientific-wireless-temperature-monitoring-system"></a>Zelf studie: integratie Azure Active Directory met SensoScientific Wireless-temperatuur bewakings systeem

In deze zelf studie leert u hoe u een SensoScientific voor draadloze temperatuur bewaking integreert met Azure Active Directory (Azure AD).
Het integreren van SensoScientific Wireless-temperatuur bewakings systeem met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot SensoScientific Wireless-temperatuur bewakings systeem.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld voor SensoScientific draadloze temperatuur bewaking systeem (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met SensoScientific Wireless-temperatuur bewakings systeem hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* Eenmalige aanmelding van het SensoScientific-abonnement voor draadloze Tempe ratuur controleren

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SensoScientific Wireless-temperatuur bewakings systeem ondersteunt door **IDP** GEÏNITIEERDe SSO

## <a name="adding-sensoscientific-wireless-temperature-monitoring-system-from-the-gallery"></a>Een SensoScientific voor draadloze temperatuur bewaking toevoegen vanuit de galerie

Als u de integratie van SensoScientific Wireless-temperatuur bewakings systeem wilt configureren in azure AD, moet u SensoScientific draadloze temperatuur bewakings systeem van de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om het systeem voor draadloze temperatuur bewaking van SensoScientific toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. In het zoekvak typt u **SensoScientific Wireless-temperatuur bewaking systeem**, selecteert u **SensoScientific draadloze temperatuur bewaking systeem** van resultaat paneel en klikt u vervolgens op **toevoegen** knop om de toepassing toe te voegen.

    ![SensoScientific draadloze temperatuur bewakings systeem in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met het SensoScientific Wireless-temperatuur bewakings systeem op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in het SensoScientific Wireless-temperatuur bewakings systeem tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met het SensoScientific Wireless-temperatuur bewakings systeem, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding van SensoScientific draadloos controle systeem configureren](#configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. Maak een SensoScientific voor de controle van de **[test gebruiker van een draadloos systeem](#create-sensoscientific-wireless-temperature-monitoring-system-test-user)** , om een soort tegen te brengen van Julia Simon in SensoScientific Wireless-temperatuur bewakings systeem dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met SensoScientific Wireless-temperatuur bewaking:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **SensoScientific draadloze Tempe ratuur bewaking** van de systeem toepassing de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **SAML-basisconfiguratie** hoeft de gebruiker geen enkele stap uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

    ![SensoScientific draadloze temperatuur bewaking systeem domein en Url's eenmalige aanmelding gegevens](common/preintegrated.png)

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer de gewenste URL ('s) op basis van uw vereiste in het gedeelte **SensoScientific draadloze temperatuur bewaking instellen** .

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-sensoscientific-wireless-temperature-monitoring-system-single-sign-on"></a>Eenmalige aanmelding van SensoScientific draadloos controle systeem configureren

1. Meld u aan bij uw SensoScientific Wireless-temperatuur bewaking systeem toepassing als beheerder.

1. Klik in het navigatie menu aan de bovenkant op **configuratie** en ga naar **configureren** onder **eenmalige aanmelding** om de instellingen voor eenmalige aanmelding te openen en de volgende stappen uit te voeren:

    ![Eenmalige aanmelding configureren](./media/sensoscientific-tutorial/tutorial_sensoscientificwtms_admin.png)

    a. Selecteer de naam van de **verlener** als Azure AD.

    b. Plak in het tekstvak **URL van uitgever** de **Azure ad-id** die u van Azure Portal hebt gekopieerd.

    c. Plak in het tekstvak **URL voor eenmalige aanmelding** de AANMELDINGS- **URL** die u van Azure Portal hebt gekopieerd.

    d. Plak de **Afmeldings-URL** die u hebt gekopieerd uit Azure Portal in het tekstvak **Single Sign-out service-URL** .

    e. Blader door het certificaat dat u hebt gedownload van Azure Portal en Upload hier.

    f. Klik op **Opslaan**.

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

In deze sectie schakelt u Julia Simon in om gebruik te maken van eenmalige aanmelding van Azure door toegang te verlenen tot het systeem voor draadloze temperatuur bewaking van SensoScientific.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer **SensoScientific Wireless-temperatuur bewakings systeem**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen het **SensoScientific-systeem voor draadloze temperatuur bewaking**.

    ![De SensoScientific draadloze temperatuur bewakings systeem koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-sensoscientific-wireless-temperature-monitoring-system-test-user"></a>Test gebruiker voor SensoScientific Wireless-temperatuur bewaking maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij het SensoScientific Wireless-temperatuur bewakings systeem, moeten ze worden ingericht in het SensoScientific voor draadloze temperatuur bewaking. Werk met de [SensoScientific Wireless-temperatuur bewaking systeem ondersteunings team](https://www.sensoscientific.com/contact-us/) om de gebruikers toe te voegen aan het systeem platform voor de SensoScientific voor draadloze temperatuur bewaking. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel systeem SensoScientific draadloze temperatuur bewaking in het toegangs venster klikt, moet u automatisch worden aangemeld bij het SensoScientific Wireless-temperatuur bewakings systeem waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

