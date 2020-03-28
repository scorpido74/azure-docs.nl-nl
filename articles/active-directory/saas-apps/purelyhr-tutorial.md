---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met PurelyHR | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en PurelyHR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 86a9c454-596d-4902-829a-fe126708f739
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 56049e1b1253cd749a8e16061957c6b5b8786e3c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72594538"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-purelyhr"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met PurelyHR

In deze zelfstudie leert u hoe u PurelyHR integreert met Azure Active Directory (Azure AD). Wanneer u PurelyHR integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot PurelyHR.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij PurelyHR met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Puur EENMALIGE aanmelding (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Purehr ondersteunt **SP en IDP** geïnitieerd sso
* Purehr ondersteunt **Just In Time** gebruikersinrichting

## <a name="adding-purelyhr-from-the-gallery"></a>Het toevoegen van PurelyHR uit de galerie

Als u de integratie van PurelyHR in Azure AD wilt configureren, moet u PurelyHR vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Puur HR** in het zoekvak in de sectie Toevoegen in **de galeriesectie.**
1. Selecteer **Puur HR** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-purelyhr"></a>Azure AD-aanmelding configureren en testen voor PuurHR

Azure AD SSO configureren en testen met PurelyHR met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in PurelyHR.

Als u Azure AD SSO wilt configureren en testen met PurelyHR, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer PurelyHR SSO](#configure-purelyhr-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Maak de gebruiker van de PurelyHR-test](#create-purelyhr-test-user)** - om een tegenhanger van B.Simon te hebben in PurelyHR die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **PurelyHR-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    Typ in het tekstvak **URL beantwoorden** een URL met het volgende patroon:`https://<companyID>.purelyhr.com/sso-consume`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<companyID>.purelyhr.com/sso-initiate`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de echte antwoord-URL en aanmeldings-URL. Neem contact op met [het ondersteuningsteam van PurelyHR Client](https://support.purelyhr.com/) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **PurelyHR instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot PurelyHR.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Puur HR**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-purelyhr-sso"></a>Puur HR-SSO configureren

1. Als u de configuratie binnen PurelyHR wilt automatiseren, moet u **de beveiligingsextensie Mijn apps secure aanmelden** installeren door op De extensie **installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

1. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **Puurinstellen als u** doorgaat naar de PurelyHR-toepassing. Geef van daaruit de beheerdersreferenties op om u aan te melden bij PurelyHR. De browserextensie configureert automatisch de toepassing voor u en automatiseert stappen 3-5.

    ![Configuratie instellen](common/setup-sso.png)

1. Als u PurelyHR handmatig wilt instellen, opent u een nieuw browservenster en meldt u zich aan bij uw Site van het PurelyHR-bedrijf als beheerder en voert u de volgende stappen uit:

1. Open het **dashboard** vanuit de opties op de werkbalk en klik op **SSO-instellingen**.

1. Plak de waarden in de onderstaande vakken.

    ![Eenmalige aanmelding configureren](./media/purelyhr-tutorial/purelyhr-dashboard-sso-settings.png)  

    a. Open het **certificaat(Bas64)** dat is gedownload van de Azure-portal in kladblok en kopieer de certificaatwaarde. Plak de gekopieerde waarde in het vak **X.509-certificaat.**

    b. Plak in het **vak URL van Idp Issuer** de Azure **AD-id** die is gekopieerd van de Azure-portal.

    c. Plak in het vak **URL van Idp Endpoint** de **inlog-URL** die is gekopieerd van de Azure-portal. 

    d. Schakel het selectievakje **Gebruikers automatisch maken** in om automatische gebruikersinrichting in PuurHR in te schakelen.

    e. Klik **op Wijzigingen opslaan** om de instellingen op te slaan.

### <a name="create-purelyhr-test-user"></a>Puur HR-testgebruiker maken

Deze stap is meestal niet vereist omdat de toepassing net op tijd gebruikersinrichting ondersteunt. Als de automatische gebruikersinrichting niet is ingeschakeld, kan handmatige gebruikersmaken worden gedaan zoals hieronder beschreven.

Meld u aan op uw Velpic SAML-bedrijfssite als beheerder en voer de volgende stappen uit:

1. Klik op het tabblad Beheren en ga naar de sectie Gebruikers en klik vervolgens op Nieuwe knop om gebruikers toe te voegen.

    ![gebruiker toevoegen](./media/velpicsaml-tutorial/velpic_7.png)

2. Voer op de pagina **Nieuwe gebruiker maken** de volgende stappen uit.

    ![gebruiker](./media/velpicsaml-tutorial/velpic_8.png)

    a. Typ in het tekstvak **Voornaam** de voornaam van B.

    b. Typ in het tekstvak **Achternaam** de achternaam van Simon.

    c. Typ in het tekstvak **Gebruikersnaam** de gebruikersnaam van B.Simon.

    d. Typ in het tekstvak **E-mail** het e-mailadres van B.Simon@contoso.com het account.

    e. De rest van de informatie is optioneel, u deze indien nodig invullen.

    f. Klik **op OPSLAAN**.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Puur HR in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de PurelyHR waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer PurelyHR met Azure AD](https://aad.portal.azure.com/)
