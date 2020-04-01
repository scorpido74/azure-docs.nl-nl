---
title: 'Zelfstudie: Azure Active Directory-integratie met Everbridge | Microsoft Documenten'
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67103262"
---
# <a name="tutorial-azure-active-directory-integration-with-everbridge"></a>Zelfstudie: Azure Active Directory-integratie met Everbridge

In deze zelfstudie leert u hoe U Everbridge integreren met Azure Active Directory (Azure AD).
Wanneer u Everbridge integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Everbridge.
* Laat uw gebruikers automatisch worden aangemeld bij Everbridge met hun Azure AD-accounts. Dit toegangscontrole wordt single sign-on (SSO) genoemd.
* Beheer uw accounts op één centrale locatie met behulp van de Azure-portal.
Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory voor](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)meer informatie over de integratie van de App Voor software as a service (SaaS) met Azure AD.
Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie met Everbridge wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, u een [gratis account](https://azure.microsoft.com/free/)krijgen.
* Een Everbridge-abonnement dat gebruik maakt van één aanmelding.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Everbridge ondersteunt door IDP geïnitieerde SSO.

## <a name="add-everbridge-from-the-azure-marketplace"></a>Everbridge toevoegen vanuit de Azure Marketplace

Als u de integratie van Everbridge in Azure AD wilt configureren, voegt u Everbridge van de Azure Marketplace toe aan uw lijst met beheerde SaaS-apps.

Voer de volgende stappen uit om Everbridge toe te voegen vanuit de Azure Marketplace.

1. Selecteer **Azure Active Directory**in de [Azure-portal](https://portal.azure.com)in het linkernavigatiedeelvenster .

    ![Knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Enterprise-toepassingen**en selecteer **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing** bovenaan het dialoogvenster.

    ![Knop Nieuwe toepassing](common/add-new-app.png)

4. Voer **Everbridge**in het zoekvak in. Selecteer **Everbridge** in het resultaatpaneel en selecteer **Toevoegen**.

     ![Everbridge in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Everbridge op basis van testgebruiker Britta Simon.
Voor eenmalige aanmelding om te werken, u een koppelingsrelatie aangaan tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Everbridge.

Als u Azure AD-single sign-on met Everbridge wilt configureren en testen, voert u de volgende bouwstenen uit:

- [Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-single-sign-on) zodat uw gebruikers deze functie kunnen gebruiken.
- [Configureer Everbridge als Everbridge manager portal single sign-on](#configure-everbridge-as-everbridge-manager-portal-single-sign-on) om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
- [Configureer Everbridge als Everbridge member portal single sign-on](#configure-everbridge-as-everbridge-member-portal-single-sign-on) om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
- [Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user) voor het testen van eenmalige aanmelding van Azure AD met Britta Simon.
- [De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user) zodat Britta Simon gebruik kan maken van eenmalige aanmelding van Azure AD.
- [Maak een Everbridge-testgebruiker](#create-an-everbridge-test-user) met een tegenhanger van Britta Simon in Everbridge die is gekoppeld aan de Azure AD-weergave van de gebruiker.
- [Eenmalige aanmelding testen](#test-single-sign-on) om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om azure AD single sign-on met Everbridge te configureren.

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Everbridge-toepassingsintegratie** de optie **Eén aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. Selecteer in het dialoogvenster **Selecteer een methode voor eenmalige aanmelding** de modus **SAML/WS-Fed** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. **Selecteer** bewerken om het dialoogvenster **BasisSAML-configuratie** te openen op de pagina **Eén aanmelding instellen met SAML.**

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

    >[!NOTE]
    >Configureer de toepassing als de managerportal *of* als lidportal op zowel de Azure-portal als de Everbridge-portal.

4. Voer de volgende stappen uit om de **Everbridge-toepassing** als de **Everbridge-beheerportal**te configureren in de sectie **BasisSAML-configuratie:**

    ![Everbridge-domein- en URL's met eenmalige aanmelding](common/idp-intiated.png)

    a. Voer in het vak **Id** een URL in die het patroon volgt`https://sso.everbridge.net/<API_Name>`

    b. Voer in het vak **URL van antwoord** een URL in die het patroon volgt`https://manager.everbridge.net/saml/SSO/<API_Name>/alias/defaultAlias`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke URL-waarden voor id en antwoord. Neem contact op met het [ondersteuningsteam van Everbridge](mailto:support@everbridge.com)om aan deze waarden te komen. U ook verwijzen naar de patronen die worden weergegeven in de sectie **BasisSAML-configuratie** in de Azure-portal.

5. Voer de volgende stappen uit om de **Everbridge-toepassing** te configureren als de **Everbridge-lidportal**in de sectie **BasisSAML-configuratie:**

  * Als u de toepassing in de idp-gestarte modus wilt configureren, voert u de volgende stappen uit:

     ![Everbridge-domein- en URL's-informatie voor iDP-geïnitieerde modus](common/idp-intiated.png)

    a. Voer in het vak **Id** een URL in die het patroon volgt`https://sso.everbridge.net/<API_Name>/<Organization_ID>`

    b. Voer in het vak **URL van antwoord** een URL in die het patroon volgt`https://member.everbridge.net/saml/SSO/<API_Name>/<Organization_ID>/alias/defaultAlias`

   * Als u de toepassing in de door SP gestarte modus wilt configureren, selecteert u **Extra URL's instellen** en volgt u de volgende stap:

     ![Everbridge-domein- en URL's-informatie met één aanmelding voor sp-geïnitieerde modus](common/both-signonurl.png)

     a. Voer in het vak **Aanmeldings-URL** een URL in die het patroon volgt`https://member.everbridge.net/saml/login/<API_Name>/<Organization_ID>/alias/defaultAlias?disco=true`

     > [!NOTE]
     > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, url van antwoord en URL-waarden aantekenen. Neem contact op met het [ondersteuningsteam van Everbridge](mailto:support@everbridge.com)om aan deze waarden te komen. U ook verwijzen naar de patronen die worden weergegeven in de sectie **BasisSAML-configuratie** in de Azure-portal.

6. Selecteer op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Downloaden** om de XML **met aalmetagegevens**van de Federatie te downloaden. Sla het op uw computer op.

    ![De koppeling om het certificaat te downloaden](common/metadataxml.png)

7. Kopieer in de sectie **Everbridge instellen** de URL's die u nodig hebt voor uw vereisten:

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    - Aanmeldings-URL
    - Azure AD-id
    - Afmeldings-URL

### <a name="configure-everbridge-as-everbridge-manager-portal-single-sign-on"></a>Everbridge configureren als Everbridge manager portal single sign-on

Voer de volgende stappen uit om SSO op **Everbridge** te configureren als een **Everbridge-portaltoepassing.**
 
1. Meld u in een ander browservenster aan bij Everbridge als beheerder.

1. Selecteer in het menu bovenaan het tabblad **Instellingen.** Selecteer **Onder Beveiliging**de optie Eén **aanmelding**.
   
     ![Eenmalige aanmelding configureren](./media/everbridge-tutorial/tutorial_everbridge_002.png)
   
     a. Voer in het vak **Naam** de naam van de id-provider in. Een voorbeeld is uw bedrijfsnaam.
   
     b. Voer in het vak **API-naam** de naam van de API in.
   
     c. Selecteer **Bestand kiezen** om het metagegevensbestand te uploaden dat u hebt gedownload van de Azure-portal.
   
     d. Selecteer Identiteit voor **SAML-identiteitslocatie** **in het element NameIdentifier van de instructie Onderwerp**.
   
     e. Plak in het vak **URL voor het inloggen van de identiteitsprovider** de **URL-waarde aanmelding** die u hebt gekopieerd vanuit de Azure-portal.
   
     f. Selecteer **HTTP Redirect**voor **serviceprovider die aanvraagbinding is gestart.**

     g. Selecteer **Opslaan**.

### <a name="configure-everbridge-as-everbridge-member-portal-single-sign-on"></a>Everbridge configureren als Everbridge-lidportal single sign-on

Als u eenmalige aanmelding op **Everbridge** wilt configureren als een **Everbridge-lidportal,** stuurt u de gedownloade **Federation Metadata XML** naar het [ondersteuningsteam van Everbridge.](mailto:support@everbridge.com) Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Voer de volgende stappen uit om de testgebruiker Britta Simon in de Azure-portal te maken.

1. Selecteer in de Azure-portal in het linkerdeelvenster de optie **Azure Active Directory** > **Users** > **All users All users**.

    ![Koppelingen voor gebruikers en alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![De knop Nieuwe gebruiker](common/new-user.png)

3. Volg deze stappen in het dialoogvenster **Gebruiker**.

    ![Dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het vak **Naam****Britta Simon**in.
  
    b. Voer in het vak **Gebruikersnaam**`brittasimon@yourcompanydomain.extension` in. Een voorbeeld is BrittaSimon@contoso.com.

    c. Schakel het selectievakje **Wachtwoord weergeven** in. Noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**

    d. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

Schakel Britta Simon in om Azure single sign-on te gebruiken door toegang te verlenen tot Everbridge.

1. Selecteer **enterprise-toepassingen** > **alle toepassingen** >**Everbridge**in de Azure-portal.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Everbridge**in de lijst met toepassingen .

    ![Everbridge link in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![Koppeling gebruikers en groepen](common/users-groups-blade.png)

4. Selecteer **Gebruiker toevoegen**. Selecteer in het dialoogvenster **Toewijzing toevoegen** de optie Gebruikers **en groepen**.

    ![Dialoogvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **Britta Simon** in het dialoogvenster **Gebruikers en groepen** in de gebruikerslijst. Kies **Selecteren** onderaan het scherm.

6. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies **Selecteren** onderaan het scherm.

7. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="create-an-everbridge-test-user"></a>Een Everbridge-testgebruiker maken

In deze sectie maakt u de testgebruiker Britta Simon in Everbridge. Als u gebruikers wilt toevoegen in het Everbridge-platform, werkt u samen met het [Everbridge-ondersteuningsteam.](mailto:support@everbridge.com) Gebruikers moeten worden gemaakt en geactiveerd in Everbridge voordat u eenmalige aanmelding gebruikt. 

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

Test uw configuratie met eenmalige aanmelding in Azure AD met behulp van het Toegangspaneel.

Wanneer u de Everbridge-tegel selecteert in het toegangspaneel, moet u automatisch worden aangemeld bij het Everbridge-account waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

