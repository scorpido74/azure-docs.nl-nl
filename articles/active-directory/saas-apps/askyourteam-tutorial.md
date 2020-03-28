---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met AskYourTeam | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en AskYourTeam.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1cef6764-de54-4920-b0ad-e560c214c72e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 403f39de1b7d226d9feeb33388c32f63271fdcd6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "78968565"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askyourteam"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met AskYourTeam

In deze zelfstudie leert u hoe u AskYourTeam integreren met Azure Active Directory (Azure AD). Wanneer u AskYourTeam integreert met Azure AD, u het als nog niet doen:

* Beheer in Azure AD die toegang heeft tot AskYourTeam.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij AskYourTeam met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* AskYourTeam single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* AskYourTeam ondersteunt **SP en IDP** geïnitieerdse SSO.
* Zodra u AskYourTeam hebt geconfigureerd, u sessiecontrole afdwingen, wat exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermt. Sessiebesturingselement strekt zich uit van Voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-askyourteam-from-the-gallery"></a>AskYourTeam toevoegen vanuit de galerie

Als u de integratie van AskYourTeam in Azure AD wilt configureren, moet u AskYourTeam vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **AskYourTeam** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **AskYourTeam** in het resultatenpaneel en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-askyourteam"></a>Azure AD-aanmelding voor AskYourTeam configureren en testen

Azure AD SSO configureren en testen met AskYourTeam met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppeling smaken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in AskYourTeam.

Als u Azure AD SSO wilt configureren en testen met AskYourTeam, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[AskYourTeam SSO configureren](#configure-askyourteam-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[AskYourTeam-testgebruiker maken](#create-askyourteam-test-user)** - om een tegenhanger van B.Simon in AskYourTeam te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **AskYourTeam-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    Typ in het tekstvak **URL beantwoorden** een URL met het volgende patroon:`https://<COMPANY>.app.askyourteam.com/users/auth/saml/callback`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<COMPANY>.app.askyourteam.com/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke URL van het antwoord en de URL-waarden aanmelding die later in de zelfstudie worden uitgelegd.

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **AskYourTeam instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot AskYourTeam.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **AskYourTeam**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-askyourteam-sso"></a>AskYourTeam SSO configureren

1. Meld u in een ander browservenster aan bij de AskYourTeam-website als beheerder.

1. Klik op de **Mijn Organisatie**.

    ![AskYourTeam-configuratie](./media/askyourteam-tutorial/user1.png)

1. Klik op **Integraties**.

    ![AskYourTeam-configuratie](./media/askyourteam-tutorial/configure1.png)

1. Klik op **Instellingen bewerken**.

    ![AskYourTeam-configuratie](./media/askyourteam-tutorial/configure2.png)

1. Voer op de pagina **Eén aanmeldingsintegratie** bewerken de volgende stappen uit: 

    ![AskYourTeam-configuratie](./media/askyourteam-tutorial/configure3.png)

    a. Plak in het tekstvak **URL-url van de SAML Single Sign-On Service** de **url-waarde aanmelding** die u hebt gekopieerd uit de Azure-portal.

    b. Plak in het tekstvak **SAML-entiteit-id** de azure **AD-id-waarde** die u hebt gekopieerd van de Azure-portal.

    c. Plak in het tekstvak VOOR afmelden URL de **URL-waarde van afmelden** die u hebt gekopieerd uit de **Azure-portal.**

    d. Open het gedownloade **certificaat (Base64)** van de Azure-portal in kladblok en plak de inhoud in het **SAML-ondertekeningscertificaat - Base64-tekstvak.**

    > [!NOTE]
    > U ook het **XML-bestand Federation Metadata uploaden** door op de optie **Bestand kiezen** te klikken.

    e. De waarde **van de URL van de antwoord-URL (Bevestigingsconsumentenservice)** en plak deze waarde in het tekstvak Url van **antwoord** in de sectie **BasisSAML-configuratie** in de Azure-portal.

    f. Kopieer **de URL-waarde** van Sign on, plak deze waarde in het tekstvak **Aanmelden op URL** in de sectie **BasisSAML-configuratie** in de Azure-portal.

    g. Klik op **Opslaan**.

### <a name="create-askyourteam-test-user"></a>AskYourTeam-testgebruiker maken

1. Meld u in een ander browservenster aan bij de AskYourTeam-website als beheerder.

1. Klik op de **Mijn Organisatie**.

    ![AskYourTeam-configuratie](./media/askyourteam-tutorial/user1.png)

1. Klik **op Gebruikers** en selecteer Nieuwe **gebruiker**.

    ![AskYourTeam-configuratie](./media/askyourteam-tutorial/user2.png)

1. Voer in de sectie **Nieuwe gebruiker** de volgende stappen uit:

    ![AskYourTeam-configuratie](./media/askyourteam-tutorial/user3.png)

    1. Voer in het tekstvak **Voornaam** de voornaam van de gebruiker in.

    1. Voer in het tekstvak **Achternaam** de achternaam van de gebruiker in.

    1. Voer in het **tekstvak E-mail** het B.Simon@contoso.come-mailadres van de gebruiker in, zoals .

    1. Selecteer de **rol** voor de gebruiker op basis van de vereisten van uw organisatie.

    1. Klik op **Opslaan**.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de AskYourTeam-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij het AskYourTeam waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [AskYourTeam uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
