---
title: 'Zelf studie: integratie Azure Active Directory met SAP Qualtrics | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SAP Qualtrics.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4df889ab-2685-4d15-a163-1ba26567eeda
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.openlocfilehash: 2e27d020bd25f234d084fba770e234bdccb40a99
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81682379"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-qualtrics"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met SAP-Qualtrics

In deze zelf studie leert u hoe u SAP Qualtrics integreert met Azure Active Directory (Azure AD). Wanneer u SAP Qualtrics integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot SAP-Qualtrics.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij SAP Qualtrics met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de SaaS-app (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Een SAP Qualtrics-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* SAP Qualtrics ondersteunt door **SP** en **IDP** geïnitieerde SSO.
* SAP Qualtrics ondersteunt **just-in-time** -gebruikers inrichting.
* Nadat u SAP Qualtrics hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessie beheer wordt uitgebreid vanuit voorwaardelijke toegang. Zie [informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)voor meer informatie.

## <a name="add-sap-qualtrics-from-the-gallery"></a>SAP Qualtrics toevoegen vanuit de galerie

Als u de integratie van SAP Qualtrics wilt configureren in azure AD, moet u SAP Qualtrics toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer **Azure Active Directory**in het linkerdeel venster.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **SAP Qualtrics** in het zoekvak.
1. Selecteer **SAP Qualtrics** van resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-qualtrics"></a>Eenmalige aanmelding van Azure AD voor SAP Qualtrics configureren en testen

Azure AD SSO met SAP Qualtrics configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een gekoppelde relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SAP Qualtrics.

Als u Azure AD SSO wilt configureren en testen met SAP Qualtrics, voltooit u de volgende bouw stenen:

1. [Configureer Azure AD SSO](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
    1. [Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user) eenmalige aanmelding van Azure ad te testen met B. Simon.
    1. [Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user) toe om B. Simon in te scha kelen voor het gebruik van eenmalige aanmelding voor Azure AD.
1. [SAP QUALTRICS SSO configureren](#configure-sap-qualtrics-sso) voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. [Maak een SAP Qualtrics-test gebruiker](#create-sap-qualtrics-test-user) om een soort tegen te brengen van B. Simon in SAP Qualtrics, gekoppeld aan de Azure AD-representatie van de gebruiker.
1. [Test SSO](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **SAP Qualtrics** Application Integration de sectie **Manage** . Selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** het potlood pictogram voor de basis- **SAML-configuratie** om de instellingen te bewerken.

   ![Scherm afbeelding van het instellen van eenmalige aanmelding met de SAML-pagina, waarbij het potlood pictogram is gemarkeerd](common/edit-urls.png)

1. Als u de toepassing wilt configureren in de gestarte modus **IDP** , voert u op de pagina **eenmalige aanmelding met SAML instellen** de waarden voor de volgende velden in:
    
    a. Typ in het tekstvak **id** een URL die gebruikmaakt van het volgende patroon:

    `https://< DATACENTER >.qualtrics.com`
   
    b. Typ in het tekstvak **antwoord-URL** een URL die gebruikmaakt van het volgende patroon:

    `https://< DATACENTER >.qualtrics.com/login/v1/sso/saml2/default-sp`

    c. Typ in het tekstvak **Relay-status** een URL die gebruikmaakt van het volgende patroon:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

1. Selecteer **extra Url's instellen**en voer de volgende stap uit als u de toepassing in de modus door **SP** gestart wilt configureren:

    Typ in het tekstvak **URL voor aanmelden** een URL die gebruikmaakt van het volgende patroon:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Deze waarden bijwerken met de werkelijke aanmeldings-URL, id, antwoord-URL en relay-status. Neem contact op met het [ondersteunings team](https://www.qualtrics.com/support/)van de Qualtrics-client om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , het Kopieer pictogram voor het kopiëren van de URL voor de **federatieve meta gegevens** van de app en sla deze op uw computer op.

    ![Scherm opname van het SAML-handtekening certificaat, waarbij het pictogram kopiëren is gemarkeerd](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer **Azure Active Directory** > **gebruikers** > van**alle gebruikers**in het linkerdeel venster van de Azure Portal.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en schrijf het wacht woord vervolgens op.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan SAP Qualtrics.

1. Selecteer in het Azure Portal**alle toepassingen**in **bedrijfs toepassingen** > .
1. Selecteer **SAP Qualtrics**in de lijst toepassingen.
1. Zoek op de pagina overzicht van de app de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![Scherm afbeelding van de sectie beheren met gebruikers en groepen gemarkeerd](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer vervolgens in het dialoog venster **toewijzing toevoegen** de optie **gebruikers en groepen**.

    ![Scherm opname van de pagina gebruikers en groepen, met gemarkeerde gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst met gebruikers. Kies vervolgens **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens **selecteren** onder aan het scherm.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-sap-qualtrics-sso"></a>SAP Qualtrics SSO configureren

Als u eenmalige aanmelding wilt configureren op de SAP Qualtrics-zijde, verzendt u de gekopieerde **app federatieve meta gegevens-URL** van de Azure Portal naar het [SAP Qualtrics-ondersteunings team](https://www.qualtrics.com/support/). Het ondersteunings team zorgt ervoor dat de SAML SSO-verbinding aan beide zijden correct is ingesteld.

### <a name="create-sap-qualtrics-test-user"></a>SAP Qualtrics-test gebruiker maken

SAP Qualtrics ondersteunt just-in-time-gebruikers inrichting, dat standaard is ingeschakeld. U hoeft verder niets te doen. Als een gebruiker nog niet bestaat in SAP Qualtrics, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>SSO testen 

In deze sectie kunt u de configuratie van eenmalige aanmelding voor Azure AD testen met behulp van het toegangs venster.

Wanneer u de tegel SAP Qualtrics in het toegangs venster selecteert, wordt u automatisch aangemeld bij de SAP-Qualtrics waarvoor u SSO hebt ingesteld. Zie voor meer informatie [Aanmelden en apps starten vanuit de portal mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [SAP-Qualtrics proberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [SAP Qualtrics beveiligen met geavanceerde zicht baarheid en controles](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

