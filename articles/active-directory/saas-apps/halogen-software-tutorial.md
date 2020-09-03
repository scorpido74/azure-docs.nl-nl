---
title: 'Zelfstudie: Azure Active Directory-integratie met Saba TalentSpace | Microsoft Docs'
description: Lees hoe u eenmalige aanmelding configureert tussen Microsoft Azure Active Directory en Saba TalentSpace.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 02/20/2020
ms.author: jeedes
ms.openlocfilehash: 1743908652a70667a368dced18b2e808ce590a1b
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88549990"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-talentspace"></a>Zelfstudie: Eenmalige aanmelding van Azure Active Directory integreren met Saba TalentSpace

In deze zelfstudie leert u hoe u Saba TalentSpace kunt integreren met Azure AD (Active Directory). Wanneer u Saba TalentSpace integreert met Azure AD, kunt u het volgende doen:

* In Azure AD beheren wie toegang heeft tot Saba TalentSpace.
* U kunt instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Saba TalentSpace.
* Uw accounts op een centrale locatie beheren: Azure Portal.

Zie [Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt het volgende nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Saba TalentSpace-abonnement waarvoor eenmalige aanmelding is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Saba TalentSpace biedt ondersteuning voor met **SP** geïnitieerde SSO
* Nadat u Saba TalentSpace hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-saba-talentspace-from-the-gallery"></a>Saba TalentSpace toevoegen vanuit de galerie

Als u de integratie van Saba TalentSpace in Azure AD wilt configureren, moet u Saba TalentSpace vanuit de galerie toevoegen aan de lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de service **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Nieuwe toepassing** om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **Toevoegen vanuit de galerie** in het zoekvak: **Saba TalentSpace**.
1. Selecteer **Saba TalentSpace** in het resultatenpaneel en voeg de app vervolgens toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-saba-talentspace"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Saba TalentSpace

Configureer en test eenmalige aanmelding van Azure AD met Saba TalentSpace met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Saba TalentSpace.

Als u eenmalige aanmelding van Azure AD met Saba TalentSpace wilt configureren en testen, moet u de volgende bouwstenen voltooien:

1. **[Eenmalige aanmelding van Azure AD configureren](#configure-azure-ad-sso)** : zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** : om eenmalige aanmelding van Azure AD te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Eenmalige aanmelding met Saba TalentSpace configureren](#configure-saba-talentspace-sso)** : als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    * **[Een testgebruiker voor Saba TalentSpace maken](#create-saba-talentspace-test-user)** : als u een tegenhanger van B.Simon in Saba TalentSpace wilt hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Eenmalige aanmelding testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Ga in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Saba TalentSpace** naar de sectie **Beheren**, en selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Op de pagina **Eenmalige aanmelding instellen met SAML** klikt u op het bewerkings-/penpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://global.hgncloud.com/[companyname]/saml/login`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://global.hgncloud.com/[companyname]/saml/metadata`

    c. Typ in het tekstvak **Antwoord-URL (URL voor Assertion Consumer Service)** een URL met het volgende patroon: `https://global.hgncloud.com/[companyname]/saml/SSO`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [klantondersteuningsteam van Saba TalentSpace](https://support.saba.com/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens**  te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **Saba TalentSpace instellen** de juiste URL('s) op basis van uw behoeften.

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

In deze sectie stelt u B.Simon in staat gebruik te maken van eenmalige aanmelding van Azure door haar toegang te verlenen tot Saba TalentSpace.

1. Selecteer in Azure Portal de optie **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **Saba TalentSpace** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** de optie **B.Simon** in de lijst Gebruikers. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Als u een waarde voor een rol verwacht in de SAML-assertie, moet u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst selecteren. Klik vervolgens op de knop **Selecteren** onderaan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-saba-talentspace-sso"></a>Eenmalige aanmelding met Saba TalentSpace configureren

1. Meld u in een ander browservenster als beheerder aan bij de toepassing **Saba TalentSpace**.

2. Klik op het tabblad **Options**.
  
    ![Wat is Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-12.png)

3. Klik in het linkernavigatiedeelvenster op **SAML Configuration**.
  
    ![Wat is Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-13.png)

4. Voer de volgende stappen uit op de pagina **SAML Configuration**:

    ![Wat is Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-14.png)

    a. Selecteer bij **Unique Identifier** de waarde **NameID**.

    b. Selecteer bij **Unique Identifier Maps To** de waarde **Username**.
  
    c. Klik op **Browse**, selecteer het gedownloade metagegevensbestand dat u wilt uploaden, en selecteer vervolgens **Upload File**.

    d. Klik op **Run Test** om de configuratie te testen.

    > [!NOTE]
    > Wacht totdat het bericht "*The SAML test is complete. Please close this window.* " wordt weergegeven. Sluit vervolgens het geopende browservenster. Het selectievakje **SAML inschakelen** is pas beschikbaar als de test is voltooid.

    e. Selecteer **SAML inschakelen**.

    f. Klik op **Wijzigingen opslaan**.

### <a name="create-saba-talentspace-test-user"></a>Een testgebruiker voor Saba TalentSpace maken

Het doel van deze sectie is om in Saba TalentSpace een gebruiker met de naam Britta Simon te maken.

**Voer de volgende stappen uit om in Saba TalentSpace een gebruiker met de naam Britta Simon te maken:**

1. Meld u als beheerder aan bij de toepassing **Saba TalentSpace**.

2. Klik op het tabblad **User Center** tabblad en klik vervolgens op **Create User**.

    ![Wat is Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-300.png)  

3. Voer de volgende stappen uit in het dialoogvenster **New User**:

    ![Wat is Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-301.png)

    a. Typ in het tekstvak **Voornaam** de voornaam van de gebruiker, zoals **B**.

    b. Typ in het tekstvak **Last Name** de achternaam van de gebruiker, bijvoorbeeld **Simon**.

    c. Typ in het tekstvak **Gebruikersnaam** **B.Simon**, de gebruikersnaam zoals weergegeven in de Azure-portal.

    d. Typ in het tekstvak **Wachtwoord** een wachtwoord voor B.Simon.

    e. Klik op **Opslaan**.

## <a name="test-sso"></a>Eenmalige aanmelding testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel Saba TalentSpace klikt, wordt u automatisch aangemeld bij de instantie van Saba TalentSpace waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Saba TalentSpace uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)