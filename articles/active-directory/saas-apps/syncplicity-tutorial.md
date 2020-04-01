---
title: 'Zelfstudie: Azure Active Directory-integratie met syncplicity | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Syncplicity.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 896a3211-f368-46d7-95b8-e4768c23be08
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.openlocfilehash: 663958ae367162eaeb336c819d1d219dc74a2cbe
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "74233290"
---
# <a name="tutorial-integrate-syncplicity-with-azure-active-directory"></a>Zelfstudie: Synchronisatie integreren met Azure Active Directory

In deze zelfstudie leert u hoe u Syncplicity integreert met Azure Active Directory (Azure AD). Wanneer u Syncplicity integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Syncplicity.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Syncplicity met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Als je geen abonnement hebt, kun je [hier](https://azure.microsoft.com/pricing/free-trial/)een gratis proefperiode van een maand krijgen.
* Syncplicity single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving. Syncplicity ondersteunt **SP** geïnitieerde SSO.

## <a name="adding-syncplicity-from-the-gallery"></a>Syncplicity toevoegen vanuit de galerie

Als u de integratie van Syncplicity in Azure AD wilt configureren, moet u Syncplicity vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Synchronisatie** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **Syncplicity in** het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD SSO configureren en testen

Azure AD SSO configureren en testen met Syncplicity met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Syncplicity.

Als u Azure AD SSO wilt configureren en testen met Syncplicity, voert u de volgende bouwstenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Syncplicity SSO](#configure-syncplicity-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
5. **[Synchronisatietestgebruiker maken](#create-syncplicity-test-user)** - om een tegenhanger van B.Simon te hebben in Syncplicity die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina Synchronisatie van toepassingen op De **synchronisatievan** **de toepassing Sync** , de sectie Beheren en selecteer **Enkele aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **BasisSAML-configuratie** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.syncplicity.com`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://<companyname>.syncplicity.com/sp`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met [het ondersteuningsteam van Syncplicity Client](https://www.syncplicity.com/contact-us) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Syncplicity instellen** de juiste URL(s) op basis van uw vereiste.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-syncplicity-sso"></a>Syncplicity SSO configureren

1. Meld u aan bij uw **Syncplicity-tenant.**

1. Klik in het menu bovenaan op **beheerder,** selecteer **instellingen**en klik vervolgens op **Aangepast domein en eenmalige aanmelding**.

    ![Syncplicity](./media/syncplicity-tutorial/ic769545.png "Syncplicity")

1. Voer op de pagina **SSO (Single Sign-On)** de volgende stappen uit:

    ![Enkele aanmelding \(SSO\)](./media/syncplicity-tutorial/ic769550.png "Single Sign-On \\\(SSO\\\)")

    a. Typ in het tekstvak **Aangepast domein** de naam van uw domein.
  
    b. Selecteer **Ingeschakeld** als **enkele aanmeldingsstatus**.

    c. Plak in het tekstvak **Entiteit-id** de waarde van de **id(Entiteit-id)** die u hebt gebruikt in de **basisSAML-configuratie** in de Azure-portal.

    d. Plak in het **tekstvak voor aanmeldingspagina-URL** de **aanmeldings-URL** die u hebt gekopieerd vanuit azure-portal.

    e. Plak in het tekstvak van de **uitlogpagina URL** de **url van de afmelding** plakken die u hebt gekopieerd vanuit de Azure-portal.

    f. Klik in **het certificaat van identiteitsprovider**op **Bestand kiezen**en upload vervolgens het certificaat dat u hebt gedownload van de Azure-portal.

    g. Klik op **WIJZIGINGEN OPSLAAN**.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Syncplicity.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer In de lijst met toepassingen de optie **Syncplicity**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

### <a name="create-syncplicity-test-user"></a>Gebruiker van syncplicity-test maken

Als Azure AD-gebruikers zich kunnen aanmelden, moeten ze zijn ingericht voor de toepassing Syncplicity. In deze sectie wordt beschreven hoe u Azure AD-gebruikersaccounts maakt in Syncplicity.

**Voer de volgende stappen uit om een gebruikersaccount in te richten op Syncplicity:**

1. Meld u aan bij uw **Syncplicity-tenant** (bijvoorbeeld: `https://company.Syncplicity.com`).

1. Klik **op beheerder** en selecteer **gebruikersaccounts** en klik vervolgens op **EEN GEBRUIKER TOEVOEGEN**.

    ![Gebruikers beheren](./media/syncplicity-tutorial/ic769764.png "Gebruikers beheren")

1. Typ de **e-mailadressen** van een Azure AD-account dat u wilt inrichten, selecteer **Gebruiker** als **rol**en klik op **VOLGENDE**.

    ![Accountgegevens](./media/syncplicity-tutorial/ic769765.png "Accountgegevens")

    > [!NOTE]
    > De azure AD-accounthouder krijgt een e-mail met een koppeling om het account te bevestigen en te activeren.

1. Selecteer een groep in uw bedrijf waarvan uw nieuwe gebruiker lid moet worden en klik op **VOLGENDE**.

    ![Groepslidmaatschap](./media/syncplicity-tutorial/ic769772.png "Groepslidmaatschap")

    > [!NOTE]
    > Als er geen groepen worden weergegeven, klikt u op **VOLGENDE**.

1. Selecteer de mappen die u wilt plaatsen onder de besturingselement van Syncplicity op de computer van de gebruiker en klik op **VOLGENDE**.

    ![Synchronisatiemappen](./media/syncplicity-tutorial/ic769773.png "Synchronisatiemappen")

> [!NOTE]
> U alle andere hulpprogramma's voor het maken van gebruikersaccounts voor gebruikersvan Syncplicity gebruiken die door Syncplicity worden geleverd om Azure AD-gebruikersaccounts in te richten.

### <a name="test-sso"></a>Test SSO

Wanneer u de tegel Syncplicity selecteert in het toegangspaneel, moet u automatisch worden aangemeld bij de Syncplicity waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)