---
title: 'Zelfstudie: SSO-integratie (Single Sign-On) van Azure Active Directory met Live Chat van Comm100 | Microsoft Documenten'
description: Leer hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Comm100 Live Chat.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0340d7f3-ab54-49ef-b77c-62a0efd5d49c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e41161a2e1bfd5544410bba0cb470bacbf152fd2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75561249"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-comm100-live-chat"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Comm100 Live Chat

In deze zelfstudie leert u hoe u Comm100 Live Chat integreert met Azure Active Directory (Azure AD). Wanneer u Comm100 Live Chat integreert met Azure AD, u het als:

* Beheer in Azure AD wie toegang heeft tot Comm100 Live Chat.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Comm100 Live Chat met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Met comm100 Live Chat single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Comm100 Live Chat ondersteunt door **SP** geïnitieerde eenmalige aanmelding

> [!NOTE]
> Id van deze toepassing is een vaste tekenreekswaarde, zodat slechts één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Comm100 Live Chat toevoegen vanuit de galerie

Om de integratie van Comm100 Live Chat in Azure AD te configureren, moet u Comm100 Live Chat vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Comm100 Live Chat** in het zoekvak in de sectie **Toevoegen in de galerijsectie.**
1. Selecteer **Comm100 Live Chat** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-comm100-live-chat"></a>Azure AD-aanmelding configureren en testen voor Live Chat van Comm100

Azure AD SSO configureren en testen met Comm100 Live Chat met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Comm100 Live Chat.

Als u Azure AD SSO wilt configureren en testen met Comm100 Live Chat, voert u de volgende bouwstenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer Comm100 Live Chat SSO](#configure-comm100-live-chat-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. Maak de gebruiker van **[de Live Chat van Comm100](#create-comm100-live-chat-test-user)** - om een tegenhanger van B.Simon te hebben in Comm100 Live Chat die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina Met live chattoepassingen van **Comm100** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`

    > [!NOTE] 
    > De waarde voor de aanmeldings-URL is niet echt. Vervang de waarde voor de aanmeldings-URL door de echte URL. Dit wordt later in de zelfstudie uitgelegd.

1. Comm100 Live Chat-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/edit-attribute.png)

1. Naast bovenstaande, Comm100 Live Chat applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw eis.

    | Name |  Bronkenmerk|
    | ---------------| --------------- |
    |   e-mail    | user.mail |

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Comm100 Live Chat** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Comm100 Live Chat.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Comm100 Live Chat** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-comm100-live-chat-sso"></a>Comm100 Live Chat SSO configureren

1. Meld u in een ander browservenster aan bij Comm100 Live Chat als beveiligingsbeheerder.

1. Klik rechtsboven op **My Account**.

   ![Comm100 Live Chat myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

1. Klik aan de linkerkant op **Security** en vervolgens op **Agent Single Sign-On**.

   ![Comm100 Live Chat security](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

1. Voer op de pagina **Agent Single Sign-On** de volgende stappen uit:

   ![Comm100 Live Chat security](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

   a. Kopieer de eerste gemarkeerde koppeling en plak deze in het **tekstvak URL aanmelding** in de sectie **BasisSAML-configuratie** op azure-portal.

   b. Plak in het tekstvak **SAML SSO URL** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

   c. Plak in het tekstvak **URL van externe uitloging** de waarde van de URL van **afmelden**, die u hebt gekopieerd uit de Azure-portal.

   d. Klik op **Choose a File** om het met base-64 gecodeerde certificaat dat u hebt gedownload uit de Azure-portal te uploaden naar het **certificaat**.

   e. Klik **op Wijzigingen opslaan**.

### <a name="create-comm100-live-chat-test-user"></a>Een testgebruiker voor Comm100 Live Chat maken

Als u Azure AD-gebruikers in staat wilt stellen zich aan te melden bij Live Chat van Comm100, moeten ze zijn ingericht in Comm100 Live Chat. In Comm100 Live Chat is dat een handmatige taak.

**Voer de volgende stappen uit als u een gebruikersaccount wilt inrichten:**

1. Meld u aan bij Comm100 Live Chat als beveiligingsbeheerder.

2. Klik rechtsboven op **My Account**.

    ![Comm100 Live Chat myaccount](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. Klik aan de linkerkant op **Agents** en vervolgens op **New Agent**.

    ![Comm100 Live Chat-agent](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. Voer op de pagina **New Agent** de volgende stappen uit:

    ![Nieuwe agent voor Comm100 Live Chat](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. Voer in het tekstvak **E-mail** de e-mail van gebruiker in, zoals **\@B.simon contoso.com**.

    b. Voer in het tekstvak **Voornaam** de voornaam van de gebruiker in, zoals **B.**

    c. Voer in het tekstvak **Achternaam** de achternaam van de gebruiker in, zoals **Simon**.

    d. Voer in het tekstvak **Weergavenaam** de weergavenaam van de gebruiker in, zoals **B.simon**

    e. Typ in het tekstvak **Password** het wachtwoord.

    f. Klik op **Opslaan**.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel van Comm100 Live Chat klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van Comm100 Live Chat waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Comm100 Live Chat met Azure AD](https://aad.portal.azure.com/)

