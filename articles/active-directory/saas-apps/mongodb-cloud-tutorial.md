---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met MongoDB Cloud | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en MongoDB Cloud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ef392044-235b-4d80-8a33-eeba9b142849
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef8094464b428f1f46017b0d4abaac5aeb1fa428
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82024508"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mongodb-cloud"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met MongoDB Cloud

In deze zelf studie leert u hoe u MongoDB Cloud kunt integreren met Azure Active Directory (Azure AD). Wanneer u MongoDB-Cloud integreert met Azure AD, kunt u het volgende doen:

* Beheer in azure AD die toegang heeft tot MongoDB Cloud, MongoDB Atlas, de MongoDB Community, MongoDB University en MongoDB-ondersteuning.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij MongoDB Cloud met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de SaaS-app (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Een MongoDB-Cloud organisatie die is ingeschakeld voor eenmalige aanmelding (SSO), u kunt zich aanmelden voor een [gratis cluster](https://www.mongodb.com/cloud)

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* MongoDB Cloud ondersteunt SSO die door **SP** en **IDP** is geïnitieerd.
* MongoDB Cloud ondersteunt **just-in-time** -gebruikers inrichting.
* Nadat u de MongoDB-Cloud hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessie beheer wordt uitgebreid vanuit voorwaardelijke toegang. Zie [informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)voor meer informatie.

## <a name="add-mongodb-cloud-from-the-gallery"></a>Een MongoDB-Cloud toevoegen vanuit de galerie

Als u de integratie van MongoDB Cloud wilt configureren in azure AD, moet u MongoDB Cloud toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer **Azure Active Directory**in het linkerdeel venster.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **MongoDb Cloud** in het zoekvak.
1. Selecteer **MongoDb Cloud** in de resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-mongodb-cloud"></a>Eenmalige aanmelding voor Azure AD voor MongoDB Cloud configureren en testen

Configureer en test Azure AD SSO met MongoDB Cloud door gebruik te maken van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een gekoppelde relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in MongoDB Cloud.

Als u Azure AD SSO wilt configureren en testen met MongoDB Cloud, voltooit u de volgende bouw stenen:

1. [Configureer Azure AD SSO](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
    1. [Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user) eenmalige aanmelding van Azure ad te testen met B. Simon.
    1. [Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user) toe om B. Simon in te scha kelen voor het gebruik van eenmalige aanmelding voor Azure AD.
1. [Configureer MongoDb Cloud-SSO](#configure-mongodb-cloud-sso) voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. [Maak een MongoDb-test gebruiker](#create-a-mongodb-cloud-test-user) in de cloud om een equivalent van B. Simon in MongoDb-Cloud te hebben, gekoppeld aan de Azure AD-representatie van de gebruiker.
1. [Test SSO](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **MongoDb Cloud** Application Integration de sectie **beheren** . Selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** het potlood pictogram voor de basis- **SAML-configuratie** om de instellingen te bewerken.

   ![Scherm afbeelding van het instellen van eenmalige aanmelding met de SAML-pagina, waarbij het potlood pictogram is gemarkeerd](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. Typ in het tekstvak **id** een URL die gebruikmaakt van het volgende patroon:`https://www.okta.com/saml2/service-provider/<Customer_Unique>`

    b. Typ in het tekstvak **antwoord-URL** een URL die gebruikmaakt van het volgende patroon:`https://auth.mongodb.com/sso/saml2/<Customer_Unique>`

1. Selecteer **extra Url's instellen**en voer de volgende stap uit als u de toepassing in de modus door **SP** gestart wilt configureren:

    Typ in het tekstvak **URL voor aanmelding** een URL die gebruikmaakt van het volgende patroon:`https://cloud.mongodb.com/sso/<Customer_Unique>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de daad werkelijke id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteunings team van MongoDb cloud client](https://support.mongodb.com/)om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De MongoDB-Cloud toepassing verwacht dat de SAML-bevestigingen een specifieke indeling hebben. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![Scherm opname van standaard kenmerken](common/default-attributes.png)

1. Naast de voor gaande kenmerken verwacht de Cloud toepassing MongoDB nog enkele kenmerken die moeten worden door gegeven in het SAML-antwoord. Deze kenmerken zijn ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.
    
    | Naam | |  Bronkenmerk|
    | ---------------| --------------- | --------- |
    | e-mail | | user.userprincipalname |
    | voornaam | | user.givenname |
    | achternaam | | user.surname |

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML**. Selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![Scherm afbeelding van het certificaat van het SAML-Ondertekeningscertificaat, met de download koppeling gemarkeerd](common/metadataxml.png)

1. Kopieer in het gedeelte **MongoDb-Cloud instellen** de juiste url's, op basis van uw vereiste.

    ![Scherm opname van de Mongo DB-Cloud sectie met de Url's die zijn gemarkeerd](common/copy-configuration-urls.png)
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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot de MongoDB-Cloud.

1. Selecteer in het Azure Portal**alle toepassingen**in **bedrijfs toepassingen** > .
1. Selecteer in de lijst toepassingen de optie **MongoDb Cloud**.
1. Zoek op de pagina overzicht van de app de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![Scherm afbeelding van de sectie beheren met gebruikers en groepen gemarkeerd](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer vervolgens in het dialoog venster **toewijzing toevoegen** de optie **gebruikers en groepen**.

    ![Scherm opname van de pagina gebruikers en groepen, met gemarkeerde gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst met gebruikers. Kies vervolgens **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens **selecteren** onder aan het scherm.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-mongodb-cloud-sso"></a>MongoDB Cloud SSO configureren

Als u eenmalige aanmelding wilt configureren op de MongoDB, moet u de juiste Url's hebben gekopieerd van de Azure Portal. U moet ook de Federatie toepassing voor uw MongoDB-Cloud organisatie configureren. Volg de instructies in de [MongoDb Cloud-documentatie](https://docs.atlas.mongodb.com/security/federated-auth-azure-ad/). Als u een probleem ondervindt, neemt u contact op met het [ondersteunings team van MongoDb Cloud](https://support.mongodb.com/).

### <a name="create-a-mongodb-cloud-test-user"></a>Een MongoDB-Cloud test gebruiker maken

MongoDB Cloud ondersteunt just-in-time-gebruikers inrichting, die standaard is ingeschakeld. U hoeft verder niets te doen. Als een gebruiker zich nog niet in MongoDB Cloud bevindt, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>SSO testen 

In deze sectie kunt u de configuratie van eenmalige aanmelding voor Azure AD testen met behulp van het toegangs venster.

Wanneer u de tegel MongoDB Cloud in het toegangs venster selecteert, wordt u automatisch aangemeld bij de MongoDB-Cloud waarvoor u SSO hebt ingesteld. Zie voor meer informatie [Aanmelden en apps starten vanuit de portal mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Meld u aan voor MongoDB Atlas op Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/mongodb.mongodb_atlas_azure_08082019?tab=Overview)

- [Probeer MongoDB Cloud met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [MongoDB-Cloud beveiligen met geavanceerde zicht baarheid en besturings elementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

