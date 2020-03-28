---
title: 'Zelfstudie: Azure Active Directory-integratie met Replicon | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Replicon.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 02a62f15-917c-417c-8d80-fe685e3fd601
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b27615b0c76b5c23bbc79788431b0e909b8bf22a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67092773"
---
# <a name="tutorial-integrate-replicon-with-azure-active-directory"></a>Zelfstudie: Replicon integreren met Azure Active Directory

In deze zelfstudie leert u hoe U Replicon integreren met Azure Active Directory (Azure AD). Wanneer u Replicon integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Replicon.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Replicon met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Als je geen abonnement hebt, kun je [hier](https://azure.microsoft.com/pricing/free-trial/)een gratis proefperiode van een maand krijgen.
* Replicon single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving. Replicon ondersteunt **SP** geïnitieerde SSO.

## <a name="adding-replicon-from-the-gallery"></a>Replicon toevoegen vanuit de galerie

Als u de integratie van Replicon in Azure AD wilt configureren, moet u Replicon uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Replicon** in het zoekvak in de sectie Toevoegen in de sectie Toevoegen **in de galerie.**
1. Selecteer **Replicon** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met Replicon met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Replicon.

Als u Azure AD SSO wilt configureren en testen met Replicon, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Replicon SSO configureren](#configure-replicon-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
4. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
5. **[Replicon-testgebruiker maken](#create-replicon-test-user)** - om een tegenhanger van B.Simon in Replicon te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Replicon-toepassingsintegratie** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **BasisSAML-configuratie** de waarden in voor de volgende velden:

    1. Typ in het tekstvak **AANmeldings-URL** een URL met het volgende patroon:`https://global.replicon.com/!/saml2/<client name>/sp-sso/post`

    1. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://global.replicon.com/!/saml2/<client name>`

    1. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://global.replicon.com/!/saml2/<client name>/sso/post`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Neem contact op met [replicon client support team](https://www.replicon.com/customerzone/contact-support) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Klik op het pictogram bewerken/pen voor **SAML-ondertekeningscertificaat** om de instellingen te bewerken.

    ![Ondertekeningsalgoritme](common/signing-algorithm.png)

    1. Selecteer **DE claim SAML ondertekenen** als de **ondertekeningsoptie**.

    1. Selecteer **SHA-256** als **ondertekeningsalgoritme**.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Federation Metadata XML** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![De link om het certificaat te downloaden](common/metadataxml.png)

### <a name="configure-replicon-sso"></a>Replicon SSO configureren

1. Meld u in een ander browservenster aan op uw site van het Replicon-bedrijf als beheerder.

2. Voer de volgende stappen uit om SAML 2.0 te configureren:

    ![SAML-verificatie inschakelen](./media/replicon-tutorial/ic777805.png "SAML-verificatie inschakelen")

    a. Als u het dialoogvenster **EnableSAML-verificatie2** wilt weergeven, sluit u het volgende toe aan uw URL, na uw bedrijfssleutel:`/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

    * Het volgende toont het schema van de volledige URL:`https://na2.replicon.com/\<YourCompanyKey\>/services/SecurityService1.svc/help/test/EnableSAMLAuthentication2`

   b. Klik **+** op de sectie om de **sectie v20Configuratie** uit te vouwen.

   c. Klik **+** op de sectie Om de **sectie metaDataConfiguration** uit te vouwen.

   d. **SHA256** selecteren voor xmlSignatureAlgorithm

   e. Klik **op Bestand kiezen**om het XML-bestand met metagegevens van uw identiteitsprovider te selecteren en klik op **Verzenden**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Replicon.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Replicon**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

### <a name="create-replicon-test-user"></a>Gebruiker van Replicon-test maken

Het doel van deze sectie is het creëren van een gebruiker genaamd B.Simon in Replicon.

**Als u de gebruiker handmatig moet maken, voert u de volgende stappen uit:**

1. Meld u in een webbrowservenster aan op uw site van het Replicon-bedrijf als beheerder.

2. Ga naar **Gebruikers van beheer \> **.

    ![Gebruikers](./media/replicon-tutorial/ic777806.png "Gebruikers")

3. Klik **op +Gebruiker toevoegen**.

    ![Gebruiker toevoegen](./media/replicon-tutorial/ic777807.png "Gebruiker toevoegen")

4. Voer in de sectie **Gebruikersprofiel** de volgende stappen uit:

    ![Gebruikersprofiel](./media/replicon-tutorial/ic777808.png "Gebruikersprofiel")

    a. Typ in het tekstvak **Vooraanmeldingsnaam** het Azure AD-e-mailadres van de Azure AD-gebruiker die u wilt inrichten, zoals `B.Simon@contoso.com`.

    > [!NOTE]
    > Aanmeldingsnaam moet overeenkomen met het e-mailadres van de gebruiker in Azure AD

    b. Selecteer **SSO**als **verificatietype**.

    c. Verificatie-id instellen op dezelfde waarde als aanmeldingsnaam (het E-mailadres van de gebruiker in Azure AD)

    d. Typ in het tekstvak **Van de afdeling** De afdeling van de gebruiker.

    e. Selecteer **Administrator** **als werknemertype**.

    f. Klik **op Gebruikersprofiel opslaan**.

> [!NOTE]
> U alle andere tools voor het maken van gebruikersaccounts voor Replicon of API's die door Replicon worden verstrekt, gebruiken om Azure AD-gebruikersaccounts in te richten.

### <a name="test-sso"></a>Test SSO

Wanneer u de replicon-tegel selecteert in het toegangspaneel, moet u automatisch worden aangemeld bij de Replicon waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)