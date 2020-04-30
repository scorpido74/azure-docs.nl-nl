---
title: 'Zelf studie: integratie Azure Active Directory met InsideView | Microsoft Docs'
description: In deze zelf studie leert u hoe u eenmalige aanmelding kunt configureren tussen Azure Active Directory en InsideView.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: jeedes
ms.openlocfilehash: 4278f00b1026fe4d1b95634540d6d035d43ca5ab
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82202346"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Zelf studie: integratie Azure Active Directory met InsideView

In deze zelf studie leert u hoe u InsideView integreert met Azure Active Directory (Azure AD).
Deze integratie biedt de volgende voor delen:

* U kunt Azure AD gebruiken om te bepalen wie toegang heeft tot InsideView.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij InsideView (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts op één centrale locatie beheren: de Azure Portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met InsideView wilt configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Een InsideView-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u eenmalige aanmelding voor Azure AD in een test omgeving.

* InsideView ondersteunt door IdP geïnitieerde SSO.

## <a name="add-insideview-from-the-gallery"></a>InsideView toevoegen vanuit de galerie

Als u de integratie van InsideView in azure AD wilt instellen, moet u InsideView uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Selecteer **Azure Active Directory**in het linkerdeel venster van de [Azure Portal](https://portal.azure.com):

    ![Selecteer Azure Active Directory](common/select-azuread.png)

2. Ga naar**alle toepassingen**in **bedrijfs toepassingen** > :

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een toepassing wilt toevoegen, selecteert u **nieuwe toepassing** boven aan het venster:

    ![Nieuwe toepassing selecteren](common/add-new-app.png)

4. Typ **InsideView**in het zoekvak. Selecteer **InsideView** in de zoek resultaten en selecteer vervolgens **toevoegen**.

    ![Zoekresultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD met InsideView configureren en testen met behulp van een test gebruiker met de naam Julia Simon.
Om eenmalige aanmelding in te scha kelen, moet u een relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in InsideView.

U moet de volgende stappen uitvoeren om eenmalige aanmelding voor Azure AD te configureren en te testen met InsideView:

1. **[Eenmalige aanmelding voor Azure AD configureren](#configure-azure-ad-single-sign-on)** om de functie voor uw gebruikers in te scha kelen.
2. **[InsideView eenmalige aanmelding configureren](#configure-insideview-single-sign-on)** aan de kant van de toepassing.
3. **[Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user)** eenmalige aanmelding van Azure ad te testen.
4. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe om eenmalige aanmelding voor Azure AD in te scha kelen voor de gebruiker.
5. **[Maak een InsideView-test gebruiker](#create-an-insideview-test-user)** die is gekoppeld aan de Azure AD-weer gave van de gebruiker.
6. **[Test eenmalige aanmelding](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie schakelt u eenmalige aanmelding voor Azure AD in de Azure Portal.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met InsideView:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina InsideView Application Integration de optie **eenmalige aanmelding**:

    ![Eenmalige aanmelding selecteren](common/select-sso.png)

2. Selecteer in het dialoog venster **eenmalige aanmelding selecteren** de optie **SAML/WS-** gegevensinvoermodus om eenmalige aanmelding in te scha kelen:

    ![Selecteer een methode voor eenmalige aanmelding](common/select-saml-option.png)

3. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** het **bewerkings** pictogram om het dialoog venster **basis configuratie van SAML** te openen:

    ![Bewerkpictogram](common/edit-urls.png)

4. Voer in het dialoog venster **basis configuratie van SAML** de volgende stappen uit.

    ![Het dialoog venster basis configuratie van SAML](common/idp-reply.png)

    Voer in het vak **antwoord-URL** een URL in dit patroon in:

    `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE]
    > Deze waarde is een tijdelijke aanduiding. U moet de daad werkelijke antwoord-URL gebruiken. Neem contact op met het [ondersteunings team van InsideView](mailto:support@insideview.com) om de waarde op te halen. U kunt ook verwijzen naar de patronen die worden weer gegeven in het dialoog venster **basis configuratie van SAML** in de Azure Portal.

5. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **Download** koppeling naast **certificaat (onbewerkt)**, volgens uw vereisten en sla het certificaat op uw computer op:

    ![De koppeling om het certificaat te downloaden](common/certificateraw.png)

6. In de sectie **InsideView instellen** kopieert u de juiste url's, op basis van uw vereisten:

    ![De configuratie-Url's kopiëren](common/copy-configuration-urls.png)

    1. **Aanmeldings-URL**.

    1. **Id van Azure AD**.

    1. **Afmeldings-URL**.

### <a name="configure-insideview-single-sign-on"></a>Eenmalige aanmelding voor InsideView configureren

1. Meld u in een nieuw browser venster aan bij uw InsideView-bedrijfs site als beheerder.

1. Selecteer boven aan het venster de optie **beheer**, **SingleSignOn instellingen**en vervolgens **SAML toevoegen**.
   
   ![Instellingen voor eenmalige SAML-aanmelding](./media/insideview-tutorial/ic794135.png "Instellingen voor eenmalige SAML-aanmelding")

1. Voer de volgende stappen uit in de sectie **een nieuwe SAML toevoegen** .

    ![Een nieuwe SAML-sectie toevoegen](./media/insideview-tutorial/ic794136.png "Een nieuwe SAML-sectie toevoegen")

    1. Voer in het vak **STS-naam** een naam in voor uw configuratie.

    1. Plak in het vak **SamlP/WS-solicit ongevraagd eind punt** de waarde van de **aanmeldings-URL** die u hebt gekopieerd uit de Azure Portal.

    1. Open het onbewerkte certificaat dat u hebt gedownload van de Azure Portal. Kopieer de inhoud van het certificaat naar het klem bord en plak de inhoud vervolgens in het vak **STS-certificaat** .

    1. Voer **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** in het vak **toewijzing van CRM-gebruikers-id** in.

    1. Voer **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`** in het vak **CRM e-mail toewijzing** in.

    1. Voer **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`** in het vak **CRM-voor naam toewijzing** in.

    1. Voer **`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`** in het vak **CRM-achternaam toewijzing** in.  

    1. Selecteer **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker met de naam Julia Simon in de Azure Portal.

1. Selecteer in de Azure Portal **Azure Active Directory** in het linkerdeel venster, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**:

    ![Selecteer Alle gebruikers](common/users.png)

2. Selecteer een **nieuwe gebruiker** boven aan het venster:

    ![Nieuwe gebruiker selecteren](common/new-user.png)

3. Voer in het dialoog venster **gebruiker** de volgende stappen uit.

    ![Dialoog venster gebruiker](common/user-properties.png)

    1. Voer in het vak **Naam****Britta Simon**in.
  
    1. Voer in het vak **gebruikers naam** **BrittaSimon@\<yourcompanydomain> in.\< extensie>**. (Bijvoorbeeld BrittaSimon@contoso.com.)

    1. Selecteer **wacht woord weer geven**en noteer de waarde in het vak **wacht woord** .

    1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om de eenmalige aanmelding van Azure te gebruiken door haar toegang tot InsideView te verlenen.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**, selecteer **alle toepassingen**en selecteer vervolgens **InsideView**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **InsideView**in de lijst met toepassingen.

    ![Lijst met toepassingen](common/all-applications.png)

3. Selecteer in het linkerdeel venster **gebruikers en groepen**:

    ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Gebruiker toevoegen selecteren](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** de optie **Julia Simon** in de lijst met gebruikers en klik vervolgens op de knop **selecteren** onder aan het venster.

6. Als u een waarde van een rol verwacht in de SAML-verklaring, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik op de knop **selecteren** onder aan het venster.

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-an-insideview-test-user"></a>Een InsideView-test gebruiker maken

Als u wilt dat Azure AD-gebruikers zich kunnen aanmelden bij InsideView, moet u deze toevoegen aan InsideView. U moet deze hand matig toevoegen.

Als u gebruikers of contact personen in InsideView wilt maken, neemt u contact op met het [ondersteunings team van InsideView](mailto:support@insideview.com).

> [!NOTE]
> U kunt elk hulp programma voor het maken van een gebruikers account of API die wordt geleverd door InsideView gebruiken om Azure AD-gebruikers accounts in te richten.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

Nu moet u de configuratie van de eenmalige aanmelding voor Azure AD testen met behulp van het toegangs venster.

Wanneer u de tegel InsideView in het toegangs venster selecteert, wordt u automatisch aangemeld bij het InsideView-exemplaar waarvoor u SSO hebt ingesteld. Zie voor meer informatie over het toegangs venster [apps openen en gebruiken in de portal mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
