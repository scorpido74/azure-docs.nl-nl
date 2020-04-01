---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met ThousandEyes | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en ThousandEyes.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 790e3f1e-1591-4dd6-87df-590b7bf8b4ba
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd179049f88454c62244cf1819cee08ef78d0633
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72373240"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-thousandeyes"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met ThousandEyes

In deze zelfstudie leert u hoe u ThousandEyes integreren met Azure Active Directory (Azure AD). Wanneer u ThousandEyes integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot ThousandEyes.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij ThousandEyes met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* ThousandEyes single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* ThousandEyes ondersteunt **SP en IDP** geïnitieerd sso
* ThousandEyes ondersteunt [ **geautomatiseerde** gebruikersinrichting](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)

> [!NOTE]
> Id van deze toepassing is een vaste tekenreekswaarde, zodat slechts één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-thousandeyes-from-the-gallery"></a>ThousandEyes toevoegen vanuit de galerij

Als u de integratie van ThousandEyes in Azure AD wilt configureren, moet u ThousandEyes vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **ThousandEyes** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **ThousandEyes** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-thousandeyes"></a>Azure AD-aanmelding voor ThousandEyes configureren en testen

Azure AD SSO configureren en testen met ThousandEyes met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in ThousandEyes.

Als u Azure AD SSO met ThousandEyes wilt configureren en testen, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer ThousandEyes SSO](#configure-thousandeyes-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Maak ThousandEyes-testgebruiker](#create-thousandeyes-test-user)** - om een tegenhanger van B.Simon in ThousandEyes te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **ThousandEyes-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **BasisSAML-configuratie** is de toepassing vooraf geconfigureerd en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ een URL in het tekstvak **AANmeldings-URL:**`https://app.thousandeyes.com/login/sso`

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **ThousandEyes instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot ThousandEyes.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **ThousandEyes**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-thousandeyes-sso"></a>ThousandEyes SSO configureren

1. Meld u in een ander browservenster aan op uw **ThousandEyes-bedrijfssite** als beheerder.

2. Klik in het menu aan de bovenkant op **Settings**.

    ![Instellingen](./media/thousandeyes-tutorial/ic790066.png "Instellingen")

3. Klik **op Account**

    ![Account](./media/thousandeyes-tutorial/ic790067.png "Account")

4. Klik op het tabblad **Beveiliging & verificatie.**

    ![Beveiliging & verificatie](./media/thousandeyes-tutorial/ic790068.png "Beveiliging & verificatie")

5. Voer in de sectie **Eén aanmelding instellen** de volgende stappen uit:

    ![Eenmalige aanmelding instellen](./media/thousandeyes-tutorial/ic790069.png "Eenmalige aanmelding instellen")

    a. Selecteer **Eén aanmelding inschakelen**.

    b. Plak in het tekstvak **URL van aanmeldingspagina** **de URL van aanmelding**, die u hebt gekopieerd vanuit azure-portal.

    c. Plak in het **tekstvak van de aanmeldingspagina** **de URL van afmelden**, die u hebt gekopieerd vanuit azure-portal.

    d. **TextBox van de uitgever van identiteitsinstellingen,** plak Azure **AD-id**, die u hebt gekopieerd van Azure-portal.

    e. Klik **in Verificatiecertificaat**op **Bestand kiezen**en upload vervolgens het certificaat dat u hebt gedownload van de Azure-portal.

    f. Klik op **Opslaan**.

### <a name="create-thousandeyes-test-user"></a>ThousandEyes-testgebruiker maken

Het doel van deze sectie is het creëren van een gebruiker genaamd Britta Simon in ThousandEyes. ThousandEyes ondersteunt automatische gebruikersinrichting, die standaard is ingeschakeld. U kunt [hier](thousandeyes-provisioning-tutorial.md) meer informatie vinden over het configureren van het automatisch inrichten van gebruikers.

**Als u de gebruiker handmatig moet maken, voert u de volgende stappen uit:**

1. Meld u aan bij uw ThousandEyes-bedrijfssite als beheerder.

2. Klik op **Instellingen**.

    ![Instellingen](./media/thousandeyes-tutorial/IC790066.png "Instellingen")

3. Klik op **Account**.

    ![Account](./media/thousandeyes-tutorial/IC790067.png "Account")

4. Klik op het tabblad **Accounts & gebruikers.**

    ![Accounts & gebruikers](./media/thousandeyes-tutorial/IC790073.png "Accounts & gebruikers")

5. Voer in de sectie **Gebruikers toevoegen & accounts** de volgende stappen uit:

    ![Gebruikersaccounts toevoegen](./media/thousandeyes-tutorial/IC790074.png "Gebruikersaccounts toevoegen")

    a. Typ in **tekstvak Naam** de naam van de gebruiker zoals **B.Simon**.

    b. Typ in **het tekstvak E-mail** de e-mail van de gebruiker als b.simon@contoso.com.

    b. Klik **op Nieuwe gebruiker toevoegen aan account**.

    > [!NOTE]
    > De houder van het Azure Active Directory-account ontvangt een e-mail met een koppeling om het account te bevestigen en te activeren.

> [!NOTE]
> U alle andere ThousandEyes-hulpprogramma's voor het maken van gebruikersaccounts of API's die door ThousandEyes worden verstrekt, gebruiken om Azure Active Directory-gebruikersaccounts in te richten.


## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de thousandeyes-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de ThousandEyes waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer ThousandEyes met Azure AD](https://aad.portal.azure.com/)

- [Gebruikersinrichting configureren](https://docs.microsoft.com/azure/active-directory/saas-apps/thousandeyes-provisioning-tutorial)