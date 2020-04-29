---
title: 'Zelf studie: integratie Azure Active Directory met TigerText Secure Messenger | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en TigerText Secure Mess enger.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 03f1e128-5bcb-4e49-b6a3-fe22eedc6d5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: ea3bda1dd51a7c3a2e5e3f8b669d7138898f1595
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67088661"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Zelf studie: integratie Azure Active Directory met TigerText Secure Mess enger

In deze zelf studie leert u hoe u TigerText Secure Mess enger integreert met Azure Active Directory (Azure AD).

Het integreren van TigerText Secure Mess enger met Azure AD biedt de volgende voor delen:

* U kunt beheren in azure AD die toegang heeft tot TigerText Secure Mess enger.
* U kunt ervoor zorgen dat uw gebruikers automatisch worden aangemeld bij TigerText Secure Mess enger (eenmalige aanmelding) met hun Azure AD-accounts.
* U kunt uw accounts op één centrale locatie beheren: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van een SaaS-app (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met TigerText Secure Mess enger wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.
* Een TigerText Secure Mess enger-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie kunt u eenmalige aanmelding voor Azure AD configureren en testen in een test omgeving en TigerText Secure Mess enger integreren met Azure AD.

TigerText Secure Mess enger ondersteunt door SP geïnitieerde eenmalige aanmelding (SSO).

## <a name="add-tigertext-secure-messenger-from-the-azure-marketplace"></a>TigerText Secure Mess enger toevoegen vanuit Azure Marketplace

Als u de integratie van TigerText Secure Mess enger wilt configureren in azure AD, moet u TigerText Secure Mess enger van Azure Marketplace toevoegen aan uw lijst met beheerde SaaS-apps:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com?azure-portal=true).
1. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.

    ![De optie Azure Active Directory](common/select-azuread.png)

1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **+ nieuwe toepassing** boven aan het deel venster.

    ![De optie nieuwe toepassing](common/add-new-app.png)

