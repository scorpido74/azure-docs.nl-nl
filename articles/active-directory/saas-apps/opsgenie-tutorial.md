---
title: 'Zelfstudie: Azure Active Directory-integratie met OpsGenie | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en OpsGenie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: 8cf82827258b3888f4c30ad39a395d697a518a32
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261092"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-opsgenie"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met OpsGenie

In deze zelfstudie leert u hoe u OpsGenie integreert met Azure Active Directory (Azure AD). Wanneer u OpsGenie integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot OpsGenie.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij OpsGenie met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* OpsGenie single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* OpsGenie ondersteunt **IDP** geïnitieerde SSO
* Zodra u OpsGenie hebt geconfigureerd, u sessiecontrole afdwingen, wat exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermt. Sessiebesturingselement strekt zich uit van Voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-opsgenie-from-the-gallery"></a>OpsGenie toevoegen vanuit de galerie

Als u de integratie van OpsGenie in Azure AD wilt configureren, moet u OpsGenie vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **OpsGenie** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **OpsGenie** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-opsgenie"></a>Azure AD-aanmelding configureren en testen voor OpsGenie

Azure AD SSO configureren en testen met OpsGenie met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in OpsGenie.

Als u Azure AD SSO wilt configureren en testen met OpsGenie, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer OpsGenie SSO](#configure-opsgenie-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Maak OpsGenie-testgebruiker](#create-opsgenie-test-user)** - om een tegenhanger van B.Simon in OpsGenie te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **OpsGenie-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`https://app.opsginie.com/auth/saml/<UNIQUEID>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://app.opsginie.com/auth/saml?id=<UNIQUEID>`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke URL-id en antwoord, die later in deze zelfstudie wordt uitgelegd.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op de kopieerknop om de **URL voor federatieve metagegevens van de app** te kopiëren en slaat u deze op uw computer op.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

1. Kopieer in de sectie **OpsGenie instellen** de juiste URL(s) op basis van uw vereiste.

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot OpsGenie.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **OpsGenie**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-opsgenie-sso"></a>OpsGenie SSO configureren

1. Open een andere browserinstantie en meld u aan bij OpsGenie als beheerder.

2. Klik **op Instellingen**en klik vervolgens op het tabblad Eén **aanmelding.**
   
    ![OpsGenie Single Sign-On](./media/opsgenie-tutorial/tutorial-opsgenie-06.png)

3. Als u SSO wilt inschakelen, selecteert u **Ingeschakeld**.
   
    ![OpsGenie-instellingen](./media/opsgenie-tutorial/tutorial-opsgenie-07.png) 

4. Klik **in** de sectie Provider op het tabblad **Azure Active Directory.**
   
    ![OpsGenie-instellingen](./media/opsgenie-tutorial/tutorial-opsgenie-08.png) 

5. Voer op de dialoogvensters Azure Active Directory de volgende stappen uit:
   
    ![OpsGenie-instellingen](./media/opsgenie-tutorial/tutorial-opsgenie-09.png)
    
    a. Kopieer de **URI-waarde van de app-id** en plak deze in het tekstvak **Id (Entity ID)** in de sectie **BasisSAML-configuratie** in de Azure-portal.

    a. Kopieer de **waarde van de URL van het antwoord** en plak deze in het tekstvak van de URL van **antwoord** in de sectie **BasisSAML-configuratie** in de Azure-portal.

    a. Plak in het **tekstvak SAML 2.0-eindpunt** **de URL-waarde van aanmelding**die u hebt gekopieerd uit de Azure-portal.
    
    b. Plak in het **tekstvak Metagegevens:** textbox de **url-waarde van app-federatie-metagegevens** die u hebt gekopieerd uit de Azure-portal.
    
    c. Als u SSO wilt inschakelen, schakelt u de schakelaar **voor eenmalig aanmelding inschakelen** in.

    d. Klik **op SSO-instellingen toepassen**.

### <a name="create-opsgenie-test-user"></a>Testgebruiker OpsGenie maken

Het doel van deze sectie is het creëren van een gebruiker genaamd B.Simon in OpsGenie. 

1. Meld u in een webbrowservenster aan bij uw OpsGenie-tenant als beheerder.

2. Navigeer naar de lijst Met gebruikers door te klikken op **Gebruikers** in het linkerdeelvenster.
   
    ![OpsGenie-instellingen](./media/opsgenie-tutorial/tutorial-opsgenie-10.png) 

3. Klik **op Gebruiker toevoegen**.

4. Voer in het dialoogvenster **Add User** de volgende stappen uit:
   
    ![OpsGenie-instellingen](./media/opsgenie-tutorial/tutorial-opsgenie-11.png)
   
    a. Typ in het tekstvak **E-mail** het e-mailadres van B.Simon dat is geadresseerd in Azure Active Directory.
   
    b. Typ **B.Simon**in het tekstvak **Volledige naam** .
   
    c. Klik op **Opslaan**. 

> [!NOTE]
> B.Simon krijgt een e-mail met instructies voor het instellen van hun profiel.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel OpsGenie in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de OpsGenie waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer OpsGenie met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)