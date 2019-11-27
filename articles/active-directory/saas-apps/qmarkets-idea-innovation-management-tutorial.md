---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Qmarkets-idee & innovatie beheer | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Qmarkets-idee & innovatie beheer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 5ca125c7-f778-4a2d-a573-7cebe1ff634d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d3e3f86d761a686993e6ecf32718aa2e15dac92
ms.sourcegitcommit: 85e7fccf814269c9816b540e4539645ddc153e6e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/26/2019
ms.locfileid: "74536286"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-qmarkets-idea--innovation-management"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Qmarkets-idee & innovatie beheer

In deze zelf studie leert u hoe u Qmarketse ideeën kunt integreren & innovatie beheer met Azure Active Directory (Azure AD). Wanneer u Qmarkets-idee & innovatie beheer met Azure AD integreert, kunt u het volgende doen:

* Beheer in azure AD die toegang heeft tot Qmarkets-idee & innovatie beheer.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Qmarkets idee & innovatie beheer met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Qmarkets-idee &-abonnement voor eenmalige aanmelding (SSO) voor innovatie beheer.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.



* Qmarkets-idee & innovatie management ondersteunt door **SP en IDP** GEÏNITIEERDe SSO
* Qmarkets-idee & innovatie beheer ondersteunt **just-in-time** -gebruikers inrichting


## <a name="adding-qmarkets-idea--innovation-management-from-the-gallery"></a>Qmarkets-idee & innovatie beheer toevoegen vanuit de galerie

Als u de integratie van Qmarkets-idee & innovatie beheer wilt configureren in azure AD, moet u Qmarkets idee & innovatie beheer van de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Qmarkets idee & innovatie beheer** in het zoekvak.
1. Selecteer **Qmarkets-idee & innovatie beheer** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-qmarkets-idea--innovation-management"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Qmarkets-idee & innovatie beheer

Azure AD SSO met Qmarkets-idee & innovatie beheer configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Qmarkets-idee & innovatie beheer.

Als u Azure AD SSO wilt configureren en testen met Qmarkets-idee & innovatie beheer, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Configureer Qmarkets-idee & innovatie management SSO](#configure-qmarkets-idea--innovation-management-sso)** -om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
    1. **[Maak een Qmarkets-idee & een test gebruiker voor innovatie beheer](#create-qmarkets-idea--innovation-management-test-user)** , om een soort tegen te brengen van B. Simon in Qmarkets idee & innovatie beheer dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina **Qmarkets-idee &** de integratie van de toepassing voor innovatie beheer de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<app_url>/sso/saml2/metadata/qmarkets_sp_<endpoint_id>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://<app_url>/sso/saml2/acs/qmarkets_sp_<endpoint_id>`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<app_url>/sso/saml2/endpoint/qmarkets_sp_<endpoint_id>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [Qmarkets-idee & innovatie Management client-ondersteunings team](mailto:support@qmarkets.net) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Klik op de pagina **eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekening certificaat** op de knop kopiëren om de URL van de **app Federation-meta gegevens** te kopiëren en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam** `B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Qmarkets-idee & innovatie beheer.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen **Qmarkets idee & innovatie beheer**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-qmarkets-idea--innovation-management-sso"></a>Qmarkets-idee & innovatie beheer-SSO configureren

Als u eenmalige aanmelding wilt configureren op **Qmarkets-idee & innovatie beheer** zijde, moet u de **URL voor de federatieve meta gegevens** van de app verzenden naar [Qmarkets idee & ondersteunings team voor innovatie beheer](mailto:support@qmarkets.net). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-qmarkets-idea--innovation-management-test-user"></a>Qmarkets-idee maken &-test gebruiker voor innovatie beheer

In deze sectie wordt een gebruiker met de naam Julia Simon gemaakt in Qmarkets-idee & innovatie beheer. Qmarkets-idee & innovatie beheer ondersteunt just-in-time-gebruikers inrichting, dat standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Qmarkets-idee & innovatie beheer, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Qmarkets idee & innovatie beheer in het toegangs venster klikt, wordt u automatisch aangemeld bij het Qmarkets-idee & innovatie beheer waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Qmarkets idee & innovatie beheer met Azure AD](https://aad.portal.azure.com/)

