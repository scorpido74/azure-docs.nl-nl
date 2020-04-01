---
title: 'Zelfstudie: Azure Active Directory-integratie met Uberflip | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Uberflip.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 754b1f5b-6694-4fd6-9e1e-9fad769c64db
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: jeedes
ms.openlocfilehash: d20c05e6ec5a413b81ede9cb4906de2595967115
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "80048475"
---
# <a name="tutorial-azure-active-directory-integration-with-uberflip"></a>Zelfstudie: Azure Active Directory-integratie met Uberflip

In deze zelfstudie leert u hoe u Uberflip integreert met Azure Active Directory (Azure AD).

De integratie van Uberflip met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot Uberflip.
* U uw gebruikers automatisch laten inloggen op Uberflip (eenmalige aanmelding) met hun Azure AD-accounts.
* U uw accounts beheren op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory voor](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)meer informatie over de integratie van de App Voor software as a service (SaaS) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Als u de AD-integratie met Uberflip wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een Uberflip-abonnement met enkele aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

Uberflip ondersteunt de volgende functies:

* SP-geïnitieerde en IDP-geïnitieerde single sign-on (SSO).
* Just-in-time gebruikersinrichting.

## <a name="add-uberflip-from-the-azure-marketplace"></a>Uberflip toevoegen vanuit de Azure Marketplace

Als u de integratie van Uberflip in Azure AD wilt configureren, moet u Uberflip van de Azure Marketplace toevoegen aan uw lijst met beheerde SaaS-apps:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
1. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.

   ![De optie Azure Active Directory](common/select-azuread.png)

1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

   ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **+ Nieuwe toepassing** boven aan het deelvenster.

   ![De optie Nieuwe toepassing](common/add-new-app.png)

