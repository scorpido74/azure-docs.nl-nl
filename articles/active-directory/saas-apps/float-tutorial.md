---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met float | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en float.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e00cbbcd-2f2a-43f0-bac5-75f5fc34b6fc
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/23/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92d3f2381f2fdce123511caa2339d41b0641b077
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690641"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-float"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met float

In deze zelf studie leert u hoe u float kunt integreren met Azure Active Directory (Azure AD). Wanneer u float integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot float.
* Stel in dat uw gebruikers zich automatisch kunnen aanmelden om te laten zweven met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Een float-abonnement. Als u geen abonnement hebt, kunt u een [gratis account](https://app.float.com/join?)aanvragen.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Float ondersteunt **SP-en IDP** GEÏNITIEERDe SSO
* Zodra u float hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie beheer is uitgebreid met voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-float-from-the-gallery"></a>Float toevoegen vanuit de galerie

Als u de integratie van float in azure AD wilt configureren, moet u zweven toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** de tekst **zweven** in het zoekvak.
1. Selecteer **zweven** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-float"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor float

Azure AD SSO met float configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in float.

Als u Azure AD SSO wilt configureren en testen met float, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. Eenmalige **[SSO configureren](#configure-float-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Gebruiker voor float-test maken](#create-float-test-user)** : er is een equivalent van B. Simon in float dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de integratie pagina voor het overs van **de toepassing en** Selecteer de sectie voor het **beheren** van **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. Typ in het tekstvak **id** de volgende URL: `https://app.float.com/sso/metadata`.

    b. Typ in het tekstvak **antwoord-URL** een URL met behulp van `https://<hostname>.float.com/sso/azuread`het patroon.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ in het tekstvak **URL voor aanmelding** een URL in het patroon `https://<hostname>.float.com/login`.

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Vervang <hostname> door uw float-hostnaam. Neem contact op met het [ondersteunings team van float-clients](mailto:support@float.com) als u niet zeker weet. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De float-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. In het voor beeld van een float-toepassing worden er nog maar weinig kenmerken door gegeven aan het SAML-antwoord, dat hieronder wordt weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.
    
    | Naam |  |  Bronkenmerk|
    | ---------------| --------------- | --------- |
    | e-mail | | user.userprincipalname |

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer op de sectie **float instellen** de juiste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan float.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **float**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-float-sso"></a>SSO van float configureren

Als u eenmalige aanmelding wilt configureren, gaat u naar de sectie team **instellingen voor zweven** en selecteert u configureren vanuit de verificatie module. Plak de URL van de Azure AD-aanmelding in het URL-veld van het SAML 2,0-eind punt, plak de Azure AD-id in het veld URL van de uitgever van de identiteits provider, plak de volledige tekst van het gedownloade **certificaat (base64)** in het veld X. 509-certificaat en sla het op.

### <a name="create-float-test-user"></a>Gebruiker voor float-test maken

In deze sectie maakt u een gebruiker met de naam Julia Simon in float. Voeg de gebruiker toe vanuit de sectie personen of team instellingen gast en verleen hen een toegangs recht. Gebruikers moeten worden gemaakt en de uitnodiging accepteren voordat u eenmalige aanmelding gebruikt.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel zwevend in het toegangs venster klikt, moet u automatisch worden aangemeld bij de float waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Float met Azure AD proberen](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Float beveiligen met geavanceerde zicht baarheid en besturings elementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

