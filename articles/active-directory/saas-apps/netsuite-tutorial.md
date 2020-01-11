---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met NetSuite | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding tussen Azure Active Directory en NetSuite configureert.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/10/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad7065ba6378bcb383e67b4a58d7c195e88679ca
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/11/2020
ms.locfileid: "75890670"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-sso-with-netsuite"></a>Zelf studie: Azure AD-eenmalige aanmelding (SSO) integreren met NetSuite

In deze zelf studie leert u hoe u NetSuite integreert met Azure Active Directory (Azure AD). Wanneer u NetSuite integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Netsuite.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij NetSuite met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie, het Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Een abonnement voor eenmalige aanmelding via Netsuite (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving. 

Netsuite ondersteunt:

* Door IDP geïnitieerde SSO.
* Just-in-time-gebruikers inrichting met JIT-toewijzing.
* [Automatische gebruikers inrichting](NetSuite-provisioning-tutorial.md).

> [!NOTE]
> Omdat de id van deze toepassing een vaste teken reeks waarde is, kan slechts één exemplaar in één Tenant worden geconfigureerd.

## <a name="add-netsuite-from-the-gallery"></a>Netsuite toevoegen vanuit de galerie

Als u de integratie van NetSuite in azure AD wilt configureren, voegt u NetSuite vanuit de galerie toe aan uw lijst met beheerde SaaS-apps door het volgende te doen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het linkerdeel venster.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** de tekst **NetSuite** in het zoekvak.
1. Selecteer **NetSuite**in het resultaten venster en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor NetSuite

Azure AD SSO met NetSuite configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Netsuite.

Als u Azure AD SSO wilt configureren en testen met Netsuite, voltooit u de volgende bouw stenen:

1. [Configureer Azure AD SSO](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
    * [Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user) eenmalige aanmelding van Azure ad te testen met gebruiker B. Simon.  
    * [Wijs de gebruiker van Azure AD-test](#assign-the-azure-ad-test-user) toe om gebruiker B. Simon in te scha kelen voor het gebruik van eenmalige aanmelding voor Azure AD.
1. [Configureer de SSO van NetSuite](#configure-netsuite-sso) voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * [Maak de gebruiker van de groep voor het testen van de](#create-the-netsuite-test-user) groep gebruiker B. Simon in NetSuite die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. [Test SSO](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Ga als volgt te werk om Azure AD SSO in te scha kelen in de Azure Portal:

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **NetSuite** -toepassings integratie naar de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML** in het deelvenster **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer in het deel venster **eenmalige aanmelding met SAML instellen** het pictogram **bewerken** (potlood) naast **basis-SAML-configuratie**.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Typ in de sectie **basis-SAML-configuratie** in het tekstvak antwoord- **URL** een URL in een van de volgende indelingen:

    ||
    |-|
    | `https://<Account ID>.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na1.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na2.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.sandbox.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na1.sandbox.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na2.sandbox.NetSuite.com/saml2/acs`|

    > [!NOTE]
    > De waarden in de voor gaande Url's zijn niet echt. Werk deze bij met de daad werkelijke antwoord-URL. Neem contact op met het [ondersteunings team van de client voor NetSuite](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml)om de waarde op te halen. U kunt ook verwijzen naar de indelingen die worden weer gegeven in de sectie **basis configuratie van SAML** in de Azure Portal.

1. De toepassing NetSuite verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerk toewijzingen moet toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. In aanvulling op hierboven verwachtte NetSuite-toepassing nog maar weinig kenmerken om te worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    | Name | Bronkenmerk |
    | ---------------| --------------- |
    | account  | `account id` |

    > [!NOTE]
    > De waarde van het account kenmerk is niet reëel. U werkt deze waarde bij, zoals verderop in deze zelf studie wordt uitgelegd.

1. Zoek op de pagina eenmalige aanmelding met SAML instellen, in de sectie SAML-handtekening certificaat, de federatieve meta gegevens-XML en selecteer downloaden om het certificaat te downloaden en op uw computer op te slaan.

    ![De koppeling voor het downloaden van certificaten](common/metadataxml.png)

1. In de sectie **NetSuite instellen** kopieert u de juiste URL of url's, afhankelijk van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer **Azure Active Directory** > **gebruikers** > **alle gebruikers**In het linkerdeel venster van de Azure Portal.

1. Selecteer **Nieuwe gebruiker** boven aan het scherm.

1. Voer in het deel venster **gebruikers** eigenschappen de volgende stappen uit:

   a. Voer in het vak **naam** **B. Simon**in.  
   b. Voer in het vak **gebruikers naam** de username@companydomain.extension in (bijvoorbeeld B.Simon@contoso.com).  
   c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.  
   d. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u gebruiker B. Simon in voor het gebruik van eenmalige aanmelding van Azure door toegang te verlenen aan NetSuite.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **NetSuite**in de lijst toepassingen.
1. Zoek in het deel venster Overzicht naar de sectie **beheren** en selecteer vervolgens de koppeling **gebruikers en groepen** .

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen** en selecteer vervolgens in het deel venster **toewijzing toevoegen** de optie **gebruikers en groepen**.

    ![De knop gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het deel venster **gebruikers en groepen** in de vervolg keuzelijst **gebruikers** de optie **B. Simon**en selecteer vervolgens de knop **selecteren** onder aan het scherm.
1. Ga als volgt te werk als u een wille keurige rol in de SAML-bewering verwacht:

   a. Selecteer in het deel venster **rol selecteren** in de vervolg keuzelijst de juiste rol voor de gebruiker.  
   b. Selecteer aan de onderkant van het scherm de knop **selecteren** .
1. Selecteer de knop **toewijzen** in het deel venster **toewijzing toevoegen** .

## <a name="configure-netsuite-sso"></a>Netsuite-SSO configureren

1. Open een nieuw tabblad in uw browser en meld u als beheerder aan bij de bedrijfs site van uw bedrijf.

2. Selecteer in de bovenste navigatie balk de optie **instellen**en selecteer vervolgens **bedrijfs** > **functies inschakelen**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Selecteer in de werk balk aan het midden van de pagina **SuiteCloud**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-suitecloud.png)

4. Schakel onder **verificatie beheren**het selectie vakje **eenmalige SAML-aanmelding** in om de optie eenmalige aanmelding via SAML in te scha kelen in Netsuite.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Selecteer in de bovenste navigatie balk de optie **Setup**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-setup.png)

6. Selecteer in de lijst **installatie taken** de optie **integratie**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-integration.png)

7. Selecteer onder **verificatie beheren**de optie **eenmalige aanmelding voor SAML**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-saml.png)

8. Ga als volgt te werk in het configuratie venster van **SAML** onder **NetSuite configureren**:

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Selecteer het selectie vakje **primaire verificatie methode** .

    b. Selecteer onder **SAMLV2-meta gegevens van de identiteits provider**de optie **IDP meta gegevensbestand uploaden**en selecteer vervolgens **Bladeren** om het meta gegevensbestand te uploaden dat u hebt gedownload van de Azure Portal.

    c. Selecteer **Indienen**.

9. Selecteer in de bovenste navigatie balk van de NetSuite de optie **instellen**en selecteer vervolgens **bedrijfs** > **Bedrijfs gegevens**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-com.png)

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-account-id.png)

    b. Kopieer de waarde van de **account-id** in het deel venster **Bedrijfs gegevens** in de rechter kolom.

    c. Plak de **account-id** die u hebt gekopieerd uit het NetSuite-account in het vak **kenmerk waarde** in azure AD. 

