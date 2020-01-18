---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met werk plek via Facebook | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding tussen Azure Active Directory en Workplace by Facebook configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 30f2ee64-95d3-44ef-b832-8a0a27e2967c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/07/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3252b7b257fda96b3d711c5f47ec7c6eb7ee36cb
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76262148"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workplace-by-facebook"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met werk plek via Facebook

In deze zelf studie leert u hoe u werk plek kunt integreren met Facebook met Azure Active Directory (Azure AD). Wanneer u werk plek via Facebook integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de werk plek via Facebook.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij werk plek via Facebook met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Werk plek via Facebook-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

> [!NOTE]
> Facebook heeft twee producten, Workplace Standard (gratis) en Workplace Premium (betaald). Elke tenant van Workplace Premium kan gratis en zonder licenties SCIM- en SSO-integratie configureren. SSO en SCIM zijn niet beschikbaar in exemplaren van Workplace Standard.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Workplace by Facebook ondersteunt door **SP** geïnitieerde SSO
* Workplace by Facebook ondersteunt **just-in-time inrichten**
* Workplace by Facebook ondersteunt **[automatisch inrichten van gebruikers](workplacebyfacebook-provisioning-tutorial.md)**
* De mobiele toepassing voor werk plekken kan nu worden geconfigureerd met Azure AD om SSO in te scha kelen. In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

## <a name="adding-workplace-by-facebook-from-the-gallery"></a>Workplace by Facebook toevoegen vanuit de galerie

Om de integratie van Workplace by Facebook in Azure AD te configureren, moet u Workplace by Facebook vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in het gedeelte **toevoegen vanuit de galerie** de tekst **werk plek op Facebook** in het zoekvak.
1. Selecteer **werk plek op Facebook** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-sso-for-workplace-by-facebook"></a>Azure AD SSO voor werk plek configureren en testen met Facebook

Azure AD SSO configureren en testen met behulp van een Facebook met een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in werk plek op Facebook.

