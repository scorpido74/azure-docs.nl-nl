---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met LinkedIn Learning | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en LinkedIn Learning.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: d598017c8b41dffc486bfef4b9af63c6942cef58
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549786"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-linkedin-learning"></a>Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met LinkedIn Learning

In deze zelfstudie leert u hoe u LinkedIn Learning integreert met Azure Active Directory (Azure AD). Wanneer u LinkedIn Learning integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot LinkedIn Learning.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij LinkedIn Learning.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op LinkedIn Learning waarvoor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* LinkedIn Learning biedt ondersteuning voor door **SP en IDP** geïnitieerde eenmalige aanmelding
* LinkedIn Learning biedt ondersteuning voor **Just In Time**-gebruikersinrichting
* Zodra u LinkedIn Learning hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-linkedin-learning-from-the-gallery"></a>LinkedIn Learning vanuit de galerie toevoegen

Als u de integratie van LinkedIn Learning met Azure AD wilt configureren, moet u LinkedIn Learning vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** in het zoekvak: **LinkedIn Learning**.
1. Selecteer **LinkedIn Learning** in het resultatenvenster en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-linkedin-learning"></a>Eenmalige aanmelding van Azure AD configureren en testen voor LinkedIn Learning

Configureer en test eenmalige aanmelding van Azure AD met LinkedIn Learning met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in LinkedIn Learning.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met LinkedIn Learning te configureren en testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding voor LinkedIn Learning configureren](#configure-linkedin-learning-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. **[Testgebruiker voor LinkedIn Learning maken](#create-linkedin-learning-test-user)** : als u een tegenhanger van Britta Simon in LinkedIn Learning wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **LinkedIn Learning** naar de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

     a. Voer in het tekstvak **id** de **Entiteits-id** in die u hebt gekopieerd uit LinkedIn Portal. 

    b. Voer in het tekstvak **Antwoord-URL** de **ACS-URL (Assertion Consumer Service)** in die u uit LinkedIn Portal hebt gekopieerd.

    c. Als u de toepassing in de **SP geïnitieerde** modus wilt configureren, klikt u in het gedeelte **Standaard SAML-configuratie** waarin u de aanmeldings-URL opgeeft op **Extra URL's instellen**. Kopieer de **ACS-URL (Assertion Consumer Service)** en vervang /saml/ door /login/ om een kopie van de aanmeldings-URL te maken. Zodra dit is gebeurd, zou de aanmeldings-URL het volgende patroon moeten hebben:

    `https://www.linkedin.com/checkpoint/enterprise/login/<AccountId>?application=learning&applicationInstanceId=<InstanceId>`

    ![Informatie over eenmalige aanmelding voor LinkedIn Learning-domein en -URL's](common/metadata-upload-additional-signon.png)

    > [!NOTE]
    > Deze waarden zijn geen werkelijke waarden. U gaat deze waarden bijwerken met de werkelijke id en de antwoord-URL. Dit wordt later uitgelegd in de sectie **Eenmalige aanmelding voor LinkedIn Learning configureren** van deze zelfstudie.

1. In de LinkedIn Learning-toepassing worden de SAML-asserties in een specifieke indeling verwacht. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. In de toepassing LinkedIn Learning wordt verwacht dat **nameidentifier** is toegewezen met **user.mail**. Daarom moet u de toewijzing van het kenmerk bewerken door op het pictogram **Bewerken** te klikken en de toewijzing van het kenmerk te wijzigen.

    ![image](common/edit-attribute.png)

1. Ga op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** naar **XML-bestand met federatieve metagegevens** en selecteer **Downloaden** om het certificaat te downloaden. Sla dit vervolgens op de computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. In de sectie **LinkedIn Learning instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in Azure Portal.

1. Selecteer in het linkerdeelvenster van Azure Portal de optie **Azure Active Directory**, selecteer **Gebruikers** en selecteer vervolgens **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u B.Simon in staat gebruik te maken van eenmalige aanmelding via Azure door haar toegang te geven tot LinkedIn Learning.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **LinkedIn Learning** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-linkedin-learning-sso"></a>Eenmalige aanmelding voor LinkedIn Learning configureren

1. Meld u in een ander browservenster als beheerder aan bij uw LinkedIn Learning-tenant.

2. Klik in **Accountcentrum** onder **Instellingen** op **Algemene instellingen**. Selecteer ook **Learning - standaard** uit de vervolgkeuzelijst.

    ![Eenmalige aanmelding configureren](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_01.png)

3. Klik op **Of klik hier om afzonderlijke velden van het formulier te laden en kopiëren** en kopieer **Entiteits-id** en **ACS-URL (Assertion Consumer Service)** en plak deze in het gedeelte **Standaard SAML-configuratie** in Azure Portal.

    ![Eenmalige aanmelding configureren](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_03.png)

4. Ga naar het gedeelte **met beheerdersinstellingen voor LinkedIn**. Upload het XML-bestand dat u hebt gedownload uit Azure Portal door op de optie **XML-bestand uploaden** te klikken.

    ![Eenmalige aanmelding configureren](./media/linkedinlearning-tutorial/tutorial_linkedin_metadata_03.png)

5. Klik op **Aan** om SSO in te schakelen. De SSO-status verandert van **Niet verbonden** naar **Verbonden**

    ![Eenmalige aanmelding configureren](./media/linkedinlearning-tutorial/tutorial_linkedin_admin_05.png)

### <a name="create-linkedin-learning-test-user"></a>Testgebruiker voor LinkedIn Learning maken

De LinkedIn Learning-toepassing biedt ondersteuning voor Just in time-gebruikersinrichting. Na verificatie worden gebruikers automatisch aangemaakt in de toepassing. Ga naar het tabblad met beheerdersinstellingen in de LinkedIn Learning-portal en zet de schakelaar voor **automatisch toewijzen van licenties** op actieve Just in time-inrichting. Hiermee wordt ook een licentie aan de gebruiker toegewezen.

   ![Een Azure AD-testgebruiker maken](./media/linkedinlearning-tutorial/LinkedinUserprovswitch.png)

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel LinkedIn Learning in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van LinkedIn Learning waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [LinkedIn Learning met Azure AD uitproberen](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)