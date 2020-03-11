---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met AskYourTeam | Microsoft Docs'
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
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2020
ms.locfileid: "78968565"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askyourteam"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met AskYourTeam

In deze zelf studie leert u hoe u AskYourTeam integreert met Azure Active Directory (Azure AD). Wanneer u AskYourTeam integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot AskYourTeam.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij AskYourTeam met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* AskYourTeam-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* AskYourTeam ondersteunt door **SP en IDP** geïnitieerde SSO.
* Nadat u AskYourTeam hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessie beheer wordt uitgebreid vanuit voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-askyourteam-from-the-gallery"></a>AskYourTeam toevoegen uit de galerie

Als u de integratie van AskYourTeam in azure AD wilt configureren, moet u AskYourTeam uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **AskYourTeam** in het zoekvak.
1. Selecteer **AskYourTeam** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-askyourteam"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor AskYourTeam

Azure AD SSO met AskYourTeam configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in AskYourTeam.

Als u Azure AD SSO wilt configureren en testen met AskYourTeam, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[ASKYOURTEAM SSO configureren](#configure-askyourteam-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een AskYourTeam-test gebruiker](#create-askyourteam-test-user)** -om een equivalent van B. Simon in AskYourTeam te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **AskYourTeam** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://<COMPANY>.app.askyourteam.com/users/auth/saml/callback`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<COMPANY>.app.askyourteam.com/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de daad werkelijke antwoord-URL en de aanmeldings-URL-waarden die verderop in de zelf studie worden beschreven.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **AskYourTeam instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan AskYourTeam.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **AskYourTeam**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-askyourteam-sso"></a>AskYourTeam SSO configureren

1. Meld u in een ander webbrowser venster aan bij de AskYourTeam-website als beheerder.

1. Klik op **Mijn organisatie**.

    ![AskYourTeam-configuratie](./media/askyourteam-tutorial/user1.png)

1. Klik op **integraties**.

    ![AskYourTeam-configuratie](./media/askyourteam-tutorial/configure1.png)

1. Klik op **Instellingen bewerken**.

    ![AskYourTeam-configuratie](./media/askyourteam-tutorial/configure2.png)

1. Voer op de pagina **eenmalige aanmelding bewerken** de volgende stappen uit: 

    ![AskYourTeam-configuratie](./media/askyourteam-tutorial/configure3.png)

    a. Plak in het tekstvak **SAML-URL voor eenmalige aanmelding** de **aanmeldings-URL** die u hebt gekopieerd uit de Azure Portal.

    b. Plak in het tekstvak **id van SAML-entiteit** de **Azure ad id** -waarde die u hebt gekopieerd uit de Azure Portal.

    c. Plak in het tekstvak voor de **Afmeldings-URL** de waarde voor de **afmeldings-URL** die u hebt gekopieerd uit de Azure Portal.

    d. Open het gedownloade **certificaat (base64)** van de Azure Portal in Klad blok en plak de inhoud in het tekstvak **SAML-handtekening certificaat-base64** .

    > [!NOTE]
    > U kunt ook het **XML-bestand met federatieve meta gegevens** uploaden door te klikken op de optie **bestand kiezen** .

    e. Kopieer de **antwoord-URL (URL van de Assertion Consumer Service)** , plak deze waarde in het tekstvak **antwoord-URL** in het gedeelte basis-SAML- **configuratie** in de Azure Portal.

    f. Kopieer **teken op URL** -waarde, plak deze waarde in het tekstvak **Sign on URL** in het gedeelte **basis-SAML-configuratie** in de Azure Portal.

    g. Klik op **Opslaan**.

### <a name="create-askyourteam-test-user"></a>AskYourTeam-test gebruiker maken

1. Meld u in een ander webbrowser venster aan bij de AskYourTeam-website als beheerder.

1. Klik op **Mijn organisatie**.

    ![AskYourTeam-configuratie](./media/askyourteam-tutorial/user1.png)

1. Klik op **gebruikers** en selecteer **nieuwe gebruiker**.

    ![AskYourTeam-configuratie](./media/askyourteam-tutorial/user2.png)

1. Voer de volgende stappen uit in de sectie **nieuwe gebruiker** :

    ![AskYourTeam-configuratie](./media/askyourteam-tutorial/user3.png)

    1. Voer in het tekstvak **voor de voor naam** de voor naam van de gebruiker in.

    1. Voer in het tekstvak **Achternaam** de achternaam van de gebruiker in.

    1. Voer in het tekstvak **e-mail** het EmailAddress van de gebruiker in zoals B.Simon@contoso.com.

    1. Selecteer de **rol** voor de gebruiker volgens uw organisatie vereiste.

    1. Klik op **Opslaan**.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel AskYourTeam in het toegangs venster klikt, moet u automatisch worden aangemeld bij de AskYourTeam waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer AskYourTeam met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
