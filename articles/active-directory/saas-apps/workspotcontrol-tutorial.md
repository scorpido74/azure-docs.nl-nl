---
title: 'Zelf studie: integratie Azure Active Directory met Workspot-besturings element | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding voor Azure Active Directory-en Workspot-besturings element.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3ea8e4e9-f61f-4f45-b635-b0e306eda3d1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/11/2019
ms.author: jeedes
ms.openlocfilehash: 086ec95531b01477be56d4b1a19d189f167a020f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67086686"
---
# <a name="tutorial-azure-active-directory-integration-with-workspot-control"></a>Zelf studie: integratie Azure Active Directory met Workspot-besturings element

In deze zelf studie leert u hoe u Workspot-besturings element integreert met Azure Active Directory (Azure AD). Wanneer u Workspot-besturings element integreert met Azure AD, kunt u het volgende doen:

* Gebruik Azure AD om te bepalen wie toegang heeft tot het Workspot-besturings element.
* Gebruikers in staat stellen om zich automatisch aan te melden bij Workspot Control (eenmalige aanmelding [SSO]) door gebruik te maken van hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [eenmalige aanmelding bij toepassingen in azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Workspot-besturings element wilt configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.

* Een Workspot-abonnement met eenmalige aanmelding beheren.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

> [!Note]
> Workspot Control ondersteunt door SP geïnitieerde en door IDP geïnitieerde SSO.


## <a name="adding-workspot-control-from-the-gallery"></a>Het besturings element Workspot toevoegen vanuit de galerie

Als u de integratie van Workspot-besturings element wilt configureren in azure AD, moet u Workspot-besturings element toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Workspot-besturings element toe te voegen vanuit de galerie:**

1. Selecteer **Azure Active Directory**in het linkerdeel venster van de [Azure Portal](https://portal.azure.com).

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

3. Selecteer een **nieuwe toepassing** boven aan het venster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Workspot Control**in, selecteer **Workspot Control** in het deel venster resultaten en selecteer vervolgens **toevoegen**.

     ![Venster toevoegen uit de galerie](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Workspot-besturings element voor een test gebruiker, Julia Simon.
Als u eenmalige aanmelding wilt gebruiken, moet u een koppeling tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Workspot-besturings element.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met Workspot Control, moet u de volgende taken uitvoeren:

1. [Eenmalige aanmelding voor Azure AD configureren](#configure-azure-ad-single-sign-on) zodat uw gebruikers de functie kunnen gebruiken.
2. [Configureer eenmalige aanmelding voor Workspot-besturings elementen](#configure-workspot-control-single-sign-on) om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
3. [Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user) eenmalige aanmelding van Azure AD voor Julia Simon te testen.
4. [De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user) zodat Britta Simon gebruik kan maken van eenmalige aanmelding van Azure AD.
5. [Maak een Workspot-controle test gebruiker](#create-a-workspot-control-test-user) voor het maken van een equivalent van Julia Simon in Workspot-besturings element dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
6. [Eenmalige aanmelding testen](#test-single-sign-on) om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD te configureren met Workspot-besturings element:

1. Selecteer op de pagina Workspot voor het **beheren** van de toepassing in de [Azure Portal](https://portal.azure.com/) **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. Selecteer in het venster **Eén aanmeldings methode selecteren** de optie **SAML** -modus om eenmalige aanmelding in te scha kelen.

    ![Selecteer een methode voor het selecteren van eenmalige aanmelding](common/select-saml-option.png)

3. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** het pictogram **bewerken** (potlood) om toegang te krijgen tot de **basis-SAML-configuratie**.

    ![Het pictogram bewerken is gemarkeerd in de basis configuratie van SAML](common/edit-urls.png)

4. Als u de toepassing in de gestarte modus in IDP wilt configureren, voert u de volgende stappen uit in de sectie **basis configuratie van SAML** :

    ![Informatie over Workspot-controle domein en Url's eenmalige aanmelding](common/idp-intiated.png)

    1. Voer in het tekstvak **id** een URL in het volgende patroon in:<br/>
    ***https://<<i> </i>INSTANCENAME>-SAML.Workspot.com/SAML/metadata***

    1. Voer in het tekstvak **antwoord-URL** een URL in het volgende patroon in:<br/>
    ***https://<<i> </i>INSTANCENAME>-SAML.Workspot.com/SAML/Assertion***

5. Als u de toepassing in de door SP geïnitieerde modus wilt configureren, selecteert u **extra Url's instellen**.

    ![Informatie over Workspot-controle domein en Url's eenmalige aanmelding](common/metadata-upload-additional-signon.png)

    Voer in het tekstvak **URL voor aanmelding** een URL in het volgende patroon in:<br/>
    ***https://<<i> </i>INSTANCENAME>-SAML.Workspot.com/***

    > [!NOTE]
    > Dit zijn geen echte waarden. Vervang deze waarden door de daad werkelijke id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteunings team van Workspot Control-client](mailto:support@workspot.com) om deze waarden op te halen. U kunt ook verwijzen naar de patronen in het gedeelte **basis configuratie van SAML** van de Azure Portal.

6. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekening certificaat** de optie **down load** to down load **Certificate (base64)** uit de beschik bare opties volgens uw vereisten. Sla het op uw computer op.

    ![De download koppeling voor het certificaat (base64)](common/certificatebase64.png)

7. Kopieer in de sectie **Workspot-besturings element instellen** de juiste url's volgens uw vereisten:

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    - **Aanmeldings-URL**

    - **Azure AD-id**

    - **Afmeldings-URL**

### <a name="configure-workspot-control-single-sign-on"></a>Eenmalige aanmelding voor Workspot Control configureren

1. Meld u in een ander browser venster aan bij Workspot-controle als beveiligings beheerder.

2. Selecteer in de werk balk boven aan de pagina **instellen** en vervolgens op **SAML**.

    ![Instelopties](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_setup.png)

3. Voer in het venster **Security Assertion Markup Language configuratie** de volgende stappen uit:
 
    ![Configuratie venster Security Assertion Markup Language](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_saml.png)

    1. Plak in het vak **Entiteits-ID** de **Azure ad-id** die u hebt gekopieerd uit de Azure Portal.

    1. Plak in het vak **aanmelding service-URL** de **aanmeldings-URL** die u hebt gekopieerd uit de Azure Portal.

    1. Plak in het vak URL van de **Afmeldings service** de **afmeldings-URL** die u hebt gekopieerd uit de Azure Portal.

    1. Selecteer **bestand bijwerken** dat u wilt uploaden naar het X. 509-certificaat het door base-64 gecodeerde certificaat dat u hebt gedownload van de Azure Portal.

    1. Selecteer **Opslaan**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in het Azure Portal.

1. Selecteer **Azure Active Directory**, **gebruikers**en vervolgens **alle gebruikers**In het linkerdeel venster van de Azure Portal.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer een **nieuwe gebruiker** boven in het venster.

    ![De knop ' nieuwe gebruiker '](common/new-user.png)

3. Voer de volgende stappen uit in de eigenschappen voor de gebruiker:

    ![Het venster gebruikers eigenschappen](common/user-properties.png)

    1. Voer **BrittaSimon**in het veld **naam** in.
  
    1. Voer in het veld **gebruikers naam** **brittasimon@* yourcompanydomain. extension * * * in. Voer bijvoorbeeld in ** BrittaSimon@contoso.<i> </i> com**.

    1. Schakel het selectie vakje **wacht woord weer geven** in. Noteer vervolgens de waarde die wordt weer gegeven in het vak **wacht woord** .

    1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie verleent u Julia Simon toegang tot Workspot Control om haar in te scha kelen voor het gebruik van eenmalige aanmelding voor Azure.

1. Selecteer in de Azure Portal **bedrijfs toepassingen**, **alle toepassingen**en vervolgens **Workspot besturings element**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Workspot-besturings element**.

    ![De koppeling voor het besturings element Workspot in de lijst met toepassingen](common/all-applications.png)

3. Selecteer **gebruikers en groepen**in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Selecteer de knop **gebruiker toevoegen** . Selecteer vervolgens **gebruikers en groepen** in het venster **toewijzing toevoegen** .

    ![Het venster toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het venster **gebruikers en groepen** **Julia Simon** in de lijst **gebruikers** . Klik vervolgens op **Selecteren**.

6. Als u een wille keurige rol in de SAML-bevestiging verwacht, selecteert u de juiste rol voor de gebruiker in de lijst in het venster **functie selecteren** . Klik vervolgens onderaan op **selecteren** .

7. Selecteer in het venster **toewijzing toevoegen** de optie **toewijzen**.

### <a name="create-a-workspot-control-test-user"></a>Een test gebruiker voor Workspot-controle maken

Om ervoor te zorgen dat Azure AD-gebruikers zich kunnen aanmelden bij Workspot Control, moeten ze worden ingericht in het besturings element Workspot. Het inrichten is een hand matige taak.

**Voer de volgende stappen uit om een gebruikers account in te richten:**

1. Meld u aan bij Workspot-controle als een beveiligings beheerder.

2. Selecteer in de werk balk boven aan de pagina de optie **gebruikers** en vervolgens **gebruiker toevoegen**.

    ![Opties voor gebruikers](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_adduser.png)

3. Voer in het venster **een nieuwe gebruiker toevoegen** de volgende stappen uit:

    ![Venster ' een nieuwe gebruiker toevoegen '](./media/workspotcontrol-tutorial/tutorial_workspotcontrol_addnewuser.png)

    1. Voer in het vak **voor naam** de voor naam van een gebruiker in, zoals **Julia**.

    1. Voer in het tekstvak **laatste naam** de achternaam van de gebruiker in, bijvoorbeeld **Simon**.

    1. Voer in het vak **e-mail** het e-mail adres van de gebruiker in, bijvoorbeeld ** Brittasimon@contoso.<i> </i> com**.

    1. Selecteer de juiste gebruikersrol in de vervolg keuzelijst **rol** .

    1. Selecteer de juiste gebruikers groep in de vervolg keuzelijst **groep** .

    1. Selecteer **gebruiker toevoegen**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie testen we de configuratie van eenmalige aanmelding voor Azure AD via *het toegangs venster*.

Wanneer u op de tegel **besturings element Workspot** in het toegangs venster klikt, moet u automatisch worden aangemeld bij het besturings element Workspot waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access) (Inleiding tot het toegangsvenster) voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [Eenmalige aanmelding bij toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
