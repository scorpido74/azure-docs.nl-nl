---
title: 'Zelf studie: integratie Azure Active Directory met LockPath-lampje | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en LockPath-lampje.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 234a32f1-9f56-4650-9e31-7b38ad734b1a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 148c2c46a911088d01ab83fe2d16e8ca81d272ff
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67098775"
---
# <a name="tutorial-azure-active-directory-integration-with-lockpath-keylight"></a>Zelf studie: integratie Azure Active Directory met LockPath-lampje

In deze zelf studie leert u hoe u LockPath-Lighting integreert met Azure Active Directory (Azure AD).
Het integreren van LockPath-basis met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot LockPath-superlicht.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij LockPath (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met LockPath-lampje, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) aanvragen
* LockPath-abonnement met eenmalige aanmelding

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* LockPath-superlampje ondersteunt door **SP** GEÏNITIEERDe SSO
* LockPath-superlampje ondersteunt **just-in-time** -gebruikers inrichting

## <a name="adding-lockpath-keylight-from-the-gallery"></a>LockPath-Sublicht toevoegen vanuit de galerie

Als u de integratie van LockPath-LockPath wilt configureren in azure AD, moet u-van de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om LockPath-Sublicht toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **LockPath**- **LockPath** in het zoekvak en klik op de knop **toevoegen** om de toepassing toe te voegen.

    ![LockPath-Sublicht in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met LockPath-toetsen bord op basis van een test gebruiker met de naam **Julia Simon**.
Voor de werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in LockPath-hoofd licht worden gemaakt.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met LockPath-toetsen bord, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding van LockPath configureren](#configure-lockpath-keylight-single-sign-on)** om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een LockPath-test gebruiker](#create-lockpath-keylight-test-user)** voor het maken van een equivalent van Julia Simon in LockPath-lampje dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met LockPath-lampje:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **LockPath** -toepassing voor de integratie van het programma voor het inschakelen van **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor LockPath-domein en Url's](common/sp-identifier-reply.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<company name>.keylightgrc.com/`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<company name>.keylightgrc.com`

    c. In het tekstvak **Antwoord-URL** typt u een URL met behulp van het volgende patroon: `https://<company name>.keylightgrc.com/Login.aspx`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, id en antwoord-URL. Neem contact op met het [ondersteunings team van LockPath-client](https://www.lockpath.com/contact/) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Raw)** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificateraw.png)

6. Kopieer op de sectie LockPath-hoofd **lampje instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-lockpath-keylight-single-sign-on"></a>Eenmalige aanmelding van LockPath configureren

1. Voer de volgende stappen uit om SSO in te scha kelen in LockPath-lampje:

    a. Meld u aan bij uw LockPath-account als Administrator.

    b. Klik in het menu aan de bovenkant op **persoon**en selecteer vervolgens de optie voor het instellen van een **licht lampje**.

    ![Eenmalige aanmelding configureren](./media/keylight-tutorial/401.png)

    c. Klik in de structuur weergave aan de linkerkant op **SAML**.

    ![Eenmalige aanmelding configureren](./media/keylight-tutorial/402.png)

    d. Klik in het dialoog venster **SAML-instellingen** op **bewerken**.

    ![Eenmalige aanmelding configureren](./media/keylight-tutorial/404.png)

1. Voer de volgende stappen uit op de pagina **SAML-instellingen bewerken** :

    ![Eenmalige aanmelding configureren](./media/keylight-tutorial/405.png)

    a. Stel **SAML-verificatie** in op **actief**.

    b. Plak in het tekstvak aanmeldings-URL van de **ID-provider** de waarde voor de AANMELDINGS- **URL** die u hebt gekopieerd uit de Azure Portal.

    c. Plak in het tekstvak voor de **Afmeldings-URL van de identiteits provider** de waarde voor de **afmeldings-URL** die u hebt gekopieerd uit de Azure Portal.

    d. Klik op **bestand kiezen** om het gedownloade LockPath-basis certificaat te selecteren en klik vervolgens op **openen** om het certificaat te uploaden.

    e. Stel de locatie van de **SAML-gebruikers-id** in op **het NameIdentifier-element van de instructie subject**.

    f. Geef de **service provider voor de licht gewicht** op met behulp van het volgende patroon: `https://<CompanyName>.keylightgrc.com`.

    g. **Gebruikers automatisch inrichten** instellen op **actief**.

    h. Stel het **account type voor automatische inrichting** in op **volledige gebruiker**.

    i. Stel de **beveiligingsrol automatische inrichting**in, selecteer **standaard gebruiker met SAML**.

    j. Stel de **beveiligings configuratie voor automatische inrichting**in en selecteer **standaard gebruikers configuratie**.

    k. Typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`in het tekstvak **e-mail kenmerk** .

    l. Typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`in het tekstvak **voor het voor naam kenmerk** .

    m. Typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`in het tekstvak **Achternaam kenmerk** .

    n. Klik op **Opslaan**.

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

In deze sectie schakelt u Julia Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan LockPath-hoofd lampje.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens LockPath-basis **licht**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **LockPath-lampje**in de lijst toepassingen.

    ![De LockPath-superlampje-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-lockpath-keylight-test-user"></a>LockPath-test gebruiker maken

In deze sectie wordt een gebruiker met de naam Julia Simon gemaakt in LockPath-Light. LockPath-superlampje ondersteunt just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker niet al aanwezig is in LockPath-lampje, wordt er na verificatie een nieuwe gemaakt. Als u hand matig een gebruiker moet maken, moet u contact opnemen met het [ondersteunings team voor LockPath-client ondersteuning](https://www.lockpath.com/contact/).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel LockPath-lampje in het toegangs venster klikt, moet u automatisch worden aangemeld bij het LockPath-lampje waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)