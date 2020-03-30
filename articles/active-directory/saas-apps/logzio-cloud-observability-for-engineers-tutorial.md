---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Logz.io - Cloud Observability for Engineers | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Logz.io - Cloudobservability for Engineers.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b0984471-d12f-4f58-896e-194ea45b01fd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/26/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: de1c929ffa790d2abe3a1922cecc2175cd7a8e12
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385478"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-logzio---cloud-observability-for-engineers"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Logz.io - Cloud Observability for Engineers

In deze zelfstudie leert u hoe u Logz.io - Cloudobservability for Engineers integreert met Azure Active Directory (Azure AD). Wanneer u Logz.io - Cloudobservability for Engineers integreert met Azure AD, u het als volgt doen:

* Beheer in Azure AD die toegang heeft tot Logz.io - Cloud Observability for Engineers.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Logz.io - Cloud observability for Engineers with their Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Logz.io - Cloud Observability for Engineers single sign-on (SSO) enabled subscription.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Logz.io - Cloud Observability for Engineers ondersteunt **IDP** geïnitieerde SSO
* Zodra u Logz.io - Cloud Observability for Engineers hebt geconfigureerd, u sessiecontrole afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebeheer strekt zich uit van Voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-logzio---cloud-observability-for-engineers-from-the-gallery"></a>Logz.io toevoegen - Cloudobservability voor engineers uit de galerie

Als u de integratie van Logz.io - Cloudobservability for Engineers in Azure AD wilt configureren, moet u Logz.io - Cloudobservability for Engineers uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Logz.io - Cloudobservability for Engineers** in het zoekvak in de sectie Toevoegen in de **galerie.**
1. Selecteer **Logz.io - Cloudobservability for Engineers** van het resultatenpaneel en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-logzio---cloud-observability-for-engineers"></a>Azure AD-singlesign-on configureren en testen voor Logz.io - Cloudobservability for Engineers

Azure AD SSO configureren en testen met Logz.io - Cloudobservability for Engineers met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Logz.io - Cloudobservability for Engineers.

Als u Azure AD SSO wilt configureren en testen met Logz.io - Cloudobservability for Engineers, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Logz.io Cloud Observability for Engineers SSO](#configure-logzio-cloud-observability-for-engineers-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Maak Logz.io Cloud Observability for Engineers test gebruiker](#create-logzio-cloud-observability-for-engineers-test-user)** - om een tegenhanger van B.Simon in Logz.io - Cloud Observability for Engineers die is gekoppeld aan de Azure AD vertegenwoordiging van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de **pagina Logz.io - Cloud Observability for Engineers-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **Eén aanmelding instellen met SAML** de waarden in voor de volgende velden:

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`urn:auth0:logzio:CONNECTION-NAME`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://logzio.auth0.com/login/callback?connection=CONNECTION-NAME`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met [Logz.io - Cloud Observability for Engineers Client support team](mailto:help@logz.io) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Logz.io - Cloud Observability for Engineers-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Naast bovenstaande, Logz.io - Cloud Observability for Engineers applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw vereisten.
    
    | Name |  Bronkenmerk|
    | ---------------| --------- |
    | sessie-verlopen | user.session-expiratie |
    | e-mail | user.mail |
    | Groep | user.groups |

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Logz.io instellen - Cloudobservability for Engineers** de juiste URL(s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u B.Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Logz.io - Cloud Observability for Engineers.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen **Logz.io - Cloudobservability for Engineers.**
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-logzio-cloud-observability-for-engineers-sso"></a>Logz.io cloudobservability configureren voor Engineers SSO

Als u eenmalige aanmelding wilt configureren op **Logz.io - Cloud Observability for** Engineers-kant, moet u het gedownloade **certificaat (Base64)** en de juiste gekopieerde URL's van Azure-portal naar [Logz.io - Ondersteuningsteam voor cloudobservability for engineers](mailto:help@logz.io)verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-logzio-cloud-observability-for-engineers-test-user"></a>Logz.io cloudobservability for Engineers-testgebruiker maken

In deze sectie maakt u een gebruiker genaamd Britta Simon in Logz.io - Cloud Observability for Engineers. Werk samen met [Logz.io - Ondersteuningteam cloudobservability for engineers](mailto:help@logz.io) om de gebruikers in het Logz.io - Cloud Observability for Engineers-platform toe te voegen. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Logz.io - CloudObservability for Engineers in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Logz.io - Cloud Observability for Engineers waarvoor u SSO instelt. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Logz.io - Cloudobservability for Engineers with Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Hoe Logz.io te beschermen - Cloud observability voor engineers met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

