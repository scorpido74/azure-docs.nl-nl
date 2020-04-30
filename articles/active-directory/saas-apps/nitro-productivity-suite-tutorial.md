---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Nitro-productiviteits pakket | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory-en Nitro-productiviteits Suite.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81676925"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nitro-productivity-suite"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met de Nitro-productiviteits Suite

In deze zelf studie leert u hoe u Nitro productivity suite integreert met Azure Active Directory (Azure AD). Wanneer u Nitro-productiviteits Suite integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de Nitro-productiviteits Suite.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Nitro-productiviteits Suite met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de SaaS-app (Software as a Service) met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Een [Enter prise-abonnement](https://www.gonitro.com/pricing)van Nitro productivity suite.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Nitro productivity suite ondersteunt door **SP** en **IDP** geïnitieerde SSO.
* Nitro productivity suite ondersteunt **just-in-time** gebruikers inrichting.
* Nadat u Nitro Productivity Suite hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessie beheer wordt uitgebreid vanuit voorwaardelijke toegang. Zie [informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)voor meer informatie.

## <a name="add-nitro-productivity-suite-from-the-gallery"></a>Nitro-productiviteits Suite toevoegen vanuit de galerie

Als u de integratie van Nitro-productiviteits Suite wilt configureren in azure AD, moet u Nitro-productiviteits Suite toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer **Azure Active Directory**in het linkerdeel venster.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Nitro productivity suite** in het zoekvak.
1. Selecteer **Nitro-productiviteits Suite** in de resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-nitro-productivity-suite"></a>Eenmalige aanmelding van Azure AD voor Nitro-productiviteits pakket configureren en testen

Configureer en test Azure AD SSO met Nitro-productiviteits pakket door gebruik te maken van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een gekoppelde relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Nitro productivity suite.

Als u Azure AD SSO wilt configureren en testen met Nitro-productiviteits Suite, voltooit u de volgende bouw stenen:

1. [Configureer Azure AD SSO](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
    1. [Maak een Azure AD-test gebruiker om de](#create-an-azure-ad-test-user) eenmalige aanmelding van Azure ad te testen met B. Simon.
    1. [Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user) toe om B. Simon in te scha kelen voor het gebruik van eenmalige aanmelding voor Azure AD.
1. [Configureer Nitro productivity suite SSO](#configure-nitro-productivity-suite-sso) om de instellingen voor eenmalige aanmelding te configureren aan de kant van de toepassing.
    1. [Maak een test gebruiker van een Nitro-productiviteits Suite](#create-a-nitro-productivity-suite-test-user) om een soort tegen te gaan van B. Simon in Nitro-productiviteits pakket, gekoppeld aan de Azure AD-representatie van de gebruiker.
1. [Test SSO](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **Nitro productivity suite** -toepassings integratie de sectie **beheren** . Selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Zoek in de sectie **SAML-handtekening certificaat** het **certificaat (base64)**. Selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![Scherm afbeelding van het certificaat van het SAML-Ondertekeningscertificaat, met de download koppeling gemarkeerd](common/certificatebase64.png)
    
1. Selecteer in de sectie **Nitro Productivity Suite instellen** het pictogram kopiëren naast AANMELDINGS- **URL**.
    
    ![Scherm afbeelding van de sectie set up Nitro productivity suite met Url's en gekopieerde pictogrammen](common/copy-configuration-urls.png)
    
1. Ga in de [Nitro-beheer Portal](https://admin.gonitro.com/)naar de pagina **ondernemings instellingen** en zoek de sectie **eenmalige aanmelding** . Selecteer **Setup SAML SSO**.

    a. Plak de **aanmeldings-URL** uit de voor gaande Step into het veld **aanmeld URL** .
    
    b. Upload het **certificaat (base64)** uit de vorige stap in het veld **x509-handtekening certificaat** .
    
    c. Selecteer **Indienen**.
    
    d. Selecteer **eenmalige aanmelding inschakelen**.


1. Ga terug naar het [Azure Portal](https://portal.azure.com/). Selecteer op de pagina **eenmalige aanmelding met SAML instellen** het potlood pictogram voor de basis- **SAML-configuratie** om de instellingen te bewerken.

   ![Scherm afbeelding van het instellen van eenmalige aanmelding met de SAML-pagina, waarbij het potlood pictogram is gemarkeerd](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. Kopieer en plak in het tekstvak **id** het veld id van de **SAML-entiteit** vanuit de [Beheer Portal](https://admin.gonitro.com/)van de Nitro. Dit moet het volgende patroon hebben:`urn:auth0:gonitro-prod:<ENVIRONMENT>`

    b. Kopieer en plak in het tekstvak **antwoord-URL** het veld **ACS-URL** vanuit de [Nitro-beheer Portal](https://admin.gonitro.com/). Dit moet het volgende patroon hebben:`https://gonitro-prod.eu.auth0.com/login/callback?connection=<ENVIRONMENT>`

1. Selecteer **extra Url's instellen**en voer de volgende stap uit als u de toepassing in de modus door **SP** gestart wilt configureren:

    Typ in het tekstvak **URL voor aanmelding** de URL:`https://sso.gonitro.com/login`

1. Selecteer **Opslaan**.

1. De Nitro productivity suite-toepassing verwacht dat de SAML-bevestigingen een specifieke indeling hebben, waarvoor u aangepaste kenmerk toewijzingen moet toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![Scherm opname van standaard kenmerken](common/default-attributes.png)

1. Naast de voor gaande kenmerken verwacht de Nitro productivity suite-toepassing nog enkele kenmerken die moeten worden door gegeven in het SAML-antwoord. Deze kenmerken zijn vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.
    
    | Naam  |  Bronkenmerk|
    | ---------------| --------------- |
    | employeeNumber |  user.objectid |


### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer **Azure Active Directory** > **gebruikers** > van**alle gebruikers**in het linkerdeel venster van de Azure Portal.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en schrijf het wacht woord vervolgens op.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot de Nitro-productiviteits Suite.

1. Selecteer in het Azure Portal**alle toepassingen**in **bedrijfs toepassingen** > .
1. Selecteer in de lijst toepassingen de optie **Nitro productivity suite**.
1. Zoek op de pagina overzicht van de app de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![Scherm afbeelding van de sectie beheren met gebruikers en groepen gemarkeerd](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer vervolgens in het dialoog venster **toewijzing toevoegen** de optie **gebruikers en groepen**.

    ![Scherm opname van de pagina gebruikers en groepen, met gemarkeerde gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst met gebruikers. Kies vervolgens **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens **selecteren** onder aan het scherm.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-nitro-productivity-suite-sso"></a>Nitro productivity suite SSO configureren

Als u eenmalige aanmelding wilt configureren op de Nitro-productiviteits Suite, verzendt u het gedownloade **certificaat (base64)** en de juiste gekopieerde url's van de Azure Portal naar het [ondersteunings team van de Nitro-productiviteits Suite](https://www.gonitro.com/support). Het ondersteunings team zorgt ervoor dat de SAML SSO-verbinding aan beide zijden correct is ingesteld.

### <a name="create-a-nitro-productivity-suite-test-user"></a>Een test gebruiker voor een Nitro-productiviteits Suite maken

Nitro productivity suite biedt ondersteuning voor Just-in-time-gebruikers inrichting, die standaard is ingeschakeld. U hoeft verder niets te doen. Als een gebruiker niet al aanwezig is in de Nitro-productiviteits Suite, wordt er na verificatie een nieuwe gemaakt.

## <a name="test-sso"></a>SSO testen 

In deze sectie kunt u de configuratie van eenmalige aanmelding voor Azure AD testen met behulp van het toegangs venster.

Wanneer u de tegel Nitro productivity suite in het toegangs venster selecteert, wordt u automatisch aangemeld bij de Nitro-productiviteits Suite waarvoor u SSO hebt ingesteld. Zie voor meer informatie [Aanmelden en apps starten vanuit de portal mijn apps](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Nitro-productiviteits Suite uit met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Nitro-productiviteits Suite beschermen met geavanceerde zicht baarheid en controles](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

