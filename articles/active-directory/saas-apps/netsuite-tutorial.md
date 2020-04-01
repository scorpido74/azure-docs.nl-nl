---
title: 'Zelfstudie: SSO-integratie (Azure Active Directory single sign-on) met NetSuite | Microsoft Documenten'
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
ms.date: 01/22/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6a920e58f1ffd4c3e3e9769bf6346100a8677b90
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76760046"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-sso-with-netsuite"></a>Zelfstudie: Azure AD single sign-on (SSO) integreren met NetSuite

In deze zelfstudie leert u hoe u NetSuite integreert met Azure Active Directory (Azure AD). Wanneer u NetSuite integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot NetSuite.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij NetSuite met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie, de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory voor](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)meer informatie over de integratie van de SaaS-app met Azure AD. .

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Een NetSuite-abonnement (SSO).A NetSuite single sign-on (SSO)-enabled.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving. 

NetSuite ondersteunt:

* IDP-geïnitieerde SSO.
* JIT (just-in-time) user provisioning.
* [Geautomatiseerde gebruikersinrichting](NetSuite-provisioning-tutorial.md).
* Zodra u de NetSuite hebt geconfigureerd, u sessiebesturingselementen afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebesturingselementen zijn van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> Omdat de id van deze toepassing een vaste tekenreekswaarde is, kan slechts één instantie in één tenant worden geconfigureerd.

## <a name="add-netsuite-from-the-gallery"></a>NetSuite toevoegen vanuit de galerie

Als u de integratie van NetSuite in Azure AD wilt configureren, voegt u NetSuite vanuit de galerie toe aan uw lijst met beheerde SaaS-apps:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkerdeelvenster de **Azure Active Directory-service.**
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **NetSuite** in het zoekvak in de sectie **Toevoegen in de galerie.**
1. Selecteer **NetSuite**in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>Azure AD-aanmelding voor NetSuite configureren en testen

Azure AD SSO configureren en testen met NetSuite met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in NetSuite.

Als u Azure AD SSO wilt configureren en testen met NetSuite, voert u de volgende bouwstenen in:

1. [Configureer Azure AD SSO](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
    * [Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user) om Azure AD-enkele aanmelding te testen met gebruiker B.Simon.  
    * [Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user) toe om gebruiker B.Simon in staat te stellen Azure AD-enkele aanmelding te gebruiken.
1. [Configureer NetSuite SSO](#configure-netsuite-sso) om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * [Maak de NetSuite-testgebruiker](#create-the-netsuite-test-user) om een tegenhanger van gebruiker B.Simon in NetSuite te hebben die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. [Test SSO](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Ga als volgt te werk om Azure AD SSO in te schakelen in de Azure-portal:

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **NetSuite-toepassingsintegratie** naar de sectie **Beheren** en selecteer **Vervolgens Eenmalige aanmelding**.
1. Selecteer **SAML** in het deelvenster **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer in het deelvenster **Eén aanmelding instellen met SAML** het pictogram **Bewerken** ('potlood') naast **basisSAML-configuratie**.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Typ in de sectie **BasisSAML-configuratie** in het tekstvak **URL beantwoorden** een URL in een van de volgende indelingen:

    ||
    |-|
    | `https://<Account ID>.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na1.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na2.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.sandbox.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na1.sandbox.NetSuite.com/saml2/acs`|
    | `https://<Account ID>.na2.sandbox.NetSuite.com/saml2/acs`|

    * U krijgt ** < `Account ID` ** de waarde in de Netsuite-configuratiesectie die later in de zelfstudie wordt uitgelegd bij stap 8 onder Netsuite-configuratie. U vindt het exacte domein (zoals system.na0.netsuite.com in dit geval).

        ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/domain-value.png)

        > [!NOTE]
        > De waarden in de voorgaande URL's zijn niet echt. Werk ze bij met de werkelijke url van het antwoord. Neem contact op met het [NetSuite Client Support Team](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml)om de waarde te krijgen. U ook verwijzen naar de indelingen die worden weergegeven in de sectie **BasisSAML-configuratie** in de Azure-portal.

1. NetSuite-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Naast bovenstaande, NetSuite applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw vereisten.

    | Name | Bronkenmerk |
    | ---------------| --------------- |
    | account  | `account id` |

    > [!NOTE]
    > De waarde van het accountkenmerk is niet reëel. U zult deze waarde bijwerken, zoals later in deze zelfstudie wordt uitgelegd.

1. Zoek op de pagina Eén aanmelding instellen met SAML in de sectie SAML-ondertekeningscertificaat de optie Federation Metadata XML en selecteer Downloaden om het certificaat te downloaden en op te slaan op uw computer.

    ![De koppeling Certificaat downloaden](common/metadataxml.png)

1. Kopieer in de sectie **NetSuite instellen** de juiste URL of URL's, afhankelijk van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster van de Azure-portal de optie **Azure Active Directory** > **Users** > **All users All users**.

1. Selecteer **Nieuwe gebruiker** boven aan het scherm.

1. Voer **in** het deelvenster Eigenschappen van gebruiker de volgende stappen uit:

   a. Voer **in** het vak Naam **B.Simon in.**  
   b. Voer **in** het vak username@companydomain.extension Gebruikersnaam de B.Simon@contoso.com(bijvoorbeeld).  
   c. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**  
   d. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u gebruiker B.Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot NetSuite.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **NetSuite**in de lijst met toepassingen .
1. Zoek in het overzichtsvenster naar de sectie **Beheren** en selecteer vervolgens de koppeling **Gebruikers en groepen.**

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens in het deelvenster Toewijzing **toevoegen** de optie Gebruikers **en groepen**.

    ![De knop Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het deelvenster **Gebruikers en groepen** in de vervolgkeuzelijst **Gebruikers** de optie **B.Simon**en selecteer vervolgens de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, gaat u als volgt te werk:

   a. Selecteer in het deelvenster **Rol selecteren** in de vervolgkeuzelijst de juiste rol voor de gebruiker.  
   b. Selecteer onder aan het scherm de knop **Selecteren.**
1. Selecteer **in** het deelvenster Toewijzing toevoegen de knop **Toewijzen.**

## <a name="configure-netsuite-sso"></a>NetSuite SSO configureren

1. Open een nieuw tabblad in uw browser en meld u aan bij uw NetSuite-bedrijfssite als beheerder.

2. Selecteer in de bovenste navigatiebalk De optie **Instellen**en selecteer **vervolgens Functies** > **voor het inschakelen van bedrijven**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Selecteer **SuiteCloud**op de werkbalk in het midden van de pagina .

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-suitecloud.png)

4. Schakel **onder Verificatie beheren**het selectievakje **SAML Single Sign-on in** om de optie SAML-aanmelding in NetSuite in te schakelen.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-ticksaml.png)

