---
title: 'Zelf studie: integratie met adaptieve inzichten Azure Active Directory | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en adaptieve inzichten.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 13af9d00-116a-41b8-8ca0-4870b31e224c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bf05fc30a2b111a12c0b8e131db5117ec784075b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74232127"
---
# <a name="tutorial-integrate-adaptive-insights-with-azure-active-directory"></a>Zelf studie: adaptieve inzichten integreren met Azure Active Directory

In deze zelf studie leert u hoe u adaptieve inzichten kunt integreren met Azure Active Directory (Azure AD). Wanneer u adaptieve inzichten integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot adaptieve inzichten.
* Uw gebruikers in staat stellen om automatisch te worden aangemeld bij adaptieve inzichten met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Adaptief Insights single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Adaptieve inzichten biedt ondersteuning voor **IDP** GEÏNITIEERDe SSO

## <a name="adding-adaptive-insights-from-the-gallery"></a>Adaptieve inzichten toevoegen vanuit de galerie

Als u de integratie van adaptieve inzichten in azure AD wilt configureren, moet u adaptieve inzichten uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. In de sectie **toevoegen vanuit de galerie** typt u **adaptieve inzichten** in het zoekvak.
1. Selecteer **adaptieve inzichten** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met adaptieve inzichten met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in adaptieve inzichten.

Als u Azure AD SSO wilt configureren en testen met adaptieve inzichten, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
2. **[Configureer adaptieve inzichten SSO](#configure-adaptive-insights-sso)** -om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
3. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
4. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
5. Een **[adaptieve Insights-test gebruiker maken](#create-adaptive-insights-test-user)** : als u een equivalent van B. Simon wilt hebben in adaptieve inzichten die is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina **adaptieve inzichten** voor toepassings integratie en selecteer de sectie voor het **beheren** van **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. Typ in het tekstvak **id** een URL met het volgende patroon:`https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://login.adaptiveinsights.com:443/samlsso/<unique-id>`

    > [!NOTE]
    > U kunt de Id's (Entiteits-ID) en antwoord-URL-waarden ophalen van de pagina met **SAML SSO-instellingen** van adaptieve inzichten.

4. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. Op de sectie **adaptieve inzichten instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-adaptive-insights-sso"></a>Adaptieve inzichten SSO configureren

1. Meld u in een ander webbrowser venster aan bij de bedrijfs site voor adaptieve inzichten als beheerder.

2. Ga naar **Administration**.

    ![Beheerder](./media/adaptivesuite-tutorial/ic805644.png "Beheerder")

3. Klik in de sectie **gebruikers en rollen** op **instellingen voor SAML SSO**.

    ![SAML SSO-instellingen beheren](./media/adaptivesuite-tutorial/ic805645.png "SAML SSO-instellingen beheren")

4. Voer de volgende stappen uit op de pagina **SAML SSO-instellingen** :

    ![SAML SSO-instellingen](./media/adaptivesuite-tutorial/ic805646.png "SAML SSO-instellingen")

    a. Typ in het tekstvak naam van de **identiteits provider** een naam voor uw configuratie.

    b. Plak de id-waarde van **Azure AD** die is gekopieerd van Azure Portal naar het tekstvak **ID-provider entiteit** .

    c. Plak de waarde van de **aanmeldings-URL** gekopieerd van Azure Portal naar het tekstvak id- **SSO-URL** .

    d. Plak de waarde van de **Afmeldings-URL** gekopieerd van Azure Portal naar het tekstvak **aangepaste afmeldings-URL** .

    e. Als u het gedownloade certificaat wilt uploaden, klikt u op **bestand kiezen**.

    f. Selecteer het volgende voor:

     * Gebruikers **naam van de gebruiker met de adaptieve**gebruikers **-id van SAML**selecteert.

     * **Gebruikers**-id-locatie van SAML Selecteer **gebruikers-id bij NameID van onderwerp**.

     * **SAML NameID-indeling**, selecteer **e-mail adres**.

     * **Schakel SAML in**, selecteer **SAML SSO en direct Adaptive Insights-aanmelding toestaan**.

    g. Kopieer de **SSO-URL voor adaptieve inzichten** en plak deze in de tekst van de **id (Entiteits-ID)** en de **antwoord-URL** in het gedeelte basis-SAML- **configuratie** in de Azure Portal.

    h. Klik op **Opslaan**.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan adaptieve inzichten.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **adaptieve inzichten**in de lijst toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

### <a name="create-adaptive-insights-test-user"></a>Een adaptieve Insights-test gebruiker maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij adaptieve inzichten, moeten ze worden ingericht in adaptieve inzichten. In het geval van adaptieve inzichten is inrichting een hand matige taak.

**Voer de volgende stappen uit om de inrichting van gebruikers te configureren:**

1. Meld u aan bij de bedrijfs site voor **adaptieve inzichten** als beheerder.

2. Ga naar **Administration**.

   ![Beheerder](./media/adaptivesuite-tutorial/IC805644.png "Beheerder")

3. Klik in de sectie **gebruikers en rollen** op **gebruikers**.

   ![Gebruiker toevoegen](./media/adaptivesuite-tutorial/IC805648.png "Gebruiker toevoegen")

4. Voer de volgende stappen uit in de sectie **nieuwe gebruiker** :

   ![Verzenden](./media/adaptivesuite-tutorial/IC805649.png "Verzenden")

   a. Typ de **naam**, de **gebruikers naam**, het **e-mail adres**en het **wacht woord** van een geldige Azure Active Directory gebruiker die u wilt inrichten in de bijbehorende tekst vakken.

   b. Selecteer een **rol**.

   c. Klik op **verzenden**.

> [!NOTE]
> U kunt alle andere hulpprogram ma's voor het maken van een adaptief Insights-gebruikers account of Api's die worden geleverd door adaptieve inzichten, gebruiken om Azure AD-gebruikers accounts in te richten.

### <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel adaptieve inzichten in het toegangs venster klikt, moet u automatisch worden aangemeld bij de adaptieve inzichten waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

