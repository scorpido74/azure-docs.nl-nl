---
title: 'Zelfstudie: Azure Active Directory-integratie met Saba TalentSpace | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Saba TalentSpace.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2ca2298d-9a0c-4f14-925c-fa23f2659d28
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/20/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 76c13a649e1c1888a11e02b83d969255615cdc67
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "77561328"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-saba-talentspace"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Saba TalentSpace

In deze zelfstudie leert u hoe u Saba TalentSpace integreren met Azure Active Directory (Azure AD). Wanneer u Saba TalentSpace integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Saba TalentSpace.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Saba TalentSpace met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Saba TalentSpace single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Saba TalentSpace ondersteunt **SP** geïnitieerde SSO
* Zodra u Saba TalentSpace hebt geconfigureerd, u sessiecontrole afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebeheer strekt zich uit van Voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-saba-talentspace-from-the-gallery"></a>Saba TalentSpace toevoegen vanuit de galerie

Als u de integratie van Saba TalentSpace in Azure AD wilt configureren, moet u Saba TalentSpace vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Saba TalentSpace** in de sectie **Toevoegen in de galerijsectie** in het zoekvak.
1. Selecteer **Saba TalentSpace** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-saba-talentspace"></a>Azure AD-aanmelding voor Saba TalentSpace configureren en testen

Azure AD SSO configureren en testen met Saba TalentSpace met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Saba TalentSpace.

Als u Azure AD SSO wilt configureren en testen met Saba TalentSpace, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Saba TalentSpace SSO configureren](#configure-saba-talentspace-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Saba TalentSpace-testgebruiker maken](#create-saba-talentspace-test-user)** - om een tegenhanger van B.Simon in Saba TalentSpace te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Saba TalentSpace-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **Standaard SAML-configuratie** voert u de volgende stappen uit:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://global.hgncloud.com/[companyname]/saml/login`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://global.hgncloud.com/[companyname]/saml/metadata`

    c. Typ in het tekstvak **Antwoord-URL (Bevestigingsconsumentenservice)** een URL met het volgende patroon:`https://global.hgncloud.com/[companyname]/saml/SSO`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en -id. Neem contact op met [het ondersteuningsteam van Saba TalentSpace Client](https://support.saba.com/) om deze waarden te krijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** klikt u op **Downloaden** om het **XML-bestand met federatieve metagegevens** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer in de sectie **Saba TalentSpace instellen** de juiste URL(s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Saba TalentSpace.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **Saba TalentSpace**in de lijst met toepassingen .
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-saba-talentspace-sso"></a>Saba TalentSpace SSO configureren

1. Meld je in een ander browservenster aan bij je **Saba TalentSpace-toepassing** als beheerder.

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
    > Je moet wachten op het bericht "*De SAML test is voltooid. Sluit dit raam*". Sluit vervolgens het geopende browservenster. Het selectievakje **SAML inschakelen** is pas beschikbaar als de test is voltooid.

    e. Selecteer **SAML inschakelen**.

    f. Klik **op Wijzigingen opslaan**.

### <a name="create-saba-talentspace-test-user"></a>Saba TalentSpace-testgebruiker maken

Het doel van deze sectie is het creëren van een gebruiker genaamd Britta Simon in Saba TalentSpace.

**Voer de volgende stappen uit om een gebruiker genaamd Britta Simon in Saba TalentSpace te maken:**

1. Meld u aan bij uw **Saba TalentSpace-toepassing** als beheerder.

2. Klik op het tabblad **User Center** tabblad en klik vervolgens op **Create User**.

    ![Wat is Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-300.png)  

3. Voer de volgende stappen uit in het dialoogvenster **New User**:

    ![Wat is Azure AD Connect?](./media/halogen-software-tutorial/tutorial-halogen-301.png)

    a. Typ in het tekstvak **Voornaam** de voornaam van de gebruiker zoals **B**.

    b. Typ in het tekstvak **Achternaam** de achternaam van de gebruiker zoals **Simon**.

    c. Typ **B.Simon**in het tekstvak **Gebruikersnaam,** de gebruikersnaam zoals in de Azure-portal.

    d. Typ in het tekstvak **Wachtwoord** een wachtwoord voor B.Simon.

    e. Klik op **Opslaan**.

## <a name="test-sso"></a>Test SSO

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de Saba TalentSpace-tegel in het toegangspaneel klikt, moet u automatisch worden aangemeld bij de Saba TalentSpace waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Saba TalentSpace met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)