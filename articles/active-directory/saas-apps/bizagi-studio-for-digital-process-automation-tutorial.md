---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met bizagi Studio voor digitale proces automatisering | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en bizagi Studio voor digitale proces automatisering.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af3d4613-c3fb-485c-b7b9-c385713e6f8f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85f7dd96bc2767b98174bee2cadaa93a6bfa1459
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "78207510"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bizagi-studio-for-digital-process-automation"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met bizagi Studio voor digitale proces automatisering

In deze zelf studie leert u hoe u bizagi Studio integreert voor het digitaal proces automatisering met Azure Active Directory (Azure AD). Wanneer u bizagi Studio integreert voor de automatisering van digitale processen met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot bizagi Studio voor het automatiseren van digitale processen.
* Stel in dat uw gebruikers automatisch worden aangemeld bij bizagi Studio voor automatisering van digitale processen met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Bizagi Studio voor het abonnement voor eenmalige aanmelding (SSO) van Digital Process Automation.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Bizagi Studio voor Digital Process Automation ondersteunt door **SP** GEÏNITIEERDe SSO
* Zodra u bizagi Studio voor digitale proces automatisering hebt geconfigureerd, kunt u sessie besturings elementen afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie beheer is uitgebreid met voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-bizagi-studio-for-digital-process-automation-from-the-gallery"></a>Bizagi Studio toevoegen voor het digitaal proces automatiseren vanuit de galerie

Als u de integratie van bizagi Studio voor digitale proces automatisering wilt configureren in azure AD, moet u bizagi Studio voor Digital Process Automation toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. In de sectie **toevoegen vanuit de galerie** typt u **bizagi Studio for Digital Process Automation** in het zoekvak.
1. Selecteer **bizagi Studio for Digital Process Automation** van het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-bizagi-studio-for-digital-process-automation"></a>Eenmalige aanmelding voor Azure AD voor bizagi Studio configureren en testen voor digitale proces automatisering

Azure AD SSO configureren en testen met bizagi Studio for Digital Process Automation met behulp van een test gebruiker met de naam **B. Simon**. Voor een goede werking van SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in bizagi Studio voor de automatisering van digitale processen.

Als u Azure AD SSO wilt configureren en testen met bizagi Studio voor Digital Process Automation, voert u de volgende bouw stenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Bizagi Studio configureren voor de SSO van digitale proces automatisering](#configure-bizagi-studio-for-digital-process-automation-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een bizagi Studio for Digital Process Automation test gebruiker](#create-bizagi-studio-for-digital-process-automation-test-user)** -om een soort tegen te brengen van B. Simon in bizagi Studio voor digitale proces automatisering die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **bizagi Studio for Digital Process Automation** Application de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL: `https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    b. Typ een URL in het vak **Id (Entiteits-id)**: `https://<COMPANYNAME>.bizagi.com/<PROJECTNAME>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de daadwerkelijke aanmeldings-URL en -id. Neem contact op met [bizagi Studio voor het ondersteunings team voor Digital Process Automation](mailto:jarvein.rivera@bizagi.com) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Klik op de pagina **eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekening certificaat** op de knop kopiëren om de URL van de **app Federation-meta gegevens** te kopiëren en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

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

In deze sectie schakelt u B. Simon in voor het gebruik van eenmalige aanmelding van Azure door toegang te verlenen aan bizagi Studio voor de automatisering van digitale processen.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **bizagi Studio for Digital Process Automation**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-bizagi-studio-for-digital-process-automation-sso"></a>Bizagi Studio configureren voor eenmalige aanmelding met Digital Process Automation

Voor het configureren van eenmalige aanmelding op **bizagi Studio voor de automatiserings zijde van digitale processen** moet u de URL voor de **federatieve meta gegevens** van de app verzenden naar [bizagi Studio voor het ondersteunings team voor Digital Process Automation](mailto:jarvein.rivera@bizagi.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-bizagi-studio-for-digital-process-automation-test-user"></a>Bizagi Studio maken voor test gebruiker voor digitale proces automatisering

In deze sectie maakt u een gebruiker met de naam Julia Simon in bizagi Studio voor Digital Process Automation. Werk met [bizagi Studio voor het ondersteunings team voor digitale processen](mailto:jarvein.rivera@bizagi.com) om de gebruikers toe te voegen in het bizagi Studio for Digital Process Automation platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel bizagi Studio voor Digital Process Automation in het toegangs venster klikt, moet u automatisch worden aangemeld bij de bizagi Studio voor het digitaal proces automatisering waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer bizagi Studio voor Digital Process Automation met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)