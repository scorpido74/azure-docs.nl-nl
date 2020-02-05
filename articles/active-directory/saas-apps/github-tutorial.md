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
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ddc2622a67979f989c030d615c402387e7762d5
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76983466"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-github"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met GitHub

In deze zelf studie leert u hoe u GitHub integreert met Azure Active Directory (Azure AD). Wanneer u GitHub integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot GitHub.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij GitHub met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

Als u Azure AD-integratie met GitHub wilt configureren, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen Azure AD-omgeving hebt, kunt u [hier](https://azure.microsoft.com/pricing/free-trial/) de proefversie van één maand krijgen.
* Een GitHub-organisatie, gemaakt in [GitHub Enterprise Cloud](https://help.github.com/articles/github-s-products/#github-enterprise), waarvoor een [GitHub Enterprise-abonnement](https://help.github.com/articles/github-s-billing-plans/#billing-plans-for-organizations) vereist is

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* GitHub ondersteunt door **SP** geïnitieerde eenmalige aanmelding

* GitHub ondersteunt het [**geautomatiseerd** inrichten van gebruikers](github-provisioning-tutorial.md)
* Nadat u GitHub hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie beheer is uitgebreid met voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-github-from-the-gallery"></a>GitHub toevoegen vanuit de galerie

Voor het configureren van de integratie van GitHub in Azure AD, moet u GitHub vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **github** in het zoekvak.
1. Selecteer **github** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-github"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor GitHub

Azure AD SSO met GitHub configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in GitHub.

Als u Azure AD SSO wilt configureren en testen met GitHub, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[GITHUB SSO configureren](#configure-github-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een github-test gebruiker](#create-github-test-user)** -om een equivalent van B. Simon in github te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **github** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

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

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan GitHub.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **GitHub** in de lijst met toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

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

    ![installatiekopie](./media/github-tutorial/tutorial_github_sha.png)

5. Klik op **Test SAML configuration** om te controleren of er geen validatiefouten of -fouten tijdens eenmalige aanmelding zijn opgetreden.

    ![Instellingen](./media/github-tutorial/tutorial_github_config_github_06.png)

6. Klik op **Opslaan**.

> [!NOTE]
> Eenmalige aanmelding in GitHub wordt geverifieerd met een specifieke organisatie in GitHub en is geen vervanging voor de verificatie van GitHub zelf. Dus als de gebruikerssessie op github.com is verlopen, kunt u worden gevraagd om u te verifiëren met de GitHub-id en het GitHub-wachtwoord tijdens het proces voor eenmalige aanmelding.

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

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel GitHub in het toegangsvenster klikt, wordt u automatisch aangemeld bij de instantie van GitHub waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer GitHub met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)