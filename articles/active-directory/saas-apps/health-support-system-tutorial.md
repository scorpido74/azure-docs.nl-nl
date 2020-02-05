---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met het Health-ondersteunings systeem | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en het Health-ondersteunings systeem.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 96f35ded-07bc-46f0-87df-fe88fcc367cd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/29/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cf449880c1a7d1fdd1f951bf5ecbb7316efb8e8
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76992336"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-health-support-system"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met het Health-ondersteunings systeem

In deze zelf studie leert u hoe u het Health-ondersteunings systeem kunt integreren met Azure Active Directory (Azure AD). Wanneer u het Health-ondersteunings systeem integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot het Health-ondersteunings systeem.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij het Health-ondersteunings systeem met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Health-ondersteunings systeem abonnement voor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Het Health-ondersteunings systeem ondersteunt door **SP** GEÏNITIEERDe SSO

* Zodra u het Health-ondersteunings systeem hebt geconfigureerd, kunt u sessie besturings elementen afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie besturings elementen worden uitgebreid vanuit voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-health-support-system-from-the-gallery"></a>Het Health-ondersteunings systeem toevoegen vanuit de galerie

Als u de integratie van het Health-ondersteunings systeem wilt configureren in azure AD, moet u het Health-ondersteunings systeem toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** het **Health-ondersteunings systeem** in het zoekvak.
1. Selecteer **Health-ondersteunings systeem** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-health-support-system"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor het Health-ondersteunings systeem

Azure AD SSO configureren en testen met het Health-ondersteunings systeem met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in het Health-ondersteunings systeem.

Als u Azure AD SSO wilt configureren en testen met het Health-ondersteunings systeem, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Health support-systeem-SSO configureren](#configure-health-support-system-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een test gebruiker voor het Health-ondersteunings systeem](#create-health-support-system-test-user)** , om een equivalent van B. Simon te hebben in het Health-ondersteunings systeem dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina voor de integratie **van de** systeem toepassing voor **status ondersteuning** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden: Typ in het tekstvak **URL voor aanmelding** de URL: `https://suntory.karakoko.jp`
    
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekening certificaat** op de knop kopiëren om de URL van de **app Federation-meta gegevens** te kopiëren en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

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

In deze sectie schakelt u B. Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan het Health-ondersteunings systeem.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen het **Health-ondersteunings systeem**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-health-support-system-sso"></a>Health support-systeem-SSO configureren

Als u eenmalige aanmelding wilt configureren op het **systeem van Health-ondersteuning** , moet u de URL voor de **federatieve meta gegevens** van de app verzenden naar het ondersteunings team van het [systeem](https://wellcoms.jp/inquiry/)voor ondersteuning. Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-health-support-system-test-user"></a>Test gebruiker voor Health-ondersteunings systeem maken

In deze sectie maakt u een gebruiker met de naam B. Simon in het Health-ondersteunings systeem. Werk met het ondersteunings team voor het [Health support-systeem](https://wellcoms.jp/inquiry/) om de gebruikers toe te voegen aan het systeem platform voor Health-ondersteuning. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel systeem status ondersteuning in het toegangs venster klikt, moet u automatisch worden aangemeld bij het Health-ondersteunings systeem waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer het Health-ondersteunings systeem met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Het Health-ondersteunings systeem beveiligen met geavanceerde zicht baarheid en controles](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)