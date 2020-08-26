---
title: 'Zelfstudie: Integratie van Azure Active Directory met SAP Qualtrics | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SAP Qualtrics.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/03/2020
ms.author: jeedes
ms.openlocfilehash: 170997099f1194bbc75d6d61a7c29fc1d18b462a
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/18/2020
ms.locfileid: "88552212"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-qualtrics"></a>Zelfstudie: Integratie van eenmalige aanmelding (SSO) van Azure Active Directory met SAP Qualtrics

In deze zelfstudie leert u hoe u SAP Qualtrics integreert met Azure AD (Active Directory AD). Wanneer u SAP Qualtrics integreert met Azure AD, kunt u het volgende doen:

* U kunt in Azure AD beheren wie toegang tot SAP Qualtrics heeft.
* Ervoor zorgen dat gebruikers zich automatisch met hun Azure AD-account kunnen aanmelden bij SAP Qualtrics.
* Uw accounts op één centrale locatie beheren: de Azure-portal.

Als u meer wilt weten over de integratie van SaaS-apps (Software as a Service) met Azure AD, gaat u naar [Eenmalige aanmelding voor toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on).

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u zich aanmelden voor een [gratis account](https://azure.microsoft.com/free/).
* Een SAP Qualtrics-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SAP Qualtrics ondersteunt door **SP** en **IDP** geïnitieerde eenmalige aanmelding.
* SAP Qualtrics biedt ondersteuning voor **Just-In-Time**-inrichting van gebruikers.
* Zodra u SAP Qualtrics hebt geconfigureerd, kunt u sessiebeheer afdwingen, waardoor exfiltratie en infiltratie van gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessiebeheer is een uitbreiding van voorwaardelijke toegang. Zie [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app) voor meer informatie.

## <a name="add-sap-qualtrics-from-the-gallery"></a>SAP Qualtrics toevoegen vanuit de galerie

Voor het configureren van de integratie van SAP Qualtrics met Azure AD moet u SAP Qualtrics uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.
1. Ga naar **Bedrijfstoepassingen** en selecteer vervolgens **Alle toepassingen**.
1. Als u een nieuwe toepassing wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ in de sectie **Toevoegen vanuit de galerie** in het zoekvak: **SAP Qualtrics**.
1. Selecteer **SAP Qualtrics** in de lijst met resultaten en voeg vervolgens de app toe. Wacht enkele seconden tot de app is toegevoegd aan de tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-qualtrics"></a>Eenmalige aanmelding van Azure AD configureren en testen voor SAP Qualtrics

Configureer en test eenmalige aanmelding van Azure AD met SAP Qualtrics met behulp van een testgebruiker met de naam **B.Simon**. Eenmalige aanmelding werkt alleen als u een koppelingsrelatie tot stand brengt tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SAP Qualtrics.

Voltooi de volgende bouwstenen om eenmalige aanmelding van Azure AD met SAP Qualtrics te configureren en te testen:

1. [Configureer eenmalige aanmelding van Azure AD](#configure-azure-ad-sso) zodat uw gebruikers deze functie kunnen gebruiken.
    1. [Maak een Azure AD-testgebruiker](#create-an-azure-ad-test-user) om eenmalige aanmelding van Azure AD te testen met B.Simon.
    1. [Wijs de Azure AD-testgebruiker toe](#assign-the-azure-ad-test-user) zodat B.Simon eenmalige aanmelding van Azure AD kan gebruiken.
1. [Eenmalige aanmelding configureren in SAP Qualtrics](#configure-sap-qualtrics-sso) als u de instellingen voor eenmalige aanmelding aan de clientzijde wilt configureren.
    1. [Maak een testgebruiker in SAP Qualtrics](#create-sap-qualtrics-test-user) om in SAP Qualtrics een tegenhanger van B.Simon te hebben, die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. [Test de eenmalige aanmelding](#test-sso) om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Eenmalige aanmelding van Azure AD configureren

Volg deze stappen om eenmalige aanmelding van Azure AD in te schakelen in Azure Portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/) op de integratiepagina van de toepassing **SAP Qualtrics** de sectie **Beheren**. Selecteer **Eenmalige aanmelding**.
1. Selecteer **SAML** op de pagina **Selecteer een methode voor eenmalige aanmelding**.
1. Selecteer op de pagina **Eenmalige aanmelding instellen met SAML** het potloodpictogram voor **Standaard-SAML-configuratie** om de instellingen te bewerken.

   ![Schermopname van Eenmalige aanmelding instellen met SAML, met het potloodpictogram gemarkeerd](common/edit-urls.png)

1. Voer op de pagina **Eenmalige aanmelding met SAML instellen** de waarden voor de volgende velden in, als u de toepassing in de met **IDP** geïnitieerde modus wilt configureren:
    
    a. In het tekstvak **Id** typt u een URL met het volgende patroon: 

    `https://< DATACENTER >.qualtrics.com`
   
    b. In het tekstvak **Antwoord-URL** typt u een URL met het volgende patroon: 

    `https://< DATACENTER >.qualtrics.com/login/v1/sso/saml2/default-sp`

    c. In het tekstvak **Relaystatus** typt u een URL met de volgende notatie:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

1. Selecteer **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon:

    `https://< brandID >.< DATACENTER >.qualtrics.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL, antwoord-URL en relaystatus. Neem contact op met het [Qaultrics-clientondersteuningsteam](https://www.qualtrics.com/support/) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Klik op de pagina **Eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekeningcertificaat** op het pictogram Kopiëren om de **URL voor federatieve metagegevens van de app** te kopiëren en sla deze vervolgens op uw computer op.

    ![Schermopname van het SAML-handtekeningcertificaat, met het kopieerpictogram gemarkeerd](common/copy-metadataurl.png)

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

In deze sectie geeft u B.Simon toestemming om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot SAP Qualtrics.

1. Selecteer in de Azure-portal **Bedrijfstoepassingen** > **Alle toepassingen**.
1. Typ en selecteer **SAP Qualtrics** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![Het gedeelte Beheren, met Gebruikers en groepen gemarkeerd](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**. Selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Schermopname van de pagina Gebruikers en groepen, met Gebruiker toevoegen gemarkeerd](common/add-assign-user.png)

1. Selecteer in het dialoogvenster **Gebruikers en groepen** **B.Simon** in de lijst met gebruikers. Kies vervolgens **Selecteren** onderaan het scherm.
1. Als u een rolwaarde verwacht in de SAML-assertie, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst. Kies vervolgens **Selecteren** onderaan het scherm.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

## <a name="configure-sap-qualtrics-sso"></a>Eenmalige aanmelding voor SAP Qualtrics

Als u eenmalige aanmelding wilt configureren aan de kant van SAP Qualtrics, verzendt u de gekopieerde **app-URL voor federatieve metagegevens** van de Azure-portal naar het [SAP Qualtrics-ondersteuningsteam](https://www.qualtrics.com/support/). Dit ondersteuningsteam zorgt ervoor dat de SAML SSO-verbinding aan beide kanten juist wordt ingesteld.

### <a name="create-sap-qualtrics-test-user"></a>SAP Qualtrics-testgebruiker maken

SAP Qualtrics biedt ondersteuning voor Just-In-Time-inrichting van gebruikers, wat standaard is ingeschakeld. U hoeft geen verdere actie te ondernemen. Als er nog geen gebruiker in SAP Qualtrics bestaat, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>Eenmalige aanmelding testen 

In deze sectie test u de configuratie voor eenmalige aanmelding van Azure AD met behulp van het toegangsvenster.

Wanneer u de tegel SAP Qualtrics in het toegangsvenster selecteert, zou u automatisch moeten worden aangemeld bij het exemplaar van SAP Qualtrics waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Aanmelden bij en het starten van apps vanuit de Mijn apps-portal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Tutorials for integrating SaaS applications with Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Zelfstudies voor het integreren van SaaS-toepassingen met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat houden toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory in?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [SAP Qualtrics proberen met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [SAP Qualtrics beveiligen met geavanceerde zichtbaarheid en controles](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

