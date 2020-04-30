---
title: 'Zelf studie: integratie Azure Active Directory met Everbridge | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Everbridge.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 58d7cd22-98c0-4606-9ce5-8bdb22ee8b3e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 60463a00c6864bed7b3a18e816ef0143d3573782
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "67103262"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Zelf studie: integratie Azure Active Directory met Everbridge

In deze zelf studie leert u hoe u Everbridge integreert met Azure Active Directory (Azure AD).
Wanneer u Everbridge integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Everbridge.
* Toestaan dat uw gebruikers automatisch worden aangemeld bij Everbridge met hun Azure AD-accounts. Dit toegangs beheer wordt eenmalige aanmelding (SSO) genoemd.
* Beheer uw accounts op één centrale locatie met behulp van de Azure Portal.
Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van een SaaS-app (Software as a Service) met Azure AD.
Als u nog geen abonnement op Azure hebt, [Maak dan een gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met Everbridge wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Een Everbridge-abonnement dat gebruikmaakt van eenmalige aanmelding.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Everbridge ondersteunt door IDP geïnitieerde SSO.

## <a name="add-everbridge-from-the-azure-marketplace"></a>Everbridge toevoegen vanuit Azure Marketplace

Als u de integratie van Everbridge in azure AD wilt configureren, voegt u Everbridge van de Azure Marketplace toe aan uw lijst met beheerde SaaS-apps.

Voer de volgende stappen uit om Everbridge toe te voegen vanuit Azure Marketplace.

1. Selecteer **Azure Active Directory**in de [Azure Portal](https://portal.azure.com)in het navigatie deel venster links.

    ![Knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **bedrijfs toepassingen**en selecteer **alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** bovenaan het dialoogvenster.

    ![Knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **Everbridge**in het zoekvak. Selecteer **Everbridge** in het deel venster met resultaten en selecteer **toevoegen**.

     ![Everbridge in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie kunt u eenmalige aanmelding voor Azure AD configureren en testen met Everbridge op basis van de test gebruiker Julia Simon.
Als u eenmalige aanmelding wilt gebruiken, stelt u een koppelings relatie in tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Everbridge.

Als u eenmalige aanmelding voor Azure AD wilt configureren en testen met Everbridge, voltooit u de volgende bouw stenen:

- [Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-single-sign-on) zodat uw gebruikers deze functie kunnen gebruiken.
- [Configureer Everbridge als Everbridge Manager Portal-](#configure-everbridge-as-everbridge-manager-portal-single-sign-on) eenmalige aanmelding om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
- [Configureer Everbridge als Everbridge-lid Portal eenmalige aanmelding](#configure-everbridge-as-everbridge-member-portal-single-sign-on) om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
- [Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user) voor het testen van eenmalige aanmelding van Azure AD met Britta Simon.
- [De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user) zodat Britta Simon gebruik kan maken van eenmalige aanmelding van Azure AD.
- [Maak een Everbridge-test gebruiker](#create-an-everbridge-test-user) die een equivalent van Julia Simon in Everbridge heeft dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
- [Eenmalige aanmelding testen](#test-single-sign-on) om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om eenmalige aanmelding voor Azure AD met Everbridge te configureren.

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **Everbridge** Application Integration de optie **eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. Selecteer in het dialoogvenster **Selecteer een methode voor eenmalige aanmelding** de modus **SAML/WS-Fed** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** de optie **bewerken** om het dialoog venster **eenvoudige SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

    >[!NOTE]
    >Configureer de toepassing als de Manager-Portal *of* als de leden Portal op zowel de Azure portal als de Everbridge-Portal.

4. Voer de volgende stappen uit als u de **Everbridge** -toepassing wilt configureren als de **Everbridge Manager-Portal**in het gedeelte **basis configuratie van SAML** :

    ![Informatie over eenmalige aanmelding voor Everbridge domein en Url's](common/idp-intiated.png)

    a. Voer in het vak **id** een URL in die volgt op het patroon`https://sso.everbridge.net/<API_Name>`

    b. Voer in het vak **antwoord-URL** een URL in die volgt op het patroon`https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Dit zijn geen echte waarden. Deze waarden bijwerken met de daad werkelijke id en antwoord-URL-waarden. Neem contact op met het [ondersteunings team van Everbridge](mailto:support@everbridge.com)om deze waarden te verkrijgen. U kunt ook verwijzen naar de patronen die worden weer gegeven in de sectie **basis configuratie van SAML** in de Azure Portal.

5. Voer de volgende stappen uit als u de **Everbridge** -toepassing wilt configureren als de **Everbridge-lid Portal**in het gedeelte **basis configuratie van SAML** :

  * Als u de toepassing in de gestarte modus wilt configureren, voert u de volgende stappen uit:

     ![Informatie over eenmalige aanmelding voor Everbridge domein en Url's voor de door IDP geïnitieerde modus](common/idp-intiated.png)

    a. Voer in het vak **id** een URL in die volgt op het patroon`https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. Voer in het vak **antwoord-URL** een URL in die volgt op het patroon`https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Als u de toepassing in de door SP geïnitieerde modus wilt configureren, selecteert u **extra Url's instellen** en volgt u deze stap:

     ![Informatie over eenmalige aanmelding voor Everbridge domein en Url's voor de door SP geïnitieerde modus](common/both-signonurl.png)

     a. Voer in het vak **AANMELDINGS URL** een URL in die volgt op het patroon`https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Dit zijn geen echte waarden. Werk deze waarden bij met de daad werkelijke id, antwoord-URL en aanmeldings-URL-waarden. Neem contact op met het [ondersteunings team van Everbridge](mailto:support@everbridge.com)om deze waarden te verkrijgen. U kunt ook verwijzen naar de patronen die worden weer gegeven in de sectie **basis configuratie van SAML** in de Azure Portal.

6. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekening certificaat** de optie **downloaden** om de **federatieve meta gegevens-XML**te downloaden. Sla het op uw computer op.

    ![De koppeling om het certificaat te downloaden](common/metadataxml.png)

7. Kopieer in de sectie **Everbridge instellen** de url's die u nodig hebt voor uw vereisten:

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    - Aanmeldings-URL
    - Azure AD-id
    - Afmeldings-URL

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>Everbridge configureren als Everbridge Manager Portal-eenmalige aanmelding

Voer de volgende stappen uit om SSO op **Everbridge** als een **Everbridge Manager-Portal** toepassing te configureren.
 
1. Meld u in een ander browser venster aan bij Everbridge als beheerder.

1. Selecteer in het menu bovenaan het tabblad **instellingen** . Onder **beveiliging**selecteert u **eenmalige aanmelding**.
   
     ![Eenmalige aanmelding configureren](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. Voer in het vak **naam** de naam van de ID-provider in. Een voor beeld is de naam van uw bedrijf.
   
     b. Voer in het vak **API-naam** de naam van de API in.
   
     c. Selecteer **bestand kiezen** om het meta gegevensbestand te uploaden dat u hebt gedownload van de Azure Portal.
   
     d. Voor de **SAML-identiteits locatie**selecteert u **identiteit in het NameIdentifier-element van de instructie subject**.
   
     e. Plak in het vak aanmeldings-URL van de **identiteits provider** de waarde voor de **aanmeldings-URL** die u hebt gekopieerd uit de Azure Portal.
   
     f. Selecteer **HTTP-omleiding**voor de door de **service provider geïnitieerde aanvraag binding**.

     g. Selecteer **Opslaan**.

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>Everbridge configureren als Everbridge-lid Portal eenmalige aanmelding

Als u eenmalige aanmelding wilt configureren op **Everbridge** als een **Everbridge-lid Portal**, verzendt u de gedownloade **federatieve meta gegevens-XML** naar het [Everbridge-ondersteunings team](mailto:support@everbridge.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Voer de volgende stappen uit om de test gebruiker Julia Simon te maken in de Azure Portal.

1. Selecteer **Azure Active Directory** > **gebruikers** > in het linkerdeel venster van de Azure Portal**alle gebruikers**.

    ![Koppelingen voor gebruikers en alle gebruikers](common/users.png)

2. Selecteer **nieuwe gebruiker** boven aan het scherm.

    ![De knop Nieuwe gebruiker](common/new-user.png)

3. Volg deze stappen in het dialoogvenster **Gebruiker**.

    ![Dialoog venster gebruiker](common/user-properties.png)

    a. Voer in het vak **Naam****Britta Simon**in.
  
    b. Voer in het vak **Gebruikersnaam**`brittasimon@yourcompanydomain.extension` in. Een voorbeeld is BrittaSimon@contoso.com.

    c. Schakel het selectie vakje **wacht woord weer geven** in. Noteer de waarde die wordt weer gegeven in het vak **wacht woord** .

    d. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

Schakel Julia Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Everbridge.

1. Selecteer in de Azure Portal**alle toepassingen** >in **bedrijfs toepassingen** > **Everbridge**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst toepassingen de optie **Everbridge**.

    ![Koppeling Everbridge in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![Koppeling gebruikers en groepen](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen**. Selecteer in het dialoog venster **toewijzing toevoegen** de optie **gebruikers en groepen**.

    ![Het dialoog venster toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoog venster **gebruikers en groepen** de optie **Julia Simon** in de lijst gebruikers. Kies **Selecteren** onderaan het scherm.

6. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies **Selecteren** onderaan het scherm.

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-an-everbridge-test-user"></a>Een Everbridge-test gebruiker maken

In deze sectie maakt u de test gebruiker Julia Simon in Everbridge. Als u gebruikers wilt toevoegen in het Everbridge-platform, moet u samen werken met het [ondersteunings team van Everbridge](mailto:support@everbridge.com). Gebruikers moeten worden gemaakt en geactiveerd in Everbridge voordat u eenmalige aanmelding gebruikt. 

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

Test uw Azure AD-configuratie voor eenmalige aanmelding via het toegangs venster.

Wanneer u de tegel Everbridge in het toegangs venster selecteert, wordt u automatisch aangemeld bij het Everbridge-account waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

