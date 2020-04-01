---
title: 'Zelfstudie: Azure Active Directory-integratie met PageDNA | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en PageDNA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c8765864-45f4-48c2-9d86-986a4aa431e4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c7f8c8efcad0a07a3d3a56925866b10d94f82ed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "68227465"
---
# <a name="tutorial-azure-active-directory-integration-with-pagedna"></a>Zelfstudie: Azure Active Directory-integratie met PageDNA

In deze zelfstudie leert u hoe u PageDNA integreren met Azure Active Directory (Azure AD).

Het integreren van PageDNA met Azure AD biedt u de volgende voordelen:

* In Azure AD u bepalen wie toegang heeft tot PageDNA.
* U uw gebruikers automatisch laten inloggen op PageDNA (single sign-on) met hun Azure AD-accounts.
* U uw accounts beheren op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory voor](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)meer informatie over de integratie van de App Voor software as a service (SaaS) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met PageDNA wilt configureren, hebt u de volgende items nodig:

* Een Azure AD-abonnement Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.
* Een PageDNA-abonnement met eenmalige aanmelding ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureer en test u Azure AD single sign-on in een testomgeving en integreert u PageDNA met Azure AD.

PageDNA ondersteunt de volgende functies:

* SP-geïnitieerde single sign-on (SSO).

* Just-in-time gebruikersinrichting.

## <a name="add-pagedna-from-the-azure-marketplace"></a>PageDNA toevoegen vanuit de Azure Marketplace

Als u de integratie van PageDNA in Azure AD wilt configureren, moet u PageDNA van de Azure Marketplace toevoegen aan uw lijst met beheerde SaaS-apps:

