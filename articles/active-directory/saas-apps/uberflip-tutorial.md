---
title: 'Zelf studie: integratie Azure Active Directory met Uberflip | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Uberflip.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 754b1f5b-6694-4fd6-9e1e-9fad769c64db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: d20c05e6ec5a413b81ede9cb4906de2595967115
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80048475"
---
# <a name="tutorial-azure-active-directory-integration-with-uberflip"></a>Zelf studie: integratie Azure Active Directory met Uberflip

In deze zelf studie leert u hoe u Uberflip integreert met Azure Active Directory (Azure AD).

Het integreren van Uberflip met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot Uberflip.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij Uberflip (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts op één centrale locatie beheren: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van een SaaS-app (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Uberflip wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* Een Uberflip-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

Uberflip biedt ondersteuning voor de volgende functies:

* Door SP geïnitieerd en IDP: eenmalige aanmelding (SSO) gestart.
* Just-in-time-gebruikers inrichting.

## <a name="add-uberflip-from-the-azure-marketplace"></a>Uberflip toevoegen vanuit Azure Marketplace

Als u de integratie van Uberflip in azure AD wilt configureren, moet u Uberflip van de Azure Marketplace toevoegen aan uw lijst met beheerde SaaS-apps:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).
1. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.

   ![De optie Azure Active Directory](common/select-azuread.png)

1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

   ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **+ nieuwe toepassing** boven aan het deel venster.

   ![De optie nieuwe toepassing](common/add-new-app.png)

1. Typ **Uberflip**in het zoekvak. Selecteer in de zoek resultaten **Uberflip**en selecteer **toevoegen** om de toepassing toe te voegen.

   ![Uberflip in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Uberflip op basis van een test gebruiker met de naam **B Simon**. Als u eenmalige aanmelding wilt gebruiken, moet u een koppeling tot stand brengen tussen een Azure AD-gebruiker en een gerelateerde gebruiker in Uberflip.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met Uberflip, moet u de volgende bouw stenen volt ooien:

1. **[Eenmalige aanmelding voor Azure AD configureren](#configure-azure-ad-single-sign-on)** om uw gebruikers in staat te stellen deze functie te gebruiken.
1. **[Uberflip eenmalige aanmelding configureren](#configure-uberflip-single-sign-on)** om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
1. **[Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user)** eenmalige aanmelding van Azure ad te testen met B. Simon.
1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe om B. Simon in te scha kelen voor het gebruik van eenmalige aanmelding voor Azure AD.
1. **[Maak een Uberflip test gebruiker](#create-an-uberflip-test-user)** zodat er een gebruiker is met de naam b. Simon in Uberflip die is gekoppeld aan de Azure AD-gebruiker met de naam B. Simon.
1. **[Test eenmalige aanmelding](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met Uberflip:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **Uberflip** Application Integration de optie **eenmalige aanmelding**.

    ![Optie voor eenmalige aanmelding configureren](common/select-sso.png)

1. Selecteer in het deel venster **eenmalige aanmelding selecteren** de optie **SAML/WS-** gegevensinvoermodus om eenmalige aanmelding in te scha kelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

1. Selecteer in het deel venster **eenmalige aanmelding met SAML instellen** de optie **bewerken** (het potlood pictogram) om het deel venster **basis-SAML-configuratie** te openen.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in het deel venster **basis-SAML-configuratie** een van de volgende stappen uit, afhankelijk van de SSO-modus die u wilt configureren:

   * Als u de toepassing in de IDP-modus voor eenmalige aanmelding wilt configureren, voert u in het vak **antwoord-URL (assertion Consumer Service-URL)** een URL in op het volgende patroon:

     `https://app.uberflip.com/sso/saml2/<IDPID>/<ACCOUNTID>`

     ![Informatie over eenmalige aanmelding voor Uberflip domein en Url's](common/both-replyurl.png)

     > [!NOTE]
     > Deze waarde is niet echt. Werk deze waarde bij met de daad werkelijke antwoord-URL. Neem contact op met het [ondersteunings team van Uberflip](mailto:support@uberflip.com)om de werkelijke waarde op te halen. U kunt ook verwijzen naar de patronen die worden weer gegeven in het deel venster **basis-SAML-configuratie** in de Azure Portal.

   * Als u de toepassing in de door SP geïnitieerde SSO-modus wilt configureren, selecteert u **extra Url's instellen**en voert u in het vak **aanmeldings-URL** de volgende URL in:

     `https://app.uberflip.com/users/login`

     ![Informatie over eenmalige aanmelding voor Uberflip domein en Url's](common/both-signonurl.png)

1. Selecteer in het deel venster **eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekening certificaat** de optie **downloaden** om de **federatieve meta gegevens-XML** te downloaden uit de opgegeven opties en op uw computer op te slaan.

   ![De optie voor XML-down load voor federatieve meta gegevens](common/metadataxml.png)

1. Kopieer in het deel venster **Uberflip instellen** de URL of url's die u nodig hebt:

   * **Aanmeldings-URL**
   * **Azure AD-id**
   * **Afmeldings-URL**

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-uberflip-single-sign-on"></a>Eenmalige aanmelding voor Uberflip configureren

Als u eenmalige aanmelding aan de Uberflip zijde wilt configureren, moet u de gedownloade federatieve meta gegevens-XML en de juiste gekopieerde Url's van de Azure Portal naar het [ondersteunings team van Uberflip](mailto:support@uberflip.com)verzenden. Het Uberflip-team zorgt ervoor dat de SAML SSO-verbinding aan beide zijden correct is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker met de naam B. Simon in de Azure Portal.

1. Selecteer **Azure Active Directory** > **gebruikers** > in het linkerdeel venster van de Azure Portal**alle gebruikers**.

    ![De opties gebruikers en alle gebruikers](common/users.png)

1. Selecteer boven aan het scherm **+ nieuwe gebruiker**.

    ![Optie nieuwe gebruiker](common/new-user.png)

1. Voer de volgende stappen uit in het deel venster van de **gebruiker** :

    ![Het deel venster gebruiker](common/user-properties.png)

    1. Typ **BSimon**in het vak **naam** .
  
    1. Voer in het vak **gebruikers naam** **BSimon\@\<yourcompanydomain> in.\< extensie>**. Bijvoorbeeld **BSimon\@contoso.com**.

    1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .

    1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om de eenmalige aanmelding van Azure te gebruiken door hun toegang te verlenen aan Uberflip.

1. Selecteer in de Azure Portal**alle toepassingen** > in **bedrijfs toepassingen** > **Uberflip**.

    ![Deel venster ondernemings toepassingen](common/enterprise-applications.png)

1. Selecteer in de lijst toepassingen de optie **Uberflip**.

    ![Uberflip in de lijst met toepassingen](common/all-applications.png)

1. Selecteer in het linkerdeel venster onder **beheren**de optie **gebruikers en groepen**.

    ![De optie gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **+ gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het deel venster **toewijzing toevoegen** .

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

1. Selecteer in het deel venster **gebruikers en groepen** de optie **B Simon** in de lijst met **gebruikers** en kies vervolgens **selecteren** onder aan het deel venster.

1. Als u een waarde voor een rol in de SAML-bevestiging verwacht, selecteert u in het deel venster **rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik onder aan het deel venster op **selecteren**.

1. Selecteer in het deel venster **toewijzing toevoegen** de optie **toewijzen**.

### <a name="create-an-uberflip-test-user"></a>Een Uberflip-test gebruiker maken

Een gebruiker met de naam B. Simon is nu gemaakt in Uberflip. U hoeft niets te doen om deze gebruiker te maken. Uberflip biedt ondersteuning voor Just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Als een gebruiker met de naam B. Simon niet al bestaat in Uberflip, wordt er een nieuwe gemaakt na verificatie.

> [!NOTE]
> Als u hand matig een gebruiker moet maken, neemt u contact op met het [ondersteunings team van Uberflip](mailto:support@uberflip.com).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie van eenmalige aanmelding voor Azure AD met behulp van de portal mijn apps.

Wanneer u **Uberflip** in de portal mijn apps selecteert, moet u automatisch worden aangemeld bij het Uberflip-abonnement waarvoor u eenmalige aanmelding hebt ingesteld. Zie [apps openen en gebruiken in de portal mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie over de portal mijn apps.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Lijst met zelf studies voor het integreren van SaaS-toepassingen met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
