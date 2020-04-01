---
title: 'Zelfstudie: SSO-integratie (Azure Active Directory single sign-on) met BlueJeans voor Azure AD | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en BlueJeans voor Azure AD.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfc38f63c5b6361122c236543320b91d22faa70a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72595053"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bluejeans-for-azure-ad"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met BlueJeans voor Azure AD

In deze zelfstudie leert u hoe u BlueJeans voor Azure AD integreert met Azure Active Directory (Azure AD). Wanneer u BlueJeans voor Azure AD integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot BlueJeans voor Azure AD.
* Stel uw gebruikers in om automatisch te worden aangemeld bij BlueJeans voor Azure AD met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* BlueJeans voor Azure AD single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* BlueJeans voor Azure AD ondersteunt **DOOR SP** geïnitieerde SSO

* BlueJeans voor Azure AD ondersteunt [ **geautomatiseerde** gebruikersinrichting](bluejeans-provisioning-tutorial.md)

> [!NOTE]
> Id van deze toepassing is een vaste tekenreekswaarde, zodat slechts één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-bluejeans-for-azure-ad-from-the-gallery"></a>BlueJeans toevoegen voor Azure AD vanuit de galerie

Als u de integratie van BlueJeans voor Azure AD in Azure AD wilt configureren, moet u BlueJeans voor Azure AD vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **BlueJeans voor Azure AD** in het zoekvak in de sectie Toevoegen in de **galeriesectie.**
1. Selecteer **BlueJeans voor Azure AD** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bluejeans-for-azure-ad"></a>Azure AD-aanmelding voor BlueJeans voor Azure AD configureren en testen

Azure AD SSO configureren en testen met BlueJeans voor Azure AD met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in BlueJeans voor Azure AD.

Als u Azure AD SSO wilt configureren en testen met BlueJeans voor Azure AD, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer BlueJeans voor Azure AD SSO](#configure-bluejeans-for-azure-ad-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Maak BlueJeans voor Azure AD-testgebruiker](#create-bluejeans-for-azure-ad-test-user)** - om een tegenhanger van B.Simon in BlueJeans voor Azure AD te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **BlueJeans for Azure AD-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<companyname>.bluejeans.com`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [BlueJeans voor azure AD Client Support Team](https://support.bluejeans.com/contact) om de waarde te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **BlueJeans instellen voor Azure AD** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot BlueJeans voor Azure AD.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **BlueJeans voor Azure AD**in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-bluejeans-for-azure-ad-sso"></a>BlueJeans configureren voor Azure AD SSO

1. Meld u in een ander browservenster aan bij uw **BlueJeans voor Azure AD-bedrijfssite** als beheerder.

2. Ga naar **ADMIN \> GROEPSINSTELLINGEN \> BEVEILIGING**.

    ![Beheerder](./media/bluejeans-tutorial/ic785868.png "Beheerder")

3. Voer in het gedeelte **BEVEILIGING** de volgende stappen uit:

    ![SAML-eenmalig aanmelden](./media/bluejeans-tutorial/ic785869.png "SAML-eenmalig aanmelden")

    a. Selecteer **SAML-eenmalige aanmelding**.

    b. Selecteer **Automatische inrichting inschakelen**.

4. Ga verder met de volgende stappen:

    ![Certificaatpad](./media/bluejeans-tutorial/ic785870.png "Certificaatpad")

    a. Klik op **Bestand kiezen** om het base-64 gecodeerde certificaat dat u hebt gedownload vanuit de Azure-portal te uploaden.

    b. Plak in het tekstvak **Aanmeldings-URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    c. Plak in het tekstvak **Wachtwoord-URL wijzigen** de waarde van **Wachtwoord-URL wijzigen** die u hebt gekopieerd uit de Azure-portal.

    d. Plak in het tekstvak **Afmeldings-URL** de waarde van **Afmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

5. Ga verder met de volgende stappen:

    ![Wijzigingen opslaan](./media/bluejeans-tutorial/ic785874.png "Wijzigingen opslaan")

    a. Typ **User Id** in het tekstvak `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`Gebruikersnaam .

    b. Typ `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` in het tekstvak **E-mailadres**.

    c. Klik op **WIJZIGINGEN OPSLAAN**.

### <a name="create-bluejeans-for-azure-ad-test-user"></a>BlueJeans maken voor Azure AD-testgebruiker

Het doel van deze sectie is het maken van een gebruiker genaamd B.Simon in BlueJeans voor Azure AD. BlueJeans voor Azure AD ondersteunt automatische gebruikersinrichting, die standaard is ingeschakeld. U kunt [hier](bluejeans-provisioning-tutorial.md) meer informatie vinden over het configureren van het automatisch inrichten van gebruikers.

**Als u de gebruiker handmatig moet maken, voert u de volgende stappen uit:**

1. Meld u aan bij uw **BlueJeans voor Azure AD-bedrijfssite** als beheerder.

2. Ga naar **ADMIN \> GEBRUIKERS BEHEREN \> GEBRUIKER TOEVOEGEN**.

    ![Beheerder](./media/bluejeans-tutorial/ic785877.png "Beheerder")

    > [!IMPORTANT]
    > Het tabblad **GEBRUIKER TOEVOEGEN** is alleen beschikbaar als op het tabblad **BEVEILIGING****Automatische inrichting inschakelen** niet is ingeschakeld.

3. Voer in de sectie **GEBRUIKER TOEVOEGEN** de volgende stappen uit:

    ![Gebruiker toevoegen](./media/bluejeans-tutorial/ic785886.png "Gebruiker toevoegen")

    a. Voer in het tekstvak **Voornaam** de voornaam van de gebruiker in, zoals **B.**

    b. Voer in het tekstvak **Achternaam** de achternaam van de gebruiker in, zoals **Simon**.

    c. Voer **in Een BlueJeans voor Azure AD-gebruikersnaam** tekstvak de gebruikersnaam van de gebruiker in, zoals **Brittasimon**

    d. Voer in het tekstvak **Create a Password** uw wachtwoord in.

    e. Voer in het tekstvak **Company** de naam van uw bedrijf in.

    f. Voer in het tekstvak **Email Address** het e-mailadres van de gebruiker in, zoals `b.simon\@contoso.com`.

    g. Voer in **Het tekstvak Van Een BlueJeans voor Azure AD-vergadering invoeren** uw vergader-id in.

    h. Geef uw wachtwoordcode op in het tekstvak **Pick a Moderator Passcode**.

    i. Klik op **CONTINUE**.

    ![Gebruiker toevoegen](./media/bluejeans-tutorial/ic785887.png "Gebruiker toevoegen")

    J. Klik op **ADD USER**.

> [!NOTE]
> U alle andere BlueJeans voor Azure AD-hulpprogramma's voor het maken van gebruikersaccounts of API's van BlueJeans voor Azure AD gebruiken om Azure AD-gebruikersaccounts in te richten.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel BlueJeans voor Azure AD klikt in het toegangspaneel, moet u automatisch worden aangemeld bij de BlueJeans voor Azure AD waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [BlueJeans voor Azure AD uitproberen met Azure AD](https://aad.portal.azure.com/)

