---
title: 'Zelfstudie: SSO-integratie (Azure Active Directory single sign-on) met Tableau Server | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Tableau Server.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d19a7cc8d81f9e6e913f147b24c5cce03ff82027
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76986730"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-server"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Tableau Server

In deze zelfstudie leert u hoe u Tableau Server integreren met Azure Active Directory (Azure AD). Wanneer u Tableau Server integreert met Azure AD, u het:

* Beheer in Azure AD wie toegang heeft tot Tableau Server.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Tableau Server met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Abonnement met enkele aanmeldingsfunctie (Tableau Server) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Tableau Server ondersteunt **door SP** geïnitieerde SSO
* Zodra u Tableau Server hebt geconfigureerd, u sessiebeheer afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebeheer strekt zich uit van Voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-server-from-the-gallery"></a>Tableau Server toevoegen vanuit de galerie

Als u de integratie van Tableau Server in Azure AD wilt configureren, moet u Tableau Server vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Tableau Server** in het zoekvak in de sectie Toevoegen in **de galerie.**
1. Selecteer **Tableau Server** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tableau-server"></a>Azure AD-aanmelding voor Tableau Server configureren en testen

Azure AD SSO configureren en testen met Tableau Server met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Tableau Server.

Als u Azure AD SSO wilt configureren en testen met Tableau Server, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Tableau Server SSO configureren](#configure-tableau-server-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Maak Tableau Server-testgebruiker](#create-tableau-server-test-user)** - om een tegenhanger van B.Simon in Tableau Server te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina Met de integratie van **tableau server-toepassingen** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    a. Typ in het tekstvak **AANmeldings-URL** een URL met het volgende patroon:`https://azure.<domain name>.link`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://azure.<domain name>.link`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > De voorgaande waarden zijn geen echte waarden. Werk de waarden bij met de werkelijke URL en id van de Configuratiepagina van Tableau Server, die later in de zelfstudie wordt uitgelegd.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **Tableau Server instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Tableau Server.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Tableau Server**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-tableau-server-sso"></a>Tableau Server SSO configureren

1. Als u SSO wilt configureren voor uw toepassing, moet u zich aanmelden bij uw Tableau Server-tenant als beheerder.

2. Selecteer **op** het tabblad CONFIGURATIE de optie **Gebruikersidentiteit & Access**en selecteer vervolgens het tabblad **Verificatiemethode.**

    ![Eenmalige aanmelding configureren](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Voer **op** de pagina CONFIGURATIE de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. Selecteer SAML voor **verificatiemethode.**

    b. Schakel het selectievakje **SAML-verificatie inschakelen voor de server**in .

    c. Tableau Server retourneert URL: de URL die Tableau <http://tableau_server>Server-gebruikers zullen openen, zoals . Gebruik `http://localhost` wordt niet aanbevolen. Het gebruik van een URL met `http://tableau_server/`een trailing slash (bijvoorbeeld) wordt niet ondersteund. **Tableau Server retour-URL kopiëren** en plakken in het **tekstvak URL aanmelden** in de sectie **BasisSAML-configuratie** in de Azure-portal

    d. SAML-entiteits-id: de entiteits-id identificeert uw Tableau Server-installatie op unieke wijze aan de IdP. Je je Tableau Server URL hier opnieuw invoeren, als je wilt, maar het hoeft niet je Tableau Server URL te zijn. **SAML-entiteits-id kopiëren** en plakken in **het tekstvak Identifier** in de sectie **BasisSAML-configuratie** in de Azure-portal

    e. Klik op het **bestand met XML-metagegevens downloaden** en open het in de toepassing teksteditor. Zoek de URL van de Bevestigingsconsumentenservice met Http Post en Index 0 en kopieer de URL. Plak het nu in het tekstvak **URL beantwoorden** in de sectie **BasisSAML-configuratie** in de Azure-portal

    f. Zoek het bestand Federatiemetagegevens dat is gedownload van de Azure-portal en upload het vervolgens in het **SAML Idp-metagegevensbestand.**

    g. Voer de namen in voor de kenmerken die de IdP gebruikt om de gebruikersnamen, weergavenamen en e-mailadressen vast te houden.

    h. Klik **op Opslaan**

    > [!NOTE]
    > De klant moet elk certificaat uploaden in de Tableau Server SAML SSO-configuratie en het wordt genegeerd in de SSO-stroom. Als je hulp nodig hebt bij het configureren van SAML op Tableau Server, raadpleeg dan dit artikel [SAML configureren.](https://help.tableau.com/current/online/en-us/saml_config_azure_ad.htm)

### <a name="create-tableau-server-test-user"></a>Tableau Server-testgebruiker maken

Het doel van deze sectie is het creëren van een gebruiker genaamd B.Simon in Tableau Server. U moet alle gebruikers in de Tableau-server inrichten.

Die gebruikersnaam van de gebruiker moet overeenkomen met de waarde die u hebt geconfigureerd in het aangepaste kenmerk Azure AD van **gebruikersnaam**. Met de juiste toewijzing moet de integratie werken Configureren Azure AD Single Sign-On.

> [!NOTE]
> Als u handmatig een gebruiker wilt maken, moet u contact opnemen met de Tableau Server-beheerder in uw organisatie.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tableauserver-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Tableau Server waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Tableau Server proberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)