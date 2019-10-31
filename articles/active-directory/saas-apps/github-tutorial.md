---
title: 'Zelf studie: integratie Azure Active Directory met GitHub | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en GitHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8761f5ca-c57c-4a7e-bf14-ac0421bd3b5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: af5021d3a8bed48003f38f01c3d61eac4bdd96b0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/30/2019
ms.locfileid: "73159216"
---
# <a name="tutorial-azure-active-directory-integration-with-github"></a>Zelf studie: integratie Azure Active Directory met GitHub

In deze zelfstudie leert u hoe u GitHub kunt integreren met Azure Active Directory (Azure AD).
Als u GitHub integreert met Azure Active Directory biedt dit de volgende voordelen:

* U kunt in Azure AD bepalen wie toegang heeft tot GitHub.
* U kunt inschakelen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij GitHub (eenmalige aanmelding).
* U kunt uw accounts vanaf één centrale locatie beheren: de Azure-portal.

Zie [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?) als u wilt graag meer wilt weten over de integratie van SaaS-apps met Azure AD.
Als u geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met GitHub wilt configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een GitHub-organisatie, gemaakt in [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise), waarvoor een [GitHub Enterprise-abonnement](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations) vereist is

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* GitHub ondersteunt door **SP** geïnitieerde eenmalige aanmelding

* GitHub ondersteunt het [**geautomatiseerd** inrichten van gebruikers](github-provisioning-tutorial.md)

## <a name="adding-github-from-the-gallery"></a>GitHub toevoegen vanuit de galerie

Voor het configureren van de integratie van GitHub in Azure AD, moet u GitHub vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

**Als u GitHub wilt toevoegen vanuit de galerie, moet u de volgende stappen uitvoeren:**

