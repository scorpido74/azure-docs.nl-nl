---
title: 'Zelfstudie: Azure Active Directory-integratie met StatusPage | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en StatusPage.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: f6ee8bb3-df43-4c0d-bf84-89f18deac4b9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: c1ad9d7afd215371b92c83445cfd1c8778bc0642
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86170527"
---
# <a name="tutorial-azure-active-directory-integration-with-statuspage"></a>Zelfstudie: Azure Active Directory-integratie met StatusPage

In deze zelfstudie leert u hoe u StatusPage kunt integreren met Azure Active Directory (Azure AD).
De integratie van StatusPage met Azure AD heeft de volgende voordelen:

* U kunt in Azure Active Directory beheren wie toegang tot StatusPage heeft.
* U kunt uw gebruikers zich automatisch laten aanmelden bij StatusPage (eenmalige aanmelding) met hun Azure AD-account.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met StatusPage hebt u de volgende zaken nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u een [gratis account](https://azure.microsoft.com/free/) krijgen
* Een abonnement op StatusPage waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* StatusPage ondersteunt door **IDP** geïnitieerde eenmalige aanmelding

## <a name="adding-statuspage-from-the-gallery"></a>StatusPage toevoegen vanuit de galerie

Voor het configureren van de integratie van StatusPage in Azure AD moet u StatusPage uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u StatusPage vanuit de galerie wilt toevoegen, voert u de volgende stappen uit:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **StatusPage**, selecteer **StatusPage** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![StatusPage in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie gaat u Azure AD-eenmalige aanmelding met StatusPage configureren en testen met behulp van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in StatusPage tot stand is gebracht.

Als u Azure AD-eenmalige aanmelding met StatusPage wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[StatusPage voor eenmalige aanmelding configureren](#configure-statuspage-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker voor StatusPage maken](#create-statuspage-test-user)** : als u een tegenhanger van Britta Simon in StatusPage wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met StatusPage moet u de volgende stappen uitvoeren:

1. In de [Azure-portal](https://portal.azure.com/) selecteert u **Eenmalige aanmelding** op de integratiepagina van de toepassing **StatusPage**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Op de pagina **Eenmalige aanmelding instellen met SAML** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij StatusPage](common/idp-intiated.png)

    a. In het tekstvak **Id** typt u een URL met het volgende patroon:

    - `https://<subdomain>.statuspagestaging.com/`
    - `https://<subdomain>.statuspage.io/`

    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon:

    - `https://<subdomain>.statuspagestaging.com/sso/saml/consume`
    - `https://<subdomain>.statuspage.io/sso/saml/consume`

    > [!NOTE]
    > Neem contact op met het ondersteunings team van StatusPage op [SupportTeam@statuspage.io](mailto:SupportTeam@statuspage.io)om metagegevens aan te vragen die nodig zijn om eenmalige aanmelding te configureren. 
    >
    > a. Kopieer de waarde van Verlener vanuit de metagegevens en plak deze in het tekstvak **Id**.
    >
    > b. Kopieer de antwoord-URL van de metagegevens en plak deze in het tekstvak **Antwoord-URL**.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

6. In de sectie **StatusPage instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-statuspage-single-sign-on"></a>StatusPage voor eenmalige aanmelding configureren

1. Meld u in een andere browser als beheerder aan bij de bedrijfssite van StatusPage.

1. Klik in de hoofdwerkbalk op **Account beheren**.

    ![Eenmalige aanmelding configureren](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Klik op het tabblad **Eenmalige aanmelding**.

    ![Eenmalige aanmelding configureren](./media/statuspage-tutorial/tutorial_statuspage_07.png)

1. Voer op de pagina Eenmalige aanmelding instellen de volgende stappen uit:

    ![Eenmalige aanmelding configureren](./media/statuspage-tutorial/tutorial_statuspage_08.png)

    ![Eenmalige aanmelding configureren](./media/statuspage-tutorial/tutorial_statuspage_09.png)

    a. Plak in het tekstvak **Doel-URL voor eenmalige aanmelding** de waarde van **Aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    b. Open het gedownloade certificaat in Kladblok, kopieer de inhoud en plak deze in het tekstvak **Certificaat**.

    c. Klik op **CONFIGURATIE OPSLAAN**.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u `brittasimon@yourcompanydomain.extension`  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot StatusPage.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en selecteer vervolgens **StatusPage**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer in de lijst met toepassingen **StatusPage**.

    ![De koppeling StatusPage in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-statuspage-test-user"></a>StatusPage-testgebruiker maken

Het doel van deze sectie is het maken van een gebruiker met de naam Britta Simon in StatusPage.

StatusPage biedt ondersteuning voor het Just-In-Time inrichten. U hebt deze al ingeschakeld in [Azure AD configureren voor eenmalige aanmelding](#configure-azure-ad-single-sign-on).

**Voer de volgende stappen uit om in StatusPage een gebruiker met de naam Britta Simon te maken:**

1. Meld u als beheerder aan bij de bedrijfssite van StatusPage.

1. Klik in het menu bovenaan op **Account beheren**.

    ![Eenmalige aanmelding configureren](./media/statuspage-tutorial/tutorial_statuspage_06.png)

1. Klik op het tabblad **Teamleden**.
  
    ![Een Azure AD-testgebruiker maken](./media/statuspage-tutorial/tutorial_statuspage_10.png) 

1. Klik op **TEAMLID TOEVOEGEN**.
  
    ![Een Azure AD-testgebruiker maken](./media/statuspage-tutorial/tutorial_statuspage_11.png) 

1. Typ in de betreffende tekstvakken het **e-mailadres**, de **voornaam** en **achternaam** van een geldige gebruiker dat u wilt inrichten. 

    ![Een Azure AD-testgebruiker maken](./media/statuspage-tutorial/tutorial_statuspage_12.png) 

1. Kies **Clientbeheerder**als **Rol**.

1. Klik op **ACCOUNT MAKEN**.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de StatusPage-tegel in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van StatusPage waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
