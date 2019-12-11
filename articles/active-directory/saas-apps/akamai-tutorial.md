---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Akamai | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Akamai.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1b7e0d7a-e78f-43a5-af93-b626186e2376
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 042dd242285081001ca48c9f17e4d42c2294c0ff
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979593"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Akamai

In deze zelf studie leert u hoe u Akamai integreert met Azure Active Directory (Azure AD). Wanneer u Akamai integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Akamai.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Akamai met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Akamai-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

- Toegestane vertraging biedt ondersteuning voor door IDP geïnitieerde SSO

## <a name="adding-akamai-from-the-gallery"></a>Akamai toevoegen uit de galerie

Als u de integratie van Akamai in azure AD wilt configureren, moet u Akamai uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Akamai** in het zoekvak.
1. Selecteer **Akamai** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-akamai"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Akamai

Azure AD SSO met Akamai configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Akamai.

Als u Azure AD SSO wilt configureren en testen met Akamai, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[AKAMAI SSO configureren](#configure-akamai-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een Akamai-test gebruiker](#create-akamai-test-user)** -om een equivalent van B. Simon in Akamai te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **Akamai** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<Yourapp>.login.go.akamai-access.com/sp/response`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https:// <Yourapp>.login.go.akamai-access.com/sp/response`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [ondersteunings team van Akamai-clients](https://www.akamai.com/us/en/contact-us/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Op de sectie **Akamai instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam** `B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Akamai.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Akamai**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-akamai-sso"></a>Akamai SSO configureren

### <a name="setting-up-idp"></a>IDP instellen

1. Aanmelden bij **Akamai Enter prise Application Access** -console.
1. Selecteer op de **AKAMAI EAA**-console **identiteit** > **id-providers**.

    ![Akamai configureren](./media/header-akamai-tutorial/configure01.png)

1. Klik op **ID-provider toevoegen**.

    ![Akamai configureren](./media/header-akamai-tutorial/configure02.png)

    a. Geef de **unieke naam**op.
    
    b. Kies **SAML** van derden en klik op **ID-provider maken en configureren**.

### <a name="general-settings"></a>Algemene instellingen

1. **Identity-interceptie** : Geef de naam op van de (SP Base-URL) die wordt gebruikt voor de configuratie van Azure AD.

    > [!NOTE]
    > U kunt ervoor kiezen om uw eigen aangepaste domein te hebben (er is een DNS-vermelding en een certificaat vereist). In dit voor beeld gaan we het Akamai-domein gebruiken.

1. **Akamai-Cloud zone** : Selecteer de juiste Cloud zone.
1. **Certificaat validatie** : Raadpleeg de documentatie van Akamai (optioneel)

    ![Akamai configureren](./media/header-akamai-tutorial/configure03.png)

### <a name="authentication-configuration"></a>Verificatie configuratie

1. URL: Geef de URL op zoals het onderscheppen van uw identiteit (dit is de locatie waar gebruikers worden omgeleid na verificatie).
2. Afmeldings-URL: werk de afmeldings-URL bij.
3. SAML-aanvraag ondertekenen: standaard uitgeschakeld.
4. Voeg voor het meta gegevensbestand IDP de toepassing toe aan de Azure AD-console.

    ![Akamai configureren](./media/header-akamai-tutorial/configure04.png)

### <a name="header-based-authentication"></a>Verificatie op basis van koptekst

Verificatie op basis van Akamai-header

1. Kies **aangepaste HTTP-** formulier de wizard toepassingen toevoegen.

    ![Akamai configureren](./media/header-akamai-tutorial/configure05.png)

    ![Akamai configureren](./media/header-akamai-tutorial/configure06.png)

    ![Akamai configureren](./media/header-akamai-tutorial/configure07.png)

    ![Akamai configureren](./media/header-akamai-tutorial/configure08.png)

#### <a name="authentication"></a>Verificatie

![Akamai configureren](./media/header-akamai-tutorial/configure09.png)

![Akamai configureren](./media/header-akamai-tutorial/configure10.png)

#### <a name="services"></a>Services

1. Klik op opslaan en ga naar verificatie.

![Akamai configureren](./media/header-akamai-tutorial/configure11.png)

#### <a name="advanced-settings"></a>Geavanceerde instellingen

1. Geef onder **HTTP-headers van klant**het kenmerk **CustomerHeader** en **SAML**op.

    ![Akamai configureren](./media/header-akamai-tutorial/configure12.png)

1. Klik op **opslaan en ga naar de implementatie** knop.

    ![Akamai configureren](./media/header-akamai-tutorial/configure13.png)

#### <a name="deploy-the-application"></a>De toepassing implementeren

1. Klik op de knop **toepassing implementeren** .

    ![Akamai configureren](./media/header-akamai-tutorial/configure14.png)

1. Controleer of de toepassing is geïmplementeerd.

    ![Akamai configureren](./media/header-akamai-tutorial/configure15.png)

### <a name="kerberos-authentication"></a>Kerberos-authenticatie

#### <a name="remote-desktop"></a>Extern bureaublad

1. Kies **RDP** in de wizard toepassingen toevoegen.

    ![Akamai configureren](./media/header-akamai-tutorial/configure16.png)

    ![Akamai configureren](./media/header-akamai-tutorial/configure17.png)

    ![Akamai configureren](./media/header-akamai-tutorial/configure18.png)

1. Geef de connector op die dit gaat verwerken.

    ![Akamai configureren](./media/header-akamai-tutorial/configure19.png)

#### <a name="authentication"></a>Verificatie

Klik op **opslaan en ga naar Services**.

![Akamai configureren](./media/header-akamai-tutorial/configure20.png)

#### <a name="services"></a>Services

Klik op **opslaan en ga naar geavanceerde instellingen**.

![Akamai configureren](./media/header-akamai-tutorial/configure21.png)

#### <a name="advanced-settings"></a>Geavanceerde instellingen

Klik op **opslaan en ga naar implementatie**.

![Akamai configureren](./media/header-akamai-tutorial/configure22.png)

![Akamai configureren](./media/header-akamai-tutorial/configure23.png)

![Akamai configureren](./media/header-akamai-tutorial/configure24.png)

### <a name="deployment"></a>Implementatie

#### <a name="ssh"></a>SSH

1. Ga naar toepassingen toevoegen en kies **SSH**.

    ![Akamai configureren](./media/header-akamai-tutorial/configure25.png)

    ![Akamai configureren](./media/header-akamai-tutorial/configure26.png)

1. Configureer de toepassings-id.

    ![Akamai configureren](./media/header-akamai-tutorial/configure27.png)

    a. Geef een naam/beschrijving op.

    b. Geef het IP-adres/de FQDN en poort van de toepassings server op voor SSH.

    c. SSH-gebruikers naam/wachtwoordzin opgeven * Controleer Akamai EAA.

    d. Geef de naam van de externe host op.

    e. Geef de locatie voor de connector op en kies de connector.

#### <a name="authentication"></a>Verificatie

Klik op **opslaan en ga naar Services**.

![Akamai configureren](./media/header-akamai-tutorial/configure28.png)

#### <a name="services"></a>Services

Klik op **opslaan en ga naar geavanceerde instellingen**.

![Akamai configureren](./media/header-akamai-tutorial/configure29.png)

#### <a name="advanced-settings"></a>Geavanceerde instellingen

Klik op opslaan en to go-implementatie

![Akamai configureren](./media/header-akamai-tutorial/configure30.png)

![Akamai configureren](./media/header-akamai-tutorial/configure31.png)

#### <a name="deployment"></a>Implementatie

Klik op **toepassing implementeren**.

![Akamai configureren](./media/header-akamai-tutorial/configure32.png)

### <a name="kerberos-applications"></a>Kerberos-toepassingen

#### <a name="adding-directory"></a>Map toevoegen

![Akamai configureren](./media/header-akamai-tutorial/configure33.png)

![Akamai configureren](./media/header-akamai-tutorial/configure34.png)

![Akamai configureren](./media/header-akamai-tutorial/configure35.png)

![Akamai configureren](./media/header-akamai-tutorial/configure36.png)

### <a name="create-akamai-test-user"></a>Akamai-test gebruiker maken

In deze sectie maakt u een gebruiker met de naam B. Simon in Akamai. Werk samen met het [Akamai-client ondersteunings team](https://www.akamai.com/us/en/contact-us/) om de gebruikers toe te voegen in het Akamai-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken. 

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Akamai in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Akamai waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Akamai met Azure AD](https://aad.portal.azure.com/)
