---
title: 'Zelfstudie: Azure Active Directory-integratie met RunMyProcess | Microsoft Documenten'
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68880372"
---
# <a name="tutorial-integrate-runmyprocess-with-azure-active-directory"></a>Zelfstudie: RunMyProcess integreren met Azure Active Directory

In deze zelfstudie leert u hoe u RunMyProcess integreren met Azure Active Directory (Azure AD). Wanneer u RunMyProcess integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot RunMyProcess.
* Stel uw gebruikers in om automatisch te worden aangemeld bij RunMyProcess met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* RunMyProcess single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* RunMyProcess ondersteunt **DOOR SP** geïnitieerde SSO

## <a name="adding-runmyprocess-from-the-gallery"></a>RunMyProcess toevoegen vanuit de galerie

Als u de integratie van RunMyProcess in Azure AD wilt configureren, moet u RunMyProcess vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **RunMyProcess** in het zoekvak in de sectie **Toevoegen in de galeriesectie.**
1. Selecteer **RunMyProcess in** het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met RunMyProcess met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in RunMyProcess.

Als u Azure AD SSO wilt configureren en testen met RunMyProcess, voert u de volgende bouwstenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
2. **[RunMyProcess SSO configureren](#configure-runmyprocess-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
5. **[RunMyProcess-testgebruiker maken](#create-runmyprocess-test-user)** - om een tegenhanger van B.Simon te hebben in RunMyProcess die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **RunMyProcess-toepassingsintegratie** de sectie **Beheren** en selecteer **Enkele aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://live.runmyprocess.com/live/<tenant id>`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [runmyprocess client support team](mailto:support@runmyprocess.com) om de waarde te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **RunMyProcess instellen** de juiste URL(s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-runmyprocess-sso"></a>RunMyProcess SSO configureren

1. Meld je in een ander browservenster aan bij de runmyprocess-tenant als beheerder.

1. Klik in het linkernavigatiedeelvenster op **Account** en selecteer **Configuratie**.

    ![Eenmalige aanmelding aan app-zijde configureren](./media/runmyprocess-tutorial/tutorial_runmyprocess_001.png)

1. Ga naar de sectie **Verificatiemethode** en voer onderstaande stappen uit:

    ![Eenmalige aanmelding aan app-zijde configureren](./media/runmyprocess-tutorial/tutorial_runmyprocess_002.png)

    a. Als **methode**selecteert u **SSO met Samlv2**.

    b. Plak in het tekstvak **SSO-omleiding** de waarde van **de aanmeldings-URL**, die u hebt gekopieerd van azure-portal.

    c. Plak in het tekstvak **Afmelden omleiden** de waarde van **de URL van Afmelden**, die u hebt gekopieerd van azure-portal.

    d. Typ in het tekstvak **Naam-id-indeling** de waarde van **de opmaak van naam-id-indeling** als **urn:oasis:names:tc:SAML:1.1:nameid-notatie:emailAddress**.

    e. Open het gedownloade certificaatbestand van azure portal in kladblok, kopieer de inhoud van het certificaatbestand en plak het vervolgens in het **tekstvak Certificaat.**

    f. Klik **op Pictogram Opslaan.**

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot RunMyProcess.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **RunMyProcess**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

### <a name="create-runmyprocess-test-user"></a>RunMyProcess-testgebruiker maken

Als u Azure AD-gebruikers in staat moet stellen zich aan te melden bij RunMyProcess, moeten ze zijn ingericht in RunMyProcess. In het geval van RunMyProcess is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij uw runmyprocess-bedrijfssite als beheerder.

1. Klik **op Account** en selecteer **Gebruikers** in het linkernavigatiedeelvenster en klik vervolgens op Nieuwe **gebruiker**.

    ![Nieuwe gebruiker](./media/runmyprocess-tutorial/tutorial_runmyprocess_003.png "Nieuwe gebruiker")

1. Voer in de sectie **Gebruikersinstellingen** de volgende stappen uit:

    ![Profiel](./media/runmyprocess-tutorial/tutorial_runmyprocess_004.png "Profiel")
  
    a. Typ de **naam** en e-mail van een geldig Azure **AD-account** dat u wilt inrichten in de gerelateerde tekstvakken.

    b. Selecteer een **IDE-taal**, **taal**en **profiel**.

    c. Selecteer **E-mail voor het maken van een account naar mij verzenden**.

    d. Klik op **Opslaan**.

    > [!NOTE]
    > U alle andere runMyProcess-hulpprogramma's voor het maken van gebruikersaccounts of API's van RunMyProcess gebruiken om Azure Active Directory-gebruikersaccounts in te richten.

### <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel RunMyProcess in het toegangspaneel klikt, moet u automatisch worden aangemeld bij het RunMyProcess waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)