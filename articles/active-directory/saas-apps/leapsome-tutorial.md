---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Leapsome | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Leapsome.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: cb523e97-add8-4289-b106-927bf1a02188
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef28b95e779e2b814b0ae91059c3edd12644d7c9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75430945"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-leapsome"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Leapsome

In deze zelfstudie leert u hoe leapsome worden geïntegreerd met Azure Active Directory (Azure AD). Wanneer u Leapsome integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Leapsome.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Leapsome met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Leapsome single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Leapsome steunt **SP en IDP** geïnitieerd SSO

## <a name="adding-leapsome-from-the-gallery"></a>Leapsome toevoegen vanuit de galerij

Als u de integratie van Leapsome in Azure AD wilt configureren, moet u Leapsome uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Leapsome** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **Leapsome** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-leapsome"></a>Azure AD-aanmelding configureren en testen voor Leapsome

Azure AD SSO configureren en testen met Leapsome met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Leapsome.

Als u Azure AD SSO met Leapsome wilt configureren en testen, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Leapsome SSO](#configure-leapsome-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Maak Leapsome-testgebruiker](#create-leapsome-test-user)** - om een tegenhanger van B.Simon in Leapsome te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Leapsome-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    a. Typ een URL in het tekstvak **Id:**`https://www.leapsome.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login`

    > [!NOTE]
    > De voorgaande URL van antwoord en de URL-waarde van aanmelding zijn geen echte waarde. U werkt deze bij met de werkelijke waarden, die later in de zelfstudie wordt uitgelegd.

1. Leapsome-toepassing verwacht dat de SAML-beweringen in een specifieke indeling zijn, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Naast bovenstaande, Leapsome applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw vereisten.

    | Name | Bronkenmerk | Naamruimte |
    | ---------------| --------------- | --------- |  
    | firstname | user.givenname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | lastname | user.surname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | titel | user.jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | afbeelding | URL naar de afbeelding van de werknemer | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | | |

    > [!Note]
    > De waarde van het kenmerk afbeelding is niet echt. Werk deze waarde bij met de werkelijke afbeeldings-URL. Om deze waarde contact [Leapsome Client support team](mailto:support@leapsome.com).

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Leapsome instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Leapsome.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Leapsome**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-leapsome-sso"></a>Leapsome SSO configureren

1. Meld u in een ander browservenster aan bij Leapsome als beveiligingsbeheerder.

1. Klik rechtsboven op het logo Instellingen en klik vervolgens op **Beheerdersinstellingen**.

    ![Leapsome reeks](./media/leapsome-tutorial/tutorial_leapsome_admin.png)

1. Klik op de linkermenubalk op **Enkel aanmelding (SSO)** en voer op de **saml-pagina met eenmalige aanmelding (SSO)** de volgende stappen uit:

    ![Leapsome saml](./media/leapsome-tutorial/tutorial_leapsome_samlsettings.png)

    a. Selecteer **SAML-gebaseerde aanmelding inschakelen**.

    b. Kopieer de **waarde van de aanmeldings-URL (wijs uw gebruikers hier aan om aan te melden)** en plak deze in het tekstvak **VOOR aanmeldings-URL** in de sectie **BasisSAML-configuratie** op Azure-portal.

    c. Kopieer de **waarde van de antwoord-URL (ontvangt antwoord van uw identiteitsprovider)** en plak deze in het tekstvak **Voor de URL van antwoord** in de sectie **BasisSAML-configuratie** op Azure-portal.

    d. Plak in het tekstvak **SSO Login URL (verstrekt door identiteitsprovider)** de waarde van **de login-URL**, die u hebt gekopieerd uit de Azure-portal.

    e. Kopieer het certificaat dat u zonder `--BEGIN CERTIFICATE and END CERTIFICATE--` commentaar hebt gedownload van de Azure-portal en plak het in het tekstvak Certificaat **(geleverd door identiteitsprovider).**

    f. Klik **op SSO-INSTELLINGEN BIJWERKEN**.

### <a name="create-leapsome-test-user"></a>Leapsome testgebruiker maken

In deze sectie maakt u een gebruiker genaamd Britta Simon in Leapsome. Werk samen met [leapsome client support team](mailto:support@leapsome.com) om de gebruikers of het domein toe te voegen dat moet worden toegevoegd aan een lijst toestaan voor het Leapsome-platform. Als het domein door het team wordt toegevoegd, worden gebruikers automatisch ingericht op het Leapsome-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de leapsome-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Leapsome waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Leapsome met Azure AD](https://aad.portal.azure.com/)