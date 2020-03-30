---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Greenlight Compliant Access Management | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Greenlight Compliant Access Management.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 47a80d88-d921-4fae-8c05-818662c396f5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 85fb972027170871cad62133965c7dc43d6374a3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78968441"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-greenlight-compliant-access-management"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Greenlight Compliant Access Management

In deze zelfstudie leert u hoe u Greenlight Compliant Access Management integreert met Azure Active Directory (Azure AD). Wanneer u Greenlight Compliant Access Management integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Greenlight Compliant Access Management.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Greenlight Compliant Access Management met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Greenlight Compliant Access Management single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Greenlight Compliant Access Management ondersteunt **SP en IDP** geïnitieerde SSO
* Zodra u Greenlight Compliant Access Management hebt geconfigureerd, u sessiebeheer afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebeheer strekt zich uit van Voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-greenlight-compliant-access-management-from-the-gallery"></a>Greenlight Compliant Access Management toevoegen vanuit de galerie

Als u de integratie van Greenlight Compliant Access Management in Azure AD wilt configureren, moet u Greenlight Compliant Access Management vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Groenlicht compatibel toegangsbeheer** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **Greenlight Compliant Access Management** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-greenlight-compliant-access-management"></a>Azure AD-aanmelding configureren en testen voor Greenlight-compatibel toegangsbeheer

Azure AD SSO configureren en testen met Greenlight Compliant Access Management met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Greenlight Compliant Access Management.

Als u Azure AD SSO wilt configureren en testen met Greenlight Compliant Access Management, voert u de volgende bouwstenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Greenlight Compliant Access Management SSO](#configure-greenlight-compliant-access-management-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * Maak de gebruiker van **[greenlight Compliant Access Management-test-](#create-greenlight-compliant-access-management-test-user)** om een tegenhanger van B.Simon te hebben in Greenlight Compliant Access Management dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Greenlight Compliant Access** Management-toepassingsintegratie de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://<CUSTOMER>.greenlightcorp.com/ebcpresq/checkLoginSAML.do`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<CUSTOMER>.greenlightcorp.com/ebcpresq/checkLoginSAML.do`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<CUSTOMER>.greenlightcorp.com/ebcpresq/checkLoginSAML.do`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met [greenlight Compliant Access Management Client support team](mailto:support@greenlightcorp.com) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **Greenlight Compliant Access Management** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie stelt u B.Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Greenlight Compliant Access Management.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Greenlight Compliant Access Management**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-greenlight-compliant-access-management-sso"></a>SSO voor groenlicht compatibel toegangsbeheer configureren

Als u eenmalige aanmelding wilt configureren aan de kant **van Greenlight Compliant Access Management,** moet u de gedownloade **XML met aalmetagegevens** van de Federatie en de juiste gekopieerde URL's van Azure-portal naar [het ondersteuningsteam voor Greenlight Compliant Access Management](mailto:support@greenlightcorp.com)verzenden. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-greenlight-compliant-access-management-test-user"></a>Gebruiker van greenlight Compliant Access Management-test maken

In deze sectie maakt u een gebruiker genaamd B.Simon in Greenlight Compliant Access Management. Werk samen met [greenlight compliant access management ondersteuningsteam](mailto:support@greenlightcorp.com) om de gebruikers toe te voegen in het Greenlight Compliant Access Management platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Greenlight Compliant Access Management in het toegangspaneel klikt, moet u automatisch worden aangemeld bij het Greenlight Compliant Access Management waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Greenlight Compliant Access Management met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Greenlight Compliant Access Management beschermen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)