1. Voer **Uberflip**in in het zoekvak . Selecteer **Uberflip**in de zoekresultaten en selecteer **Toevoegen** om de toepassing toe te voegen.

   ![Uberflip in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met Uberflip op basis van een testgebruiker met de naam **B Simon**. Voor eenmalige aanmelding op het werk moet u een koppeling maken tussen een Azure AD-gebruiker en een verwante gebruiker in Uberflip.

Als u Azure AD-singlesign-aan met Uberflip wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Configureer azure AD single sign-on](#configure-azure-ad-single-sign-on)** om uw gebruikers in staat te stellen deze functie te gebruiken.
1. **[Configureer Uberflip single sign-on](#configure-uberflip-single-sign-on)** om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
1. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om Azure AD-eenmaligaanmelding met B. Simon te testen.
1. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om B. Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Maak een Uberflip-testgebruiker](#create-an-uberflip-test-user)** zodat er een gebruiker met de naam B. Simon in Uberflip is gekoppeld aan de Azure AD-gebruiker met de naam B. Simon.
1. **[Test eenmalige aanmelding](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Ga als volgt te werk om azure AD single sign-on te configureren met Uberflip:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **Uberflip-toepassingsintegratie** de optie **Eén aanmelding**.

    ![Optie eenmalig aanmelden configureren](common/select-sso.png)

1. Selecteer in het deelvenster **Eenmalige aanmeldingsmethode** de **SAML/WS-Fed-modus** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

1. **Selecteer** bewerken (het potloodpictogram) in het deelvenster Eén aanmelding instellen met SAML om het deelvenster **BasisSAML-configuratie** te openen. **Set up Single Sign-On with SAML**

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Doe in het deelvenster **BasisSAML-configuratie** een van de volgende stappen, afhankelijk van de SSO-modus die u wilt configureren:

   * Als u de toepassing wilt configureren in de Door IDP geïnitieerde SSO-modus, voert u in het vak Url van de **antwoord-URL (Bevestigingsconsumentenservice URL)** een URL in met behulp van het volgende patroon:

     `https://app.uberflip.com/sso/saml2/<IDPID>/<ACCOUNTID>`

     ![Uberflip-domein- en URL's met eenmalige aanmelding](common/both-replyurl.png)

     > [!NOTE]
     > Deze waarde is niet echt. Werk deze waarde bij met de werkelijke antwoord-URL. Neem contact op met het [Uberflip-ondersteuningsteam](mailto:support@uberflip.com)om de werkelijke waarde te krijgen. U ook verwijzen naar de patronen die worden weergegeven in het deelvenster **BasisSAML-configuratie** in de Azure-portal.

   * Als u de toepassing wilt configureren in de door SP geïnitieerde SSO-modus, selecteert u **Extra URL's instellen**en voert u in het vak **URL aanmelden** deze URL in:

     `https://app.uberflip.com/users/login`

     ![Uberflip-domein- en URL's met eenmalige aanmelding](common/both-signonurl.png)

1. Selecteer in het deelvenster **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Downloaden** om de **Federation Metadata XML** uit de opgegeven opties te downloaden en op te slaan op uw computer.

   ![De optie XML-download met federatiemetagegevens](common/metadataxml.png)

1. Kopieer in het deelvenster **Uberflip instellen** de URL of URL's die u nodig hebt:

   * **Aanmeldings-URL**
   * **Azure AD-id**
   * **Afmeldings-URL**

   ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-uberflip-single-sign-on"></a>Uberflip-aanmelding configureren

Als u eenmalige aanmelding wilt configureren aan de Uberflip-kant, moet u de gedownloade Federation Metadata XML en de juiste gekopieerde URL's vanuit de Azure-portal naar het [Uberflip-ondersteuningsteam](mailto:support@uberflip.com)verzenden. Het Uberflip-team zorgt ervoor dat de SAML SSO-verbinding aan beide zijden goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker met de naam B. Simon in de Azure-portal.

1. Selecteer in de Azure-portal in het linkerdeelvenster de optie **Azure Active Directory** > **Users** > **All users All users**.

    ![De opties Gebruikers en 'Alle gebruikers'](common/users.png)

1. Selecteer boven aan het scherm **+ Nieuwe gebruiker**.

    ![Nieuwe gebruikersoptie](common/new-user.png)

1. Ga **in** het deelvenster Gebruiker de volgende stappen uit:

    ![Het deelvenster Gebruiker](common/user-properties.png)

    1. Voer **in** het vak Naam **BSimon**in .
  
    1. Voer **in** het vak Gebruikersnaam **BSimon\@\<uw\< bedrijfsdomein in>. uitbreiding>**. Bijvoorbeeld, **BSimon\@contoso.com**.

    1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**

    1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B. Simon inschakelen om Azure single sign-on te gebruiken door hun toegang tot Uberflip te verlenen.

1. Selecteer in de Azure-portal **Enterprise Applications** > **All applications** > **Uberflip**.

    ![Deelvenster Ondernemingstoepassingen](common/enterprise-applications.png)

1. Selecteer **Uberflip**in de lijst met toepassingen .

    ![Uberflip in de lijst met toepassingen](common/all-applications.png)

1. Selecteer in het linkerdeelvenster onder **BEHEREN**de optie **Gebruikers en groepen**.

    ![De optie 'Gebruikers en groepen'](common/users-groups-blade.png)

1. Selecteer **+ Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het deelvenster **Toewijzing toevoegen.**

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

1. Selecteer **In** het deelvenster Gebruikers en groepen B **Simon** in de lijst **Gebruikers** en kies **Selecteer selecteer Selecteren** onder aan het deelvenster.

1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het deelvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies onder aan het deelvenster **selecteren**.

1. Selecteer **Toewijzing toewijzen** in het deelvenster Toewijzing **toevoegen**.

### <a name="create-an-uberflip-test-user"></a>Een Uberflip-testgebruiker maken

Een gebruiker met de naam B. Simon is nu gemaakt in Uberflip. U hoeft niets te doen om deze gebruiker te maken. Uberflip ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Als een gebruiker met de naam B. Simon nog niet bestaat in Uberflip, wordt er een nieuwe gemaakt na verificatie.

> [!NOTE]
> Als u handmatig een gebruiker wilt maken, neemt u contact op met het [ondersteuningsteam van Uberflip](mailto:support@uberflip.com).

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie test u uw Azure AD-configuratie met eenmalige aanmelding met de portal Mijn apps.

Wanneer u **Uberflip** selecteert in de portal Mijn apps, moet u automatisch zijn aangemeld bij het Uberflip-abonnement waarvoor u eenmalige aanmelding instelt. Zie Apps openen en gebruiken [op de portal Mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie over de portal Mijn apps.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Lijst met zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
