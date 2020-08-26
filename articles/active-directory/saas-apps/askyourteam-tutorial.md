---
title: 'Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met AskYourTeam | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en AskYourTeam.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: jeedes
ms.openlocfilehash: ad68208f8993ce84c71c97d97de7e2b090f1e7da
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549412"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askyourteam"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met AskYourTeam

In deze zelfstudie leert u hoe u AskYourTeam integreert met Azure Active Directory (Azure AD). Wanneer u AskYourTeam integreert met Azure AD, kunt u het volgende:

* In Azure AD bepalen wie er toegang heeft tot AskYourTeam.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij AskYourTeam.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een abonnement op AskYourTeam waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* AskYourTeam ondersteunt door **SPen IDP** geïnitieerde eenmalige aanmelding.
* Zodra u AskYourTeam hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-askyourteam-from-the-gallery"></a>AskYourTeam toevoegen vanuit de galerie

Om de integratie van AskYourTeam in Azure AD te configureren, moet u AskYourTeam vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen uit de galerie** **AskYourTeam** in het zoekvak.
1. Selecteer **AskYourTeam** in de resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-askyourteam"></a>Eenmalige aanmelding van Azure AD configureren en testen voor AskYourTeam

Configureer en test eenmalige aanmelding van Azure AD met AskYourTeam met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in AskYourTeam.

Voltooi de volgende stappen om eenmalige aanmelding van Azure AD met AskYourTeam te configureren en te testen:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding configureren voor AskYourTeam](#configure-askyourteam-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Een testgebruiker voor AskYourTeam maken](#create-askyourteam-test-user)** : als u een equivalent van B.Simon in AskYourTeam wilt hebben dat is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **AskYourTeam** de sectie **Beheren** en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: `https://<COMPANY>.app.askyourteam.com/users/auth/saml/callback`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<COMPANY>.app.askyourteam.com/login`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de daadwerkelijke antwoord-URL en aanmeldings-URL. Dit wordt verderop in de zelfstudie nog uitgelegd.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** gaat u naar **Certificaat (Base64)** en selecteert u **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. In de sectie **AskYourTeam instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot AskYourTeam.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **AskYourTeam** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-askyourteam-sso"></a>Eenmalige aanmelding configureren voor AskYourTeam

1. Meld u in een ander browservenster als beheerder aan bij de website van AskYourTeam.

1. Klik op **My Organisation**.

    ![Configuratie van AskYourTeam](./media/askyourteam-tutorial/user1.png)

1. Klik op **Integrations**.

    ![Configuratie van AskYourTeam](./media/askyourteam-tutorial/configure1.png)

1. Klik op **Edit Settings**.

    ![Configuratie van AskYourTeam](./media/askyourteam-tutorial/configure2.png)

1. Voer op de pagina **Edit Single Sign-On Integration** de volgende stappen uit: 

    ![Configuratie van AskYourTeam](./media/askyourteam-tutorial/configure3.png)

    a. Plak in het tekstvak **SAML Single Sign-On Service URL** de waarde van de **aanmeldings-URL** die u hebt gekopieerd uit de Azure-portal.

    b. Plak in het tekstvak **SAML Entity ID** de waarde van de **Azure AD-id** die u uit de Azure-portal hebt gekopieerd.

    c. Plak in het tekstvak **Sign-Out URL** de waarde van de **afmeldings-URL** die u uit de Azure-portal hebt gekopieerd.

    d. Open in Kladblok het **certificaat (Base64)** dat u uit de Azure-portal hebt gedownload en plak de inhoud in het tekstvak **SAML Signing Certificate - Base64**.

    > [!NOTE]
    > U kunt ook het **XML-bestand met federatieve metagegevens** uploaden door op de optie **Choose File** te klikken.

    e. Kopieer de waarde van **Reply URL (Assertion Consumer Service URL)** en plak deze in het tekstvak **Antwoord-URL** in de sectie **Standaard SAML-configuratie** van de Azure-portal.

    f. Kopieer de waarde van **Sign on URL** en plak deze waarde in het tekstvak **Aanmeldings-URL** in de sectie **Standaard SAML-configuratie** van de Azure-portal.

    g. Klik op **Opslaan**.

### <a name="create-askyourteam-test-user"></a>Testgebruiker voor AskYourTeam maken

1. Meld u in een ander browservenster als beheerder aan bij de website van AskYourTeam.

1. Klik op **My Organisation**.

    ![Configuratie van AskYourTeam](./media/askyourteam-tutorial/user1.png)

1. Klik op **Users** en selecteer **New User**.

    ![Configuratie van AskYourTeam](./media/askyourteam-tutorial/user2.png)

1. Voer in het gedeelte **New user** de volgende stappen uit:

    ![Configuratie van AskYourTeam](./media/askyourteam-tutorial/user3.png)

    1. Voer in het vak **First name** de voornaam van de gebruiker in.

    1. Voer in het vak **Last name** de achternaam van de gebruiker in.

    1. Voer in het tekstvak **Email** het e-mailadres van de gebruiker in, bijvoorbeeld B.Simon@contoso.com.

    1. Selecteer bij **Role** de rol van de gebruiker volgens uw organisatievereiste.

    1. Klik op **Opslaan**.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel AskYourTeam klikt, zou u automatisch moeten worden aangemeld bij de instantie van AskYourTeam waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [AskYourTeam uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
