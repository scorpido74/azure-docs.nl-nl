---
title: 'Zelfstudie: Azure Active Directory-integratie met Blackboard Learn - Shibboleth| Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Blackboard Learn - Shibboleth.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: jeedes
ms.openlocfilehash: dd9077c647d7f9f0a9272b71654767acc2e2d117
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88556042"
---
# <a name="tutorial-azure-active-directory-integration-with-blackboard-learn---shibboleth"></a>Zelfstudie: Azure Active Directory-integratie met Blackboard Learn - Shibboleth

In deze zelfstudie leert u hoe u Blackboard Learn - Shibboleth kunt integreren met Azure Active Directory (Azure AD).
De integratie van Blackboard Learn - Shibboleth met Azure AD biedt u de volgende voordelen:

* U kunt in Azure AD bepalen wie toegang heeft tot Blackboard Learn - Shibboleth.
* U kunt uw gebruikers zich automatisch laten aanmelden bij Blackboard Learn - Shibboleth (eenmalige aanmelding) met hun Azure AD-account.
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Voor het configureren van Azure AD-integratie met Blackboard Learn - Shibboleth hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een abonnement op Blackboard Learn - Shibboleth waarvoor eenmalige aanmelding is ingeschakeld

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Blackboard Learn - Shibboleth ondersteunt door **SP** geïnitieerde eenmalige aanmelding

## <a name="adding-blackboard-learn---shibboleth-from-the-gallery"></a>Blackboard Learn - Shibboleth toevoegen vanuit de galerie

Voor het configureren van de integratie van Blackboard Learn - Shibboleth in Azure AD, moet u Blackboard Learn - Shibboleth vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Voer de volgende stappen uit om Blackboard Learn - Shibboleth toe te voegen vanuit de galerie:**

1. Klik in het linkernavigatievenster in de **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ in het zoekvak **Blackboard Learn - Shibboleth**, selecteer **Blackboard Learn - Shibboleth** in het resultaatvenster en klik vervolgens op de knop **Toevoegen** om de toepassing toe te voegen.

    ![Blackboard Learn - Shibboleth in de resultatenlijst](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met Blackboard Learn - Shibboleth op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in Blackboard Learn - Shibboleth tot stand is gebracht.

Om Azure AD eenmalige aanmelding met Blackboard Learn - Shibboleth te configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[Blackboard Learn - Shibboleth voor eenmalige aanmelding configureren](#configure-blackboard-learn---shibboleth-single-sign-on)**: als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[Testgebruiker maken in Blackboard Learn - Shibboleth](#create-blackboard-learn---shibboleth-test-user)**: als u een tegenhanger van Britta Simon in Blackboard Learn - Shibboleth wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voor het configureren van Azure AD-eenmalige aanmelding met Blackboard Learn - Shibboleth moet u de volgende stappen uitvoeren:

1. Selecteer in de [Azure-portal](https://portal.azure.com/), op de pagina voor de integratie van de toepassing **Blackboard Learn - Shibboleth**, **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Domein- en URL-gegevens voor eenmalige aanmelding bij Blackboard Learn - Shibboleth](common/sp-identifier-reply.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/Login`

    b. In het tekstvak **Id** typt u een URL met het volgende patroon: `https://<yourblackoardlearnserver>.blackboardlearn.com/shibboleth-sp`

    c. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<yourblackoardlearnserver>.blackboardlearn.com/Shibboleth.sso/SAML2/POST`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id, de antwoord-URL en de aanmeldings-URL. Neem contact op met het [ondersteuningsteam van Blackboard Learn - Shibboleth](https://www.blackboard.com/forms/contact-us_form.aspx) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

5. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

6. Kopieer in de sectie **Blackboard Learn - Shibboleth instellen** de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-blackboard-learn---shibboleth-single-sign-on"></a>Blackboard Learn - Shibboleth voor eenmalige aanmelding configureren

Als u eenmalige aanmelding aan de zijde van **Blackboard Learn - Shibboleth** wilt configureren, moet u het gedownloade **XML-bestand met federatieve metagegevens** en de juiste uit de Azure-portal gekopieerde URL's verzenden naar het [ondersteuningsteam van Blackboard Learn - Shibboleth](https://www.blackboard.com/forms/contact-us_form.aspx). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon**in.
  
    b. In het veld **Gebruikersnaam** typt u **brittasimon\@yourcompanydomain.extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot Blackboard Learn - Shibboleth.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen**, selecteer **Alle toepassingen** en selecteer vervolgens **Blackboard Learn - Shibboleth**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **Blackboard Learn - Shibboleth** in de lijst met toepassingen.

    ![De koppeling Blackboard Learn - Shibboleth in de lijst met toepassingen](common/all-applications.png)

3. Selecteer in het menu aan de linkerkant **Gebruikers en groepen**.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-blackboard-learn---shibboleth-test-user"></a>Testgebruiker maken in Blackboard Learn - Shibboleth

In deze sectie gaat u in Blackboard Learn - Shibboleth een gebruiker maken met de naam Britta Simon. Werk samen met het  [ondersteuningsteam van Blackboard Learn - Shibboleth](https://www.blackboard.com/forms/contact-us_form.aspx) om de gebruikers toe te voegen in het Blackboard Learn - Shibboleth-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Blackboard Learn - Shibboleth in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van Blackboard Learn - Shibboleth waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
