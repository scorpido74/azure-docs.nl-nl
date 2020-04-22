---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Nitro Productivity Suite | Microsoft Documenten'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Nitro Productivity Suite.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 98c96c9f-18a6-4a20-919b-74fb113ee465
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/02/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9ca675e43f4d20898d9f97c175da71f8d5c59e4f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81676925"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nitro-productivity-suite"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Nitro Productivity Suite

In deze zelfstudie leert u hoe u Nitro Productivity Suite integreren met Azure Active Directory (Azure AD). Wanneer u Nitro Productivity Suite integreert met Azure AD, u het als:

* Beheer in Azure AD die toegang heeft tot Nitro Productivity Suite.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Nitro Productivity Suite met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de App Voor software as a service (SaaS) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Een Nitro Productivity Suite [Enterprise-abonnement.](https://www.gonitro.com/pricing)

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Nitro Productivity Suite ondersteunt **SP** en **IDP** geïnitieerde SSO.
* Nitro Productivity Suite ondersteunt Just In Time-gebruikersinrichting. **Just In Time**
* Nadat u Nitro Productivity Suite hebt geconfigureerd, u sessiecontrole afdwingen, wat exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermt. Sessiebeheer strekt zich uit van voorwaardelijke toegang. Zie Meer informatie voor meer informatie [over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-nitro-productivity-suite-from-the-gallery"></a>Nitro Productivity Suite toevoegen vanuit de galerie

Als u de integratie van Nitro Productivity Suite in Azure AD wilt configureren, moet u Nitro Productivity Suite vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkerdeelvenster **Azure Active Directory**.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Nitro Productivity Suite** in de sectie Toevoegen in de **galeriesectie** in het zoekvak.
1. Selecteer **Nitro Productivity Suite** in de resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-nitro-productivity-suite"></a>Azure AD-eenmaligaanmelding voor Nitro Productivity Suite configureren en testen

Azure AD SSO configureren en testen met Nitro Productivity Suite, met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een gekoppelde relatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Nitro Productivity Suite.

Als u Azure AD SSO wilt configureren en testen met Nitro Productivity Suite, voert u de volgende bouwstenen in:

1. [Configureer Azure AD SSO](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
    1. [Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user) om Azure AD-enkele aanmelding met B.Simon te testen.
    1. [Wijs de Azure AD-testgebruiker](#assign-the-azure-ad-test-user) toe om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. [Configureer Nitro Productivity Suite SSO](#configure-nitro-productivity-suite-sso) om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    1. [Maak een Nitro Productivity Suite-testgebruiker](#create-a-nitro-productivity-suite-test-user) met een tegenhanger van B.Simon in Nitro Productivity Suite, gekoppeld aan de Azure AD-weergave van de gebruiker.
1. [Test SSO](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **Nitro Productivity Suite-toepassingsintegratie** de sectie **Beheren.** Selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Zoek in de sectie **SAML-ondertekeningscertificaat** **het certificaat (Base64).** Selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![Schermafbeelding van de sectie SAML-ondertekeningscertificaat, met koppeling Downloaden gemarkeerd](common/certificatebase64.png)
    
1. Selecteer in de sectie **Nitro Productivity Suite instellen** het kopieerpictogram naast de **aanmeldings-URL**.
    
    ![Schermafbeelding van de sectie Nitro Productivity Suite instellen, met URL's en kopieerpictogrammen gemarkeerd](common/copy-configuration-urls.png)
    
1. Zoek in de [Nitro Admin-portal](https://admin.gonitro.com/)op de pagina **Ondernemingsinstellingen** de sectie **Eén aanmelding.** Selecteer **SAML SSO instellen**.

    a. Plak de **URL van aanmelding** van de vorige stap in het veld **AanmeldingsURL.**
    
    b. Upload het **certificaat (Base64)** vanuit de eerdere stap in het veld **Tekencertificaat X509.**
    
    c. Selecteer **Indienen**.
    
    d. Selecteer **Eén aanmelding inschakelen**.


1. Ga terug naar de [Azure-portal](https://portal.azure.com/). Selecteer op de pagina **Eén aanmelding instellen met SAML** het potloodpictogram voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Schermafbeelding van Eenmalige aanmelding instellen met SAML-pagina, met potloodpictogram gemarkeerd](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden voor de volgende velden in als u de toepassing in de **idp-modus** wilt configureren:

    a. Kopieer en plak in het tekstvak **Id** het veld **SAML-entiteits-id** uit de [Nitro Admin-portal](https://admin.gonitro.com/). Het moet het volgende patroon hebben:`urn:auth0:gonitro-prod:<ENVIRONMENT>`

    b. Kopieer en plak in het tekstvak **URL beantwoorden** het **veld ACS-URL** uit de [Nitro Admin-portal](https://admin.gonitro.com/). Het moet het volgende patroon hebben:`https://gonitro-prod.eu.auth0.com/login/callback?connection=<ENVIRONMENT>`

1. Selecteer **Extra URL's instellen**en voer de volgende stap uit als u de toepassing wilt configureren in de gestarte **SP-modus:**

    Typ de URL in het tekstvak **AANmeldings-URL:**`https://sso.gonitro.com/login`

1. Selecteer **Opslaan**.

1. De Nitro Productivity Suite-toepassing verwacht dat de SAML-beweringen in een specifieke indeling zijn, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![Schermafbeelding van standaardkenmerken](common/default-attributes.png)

1. Naast de voorgaande kenmerken verwacht de Nitro Productivity Suite-toepassing dat er nog een paar kenmerken worden doorgegeven in de SAML-respons. Deze kenmerken zijn vooraf ingevuld, maar u ze bekijken volgens uw vereisten.
    
    | Naam  |  Bronkenmerk|
    | ---------------| --------------- |
    | werknemerAantal |  user.objectid |


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

In deze sectie stelt u B.Simon in staat om Azure single sign-on te gebruiken door toegang te verlenen tot Nitro Productivity Suite.

1. Selecteer in de Azure-portal Alle**bedrijfstoepassingen** **.** > 
1. Selecteer Nitro Productivity **Suite**in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![Schermafbeelding van de sectie Beheren, met gemarkeerde gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer vervolgens in het dialoogvenster **Toewijzing toevoegen** de optie Gebruikers **en groepen**.

    ![Schermafbeelding van de pagina Gebruikers en groepen, met Gebruiker toevoegen gemarkeerd](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst met gebruikers. Kies vervolgens **Selecteer** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens **Selecteer** onder aan het scherm.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-nitro-productivity-suite-sso"></a>Nitro Productivity Suite SSO configureren

Als u eenmalige aanmelding wilt configureren aan de kant van Nitro Productivity Suite, verzendt u het gedownloade **certificaat (Base64)** en de juiste gekopieerde URL's van de Azure-portal naar het [ondersteuningsteam van Nitro Productivity Suite.](https://www.gonitro.com/support) Het ondersteuningsteam zorgt ervoor dat de SAML SSO-verbinding aan beide zijden goed is ingesteld.

### <a name="create-a-nitro-productivity-suite-test-user"></a>Een Nitro Productivity Suite-testgebruiker maken

Nitro Productivity Suite ondersteunt just-in-time gebruikersinrichting, die standaard is ingeschakeld. Er is geen extra actie voor u om te nemen. Als een gebruiker nog niet bestaat in Nitro Productivity Suite, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>Test SSO 

In deze sectie test u uw azure AD-configuratie voor eenmalige aanmelding met access panel.

Wanneer u de nitro productivity suite-tegel in het Access-paneel selecteert, wordt u automatisch aangemeld bij de Nitro Productivity Suite waarvoor u SSO hebt ingesteld. Zie [Aanmelden en apps starten via de portal Mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Nitro Productivity Suite uitproberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Bescherm Nitro Productivity Suite met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

