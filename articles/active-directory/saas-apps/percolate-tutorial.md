---
title: 'Zelf studie: integratie Azure Active Directory met Percolate | Microsoft Docs'
description: In deze zelf studie leert u hoe u eenmalige aanmelding kunt configureren tussen Azure Active Directory en Percolate.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 355f9659-b378-44c9-aa88-236e9b529a53
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: a6c1f893757baf1e6c85420b31997a5073cff684
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67094601"
---
# <a name="tutorial-azure-active-directory-integration-with-percolate"></a>Zelf studie: integratie Azure Active Directory met Percolate

In deze zelf studie leert u hoe u Percolate integreert met Azure Active Directory (Azure AD).

Deze integratie biedt de volgende voor delen:

* U kunt Azure AD gebruiken om te bepalen wie toegang heeft tot Percolate.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij Percolate (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts op één centrale locatie beheren: de Azure Portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Percolate wilt configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Een Percolate-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u eenmalige aanmelding voor Azure AD in een test omgeving.

* Percolate ondersteunt door SP geïnitieerde en door IdP geïnitieerde SSO.

## <a name="add-percolate-from-the-gallery"></a>Percolate toevoegen vanuit de galerie

Als u de integratie van Percolate in azure AD wilt configureren, moet u Percolate uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Selecteer **Azure Active Directory**in het linkerdeel venster van de [Azure Portal](https://portal.azure.com):

    ![Selecteer Azure Active Directory](common/select-azuread.png)

2. Ga naar**alle toepassingen**in **bedrijfs toepassingen** > :

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een toepassing wilt toevoegen, selecteert u **nieuwe toepassing** boven aan het venster:

    ![Nieuwe toepassing selecteren](common/add-new-app.png)

4. Typ **Percolate**in het zoekvak. Selecteer **Percolate** in de zoek resultaten en selecteer vervolgens **toevoegen**.

     ![Zoekresultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD met Percolate configureren en testen met behulp van een test gebruiker met de naam Julia Simon.
Om eenmalige aanmelding in te scha kelen, moet u een relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Percolate.

U moet de volgende stappen uitvoeren om eenmalige aanmelding voor Azure AD te configureren en te testen met Percolate:

1. **[Eenmalige aanmelding voor Azure AD configureren](#configure-azure-ad-single-sign-on)** om de functie voor uw gebruikers in te scha kelen.
2. **[Percolate eenmalige aanmelding configureren](#configure-percolate-single-sign-on)** aan de kant van de toepassing.
3. **[Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user)** eenmalige aanmelding van Azure ad te testen.
4. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe om eenmalige aanmelding voor Azure AD in te scha kelen voor de gebruiker.
5. **[Maak een Percolate-test gebruiker](#create-a-percolate-test-user)** die is gekoppeld aan de Azure AD-weer gave van de gebruiker.
6. **[Test eenmalige aanmelding](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie schakelt u eenmalige aanmelding voor Azure AD in de Azure Portal.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met Percolate:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **Percolate** Application Integration de optie **eenmalige aanmelding**:

    ![Eenmalige aanmelding selecteren](common/select-sso.png)

2. Selecteer in het dialoog venster **eenmalige aanmelding selecteren** de optie **SAML/WS-** gegevensinvoermodus om eenmalige aanmelding in te scha kelen:

    ![Selecteer een methode voor eenmalige aanmelding](common/select-saml-option.png)

3. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** het **bewerkings** pictogram om het dialoog venster **basis configuratie van SAML** te openen:

    ![Bewerkpictogram](common/edit-urls.png)

4. In het dialoog venster **basis configuratie van SAML** hoeft u geen actie te ondernemen voor het configureren van de toepassing in de modus gestart door IDP. De app is al geïntegreerd met Azure.

    ![Informatie over eenmalige aanmelding voor Percolate domein en Url's](common/preintegrated.png)

5. Als u de toepassing in de modus door SP gestart wilt configureren, selecteert u **extra Url's instellen** en voert **https://percolate.com/app/login**u in het vak **Sign on URL** het volgende in:

   ![Informatie over eenmalige aanmelding voor Percolate domein en Url's](common/metadata-upload-additional-signon.png)
6. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , het **Kopieer** pictogram om de URL voor de **federatieve meta gegevens**van de app te kopiëren. Sla deze URL op.

    ![De URL voor de federatieve meta gegevens van de app kopiëren](common/copy-metadataurl.png)

7. In de sectie **Percolate instellen** kopieert u de juiste url's, op basis van uw vereisten.

    ![De configuratie-Url's kopiëren](common/copy-configuration-urls.png)

    1. **Aanmeldings-URL**.

    1. **Id van Azure AD**.

    1. **Afmeldings-URL**.

### <a name="configure-percolate-single-sign-on"></a>Eenmalige aanmelding voor Percolate configureren

1. Meld u in een nieuw browser venster aan bij Percolate als beheerder.

2. Selecteer aan de linkerkant van de start pagina **instellingen**:
    
    ![Instellingen selecteren](./media/percolate-tutorial/configure01.png)

3. Selecteer in het linkerdeel venster **SSO** onder **organisatie**:

    ![SSO onder organisatie selecteren](./media/percolate-tutorial/configure02.png)

    1. Plak in het vak **aanmeldings-URL** de waarde voor de **aanmeldings-URL** die u hebt gekopieerd uit de Azure Portal.

    1. Plak in het vak **Entiteits-ID** de waarde van de **Azure ad-id** die u hebt gekopieerd uit de Azure Portal.

    1. Open in Klad blok het door base-64 gecodeerde certificaat dat u hebt gedownload van de Azure Portal. Kopieer de inhoud en plak deze in het vak **x509-certificaten** .

    1. Voer **EmailAddress**in het vak **e-mail kenmerk** in.

    1. Het vak **URL van meta gegevens van de identiteits provider** is een optioneel veld. Als u een URL voor de **federatieve meta gegevens** van de app hebt gekopieerd uit de Azure Portal, kunt u deze in dit vak plakken.

    1. Selecteer in de lijst **moet AuthNRequests zijn ondertekend?** de optie **Nee**.

    1. Selecteer in de lijst **automatische SSO-inrichting inschakelen** de optie **Nee**.

    1. Selecteer **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker met de naam Julia Simon in de Azure Portal.

1. Selecteer in de Azure Portal **Azure Active Directory** in het linkerdeel venster, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**:

    ![Selecteer Alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm:

    ![Nieuwe gebruiker selecteren](common/new-user.png)

3. Voer in het dialoog venster **gebruiker** de volgende stappen uit.

    ![Dialoog venster gebruiker](common/user-properties.png)

    1. Voer in het vak **Naam****Britta Simon**in.
  
    1. Voer in het vak **gebruikers naam** **BrittaSimon@\<yourcompanydomain> in.\< extensie>**. (Bijvoorbeeld BrittaSimon@contoso.com.)

    1. Selecteer **wacht woord weer geven**en noteer de waarde in het vak **wacht woord** .

    1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure AD te gebruiken door haar toegang tot Percolate te verlenen.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **Percolate**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Percolate**in de lijst met toepassingen.

    ![Lijst met toepassingen](common/all-applications.png)

3. Selecteer in het linkerdeel venster **gebruikers en groepen**:

    ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Gebruikers en groepen selecteren](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** de optie **Julia Simon** in de lijst met gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.

6. Als u een waarde van een rol verwacht in de SAML-verklaring, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik op de knop **selecteren** onder aan het scherm.

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-a-percolate-test-user"></a>Een Percolate-test gebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Percolate, moet u deze toevoegen aan Percolate. U moet deze hand matig toevoegen.

Voer de volgende stappen uit om een gebruikers account te maken:

1. Meld u aan bij Percolate als beheerder.

2. Selecteer in het linkerdeel venster **gebruikers** onder **organisatie**. **Nieuwe gebruikers**selecteren:

    ![Nieuwe gebruikers selecteren](./media/percolate-tutorial/configure03.png)

3. Voer de volgende stappen uit op de pagina **gebruikers maken** .

    ![Pagina gebruikers maken](./media/percolate-tutorial/configure04.png)

    1. Voer in het vak **e-mail** het e-mail adres van de gebruiker in. Bijvoorbeeld brittasimon@contoso.com.

    1. Voer in het vak **volledige naam** de naam van de gebruiker in. Bijvoorbeeld **Brittasimon**.

    1. Selecteer **gebruikers maken**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

Nu moet u de configuratie van de eenmalige aanmelding voor Azure AD testen met behulp van het toegangs venster.

Wanneer u de tegel Percolate in het toegangs venster selecteert, wordt u automatisch aangemeld bij het Percolate-exemplaar waarvoor u SSO hebt ingesteld. Zie voor meer informatie [apps openen en gebruiken in de portal mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)