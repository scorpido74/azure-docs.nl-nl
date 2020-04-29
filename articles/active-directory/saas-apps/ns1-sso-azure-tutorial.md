---
title: 'Zelf studie: Azure Active Directory SSO-integratie (single sign-on) met NS1 SSO voor Azure | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en NS1 SSO voor Azure.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 24a1afb6-b8b6-4787-bd4b-8fe3a32f8def
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5e917265f4bf6f857a0eada2433f0a0e4e24d7c5
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77565567"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ns1-sso-for-azure"></a>Zelf studie: Azure Active Directory SSO-integratie (single sign-on) met NS1 SSO voor Azure

In deze zelf studie leert u hoe u NS1 SSO voor Azure kunt integreren met Azure Active Directory (Azure AD). Wanneer u NS1 SSO voor Azure integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot NS1 SSO voor Azure.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij NS1 SSO voor Azure met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie, het Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de SaaS-app (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* NS1 SSO voor Azure-abonnement met eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* NS1 SSO voor Azure ondersteunt SSO met SP en IDP.
* Nadat u NS1 SSO voor Azure hebt geconfigureerd, kunt u sessie beheer afdwingen. Dit beveiligt exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time. Sessie beheer wordt uitgebreid vanuit voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).


## <a name="add-ns1-sso-for-azure-from-the-gallery"></a>NS1 SSO voor Azure toevoegen vanuit de galerie

Als u de integratie van NS1 SSO voor Azure wilt configureren in azure AD, moet u NS1 SSO voor Azure toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **ns1 SSO voor Azure** in het zoekvak.
1. Selecteer **ns1 SSO voor Azure** in het deel venster resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-ns1-sso-for-azure"></a>Eenmalige aanmelding van Azure AD voor NS1 SSO voor Azure configureren en testen

Azure AD SSO configureren en testen met NS1 SSO voor Azure met behulp van een test gebruiker met de naam **B. Simon**. Als u SSO wilt gebruiken, moet u een gekoppelde relatie instellen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in NS1 SSO voor Azure.

Hier volgen de algemene stappen voor het configureren en testen van Azure AD SSO met NS1 SSO voor Azure:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.

    a. **[Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user)** eenmalige aanmelding van Azure ad te testen met B. Simon.

    b. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe om B. Simon in te scha kelen voor het gebruik van eenmalige aanmelding voor Azure AD.
