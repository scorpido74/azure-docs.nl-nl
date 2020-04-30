---
title: 'Zelf studie: integratie Azure Active Directory met AlertOps | Microsoft Docs'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en AlertOps.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 3db13ed4-35c2-4b1e-bed8-9b5977061f93
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 21b8cb06712e370972e0b8fec518c37d078262e0
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67107062"
---
# <a name="tutorial-integrate-alertops-with-azure-active-directory"></a>Zelf studie: AlertOps integreren met Azure Active Directory

In deze zelf studie leert u hoe u AlertOps integreert met Azure Active Directory (Azure AD). Wanneer u AlertOps integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot AlertOps.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij AlertOps met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* AlertOps-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving. AlertOps ondersteunt door **SP en IDP** geïnitieerde SSO.

## <a name="adding-alertops-from-the-gallery"></a>AlertOps toevoegen vanuit de galerie

Om de integratie van AlertOps in Azure AD te configureren, moet u AlertOps vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **AlertOps** in het zoekvak.
1. Selecteer **AlertOps** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Configureer en test Azure AD SSO met AlertOps met behulp van een test gebruiker met de naam **Julia Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in AlertOps.

Als u Azure AD SSO wilt configureren en testen met AlertOps, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer AlertOps](#configure-alertops)** om de SSO-instellingen aan de kant van de toepassing te configureren.
3. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** om eenmalige aanmelding voor Azure ad te testen met Julia Simon.
4. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe om Julia Simon in staat te stellen om eenmalige aanmelding van Azure ad te gebruiken.
5. **[Maak AlertOps-test gebruiker](#create-alertops-test-user)** een tegen hanger van Julia Simon in AlertOps dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **AlertOps** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus van **IDP** wilt configureren, voert u de volgende stappen uit in de sectie **basis configuratie van SAML** :

    1. Typ in het tekstvak **id** een URL met het volgende patroon:`https://<SUBDOMAIN>.alertops.com`

    1. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.alertops.com/login.aspx`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<SUBDOMAIN>.alertops.com/login.aspx`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van AlertOps](mailto:support@alertops.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **AlertOps instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-alertops"></a>AlertOps configureren

1. Als u de configuratie wilt automatiseren in AlertOps, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **Setup AlertOps** gaat u naar de AlertOps-toepassing. Geef de beheerders referenties op om u aan te melden bij AlertOps. Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-5 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

3. Als u AlertOps hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij uw AlertOps-bedrijfs site als beheerder en voert u de volgende stappen uit:

4. Klik in het linkernavigatievenster op **Account Settings**.

    ![AlertOps-configuratie](./media/alertops-tutorial/configure1.png)

5. Selecteer **SSO** op de pagina **Subscription Settings** en voer de volgende stappen uit:

    ![AlertOps-configuratie](./media/alertops-tutorial/configure2.png)

    a. Schakel het selectievakje **Use Single Sign-On(SSO)** in.

    b. Selecteer **Azure Active Directory** als een **SSO-provider** in de vervolg keuzelijst.

    c. In het tekstvak URL van de **Uitgever** gebruikt u de id-waarde, die u hebt gebruikt in de sectie **basis configuratie van SAML** in de Azure Portal.

    d. Plak in het tekstvak URL voor het **SAML-eind punt** de waarde voor de **aanmeldings-URL** , die u hebt gekopieerd uit de Azure Portal.

    e. Plak in het tekstvak **URL van Slo-eind punt** de waarde voor de **aanmeldings-URL** , die u hebt gekopieerd uit de Azure Portal.

    f. Selecteer **SHA256** in de vervolgkeuzelijst **SAML Signature Algorithm**.

    g. Open het gedownloade bestand Certificate(Base64) in Kladblok. Kopieer de inhoud van het bestand naar het klembord en plak deze vervolgens in het tekstvak X.509 Certificate.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam Julia Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`Britta Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u Julia Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan AlertOps.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **AlertOps** in de lijst met toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **Julia Simon** in de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

### <a name="create-alertops-test-user"></a>Een testgebruiker voor AlertOps maken

1. Meld u in een ander browser venster aan bij uw AlertOps-bedrijfs site als beheerder.

2. Klik op **Users** in het linkernavigatievenster.

    ![AlertOps-configuratie](./media/alertops-tutorial/user1.png)

3. Selecteer **gebruiker toevoegen**.

    ![AlertOps-configuratie](./media/alertops-tutorial/user2.png)

4. Voer in het dialoogvenster **Add User** de volgende stappen uit:

    ![AlertOps-configuratie](./media/alertops-tutorial/user3.png)

    a. Voer in het tekstvak **Login User Name** de gebruikersnaam van de gebruiker in, bijvoorbeeld **Brittasimon**.

    b. Voer in het tekstvak **officiële e-mail** het e-mail adres van de gebruiker in, zoals **Brittasimon\@contoso.com**.

    c. Voer in het tekstvak **First Name** de voornaam van de gebruiker in, zoals **Britta**.

    d. Voer in het tekstvak **Last Name** de achternaam van de gebruiker in, zoals **Simon**.

    e. Selecteer in de vervolgkeuzelijst **Type** een waarde die geschikt is voor uw organisatie.

    f. Selecteer in de vervolgkeuzelijst **Role** een waarde voor de rol van de gebruiker in uw organisatie.

    g. Selecteer **Toevoegen**.

### <a name="test-sso"></a>SSO testen

Wanneer u de tegel AlertOps in het toegangs venster selecteert, wordt u automatisch aangemeld bij de AlertOps waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)