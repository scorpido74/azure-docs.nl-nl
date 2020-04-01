---
title: 'Zelfstudie: Azure Active Directory-integratie met TigerText Secure Messenger | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en TigerText Secure Messenger.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 03f1e128-5bcb-4e49-b6a3-fe22eedc6d5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/29/2019
ms.author: jeedes
ms.openlocfilehash: ea3bda1dd51a7c3a2e5e3f8b669d7138898f1595
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "67088661"
---
# <a name="tutorial-azure-active-directory-integration-with-tigertext-secure-messenger"></a>Zelfstudie: Azure Active Directory-integratie met TigerText Secure Messenger

In deze zelfstudie leert u hoe u TigerText Secure Messenger integreert met Azure Active Directory (Azure AD).

De integratie van TigerText Secure Messenger met Azure AD biedt u de volgende voordelen:

* U in Azure AD bepalen wie toegang heeft tot TigerText Secure Messenger.
* U uw gebruikers automatisch laten inloggen op TigerText Secure Messenger (single sign-on) met hun Azure AD-accounts.
* U uw accounts beheren op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory voor](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)meer informatie over de integratie van de App Voor software as a service (SaaS) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Als u azure AD-integratie wilt configureren met TigerText Secure Messenger, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een TigerText Secure Messenger-abonnement met eenmalige aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureer en test u Azure AD single sign-on in een testomgeving en integreert u TigerText Secure Messenger met Azure AD.

TigerText Secure Messenger ondersteunt SP-geïnitieerde single sign-on (SSO).

## <a name="add-tigertext-secure-messenger-from-the-azure-marketplace"></a>TigerText Secure Messenger toevoegen vanuit azure marketplace

Als u de integratie van TigerText Secure Messenger in Azure AD wilt configureren, moet u TigerText Secure Messenger van de Azure Marketplace toevoegen aan uw lijst met beheerde SaaS-apps:

