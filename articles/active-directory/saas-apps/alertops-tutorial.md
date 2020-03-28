---
title: 'Zelfstudie: Azure Active Directory-integratie met AlertOps | Microsoft Documenten'
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67107062"
---
# <a name="tutorial-integrate-alertops-with-azure-active-directory"></a>Zelfstudie: AlertOps integreren met Azure Active Directory

In deze zelfstudie leert u hoe u AlertOps integreert met Azure Active Directory (Azure AD). Wanneer u AlertOps integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot AlertOps.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij AlertOps met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* AlertOps single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving. AlertOps ondersteunt **SP en IDP** geïnitieerde SSO.

## <a name="adding-alertops-from-the-gallery"></a>AlertOps toevoegen vanuit de galerie

Om de integratie van AlertOps in Azure AD te configureren, moet u AlertOps vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **AlertOps** in het zoekvak in de sectie Toevoegen in de sectie Toevoegen **in de galerie.**
1. Selecteer **AlertOps** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met AlertOps met behulp van een testgebruiker genaamd **Britta Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in AlertOps.

Als u Azure AD SSO wilt configureren en testen met AlertOps, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer AlertOps](#configure-alertops)** om de SSO-instellingen aan toepassingszijde te configureren.
3. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om Azure AD-enkele aanmelding met Britta Simon te testen.
4. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om Britta Simon in staat te stellen azure AD-aanmelding te gebruiken.
5. **[Maak alertops testgebruiker](#create-alertops-test-user)** om een tegenhanger van Britta Simon in AlertOps die is gekoppeld aan de Azure AD vertegenwoordiging van de gebruiker.
6. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **AlertOps-toepassingsintegratie** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de volgende stappen uit als u de toepassing in de **idp-modus** wilt configureren:

    1. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://<SUBDOMAIN>.alertops.com`

    1. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<SUBDOMAIN>.alertops.com/login.aspx`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<SUBDOMAIN>.alertops.com/login.aspx`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke-id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [klantondersteuningsteam van AlertOps](mailto:support@alertops.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **AlertOps instellen** de juiste URL(s) op basis van uw vereiste.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-alertops"></a>AlertOps configureren

1. Als u de configuratie binnen AlertOps wilt automatiseren, moet u **de beveiligingsextensie Mijn apps secure aanmelden** installeren door op De extensie **installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **Setup AlertOps** naar de AlertOps-toepassing. Geef van daaruit de beheerdersreferenties op om u aan te melden bij AlertOps. De browserextensie configureert automatisch de toepassing voor u en automatiseert stappen 3-5.

    ![Configuratie instellen](common/setup-sso.png)

3. Als u AlertOps handmatig wilt instellen, opent u een nieuw webbrowservenster en meldt u zich als beheerder aan bij uw AlertOps-bedrijfssite en voert u de volgende stappen uit:

4. Klik in het linkernavigatievenster op **Account Settings**.

    ![AlertOps-configuratie](./media/alertops-tutorial/configure1.png)

5. Selecteer **SSO** op de pagina **Subscription Settings** en voer de volgende stappen uit:

    ![AlertOps-configuratie](./media/alertops-tutorial/configure2.png)

    a. Schakel het selectievakje **Use Single Sign-On(SSO)** in.

    b. Selecteer **Azure Active Directory** als **SSO-provider** in de vervolgkeuzelijst.

    c. Gebruik in het tekstvak **Van de URL van de uitgever** de id-waarde die u hebt gebruikt in de sectie **BasisSAML-configuratie** in de Azure-portal.

    d. Plak in het tekstvak **SAML-eindpunt-URL** de **URL-waarde aanmelding,** die u hebt gekopieerd uit de Azure-portal.

    e. Plak in het tekstvak **URL-url van SLO** de **URL-waarde aanmelding,** die u hebt gekopieerd uit de Azure-portal.

    f. Selecteer **SHA256** in de vervolgkeuzelijst **SAML Signature Algorithm**.

    g. Open het gedownloade bestand Certificate(Base64) in Kladblok. Kopieer de inhoud van het bestand naar het klembord en plak deze vervolgens in het tekstvak X.509 Certificate.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd Britta Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`Britta Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u Britta Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot AlertOps.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **AlertOps** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **In** het dialoogvenster Gebruikers en groepen De optie **Britta Simon** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

### <a name="create-alertops-test-user"></a>Een testgebruiker voor AlertOps maken

1. Meld u in een ander browservenster aan bij uw site van het bedrijf AlertOps als beheerder.

2. Klik op **Users** in het linkernavigatievenster.

    ![AlertOps-configuratie](./media/alertops-tutorial/user1.png)

3. Selecteer **Gebruiker toevoegen**.

    ![AlertOps-configuratie](./media/alertops-tutorial/user2.png)

4. Voer in het dialoogvenster **Add User** de volgende stappen uit:

    ![AlertOps-configuratie](./media/alertops-tutorial/user3.png)

    a. Voer in het tekstvak **Login User Name** de gebruikersnaam van de gebruiker in, bijvoorbeeld **Brittasimon**.

    b. Voer in het **tekstvak Officiële e-mail** het e-mailadres van de gebruiker in, zoals **\@Brittasimon contoso.com**.

    c. Voer in het tekstvak **First Name** de voornaam van de gebruiker in, zoals **Britta**.

    d. Voer in het tekstvak **Last Name** de achternaam van de gebruiker in, zoals **Simon**.

    e. Selecteer in de vervolgkeuzelijst **Type** een waarde die geschikt is voor uw organisatie.

    f. Selecteer in de vervolgkeuzelijst **Role** een waarde voor de rol van de gebruiker in uw organisatie.

    g. Selecteer **Toevoegen**.

### <a name="test-sso"></a>Test SSO

Wanneer u de tegel AlertOps selecteert in het toegangspaneel, moet u automatisch worden aangemeld bij de AlertOps waarvoor u SSO instelt. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)