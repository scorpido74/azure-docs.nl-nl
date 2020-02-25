---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met eenmalige aanmelding voor Skytap | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en eenmalige aanmelding voor Skytap.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d6cb7ab2-da1a-4015-8e6f-c0c47bb6210f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/13/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 33a8035f16f531dbb17177d1c2f4d5cd344e5a28
ms.sourcegitcommit: f27b045f7425d1d639cf0ff4bcf4752bf4d962d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/23/2020
ms.locfileid: "77565771"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-single-sign-on-for-skytap"></a>Zelf studie: Azure Active Directory SSO-integratie (single sign-on) met eenmalige aanmelding voor Skytap

In deze zelf studie leert u hoe u eenmalige aanmelding integreert voor Skytap met Azure Active Directory (Azure AD). Wanneer u eenmalige aanmelding integreert voor Skytap met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot eenmalige aanmelding voor Skytap.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij eenmalige aanmelding voor Skytap met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie, het Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de SaaS-app (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Eenmalige aanmelding voor Skytap-abonnement met eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Eenmalige aanmelding voor Skytap ondersteunt door SP en IDP geïnitieerde SSO.
* Nadat u eenmalige aanmelding voor Skytap hebt geconfigureerd, kunt u sessie beheer afdwingen. Dit beveiligt exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time. Sessie beheer wordt uitgebreid vanuit voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-single-sign-on-for-skytap-from-the-gallery"></a>Eenmalige aanmelding toevoegen voor Skytap uit de galerie

Als u de integratie van eenmalige aanmelding voor Skytap wilt configureren in azure AD, moet u eenmalige aanmelding voor Skytap in de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **eenmalige aanmelding voor Skytap** in het zoekvak.
1. Selecteer **eenmalige aanmelding voor Skytap** in het deel venster resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-single-sign-on-for-skytap"></a>Eenmalige aanmelding van Azure AD voor Skytap configureren en testen voor eenmalige aanmelding

Azure AD SSO configureren en testen met eenmalige aanmelding voor Skytap met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een gekoppelde relatie instellen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in eenmalige aanmelding voor Skytap.

Hier volgen de algemene stappen voor het configureren en testen van Azure AD SSO met eenmalige aanmelding voor Skytap:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.

    a. **[Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user)** eenmalige aanmelding van Azure ad te testen met B. Simon.

    b. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe om B. Simon in te scha kelen voor het gebruik van eenmalige aanmelding voor Azure AD.
1. **[Eenmalige aanmelding configureren voor SKYTAP SSO](#configure-single-sign-on-for-skytap-sso)** voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.

    a. **[Maak een eenmalige aanmelding voor Skytap test gebruiker](#create-single-sign-on-for-skytap-test-user)** als u een tegen hanger van B. Simon wilt hebben in eenmalige aanmelding voor Skytap. Deze tegen hanger is gekoppeld aan de Azure AD-weer gave van de gebruiker.
1. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina **eenmalige aanmelding voor de Skytap** -toepassings integratie de sectie **beheren** . Selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** het potlood pictogram voor de basis- **SAML-configuratie** om de instellingen te bewerken.

   ![Scherm afbeelding van het instellen van eenmalige aanmelding met de SAML-pagina, waarbij het potlood pictogram is gemarkeerd](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. Typ in het tekstvak **id** een URL die gebruikmaakt van het volgende patroon: `http://pingone.com/<custom EntityID>`

    b. Typ in het tekstvak **antwoord-URL** een URL die gebruikmaakt van het volgende patroon: `https://sso.connect.pingidentity.com/sso/sp/ACS.saml2`

1. Selecteer **extra Url's instellen**en voer de volgende stappen uit als u de toepassing in de modus door **SP** gestart wilt configureren:

    a. Typ in het tekstvak **URL voor aanmelding** een URL die gebruikmaakt van het volgende patroon: `https://sso.connect.pingidentity.com/sso/sp/initsso?saasid=<saasid>&idpid=<idpid>`

    
    b. Typ in het tekstvak **Relay-status** een URL die gebruikmaakt van het volgende patroon: `https://pingone.com/1.0/<custom ID>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Deze waarden bijwerken met de daad werkelijke id, antwoord-URL, aanmeldings-URL en relay-status. Neem contact op met de [eenmalige aanmelding voor het Skytap-client ondersteunings team](mailto:support@skytap.com) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML**. Selecteer **downloaden** om het meta gegevensbestand te downloaden en op uw computer op te slaan.

    ![Scherm afbeelding van de koppeling voor het downloaden van het certificaat](common/metadataxml.png)

1. Op de sectie **eenmalige aanmelding instellen voor Skytap** kopieert u de juiste URL of url's, op basis van uw vereiste.

    ![Scherm opname van configuratie-Url's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer **Azure Active Directory** > **gebruikers** > **alle gebruikers**in het linkerdeel venster van de Azure Portal.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het veld **wacht woord** .
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan eenmalige aanmelding voor Skytap.

1. Selecteer in de Azure Portal **bedrijfs toepassingen** > **alle toepassingen**.
1. Selecteer **eenmalige aanmelding voor Skytap**in de lijst toepassingen.
1. Zoek op de pagina overzicht van de app de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![Scherm afbeelding van de sectie beheren met gebruikers en groepen gemarkeerd](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer in het dialoog venster **toewijzing toevoegen** de optie **gebruikers en groepen**.

    ![Scherm opname van de pagina gebruikers en groepen, met gemarkeerde gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers. Kies vervolgens de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens de knop **selecteren** onder aan het scherm.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-single-sign-on-for-skytap-sso"></a>Eenmalige aanmelding configureren voor Skytap SSO

Als u eenmalige aanmelding wilt configureren voor eenmalige aanmelding voor Skytap, moet u het gedownloade **XML-bestand met federatieve meta gegevens**en de juiste gekopieerde url's verzenden van de Azure Portal naar de [eenmalige aanmelding voor Skytap client ondersteunings team](mailto:support@skytap.com). Het team configureert deze instelling dusdanig dat de SAML SSO-verbinding goed is ingesteld aan beide zijden.


### <a name="create-single-sign-on-for-skytap-test-user"></a>Eenmalige aanmelding maken voor Skytap test gebruiker

In deze sectie maakt u een gebruiker met de naam B. Simon in eenmalige aanmelding voor Skytap. Werk met de [eenmalige aanmelding voor het Skytap-client ondersteunings team](mailto:support@skytap.com) om de gebruikers toe te voegen aan de eenmalige aanmelding voor Skytap-platform. U kunt eenmalige aanmelding pas gebruiken als u gebruikers maakt en activeert.

## <a name="test-sso"></a>SSO testen 

In deze sectie kunt u de configuratie van eenmalige aanmelding voor Azure AD testen met behulp van het toegangs venster.

Wanneer u de tegel eenmalige aanmelding voor Skytap in het toegangs venster selecteert, moet u automatisch worden aangemeld bij de eenmalige aanmelding voor Skytap waarvoor u SSO hebt ingesteld. Zie [Inleiding tot het toegangs venster](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie.

## <a name="additional-resources"></a>Aanvullende resources

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer vertraging met Azure AD](https://aad.portal.azure.com/)

