---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met AcquireIO | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en AcquireIO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 97815e13-32ec-4470-b075-1253f5814f4f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01bcc3678485119afae1d567d97eff9dcebe6b95
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714618"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-acquireio"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met AcquireIO

In deze zelf studie leert u hoe u AcquireIO integreert met Azure Active Directory (Azure AD). Wanneer u AcquireIO integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot AcquireIO.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij AcquireIO met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* AcquireIO-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* AcquireIO ondersteunt door **IDP** GEÏNITIEERDe SSO

## <a name="adding-acquireio-from-the-gallery"></a>AcquireIO toevoegen uit de galerie

Als u de integratie van AcquireIO in azure AD wilt configureren, moet u AcquireIO uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **AcquireIO** in het zoekvak.
1. Selecteer **AcquireIO** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-acquireio"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor AcquireIO

Azure AD SSO met AcquireIO configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in AcquireIO.

Als u Azure AD SSO wilt configureren en testen met AcquireIO, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[ACQUIREIO SSO configureren](#configure-acquireio-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een AcquireIO-test gebruiker](#create-acquireio-test-user)** -om een equivalent van B. Simon in AcquireIO te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **AcquireIO** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://app.acquire.io/ad/<acquire_account_uid>`

    > [!NOTE]
    > De waarde is niet echt. U krijgt de daad werkelijke antwoord-URL die verderop wordt beschreven in de sectie **AcquireIO configureren** van de zelf studie. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **AcquireIO instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan AcquireIO.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **AcquireIO**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-acquireio-sso"></a>AcquireIO SSO configureren

1. Als u de configuratie wilt automatiseren in AcquireIO, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

1. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **AcquireIO instellen**. Hiermee wordt u doorgestuurd naar de AcquireIO-toepassing. Geef de beheerders referenties op om u aan te melden bij AcquireIO. Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-6 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

1. Als u AcquireIO hand matig wilt instellen, meldt u zich in een ander webbrowser venster aan bij AcquireIO als beheerder.

1. Klik aan de linkerkant van het menu op **App Store**.

     ![AcquireIO-configuratie](./media/acquireio-tutorial/config01.png)

1. Schuif omlaag naar **Active Directory** en klik op **installeren**.

    ![AcquireIO-configuratie](./media/acquireio-tutorial/config02.png)

1. Voer in het pop-upvenster Active Directory de volgende stappen uit:

    ![AcquireIO-configuratie](./media/acquireio-tutorial/config03.png)

    a. Klik op **kopiëren** om de antwoord-URL voor uw exemplaar te kopiëren en plak deze in het tekstvak **antwoord-URL** in het gedeelte basis-SAML- **configuratie** op Azure Portal.

    b. Plak in het tekstvak **aanmeldings-URL** de waarde van de **aanmeldings-URL**die u van Azure Portal hebt gekopieerd.

    c. Open het met base64 gecodeerde certificaat in Klad blok, kopieer de inhoud en plak het in het tekstvak **X. 509-certificaat** .

    d. Klik op **nu verbinding maken**.

### <a name="create-acquireio-test-user"></a>AcquireIO-test gebruiker maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij AcquireIO, moeten ze worden ingericht in AcquireIO. In AcquireIO is inrichten een hand matige taak.

**Als u een gebruikersaccount wilt inrichten, voert u de volgende stappen uit:**

1. Meld u in een ander browser venster aan bij AcquireIO als beheerder.

1. Klik aan de linkerkant van het menu op **profielen** en navigeer om **profiel toe te voegen**.

     ![AcquireIO-configuratie](./media/acquireio-tutorial/config04.png)

1. Voer de volgende stappen uit in het pop-upvenster **klant toevoegen** :

    ![AcquireIO-configuratie](./media/acquireio-tutorial/config05.png)

    a. Voer in het tekstvak **naam** de naam van de gebruiker in zoals **B. Simon**.

    b. Voer in het tekstvak **Email** het e-mailadres van de gebruiker in, zoals **B.simon@contoso.com** .

    c. Klik op **Submit**

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel AcquireIO in het toegangs venster klikt, moet u automatisch worden aangemeld bij de AcquireIO waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer AcquireIO met Azure AD](https://aad.portal.azure.com/)
