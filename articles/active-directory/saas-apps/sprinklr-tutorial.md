---
title: 'Zelf studie: integratie Azure Active Directory met sprinkler | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en sprinkler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 9e4025d040783bff1cd85fb46d571e3a89967892
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67089647"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Zelf studie: integratie met de sprinkler Azure Active Directory

In deze zelf studie leert u hoe u sprinkler kunt integreren met Azure Active Directory (Azure AD).
Het integreren van de sprinkler met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot de sprinkler.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij een sprinkler (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met de sprinkler wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Eenmalige aanmelding voor de sprinkler functie ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Sprinkler ondersteuning voor door **SP** GEÏNITIEERDe SSO

## <a name="adding-sprinklr-from-the-gallery"></a>Sprinkler toevoegen vanuit de galerie

Als u de integratie van de sprinkler wilt configureren in azure AD, moet u een sprinkler van de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u een sprinkler wilt toevoegen vanuit de galerie, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak de tekst **sprinkler**, selecteer **sprinkler** uit het paneel resultaten en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

     ![Sprinkler in de resultaten lijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met sprinkler op basis van een test gebruiker met de naam **Julia Simon**.
Voor de werking van eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in de sprinkler tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met de sprinkler, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Eenmalige aanmelding voor de sprinkler configuratie configureren](#configure-sprinklr-single-sign-on)** voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. De gebruiker van de **[sprinkler test maken](#create-sprinklr-test-user)** : een tegen hanger van Julia Simon in de sprinkler dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met de sprinkler:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **sprinkler** toepassings integratie de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor het sprinkler domein en Url's](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<subdomain>.sprinklr.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<subdomain>.sprinklr.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met het [ondersteunings team van de sprinkler client](https://www.sprinklr.com/contact-us/) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Op de sectie **sprinkler instellen** kopieert u de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-sprinklr-single-sign-on"></a>Eenmalige aanmelding voor de sprinkler configuratie configureren

1. Meld u in een ander webbrowser venster aan bij uw sprinkler site als beheerder.

1. Ga naar **beheer \> instellingen**.

    ![Beheer](./media/sprinklr-tutorial/ic782907.png "Beheer")

1. Ga naar **beheer partner \> eenmalige aanmelding** in het linkerdeel venster.

    ![Partner beheren](./media/sprinklr-tutorial/ic782908.png "Partner beheren")

1. Klik op **+ afzonderlijke aanmeldings onderdelen toevoegen**.

    ![Eenmalige aanmeldingen](./media/sprinklr-tutorial/ic782909.png "Eenmalige aanmeldingen")

1. Voer op de pagina **eenmalige aanmelding** de volgende stappen uit:

    ![Eenmalige aanmeldingen](./media/sprinklr-tutorial/ic782910.png "Eenmalige aanmeldingen")

    a. Typ in het tekstvak **naam** een naam voor uw configuratie (bijvoorbeeld: *WAADSSOTest*).

    b. Selecteer **Ingeschakeld**.

    c. Selecteer **Nieuw SSO-certificaat gebruiken**.

    d. Open uw met base 64 versleutelde certificaat in Klad blok, kopieer de inhoud ervan naar het klem bord en plak het in het tekstvak **ID-provider certificaat** .

    e. Plak de id-waarde van **Azure AD** die u hebt gekopieerd uit Azure Portal naar het tekstvak **Entiteits-ID** .

    f. Plak de waarde voor de **aanmeldings-URL** die u hebt gekopieerd uit Azure Portal naar het tekstvak **URL-provider aanmeld-id** .

    g. Plak de waarde voor de **Afmeldings-URL** die u hebt gekopieerd uit Azure Portal naar het tekstvak **id-afmeldings-URL** .

    h. Selecteer bewering als **SAML-gebruikers-ID type** **bevat de gebruikers naam van de gebruiker sprinklr.com**.

    i. Selecteer gebruikers-ID als locatie van de **gebruikers-id voor SAML** **in het element naam identifier van de instructie subject**.

    j. Klik op **Opslaan**.

    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

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

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de sprinkler.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **sprinkler**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **sprinkler**.

    ![De sprinkler koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-sprinklr-test-user"></a>Gebruiker van sprinkler test maken

1. Meld u als beheerder aan bij de site van uw sprinkler bedrijf.

1. Ga naar **beheer \> instellingen**.

    ![Beheer](./media/sprinklr-tutorial/ic782907.png "Beheer")

1. Ga naar **client \> gebruikers beheren** vanuit het linkerdeel venster.

    ![Instellingen](./media/sprinklr-tutorial/ic782914.png "Instellingen")

1. Klik op **gebruiker toevoegen**.

    ![Instellingen](./media/sprinklr-tutorial/ic782915.png "Instellingen")

1. Voer de volgende stappen uit in het dialoog venster **gebruiker bewerken** :

    ![Gebruiker bewerken](./media/sprinklr-tutorial/ic782916.png "Gebruiker bewerken")

    a. Typ in het tekstvak **e-mail**, **voor naam** en **Achternaam** de informatie van een Azure AD-gebruikers account die u wilt inrichten.

    b. Selecteer **wacht woord uitgeschakeld**.

    c. Selecteer een **taal**.

    d. Selecteer een **gebruikers type**.

    e. Klik op **Update**.

    > [!IMPORTANT]
    > Als u een **wacht woord** wilt inschakelen, moet een gebruiker zich aanmelden via een id-provider. 

1. Ga naar **rol**en voer de volgende stappen uit:

    ![Partner rollen](./media/sprinklr-tutorial/ic782917.png "Partner rollen")

    a. Selecteer in de lijst **globaal** de optie **ALL_Permissions**.  

    b. Klik op **Update**.

> [!NOTE]
> U kunt alle andere hulpprogram ma's voor het maken van een sprinkler gebruikers account of Api's die worden geleverd door de sprinkler gebruiken om Azure AD-gebruikers accounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de sprinkler tegel in het toegangs venster klikt, moet u automatisch worden aangemeld bij de sprinkler waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
