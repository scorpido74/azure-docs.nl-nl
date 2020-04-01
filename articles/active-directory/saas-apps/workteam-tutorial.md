---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Workteam | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Workteam.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41df17a1-ba69-414f-8ec3-11079b030df6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b14c08604fcc0f6d2550127ca1f1aa053172b75
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "72026731"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-workteam"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Workteam

In deze zelfstudie leert u hoe u Workteam integreren met Azure Active Directory (Azure AD). Wanneer u Workteam integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Workteam.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Workteam met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Workteam single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Workteam ondersteunt **SP en IDP** geïnitieerd sso

## <a name="adding-workteam-from-the-gallery"></a>Workteam toevoegen vanuit de galerie

Als u de integratie van Workteam in Azure AD wilt configureren, moet u Workteam uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Workteam** in het zoekvak in de sectie **Toevoegen in de galeriesectie.**
1. Selecteer **Workteam** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-workteam"></a>Azure AD-aanmelding voor Workteam configureren en testen

Azure AD SSO configureren en testen met Workteam met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Workteam.

Als u Azure AD SSO wilt configureren en testen met Workteam, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Workteam SSO configureren](#configure-workteam-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[Workteam-testgebruiker maken](#create-workteam-test-user)** - om een tegenhanger van B.Simon in Workteam te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Workteam-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **BasisSAML-configuratie** is de toepassing vooraf geconfigureerd in de **idp-gestarte** modus en zijn de benodigde URL's al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door op de knop **Opslaan** te klikken.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    Typ een URL in het tekstvak **AANmeldings-URL:**`https://app.workte.am`

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Workteam instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Workteam.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Workteam**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

### <a name="configure-workteam-sso"></a>Workteam SSO configureren

1. Als u de configuratie binnen Workteam wilt automatiseren, moet u **de beveiligingsextensie Mijn apps secure aanmelden** installeren door op De extensie **installeren**te klikken.

    ![Extensie Mijn apps](common/install-myappssecure-extension.png)

2. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **Setup Workteam** en verwijst u naar de Workteam-toepassing. Geef van daaruit de beheerdersreferenties op om u aan te melden bij Workteam. De browserextensie configureert automatisch de toepassing voor u en automatiseert stappen 3-6.

    ![Configuratie instellen](common/setup-sso.png)

3. Als u Workteam handmatig wilt instellen, opent u een nieuw webbrowservenster en meldt u zich als beheerder aan bij uw werkteambedrijfssite en voert u de volgende stappen uit:

4. Klik in de rechterbovenhoek op **het profiellogo** en klik vervolgens op **Organisatie-instellingen**. 

    ![Workteam-instellingen](./media/workteam-tutorial/tutorial_workteam_settings.png)

5. Klik **onder de** sectie VERIFICATIE op het logo **Instellingen**.

     ![Workteam azure](./media/workteam-tutorial/tutorial_workteam_azure.png)

6. Voer op de pagina **SAML-instellingen** de volgende stappen uit:

     ![Workteam saml](./media/workteam-tutorial/tutorial_workteam_saml.png)

    a. Selecteer **SAML IdP** als **AD Azure**.

    b. Plak in het tekstvak **URL van de SAML Single Sign-On Service** de waarde van de **aanmeldings-URL**, die u hebt gekopieerd uit de Azure-portal.

    c. Plak in het tekstvak **SAML-entiteit-id** de waarde van **Azure AD-id**, die u hebt gekopieerd van de Azure-portal.

    d. Open in Kladblok het **basis-64-gecodeerde certificaat** dat u hebt gedownload van de Azure-portal, kopieer de inhoud ervan en plak het vervolgens in het vak **SAML-ondertekeningscertificaat (Base64).**

    e. Klik op **OK**.

### <a name="create-workteam-test-user"></a>Workteam-testgebruiker maken

Als u Azure AD-gebruikers in staat wilt stellen zich aan te melden bij Workteam, moeten ze worden ingericht in Workteam. In Workteam is inrichten een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij Workteam als beveiligingsbeheerder.

2. Klik in het midden van de pagina **Organisatie-instellingen** op **GEBRUIKERS** en klik vervolgens op **NIEUWE GEBRUIKER**.

    ![Workteam-gebruiker](./media/workteam-tutorial/tutorial_workteam_user.png)

3. Voer op de pagina **Nieuw werknemer** de volgende stappen uit:

    ![Workteam nieuwe gebruiker](./media/workteam-tutorial/tutorial_workteam_newuser.png)

    a. Voer in het tekstvak **Naam** de voornaam van de gebruiker in, zoals **B.Simon**.

    b. Voer in het tekstvak **Email** het e-mailadres van de gebruiker in, zoals `B.Simon\@contoso.com`.

    c. Klik op **OK**.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Workteam in het toegangspaneel klikt, moet u automatisch worden aangemeld bij het werkteam waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Workteam proberen met Azure AD](https://aad.portal.azure.com/)

