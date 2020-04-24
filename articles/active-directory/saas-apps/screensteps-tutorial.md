---
title: 'Zelf studie: integratie Azure Active Directory met ScreenSteps | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en ScreenSteps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4563fe94-a88f-4895-a07f-79df44889cf9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 864a9243a9f737506fd4d8cbc3940d7a86711f20
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67091654"
---
# <a name="tutorial-azure-active-directory-integration-with-screensteps"></a>Zelf studie: integratie Azure Active Directory met ScreenSteps

In deze zelf studie leert u hoe u ScreenSteps integreert met Azure Active Directory (Azure AD).
Het integreren van ScreenSteps met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot ScreenSteps.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij ScreenSteps (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met ScreenSteps wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement voor eenmalige aanmelding ScreenSteps ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* ScreenSteps ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-screensteps-from-the-gallery"></a>ScreenSteps toevoegen uit de galerie

Als u de integratie van ScreenSteps in azure AD wilt configureren, moet u ScreenSteps uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om ScreenSteps toe te voegen uit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **ScreenSteps**in het zoekvak, selecteer **ScreenSteps** in het resultaten paneel en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

     ![ScreenSteps in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met ScreenSteps op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in ScreenSteps tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met ScreenSteps, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[ScreenSteps eenmalige aanmelding configureren](#configure-screensteps-single-sign-on)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Maak een ScreenSteps-test gebruiker](#create-screensteps-test-user)** -om een equivalent van Julia Simon in ScreenSteps te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met ScreenSteps:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **ScreenSteps** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor ScreenSteps domein en Url's](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<tenantname>.ScreenSteps.com`

    > [!NOTE]
    > Deze waarde is niet echt. Werk deze waarde bij met de werkelijke aanmeldings-URL, die later in deze zelf studie wordt uitgelegd.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Kopieer op de sectie **ScreenSteps instellen** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-screensteps-single-sign-on"></a>Eenmalige aanmelding voor ScreenSteps configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij de ScreenSteps-bedrijfs site.

1. Klik op **account instellingen**.

    ![Account beheer](./media/screensteps-tutorial/ic778523.png "Accountbeheer")

1. Klik op **eenmalige aanmelding**.

    ![Externe verificatie](./media/screensteps-tutorial/ic778524.png "Externe verificatie")

1. Klik op **eind punt eenmalige aanmelding maken**.

    ![Externe verificatie](./media/screensteps-tutorial/ic778525.png "Externe verificatie")

1. Voer de volgende stappen uit in de sectie **eenmalige aanmelding maken** :

    ![Een verificatie-eind punt maken](./media/screensteps-tutorial/ic778526.png "Een verificatie-eind punt maken")

    a. Typ een titel in het tekstvak **titel** .

    b. Selecteer in de **modus** lijst de optie **SAML**.

    c. Klik op **maken**.

1. **Bewerk** het nieuwe eind punt.

    ![Eind punt bewerken](./media/screensteps-tutorial/ic778528.png "Eind punt bewerken")

1. Voer de volgende stappen uit in de sectie **eenmalige aanmelding bewerken** :

    ![Eind punt voor externe verificatie](./media/screensteps-tutorial/ic778527.png "Eind punt voor externe verificatie")

    a. Klik op **Nieuw SAML-certificaat bestand uploaden**en upload het certificaat dat u hebt gedownload van Azure Portal.

    b. Plak de waarde van de **aanmeldings-URL** , die u hebt gekopieerd van de Azure Portal naar het tekstvak **externe aanmeldings-URL** .

    c. Plak URL-waarde voor **Afmelden** , die u hebt gekopieerd van de Azure Portal naar het tekstvak voor de afmeldings- **URL** .

    d. Selecteer een **groep** waaraan u gebruikers wilt toewijzen wanneer ze worden ingericht.

    e. Klik op **Update**.

    f. Kopieer de **URL** van de SAML-consument naar het klem bord en plak deze in het tekstvak **aanmeldings-URL** in het gedeelte **basis configuratie van SAML** in de Azure Portal.

    g. Ga terug naar het **eind punt voor eenmalige aanmelding bewerken**.

    h. Klik op de knop **standaard voor account maken** om dit eind punt te gebruiken voor alle gebruikers die zich aanmelden bij ScreenSteps. U kunt ook op de knop **toevoegen aan site** klikken om dit eind punt te gebruiken voor specifieke sites in **ScreenSteps**.

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

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan ScreenSteps.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **ScreenSteps**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **ScreenSteps**.

    ![De koppeling ScreenSteps in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-screensteps-test-user"></a>ScreenSteps-test gebruiker maken

In deze sectie maakt u een gebruiker met de naam Julia Simon in ScreenSteps. Werk samen met het [ScreenSteps-client ondersteunings team](https://www.screensteps.com/contact) om de gebruikers toe te voegen in het ScreenSteps-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel ScreenSteps in het toegangs venster klikt, moet u automatisch worden aangemeld bij de ScreenSteps waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)