1. Voer **TigerText Secure Mess enger**in het zoekvak in. Selecteer in de zoek resultaten **TigerText Secure Mess enger**en selecteer **toevoegen** om de toepassing toe te voegen.

    ![Beveiligde Mess enger TigerText in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met TigerText Secure Mess enger op basis van een test gebruiker met de naam **Julia Simon**. Als u eenmalige aanmelding wilt gebruiken, moet u een koppeling tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in TigerText Secure Mess enger.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met TigerText Secure Mess enger, moet u de volgende bouw stenen volt ooien:

1. **[Eenmalige aanmelding voor Azure AD configureren](#configure-azure-ad-single-sign-on)** om uw gebruikers in staat te stellen deze functie te gebruiken.
1. **[Configureer eenmalige aanmelding voor TigerText Secure Mess enger](#configure-tigertext-secure-messenger-single-sign-on)** om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding voor Azure ad te testen met Julia Simon.
1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe om Julia Simon in staat te stellen om eenmalige aanmelding van Azure ad te gebruiken.
1. **[Maak een TigerText beveiligde Mess enger-test gebruiker](#create-a-tigertext-secure-messenger-test-user)** , zodat er een gebruiker met de naam Julia Simon in TigerText Secure Mess enger is gekoppeld aan de Azure AD-gebruiker met de naam Julia Simon.
1. **[Test eenmalige aanmelding](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met TigerText Secure Mess enger:

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **beveiligde Mess enger** -toepassings integratie TigerText de optie **eenmalige aanmelding**.

    ![Optie voor eenmalige aanmelding configureren](common/select-sso.png)

1. Selecteer in het deel venster **eenmalige aanmelding selecteren** de optie **SAML/WS-** gegevensinvoermodus om eenmalige aanmelding in te scha kelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

1. Selecteer in het deel venster **eenmalige aanmelding met SAML instellen** de optie **bewerken** (het potlood pictogram) om het deel venster **basis-SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer de volgende stappen uit in het deel venster **basis configuratie van SAML** :

    ![Informatie over TigerText Secure Mess enger-domein en Url's voor eenmalige aanmelding](common/sp-identifier.png)

    1. Voer in het vak **AANMELDINGS URL** een URL in:

       `https://home.tigertext.com`

    1. Typ in het vak **id (Entiteits-ID)** een URL met behulp van het volgende patroon:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > De **id-waarde (Entiteits-ID)** is niet echt. Werk deze waarde bij met de werkelijke id. Neem contact op met het [ondersteunings team van TigerText Secure Mess enger](mailto:prosupport@tigertext.com)om de waarde op te halen. U kunt ook verwijzen naar de patronen die worden weer gegeven in het deel venster **basis-SAML-configuratie** in de Azure Portal.

1. Selecteer in het deel venster **eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekening certificaat** de optie **downloaden** om de **federatieve meta gegevens-XML** te downloaden uit de opgegeven opties en op uw computer op te slaan.

    ![De optie voor XML-down load voor federatieve meta gegevens](common/metadataxml.png)

1. Kopieer de URL of Url's die u nodig hebt in de sectie **TigerText Secure Mess enger instellen** :

   * **Aanmeldings-URL**
   * **Azure AD-id**
   * **Afmeldings-URL**

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-tigertext-secure-messenger-single-sign-on"></a>Eenmalige aanmelding voor TigerText Secure Mess enger configureren

Voor het configureren van eenmalige aanmelding op de TigerText beveiligde Mess enger-zijde moet u het gedownloade XML-bestand met federatieve meta gegevens en de juiste gekopieerde Url's van de Azure Portal naar het [TigerText Secure Mess enger ondersteunings team](mailto:prosupport@tigertext.com)verzenden. Het TigerText Secure Mess enger-team zorgt ervoor dat de SAML SSO-verbinding aan beide zijden correct is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam Britta Simon maken in de Azure-portal.

1. Selecteer **Azure Active Directory**   > **gebruikers** > in het linkerdeel venster van de Azure Portal**alle gebruikers**.

    ![De opties gebruikers en alle gebruikers](common/users.png)

1. Selecteer boven aan het scherm **+ nieuwe gebruiker**.

    ![Optie nieuwe gebruiker](common/new-user.png)

1. Voer de volgende stappen uit in het deel venster van de **gebruiker** :

    ![Het deel venster gebruiker](common/user-properties.png)

    1. Voer in het vak **Naam****Britta Simon**in.
  
    1. Voer in het vak **gebruikers naam** **BrittaSimon\@\<yourcompanydomain> in.\< extensie>**. Bijvoorbeeld **BrittaSimon\@contoso.com**.

    1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .

    1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om eenmalige aanmelding van Azure te gebruiken door hen toegang te geven tot TigerText Secure Mess enger.

1. Selecteer in de Azure Portal **bedrijfs toepassingen** > **alle toepassingen** > **TigerText Secure Mess enger**.

    ![Deel venster ondernemings toepassingen](common/enterprise-applications.png)

1. Selecteer in de lijst toepassingen de optie **TigerText Secure Mess enger**.

    ![Beveiligde Mess enger TigerText in de lijst met toepassingen](common/all-applications.png)

1. Selecteer in het linkerdeel venster onder **beheren**de optie **gebruikers en groepen**.

    ![De optie gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **+ gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het deel venster **toewijzing toevoegen** .

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

1. Selecteer in het deel venster **gebruikers en groepen** de optie **Julia Simon** in de lijst met **gebruikers** en kies vervolgens **selecteren** onder aan het deel venster.

1. Als u een waarde voor een rol in de SAML-bevestiging verwacht, selecteert u in het deel venster **rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik onder aan het deel venster op **selecteren**.

1. Selecteer in het deel venster **toewijzing toevoegen** de optie **toewijzen**.

### <a name="create-a-tigertext-secure-messenger-test-user"></a>Een TigerText Secure Mess enger-test gebruiker maken

In deze sectie maakt u een gebruiker met de naam Julia Simon in TigerText Secure Mess enger. Werk met het [ondersteunings team van TigerText beveiligde Mess enger](mailto:prosupport@tigertext.com) om Julia Simon toe te voegen als gebruiker in TigerText Secure Mess enger. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie test u de configuratie van eenmalige aanmelding voor Azure AD met behulp van de portal mijn apps.

Wanneer u **TigerText Secure Mess enger** selecteert in de portal mijn apps, moet u automatisch worden aangemeld bij het TigerText Secure Mess enger-abonnement waarvoor u eenmalige aanmelding hebt ingesteld. Zie [apps openen en gebruiken in de portal mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie over de portal mijn apps.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
