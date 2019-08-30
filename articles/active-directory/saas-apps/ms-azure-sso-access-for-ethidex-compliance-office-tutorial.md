---
title: 'Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met MS Azure SSO Access voor Ethidex-naleving Office™ | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en MS Azure SSO Access voor Ethidex-naleving Office™.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 58b2e70f-d1dd-47b6-b91f-f77581df01c6
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fa0ec97683fe8597ced234fe4b7251a4daeed49f
ms.sourcegitcommit: ee61ec9b09c8c87e7dfc72ef47175d934e6019cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/30/2019
ms.locfileid: "70173495"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ms-azure-sso-access-for-ethidex-compliance-office"></a>Zelfstudie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met MS Azure SSO Access voor Ethidex-naleving Office™

In deze zelf studie leert u hoe u MS Azure SSO Access integreert voor Ethidex-naleving Office™ met Azure Active Directory (Azure AD). Wanneer u MS Azure SSO Access integreert voor Ethidex-naleving Office™ met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot MS Azure SSO-toegang voor Ethidex-naleving Office™.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij MS Azure SSO-toegang voor Ethidex-naleving Office™ met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* MS Azure SSO-toegang voor Ethidex-naleving Office™ eenmalige aanmelding (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* MS Azure SSO-toegang voor Ethidex-naleving Office™ ondersteunt door **IDP** GEÏNITIEERDe SSO

## <a name="adding-ms-azure-sso-access-for-ethidex-compliance-office-from-the-gallery"></a>MS Azure SSO Access toevoegen voor Ethidex-naleving Office™ vanuit de galerie

Als u de integratie van MS Azure-SSO-toegang voor Ethidex-nalevings Office™ wilt configureren in azure AD, moet u MS Azure SSO Access voor Ethidex-nalevings Office™ toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ **MS Azure SSO Access voor Ethidex-naleving Office™** in het zoekvak in het gedeelte **toevoegen van de galerie** .
1. Selecteer **MS Azure SSO Access voor Ethidex-naleving Office™** van het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ms-azure-sso-access-for-ethidex-compliance-office"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor MS Azure SSO-toegang voor Ethidex-naleving Office™

Azure AD SSO configureren en testen met MS Azure SSO-toegang voor Ethidex-naleving Office™ met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in MS Azure SSO-toegang voor Ethidex-naleving Office™.

Als u Azure AD SSO wilt configureren en testen met MS Azure SSO-toegang voor Ethidex-naleving Office™, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[MS Azure SSO-toegang configureren voor Ethidex compliantie Office SSO](#configure-ms-azure-sso-access-for-ethidex-compliance-office-sso)** -voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak MS Azure SSO-toegang voor Ethidex-naleving Office test User](#create-ms-azure-sso-access-for-ethidex-compliance-office-test-user)** -om een soort tegen te gaan van B. Simon in MS Azure SSO Access voor Ethidex naleving Office™ dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina **MS Azure SSO Access for Ethidex compliance Office™** Application Integration de sectie **Manage (beheren** ) en selecteer eenmalige **aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **basis configuratie van SAML** is de toepassing vooraf geconfigureerd en de benodigde url's zijn al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door te klikken op de knop **Opslaan** .


1. MS Azure SSO-toegang voor Ethidex-naleving Office™ toepassings toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. MS Azure SSO-toegang voor Ethidex-naleving Office™ toepassing verwacht dat **nameidentifier** moet worden toegewezen aan **User. mail**, dus u moet de kenmerk toewijzing bewerken door op het pictogram **bewerken** te klikken en de kenmerk toewijzing te wijzigen.

    ![image](common/edit-attribute.png)

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , **certificaat (RAW)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De downloadkoppeling certificaat](common/certificateraw.png)

1. Op de sectie **MS Azure SSO Access instellen voor Ethidex-naleving Office™** , kopieer de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot MS Azure SSO-toegang voor Ethidex-naleving Office™.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **MS Azure-SSO-toegang voor Ethidex-naleving Office™**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling 'Gebruikers en groepen'](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-ms-azure-sso-access-for-ethidex-compliance-office-sso"></a>MS Azure SSO-toegang configureren voor Ethidex-naleving Office SSO

Voor het configureren van eenmalige aanmelding op **MS Azure SSO-toegang voor Ethidex-naleving office™** zijde, moet u het gedownloade **certificaat (RAW)** en de juiste gekopieerde url's verzenden van Azure Portal naar [MS Azure SSO Access voor Ethidex-naleving Office™ ondersteunings team](mailto:support@ethidex.com). Ze stelt u deze optie om de SAML SSO-verbinding instellen goed aan beide zijden.

### <a name="create-ms-azure-sso-access-for-ethidex-compliance-office-test-user"></a>MS Azure-SSO-toegang voor Ethidex-naleving Office-test gebruiker maken

In deze sectie maakt u een gebruiker met de naam B. Simon in MS Azure SSO Access voor Ethidex-naleving Office™. Werk met [MS Azure SSO Access voor Ethidex-naleving office™](mailto:support@ethidex.com) -ondersteunings team om de gebruikers toe te voegen aan de MS Azure SSO-toegang voor Ethidex-naleving Office™ platform. Gebruikers moeten worden gemaakt en worden geactiveerd voordat u eenmalige aanmelding gebruiken.

## <a name="test-sso"></a>SSO testen 

In deze sectie maakt testen u uw Azure AD eenmalige aanmelding configuratie met behulp van het toegangsvenster.

Wanneer u op de tegel MS Azure SSO Access voor Ethidex-naleving Office™ in het toegangs venster klikt, moet u automatisch worden aangemeld bij de MS Azure SSO-toegang voor Ethidex-naleving Office™ waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende resources

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer MS Azure SSO Access uit voor Ethidex-naleving Office™ met Azure AD](https://aad.portal.azure.com/)

