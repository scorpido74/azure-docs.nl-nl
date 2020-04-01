---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Slack | Microsoft Documenten'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Slack.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ffc5e73f-6c38-4bbb-876a-a7dd269d4e1c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6c80963976783321d05fc6f32bb24daed36fa105
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76985530"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Slack

In deze zelfstudie leert u hoe u Slack integreert met Azure Active Directory (Azure AD). Wanneer u Slack integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Slack.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Slack met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Slack single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Slack ondersteunt eenmalige aanmelding die wordt gestart vanuit **SP**
* Slack biedt ondersteuning voor het **Just In Time** inrichten van gebruikers
* Slack biedt ondersteuning voor het [**geautomatiseerd**](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-provisioning-tutorial) inrichten van gebruikers
* Zodra u Slack configureert, u sessiebeheer afdwingen, die exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebeheer strekt zich uit van Voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> Id van deze toepassing is een vaste tekenreekswaarde, zodat slechts één instantie in één tenant kan worden geconfigureerd.

## <a name="adding-slack-from-the-gallery"></a>Slack toevoegen vanuit de galerie

Als u de integratie van Slack in Azure AD wilt configureren, moet u Slack vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ Slack in het zoekvak in de sectie Toevoegen in de sectie **Toevoegen** in **de galerie.**
1. Selecteer **Slack** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-slack"></a>Azure AD-aanmelding voor Slack configureren en testen

Azure AD SSO configureren en testen met Slack met een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Slack.

Als u Azure AD SSO met Slack wilt configureren en testen, voert u de volgende bouwstenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Slack SSO configureren](#configure-slack-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Slack-testgebruiker maken](#create-slack-test-user)** - om een tegenhanger van B.Simon in Slack te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Slack-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.slack.com`

    b. Typ een URL in het vak **Id (Entiteits-id)**: `https://slack.com`

    > [!NOTE]
    > De waarde van de aanmeldings-URL is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [Slack-clientondersteuningsteam](https://slack.com/help/contact) om de waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Slack-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/edit-attribute.png)

1. Naast bovenstaande, Slack applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw vereisten. Als de gebruikers geen e-mailadres hebben, wijs het **e-mailadres dan** toe aan **user.userprincipalname.**

    | Name | Bronkenmerk |
    | -----|---------|
    | emailaddress | user.userprincipalname |
    | | |

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **Slack instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Slack.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer in de lijst met toepassingen de optie **Slack**.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-slack-sso"></a>Slack SSO configureren

1. Meld u in een ander browservenster aan bij uw Slack-bedrijfssite als beheerder.

2. Navigeer naar **Microsoft Azure AD** en ga naar **Teaminstellingen**.

     ![Eenmalige aanmelding aan app-zijde configureren](./media/slack-tutorial/tutorial_slack_001.png)

3. Klik in het gedeelte **Teaminstellingen** op het tabblad **Verificatie** en klik op **Instellingen wijzigen**.

    ![Eenmalige aanmelding aan app-zijde configureren](./media/slack-tutorial/tutorial_slack_002.png)

4. Voer in het dialoogvenster **SAML-verificatie-instellingen** de volgende stappen uit:

    ![Eenmalige aanmelding aan app-zijde configureren](./media/slack-tutorial/tutorial_slack_003.png)

    a.  Plak de waarde van **Aanmeldings-URL**, die u hebt gekopieerd vanuit Azure Portal, in het tekstvak **SAML 2.0-eindpunt (HTTP)**.

    b.  Plak in het tekstvak van de **identiteitsprovideruitgever** de waarde van **Azure Ad Identifier**, die u hebt gekopieerd van Azure-portal.

    c.  Open het gedownloade certificaatbestand in Kladblok, kopieer de inhoud ervan naar het klembord en plak deze in het tekstvak **Openbaar certificaat**.

    d. Configureer de bovenstaande drie instellingen in overeenstemming met uw Slack-team. Raadpleeg hier de **Configuratiehandleiding voor eenmalige aanmelding van Slack** voor meer informatie over de instellingen. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  Klik op **Configuratie opslaan**.

### <a name="create-slack-test-user"></a>Slack-testgebruiker maken

Het doel van deze sectie is het creëren van een gebruiker genaamd B.Simon in Slack. Slack ondersteunt Just-In-Time inrichting, dit is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Tijdens een poging Slack te openen, wordt er een nieuwe gebruiker gemaakt als deze nog niet bestaat. Slack ondersteunt ook automatische inrichting van gebruikers. Meer details over het configureren van automatische inrichting van gebruikers vindt u [hier](slack-provisioning-tutorial.md).

> [!NOTE]
> Als u een gebruiker handmatig moet maken, neem dan contact op met het [Slack-ondersteuningsteam](https://slack.com/help/contact).

> [!NOTE]
> Azure AD Connect is het synchronisatieprogramma dat Active Directory Identities on premises kan synchroniseren met Azure AD en vervolgens kunnen deze gesynchroniseerde gebruikers de toepassingen ook gebruiken zoals andere cloudgebruikers.

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Slack in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van Slack waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Slack uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)