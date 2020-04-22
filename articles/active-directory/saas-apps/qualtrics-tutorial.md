---
title: 'Zelfstudie: Azure Active Directory-integratie met SAP Qualtrics | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SAP Qualtrics.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4df889ab-2685-4d15-a163-1ba26567eeda
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.openlocfilehash: 2e27d020bd25f234d084fba770e234bdccb40a99
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81682379"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-qualtrics"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met SAP Qualtrics

In deze zelfstudie leert u hoe u SAP Qualtrics integreert met Azure Active Directory (Azure AD). Wanneer u SAP Qualtrics integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot SAP Qualtrics.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij SAP Qualtrics met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de App Voor software as a service (SaaS) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Een SAP Qualtrics-abonnement ingeschakeld voor single sign-on (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* SAP Qualtrics ondersteunt **SP** en **IDP** heeft SSO geïnitieerd.
* SAP Qualtrics ondersteunt **Just In Time** gebruikersinrichting.
* Nadat u SAP Qualtrics hebt geconfigureerd, u sessiecontrole afdwingen, wat exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermt. Sessiebeheer strekt zich uit van voorwaardelijke toegang. Zie Meer informatie voor meer informatie [over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-sap-qualtrics-from-the-gallery"></a>SAP Qualtrics toevoegen vanuit de galerie

Als u de integratie van SAP Qualtrics in Azure AD wilt configureren, moet u SAP Qualtrics uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkerdeelvenster **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **SAP Qualtrics** in het zoekvak in de sectie **Toevoegen vanuit de galerie.**
1. Selecteer **SAP Qualtrics** uit de resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-qualtrics"></a>Azure AD-aanmelding configureren en testen voor SAP Qualtrics

Azure AD SSO configureren en testen met SAP Qualtrics, met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een gekoppelde relatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in SAP Qualtrics.

Als u Azure AD SSO wilt configureren en testen met SAP Qualtrics, voert u de volgende bouwstenen in:

1. [Configureer Azure AD SSO](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
    1. [Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user) om Azure AD-enkele aanmelding met B.Simon te testen.
    1. [Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user) toe om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. [Configureer SAP Qualtrics SSO](#configure-sap-qualtrics-sso) om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. [Maak een SAP Qualtrics-testgebruiker](#create-sap-qualtrics-test-user) om een tegenhanger van B.Simon in SAP Qualtrics te hebben, gekoppeld aan de Azure AD-weergave van de gebruiker.
1. [Test SSO](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **SAP Qualtrics-toepassingsintegratie** de sectie **Beheren.** Selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Selecteer op de pagina **Eén aanmelding instellen met SAML** het potloodpictogram voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Schermafbeelding van Eenmalige aanmelding instellen met SAML-pagina, met potloodpictogram gemarkeerd](common/edit-urls.png)

1. Voer op de pagina **Eén aanmelding instellen met SAML** de waarden in voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:
    
    a. Typ **in** het tekstvak Id een URL die het volgende patroon gebruikt:

    `https://< DATACENTER >.qualtrics.com`
   
    b. Typ in het tekstvak **URL beantwoorden** een URL die het volgende patroon gebruikt:

    `https://< DATACENTER >.qualtrics.com/login/v1/sso/saml2/default-sp`

    c. Typ in het tekstvak **Relaystatus** een URL die het volgende patroon gebruikt:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

1. Selecteer **Extra URL's instellen**en voer de volgende stap uit als u de toepassing wilt configureren in de gestarte **SP-modus:**

    Typ in het tekstvak **AANmeldings-URL** een URL die het volgende patroon gebruikt:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, id, antwoord-URL en relaystatus. Neem contact op met het [Qualtrics Client support team](https://www.qualtrics.com/support/)om aan deze waarden te komen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Selecteer op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** het kopieerpictogram om **de url van de appfederatie-metagegevens** te kopiëren en op uw computer op te slaan.

    ![Schermafbeelding van het SAML-ondertekeningscertificaat, met het pictogram kopiëren gemarkeerd](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal de optie **Azure Active Directory** > **Users** > **All users .**
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en schrijf het wachtwoord op.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie stelt u B.Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot SAP Qualtrics.

1. Selecteer in de Azure-portal Alle**bedrijfstoepassingen** **.** > 
1. Selecteer **SAP Qualtrics**in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![Schermafbeelding van de sectie Beheren, met gemarkeerde gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer vervolgens in het dialoogvenster **Toewijzing toevoegen** de optie Gebruikers **en groepen**.

    ![Schermafbeelding van de pagina Gebruikers en groepen, met Gebruiker toevoegen gemarkeerd](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst met gebruikers. Kies vervolgens **Selecteer** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens **Selecteer** onder aan het scherm.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-sap-qualtrics-sso"></a>SAP Qualtrics SSO configureren

Als u eenmalige aanmelding wilt configureren aan de KANT van SAP Qualtrics, stuurt u de url van de gekopieerde **appfederatie-metagegevens** van de Azure-portal naar het [ondersteuningsteam van SAP Qualtrics.](https://www.qualtrics.com/support/) Het ondersteuningsteam zorgt ervoor dat de SAML SSO-verbinding aan beide zijden goed is ingesteld.

### <a name="create-sap-qualtrics-test-user"></a>SAP Qualtrics-testgebruiker maken

SAP Qualtrics ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Er is geen extra actie voor u om te nemen. Als een gebruiker nog niet bestaat in SAP Qualtrics, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>Test SSO 

In deze sectie test u uw azure AD-configuratie voor eenmalige aanmelding met access panel.

Wanneer u de SAP Qualtrics-tegel in het Access Panel selecteert, wordt u automatisch aangemeld bij de SAP Qualtrics waarvoor u SSO hebt ingesteld. Zie [Aanmelden en apps starten via de portal Mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer SAP Qualtrics met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Bescherm SAP Qualtrics met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

