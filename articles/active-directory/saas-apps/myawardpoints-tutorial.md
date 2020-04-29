---
title: 'Zelf studie: integratie Azure Active Directory met mijn Award-punten bovenste sub/bovenste team | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en mijn toekennings punten boven-sub/beste team.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a7a08eed-7a6b-4a83-8f8e-0add6d2fb8cf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.openlocfilehash: b1de9e394dd1e6b4c47b8de3175dd1e8fba1d87e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "73161340"
---
# <a name="tutorial-azure-active-directory-integration-with-my-award-points-top-subtop-team"></a>Zelf studie: integratie Azure Active Directory met mijn toekennings punten bovenste sub/bovenste team

In deze zelf studie leert u hoe u mijn toekennings punten kunt integreren met Azure Active Directory (Azure AD).
Het integreren van mijn Award-punten in het bovenste sub/beste-team met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot mijn toekennings punten bovenste sub/top-team.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij mijn toekennings punten voor het bovenste sub/bovenste team (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om Azure AD-integratie met mijn toekennings punten te configureren voor het bovenste sub/bovenste team:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Mijn toekennings punten voor het bovenste sub/bovenste team abonnement voor eenmalige aanmelding

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Mijn Award Points top sub/top-team ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-my-award-points-top-subtop-team-from-the-gallery"></a>Het bovenste sub/bovenste team van de toewijzings punten toevoegen vanuit de galerie

Als u de integratie van mijn toekennings punten wilt configureren in azure AD, moet u het bovenste sub/bovenste team van de toewijzings punten toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit als u mijn toekennings punten wilt toevoegen aan het bovenste sub/bovenste team van de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **mijn toekennings punten bovenste sub/top-team**, selecteer **mijn toekennings punten bovenste sub/bovenste team** uit het paneel resultaten en klik vervolgens op **toevoegen** knop om de toepassing toe te voegen.

     ![Het bovenste sub/bovenste team van de Award-punten in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met mijn Award Points top-sub/beste team op basis van een test gebruiker met de naam **Julia Simon**.
Voor eenmalige aanmelding moet er een koppelings relatie tussen een Azure AD-gebruiker en de bijbehorende gebruiker in mijn toekennings punten voor het bovenste sub/bovenste team tot stand worden gebracht.

U moet de volgende bouw stenen volt ooien om de eenmalige aanmelding van Azure AD te configureren en te testen met mijn Award-punten in het bovenste sub/bovenste team:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **Mijn toekennings punten configureren eenmalige aanmelding voor het sub/bovenste team** -voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **Maak mijn toekennings punten boven-sub/bovenste team test gebruiker** -om een tegen hanger te hebben van Julia Simon in mijn toekennings punten aan het bovenste sub/bovenste team dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met mijn Award-punten in het bovenste sub/bovenste team:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **mijn toekennings punten bovenste sub/bovenste team** toepassings integratie de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Mijn toekennings punten boven-sub/bovenste team domein en Url's eenmalige aanmelding gegevens](common/sp-signonurl.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://microsoftrr.performnet.com/biwv1auth/Shibboleth.sso/Login?providerId=<Azure AD Identifier>`

    > [!NOTE]
    > De waarde is niet echt. U krijgt de `<Azure AD Identifier>` waarde in de volgende stappen in deze zelf studie.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer de gewenste URL ('s) volgens uw vereiste op de sectie **mijn toekennings punten instellen voor het bovenste sub/bovenste team** . 

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

    >[!NOTE]
    >Voeg de gekopieerde id-waarde van Azure AD toe met de aanmeldings-URL `<Azure AD Identifier>` in de plaats van in het gedeelte **basis configuratie van SAML** in het Azure Portal.

### <a name="configure-my-award-points-top-subtop-team-single-sign-on"></a>Mijn toekennings punten configureren eenmalige sub/bovenste team-eenmalige aanmelding

Voor het configureren van eenmalige aanmelding op **mijn toekennings punten aan de bovenkant van het sub/top-team** , moet u het gedownloade **XML-bestand met federatieve meta gegevens** en de juiste gekopieerde url's verzenden van Azure Portal naar [mijn toekennings punten bovenste sub/top team ondersteunings team](mailto:myawardpoints@biworldwide.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam****Britta Simon**in.
  
    b. Typ in het veld **gebruikers naam** **brittasimon\@yourcompanydomain. extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om gebruik te maken van eenmalige aanmelding van Azure door toegang te verlenen tot mijn bekroonde punten voor het bovenste en bovenste team.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **mijn toekennings punten bovenste sub/bovenste team**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen **mijn toekennings punten bovenste sub/bovenste team**.

    ![De koppeling voor het bovenste sub/bovenste team van mijn toewijzings punten in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-my-award-points-top-subtop-team-test-user"></a>Mijn toekennings punten maken bovenste sub/bovenste team test gebruiker

In deze sectie maakt u een gebruiker met de naam Julia Simon in mijn Award Points top sub/top team. Werk met [mijn bekroonde punten het bovenste sub/bovenste team ondersteunings team](mailto:myawardpoints@biworldwide.com) om de gebruikers toe te voegen aan het bovenste sub/bovenste team platform van mijn Award-punten. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het deel venster toegang op de tegel voor het bovenste sub/bovenste team van mijn toewijzings punten klikt, moet u automatisch worden aangemeld bij het bovenste sub/bovenste team van mijn toekennings punten waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
