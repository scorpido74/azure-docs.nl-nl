---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met PurelyHR | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en PurelyHR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 86a9c454-596d-4902-829a-fe126708f739
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56049e1b1253cd749a8e16061957c6b5b8786e3c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "72594538"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purelyhr"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met PurelyHR

In deze zelf studie leert u hoe u PurelyHR integreert met Azure Active Directory (Azure AD). Wanneer u PurelyHR integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot PurelyHR.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij PurelyHR met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* PurelyHR-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* PurelyHR ondersteunt SSO die door **SP en IDP** is geïnitieerd
* PurelyHR ondersteunt **just-in-time** -gebruikers inrichting

## <a name="adding-purelyhr-from-the-gallery"></a>PurelyHR toevoegen uit de galerie

Als u de integratie van PurelyHR in azure AD wilt configureren, moet u PurelyHR uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **PurelyHR** in het zoekvak.
1. Selecteer **PurelyHR** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purelyhr"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor PurelyHR

Azure AD SSO met PurelyHR configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in PurelyHR.

Als u Azure AD SSO wilt configureren en testen met PurelyHR, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[PURELYHR SSO configureren](#configure-purelyhr-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een PurelyHR-test gebruiker](#create-purelyhr-test-user)** -om een equivalent van B. Simon in PurelyHR te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **PurelyHR** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    Typ in het tekstvak **antwoord-URL** een URL met het volgende patroon:`https://<companyID>.purelyhr.com/sso-consume`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<companyID>.purelyhr.com/sso-initiate`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de echte antwoord-URL en aanmeldings-URL. Neem contact op met het [ondersteunings team van PurelyHR-clients](https://support.purelyhr.com/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **PurelyHR instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan PurelyHR.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **PurelyHR**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-purelyhr-sso"></a>PurelyHR SSO configureren

1. Als u de configuratie wilt automatiseren in PurelyHR, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

1. Nadat u de extensie hebt toegevoegd aan de browser, klikt u op **PurelyHR instellen** gaat u naar de PurelyHR-toepassing. Geef de beheerders referenties op om u aan te melden bij PurelyHR. Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-5 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

1. Als u PurelyHR hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij uw PurelyHR-bedrijfs site als beheerder en voert u de volgende stappen uit:

1. Open het **dash board** via de opties op de werk balk en klik op **instellingen voor eenmalige aanmelding**.

1. Plak de waarden in de vakken zoals hieronder wordt beschreven:

    ![Eenmalige aanmelding configureren](./media/purelyhr-tutorial/purelyhr-dashboard-sso-settings.png)  

    a. Open het **certificaat (Bas64)** dat u hebt gedownload van de Azure Portal in Klad blok en kopieer de waarde van het certificaat. Plak de gekopieerde waarde in het vak **X. 509-certificaat** .

    b. Plak in het vak URL van de **IDP-Uitgever** de **Azure ad-id** die u hebt gekopieerd uit de Azure Portal.

    c. Plak in het vak **URL van IDP-eind punt** de **aanmeldings-URL** die u hebt gekopieerd uit de Azure Portal. 

    d. Schakel het selectie vakje **gebruikers automatisch maken** in om de automatische gebruikers inrichting in PurelyHR in te scha kelen.

    e. Klik op **wijzigingen opslaan** om de instellingen op te slaan.

### <a name="create-purelyhr-test-user"></a>PurelyHR-test gebruiker maken

Deze stap is doorgaans niet vereist omdat de toepassing just-in-time-gebruikers inrichting ondersteunt. Als de automatische gebruikers inrichting niet is ingeschakeld, kan hand matig maken van de gebruiker worden uitgevoerd, zoals hieronder wordt beschreven.

Meld u aan bij uw Velpic SAML-bedrijfs site als beheerder en voer de volgende stappen uit:

1. Klik op het tabblad beheren en ga naar de sectie gebruikers en klik vervolgens op de knop Nieuw om gebruikers toe te voegen.

    ![gebruiker toevoegen](./media/velpicsaml-tutorial/velpic_7.png)

2. Voer de volgende stappen uit op de dialoog pagina **' nieuwe gebruiker maken '** .

    ![gebruiker](./media/velpicsaml-tutorial/velpic_8.png)

    a. In het tekstvak **voor de voor naam** typt u de voor naam B.

    b. Typ in het tekstvak **Achternaam** de achternaam van Simon.

    c. Typ in het tekstvak **gebruikers naam** de gebruikers naam van B. Simon.

    d. Typ in het tekstvak **e-mail** het e-mail adres B.Simon@contoso.com van het account.

    e. De rest van de informatie is optioneel. u kunt deze indien nodig invullen.

    f. Klik op **Opslaan**.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel PurelyHR in het toegangs venster klikt, moet u automatisch worden aangemeld bij de PurelyHR waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer PurelyHR met Azure AD](https://aad.portal.azure.com/)
