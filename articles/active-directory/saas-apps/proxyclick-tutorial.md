---
title: 'Zelf studie: integratie Azure Active Directory met Proxyclick | Microsoft Docs'
description: In deze zelf studie leert u hoe u eenmalige aanmelding kunt configureren tussen Azure Active Directory en Proxyclick.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5c58a859-71c2-4542-ae92-e5f16a8e7f18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/27/2019
ms.author: jeedes
ms.openlocfilehash: 6a4d1c8a390ebd1194d14c057bb32d3111bf39be
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67093499"
---
# <a name="tutorial-azure-active-directory-integration-with-proxyclick"></a>Zelf studie: integratie Azure Active Directory met Proxyclick

In deze zelf studie leert u hoe u Proxyclick integreert met Azure Active Directory (Azure AD).
Deze integratie biedt de volgende voor delen:

* U kunt Azure AD gebruiken om te bepalen wie toegang heeft tot Proxyclick.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij Proxyclick (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts op één centrale locatie beheren: de Azure Portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Proxyclick wilt configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u zich aanmelden voor een [proef versie van één maand](https://azure.microsoft.com/pricing/free-trial/).
* Een Proxyclick-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u eenmalige aanmelding voor Azure AD in een test omgeving.

* Proxyclick ondersteunt door SP geïnitieerde en door IdP geïnitieerde SSO.

## <a name="add-proxyclick-from-the-gallery"></a>Proxyclick toevoegen vanuit de galerie

Als u de integratie van Proxyclick in azure AD wilt instellen, moet u Proxyclick uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Selecteer **Azure Active Directory**in het linkerdeel venster van de [Azure Portal](https://portal.azure.com):

    ![Selecteer Azure Active Directory](common/select-azuread.png)

2. Ga naar**alle toepassingen**in **bedrijfs toepassingen** > :

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een toepassing wilt toevoegen, selecteert u **nieuwe toepassing** boven aan het venster:

    ![Nieuwe toepassing selecteren](common/add-new-app.png)

4. Typ **Proxyclick**in het zoekvak. Selecteer **Proxyclick** in de zoek resultaten en selecteer vervolgens **toevoegen**.

     ![Zoekresultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD met Proxyclick configureren en testen met behulp van een test gebruiker met de naam Julia Simon.
Om eenmalige aanmelding in te scha kelen, moet u een relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Proxyclick.

U moet de volgende stappen uitvoeren om eenmalige aanmelding voor Azure AD te configureren en te testen met Proxyclick:

1. **[Eenmalige aanmelding voor Azure AD configureren](#configure-azure-ad-single-sign-on)** om de functie voor uw gebruikers in te scha kelen.
2. **[Proxyclick eenmalige aanmelding configureren](#configure-proxyclick-single-sign-on)** aan de kant van de toepassing.
3. **[Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user)** eenmalige aanmelding van Azure ad te testen.
4. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe om eenmalige aanmelding voor Azure AD in te scha kelen voor de gebruiker.
5. **[Maak een Proxyclick-test gebruiker](#create-a-proxyclick-test-user)** die is gekoppeld aan de Azure AD-weer gave van de gebruiker.
6. **[Test eenmalige aanmelding](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie schakelt u eenmalige aanmelding voor Azure AD in de Azure Portal.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met Proxyclick:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina Proxyclick Application Integration de optie **eenmalige aanmelding**:

    ![Eenmalige aanmelding selecteren](common/select-sso.png)

2. Selecteer in het dialoog venster **eenmalige aanmelding selecteren** de optie **SAML/WS-** gegevensinvoermodus om eenmalige aanmelding in te scha kelen:

    ![Selecteer een methode voor eenmalige aanmelding](common/select-saml-option.png)

3. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** het **bewerkings** pictogram om het dialoog venster **basis configuratie van SAML** te openen:

    ![Bewerkpictogram](common/edit-urls.png)

4. In het dialoog venster **basis configuratie van SAML** kunt u de volgende stappen uitvoeren als u de toepassing in de door IDP gestarte modus wilt configureren.

    ![Het dialoog venster basis configuratie van SAML](common/idp-intiated.png)

    1. Voer in het vak **id** een URL in dit patroon in:
   
       `https://saml.proxyclick.com/init/<companyId>`

    1. Voer in het vak **antwoord-URL** een URL in dit patroon in:

       `https://saml.proxyclick.com/consume/<companyId>`

5. Als u de toepassing in de door SP geïnitieerde modus wilt configureren, selecteert u **extra Url's instellen**. Voer in het vak **URL voor aanmelden** een URL in dit patroon in:
   
   `https://saml.proxyclick.com/init/<companyId>`

    ![Informatie over eenmalige aanmelding voor Proxyclick domein en Url's](common/metadata-upload-additional-signon.png)

    

    > [!NOTE]
    > Deze waarden zijn tijdelijke aanduidingen. U moet de daad werkelijke id, de antwoord-URL en de aanmeldings-URL gebruiken. Stappen voor het ophalen van deze waarden worden verderop in deze zelf studie beschreven.

6. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **Download** koppeling naast **certificaat (base64)**, volgens uw vereisten en sla het certificaat op uw computer op:

    ![De koppeling om het certificaat te downloaden](common/certificatebase64.png)

7. In de sectie **Proxyclick instellen** kopieert u de juiste url's, op basis van uw vereisten:

    ![De configuratie-Url's kopiëren](common/copy-configuration-urls.png)

    1. **Aanmeldings-URL**.

    1. **Id van Azure AD**.

    1. **Afmeldings-URL**.

### <a name="configure-proxyclick-single-sign-on"></a>Eenmalige aanmelding voor Proxyclick configureren

1. Meld u in een nieuw browser venster aan bij uw Proxyclick-bedrijfs site als beheerder.

2. Selecteer **Account & instellingen**:

    ![Account & instellingen selecteren](./media/proxyclick-tutorial/configure1.png)

3. Schuif omlaag naar de sectie **integraties** en selecteer **SAML**:

    ![SAML selecteren](./media/proxyclick-tutorial/configure2.png)

4. Voer de volgende stappen uit in de sectie **SAML** .

    ![Sectie SAML](./media/proxyclick-tutorial/configure3.png)

    1. Kopieer de waarde van de **SAML Consumer-URL** en plak deze in het vak **antwoord-URL** in het dialoog venster **basis configuratie van SAML** in de Azure Portal.

    1. Kopieer de waarde van de **SAML SSO omleidings-URL** en plak deze in de vakken **Sign on URL** en **id** in het dialoog venster **basis-SAML-configuratie** in de Azure Portal.

    1. In de lijst **SAML-aanvraag methode** selecteert u **HTTP-omleiding**.

    1. Plak in het vak **Uitgever** de id-waarde van **Azure AD** die u hebt gekopieerd uit de Azure Portal.

    1. Plak in het vak URL van het **SAML 2,0-eind punt** de waarde van de **aanmeldings-URL** die u hebt gekopieerd uit de Azure Portal.

    1. Open in Klad blok het certificaat bestand dat u hebt gedownload van de Azure Portal. Plak de inhoud van dit bestand in het vak **certificaat** .

    1. Selecteer **Save changes**.

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

In deze sectie schakelt u Julia Simon in om de eenmalige aanmelding van Azure te gebruiken door haar toegang tot Proxyclick te verlenen.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **Proxyclick**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Proxyclick**in de lijst met toepassingen.

    ![Lijst met toepassingen](common/all-applications.png)

3. Selecteer in het linkerdeel venster **gebruikers en groepen**:

    ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Gebruiker toevoegen selecteren](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** de optie **Julia Simon** in de lijst met gebruikers en klik vervolgens op de knop **selecteren** onder aan het venster.

6. Als u een waarde van een rol verwacht in de SAML-verklaring, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik op de knop **selecteren** onder aan het venster.

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-a-proxyclick-test-user"></a>Een Proxyclick-test gebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij Proxyclick, moet u deze toevoegen aan Proxyclick. U moet deze hand matig toevoegen.

Voer de volgende stappen uit om een gebruikers account te maken:

1. Meld u als beheerder aan bij uw Proxyclick-bedrijfs site.

1. Selecteer **collega's** bovenaan het venster:

    ![Collega's selecteren](./media/proxyclick-tutorial/user1.png)

1. Selecteer **collega toevoegen**:

    ![Collega toevoegen selecteren](./media/proxyclick-tutorial/user2.png)

1. Voer de volgende stappen uit in de sectie **een collega toevoegen** .

    ![Een sectie van een collega toevoegen](./media/proxyclick-tutorial/user3.png)

    1. Voer in het vak **e-mail** het e-mail adres van de gebruiker in. In dit geval **brittasimon\@contoso.com**.

    1. Voer in het vak **voor naam** de voor naam van de gebruiker in. In dit geval **Julia**.

    1. Voer in het vak **Achternaam** de achternaam van de gebruiker in. In dit geval **Simon**.

    1. Selecteer **gebruiker toevoegen**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

Nu moet u de configuratie van de eenmalige aanmelding voor Azure AD testen met behulp van het toegangs venster.

Wanneer u de tegel Proxyclick in het toegangs venster selecteert, wordt u automatisch aangemeld bij het Proxyclick-exemplaar waarvoor u SSO hebt ingesteld. Zie voor meer informatie over het toegangs venster [apps openen en gebruiken in de portal mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