Als u Azure AD SSO wilt configureren en testen met behulp van Facebook, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
2. **[Werk plek configureren via Facebook SSO](#configure-workplace-by-facebook-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Werk plek maken op basis van Facebook test gebruiker](#create-workplace-by-facebook-test-user)** : als u een tegen hanger van B. Simon in werk plek wilt hebben op Facebook dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
3. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina **werk plek per Facebook** -toepassings integratie en selecteer de sectie voor het **beheren** van **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<instancename>.facebook.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://www.facebook.com/company/<instanceID>`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://www.facebook.com/company/<instanceID>`

    > [!NOTE]
    > Dit zijn niet de echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Zie de pagina verificatie van het dash board bedrijfs netwerk voor de juiste waarden voor uw werkplek community. dit wordt verderop in de zelf studie uitgelegd.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer op de sectie **werk plek instellen per Facebook** de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de werk plek via Facebook.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **werk plek op Facebook**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-workplace-by-facebook-sso"></a>Werk plek configureren via Facebook SSO

1. Als u de configuratie binnen werk plek wilt automatiseren via Facebook, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

1. Nadat u een uitbrei ding aan de browser hebt toegevoegd, klikt u op **werk plek instellen via Facebook** , wordt u doorgestuurd naar de toepassing werk plek op Facebook. Geef de beheerders referenties op om u aan te melden op de werk plek via Facebook. Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-5 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

1. Als u werk plek via Facebook hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij uw werk plek op de Facebook-bedrijfs site als beheerder en voert u de volgende stappen uit:

    > [!NOTE]
    > Als onderdeel van het SAML-verificatieproces kan Workplace queryreeksen van maximaal 2,5 kB gebruiken om parameters door te geven aan Azure AD.

1. Navigeer in het navigatie venster aan de linkerkant naar **beveiliging** > tabblad **verificatie** .

    ![Deelvenster Beheer](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure01.png)

    a. Controleer de optie **voor eenmalige aanmelding (SSO)** .
    
    b. Klik op **+ nieuwe SSO-provider toevoegen**.

1. Selecteer op het tabblad **Verificatie** de optie **Eenmalige aanmelding (SSO)** en voer de volgende stappen uit:

    ![Tabblad Verificatie](./media/workplacebyfacebook-tutorial/tutorial-workplace-by-facebook-configure02.png)

    a. Voer in de **naam van de SSO-provider**de SSO-instantie naam in, zoals Azureadsso.

    b. Plak in het tekstvak **SAM-URL** de waarde van **Aanmeldings-URL**, die u hebt gekopieerd uit de Azure-portal.

    c. Plak in het tekstvak **URL van SAML-Uitgever** de waarde van de **Azure ad-id**die u van Azure Portal hebt gekopieerd.

    d. Open in Kladblok het **met Base 64 gecodeerde certificaat** dat u hebt gedownload uit de Azure-portal, kopieer de inhoud ervan naar het Klembord en plak deze vervolgens in het tekstvak **SAML-certificaat**.

    e. Kopieer de **URL van de doel groep** voor uw exemplaar en plak het in het tekstvak **id (Entiteits-ID)** in het gedeelte **basis configuratie van SAML** op Azure Portal.

    f. Kopieer de **URL** van de ontvanger voor uw exemplaar en plak deze in het tekstvak **URL aanmelden** in de sectie **basis configuratie van SAML** op Azure Portal.

    g. Kopieer de **ACS-URL (assertion Consumer Service)** voor uw exemplaar en plak deze in het tekstvak **antwoord-URL** in het gedeelte **basis-SAML-configuratie** op Azure Portal.

    h. Blader naar de onderkant van het gedeelte en klik op de knop **SSO testen**. Er verschijnt een pop-upvenster met de aanmeldingspagina van Azure AD. Voer uw referenties op de gebruikelijke manier in om te verifiëren.

    **Problemen oplossen:** Zorg ervoor dat het e-mail adres dat wordt teruggestuurd vanuit Azure AD hetzelfde is als het werkplek account waarmee u bent aangemeld.

    i. Zodra de test is voltooid, bladert u naar de onderkant van de pagina en klikt u op de knop **Opslaan**.

    j. Alle gebruikers van Workplace zien nu voortaan de Azure AD-aanmeldingspagina voor verificatie.

1. **Omleiden SAML-afmelding (optioneel)**  -

    U kunt eventueel een URL voor SAML afmelding configureren, die kan worden gebruikt om te verwijzen naar de afmeldingspagina van Azure AD. Als deze instelling is ingeschakeld en geconfigureerd, wordt de gebruiker niet meer omgeleid naar de afmeldingspagina van Workplace. In plaats daarvan wordt de gebruiker omgeleid naar de URL die is opgegeven in het tekstvak Omleiden SAML-afmelding.

### <a name="configuring-reauthentication-frequency"></a>Herauthenticatie frequentie configureren

U kunt instellen dat Workplace elke dag, drie dagen, week, twee weken, maanden of nooit vraagt om een SAML-controle.

> [!NOTE]
> De minimumwaarde voor de SAML-controle voor mobiele toepassingen is ingesteld op één week.

U kunt ook een SAML reset voor alle gebruikers forceren met behulp van de knop: SAML-verificatie vereisen voor alle gebruikers.

### <a name="create-workplace-by-facebook-test-user"></a>Testgebruiker voor Workplace by Facebook maken

In deze sectie wordt een gebruiker met de naam B. Simon gemaakt op de werk plek op Facebook. Workplace by Facebook ondersteunt just-in-time inrichten. Deze functie is standaard ingeschakeld.

U hoeft geen handelingen uit te voeren in dit gedeelte. Als er geen gebruiker bestaat in Workplace by Facebook, wordt er een nieuwe gemaakt wanneer u probeert toegang te krijgen tot Workplace by Facebook.

>[!Note]
>Als u handmatig een gebruiker wilt maken, neemt u contact op met [het ondersteuningsteam van Workplace by Facebook](https://workplace.fb.com/faq/).

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Workplace by Facebook klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van in het toegangsvenster waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="test-sso-for-workplace-by-facebook-mobile"></a>SSO voor werk plek testen via Facebook (mobiel)

1. Open werk plek via Facebook Mobile Application. Klik op de pagina aanmelden op **Aanmelden**.

    ![De aanmelding](./media/workplacebyfacebook-tutorial/test05.png)

2. Voer uw zakelijke e-mail adres in en klik op **door gaan**.

    ![Het e-mail adres](./media/workplacebyfacebook-tutorial/test02.png)

3. Klik op **slechts één keer**.

    ![Eenmaal](./media/workplacebyfacebook-tutorial/test04.png)

4. Klik op **Toestaan**.

    ![De toestaan](./media/workplacebyfacebook-tutorial/test03.png)

5. Als het aanmelden is voltooid, wordt de start pagina van de toepassing weer gegeven.    

    ![De start pagina](./media/workplacebyfacebook-tutorial/test01.png)

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Inrichten van gebruikers configureren](workplacebyfacebook-provisioning-tutorial.md)

- [Probeer werk plek van Facebook uit met Azure AD](https://aad.portal.azure.com)
