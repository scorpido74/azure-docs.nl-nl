---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Sonarqube | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Sonarqube.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b01665e7-c3d0-4393-9286-d5bcf8cf6add
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 571d8849fd7cae5c872a56182858848dbb43ef42
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72026683"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sonarqube"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Sonarqube

In deze zelfstudie leert u hoe u Sonarqube integreren met Azure Active Directory (Azure AD). Wanneer u Sonarqube integreert met Azure AD, u het als:

* Controle in Azure AD die toegang heeft tot Sonarqube.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Sonarqube met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Sonarqube single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Sonarqube ondersteunt **SP** geïnitieerde SSO

> [!NOTE]
> Id van deze toepassing is een vaste tekenreekswaarde, zodat slechts één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-sonarqube-from-the-gallery"></a>Sonarqube toevoegen vanuit de galerie

Als u de integratie van Sonarqube in Azure AD wilt configureren, moet u Sonarqube vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Sonarqube** in de sectie **Toevoegen vanuit de galerie** in het zoekvak.
1. Selecteer **Sonarqube** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sonarqube"></a>Azure AD-aanmelding voor Sonarqube configureren en testen

Azure AD SSO configureren en testen met Sonarqube met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Sonarqube.

Als u Azure AD SSO wilt configureren en testen met Sonarqube, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Sonarqube SSO](#configure-sonarqube-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Maak Sonarqube-testgebruiker](#create-sonarqube-test-user)** - om een tegenhanger van B.Simon in Sonarqube te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Sonarqube-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    Typ een URL in het tekstvak **Aanmeldings-URL**: 

    * **Voor productieomgeving**

        `https://servicessonar.corp.microsoft.com/`

    * **Voor Dev Environment**

        `https://servicescode-dev.westus.cloudapp.azure.com`

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Sonarqube instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Sonarqube.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Sonarqube**in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-sonarqube-sso"></a>Sonarqube SSO configureren

1. Open een nieuw webbrowservenster en meld u aan op uw Sonarqube-bedrijfssite als beheerder.

2. Installeer de SAML plugin van de sonarqube marktplaats.

3. Klik linksboven op de pagina op **BEHEERDER** en navigeer vervolgens naar **SAML.**

4. Voer op de **SAML-pagina** de volgende stappen uit:

    ![Sonarqube-configuratie](./media/sonarqube-tutorial/config01.png)

    a. Schakel de optie **Ingeschakeld in** op **ja**.

    b. Voer in het tekstvak **Toepassings-ID** de naam als **sonarqube**in.

    c. Voer in het tekstvak **Providernaam** de naam als **SAML in.**

    d. Plak in het tekstvak **Provider-id** de waarde van **Azure AD-id**, die u hebt gekopieerd van Azure-portal.

    e. Plak in **het tekstvak URL-inlogformulier saml** de waarde van **de aanmeldings-URL**, die u hebt gekopieerd van azure-portal.

    f. Open het certificaat Base64 gecodeerd in kladblok, kopieer de inhoud ervan en plak het in het tekstvak van het **providercertificaat.**

    g. Voer in **het inlogvak van saml-gebruikershet aanmeldingsvak** de waarde `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`in .

    h. Voer in het tekstvak **saml-gebruikersnaamkenmerk** de waarde `http://schemas.microsoft.com/identity/claims/displayname`in .

    i. Voer in **het tekstvak van het e-mailkenmerk SAML-gebruiker** de waarde `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`in .

    j. Klik op **Opslaan**.

### <a name="create-sonarqube-test-user"></a>Sonarqube-testgebruiker maken

In deze sectie maakt u een gebruiker genaamd B.Simon in Sonarqube. Werk samen met [sonarqube client ondersteuningsteam](https://www.sonarsource.com/support/) om de gebruikers toe te voegen in het Sonarqube-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken. 

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de sonarqube-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Sonarqube waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Sonarqube met Azure AD](https://aad.portal.azure.com/)