1. Meld u aan bij [Azure Portal](https://portal.azure.com?azure-portal=true).
1. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.

    ![De optie Azure Active Directory](common/select-azuread.png)

1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **+ Nieuwe toepassing** boven aan het deelvenster.

    ![De optie Nieuwe toepassing](common/add-new-app.png)

1. Voer **TigerText Secure Messenger**in in het zoekvak . Selecteer **TigerText Secure Messenger**in de zoekresultaten en selecteer **Toevoegen** om de toepassing toe te voegen.

    ![TigerText Secure Messenger in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met TigerText Secure Messenger op basis van een testgebruiker genaamd **Britta Simon**. Voor eenmalige aanmelding moet u een koppeling maken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in TigerText Secure Messenger.

Als u Azure AD single sign-on met TigerText Secure Messenger wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Configureer azure AD single sign-on](#configure-azure-ad-single-sign-on)** om uw gebruikers in staat te stellen deze functie te gebruiken.
1. **[Configureer TigerText Secure Messenger single sign-on](#configure-tigertext-secure-messenger-single-sign-on)** om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
1. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om Azure AD-enkele aanmelding met Britta Simon te testen.
1. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om Britta Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Maak een TigerText Secure Messenger-testgebruiker,](#create-a-tigertext-secure-messenger-test-user)** zodat er een gebruiker is die Britta Simon heet in TigerText Secure Messenger die is gekoppeld aan de Azure AD-gebruiker britta Simon.
1. **[Test eenmalige aanmelding](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Ga als volgt te werk om azure AD single sign-on te configureren met TigerText Secure Messenger:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de integratiepagina **van TigerText Secure** **Messenger-toepassingen de**optie Eén aanmelding .

    ![Optie eenmalig aanmelden configureren](common/select-sso.png)

1. Selecteer in het deelvenster **Eenmalige aanmeldingsmethode** de **SAML/WS-Fed-modus** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

1. **Selecteer** bewerken (het potloodpictogram) in het deelvenster Eén aanmelding instellen met SAML om het deelvenster **BasisSAML-configuratie** te openen. **Set up Single Sign-On with SAML**

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Neem in het deelvenster **BasisSAML-configuratie** de volgende stappen:

    ![TigerText Secure Messenger-domein en URL's met eenmalige aanmeldingsgegevens](common/sp-identifier.png)

    1. Voer in het vak **Aanmelding op URL** een URL in:

       `https://home.tigertext.com`

    1. Typ in het vak **Id (Entity ID)** een URL met het volgende patroon:

       `https://saml-lb.tigertext.me/v1/organization/<instance ID>`

    > [!NOTE]
    > De waarde **van de id-id** is niet reëel. Werk deze waarde bij met de werkelijke id. Neem contact op met het [TigerText Secure Messenger-ondersteuningsteam](mailto:prosupport@tigertext.com)om de waarde te verkrijgen. U ook verwijzen naar de patronen die worden weergegeven in het deelvenster **BasisSAML-configuratie** in de Azure-portal.

1. Selecteer in het deelvenster **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Downloaden** om de **Federation Metadata XML** uit de opgegeven opties te downloaden en op te slaan op uw computer.

    ![De optie XML-download met federatiemetagegevens](common/metadataxml.png)

1. Kopieer in de sectie **TigerText Secure Messenger** de URL of URL's die u nodig hebt:

   * **Aanmeldings-URL**
   * **Azure AD-id**
   * **Afmeldings-URL**

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-tigertext-secure-messenger-single-sign-on"></a>TigerText Secure Messenger-aanmelding configureren

Als u eenmalige aanmelding wilt configureren aan de TigerText Secure Messenger-kant, moet u de gedownloade Federation Metadata XML en de juiste gekopieerde URL's van de Azure-portal naar het [TigerText Secure Messenger-ondersteuningsteam](mailto:prosupport@tigertext.com)verzenden. Het TigerText Secure Messenger-team zorgt ervoor dat de SAML SSO-verbinding aan beide zijden goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam Britta Simon maken in de Azure-portal.

1. Selecteer in de Azure-portal in het linkerdeelvenster de optie **Azure Active Directory**   > **Users** > **All users All users**.

    ![De opties Gebruikers en 'Alle gebruikers'](common/users.png)

1. Selecteer boven aan het scherm **+ Nieuwe gebruiker**.

    ![Nieuwe gebruikersoptie](common/new-user.png)

1. Ga **in** het deelvenster Gebruiker de volgende stappen uit:

    ![Het deelvenster Gebruiker](common/user-properties.png)

    1. Voer in het vak **Naam****Britta Simon**in.
  
    1. Voer **in** het vak Gebruikersnaam **De> van BrittaSimon\@\<\< yourcompanydomain in. uitbreiding>**. Bijvoorbeeld, **BrittaSimon\@contoso.com**.

    1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**

    1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u Britta Simon inschakelen om Azure single sign-on te gebruiken door hen toegang te verlenen tot TigerText Secure Messenger.

1. Selecteer in de Azure-portal **Enterprise-toepassingen** > **Alle toepassingen** > **TigerText Secure Messenger**.

    ![Deelvenster Ondernemingstoepassingen](common/enterprise-applications.png)

1. Selecteer **TigerText Secure Messenger**in de lijst met toepassingen .

    ![TigerText Secure Messenger in de lijst met toepassingen](common/all-applications.png)

1. Selecteer in het linkerdeelvenster onder **BEHEREN**de optie **Gebruikers en groepen**.

    ![De optie 'Gebruikers en groepen'](common/users-groups-blade.png)

1. Selecteer **+ Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het deelvenster **Toewijzing toevoegen.**

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

1. Selecteer **In** het deelvenster Gebruikers en groepen de optie **Britta Simon** in de lijst **Gebruikers** en kies Selecteer Selecteer onder aan het deelvenster **Selecteren.**

1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het deelvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies onder aan het deelvenster **selecteren**.

1. Selecteer **Toewijzing toewijzen** in het deelvenster Toewijzing **toevoegen**.

### <a name="create-a-tigertext-secure-messenger-test-user"></a>Een TigerText Secure Messenger-testgebruiker maken

In deze sectie maakt u een gebruiker genaamd Britta Simon in TigerText Secure Messenger. Werk samen met het [TigerText Secure Messenger-ondersteuningsteam](mailto:prosupport@tigertext.com) om Britta Simon als gebruiker toe te voegen in TigerText Secure Messenger. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie test u uw Azure AD-configuratie met eenmalige aanmelding met de portal Mijn apps.

Wanneer u **TigerText Secure Messenger** selecteert in de portal Mijn apps, moet u automatisch worden aangemeld bij het TigerText Secure Messenger-abonnement waarvoor u eenmalige aanmelding hebt ingesteld. Zie Apps openen en gebruiken [op de portal Mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie over de portal Mijn apps.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Lijst met zelfstudies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
