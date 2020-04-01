---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met AcquireIO | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en AcquireIO.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 97815e13-32ec-4470-b075-1253f5814f4f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01bcc3678485119afae1d567d97eff9dcebe6b95
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76714618"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-acquireio"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met AcquireIO

In deze zelfstudie leert u hoe u AcquireIO integreren met Azure Active Directory (Azure AD). Wanneer u AcquireIO integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot AcquireIO.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij AcquireIO met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* AcquireIO single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* AcquireIO ondersteunt **IDP** geïnitieerde SSO

## <a name="adding-acquireio-from-the-gallery"></a>AcquireIO toevoegen vanuit de galerie

Als u de integratie van AcquireIO in Azure AD wilt configureren, moet u AcquireIO uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **AcquireIO** in het zoekvak in de sectie **Toevoegen vanuit de galerie.**
1. Selecteer **AcquireIO** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-acquireio"></a>Azure AD-aanmelding voor AcquireIO configureren en testen

Azure AD SSO configureren en testen met AcquireIO met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in AcquireIO.

Als u Azure AD SSO wilt configureren en testen met AcquireIO, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer AcquireIO SSO](#configure-acquireio-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[AcquireIO-testgebruiker maken](#create-acquireio-test-user)** - om een tegenhanger van B.Simon in AcquireIO te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **AcquireIO-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    Typ in het tekstvak **URL beantwoorden** een URL met het volgende patroon:`https://app.acquire.io/ad/<acquire_account_uid>`

    > [!NOTE]
    > De waarde is niet echt. U krijgt de werkelijke URL van het antwoord die later wordt uitgelegd in de sectie **AcquireIO configureren** van de zelfstudie. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **AcquireIO instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot AcquireIO.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **AcquireIO**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-acquireio-sso"></a>AcquireIO SSO configureren

1. Als u de configuratie binnen AcquireIO wilt automatiseren, moet u **de beveiligingsextensie Mijn apps secure aanmelden** installeren door op De extensie **installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

1. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **AcquireIO instellen**, wat u doorverwijst naar de AcquireIO-toepassing. Geef van daaruit de beheerdersreferenties op om u aan te melden bij AcquireIO. De browserextensie configureert automatisch de toepassing voor u en automatiseert stappen 3-6.

    ![Configuratie instellen](common/setup-sso.png)

1. Als u AcquireIO handmatig wilt instellen, u zich in een ander browservenster aanmelden bij AcquireIO als beheerder.

1. Klik links in het menu op **app store**.

     ![AcquireIO-configuratie](./media/acquireio-tutorial/config01.png)

1. Schuif omlaag naar **Active Directory** en klik op **Installeren**.

    ![AcquireIO-configuratie](./media/acquireio-tutorial/config02.png)

1. Voer in de pop-up van Active Directory de volgende stappen uit:

    ![AcquireIO-configuratie](./media/acquireio-tutorial/config03.png)

    a. Klik **op Kopiëren** om de url van het antwoord voor uw instantie te kopiëren en deze te plakken in het tekstvak Van De URL van **Antwoord** in de sectie **BasisSAML-configuratie** op azure-portal.

    b. Plak in het tekstvak **Aanmeldings-URL** de waarde van **de aanmeldings-URL**, die u hebt gekopieerd vanuit azure-portal.

    c. Open het certificaat Base64 gecodeerd in Kladblok, kopieer de inhoud ervan en plak het in het tekstvak **X.509-certificaat.**

    d. Klik **op Nu verbinden**.

### <a name="create-acquireio-test-user"></a>AcquireIO-testgebruiker maken

Om Azure AD-gebruikers in staat te stellen zich aan te melden bij AcquireIO, moeten ze worden ingericht in AcquireIO. In AcquireIO is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u in een ander browservenster aan bij AcquireIO als beheerder.

1. Klik links van het menu op **Profielen** en navigeer naar **Profiel toevoegen**.

     ![AcquireIO-configuratie](./media/acquireio-tutorial/config04.png)

1. Voer in de pop-up **Klant toevoegen** de volgende stappen uit:

    ![AcquireIO-configuratie](./media/acquireio-tutorial/config05.png)

    a. Voer in het tekstvak **Naam** de naam van de gebruiker in, zoals **B.simon.**

    b. Voer in het tekstvak **Email** het e-mailadres van de gebruiker in, zoals **B.simon@contoso.com**.

    c. Klik **op Verzenden**.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel AcquireIO in het access panel klikt, moet u automatisch worden aangemeld bij de AcquireIO waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer AcquireIO met Azure AD](https://aad.portal.azure.com/)
