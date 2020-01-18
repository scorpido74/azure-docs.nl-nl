---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met uniFLOW online | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en uniFLOW online.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28313d27-638c-4d50-92ad-d093f2ae9ecf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f26af813fcd4032aabce2305ac8845307d1fca65
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76262119"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-uniflow-online"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met uniFLOW online

In deze zelf studie leert u hoe u uniFLOW online integreert met Azure Active Directory (Azure AD). Wanneer u uniFLOW online integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot uniFLOW online.
* Stel uw gebruikers in staat om zich online aan te melden bij uniFLOW met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* uniFLOW online Tenant.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* uniFLOW online ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-uniflow-online-from-the-gallery"></a>UniFLOW online toevoegen vanuit de galerie

Als u de integratie van uniFLOW online wilt configureren in azure AD, moet u uniFLOW online toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **uniFLOW online** in het zoekvak.
1. Selecteer **UniFLOW online** in het resultaten paneel en voeg de app vervolgens toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-uniflow-online"></a>Eenmalige aanmelding voor Azure AD voor uniFLOW online configureren en testen

Configureer en test Azure AD SSO met uniFLOW online met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in uniFLOW online.

Als u Azure AD SSO wilt configureren en testen met uniFLOW online, voert u de volgende bouw stenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
   1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
   1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[UniFLOW online-SSO configureren](#configure-uniflow-online-sso)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Meld u online aan bij uniFLOW met behulp van de gemaakte test gebruiker](#sign-in-to-uniflow-online-using-the-created-test-user)** -om gebruikers aanmelding te testen aan de kant van de toepassing.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **uniFLOW online** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon:

    | | |
    |-|-|
    | `https://<tenant_domain_name>.eu.uniflowonline.com`|
    | `https://<tenant_domain_name>.us.uniflowonline.com`|
    | `https://<tenant_domain_name>.sg.uniflowonline.com`|
    | `https://<tenant_domain_name>.jp.uniflowonline.com`|
    | `https://<tenant_domain_name>.au.uniflowonline.com`|

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon:

    | | |
    |-|-|
    | `https://<tenant_domain_name>.eu.uniflowonline.com`|
    | `https://<tenant_domain_name>.us.uniflowonline.com`|
    | `https://<tenant_domain_name>.sg.uniflowonline.com`|
    | `https://<tenant_domain_name>.jp.uniflowonline.com`|
    | `https://<tenant_domain_name>.au.uniflowonline.com`|

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met het [ondersteunings team van UniFLOW online](mailto:support@nt-ware.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar de patronen die worden weer gegeven in de sectie **basis configuratie van SAML** in de Azure portal of verwijzen naar de antwoord-URL die wordt weer gegeven in uw uniFLOW online-Tenant.

1. uniFLOW online-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Daarnaast verwacht uniFLOW online toepassing nog maar weinig kenmerken om te worden door gegeven in een SAML-respons die hieronder wordt weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    | Name |  Bronkenmerk|
    | -----------| --------------- |
    | displayname | user.displayname |
    | naam | User. onpremisessamaccountname |

   > [!NOTE]
   > Het kenmerk `user.onpremisessamaccountname` bevat alleen een waarde als uw Azure AD-gebruikers worden gesynchroniseerd vanuit een lokale Windows-Active Directory.

1. Klik op de pagina **eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekening certificaat** op de knop kopiëren om de URL van de **app Federation-meta gegevens** te kopiëren en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan uniFLOW online.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **UniFLOW online**in de lijst toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

   ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

> [!NOTE]
> Als u wilt dat alle gebruikers toegang krijgen tot de toepassing zonder hand matige toewijzing, gaat u naar de sectie **beheren** en selecteert u **Eigenschappen**. Wijzig vervolgens de vereiste para meter voor de **gebruikers toewijzing** in **Nee**.

## <a name="configure-uniflow-online-sso"></a>UniFLOW online-SSO configureren

1. Meld u in een ander webbrowser venster aan bij uniFLOW online-website als beheerder.

1. Selecteer tabblad **gebruiker** vanuit het navigatie venster aan de linkerkant.

    ![uniFLOW online configureren](./media/uniflow-online-tutorial/configure1.png)

1. Klik op **id-providers**.

    ![uniFLOW online configureren](./media/uniflow-online-tutorial/configure2.png)

1. Klik op **ID-provider toevoegen**.

    ![uniFLOW online configureren](./media/uniflow-online-tutorial/configure3.png)

1. Voer de volgende stappen uit in de sectie **ID-provider toevoegen** :


    ![uniFLOW online configureren](./media/uniflow-online-tutorial/configure4.png)

    a. Voer de weergave naam in, bijvoorbeeld: *AZUREAD SSO*.

    b. Voor **provider type**selecteert u de optie voor **WS-** inschakeling in de vervolg keuzelijst.

    c. Selecteer **Azure Active Directory** optie in de vervolg keuzelijst voor **WS-type**.

    d. Klik op **Opslaan**.

1. Voer op het tabblad **Algemeen** de volgende stappen uit:

    ![uniFLOW online configureren](./media/uniflow-online-tutorial/configure5.png)

    a. Voer de weergave naam in, bijvoorbeeld: *AZUREAD SSO*.

    b. Selecteer de optie **van URL** voor de **meta gegevens**van de ADFS-federatie.

    c. Plak in het tekstvak **URL voor federatieve meta** gegevens de waarde van de URL voor de **federatieve meta gegevens** van de app die u hebt gekopieerd van de Azure Portal.

    d. Selecteer **ID-provider** als **ingeschakeld**.

    e. Selecteer **automatische gebruikers registratie** als **geactiveerd**.

    f. Klik op **Opslaan**.

### <a name="sign-in-to-uniflow-online-using-the-created-test-user"></a>Meld u online aan bij uniFLOW met de gemaakte test gebruiker

1. In een ander browser venster gaat u naar de uniFLOW online-URL voor uw Tenant.

1. Selecteer de eerder gemaakte ID-provider om u aan te melden via uw Azure AD-exemplaar.

1. Meld u aan met de test gebruiker.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer uniFLOW online met Azure AD](https://aad.portal.azure.com/)
