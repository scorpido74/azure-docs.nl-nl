---
title: 'Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met school bord leren | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en Blackboard Learn.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0b8ca505-61ea-487c-9a3e-fa50c936df0c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e65ede5677ccb29e7c30eb575b782064fb719034
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193700"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-blackboard-learn"></a>Zelfstudie: Azure Active Directory SSO-integratie (single sign-on) met school bord leren

In deze zelf studie leert u hoe u school leren integreert met Azure Active Directory (Azure AD). Wanneer u school bord integreert met Azure AD, kunt u het volgende doen:

* Beheer in azure AD die toegang heeft tot school cursus.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij school bord met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* School bord meer informatie over eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Blackboard Learn ondersteunt door **SP** geïnitieerde eenmalige aanmelding
* Blackboard Learn biedt ondersteuning voor **Just In Time**-gebruikersinrichting


## <a name="adding-blackboard-learn-from-the-gallery"></a>Blackboard Learn toevoegen vanuit de galerie

Als u de integratie van Blackboard Learn met Azure AD wilt configureren, dient u Blackboard Learn vanuit de galerie toe te voegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **school bord leer** in het zoekvak.
1. Selecteer **school bord informatie** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-blackboard-learn"></a>Eenmalige aanmelding voor Azure AD voor school bord configureren en testen

Azure AD SSO configureren en testen met school bord meer informatie over het gebruik van een test gebruiker met de naam **B. Simon**. Voor een goede werking van SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in school leer.

Als u Azure AD SSO wilt configureren en testen met school bord, moet u de volgende bouw stenen volt ooien:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[School bord configureren Learn SSO](#configure-blackboard-learn-sso)** -voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[School bord leren gebruiker testen](#create-blackboard-learn-test-user)** : voor een soort tegen partij B. Simon in school leer dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina **school leer informatie over** de integratie van toepassingen en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. In het tekstvak **Aanmeldings-URL** typt u een URL met de volgende notatie: `https://<subdomain>.blackboard.com/`

    b. In het tekstvak **Id (Entiteits-id)** typt u een URL met de volgende notatie: `https://<subdomain>.blackboard.com/auth-saml/saml/SSO/entity-id/SAML_AD`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke aanmeldings-URL en id. Neem contact op met het [klantondersteuningsteam van Blackboard Learn](https://www.blackboard.com/support/index.aspx) om deze waarden op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De downloadkoppeling certificaat](common/metadataxml.png)

1. Kopieer op de sectie **school bord instellen** de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Maak een testgebruiker Azure AD

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** aan de bovenkant van het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam** `B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Create**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in voor het gebruik van eenmalige aanmelding voor Azure door toegang te verlenen aan school leer.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **Blackboard Learn** in de lijst met toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling 'Gebruikers en groepen'](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-blackboard-learn-sso"></a>School bord informatie over eenmalige aanmelding configureren

Als u eenmalige aanmelding op **school bord wilt** configureren, volgt u de [koppeling](https://help.blackboard.com/Learn/Administrator/SaaS/Authentication/Implement_Authentication/SAML_Authentication_Provider_Type). Als u een probleem ondervindt tijdens het configureren, kunt u contact opnemen met het ondersteunings [team van school informatie](https://www.blackboard.com/support/index.aspx).


### <a name="create-blackboard-learn-test-user"></a>Testgebruiker van Blackboard Learn maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in Blackboard Learn. Ondersteuning van Blackboard Learn-toepassing voor Just In Time-gebruikersinrichting. Zorg ervoor dat u de claims hebt geconfigureerd zoals beschreven in de sectie **eenmalige aanmelding voor Azure AD configureren**.

## <a name="test-sso"></a>SSO testen

In deze sectie maakt testen u uw Azure AD eenmalige aanmelding configuratie met behulp van het toegangsvenster.

Wanneer u op de tegel Blackboard Learn in het toegangsvenster klikt, zou u automatisch moeten worden aangemeld bij de instantie van Blackboard Learn waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer school meer met Azure AD](https://aad.portal.azure.com/)