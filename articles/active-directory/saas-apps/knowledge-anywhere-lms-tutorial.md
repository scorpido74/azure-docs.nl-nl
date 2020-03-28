---
title: 'Zelfstudie: Azure Active Directory-integratie met Knowledge Anywhere LMS | Microsoft Documenten'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Knowledge Anywhere LMS.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5cfa07b1-a792-4f0a-8c6f-1a13142193d9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f44324bbdd5af6675dfb4f5664cbbde2627edfec
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67098564"
---
# <a name="tutorial-integrate-knowledge-anywhere-lms-with-azure-active-directory"></a>Zelfstudie: Knowledge Anywhere LMS integreren met Azure Active Directory

In deze zelfstudie leert u hoe Knowledge Anywhere LMS worden geïntegreerd met Azure Active Directory (Azure AD). Wanneer u Knowledge Anywhere LMS integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Knowledge Anywhere LMS.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Knowledge Anywhere LMS met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Knowledge Anywhere LMS single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving. Knowledge Anywhere LMS ondersteunt **SP** geïnitieerde SSO en ondersteunt **Just In Time** gebruikersinrichting.

## <a name="adding-knowledge-anywhere-lms-from-the-gallery"></a>Knowledge Anywhere LMS toevoegen vanuit de galerie

Voor het configureren van de integratie van Knowledge Anywhere LMS met Azure AD moet u Knowledge Anywhere LMS uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Knowledge Anywhere LMS** in de sectie **Toevoegen in de galeriesectie** in het zoekvak.
1. Selecteer **Knowledge Anywhere LMS** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

Azure AD SSO configureren en testen met Knowledge Anywhere LMS met behulp van een testgebruiker genaamd **B. Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Knowledge Anywhere LMS.

Als u Azure AD SSO wilt configureren en testen met Knowledge Anywhere LMS, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** zodat uw gebruikers deze functie kunnen gebruiken.
2. **[Configureer Knowledge Anywhere LMS](#configure-knowledge-anywhere-lms)** om de SSO-instellingen aan toepassingszijde te configureren.
3. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om Azure AD-eenmaligaanmelding met B. Simon te testen.
4. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om B. Simon in staat te stellen azure AD-aanmelding te gebruiken.
5. **[Maak Knowledge Anywhere LMS-testgebruiker](#create-knowledge-anywhere-lms-test-user)** om een tegenhanger van B. Simon in Knowledge Anywhere LMS te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Test SSO](#test-sso)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Knowledge Anywhere LMS-toepassingsintegratie** de sectie **Beheren** en selecteer **Enkele aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de volgende stappen uit als u de toepassing in de **idp-modus** wilt configureren:

    1. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://<CLIENTNAME>.knowledgeanywhere.com/`

    1. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<CLIENTNAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDPNAME>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL, zoals later in de zelfstudie wordt uitgelegd.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > De waarde voor de aanmeldings-URL is niet echt. Werk deze waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteuningsteam van Knowledge Anywhere LMS](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide) om deze waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Knowledge up Anywhere LMS** de juiste URL(s) op basis van uw vereiste.

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-knowledge-anywhere-lms"></a>Kennis overal configureren LMS

1. Als u de configuratie binnen Knowledge Anywhere LMS wilt automatiseren, moet u **de beveiligingsextensie Mijn apps secure aanmelden** installeren door op De extensie **installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

2. Na het toevoegen van extensie aan de browser, klik op **Setup Knowledge Anywhere LMS** zal u doorverwijzen naar de Knowledge Anywhere LMS applicatie. Geef van daaruit de beheerdersreferenties op om u aan te melden bij Knowledge Anywhere LMS. In de browserextensie wordt de toepassing automatisch voor u geconfigureerd en worden stappen 3-7 geautomatiseerd.

    ![Configuratie instellen](common/setup-sso.png)

3. Als u Knowledge Anywhere LMS handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich als beheerder aan bij uw Knowledge Anywhere LMS-bedrijfssite en voert u de volgende stappen uit:

4. Selecteer het tabblad **Site**.

    ![Configuratie van Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure1.png)

5. Selecteer het tabblad **SAML Settings** (SAML-instellingen).

    ![Configuratie van Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure2.png)

6. Klik op **Add New** (Nieuwe toevoegen).

    ![Configuratie van Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure3.png)

7. Voer de volgende stappen uit op de pagina **Add/Update SAML Settings** (SAML-instellingen toevoegen/bijwerken):

    ![Configuratie van Knowledge Anywhere LMS](./media/knowledge-anywhere-lms-tutorial/configure4.png)

    a. Voer de naam van de id-provider voor uw organisatie in. Bijvoorbeeld: `Azure`.

    b. Plak in het tekstvak IDP-entiteit-id de waarde **van azure AD-id** (die u hebt gekopieerd vanuit azure-portal) in het tekstvak Van De **IDP-entiteit-id.**

    c. Plak in het tekstvak van de **IDP-URL** **de url-waarde van aanmelding,** die u hebt gekopieerd vanuit azure-portal.

    d. Open het certificaatbestand dat u vanuit de Azure-portal hebt gedownload in Kladblok, kopieer de inhoud van het certificaat en plak deze in het tekstvak **Certificate** (Certificaat).

    e. Plak in het tekstvak **Van de URL van de uitloging** de **URL-waarde** plakken die u hebt gekopieerd vanuit de Azure-portal.

    f. Selecteer **Main Site** in de vervolgkeuzelijst voor **Domain**.

    g. Kopieer de waarde van **SP Entity ID** en plak deze in het tekstvak **Id** in de sectie **Standaard SAML-configuratie** in de Azure-portal.

    h. Kopieer de waarde van **SP Response (ACS) URL** en plak deze in het tekstvak **Antwoord-URL** in de sectie **Standaard SAML-configuratie** in de Azure-portal.

    i. Klik op **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B. Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B. Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `BrittaSimon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B. Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Knowledge Anywhere LMS.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Knowledge Anywhere LMS** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **In** het dialoogvenster Gebruikers en groepen **B. Simon** in de lijst Gebruikers en klik op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

### <a name="create-knowledge-anywhere-lms-test-user"></a>Knowledge Anywhere LMS-testgebruiker maken

In deze sectie wordt een gebruiker genaamd B. Simon gemaakt in Knowledge Anywhere LMS. Knowledge Anywhere biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker in Knowledge Anywhere LMS bestaat, wordt er een nieuwe gemaakt na de verificatie.

### <a name="test-sso"></a>Test SSO

Wanneer u de Knowledge Anywhere LMS-tegel selecteert in het toegangspaneel, moet u automatisch worden aangemeld bij de Knowledge Anywhere LMS waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)