10. Voordat gebruikers eenmalige aanmelding kunnen gebruiken met NetSuite moeten ze over de juiste machtigingen beschikken in NetSuite. Ga als volgt te werk om deze machtigingen toe te wijzen:

    a. Selecteer in de bovenste navigatie balk de optie **Setup**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-setup.png)

    b. Selecteer in het linkerdeel venster **gebruikers/rollen**en selecteer vervolgens **rollen beheren**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Selecteer **nieuwe rol**.

    d. Voer een **naam** in voor de nieuwe rol.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-new-role.png)

    e. Selecteer **Opslaan**.

    f. Selecteer in de bovenste navigatie balk de optie **machtigingen**. Selecteer vervolgens **Setup**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-sso.png)

    g. Selecteer **eenmalige aanmelding voor SAML**en selecteer vervolgens **toevoegen**.

    h. Selecteer **Opslaan**.

    i. Selecteer in de bovenste navigatie balk de optie **Setup**en selecteer vervolgens **installatie beheer**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-setup.png)

    j. Selecteer in het linkerdeel venster **gebruikers/rollen**en selecteer vervolgens **gebruikers beheren**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Selecteer een test gebruiker, selecteer **bewerken**en selecteer vervolgens het tabblad **toegang** .

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-edit-user.png)

    l. Wijs in het deel venster **rollen** de juiste rol toe die u hebt gemaakt.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-add-role.png)

    m. Selecteer **Opslaan**.

### <a name="create-the-netsuite-test-user"></a>De test gebruiker voor de NetSuite maken

In deze sectie wordt een gebruiker met de naam B. Simon gemaakt in Netsuite. NetSuite biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker bestaat in NetSuite, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>SSO testen

In dit gedeelte test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u de tegel NetSuite selecteert in het toegangs venster, moet u automatisch worden aangemeld bij de NetSuite waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)
- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Netsuite proberen met Azure AD](https://aad.portal.azure.com/)
