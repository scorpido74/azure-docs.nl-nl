---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Screen cast-O-Matic | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en screen cast-O-Matic.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 525ad47d-5488-40e2-aeaf-ae6183745682
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bc887e95b6fa6f8b17fbbb3dbaae5105385a07fa
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/16/2019
ms.locfileid: "74132150"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-screencast-o-matic"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Screen cast-O-Matic

In deze zelf studie leert u hoe u screen cast-O-Matic integreert met Azure Active Directory (Azure AD). Wanneer u screen cast-O-Matic integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot screen cast-O-Matic.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij screen cast-O-Matic met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Screen cast-O-Matic eenmalige aanmelding (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Screen cast-O-Matic ondersteunt door **SP** GEÏNITIEERDe SSO
* Screen cast-O-Matic ondersteunt **just-in-time** -gebruikers inrichting

## <a name="adding-screencast-o-matic-from-the-gallery"></a>Screen cast-O-Matic toevoegen uit de galerie

Als u de integratie van screen cast-O-Matic in azure AD wilt configureren, moet u screen cast-O-Matic van de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in het gedeelte **toevoegen vanuit de galerie** de tekst **Screen cast-O-Matic** in het zoekvak.
1. Selecteer **Screen cast-O-Matic** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-screencast-o-matic"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor screen cast-O-Matic

Azure AD SSO configureren en testen met Screen cast-O-Matic met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in screen cast-O-Matic.

Als u Azure AD SSO wilt configureren en testen met Screen cast-O-Matic, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Configureer screen cast-O-Matic SSO](#configure-screencast-o-matic-sso)** -om de instellingen voor eenmalige aanmelding aan de kant van de toepassing te configureren.
    * **[Maak een screen cast-O-Matic-test gebruiker](#create-screencast-o-matic-test-user)** -als u een equivalent van B. Simon wilt hebben in screen cast-O-Matic dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **Screen cast-O-Matic-** toepassings integratie de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://screencast-o-matic.com/<InstanceName>`

    > [!NOTE]
    > De waarde is niet echt. Werk de waarde bij met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteunings team van screen cast-O-Matic](mailto:support@screencast-o-matic.com) om de waarde op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer op de sectie **Screen cast-O-Matic instellen** de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam** `B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan screen cast-O-Matic.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Screen cast-O-Matic**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-screencast-o-matic-sso"></a>Screen cast-O-Matic SSO configureren

1. Als u de configuratie wilt automatiseren in screen cast-O-Matic, moet u de **uitbrei ding mijn apps Secure Sign-in browser** installeren door te klikken op **de uitbrei ding installeren**.

    ![Uitbrei ding voor mijn apps](common/install-myappssecure-extension.png)

1. Nadat u de extensie aan de browser hebt toegevoegd, klikt u op **Screen cast-o-Matic instellen** wordt u naar de Screen cast-o-Matic-toepassing geleid. Geef de beheerders referenties op om u aan te melden bij screen cast-O-Matic. Met de browser uitbreiding wordt de toepassing automatisch voor u geconfigureerd en wordt stap 3-11 geautomatiseerd.

    ![Configuratie van Setup](common/setup-sso.png)

1. Als u screen cast-O-Matic hand matig wilt instellen, opent u een nieuw webbrowser venster en meldt u zich aan bij uw bedrijfs site screen cast-O-Matic als beheerder en voert u de volgende stappen uit:

1. Klik op **abonnement**.

    ![Het abonnement](./media/screencast-tutorial/tutorial_screencast_sub.png)

1. Klik onder de sectie **toegangs pagina** op **instellen**.

    ![De toegang](./media/screencast-tutorial/tutorial_screencast_setup.png)

1. Voer de volgende stappen uit op de **pagina toegang instellen**.

1. Typ in het gedeelte **toegangs-URL** naar INSTANCENAME in het opgegeven tekstvak.

    ![De toegang](./media/screencast-tutorial/tutorial_screencast_access.png)

1. Selecteer de sectie **domein gebruiker vereisen** onder **SAML-gebruikers beperking (optioneel)** .

1. Klik onder **IDP XML-bestand voor meta gegevens uploaden**op **bestand kiezen** om de meta gegevens te uploaden die u hebt gedownload van Azure Portal.

1. Klik op **OK**.

    ![De toegang](./media/screencast-tutorial/tutorial_screencast_save.png)

### <a name="create-screencast-o-matic-test-user"></a>Screen cast-O-Matic-test gebruiker maken

In deze sectie wordt een gebruiker met de naam Julia Simon gemaakt in screen cast-O-Matic. Screen cast-O-Matic ondersteunt just-in-time-gebruikers inrichting, dat standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in screen cast-O-Matic, wordt er een nieuwe gemaakt na verificatie. Als u hand matig een gebruiker moet maken, neemt u contact op met [het ondersteunings team van screen cast-O-Matic](mailto:support@screencast-o-matic.com).

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel screen cast-O-Matic in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Screen cast-O-Matic waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer screen cast-O-Matic met Azure AD](https://aad.portal.azure.com/)