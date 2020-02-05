---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met tableau-server | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en tableau server.
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
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76986730"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-tableau-server"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met tableau-server

In deze zelf studie leert u hoe u tableau Server integreert met Azure Active Directory (Azure AD). Wanneer u tableau Server integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de tableau-server.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij de tableau-server met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Eenmalige aanmelding (SSO) van tableau server ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Tableau-server ondersteunt door **SP** GEÏNITIEERDe SSO
* Zodra u tableau server hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor de gevoelige gegevens van uw organisatie in realtime worden beschermd door exfiltration en onfiltratie. Sessie beheer is uitgebreid met voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-tableau-server-from-the-gallery"></a>De tableau-server toevoegen vanuit de galerie

Als u de integratie van tableau-server in azure AD wilt configureren, moet u tableau-server uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **tableau server** in het zoekvak.
1. Selecteer **tableau-server** in het resultaten paneel en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-tableau-server"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor tableau-server

Configureer en test Azure AD SSO met tableau server met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in tableau server.

Als u Azure AD SSO wilt configureren en testen met tableau-server, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Tableau server-SSO configureren](#configure-tableau-server-sso)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak een tableau-server test gebruiker](#create-tableau-server-test-user)** -om een equivalent van B. Simon te hebben in tableau-server dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina **tableau-server** toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://azure.<domain name>.link`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://azure.<domain name>.link`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > De voor gaande waarden zijn geen echte waarden. Werk de waarden bij met de werkelijke URL en id van de tableau-server configuratie pagina die verderop in de zelf studie wordt beschreven.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer op de sectie **tableau-server instellen** de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de tableau-server.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **tableau server**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-tableau-server-sso"></a>SSO van tableau-server configureren

1. Als u SSO wilt ophalen die voor uw toepassing is geconfigureerd, moet u zich bij uw tableau-server Tenant aanmelden als beheerder.

2. Op het tabblad **configuratie** selecteert u **gebruikers identiteit & toegang**en selecteert u vervolgens het tabblad **verificatie** methode.

    ![Eenmalige aanmelding configureren](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. Voer de volgende stappen uit op de pagina **configuratie** :

    ![Eenmalige aanmelding configureren](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. Selecteer voor **verificatie methode**de optie SAML.

    b. Schakel het selectie vakje in **voor het inschakelen van SAML-verificatie voor de-server**.

    c. Tableau server-retour-URL: de URL van tableau Server-gebruikers die toegang krijgen, zoals <http://tableau_server>. Het gebruik van `http://localhost` wordt niet aanbevolen. Het gebruik van een URL met een slash (bijvoorbeeld `http://tableau_server/`) wordt niet ondersteund. Kopieer de URL van de **tableau-server** en plak deze in om het **URL** -tekstvak op te geven in de sectie **basis configuratie van SAML** in de Azure Portal

    d. SAML-entiteit-ID: de entiteit-ID is een unieke aanduiding voor uw tableau-server installatie naar de IdP. U kunt de URL van uw tableau-server hier hier opgeven, maar dit is niet uw tableau-server-URL. Kopieer de ID van de **SAML-entiteit** en plak deze in het tekstvak **id** in de sectie **basis configuratie van SAML** in de Azure Portal

    e. Klik op het **XML-bestand met meta gegevens downloaden** en open het in de toepassing tekst editor. Zoek de URL van de service voor de bewerings verbruiker met http post en index 0 en kopieer de URL. Plak het in het tekstvak **antwoord URL** in het gedeelte **basis configuratie van SAML** in de Azure Portal

    f. Zoek het bestand met federatieve meta gegevens dat is gedownload van Azure Portal en upload het naar het bestand met de **SAML IDP-meta gegevens**.

    g. Voer de namen in voor de kenmerken die de IdP gebruikt om de gebruikers namen, weergave namen en e-mail adressen op te slaan.

    h. Klik op **Opslaan**.

    > [!NOTE]
    > De klant moet een certificaat uploaden in de SAML SSO-configuratie van de tableau-server en wordt genegeerd in de SSO-stroom. Als u hulp nodig hebt bij het configureren van SAML op tableau server, raadpleegt u dit artikel [SAML configureren](https://help.tableau.com/current/online/en-us/saml_config_azure_ad.htm).

### <a name="create-tableau-server-test-user"></a>Test gebruiker voor tableau-server maken

Het doel van deze sectie is het maken van een gebruiker met de naam B. Simon in tableau server. U moet alle gebruikers op de tableau-server inrichten.

De gebruikers naam van de gebruiker moet overeenkomen met de waarde die u hebt geconfigureerd in het aangepaste Azure AD-kenmerk van de **gebruikers naam**. Met de juiste toewijzing moet de integratie werken met het configureren van eenmalige aanmelding voor Azure AD.

> [!NOTE]
> Als u hand matig een gebruiker moet maken, moet u contact opnemen met de beheerder van de tableau-server in uw organisatie.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel tableau server in het toegangs venster klikt, moet u automatisch worden aangemeld bij de tableau-server waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer tableau server met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)