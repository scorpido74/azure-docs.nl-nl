---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Leapsome | Microsoft Docs'
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
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75430945"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-leapsome"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Leapsome

In deze zelf studie leert u hoe u Leapsome integreert met Azure Active Directory (Azure AD). Wanneer u Leapsome integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Leapsome.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Leapsome met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Leapsome-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Leapsome ondersteunt SSO die door **SP en IDP** is geïnitieerd

## <a name="adding-leapsome-from-the-gallery"></a>Leapsome toevoegen uit de galerie

Als u de integratie van Leapsome in azure AD wilt configureren, moet u Leapsome uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Leapsome** in het zoekvak.
1. Selecteer **Leapsome** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-leapsome"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Leapsome

Azure AD SSO met Leapsome configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Leapsome.

Als u Azure AD SSO wilt configureren en testen met Leapsome, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[LEAPSOME SSO configureren](#configure-leapsome-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een Leapsome-test gebruiker](#create-leapsome-test-user)** -om een equivalent van B. Simon in Leapsome te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **Leapsome** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. In het tekstvak **Id** typt u een URL: `https://www.leapsome.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login`

    > [!NOTE]
    > De waarde van de voor gaande antwoord-URL en aanmeldings-URL is geen echte waarde. Deze worden bijgewerkt met de werkelijke waarden. deze worden verderop in de zelf studie beschreven.

1. De Leapsome-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Daarnaast verwacht Leapsome toepassing nog maar weinig kenmerken die worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    | Name | Bronkenmerk | Naamruimte |
    | ---------------| --------------- | --------- |  
    | firstname | user.givenname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | lastname | user.surname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | titel | user.jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | afbeelding | URL naar de afbeelding van de werk nemer | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | | |

    > [!Note]
    > De waarde van het afbeeldings kenmerk is niet reëel. Deze waarde bijwerken met de daad werkelijke afbeeldings-URL. Neem contact op met het [ondersteunings team van Leapsome-clients](mailto:support@leapsome.com)om deze waarde te verkrijgen.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **Leapsome instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Leapsome.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Leapsome**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-leapsome-sso"></a>Leapsome SSO configureren

1. Meld u in een ander browser venster aan bij Leapsome als een beveiligings beheerder.

1. Klik in de rechter bovenhoek op instellingen logo en klik vervolgens op **beheer instellingen**.

    ![Leapsome ingesteld](./media/leapsome-tutorial/tutorial_leapsome_admin.png)

1. Klik in de linkermenu op **eenmalige aanmelding (SSO)** en voer op de pagina op **SAML gebaseerde eenmalige aanmelding (SSO)** de volgende stappen uit:

    ![Leapsome SAML](./media/leapsome-tutorial/tutorial_leapsome_samlsettings.png)

    a. Selecteer op **SAML gebaseerde eenmalige aanmelding inschakelen**.

    b. Kopieer de **aanmeldings-URL (wijs uw gebruikers hier aan om de aanmelding te starten)** en plak deze in het tekstvak **aanmeld-URL** in het gedeelte **basis-SAML-configuratie** op Azure Portal.

    c. Kopieer de **antwoord-URL (ontvangt een reactie van uw ID-provider)** en plak deze in het tekstvak **antwoord-URL** in het gedeelte basis- **SAML-configuratie** op Azure Portal.

    d. Plak de waarde van de **aanmeldings-URL**die u hebt gekopieerd uit de Azure Portal in het tekstvak AANMELD- **URL voor eenmalige aanmelding (door de ID-provider)** .

    e. Kopieer het certificaat dat u hebt gedownload van Azure Portal zonder opmerkingen `--BEGIN CERTIFICATE and END CERTIFICATE--` en plak het in het tekstvak **certificaat (verschaft door de ID-provider)** .

    f. Klik op **SSO-instellingen bijwerken**.

### <a name="create-leapsome-test-user"></a>Leapsome-test gebruiker maken

In deze sectie maakt u een gebruiker met de naam Julia Simon in Leapsome. Werk samen met het [Leapsome-client ondersteunings team](mailto:support@leapsome.com) om de gebruikers of het domein toe te voegen dat moet worden toegevoegd aan een acceptatie lijst voor het Leapsome-platform. Als het domein wordt toegevoegd door het team, worden gebruikers automatisch ingericht voor het Leapsome-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Leapsome in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Leapsome waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Leapsome met Azure AD](https://aad.portal.azure.com/)