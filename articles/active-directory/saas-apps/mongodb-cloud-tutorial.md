---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met MongoDB Cloud | Microsoft Documenten'
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
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82024508"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mongodb-cloud"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met MongoDB Cloud

In deze zelfstudie leert u hoe u MongoDB Cloud integreren met Azure Active Directory (Azure AD). Wanneer u MongoDB Cloud integreert met Azure AD, u het als:

* Controle in Azure AD die toegang heeft tot MongoDB Cloud, MongoDB Atlas, de MongoDB-community, De MongoDB-universiteit en MongoDB-ondersteuning.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij MongoDB Cloud met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de App Voor software as a service (SaaS) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Een MongoDB Cloud-organisatie die is ingeschakeld voor single sign-on (SSO), u zich aanmelden voor een [gratis cluster](https://www.mongodb.com/cloud)

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* MongoDB Cloud ondersteunt **SP** en **IDP** heeft SSO geïnitieerd.
* MongoDB Cloud ondersteunt **Just In Time** gebruikersinrichting.
* Nadat u MongoDB Cloud hebt geconfigureerd, u sessiebeheer afdwingen, wat exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermt. Sessiebeheer strekt zich uit van voorwaardelijke toegang. Zie Meer informatie voor meer informatie [over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-mongodb-cloud-from-the-gallery"></a>MongoDB Cloud toevoegen vanuit de galerie

Als u de integratie van MongoDB Cloud in Azure AD wilt configureren, moet u MongoDB Cloud vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkerdeelvenster **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **MongoDB Cloud** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **MongoDB Cloud** in de resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-mongodb-cloud"></a>Azure AD-aanmelding voor MongoDB Cloud configureren en testen

Azure AD SSO configureren en testen met MongoDB Cloud, met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een gekoppelde relatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in MongoDB Cloud.

Als u Azure AD SSO wilt configureren en testen met MongoDB Cloud, voert u de volgende bouwstenen uit:

1. [Configureer Azure AD SSO](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
    1. [Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user) om Azure AD-enkele aanmelding met B.Simon te testen.
    1. [Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user) toe om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. [Configureer MongoDB Cloud SSO](#configure-mongodb-cloud-sso) om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. [Maak een MongoDB Cloud-testgebruiker](#create-a-mongodb-cloud-test-user) om een tegenhanger van B.Simon in MongoDB Cloud te hebben, gekoppeld aan de Azure AD-weergave van de gebruiker.
1. [Test SSO](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **MongoDB** Cloud-toepassingsintegratie de sectie **Beheren.** Selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Selecteer op de pagina **Eén aanmelding instellen met SAML** het potloodpictogram voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Schermafbeelding van Eenmalige aanmelding instellen met SAML-pagina, met potloodpictogram gemarkeerd](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    a. Typ **in** het tekstvak Id een URL die het volgende patroon gebruikt:`https://www.okta.com/saml2/service-provider/<Customer_Unique>`

    b. Typ in het tekstvak **URL beantwoorden** een URL die het volgende patroon gebruikt:`https://auth.mongodb.com/sso/saml2/<Customer_Unique>`

1. Selecteer **Extra URL's instellen**en voer de volgende stap uit als u de toepassing wilt configureren in de gestarte **SP-modus:**

    Typ in het tekstvak **AANmeldings-URL** een URL die het volgende patroon gebruikt:`https://cloud.mongodb.com/sso/<Customer_Unique>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, url voor beantwoorden en de URL van Aanmelding. Neem contact op met het [MongoDB Cloud Client Support Team](https://support.mongodb.com/)om aan deze waarden te komen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De MongoDB Cloud-toepassing verwacht dat de SAML-beweringen in een specifieke indeling zijn, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![Schermafbeelding van standaardkenmerken](common/default-attributes.png)

1. Naast de voorgaande kenmerken verwacht de MongoDB Cloud-toepassing dat er nog een paar kenmerken worden doorgegeven in de SAML-respons. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw vereisten.
    
    | Naam | |  Bronkenmerk|
    | ---------------| --------------- | --------- |
    | e-mail | | user.userprincipalname |
    | voornaam | | user.givenname |
    | achternaam | | user.surname |

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de xml **met aalmetagegevens**van federatie . Selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![Schermafbeelding van de sectie SAML-ondertekeningscertificaat, met koppeling Downloaden gemarkeerd](common/metadataxml.png)

1. Kopieer in de sectie **MongoDB Cloud instellen** de juiste URL's op basis van uw vereiste.

    ![Schermafbeelding van de sectie Mongo DB Cloud instellen, met URL's gemarkeerd](common/copy-configuration-urls.png)
### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory** > **Users** > **All users .**
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en schrijf het wachtwoord op.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u B.Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot MongoDB Cloud.

1. Selecteer in de Azure-portal Alle**bedrijfstoepassingen** **.** > 
1. Selecteer **MongoDB Cloud**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![Schermafbeelding van de sectie Beheren, met gemarkeerde gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer vervolgens in het dialoogvenster **Toewijzing toevoegen** de optie Gebruikers **en groepen**.

    ![Schermafbeelding van de pagina Gebruikers en groepen, met Gebruiker toevoegen gemarkeerd](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst met gebruikers. Kies vervolgens **Selecteer** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens **Selecteer** onder aan het scherm.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-mongodb-cloud-sso"></a>MongoDB Cloud SSO configureren

Als u eenmalige aanmelding wilt configureren aan de MongoDB Cloud-kant, hebt u de juiste URL's nodig die zijn gekopieerd van de Azure-portal. U moet ook de Federation Application configureren voor uw MongoDB Cloud Organization. Volg de instructies in de [MongoDB Cloud documentatie.](https://docs.atlas.mongodb.com/security/federated-auth-azure-ad/) Als u een probleem hebt, neemt u contact op met het [ondersteuningsteam van MongoDB Cloud.](https://support.mongodb.com/)

### <a name="create-a-mongodb-cloud-test-user"></a>Een MongoDB Cloud-testgebruiker maken

MongoDB Cloud ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Er is geen extra actie voor u om te nemen. Als een gebruiker nog niet bestaat in MongoDB Cloud, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>Test SSO 

In deze sectie test u uw azure AD-configuratie voor eenmalige aanmelding met access panel.

Wanneer u de MongoDB Cloud-tegel selecteert in het Access Panel, wordt u automatisch aangemeld bij de MongoDB-cloud waarvoor u SSO hebt ingesteld. Zie [Aanmelden en apps starten via de portal Mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie.

## <a name="additional-resources"></a>Extra resources

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Meld u aan voor MongoDB Atlas op Azure](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/mongodb.mongodb_atlas_azure_08082019?tab=Overview)

- [Probeer MongoDB Cloud met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Bescherm MongoDB Cloud met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