1. Klik in het linkernavigatievenster in de  **[Azure-portal](https://portal.azure.com)** op het **Azure Active Directory**-pictogram.

    ![De knop Azure Active Directory](common/select-azuread.png)

2. Navigeer naar **Bedrijfstoepassingen** en selecteer vervolgens de optie **Alle toepassingen**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

3. Als u de nieuwe toepassing wilt toevoegen, klikt u op de knop **Nieuwe toepassing** boven aan het dialoogvenster.

    ![De knop Nieuwe toepassing](common/add-new-app.png)

4. Typ **github**in het zoekvak, selecteer **github.com** in het resultaten paneel en klik vervolgens op de knop **toevoegen** om de toepassing toe te voegen.

     ![GitHub in de lijst met resultaten](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Azure AD-eenmalige aanmelding configureren en testen

In deze sectie configureert en test u eenmalige aanmelding van Azure AD met GitHub op basis van een testgebruiker met de naam **Britta Simon**.
Eenmalige aanmelding werkt alleen als er een koppelingsrelatie tussen een Azure AD-gebruiker en de daaraan gerelateerde gebruiker in GitHub tot stand is gebracht.

Als u eenmalige aanmelding van Azure AD met GitHub wilt configureren en testen, dient u de volgende bouwstenen te voltooien:

1. **[Azure AD-eenmalige aanmelding configureren](#configure-azure-ad-single-sign-on)** : als u wilt dat uw gebruikers deze functie kunnen gebruiken.
2. **[GitHub-eenmalige aanmelding configureren](#configure-github-single-sign-on)** : als u de instellingen voor eenmalige aanmelding aan de clientzijde wil configureren.
3. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : als u Azure AD-eenmalige aanmelding wil testen met Britta Simon.
4. **[De testgebruiker van Azure AD-toewijzen](#assign-the-azure-ad-test-user)** : als u wilt dat Britta Simon gebruik kan maken van Azure AD-eenmalige aanmelding.
5. **[GitHub-testgebruiker maken](#create-github-test-user)** : als u een equivalent van Britta Simon in GitHub wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
6. **[Eenmalige aanmelding testen](#test-single-sign-on)** : als u wilt controleren of de configuratie werkt.

### <a name="configure-azure-ad-single-sign-on"></a>Azure AD configureren voor eenmalige aanmelding

In deze sectie gaat u Azure AD-eenmalige aanmelding in de Azure-portal inschakelen.

Voer de volgende stappen uit als u eenmalige aanmelding van Azure AD met GitHub wilt configureren:

1. In de [Azure-portal](https://portal.azure.com/), op de pagina voor integratie van toepassingen met **GitHub**, selecteert u **Eenmalige aanmelding**.

    ![Koppeling Eenmalige aanmelding configureren](common/select-sso.png)

2. In het dialoogvenster **Een methode voor eenmalige aanmelding selecteren** selecteert u de modus **SAML/WS-Federation** om eenmalige aanmelding in te schakelen.

    ![De modus Eenmalige aanmelding selecteren](common/select-saml-option.png)

3. Op de pagina **Eenmalige aanmelding met SAML instellen** klikt u op het pictogram **Bewerken** om het dialoogvenster **Standaard SAML-configuratie** te openen.

    ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    ![Informatie over eenmalige aanmelding voor domeinen en URL's van GitHub](common/sp-identifier.png)

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://github.com/orgs/<entity-id>/sso`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Houd er rekening mee dat dit niet de werkelijke waarden zijn. U dient deze waarden bij te werken met de werkelijke aanmeldings-URL en -id. Wij raden u aan hiervoor de unieke waarde van de tekenreeks in de id te gebruiken. Ga naar de sectie GitHub-beheerder om de waarden op te halen.

5. In de GitHub-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. In de GitHub-toepassing wordt verwacht dat **nameidentifier** is toegewezen aan **user.mail**. Daarom moet u de kenmerktoewijzing bewerken door op het pictogram **Bewerken** te klikken en de kenmerktoewijzing te wijzigen.

    ![image](common/edit-attribute.png)

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. In het gedeelte **GitHub instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="configure-github-single-sign-on"></a>Eenmalige aanmelding van GitHub configureren

1. Meld u in een ander venster van de webbrowser als beheerder aan bij de site van uw GitHub-organisatie.

2. Ga naar **Settings** en klik op **Security**

    ![Instellingen](./media/github-tutorial/tutorial_github_config_github_03.png)

3. Schakel het selectievakje **Enable SAML authentication** in. De velden voor het configureren van eenmalige aanmelding worden zichtbaar. Gebruik vervolgens de URL-waarde voor eenmalige aanmelding om de URL voor eenmalige aanmelding in Azure AD-configuratie te werken.

    ![Instellingen](./media/github-tutorial/tutorial_github_config_github_13.png)

4. Configureer de volgende velden:

    ![Instellingen](./media/github-tutorial/tutorial_github_config_github_051.png)

    a. Plak in het tekstvak **Sign on URL** de waarde van de **aanmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    b. Plak in het tekstvak **Issuer** de waarde van de **Azure AD-id** die u uit Azure Portal hebt gekopieerd.

    c. Open in de Azure-portal het gedownloade certificaat in kladblok en plak de inhoud in het tekstvak **Public Certificate**.

    d. Klik op het pictogram **Edit** om de **handtekeningmethode** en **samenvattingsmethode** vanuit **RSA-SHA1** en **SHA1** te bewerken tot **RSA-SHA256** en **SHA256**, zoals hieronder aangegeven.

    ![image](./media/github-tutorial/tutorial_github_sha.png)

5. Klik op **Test SAML configuration** om te controleren of er geen validatiefouten of -fouten tijdens eenmalige aanmelding zijn opgetreden.

    ![Instellingen](./media/github-tutorial/tutorial_github_config_github_06.png)

6. Klik op **Opslaan**.

> [!NOTE]
> Eenmalige aanmelding in GitHub wordt geverifieerd met een specifieke organisatie in GitHub en is geen vervanging voor de verificatie van GitHub zelf. Dus als de gebruikerssessie op github.com is verlopen, kunt u worden gevraagd om u te verifiëren met de GitHub-id en het GitHub-wachtwoord tijdens het proces voor eenmalige aanmelding.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken 

Het doel van deze sectie is om in de Azure-portal een testgebruiker met de naam Britta Simon te maken.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.

    ![De koppelingen Gebruikers en groepen en Alle gebruikers](common/users.png)

2. Selecteer **Nieuwe gebruiker** boven aan het scherm.

    ![Knop Nieuwe gebruiker](common/new-user.png)

3. In Gebruikerseigenschappen voert u de volgende stappen uit.

    ![Het dialoogvenster Gebruiker](common/user-properties.png)

    a. Voer in het veld **Naam** **Britta Simon**in.
  
    b. Typ in het veld **gebruikers naam** **brittasimon\@yourcompanydomain. extension**  
    Bijvoorbeeld: BrittaSimon@contoso.com

    c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak Wachtwoord.

    d. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In dit gedeelte gaat u Britta Simon toestemming geven voor gebruik van eenmalige aanmelding met Azure door haar toegang te geven tot GitHub.

1. Selecteer **Bedrijfstoepassingen** in de Azure-portal, selecteer **Alle toepassingen** en selecteer vervolgens **GitHub**.

    ![De blade Bedrijfstoepassingen](common/enterprise-applications.png)

2. Selecteer **GitHub** in de lijst met toepassingen.

    ![De GitHub-koppeling in de lijst Toepassingen](common/all-applications.png)

3. Selecteer **Gebruikers en groepen** in het menu aan de linkerkant.

    ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

4. Klik op de knop**Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Het deelvenster Toewijzing toevoegen](common/add-assign-user.png)

5. Selecteer in het dialoogvenster **Gebruikers en groepen** **Britta Simon** in de lijst met gebruikers en klik op de knop **Selecteren** onder aan het scherm.

6. Als u een waarde voor een rol verwacht in de SAML-bewering, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren en vervolgens op de knop **Selecteren** onder aan het scherm klikken.

7. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

### <a name="create-github-test-user"></a>GitHub-testgebruiker maken

Het doel van deze sectie is om in GitHub een gebruiker te maken met de naam Britta Simon. GitHub ondersteunt het automatisch inrichten van gebruikers, wat standaard is ingeschakeld. U kunt [hier](github-provisioning-tutorial.md) meer informatie vinden over het configureren van het automatisch inrichten van gebruikers.

**Als u de gebruiker handmatig moet maken, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij de bedrijfssite van GitHub.

2. Klik op **People**.

    ![Iedereen](./media/github-tutorial/tutorial_github_config_github_08.png "Personen")

3. Klik op **Invite member**.

    ![Gebruikers uitnodigen](./media/github-tutorial/tutorial_github_config_github_09.png "Gebruikers uitnodigen")

4. Voer in het dialoogvenster **Invite member** de volgende stappen uit:

    a. Typ in het tekstvak **Email** het e-mailadres van het account van Britta Simon.

    ![Personen uitnodigen](./media/github-tutorial/tutorial_github_config_github_10.png "Personen uitnodigen")

    b. Klik op **Send Invitation**.

    ![Personen uitnodigen](./media/github-tutorial/tutorial_github_config_github_11.png "Personen uitnodigen")

    > [!NOTE]
    > De houder van het Azure Active Directory-account ontvangt een e-mail en volgt een koppeling om zijn account te bevestigen voordat het actief wordt.

### <a name="test-single-sign-on"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel GitHub in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van GitHub waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
