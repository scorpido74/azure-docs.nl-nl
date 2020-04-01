---
title: 'Zelfstudie: Azure Active Directory-integratie met TurboRater | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en TurboRater.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: abb116b8-8024-4cc6-bc81-f32ef490ea17
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 3/8/2019
ms.author: jeedes
ms.openlocfilehash: 3777cf09ec669fe3df6bca13f6960f53c689767c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67088277"
---
# <a name="tutorial-azure-active-directory-integration-with-turborater"></a>Zelfstudie: Azure Active Directory-integratie met TurboRater

In deze zelfstudie leert u hoe u TurboRater integreert met Azure Active Directory (Azure AD).

De integratie van TurboRater met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot TurboRater.
* U uw gebruikers automatisch laten inloggen op TurboRater (single sign-on) met hun Azure AD-accounts.
* U uw accounts beheren op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory voor](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)meer informatie over de integratie van de App Voor software as a service (SaaS) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie met TurboRater wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een TurboRater-abonnement met enkele aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

TurboRater ondersteunt IDP-geïnitieerde single sign-on (SSO).

## <a name="add-turborater-from-the-azure-marketplace"></a>TurboRater toevoegen vanuit de Azure Marketplace

Als u de integratie van TurboRater in Azure AD wilt configureren, moet u TurboRater van de Azure Marketplace toevoegen aan uw lijst met beheerde SaaS-apps:

1. Meld u aan bij [Azure Portal](https://portal.azure.com?azure-portal=true).
1. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.

    ![De optie Azure Active Directory](common/select-azuread.png)

1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![De optie Enterprise-toepassingen](common/enterprise-applications.png)

1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **+ Nieuwe toepassing** boven aan het deelvenster.

    ![De optie Nieuwe toepassing](common/add-new-app.png)

1. Voer in het zoekvak **TurboRater**in. Selecteer In de zoekresultaten **TurboRater**en selecteer vervolgens **Toevoegen** om de toepassing toe te voegen.

    ![TurboRater in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met TurboRater op basis van een testgebruiker met de naam **B Simon**. Voor eenmalige aanmelding aan het werk moet u een koppeling maken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in TurboRater.

Als u Azure AD-single sign-on met TurboRater wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Configureer azure AD single sign-on](#configure-azure-ad-single-sign-on)** om uw gebruikers in staat te stellen deze functie te gebruiken.
1. **[Configureer TurboRater single sign-on](#configure-turborater-single-sign-on)** om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
1. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om Azure AD-eenmaligaanmelding met B. Simon te testen.
1. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om B. Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Maak een TurboRater-testgebruiker](#create-a-turborater-test-user)** zodat er een gebruiker is met de naam B. Simon in TurboRater die is gekoppeld aan de Azure AD-gebruiker genaamd B. Simon.
1. **[Test eenmalige aanmelding](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit om azure AD single sign-on met TurboRater te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina Met de integratie **van de TurboRater-toepassing** de optie **Enkele aanmelding**.

    ![Optie eenmalig aanmelden configureren](common/select-sso.png)

1. Selecteer in het deelvenster **Eenmalige aanmeldingsmethode** de **SAML/WS-Fed-modus** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

1. **Selecteer** bewerken (het potloodpictogram) op de pagina Eén aanmelding instellen met SAML om het deelvenster **BasisSAML-configuratie** te openen. **Set up Single Sign-On with SAML**

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Ga in het deelvenster **BasisSAML-configuratie** de volgende stappen uit:

    ![TurboRater-domein- en URL's met eenmalige aanmelding](common/idp-intiated.png)

    1. Voer in het vak **Id (Entity ID)** een URL in:

       `https://www.itcdataservices.com`

    1. Voer in het vak URL van de **antwoord-URL (Bevestigingsconsumentenservice)** een URL in met het volgende patroon:

       | Omgeving | URL |
       | ---------------| --------------- |
       | Test  | `https://ratingqa.itcdataservices.com/webservices/imp/saml/login` |
       | Live  | `https://www.itcratingservices.com/webservices/imp/saml/login` |

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem contact op met het [Ondersteuningsteam van TurboRater](https://www.getitc.com/support)om aan deze waarden te komen. U ook verwijzen naar de patronen die worden weergegeven in het deelvenster **BasisSAML-configuratie** in de Azure-portal.

1. Selecteer in het deelvenster **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Downloaden** om de **Federation Metadata XML** uit de opgegeven opties te downloaden en op te slaan op uw computer.

    ![De optie XML-download met federatiemetagegevens](common/metadataxml.png)

1. Kopieer in de sectie **TurboRater instellen** de URL of URL's die u nodig hebt:

   * **Aanmeldings-URL**
   * **Azure AD-id**
   * **Afmeldings-URL**

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-turborater-single-sign-on"></a>TurboRater-aanmelding configureren

Als u eenmalige aanmelding wilt configureren aan de TurboRater-kant, moet u de gedownloade Federation Metadata XML en de juiste gekopieerde URL's van de Azure-portal naar het [TurboRater-ondersteuningsteam](https://www.getitc.com/support)verzenden. Het TurboRater-team zorgt ervoor dat de SAML SSO-verbinding aan beide kanten goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam Britta Simon maken in de Azure-portal.

1. Selecteer in de Azure-portal in het linkerdeelvenster de optie **Azure Active Directory**   > **Users** > **All users All users**.

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

In deze sectie stelt u B. Simon in staat om Azure single sign-on te gebruiken door hun toegang tot TurboRater te verlenen.

1. Selecteer in de Azure-portal **Enterprise-toepassingen** > **Alle toepassingen** > **TurboRater**.

    ![Deelvenster Ondernemingstoepassingen](common/enterprise-applications.png)

1. Selecteer **TurboRater**in de lijst met toepassingen .

    ![TurboRater in de lijst van toepassingen](common/all-applications.png)

1. Selecteer in het linkerdeelvenster onder **BEHEREN**de optie **Gebruikers en groepen**.

    ![De optie 'Gebruikers en groepen'](common/users-groups-blade.png)

1. Selecteer **+ Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het deelvenster **Toewijzing toevoegen.**

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

1. Selecteer **In** het deelvenster Gebruikers en groepen de optie **B. Simon** in de lijst **Gebruikers** en kies Selecteer Selecteer onder aan het deelvenster **Selecteren.**

1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het deelvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies onder aan het deelvenster **selecteren**.

1. Selecteer **Toewijzing toewijzen** in het deelvenster Toewijzing **toevoegen**.

### <a name="create-a-turborater-test-user"></a>Een TurboRater-testgebruiker maken

In deze sectie maakt u een gebruiker genaamd B. Simon in TurboRater. Werk samen met het [TurboRater support team](https://www.getitc.com/support) om B. Simon toe te voegen als gebruiker in TurboRater. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie test u uw Azure AD-configuratie met eenmalige aanmelding met de portal Mijn apps.

Wanneer u **TurboRater** selecteert in de My Apps-portal, moet u automatisch worden aangemeld bij het TurboRater-abonnement waarvoor u eenmalige aanmelding instelt. Zie Apps openen en gebruiken [op de portal Mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie over de portal Mijn apps.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Lijst met zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