1. **[CONFIGUREER ns1 SSO voor Azure SSO](#configure-ns1-sso-for-azure-sso)** om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.

    a. **[Maak een ns1-SSO voor Azure test gebruiker](#create-an-ns1-sso-for-azure-test-user)** om een soort tegen partij te hebben van B. Simon in ns1 SSO voor Azure. Deze tegen hanger is gekoppeld aan de Azure AD-weer gave van de gebruiker.
1. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **ns1 SSO voor Azure** Application Integration de sectie **beheren** . Selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** het potlood pictogram voor de basis- **SAML-configuratie** om de instellingen te bewerken.

   ![Scherm afbeelding van het instellen van eenmalige aanmelding met de SAML-pagina, waarbij het potlood pictogram is gemarkeerd](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. Typ de volgende URL in het tekstvak **id** :`https://api.nsone.net/saml/metadata`

    b. Typ in het tekstvak **antwoord-URL** een URL die gebruikmaakt van het volgende patroon:`https://api.nsone.net/saml/sso/<ssoid>`

1. Selecteer **extra Url's instellen**en voer de volgende stap uit als u de toepassing in de modus door **SP** gestart wilt configureren:

    Typ in het tekstvak **URL voor aanmelding** de volgende URL:`https://my.nsone.net/#/login/sso`

    > [!NOTE]
    > De waarde van de antwoord-URL is niet echt. Werk de waarde van de antwoord-URL bij met de werkelijke antwoord-URL. Neem contact op met het [ondersteunings team van ns1 SSO voor Azure](mailto:techops@nsone.net) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De NS1 SSO voor Azure-toepassing verwacht de SAML-beweringen in een specifieke indeling. Configureer de volgende claims voor deze toepassing. U kunt de waarden van deze kenmerken beheren in de sectie **gebruikers kenmerken & claims** op de pagina voor de integratie van de toepassing. Op de pagina **eenmalige aanmelding met SAML instellen** selecteert u het potlood pictogram om het dialoog venster **gebruikers kenmerken** te openen.

    ![Scherm opname van gebruikers kenmerken & sectie claims, waarbij het potlood pictogram is gemarkeerd](./media/ns1-sso-for-azure-tutorial/attribute-edit-option.png)

1. Selecteer de naam van het kenmerk om de claim te bewerken.

    ![Scherm opname van gebruikers kenmerken & sectie claims, waarbij de kenmerk naam is gemarkeerd](./media/ns1-sso-for-azure-tutorial/attribute-claim-edit.png)

1. **Trans formatie**selecteren.

    ![Scherm opname van de sectie claim beheren, met de markering trans formatie](./media/ns1-sso-for-azure-tutorial/prefix-edit.png)

1. Voer de volgende stappen uit in de sectie **trans formatie beheren** :

    ![Scherm opname van de sectie trans formatie beheren waarbij verschillende velden zijn gemarkeerd](./media/ns1-sso-for-azure-tutorial/prefix-added.png)

    1. Selecteer **ExactMailPrefix ()** als **trans formatie**.

    1. Selecteer **User. userPrincipalName** als **para meter 1**.

    1. Selecteer **Toevoegen**.

    1. Selecteer **Opslaan**.

1. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de knop kopiëren. Hiermee wordt de **URL voor de federatieve meta gegevens** van de app gekopieerd en op uw computer opgeslagen.

    ![Scherm opname van het SAML-handtekening certificaat, met de knop kopiëren gemarkeerd](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer **Azure Active Directory** > **gebruikers** > van**alle gebruikers**in het linkerdeel venster van de Azure Portal.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :

   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het veld **wacht woord** .
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan NS1 SSO voor Azure.

1. Selecteer in het Azure Portal**alle toepassingen**in **bedrijfs toepassingen** > .
1. Selecteer in de lijst toepassingen de optie **ns1 SSO voor Azure**.
1. Zoek op de pagina overzicht van de app de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![Scherm afbeelding van de sectie beheren met gebruikers en groepen gemarkeerd](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer in het dialoog venster **toewijzing toevoegen** de optie **gebruikers en groepen**.

    ![Scherm opname van de pagina gebruikers en groepen, met gemarkeerde gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers. Kies vervolgens de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens de knop **selecteren** onder aan het scherm.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-ns1-sso-for-azure-sso"></a>NS1 SSO voor Azure SSO configureren

Als u eenmalige aanmelding wilt configureren op de NS1 SSO voor Azure, moet u de URL voor de app-federatieve meta gegevens verzenden naar het [ondersteunings team ns1 SSO voor Azure](mailto:techops@nsone.net). Het team configureert deze instelling dusdanig dat de SAML SSO-verbinding goed is ingesteld aan beide zijden.

### <a name="create-an-ns1-sso-for-azure-test-user"></a>Een NS1-SSO voor Azure-test gebruiker maken

In deze sectie maakt u een gebruiker met de naam B. Simon in NS1 SSO voor Azure. Werk met het ondersteunings team NS1 SSO voor Azure om de gebruikers toe te voegen aan de NS1 SSO voor Azure-platform. U kunt eenmalige aanmelding pas gebruiken als u gebruikers maakt en activeert.

## <a name="test-sso"></a>SSO testen 

In deze sectie kunt u de configuratie van eenmalige aanmelding voor Azure AD testen met behulp van het toegangs venster.

Wanneer u de tegel NS1 SSO voor Azure in het toegangs venster selecteert, moet u automatisch worden aangemeld bij de NS1 SSO voor Azure waarvoor u SSO hebt ingesteld. Zie [Inleiding tot het toegangs venster](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [NS1 SSO voor Azure proberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)