---
title: 'Zelf studie: integratie Azure Active Directory met TurboRater | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en TurboRater.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: abb116b8-8024-4cc6-bc81-f32ef490ea17
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/8/2019
ms.author: jeedes
ms.openlocfilehash: 3777cf09ec669fe3df6bca13f6960f53c689767c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "67088277"
---
# <a name="tutorial-azure-active-directory-integration-with-turborater"></a>Zelf studie: integratie Azure Active Directory met TurboRater

In deze zelf studie leert u hoe u TurboRater integreert met Azure Active Directory (Azure AD).

Het integreren van TurboRater met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot TurboRater.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij TurboRater (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts op één centrale locatie beheren: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van een SaaS-app (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met TurboRater wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* Een TurboRater-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

TurboRater ondersteunt door IDP geïnitieerde eenmalige aanmelding (SSO).

## <a name="add-turborater-from-the-azure-marketplace"></a>TurboRater toevoegen vanuit Azure Marketplace

Als u de integratie van TurboRater in azure AD wilt configureren, moet u TurboRater van de Azure Marketplace toevoegen aan uw lijst met beheerde SaaS-apps:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com?azure-portal=true).
1. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.

    ![De optie Azure Active Directory](common/select-azuread.png)

1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De optie bedrijfs toepassingen](common/enterprise-applications.png)

1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **+ nieuwe toepassing** boven aan het deel venster.

    ![De optie nieuwe toepassing](common/add-new-app.png)

1. Typ **TurboRater**in het zoekvak. Selecteer in de zoek resultaten **TurboRater**en selecteer **toevoegen** om de toepassing toe te voegen.

    ![TurboRater in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met TurboRater op basis van een test gebruiker met de naam **B Simon**. Als u eenmalige aanmelding wilt gebruiken, moet u een koppeling tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in TurboRater.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met TurboRater, moet u de volgende bouw stenen volt ooien:

1. **[Eenmalige aanmelding voor Azure AD configureren](#configure-azure-ad-single-sign-on)** om uw gebruikers in staat te stellen deze functie te gebruiken.
1. **[TurboRater eenmalige aanmelding configureren](#configure-turborater-single-sign-on)** om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
1. **[Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user)** eenmalige aanmelding van Azure ad te testen met B. Simon.
1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe om B. Simon in te scha kelen voor het gebruik van eenmalige aanmelding voor Azure AD.
1. **[Maak een test gebruiker voor TurboRater](#create-a-turborater-test-user)** , zodat er een gebruiker is met de naam b. Simon in TurboRater die is gekoppeld aan de Azure AD-gebruiker met de naam B. Simon.
1. **[Test eenmalige aanmelding](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met TurboRater:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **TurboRater** Application Integration de optie **eenmalige aanmelding**.

    ![Optie voor eenmalige aanmelding configureren](common/select-sso.png)

1. Selecteer in het deel venster **eenmalige aanmelding selecteren** de optie **SAML/WS-** gegevensinvoermodus om eenmalige aanmelding in te scha kelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

1. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** de optie **bewerken** (het potlood pictogram) om het deel venster **basis-SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer de volgende stappen uit in het deel venster **basis-SAML-configuratie** :

    ![Informatie over eenmalige aanmelding voor TurboRater domein en Url's](common/idp-intiated.png)

    1. Voer in het vak **id (Entiteits-ID)** een URL in:

       `https://www.itcdataservices.com`

    1. Voer in het vak **antwoord-URL (assertion Consumer Service-URL)** een URL in met behulp van het volgende patroon:

       | Omgeving | URL |
       | ---------------| --------------- |
       | Testen  | `https://ratingqa.itcdataservices.com/webservices/imp/saml/login` |
       | Live  | `https://www.itcratingservices.com/webservices/imp/saml/login` |

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [ondersteunings team van TurboRater](https://www.getitc.com/support)om deze waarden te verkrijgen. U kunt ook verwijzen naar de patronen die worden weer gegeven in het deel venster **basis-SAML-configuratie** in de Azure Portal.

1. Selecteer in het deel venster **eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekening certificaat** de optie **downloaden** om de **federatieve meta gegevens-XML** te downloaden uit de opgegeven opties en op uw computer op te slaan.

    ![De optie voor XML-down load voor federatieve meta gegevens](common/metadataxml.png)

1. Kopieer de URL of Url's die u nodig hebt in de sectie **TurboRater instellen** :

   * **Aanmeldings-URL**
   * **Azure AD-id**
   * **Afmeldings-URL**

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-turborater-single-sign-on"></a>Eenmalige aanmelding voor TurboRater configureren

Als u eenmalige aanmelding aan de TurboRater zijde wilt configureren, moet u de gedownloade federatieve meta gegevens-XML en de juiste gekopieerde Url's van de Azure Portal naar het [ondersteunings team van TurboRater](https://www.getitc.com/support)verzenden. Het TurboRater-team zorgt ervoor dat de SAML SSO-verbinding aan beide zijden correct is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam Britta Simon maken in de Azure-portal.

1. Selecteer **Azure Active Directory**   > **gebruikers** > in het linkerdeel venster van de Azure Portal**alle gebruikers**.

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

In deze sectie schakelt u B. Simon in om de eenmalige aanmelding van Azure te gebruiken door hun toegang te verlenen aan TurboRater.

1. Selecteer in de Azure Portal**alle toepassingen** > in **bedrijfs toepassingen** > **TurboRater**.

    ![Deel venster ondernemings toepassingen](common/enterprise-applications.png)

1. Selecteer in de lijst toepassingen de optie **TurboRater**.

    ![TurboRater in de lijst met toepassingen](common/all-applications.png)

1. Selecteer in het linkerdeel venster onder **beheren**de optie **gebruikers en groepen**.

    ![De optie gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **+ gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het deel venster **toewijzing toevoegen** .

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

1. Selecteer in het deel venster **gebruikers en groepen** de optie **B. Simon** in de lijst met **gebruikers** en kies vervolgens **selecteren** onder aan het deel venster.

1. Als u een waarde voor een rol in de SAML-bevestiging verwacht, selecteert u in het deel venster **rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik onder aan het deel venster op **selecteren**.

1. Selecteer in het deel venster **toewijzing toevoegen** de optie **toewijzen**.

### <a name="create-a-turborater-test-user"></a>Een TurboRater-test gebruiker maken

In deze sectie maakt u een gebruiker met de naam B. Simon in TurboRater. Werk met het [TurboRater-ondersteunings team](https://www.getitc.com/support) om B. Simon toe te voegen als gebruiker in TurboRater. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie van eenmalige aanmelding voor Azure AD met behulp van de portal mijn apps.

Wanneer u **TurboRater** in de portal mijn apps selecteert, moet u automatisch worden aangemeld bij het TurboRater-abonnement waarvoor u eenmalige aanmelding hebt ingesteld. Zie [apps openen en gebruiken in de portal mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie over de portal mijn apps.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Lijst met zelf studies voor het integreren van SaaS-toepassingen met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
