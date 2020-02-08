---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met een BIC-Cloud-ontwerp | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en een BIC-Cloud ontwerp.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 68709502-480d-4f47-add2-05e0046be8ac
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/07/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 13f6de35400280d68227af1dd7e3a981494d9e61
ms.sourcegitcommit: cfbea479cc065c6343e10c8b5f09424e9809092e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/08/2020
ms.locfileid: "77088212"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bic-cloud-design"></a>Zelf studie: Azure Active Directory SSO-integratie (single sign-on) met BIC-Cloud ontwerp

In deze zelf studie leert u hoe u een BIC-Cloud ontwerp kunt integreren met Azure Active Directory (Azure AD). Wanneer u een BIC-Cloud ontwerp integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot een BIC-Cloud ontwerp.
* Uw gebruikers in staat stellen om automatisch te worden aangemeld bij een BIC-Cloud ontwerp met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* BIC-abonnement voor eenmalige aanmelding (SSO) van het Cloud ontwerp ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* BIC-Cloud ontwerp ondersteunt door **SP** GEÏNITIEERDe SSO
* Zodra u het BIC-Cloud ontwerp hebt geconfigureerd, kunt u sessie besturings elementen afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie besturings elementen worden uitgebreid vanuit voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-bic-cloud-design-from-the-gallery"></a>Een BIC-Cloud ontwerp toevoegen vanuit de galerie

Als u de integratie van het BIC-Cloud ontwerp wilt configureren in azure AD, moet u een BIC-Cloud ontwerp vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie in **de galerie toevoegen** de tekst **BIC-Cloud ontwerp** in het zoekvak.
1. Selecteer in het paneel resultaten de optie **BIC-Cloud ontwerp** en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bic-cloud-design"></a>Eenmalige aanmelding voor een BIC-Cloud ontwerp configureren en testen in azure AD

Configureer en test Azure AD SSO met een BIC-Cloud ontwerp met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in een BIC-Cloud ontwerp.

Als u Azure AD SSO wilt configureren en testen met een BIC-Cloud ontwerp, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[BIC Cloud design SSO configureren](#configure-bic-cloud-design-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een BIC-test gebruiker voor Cloud ontwerp](#create-bic-cloud-design-test-user)** -een soort equivalent van B. Simon in een BIC-Cloud ontwerp dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina voor de integratie van de **BIC-Cloud** toepassing en selecteer de sectie voor het **beheren** van **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de volgende stappen uit als u beschikt over een **bestand met metagegevens van de serviceprovider**:

    a. Klik op **Metagegevensbestand uploaden**.

    ![Metagegevensbestand uploaden](common/upload-metadata.png)

    b. Klik op het **mappictogram** om het metagegevensbestand te selecteren en klik op **Uploaden**.

    ![Metagegevensbestand kiezen](common/browse-upload-metadata.png)

    c. Nadat het meta gegevensbestand is geüpload, wordt de **id** -waarde automatisch ingevuld in de basis configuratie sectie voor SAML.

    ![BIC-Cloud ontwerpen domein en Url's eenmalige aanmelding gegevens](common/sp-identifier.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon:

    |||
    |-|-|
    | `https://<customer-specific-name/tenant>.biccloud.com`|
    | `https://<customer-specific-name/tenant>.biccloud.de` |

    > [!Note]
    > Als de waard voor **Id** niet automatisch wordt ingevuld, kunt u de waarde zelf invullen afhankelijk van uw behoeften. De waarde voor de aanmeldings-URL is niet echt. Werk deze waarde bij met de werkelijke aanmeldings-URL. Neem contact op met de [BIC cloud-client ondersteunings team](mailto:bicsupport@gbtec.de) om deze waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De BIC-Cloud toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerk toewijzingen moet toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![image](common/default-attributes.png)

1. Een BIC-Cloud toepassing verwacht nog weinig kenmerken te worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    | Naam | Bronkenmerk|
    | ------------ | --------- |
    | Naam | user.name |
    | E-mailadres | user.mail |
    | Naam-ID | user.userprincipalname |
    | e-mail | user.mail |
    | nametest | user.displayname |

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
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot een BIC-Cloud ontwerp.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **BIC-Cloud-ontwerp**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-bic-cloud-design-sso"></a>Een BIC-Cloud ontwerp configureren

Als u eenmalige aanmelding wilt configureren voor een **BIC-Cloud** , moet u de URL voor de **federatieve meta gegevens** van de app naar het [BIC-Cloud ontwerp ondersteunings team](mailto:bicsupport@gbtec.de)verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-bic-cloud-design-test-user"></a>Een BIC-test gebruiker voor het Cloud ontwerp maken

In deze sectie maakt u een gebruiker met de naam B. Simon in een BIC-Cloud ontwerp. Werk samen met het [hulp team](mailto:bicsupport@gbtec.de) voor de ondersteuning van een BIC-Cloud om de gebruikers toe te voegen in het BIC-Cloud ontwerp platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel BIC-Cloud ontwerp in het toegangs venster klikt, moet u automatisch worden aangemeld bij het BIC-Cloud ontwerp waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer het met een BIC-Cloud ontwerp met Azure AD](https://aad.portal.azure.com/)