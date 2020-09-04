---
title: 'Zelfstudie: Integratie van eenmalige aanmelding van Azure Active Directory met Nitro Productivity Suite | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Nitro Productivity Suite.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/02/2020
ms.author: jeedes
ms.openlocfilehash: b67e280824c5e2336aa79e14d3e09d3a670118a5
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88554325"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-nitro-productivity-suite"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met Nitro Productivity Suite

In deze zelfstudie leert u hoe u Nitro Productivity Suite integreert met Azure Active Directory (Azure AD). Wanneer u Nitro Productivity Suite integreert met Azure AD, kunt u:

* In Azure AD beheren wie toegang heeft tot Nitro Productivity Suite.
* Instellen dat gebruikers automatisch met hun Azure AD-account worden aangemeld bij Nitro Productivity Suite.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

Als u meer wilt weten over de integratie van SaaS-apps (Software as a Service) met Azure AD, gaat u naar [Eenmalige aanmelding voor toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een Nitro Productivity Suite [Enterprise-abonnement](https://www.gonitro.com/pricing).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* Nitro Productivity Suite ondersteunt door **SP** en **IDP** geïnitieerde eenmalige aanmelding.
* Nitro Productivity Suite biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers.
* Zodra u Nitro Productivity Suite hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. Zie [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app) voor meer informatie.

## <a name="add-nitro-productivity-suite-from-the-gallery"></a>Nitro Productivity Suite toevoegen vanuit de galerie

Als u de integratie van Nitro Productivity Suite in Azure AD wilt configureren, moet u Nitro Productivity Suite vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ in de sectie **Toevoegen uit de galerie** **Nitro Productivity Suite** in het zoekvak.
1. Selecteer **Nitro Productivity Suite** in de resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-nitro-productivity-suite"></a>Eenmalige aanmelding van Azure AD configureren en testen voor Nitro Productivity Suite

Configureer en test eenmalige aanmelding van Azure AD met Nitro Productivity Suite met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Nitro Productivity Suite.

Voer de volgende stappen uit om eenmalige aanmelding van Azure AD met Nitro Productivity Suite te configureren en te testen:

1. [Configureer eenmalige aanmelding van Azure AD](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
    1. [Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user) om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. [Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user) zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. [Eenmalige aanmelding bij Nitro Productivity Suite configureren](#configure-nitro-productivity-suite-sso) als u de instellingen voor eenmalige aanmelding aan de toepassingszijde wilt configureren.
    1. [Een testgebruiker voor Nitro Productivity Suite maken](#create-a-nitro-productivity-suite-test-user): als u in Nitro Productivity Suite een tegenhanger van B.Simon wilt hebben die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. [Test de eenmalige aanmelding](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in [Azure Portal](https://portal.azure.com/) op de integratiepagina van de toepassing **Nitro Productivity Suite** de sectie **Beheren**. Selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Zoek **Certificaat (Base64)** in de sectie **SAML-handtekeningcertificaat**. Selecteer **Downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![Schermopname van de sectie SAML-handtekeningcertificaat, met de koppeling Downloaden gemarkeerd](common/certificatebase64.png)
    
1. Selecteer in de sectie **Nitro Productivity Suite instellen** het kopieerpictogram naast de **aanmeldings-URL**.
    
    ![Schermafbeelding van de sectie Nitro Productivity Suite instellen, met URL's en kopieerpictogrammen gemarkeerd](common/copy-configuration-urls.png)
    
1. Ga in de [Nitro-beheerportal](https://admin.gonitro.com/) op de pagina **Enterprise-instellingen** naar de sectie **eenmalige aanmelding**. Selecteer **SAML SSO instellen**.

    a. Plak de **aanmeldings-URL** uit de voorgaande stap in het veld **aanmeldings-URL**.
    
    b. Upload het **certificaat (base64)** uit de vorige stap in het veld **X509 handtekeningcertificaat**.
    
    c. Selecteer **Indienen**.
    
    d. Schakel het selectievakje **Eenmalige aanmelding inschakelen** in.


1. Ga terug naar [Azure Portal](https://portal.azure.com/). Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** op het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Schermopname van Eenmalige aanmelding instellen met SAML, met het potloodpictogram gemarkeerd](common/edit-urls.png)

1. Voer in de sectie **Standaard SAML-configuratie** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:

    a. Kopieer en plak in het tekstvak **Id** het veld **SAML-entiteits-id** uit de [Nitro-beheerportal](https://admin.gonitro.com/). Gebruik hiervoor het volgende patroon: `urn:auth0:gonitro-prod:<ENVIRONMENT>`

    b. Kopieer en plak in het tekstvak **antwoord-URL** het veld **ACS-URL** uit de [Nitro-beheerportal](https://admin.gonitro.com/). Gebruik hiervoor het volgende patroon: `https://gonitro-prod.eu.auth0.com/login/callback?connection=<ENVIRONMENT>`

1. Selecteer **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u de URL: `https://sso.gonitro.com/login`

1. Selecteer **Opslaan**.

1. De Nitro Productivity Suite-toepassing verwacht SAML-asserties in een specifieke indeling. Hiervoor moet u aangepaste kenmerktoewijzingen toevoegen aan de configuratie van uw SAML-tokenkenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![Schermopname van standaardkenmerken](common/default-attributes.png)

1. Naast de voorgaande kenmerken verwacht Nitro Productivity Suite nog enkele kenmerken die als SAML-antwoord moeten worden doorgestuurd. Deze kenmerken worden vooraf ingevuld, maar u kunt deze indien nodig herzien.
    
    | Naam  |  Bronkenmerk|
    | ---------------| --------------- |
    | employeeNumber |  user.objectid |


### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie gaat u een testgebruiker met de naam B.Simon maken in de Azure-portal.

1. Selecteer in het linkerdeelvenster van de Azure-portal **Azure Active Directory** > **Gebruikers** > **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Volg de volgende stappen bij de eigenschappen voor **Gebruiker**:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer username@companydomain.extension in het veld **Gebruikersnaam** in. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer het wachtwoord.
   1. Selecteer **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Nitro Productivity Suite.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen**.
1. Selecteer **Nitro Productivity Suite** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![Het gedeelte Beheren, met Gebruikers en groepen gemarkeerd](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Schermopname van de pagina Gebruikers en groepen, met Gebruiker toevoegen gemarkeerd](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** **B.Simon** in de lijst met gebruikers. Kies vervolgens **Selecteren** onderaan het scherm.
1. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens **Selecteren** onderaan het scherm.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-nitro-productivity-suite-sso"></a>Eenmalige aanmelding voor Nitro Productivity Suite configureren

Als u eenmalige aanmelding aan de zijde van Nitro Productivity Suite wilt configureren, moet u het gedownloade **Certificaat (Base64)** en de juiste uit Azure Portal gekopieerde URL's verzenden naar het [ondersteuningsteam voor Nitro Productivity Suite](https://www.gonitro.com/support). Dit ondersteuningsteam zorgt ervoor dat de SAML SSO-verbinding aan beide kanten juist wordt ingesteld.

### <a name="create-a-nitro-productivity-suite-test-user"></a>Een testgebruiker voor Nitro Productivity Suite maken

Nitro Productivity Suite biedt ondersteuning voor Just-In-Time-inrichting van gebruikers. Deze functie is standaard ingeschakeld. U hoeft geen verdere actie te ondernemen. Als er nog geen gebruiker bestaat in Nitro Productivity Suite, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u de tegel Nitro Productivity Suite in het toegangsvenster selecteert, wordt u automatisch aangemeld bij het exemplaar van Nitro Productivity Suite waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Aanmelden bij en het starten van apps vanuit de Mijn apps-portal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Nitro Productivity Suite proberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Nitro Productivity Suite beschermen met geavanceerde zichtbaarheid en controles](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

