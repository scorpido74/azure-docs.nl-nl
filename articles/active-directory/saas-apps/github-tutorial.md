---
title: 'Zelfstudie: Azure Active Directory-integratie met GitHub | Microsoft Documenten'
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
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e812e1b03637a3ecd7a45f02664c4e3547f1aef1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "79138974"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met GitHub

In deze zelfstudie leert u hoe u GitHub integreren met Azure Active Directory (Azure AD). Wanneer u GitHub integreert met Azure AD, u het als:

* Beheer in Azure AD wie toegang heeft tot uw GitHub Enterprise Cloud Organization.
* Beheer de toegang tot uw GitHub Enterprise Cloud Organization op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met GitHub wilt configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een GitHub-organisatie, gemaakt in [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise), waarvoor een [GitHub Enterprise-abonnement](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations) vereist is

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* GitHub ondersteunt door **SP** geïnitieerde eenmalige aanmelding

* GitHub ondersteunt [ **geautomatiseerde** gebruikersinrichting (uitnodigingen voor organisaties)](github-provisioning-tutorial.md)
* Zodra u GitHub hebt geconfigureerd, u sessiebeheer afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebeheer strekt zich uit van Voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-github-from-the-gallery"></a>GitHub toevoegen vanuit de galerie

Voor het configureren van de integratie van GitHub in Azure AD, moet u GitHub vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **GitHub** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **GitHub** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-github"></a>Azure AD-eenmaligaanmelding voor GitHub configureren en testen

Azure AD SSO configureren en testen met GitHub met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in GitHub.

Als u Azure AD SSO wilt configureren en testen met GitHub, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer GitHub SSO](#configure-github-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Maak GitHub-testgebruiker](#create-github-test-user)** - om een tegenhanger van B.Simon in GitHub te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **GitHub-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

   a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://github.com/orgs/<entity-id>/sso`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://github.com/orgs/<entity-id>`

    > [!NOTE]
    > Houd er rekening mee dat dit niet de werkelijke waarden zijn. U dient deze waarden bij te werken met de werkelijke aanmeldings-URL en -id. Wij raden u aan hiervoor de unieke waarde van de tekenreeks in de id te gebruiken. Ga naar de sectie GitHub-beheerder om de waarden op te halen.

5. In de GitHub-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. In de GitHub-toepassing wordt verwacht dat **nameidentifier** is toegewezen aan **user.mail**. Daarom moet u de kenmerktoewijzing bewerken door op het pictogram **Bewerken** te klikken en de kenmerktoewijzing te wijzigen.

    ![installatiekopie](common/edit-attribute.png)

6. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **Certificaat (Base64)** te downloaden uit de opgegeven opties overeenkomstig uw behoeften, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

7. In het gedeelte **GitHub instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot GitHub.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **GitHub** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-github-sso"></a>GitHub SSO configureren

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
    
    e. Werk de URL van de **bevestigingsservice (Reply URL)** bij vanaf de standaard-URL, zodat de URL in Github overeenkomt met de URL in de Azure-appregistratie.

    ![installatiekopie](./media/github-tutorial/tutorial_github_sha.png)

5. Klik op **Test SAML configuration** om te controleren of er geen validatiefouten of -fouten tijdens eenmalige aanmelding zijn opgetreden.

    ![Instellingen](./media/github-tutorial/tutorial_github_config_github_06.png)

6. Klik **op Opslaan**

> [!NOTE]
> Eenmalige aanmelding in GitHub wordt geverifieerd met een specifieke organisatie in GitHub en is geen vervanging voor de verificatie van GitHub zelf. Dus als de gebruikerssessie op github.com is verlopen, kunt u worden gevraagd om u te verifiëren met de GitHub-id en het GitHub-wachtwoord tijdens het proces voor eenmalige aanmelding.

### <a name="create-github-test-user"></a>GitHub-testgebruiker maken

Het doel van deze sectie is om in GitHub een gebruiker te maken met de naam Britta Simon. GitHub ondersteunt het automatisch inrichten van gebruikers, wat standaard is ingeschakeld. U kunt [hier](github-provisioning-tutorial.md) meer informatie vinden over het configureren van het automatisch inrichten van gebruikers.

**Als u de gebruiker handmatig moet maken, voert u de volgende stappen uit:**

1. Meld u als beheerder aan bij de bedrijfssite van GitHub.

2. Klik op **People**.

    ![Mensen](./media/github-tutorial/tutorial_github_config_github_08.png "People")

3. Klik op **Invite member**.

    ![Gebruikers uitnodigen](./media/github-tutorial/tutorial_github_config_github_09.png "Gebruikers uitnodigen")

4. Voer in het dialoogvenster **Invite member** de volgende stappen uit:

    a. Typ in het tekstvak **Email** het e-mailadres van het account van Britta Simon.

    ![Mensen uitnodigen](./media/github-tutorial/tutorial_github_config_github_10.png "Mensen uitnodigen")

    b. Klik op **Send Invitation**.

    ![Mensen uitnodigen](./media/github-tutorial/tutorial_github_config_github_11.png "Mensen uitnodigen")

    > [!NOTE]
    > De houder van het Azure Active Directory-account ontvangt een e-mail en volgt een koppeling om zijn account te bevestigen voordat het actief wordt.

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel GitHub in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van GitHub waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer GitHub met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
