---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met CylancePROTECT | Microsoft Docs'
description: Ontdek hoe u eenmalige aanmelding configureert tussen Azure Active Directory en CylancePROTECT.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ea392d8c-c8aa-4475-99d0-b08524ef0f3a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/10/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1df0a295cb48925587e9741fa29d4d02376441a8
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76983498"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cylanceprotect"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met CylancePROTECT

In deze zelf studie leert u hoe u CylancePROTECT integreert met Azure Active Directory (Azure AD). Wanneer u CylancePROTECT integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot CylancePROTECT.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij CylancePROTECT met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* CylancePROTECT-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* CylancePROTECT ondersteunt door **IDP** geïnitieerde SSO

## <a name="adding-cylanceprotect-from-the-gallery"></a>CylancePROTECT toevoegen vanuit de galerie

Voor het configureren van de integratie van CylancePROTECT in Azure AD, moet u CylancePROTECT vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **CylancePROTECT** in het zoekvak.
1. Selecteer **CylancePROTECT** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cylanceprotect"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor CylancePROTECT

Azure AD SSO met CylancePROTECT configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in CylancePROTECT.

Als u Azure AD SSO wilt configureren en testen met CylancePROTECT, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[CYLANCEPROTECT SSO configureren](#configure-cylanceprotect-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een CylancePROTECT-test gebruiker](#create-cylanceprotect-test-user)** -om een equivalent van B. Simon in CylancePROTECT te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **CylancePROTECT** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **eenmalige aanmelding met SAML instellen** de waarden in voor de volgende velden:

    a. Typ de URL in het tekstvak **Id**:
    
    | Regio | URL-waarde |
    |----------|---------|
    | Azië en Stille Oceaan - noordoost (APNE1)| `https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Azië en Stille Oceaan - zuidoost (AU) | `https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml` |
    | Centraal-Europa (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Noord-Amerika|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Zuid-Amerika (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

    b. Typ de URL in het tekstvak **Antwoord-URL**:
    
    | Regio | URL-waarde |
    |----------|---------|
    | Azië en Stille Oceaan - noordoost (APNE1)|`https://login-apne1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Azië en Stille Oceaan - zuidoost (AU)|`https://login-au.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Centraal-Europa (EUC1)|`https://login-euc1.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Noord-Amerika|`https://login.cylance.com/EnterpriseLogin/ConsumeSaml`|
    | Zuid-Amerika (SAE1)|`https://login-sae1.cylance.com/EnterpriseLogin/ConsumeSaml`|

1. De CylancePROTECT-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding ziet u de lijst met standaardkenmerken, waarbij **nameidentifier** is toegewezen aan **user.userprincipalname**. De CylancePROTECT-toepassing verwacht dat **nameidentifier** moet worden toegewezen aan de **gebruiker. mail** en alle overige resterende claims verwijderen. u moet dus de kenmerk toewijzing bewerken door op het pictogram **bewerken** te klikken en de kenmerk toewijzing te wijzigen.

    ![installatiekopie](common/edit-attribute.png)

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **CylancePROTECT instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

> [!NOTE]
> Open het gedownloade base64-gecodeerde certificaat in een tekst editor en kopieer alleen de tekst tussen de **begin** -en **eind** codes om in de Cylance-beheer portal te plakken.

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** de username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord**.
   1. Klik op **Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan CylancePROTECT.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **CylancePROTECT** in de lijst met toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-cylanceprotect-sso"></a>CylancePROTECT SSO configureren

Als u eenmalige aanmelding wilt configureren op **CylancePROTECT** , moet u het gedownloade **certificaat (base64)** en de juiste gekopieerde url's verzenden van Azure Portal naar [CylancePROTECT ondersteunings team](mailto:Ibrahim.nafea@toyota.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld. Gebruik Cylance-documentatie voor meer informatie: [https://support.cylance.com/s/article/Use-Microsoft-Azure-with-CylancePROTECT4](https://support.cylance.com/s/article/Use-Microsoft-Azure-with-CylancePROTECT4).

### <a name="create-cylanceprotect-test-user"></a>Een CylancePROTECT-testgebruiker maken

In deze sectie maakt u een gebruiker met de naam Britta Simon in CylancePROTECT. Werk samen met de console beheerder om de gebruikers toe te voegen aan het CylancePROTECT-platform. De houder van het Azure Active Directory-account ontvangt een e-mail en volgt een koppeling om zijn account te bevestigen voordat het actief wordt.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u in het toegangsvenster op de tegel CylancePROTECT klikt, zou u automatisch moeten worden aangemeld bij het exemplaar van CylancePROTECT waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer CylancePROTECT met Azure AD](https://aad.portal.azure.com/)