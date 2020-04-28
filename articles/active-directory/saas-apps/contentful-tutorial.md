---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met inhoud | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en de inhoud.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f29e1015-d508-4698-a381-5d871c646161
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b58abede48c0e096f3f54989d783b1e455f8e4d4
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/27/2020
ms.locfileid: "82169639"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-contentful"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met inhoud

In deze zelf studie leert u hoe u inhoud kunt integreren met Azure Active Directory (Azure AD). Wanneer u inhoud integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot inhoud.
* Stel in dat uw gebruikers zich automatisch kunnen aanmelden met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Het abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Contental ondersteunt SSO die **met SP en IDP** is gestart
* Biedt ondersteuning **voor Just-in-time** -gebruikers inrichting
* Zodra u inhoud hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie beheer is uitgebreid met voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

> [!NOTE]
> De id van deze toepassing is een vaste teken reeks waarde. Er kan slechts één exemplaar worden geconfigureerd in één Tenant.

## <a name="adding-contentful-from-the-gallery"></a>Inhoud vanuit de galerie toevoegen

Als u de integratie van inhoud in azure AD wilt configureren, moet u inhoud vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. In de sectie **toevoegen vanuit de galerie** typt u **inhoud** in het zoekvak.
1. Selecteer **inhoud** in de resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-contentful"></a>Eenmalige aanmelding voor Azure AD voor inhoud configureren en testen

Configureer en test Azure AD SSO met inhoud met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in een inhouds verband.

Als u Azure AD SSO wilt configureren en testen met behulp van inhoud, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Content-SSO configureren](#configure-contentful-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een gebruiker voor content-test](#create-contentful-test-user)** : als u een equivalent van B. Simon wilt hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina voor de integratie van **webtoepassingen** en selecteer de sectie voor het **beheren** van **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    - In het tekstvak **antwoord-URL** kopieert u de ACS-URL (assertion Consumer Service) van de pagina SSO Setup in een inhouds bestand. Dit ziet er als volgt uit:`https://be.contentful.com/sso/<organization_id>/consume`

1. Klik op **aanvullende Url's instellen** en voer de volgende stap uit als u de toepassing in de modus door **SP** gestart wilt configureren:

    - Kopieer in het tekstvak **URL voor aanmelding** dezelfde ACS-URL (assertion Consumer Service). Dit ziet er als volgt uit:`https://be.contentful.com/sso/<organization_id>/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de daad werkelijke antwoord-URL en aanmeldings-URL door de ACS-URL (assertion Consumer Service) te kopiëren van de pagina SSO Setup in de richting van de installatie.

1. Voor een inhouds afhankelijke toepassing worden de SAML-beweringen in een specifieke indeling verwacht. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. In aanvulling op hierboven verwachtte de toepassing van de inhoud dat er nog maar enkele kenmerken worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.
    
    | Naam |  Bronkenmerk|
    | --------------- | --------- |
    | e-mail | user.userprincipalname |

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **inhoud instellen** de AANMELDINGS-URL voor het configureren van contental SSO.

    ![Configuratie-URL's kopiëren](media/contentful-tutorial/copy-configuration-urls.png)

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan inhoud.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **contented**.
1. Zoek op de pagina overzicht van de app de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan de pagina.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan de pagina.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-contentful-sso"></a>Content-SSO configureren

Volg deze stappen om eenmalige aanmelding te configureren aan de zijde van de **inhoud** .

1. Navigeer [in de](https://app.contentful.com) **organisatie-instellingen**naar de pagina SSO Setup.
1. Klik op **SSO instellen**.
1. Kopieer en plak de aanmeldings-URL uit de sectie inhoud in azure AD **instellen** .
1. Kopieer en plak het certificaat vanuit het Base64-certificaat bestand dat u hebt gedownload van Azure AD.
1. Stel een SSO-naam in voor door SP geïnitieerde aanmelding.
1. Klik op **SSO inschakelen**.

Als dat niet werkt, kunt u contact met het [ondersteunings team voor inhoud](mailto:support@contentful.com)bereiken.

### <a name="create-contentful-test-user"></a>Inhouds gebruiker voor test doeleinden maken

In deze sectie wordt een gebruiker met de naam B. Simon in een inhouds opgemaakt. Contentd ondersteunt just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet aanwezig is in de inhouds opgelegde, wordt er na de verificatie een nieuwe gemaakt.

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel inhoud in het toegangs venster klikt, moet u automatisch worden aangemeld bij de inhoud waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer het met Azure AD](https://aad.portal.azure.com/)