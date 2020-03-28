---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Akamai | Microsoft Documenten'
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74979593"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-akamai"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Akamai

In deze zelfstudie leert u hoe u Akamai integreert met Azure Active Directory (Azure AD). Wanneer u Akamai integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Akamai.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Akamai met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Akamai single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

- Slack ondersteunt IDP geïnitieerde SSO

## <a name="adding-akamai-from-the-gallery"></a>Akamai toevoegen vanuit de galerie

Als u de integratie van Akamai in Azure AD wilt configureren, moet u Akamai uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Akamai** in de sectie **Toevoegen in de galerijsectie** in het zoekvak.
1. Selecteer **Akamai** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-akamai"></a>Azure AD-aanmelding voor Akamai configureren en testen

Azure AD SSO configureren en testen met Akamai met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Akamai.

Als u Azure AD SSO met Akamai wilt configureren en testen, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Akamai SSO](#configure-akamai-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Akamai-testgebruiker maken](#create-akamai-test-user)** - om een tegenhanger van B.Simon in Akamai te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Akamai-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://<Yourapp>.login.go.akamai-access.com/sp/response`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https:// <Yourapp>.login.go.akamai-access.com/sp/response`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met [akamai Client support team](https://www.akamai.com/us/en/contact-us/) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **Akamai instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Akamai.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Akamai**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-akamai-sso"></a>Akamai SSO configureren

### <a name="setting-up-idp"></a>IDP instellen

1. Meld u aan bij **akamai Enterprise Application** Access-console.
1. Op de **Akamai EAA-console**selecteert u > **identiteitsproviders**. **Identity**

    ![Akamai configureren](./media/header-akamai-tutorial/configure01.png)

1. Klik **op Identiteitsprovider toevoegen**.

    ![Akamai configureren](./media/header-akamai-tutorial/configure02.png)

    a. Geef de **unieke naam op**.
    
    b. Kies **SAML van derden** en klik op **Identiteitsprovider maken en configureren**.

### <a name="general-settings"></a>Algemene instellingen

1. **Identiteitsonderschepping** - Geef de naam op van de URL van de (SP-basis-URL- wordt gebruikt voor Azure AD-configuratie)

    > [!NOTE]
    > U ervoor kiezen om uw eigen aangepaste domein te hebben (vereist een DNS-vermelding en een certificaat). In dit voorbeeld gaan we het Akamai-domein gebruiken.

1. **Akamai Cloud Zone** - Selecteer de juiste cloudzone.
1. **Certificaatvalidatie** - Akamai-documentatie controleren (optioneel)

    ![Akamai configureren](./media/header-akamai-tutorial/configure03.png)

### <a name="authentication-configuration"></a>Verificatieconfiguratie

1. URL - Geef de URL op die hetzelfde is als uw identiteitsonderschepping (dit is waar gebruikers worden omgeleid na verificatie).
2. URL van afmelden : De aanmeldings-URL bijwerken.
3. Teken SAML-aanvraag: standaard niet ingeschakeld.
4. Voeg de toepassing toe aan het IDP-metagegevensbestand in de Azure AD-console.

    ![Akamai configureren](./media/header-akamai-tutorial/configure04.png)

### <a name="header-based-authentication"></a>Verificatie op basis van kopteksten

Verificatie op basis van Akamai-header

1. Kies **Aangepast HTTP-formulier** in de wizard Toepassingen toevoegen.

    ![Akamai configureren](./media/header-akamai-tutorial/configure05.png)

    ![Akamai configureren](./media/header-akamai-tutorial/configure06.png)

    ![Akamai configureren](./media/header-akamai-tutorial/configure07.png)

    ![Akamai configureren](./media/header-akamai-tutorial/configure08.png)

#### <a name="authentication"></a>Authentication

![Akamai configureren](./media/header-akamai-tutorial/configure09.png)

![Akamai configureren](./media/header-akamai-tutorial/configure10.png)

#### <a name="services"></a>Services

1. Klik op Opslaan en ga naar verificatie.

![Akamai configureren](./media/header-akamai-tutorial/configure11.png)

#### <a name="advanced-settings"></a>Geavanceerde instellingen

1. Geef onder de **HTTP-headers van**de klant het kenmerk **CustomerHeader** en **SAML op**.

    ![Akamai configureren](./media/header-akamai-tutorial/configure12.png)

1. Klik op Opslaan en ga naar de knop **Implementatie.**

    ![Akamai configureren](./media/header-akamai-tutorial/configure13.png)

#### <a name="deploy-the-application"></a>De toepassing implementeren

1. Klik **op Knop Toepassing implementeren.**

    ![Akamai configureren](./media/header-akamai-tutorial/configure14.png)

1. Controleer of de toepassing is geïmplementeerd.

    ![Akamai configureren](./media/header-akamai-tutorial/configure15.png)

### <a name="kerberos-authentication"></a>Kerberos-authenticatie

#### <a name="remote-desktop"></a>Extern bureaublad

1. Kies **RDP** in de wizard Toepassingen TOEVOEGEN.

    ![Akamai configureren](./media/header-akamai-tutorial/configure16.png)

    ![Akamai configureren](./media/header-akamai-tutorial/configure17.png)

    ![Akamai configureren](./media/header-akamai-tutorial/configure18.png)

1. Geef de connector op die dit onderhoud mogelijk maakt.

    ![Akamai configureren](./media/header-akamai-tutorial/configure19.png)

#### <a name="authentication"></a>Authentication

Klik **op Opslaan en ga naar Services**.

![Akamai configureren](./media/header-akamai-tutorial/configure20.png)

#### <a name="services"></a>Services

Klik **op Opslaan en ga naar Geavanceerde instellingen**.

![Akamai configureren](./media/header-akamai-tutorial/configure21.png)

#### <a name="advanced-settings"></a>Geavanceerde instellingen

Klik **op Opslaan en ga naar Implementatie**.

![Akamai configureren](./media/header-akamai-tutorial/configure22.png)

![Akamai configureren](./media/header-akamai-tutorial/configure23.png)

![Akamai configureren](./media/header-akamai-tutorial/configure24.png)

### <a name="deployment"></a>Implementatie

#### <a name="ssh"></a>SSH

1. Ga naar Toepassingen toevoegen, Kies **SSH**.

    ![Akamai configureren](./media/header-akamai-tutorial/configure25.png)

    ![Akamai configureren](./media/header-akamai-tutorial/configure26.png)

1. Toepassingsidentiteit configureren.

    ![Akamai configureren](./media/header-akamai-tutorial/configure27.png)

    a. Geef naam / beschrijving op.

    b. Geef IP/FQDN van de toepassingsserver en poort voor SSH op.

    c. Geef ssh gebruikersnaam / wachtwoordzin * Check Akamai EAA.

    d. Geef de naam van externe host op.

    e. Geef de locatie voor de connector op en kies de connector.

#### <a name="authentication"></a>Authentication

Klik op **Opslaan en ga naar Services**.

![Akamai configureren](./media/header-akamai-tutorial/configure28.png)

#### <a name="services"></a>Services

Klik **op Opslaan en ga naar Geavanceerde instellingen**.

![Akamai configureren](./media/header-akamai-tutorial/configure29.png)

#### <a name="advanced-settings"></a>Geavanceerde instellingen

Klik op Opslaan en gaan implementatie

![Akamai configureren](./media/header-akamai-tutorial/configure30.png)

![Akamai configureren](./media/header-akamai-tutorial/configure31.png)

#### <a name="deployment"></a>Implementatie

Klik **op Toepassing implementeren**.

![Akamai configureren](./media/header-akamai-tutorial/configure32.png)

### <a name="kerberos-applications"></a>Kerberos-toepassingen

#### <a name="adding-directory"></a>Map toevoegen

![Akamai configureren](./media/header-akamai-tutorial/configure33.png)

![Akamai configureren](./media/header-akamai-tutorial/configure34.png)

![Akamai configureren](./media/header-akamai-tutorial/configure35.png)

![Akamai configureren](./media/header-akamai-tutorial/configure36.png)

### <a name="create-akamai-test-user"></a>Akamai-testgebruiker maken

In deze sectie maakt u een gebruiker genaamd B.Simon in Akamai. Werk samen met [akamai Client support team](https://www.akamai.com/us/en/contact-us/) om de gebruikers toe te voegen in het Akamai-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken. 

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Akamai in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Akamai waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Akamai met Azure AD](https://aad.portal.azure.com/)
