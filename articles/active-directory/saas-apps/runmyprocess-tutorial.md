---
title: 'Zelf studie: integratie Azure Active Directory met RunMyProcess | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en RunMyProcess.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d31f7395-048b-4a61-9505-5acf9fc68d9b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46c31a209e8521b24e7f604dbe630f689fca484e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "68880372"
---
# <a name="tutorial-integrate-runmyprocess-with-azure-active-directory"></a>Zelf studie: RunMyProcess integreren met Azure Active Directory

In deze zelf studie leert u hoe u RunMyProcess integreert met Azure Active Directory (Azure AD). Wanneer u RunMyProcess integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot RunMyProcess.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij RunMyProcess met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* RunMyProcess-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* RunMyProcess ondersteunt door **SP** GEÏNITIEERDe SSO

## <a name="adding-runmyprocess-from-the-gallery"></a>RunMyProcess toevoegen uit de galerie

Als u de integratie van RunMyProcess in azure AD wilt configureren, moet u RunMyProcess uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **RunMyProcess** in het zoekvak.
1. Selecteer **RunMyProcess** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO met RunMyProcess configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in RunMyProcess.

Als u Azure AD SSO wilt configureren en testen met RunMyProcess, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
2. **[RUNMYPROCESS SSO configureren](#configure-runmyprocess-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
3. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
4. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
5. **[Maak een RunMyProcess-test gebruiker](#create-runmyprocess-test-user)** -om een equivalent van B. Simon in RunMyProcess te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **RunMyProcess** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://live.runmyprocess.com/live/<tenant id>`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteunings team van RunMyProcess](mailto:support@runmyprocess.com) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **RunMyProcess instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-runmyprocess-sso"></a>RunMyProcess SSO configureren

1. Meld u in een ander browser venster aan bij uw RunMyProcess-Tenant als beheerder.

1. Klik in het navigatie venster aan de linkerkant op **account** en selecteer **configuratie**.

    ![Eenmalige aanmelding aan app-zijde configureren](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

1. Ga naar de sectie **verificatie methode** en voer de onderstaande stappen uit:

    ![Eenmalige aanmelding aan app-zijde configureren](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. Selecteer als **methode** **SSO met Samlv2**.

    b. Plak in het tekstvak **SSO-omleiding** de waarde van de **aanmeldings-URL**die u van Azure Portal hebt gekopieerd.

    c. Plak de waarde van de **Afmeldings-URL**die u van Azure Portal hebt gekopieerd in het tekstvak **afleiding van afmeldingen** .

    d. Typ in het tekstvak **naam-ID-indeling** de waarde van de **indeling naam-id** als **urn: Oasis: names: TC: SAML: 1.1: NameID-indeling: emailAddress**.

    e. Open het gedownloade certificaat bestand van Azure Portal in Klad blok, kopieer de inhoud van het certificaat bestand en plak het in het tekstvak **certificaat** .

    f. Klik op pictogram **Opslaan** .

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan RunMyProcess.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **RunMyProcess**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

### <a name="create-runmyprocess-test-user"></a>RunMyProcess-test gebruiker maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij RunMyProcess, moeten ze worden ingericht in RunMyProcess. In het geval van RunMyProcess is inrichting een hand matige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u als beheerder aan bij de RunMyProcess-bedrijfs site.

1. Klik op **account** en selecteer **gebruikers** in het navigatie venster aan de linkerkant en klik vervolgens op **nieuwe gebruiker**.

    ![Nieuwe gebruiker](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "Nieuwe gebruiker")

1. Voer de volgende stappen uit in de sectie **gebruikers instellingen** :

    ![Profiel](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "Profiel")
  
    a. Typ de **naam** en het **e-mail adres** van een geldig Azure ad-account dat u wilt inrichten in de bijbehorende tekst vakken.

    b. Selecteer een **IDE-taal**,- **taal**en- **profiel**.

    c. Selecteer **e-mail voor het maken van een account naar mij verzenden**.

    d. Klik op **Opslaan**.

    > [!NOTE]
    > U kunt alle andere hulpprogram ma's voor het maken van RunMyProcess-gebruikers accounts of Api's die worden geleverd door RunMyProcess, gebruiken om Azure Active Directory gebruikers accounts in te richten.

### <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel RunMyProcess in het toegangs venster klikt, moet u automatisch worden aangemeld bij de RunMyProcess waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)