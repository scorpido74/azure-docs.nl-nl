---
title: 'Zelf studie: integratie met organigram Azure Active Directory nu | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en het organigram.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 50a1522f-81de-4d14-9b6b-dd27bb1338a4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: b96606b5558e0fbb81733b2f548a89bfb38d5f99
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67095436"
---
# <a name="tutorial-azure-active-directory-integration-with-orgchart-now"></a>Zelf studie: integratie met organigram Azure Active Directory nu

In deze zelf studie leert u hoe u het organigram nu kunt integreren met Azure Active Directory (Azure AD).
Met Azure AD kunt u het organigram nu integreren met de volgende voor delen:

* U kunt beheren in azure AD die nu toegang tot het organigram heeft.
* U kunt ervoor zorgen dat uw gebruikers zich nu automatisch kunnen aanmelden bij het organigram (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie nu met organigram wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Abonnement voor nu eenmalige aanmelding op organigram

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Het organigram ondersteunt nu **SP** en **IDP** geïnitieerde SSO

## <a name="adding-orgchart-now-from-the-gallery"></a>Het organigram nu toevoegen vanuit de galerie

Als u de integratie van het organigram nu wilt configureren in azure AD, moet u nu een organigram toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

**Als u nu een organigram wilt toevoegen vanuit de galerie, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **organigram nu**, selecteer **nu organigram** in resultaat paneel en klik vervolgens op knop **toevoegen** om de toepassing toe te voegen.

     ![Organigram nu in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD met het organigram nu configureren en testen op basis van een test gebruiker met de naam **Julia Simon**.
Voor een goede werking van eenmalige aanmelding moet er nu een koppelings relatie tussen een Azure AD-gebruiker en de gerelateerde gebruiker in het organigram tot stand worden gebracht.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met het organigram, moet u de volgende bouw stenen volt ooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)**: als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer nu het organigram eenmalige aanmelding](#configure-orgchart-now-single-sign-on)** -voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)**: als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)**: als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Nu organigram maken test gebruiker](#create-orgchart-now-test-user)** : als u nu een equivalent van Julia Simon in het organigram wilt hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)**: als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om de eenmalige aanmelding voor Azure AD te configureren met het organigram:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **nu** op het organigram toepassings integratie de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit als u de toepassing in de door **IDP** geïnitieerde modus wilt configureren:

    ![Domein en Url's voor nu op het organigram gegevens van eenmalige aanmelding](common/idp-identifier.png)

    In het tekstvak **Id** typt u een URL: `https://sso2.orgchartnow.com`

5. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    ![installatiekopie](common/both-preintegrated-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://sso2.orgchartnow.com/Shibboleth.sso/Login?entityID=<YourEntityID>&target=https://sso2.orgchartnow.com`

    > [!NOTE]
    > `<YourEntityID>`is de **Azure ad-id** gekopieerd van het gedeelte **nu organigram instellen** , verderop in de zelf studie beschreven.

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

7. Kopieer op de sectie **organigram instellen nu** de gewenste URL ('s) volgens uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-orgchart-now-single-sign-on"></a>Organigram nu configureren voor eenmalige aanmelding

Als u eenmalige aanmelding op het **organigram** wilt configureren, moet u het gedownloade **XML-bestand met federatieve meta gegevens** en de juiste gekopieerde url's verzenden van Azure Portal naar het [organigram nu ondersteunings team](mailto:ocnsupport@officeworksoftware.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

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

In deze sectie schakelt u Julia Simon in voor het gebruik van eenmalige aanmelding van Azure door de toegang tot het organigram nu te verlenen.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **organigram nu**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen **nu organigram**.

    ![De koppeling nu organigram in de lijst toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-orgchart-now-test-user"></a>Nu organigram maken test gebruiker

Als u wilt dat Azure AD-gebruikers zich nu kunnen aanmelden bij het organigram, moeten ze nu in het organigram worden ingericht. 

1. Het organigram biedt nu ondersteuning voor Just-in-time-inrichting, die standaard is ingeschakeld. Er wordt nu een nieuwe gebruiker gemaakt tijdens een poging om toegang te krijgen tot het organigram als het nog niet bestaat. Met de just-in-time gebruikers inrichtings functie wordt alleen een **alleen-lezen** gebruiker gemaakt wanneer een SSO-aanvraag afkomstig is van een herkende IDP en het e-mail adres in de gebruikers lijst niet is gevonden. Voor deze functie voor automatische inrichting moet u nu een toegangs groep met de titel **Algemeen** in het organigram maken. Volg de onderstaande stappen om een toegangs groep te maken:

    a. Ga naar de optie **groepen beheren** , nadat u op het **vistuig** hebt geklikt in de rechter bovenhoek van de gebruikers interface.

    ![Groepen nu op het organigram](./media/orgchartnow-tutorial/tutorial_orgchartnow_manage.png)    

    b. Selecteer het pictogram **toevoegen** en noem de groep **Algemeen** en klik vervolgens op **OK**. 

    ![Organigram nu toevoegen](./media/orgchartnow-tutorial/tutorial_orgchartnow_add.png)

    c. Selecteer de map (pen) waarvoor u de algemene of alleen-lezen gebruikers toegang wilt geven:

    ![Nu mappen op organigram](./media/orgchartnow-tutorial/tutorial_orgchartnow_chart.png)

    d. **Vergrendel** de mappen zodat alleen gebruikers met beheerders rechten deze kunnen wijzigen. Klik vervolgens op **OK**.

    ![Organigram nu vergren delen](./media/orgchartnow-tutorial/tutorial_orgchartnow_lock.png)

2. Als u gebruikers met **beheerders** rechten en gebruikers voor **lezen/schrijven** wilt maken, moet u hand matig een gebruiker maken om toegang te krijgen tot hun bevoegdheids niveau via SSO. Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:

    a. Meld u nu aan bij het organigram als een beveiligings beheerder.

    b.  Klik op **instellingen** in de rechter bovenhoek en navigeer vervolgens naar **gebruikers beheren**.

    ![Instellingen voor het organigram nu](./media/orgchartnow-tutorial/tutorial_orgchartnow_settings.png)

    c. Klik op **toevoegen** en voer de volgende stappen uit:

    ![Organigram wordt nu beheerd](./media/orgchartnow-tutorial/tutorial_orgchartnow_manageusers.png)

    * Voer in het tekstvak **gebruikers-id** de gebruikers-id in, zoals **\@brittasimon contoso.com**.

    * Voer in het tekstvak **e-mail adres** het e-mail bericht van de gebruiker in, zoals **\@brittasimon contoso.com**.

    * Klik op **Add**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel organigram nu klikt in het toegangs venster, moet u zich nu automatisch bij het organigram aanmelden waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