1. Meld u aan bij [Azure Portal](https://portal.azure.com?azure-portal=true).
1. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.

    ![De optie Azure Active Directory](common/select-azuread.png)

1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.

    ![Het deelvenster Bedrijfstoepassingen](common/enterprise-applications.png)

1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **+ Nieuwe toepassing** boven aan het deelvenster.

    ![De optie Nieuwe toepassing](common/add-new-app.png)

1. Voer in het zoekvak **PageDNA**in . Selecteer **PageDNA**in de zoekresultaten en selecteer **Toevoegen** om de toepassing toe te voegen.

    ![PageDNA in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD single sign-on met PageDNA op basis van een testgebruiker met de naam **Britta Simon**. Voor eenmalige aanmelding aan het werk moet u een koppeling maken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in PageDNA.

Als u Azure AD-single sign-on met PageDNA wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Configureer azure AD single sign-on](#configure-azure-ad-single-sign-on)** om uw gebruikers in staat te stellen deze functie te gebruiken.
1. **[Configureer PageDNA single sign-on](#configure-pagedna-single-sign-on)** om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
1. **[Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user)** om Azure AD-enkele aanmelding met Britta Simon te testen.
1. **[Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user)** toe om Britta Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Maak een PageDNA-testgebruiker,](#create-a-pagedna-test-user)** zodat er een gebruiker is die Britta Simon heet in PageDNA die is gekoppeld aan de Azure AD-gebruiker britta Simon.
1. **[Test eenmalige aanmelding](#test-single-sign-on)** om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Ga als volgt te werk om de volgende stappen uit te voeren om de enkele aanmelding van Azure AD met PageDNA te configureren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/)op de pagina **PageDNA-toepassingsintegratie** de optie **Eén aanmelding .**

    ![Optie eenmalig aanmelden configureren](common/select-sso.png)

1. Selecteer in het deelvenster **Eenmalige aanmeldingsmethode** de **SAML/WS-Fed-modus** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

1. **Selecteer** bewerken (het potloodpictogram) in het deelvenster Eén aanmelding instellen met SAML om het deelvenster **BasisSAML-configuratie** te openen. **Set up Single Sign-On with SAML**

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Neem in het deelvenster **BasisSAML-configuratie** de volgende stappen:

    ![Informatie over pageDNA-domeinen en URL's met eenmalige aanmelding](common/sp-identifier.png)

    1. Voer in het vak **Aanmelding op URL** een URL in met een van de volgende patronen:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>` |
        | `https://<your domain>` |
        | `https://<your domain>/<your site>` |
        | `https://www.nationsprint.com/<your site>` |
        | |

    1. Voer in het vak **Id (Entity ID)** een URL in met een van de volgende patronen:

        ||
        |--|
        | `https://stores.pagedna.com/<your site>/saml2ep.cgi` |
        | `https://www.nationsprint.com/<your site>/saml2ep.cgi` |
        | |

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [PageDNA-ondersteuningsteam](mailto:success@pagedna.com)om deze waarden te bereiken. U ook verwijzen naar de patronen die worden weergegeven in het deelvenster **BasisSAML-configuratie** in de Azure-portal.

1. Selecteer in het deelvenster **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Downloaden** om **certificaat (Raw)** te downloaden van de opgegeven opties en op te slaan op uw computer.

    ![De optie Certificaat (Raw) downloaden](common/certificateraw.png)

1. Kopieer in de sectie **PageDNA instellen** de URL of URL's die u nodig hebt:

   * **Aanmeldings-URL**
   * **Azure AD-id**
   * **Afmeldings-URL**

    ![De configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="configure-pagedna-single-sign-on"></a>PageDNA-aanmelding configureren

Als u eenmalige aanmelding wilt configureren aan de PageDNA-kant, verzendt u het gedownloade certificaat (Raw) en de juiste gekopieerde URL's vanuit de Azure-portal naar het [PageDNA-ondersteuningsteam.](mailto:success@pagedna.com) Het PageDNA-team zorgt ervoor dat de SAML SSO-verbinding aan beide zijden goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In dit gedeelte gaat u een testgebruiker met de naam Britta Simon maken in de Azure-portal.

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

In deze sectie stelt u de gebruiker Britta Simon in staat om Azure single sign-on te gebruiken door de gebruiker toegang te verlenen tot PageDNA.

1. Selecteer in de Azure-portal **Enterprise-toepassingen** > **Alle toepassingen** > **PageDNA**.

    ![Deelvenster Ondernemingstoepassingen](common/enterprise-applications.png)

1. Selecteer **PageDNA**in de lijst met toepassingen .

    ![PageDNA in de lijst met toepassingen](common/all-applications.png)

1. Selecteer in het linkerdeelvenster onder **BEHEREN**de optie **Gebruikers en groepen**.

    ![De optie 'Gebruikers en groepen'](common/users-groups-blade.png)

1. Selecteer **+ Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het deelvenster **Toewijzing toevoegen.**

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

1. Selecteer **In** het deelvenster Gebruikers en groepen de optie **Britta Simon** in de lijst **Gebruikers** en kies Selecteer Selecteer onder aan het deelvenster **Selecteren.**

1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het deelvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies onder aan het deelvenster **selecteren**.

1. Selecteer **Toewijzing toewijzen** in het deelvenster Toewijzing **toevoegen**.

### <a name="create-a-pagedna-test-user"></a>Een PageDNA-testgebruiker maken

Een gebruiker genaamd Britta Simon is nu gemaakt in PageDNA. U hoeft niets te doen om deze gebruiker te maken. PageDNA ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Als een gebruiker met de naam Britta Simon nog niet bestaat in PageDNA, wordt er een nieuwe gemaakt na verificatie.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie test u uw Azure AD-configuratie met eenmalige aanmelding met de portal Mijn apps.

Wanneer u **PageDNA** selecteert in de portal Mijn apps, moet u automatisch worden aangemeld bij het PageDNA-abonnement waarvoor u eenmalige aanmelding instelt. Zie Apps openen en gebruiken [op de portal Mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie over de portal Mijn apps.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Lijst met zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

* [Eenmalige aanmelding bij toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

* [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