5. Selecteer **Instellen**in de bovenste navigatiebalk .

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-setup.png)

6. Selecteer **Integratie**in de lijst **Taken instellen** .

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-integration.png)

7. Selecteer **onder Verificatie beheren**de optie **SAML Single Sign-on**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-saml.png)

8. Ga in het deelvenster **SAML-installatie** onder **NetSuite-configuratie**als volgt te werk:

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Schakel het selectievakje **Primaire verificatiemethode** in.

    b. Selecteer **onder SAMLV2-gegevensagegevens van de identiteitsprovider**de optie **IDP-metagegevensbestand uploaden**en selecteer **Vervolgens Bladeren** om het metagegevensbestand te uploaden dat u hebt gedownload van de Azure-portal.

    c. Selecteer **Indienen**.

9. Selecteer **in**de netsuite-bovenste navigatiebalk De optie Setup en selecteer vervolgens **Bedrijfsgegevens** > **Company Information**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-com.png)

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-account-id.png)

    b. Kopieer in het deelvenster **Bedrijfsgegevens** in de rechterkolom de waarde **account-id.**

    c. Plak de **account-id** die u hebt gekopieerd van het NetSuite-account in het vak **Kenmerkwaarde** in Azure AD.

10. Voordat gebruikers eenmalige aanmelding kunnen gebruiken met NetSuite moeten ze over de juiste machtigingen beschikken in NetSuite. Ga als volgt te werk om deze machtigingen toe te wijzen:

    a. Selecteer **Instellen**in de bovenste navigatiebalk .

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-setup.png)

    b. Selecteer in het linkerdeelvenster **Gebruikers/Rollen**en selecteer **Rollen beheren**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Selecteer **Nieuwe rol**.

    d. Voer een **naam** in voor de nieuwe rol.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-new-role.png)

    e. Selecteer **Opslaan**.

    f. Selecteer **machtigingen**in de bovenste navigatiebalk . Selecteer vervolgens **Setup**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-sso.png)

    g. Selecteer **SAML Single Sign-on**en selecteer **Toevoegen**.

    h. Selecteer **Opslaan**.

    i. Selecteer **instellen**op de bovenste navigatiebalk en selecteer **Vervolgens Setup Manager**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-setup.png)

    j. Selecteer in het linkerdeelvenster **Gebruikers/Rollen**en selecteer **Gebruikers beheren**.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Selecteer een testgebruiker, selecteer **Bewerken**en selecteer vervolgens het tabblad **Access.**

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-edit-user.png)

    l. Wijs in het deelvenster **Rollen** de juiste rol toe die u hebt gemaakt.

    ![Eenmalige aanmelding configureren](./media/NetSuite-tutorial/ns-add-role.png)

    m. Selecteer **Opslaan**.

### <a name="create-the-netsuite-test-user"></a>De NetSuite-testgebruiker maken

In deze sectie wordt een gebruiker genaamd B.Simon gemaakt in NetSuite. NetSuite biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. Er is geen actie-item voor u in deze sectie. Als er nog geen gebruiker bestaat in NetSuite, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>Test SSO

In dit gedeelte test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u de NetSuite-tegel in het toegangspaneel selecteert, moet u automatisch worden aangemeld bij de NetSuite waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Probeer NetSuite met Azure AD](https://aad.portal.azure.com/)
- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [NetSuite beveiligen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)