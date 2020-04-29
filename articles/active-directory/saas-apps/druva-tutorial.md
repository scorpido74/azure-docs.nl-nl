---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Druva | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Druva.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f019d818fb5a017d184bda8d773eb0aaf0f3645a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78944403"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-druva"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Druva

In deze zelf studie leert u hoe u Druva integreert met Azure Active Directory (Azure AD). Wanneer u Druva integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Druva.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Druva met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Druva-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Druva ondersteunt door **IDP** GEÏNITIEERDe SSO
* Zodra u Druva SSO hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessie beheer wordt uitgebreid vanuit voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> De id van deze toepassing is een vaste teken reeks waarde zodat slechts één exemplaar in één Tenant kan worden geconfigureerd.

## <a name="adding-druva-from-the-gallery"></a>Druva uit de galerie toevoegen

Om de integratie van Druva in Azure Active Directory te configureren, moet u Druva uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Druva** in het zoekvak.
1. Selecteer **Druva** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-druva"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Druva

Azure AD SSO met Druva configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Druva.

Als u Azure AD SSO wilt configureren en testen met Druva, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[DRUVA SSO configureren](#configure-druva-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een Druva-test gebruiker](#create-druva-test-user)** -om een equivalent van B. Simon in Druva te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **Druva** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. Typ in het tekstvak **id (Entiteits-ID)** de teken reeks waarde `DCP-login`:.
    
    b. Typ in het tekstvak **antwoord-URL (assertion Consumer Service-URL)** de URL: `https://cloud.druva.com/wrsaml/consume`.

1. Klik op **Opslaan**.

1. De Druva-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Daarnaast verwacht Druva toepassing nog maar weinig kenmerken die worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    | Naam | Bronkenmerk|
    | ------------------- | -------------------- |
    | emailAddress | gebruiker. e-mail adres |
    | druva_auth_token | SSO-token gegenereerd door de DCP-beheer console, zonder aanhalings tekens.  Bijvoorbeeld: X-XXXXX-XXXX-S-A-M-P-L-E + TXOXKXEXNX =. Azure plaatst automatisch aanhalings tekens rond het verificatie token. |

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **Druva instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Druva.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst met toepassingen **Druva**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-druva-sso"></a>Druva SSO configureren

1. Meld u in een ander webbrowser venster als beheerder aan bij uw Druva-bedrijfs site.

1. Klik op het logo van de Druva in de linkerbovenhoek en klik vervolgens op **Druva Cloud instellingen**.

    ![Instellingen](./media/druva-tutorial/ic795091.png "Instellingen")

1. Klik op het tabblad **eenmalige aanmelding** op **bewerken**.

    ![Instellingen voor eenmalige aanmelding](./media/druva-tutorial/ic795092.png "Instellingen voor eenmalige aanmelding")

1. Voer de volgende stappen uit op de pagina **instellingen voor eenmalige aanmelding bewerken** :

    ![Instellingen voor eenmalige aanmelding](./media/druva-tutorial/ic795095.png "Instellingen voor eenmalige aanmelding")

    1. Plak in het tekstvak **Aanmeldings-URL van ID-provier** de waarde van de **Aanmeldings-URL** in die u hebt gekopieerd uit de Azure Portal.

    1. Open uw base-64 gecodeerde certificaat in Kladblok, kopieer de inhoud ervan naar het klembord en plak deze naar het tekstvak **Certificaat ID-provider**

       > [!NOTE]
       > Als u eenmalige aanmelding voor beheerders wilt inschakelen, selecteert u **beheerders meldt zich aan bij Druva Cloud via SSO-provider** en **staat u failsafe toegang tot Druva Cloud Administrators (aanbevolen)** selectie vakjes toe. Druva raadt aan om failsafe in te scha kelen **voor beheerders** , zodat ze toegang hebben tot de DCP-console in het geval van storingen in IDP. Ook kunnen de beheerders het SSO-en DCP-wacht woord gebruiken om toegang te krijgen tot de DCP-console.

    1. Klik op **Opslaan**. Hierdoor is de toegang tot Druva-Cloud platform via SSO mogelijk.

### <a name="create-druva-test-user"></a>Druva-testgebruiker maken

In deze sectie wordt een gebruiker met de naam B. Simon gemaakt in Druva. Druva biedt ondersteuning voor Just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Druva, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Druva in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van Druva waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Druva met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)