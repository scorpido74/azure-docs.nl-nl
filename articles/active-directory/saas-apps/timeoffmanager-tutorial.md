---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met TimeOffManager | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en TimeOffManager.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3685912f-d5aa-4730-ab58-35a088fc1cc3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 06aa2ddf3e7168147ec091ef6fb9826025f23364
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75561810"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-timeoffmanager"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met TimeOffManager

In deze zelfstudie leert u hoe u TimeOffManager integreren met Azure Active Directory (Azure AD). Wanneer u TimeOffManager integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot TimeOffManager.
* Stel uw gebruikers in om automatisch te worden aangemeld bij TimeOffManager met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* TimeOffManager single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.


* TimeOffManager ondersteunt **Door IDP** geïnitieerde SSO

* TimeOffManager ondersteunt **Just In Time-gebruikersinrichting**

> [!NOTE]
> Id van deze toepassing is een vaste tekenreekswaarde, zodat slechts één instantie in één tenant kan worden geconfigureerd.


## <a name="adding-timeoffmanager-from-the-gallery"></a>TimeOffManager toevoegen vanuit de galerie

Als u de integratie van TimeOffManager in Azure AD wilt configureren, moet u TimeOffManager vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **TimeOffManager** in het zoekvak in de sectie **Toevoegen in de galeriesectie.**
1. Selecteer **TimeOffManager** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-timeoffmanager"></a>Azure AD-aanmelding configureren en testen voor TimeOffManager

Azure AD SSO configureren en testen met TimeOffManager met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in TimeOffManager.

Als u Azure AD SSO wilt configureren en testen met TimeOffManager, voert u de volgende bouwstenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[TimeOffManager SSO configureren](#configure-timeoffmanager-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. **[TimeOffManager-testgebruiker maken](#create-timeoffmanager-test-user)** - om een tegenhanger van B.Simon in TimeOffManager te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **TimeOffManager-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    Typ in het tekstvak **URL beantwoorden** een URL met het volgende patroon:`https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company_id=<companyid>`

    > [!NOTE]
    > Deze waarde is niet echt. Werk deze waarde bij met de werkelijke antwoord-URL. U deze waarde ophalen op **de pagina Instellingen voor één teken** die later in de zelfstudie of het ondersteuningsteam van Contact [TimeOffManager](https://www.purelyhr.com/contact-us)wordt uitgelegd. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. TimeOffManager-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/edit-attribute.png)

1. Naast bovenstaande, TimeOffManager applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw eis.

    | Name | Bronkenmerk|
    | --- | --- |
    | Voornaam |User.givenname |
    | Achternaam |User.surname |
    | Email |User.mail |

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **TimeOffManager instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot TimeOffManager.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **TimeOffManager**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-timeoffmanager-sso"></a>TimeOffManager SSO configureren

1. Meld u in een ander browservenster aan op uw TimeOffManager-bedrijfssite als beheerder.

2. Ga naar **Accountaccountopties \> \> Instellingen voor één aanmelding**.
   
    ![Instellingen voor eenmalig aanmelden](./media/timeoffmanager-tutorial/ic795917.png "Instellingen voor eenmalig aanmelden")

3. Voer in de sectie **Instellingen voor aanmelding** smaken de volgende stappen uit:
   
    ![Instellingen voor eenmalig aanmelden](./media/timeoffmanager-tutorial/ic795918.png "Instellingen voor eenmalig aanmelden")
   
    a. Open uw basis-64 gecodeerd certificaat in kladblok, kopieer de inhoud ervan in uw klembord en plak het volledige certificaat in het tekstvak **van het Certificaat x.509.**
   
    b. Plak in het tekstvak van **Idp Issuer** de waarde van **Azure AD Identifier** die u hebt gekopieerd van Azure-portal.
   
    c. Plak in het tekstvak **IDP Endpoint URL** de waarde van **de aanmeldings-URL** die u hebt gekopieerd vanuit azure-portal.
   
    d. Als **SAML afdwingen,** selecteert u **Nee**.
   
    e. Selecteer **Auto-Create Users** **Ja**.
   
    f. Plak in het tekstvak **VAN de URL van afmelden** de waarde van de URL van **afmelden** die u hebt gekopieerd uit azure-portal.
   
    g. klik **op Wijzigingen opslaan**.

4. Kopieer in de pagina **Instellingen voor één teken** op de waarde van de URL van de **Bevestigingsconsumentenservice** en plak deze in het tekstvak **Voor-url beantwoorden** onder de sectie **BasisSAML-configuratie** in Azure-portal. 

      ![Instellingen voor eenmalig aanmelden](./media/timeoffmanager-tutorial/ic795915.png "Instellingen voor eenmalig aanmelden")

### <a name="create-timeoffmanager-test-user"></a>TimeOffManager-testgebruiker maken

In deze sectie wordt een gebruiker genaamd Britta Simon gemaakt in TimeOffManager. TimeOffManager ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in TimeOffManager, wordt er een nieuwe gemaakt na verificatie.

>[!NOTE]
>U alle andere timeoffmanager-hulpprogramma's voor het maken van gebruikersaccounts of API's die door TimeOffManager worden verstrekt, gebruiken om Azure AD-gebruikersaccounts in te richten.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel TimeOffManager in het access-paneel klikt, moet u automatisch worden aangemeld bij de TimeOffManager waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [TimeOffManager uitproberen met Azure AD](https://aad.portal.azure.com/)

