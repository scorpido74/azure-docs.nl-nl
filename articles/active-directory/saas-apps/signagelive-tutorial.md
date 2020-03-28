---
title: 'Zelfstudie: Azure Active Directory-integratie met Signagelive | Microsoft Documenten'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Signagelive.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d923f0e7-ad31-4d59-a6fd-f0e895e1a32d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dab2fd0ee2f25e835b4bd07a3534475d3d93b5e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160924"
---
# <a name="tutorial-azure-active-directory-integration-with-signagelive"></a>Zelfstudie: Azure Active Directory-integratie met Signagelive

In deze zelfstudie leert u hoe u Signagelive kunt integreren met Azure AD (Azure Active Directory).
Integratie van Signagelive met Azure AD biedt u de volgende voordelen:

* U kunt in Azure AD beheren wie toegang heeft tot Signagelive.
* U uw gebruikers automatisch laten inloggen op Signagelive (single sign-on) met hun Azure AD-accounts.
* U uw accounts beheren op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en enkele aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD. Als u geen Azure-abonnement hebt, [maakt u een gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om Azure AD-integratie met Signagelive te configureren:

* Een Azure AD-abonnement Als u geen Azure [AD-omgeving](https://azure.microsoft.com/pricing/free-trial/)hebt, u een proefperiode van één maand krijgen.
* Een Signagelive single-sign-on-enabled abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Signagelive ondersteunt SP-geïnitieerde SSO.

## <a name="add-signagelive-from-the-gallery"></a>Signagelive toevoegen vanuit de galerij

Als u de integratie van Signagelive in Azure AD wilt configureren, voegt u Signagelive vanuit de galerie eerst toe aan uw lijst met beheerde SaaS-apps.

Ga als volgt te werk om Signagelive vanuit de galerie toe te voegen:

1. Selecteer in de [Azure-portal](https://portal.azure.com)in het linkerdeelvenster het Azure **Active Directory-pictogram.**

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Ga naar **Ondernemingstoepassingen**en selecteer de optie **Alle toepassingen.**

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u een nieuwe toepassing wilt toevoegen, selecteert u de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Voer in het zoekvak **Signagelive**in. 

     ![Signagelive in de resultatenlijst](common/search-new-app.png)

5. Selecteer **Signagelive** in het resultatenvenster en selecteer vervolgens de knop **Toevoegen** om de toepassing toe te voegen.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u Azure AD-eenmalige aanmelding met Signagelive op basis van een testgebruiker met de naam **Britta Simon**.
Voor eenmalige aanmelding op het werk moet u een koppeling maken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Signagelive.

Als u Azure AD-single sign-on met Signagelive wilt configureren en testen, voert u eerst de volgende bouwstenen uit:

1. [Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-single-sign-on) zodat uw gebruikers deze functie kunnen gebruiken.
2. [Configureer Signagelive single sign-on](#configure-signagelive-single-sign-on) om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
3. [Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user) voor het testen van eenmalige aanmelding van Azure AD met Britta Simon.
4. [De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user) zodat Britta Simon gebruik kan maken van eenmalige aanmelding van Azure AD.
5. [Maak een Signagelive-testgebruiker](#create-a-signagelive-test-user) om een tegenhanger van Britta Simon in Signagelive te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. [Eenmalige aanmelding testen](#test-single-sign-on) om te controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Ga als volgt te werk om Azure AD single sign-on te configureren met Signagelive:

1. Selecteer in de [Azure-portal](https://portal.azure.com/) op de pagina voor integratie van toepassingen met **Signagelive** de optie **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. Selecteer **SAML** in het dialoogvenster **Een enkele aanmeldingsmethode** selecteren om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. **Selecteer** bewerken om het dialoogvenster **BasisSAML-configuratie** te openen op de pagina **Eén aanmelding instellen met SAML.**

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Neem in de sectie **BasisSAML-configuratie** de volgende stappen:

    ![Informatie over eenmalige aanmelding bij Signagelive-domeinen en -URL's](common/sp-signonurl.png)

    Voer in het vak **URL aanmelden** een URL in die het volgende patroon gebruikt:`https://login.signagelive.com/sso/<ORGANIZATIONALUNITNAME>`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [Signagelive Client support team](mailto:support@signagelive.com) om de waarde te krijgen. U ook verwijzen naar de patronen die worden weergegeven in de sectie **BasisSAML-configuratie** in de Azure-portal.

5. Selecteer op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** de optie **Downloaden** om het **certificaat (Raw)** te downloaden van de opgegeven opties per uw vereiste. Sla het vervolgens op uw computer op.

    ![De link om het certificaat te downloaden](common/certificateraw.png)

6. Kopieer in de sectie **Signagelive instellen** de URL(s) die u nodig hebt.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-signagelive-single-sign-on"></a>Signagelive Single sign-on configureren

Als u eenmalige aanmelding wilt configureren aan de Signagelive-kant, verzendt u het gedownloade **certificaat (Raw)** en gekopieerde URL's van de Azure-portal naar het [ondersteuningsteam van Signagelive.](mailto:support@signagelive.com) Ze zorgen ervoor dat de SAML SSO-verbinding aan beide zijden goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![De knop Nieuwe gebruiker](common/new-user.png)

3. Neem **in** het dialoogvenster Gebruiker de volgende stappen.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer In het veld **Naam** **BrittaSimon**in .
  
    b. Voer **in** het veldbrittasimon@yourcompanydomain.extensionGebruikersnaam " in. In dit geval u bijvoorbeeldBrittaSimon@contoso.com.

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u Britta Simon toestemming voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Signagelive.

1. Selecteer in de Azure-portal **Enterprise-toepassingen,** selecteer **Alle toepassingen**en selecteer **vervolgens Signagelive**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen de optie **Signagelive**.

    ![De Signagelive-koppeling in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Selecteer de **knop Gebruiker toevoegen.** Selecteer vervolgens in het dialoogvenster **Toewijzing toevoegen** de optie Gebruikers **en groepen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer **Britta Simon**in het dialoogvenster **Gebruikers en groepen** in de lijst **Gebruikers** . Klik vervolgens op de knop **Selecteren** onder aan het scherm.

6. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Klik vervolgens op de knop **Selecteren** onder aan het scherm.

7. Selecteer in het dialoogvenster **Toewijzing toevoegen** de knop **Toewijzen.**

### <a name="create-a-signagelive-test-user"></a>Een Signagelive-testgebruiker maken

In deze sectie maakt u een gebruiker in Signagelive met de naam Britta Simon. Werk samen met het [Signagelive support team](mailto:support@signagelive.com) om de gebruikers toe te voegen in het Signagelive platform. U moet gebruikers maken en activeren voordat u één aanmelding gebruikt.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie test u uw Azure AD-configuratie met eenmalige aanmelding met de MyApps-portal.

Wanneer u de **tegel Signagelive** selecteert in de MyApps-portal, moet u automatisch worden aangemeld. Zie [Wat is de MyApps-portal voor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)meer informatie over de MyApps-portal? .

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

