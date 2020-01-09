---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met een toegestane vertraging | Microsoft Docs'
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
ms.date: 12/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f67a5b5513ad5d8a07551b2a9f5605fc32a9bf6
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/31/2019
ms.locfileid: "75561844"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-slack"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met toegestane vertraging

In deze zelf studie leert u hoe u de toegestane vertraging kunt integreren met Azure Active Directory (Azure AD). Wanneer u de vertraging integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot toegestane vertraging.
* Stel in dat uw gebruikers automatisch worden aangemeld voor een toegestane vertraging met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Het abonnement waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Slack ondersteunt eenmalige aanmelding die wordt gestart vanuit **SP**
* Slack biedt ondersteuning voor het **Just In Time** inrichten van gebruikers
* Slack biedt ondersteuning voor het [**geautomatiseerd**](https://docs.microsoft.com/azure/active-directory/saas-apps/slack-provisioning-tutorial) inrichten van gebruikers

> [!NOTE]
> De id van deze toepassing is een vaste teken reeks waarde zodat slechts één exemplaar in één Tenant kan worden geconfigureerd.

## <a name="adding-slack-from-the-gallery"></a>Slack toevoegen vanuit de galerie

Als u de integratie van Slack in Azure AD wilt configureren, moet u Slack vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. In de sectie **toevoegen vanuit de galerie** typt u **toegestane vertraging** in het zoekvak.
1. Selecteer **toegestane vertraging** van het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-slack"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor een toegestane vertraging

Azure AD SSO met een toegestane vertraging configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in een toegestane vertraging.

Als u Azure AD SSO wilt configureren en testen met toegestane vertraging, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Configureer de configuratie van de toegestane vertraging](#configure-slack-sso)** -voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een gebruiker met een toegestane vertragings test](#create-slack-test-user)** voor een soort van B. Simon in een toegestane vertraging die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina **toegestane** toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<companyname>.slack.com`

    b. Typ een URL in het vak **Id (Entiteits-id)** : `https://slack.com`

    > [!NOTE]
    > De waarde van de aanmeldings-URL is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met [Slack-clientondersteuningsteam](https://slack.com/help/contact) om de waarde te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Voor de toegestane toepassings grootte worden de SAML-beweringen in een specifieke indeling verwacht. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/edit-attribute.png)

1. De toegestane vertraging van de toepassing verwacht nog maar enkele kenmerken die opnieuw worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten. Als de gebruikers geen e-mail adres hebben, wijst u het **EmailAddress** toe aan **User. userPrincipalName**.

    | Name | Bronkenmerk |
    | -----|---------|
    | emailaddress | user.userprincipalname |
    | | |

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **toegestane vertraging instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om de eenmalige aanmelding van Azure te gebruiken door de toegang tot toegestane vertraging te verlenen.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst met toepassingen de optie **Slack**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-slack-sso"></a>SSO van toegestane vertraging configureren

1. Meld u in een ander browser venster aan bij de bedrijfs site van de toegestane vertraging als beheerder.

2. Navigeer naar **Microsoft Azure AD** en ga naar **Teaminstellingen**.

     ![Eenmalige aanmelding configureren aan de kant van de app](./media/slack-tutorial/tutorial_slack_001.png)

3. Klik in het gedeelte **Teaminstellingen** op het tabblad **Verificatie** en klik op **Instellingen wijzigen**.

    ![Eenmalige aanmelding configureren aan de kant van de app](./media/slack-tutorial/tutorial_slack_002.png)

4. Voer in het dialoogvenster **SAML-verificatie-instellingen** de volgende stappen uit:

    ![Eenmalige aanmelding configureren aan de kant van de app](./media/slack-tutorial/tutorial_slack_003.png)

    a.  Plak de waarde van **Aanmeldings-URL**, die u hebt gekopieerd vanuit Azure Portal, in het tekstvak **SAML 2.0-eindpunt (HTTP)** .

    b.  Plak de waarde van **Azure Ad-id**, die u hebt gekopieerd vanuit Azure Portal, in het tekstvak **URL van id-provider**.

    c.  Open het gedownloade certificaatbestand in Kladblok, kopieer de inhoud ervan naar het klembord en plak deze in het tekstvak **Openbaar certificaat**.

    d. Configureer de bovenstaande drie instellingen in overeenstemming met uw Slack-team. Raadpleeg hier de **Configuratiehandleiding voor eenmalige aanmelding van Slack** voor meer informatie over de instellingen. `https://get.slack.help/hc/articles/220403548-Guide-to-single-sign-on-with-Slack%60`

    e.  Klik op **Configuratie opslaan**.

### <a name="create-slack-test-user"></a>Slack-testgebruiker maken

Het doel van deze sectie is het maken van een gebruiker met de naam B. Simon in toegestane vertraging. Slack ondersteunt Just-In-Time inrichting, dit is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Tijdens een poging Slack te openen, wordt er een nieuwe gebruiker gemaakt als deze nog niet bestaat. Slack ondersteunt ook automatische inrichting van gebruikers. Meer details over het configureren van automatische inrichting van gebruikers vindt u [hier](slack-provisioning-tutorial.md).

> [!NOTE]
> Als u een gebruiker handmatig moet maken, neem dan contact op met het [Slack-ondersteuningsteam](https://slack.com/help/contact).

> [!NOTE]
> Azure AD Connect is het synchronisatie hulpprogramma dat kan worden gesynchroniseerd on premises Active Directory identiteiten naar Azure AD. deze gesynchroniseerde gebruikers kunnen de toepassingen ook gebruiken zoals andere Cloud gebruikers.

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Slack in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van Slack waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer vertraging met Azure AD](https://aad.portal.azure.com/)