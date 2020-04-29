---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Spotinst | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Spotinst.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2f6dbd70-c2db-4ae9-99ee-976c3090d214
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5490ff6c6143dff258d74e013bb9d4c821aab625
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "76263282"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-spotinst"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Spotinst

In deze zelf studie leert u hoe u Spotinst integreert met Azure Active Directory (Azure AD). Wanneer u Spotinst integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Spotinst.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Spotinst met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Spotinst-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Spotinst ondersteunt SSO die door **SP en IDP** is geïnitieerd

## <a name="adding-spotinst-from-the-gallery"></a>Spotinst toevoegen uit de galerie

Als u de integratie van Spotinst in azure AD wilt configureren, moet u Spotinst uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Spotinst** in het zoekvak.
1. Selecteer **Spotinst** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-spotinst"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Spotinst

Azure AD SSO met Spotinst configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Spotinst.

Als u Azure AD SSO wilt configureren en testen met Spotinst, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[SPOTINST SSO configureren](#configure-spotinst-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een Spotinst-test gebruiker](#create-spotinst-test-user)** -om een equivalent van B. Simon in Spotinst te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **Spotinst** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus van **IDP** wilt configureren, voert u de volgende stappen uit in de sectie **basis configuratie van SAML** :

    a. Schakel **extra Url's instellen**in.

    b. Typ een waarde in het tekstvak **Relay-status** :`<ID>`

1. Klik op **Extra URL's instellen** en voer de volgende stappen uit als u de toepassing in de met **SP** geïnitieerde modus wilt configureren:

    Typ in het tekstvak **URL voor aanmelding** de URL:`https://console.spotinst.com/auth/saml`

    > [!NOTE]
    > De waarde voor de relay-status is niet real. U gaat de waarde voor relay State bijwerken met de werkelijke waarde voor relay State, die verderop in de zelf studie wordt beschreven.

1. Klik op **Opslaan**.

1. De Spotinst-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Daarnaast verwacht Spotinst toepassing nog maar weinig kenmerken die worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    | Naam | Bronkenmerk|
    | -----| --------------- |
    | E-mail | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Op de sectie **Spotinst instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Spotinst.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Spotinst**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-spotinst-sso"></a>Spotinst SSO configureren

1. Meld u in een ander browser venster aan bij Spotinst als een beveiligings beheerder.

2. Klik rechtsboven in het scherm op het **gebruikers pictogram** en klik op **instellingen**.

    ![Spotinst-instellingen](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

3. Klik op het tabblad **beveiliging** bovenaan en selecteer vervolgens id- **providers** en voer de volgende stappen uit:

    ![Spotinst beveiliging](./media/spotinst-tutorial/tutorial_spotinst_security.png)

    a. Kopieer de waarde voor de **Relay-status** voor uw exemplaar en plak deze in het tekstvak **Relay-status** in de sectie **basis configuratie van SAML** op Azure Portal.

    b. Klik op **Bladeren** om het XML-bestand met meta gegevens te uploaden dat u hebt gedownload van Azure Portal

    c. Klik op **Opslaan**.

### <a name="create-spotinst-test-user"></a>Spotinst-test gebruiker maken

Het doel van deze sectie is het maken van een gebruiker met de naam Julia Simon in Spotinst.

1. Als u de toepassing hebt geconfigureerd in de modus door **SP** gestart, voert u de volgende stappen uit:

   a. Meld u in een ander browser venster aan bij Spotinst als een beveiligings beheerder.

   b. Klik rechtsboven in het scherm op het **gebruikers pictogram** en klik op **instellingen**.

    ![Spotinst-instellingen](./media/spotinst-tutorial/tutorial_spotinst_settings.png)

    c. Klik op **gebruikers** en selecteer **gebruiker toevoegen**.

    ![Spotinst-instellingen](./media/spotinst-tutorial/adduser1.png)

    d. Voer de volgende stappen uit in de sectie gebruiker toevoegen:

    ![Spotinst-instellingen](./media/spotinst-tutorial/adduser2.png)

    * Voer in het tekstvak **volledige naam** de volledige naam van de gebruiker in, zoals **BrittaSimon**.

    * Voer in het tekstvak **e-mail** het e-mail adres van de gebruiker `brittasimon\@contoso.com`in.

    * Selecteer de organisatie-specifieke Details voor de **rol van de organisatie, de rol van het account en de accounts**.

2. Als u de toepassing in de gestarte modus **IDP** hebt geconfigureerd, is in deze sectie geen actie-item voor u. Spotinst biedt ondersteuning voor Just-in-time-inrichting, die standaard is ingeschakeld. Er wordt een nieuwe gebruiker gemaakt tijdens een poging om toegang te krijgen tot Spotinst als deze nog niet bestaat.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Spotinst in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Spotinst waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Spotinst met Azure AD](https://aad.portal.azure.com/)

