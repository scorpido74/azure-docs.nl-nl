---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met SurveyMonkey Enter prise | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SurveyMonkey Enter prise.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a2d9b57-27b5-4d9f-b52c-09b692872cf7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4238033f94fe8bfdc677c9eb623a2eab3cdf371c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "72532919"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-surveymonkey-enterprise"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met SurveyMonkey Enter prise

In deze zelf studie leert u hoe u SurveyMonkey Enter prise integreert met Azure Active Directory (Azure AD). Wanneer u SurveyMonkey Enter prise integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot SurveyMonkey Enter prise.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij SurveyMonkey Enter prise met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* SurveyMonkey Enter prise single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* SurveyMonkey Enter prise ondersteunt door **IDP** GEÏNITIEERDe SSO

> [!NOTE]
> De id van deze toepassing is een vaste teken reeks waarde zodat slechts één exemplaar in één Tenant kan worden geconfigureerd.

## <a name="adding-surveymonkey-enterprise-from-the-gallery"></a>SurveyMonkey Enter prise toevoegen vanuit de galerie

Als u de integratie van SurveyMonkey Enter prise wilt configureren in azure AD, moet u SurveyMonkey Enter prise toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. In de sectie **toevoegen vanuit de galerie** typt u **surveymonkey Enter prise** in het zoekvak.
1. Selecteer **surveymonkey Enter prise** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-surveymonkey-enterprise"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor SurveyMonkey Enter prise

Azure AD SSO met SurveyMonkey Enter prise configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SurveyMonkey Enter prise.

Als u Azure AD SSO wilt configureren en testen met SurveyMonkey Enter prise, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Surveymonkey Enter PRISE SSO configureren](#configure-surveymonkey-enterprise-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    1. **[Maak surveymonkey Enter prise test User](#create-surveymonkey-enterprise-test-user)** -om een soort tegen te brengen van B. Simon in surveymonkey Enter prise dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **surveymonkey Enter prise** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **basis configuratie van SAML** is de toepassing vooraf geconfigureerd en de benodigde url's zijn al vooraf ingevuld met Azure. De gebruiker moet de configuratie opslaan door te klikken op de knop **Opslaan** .

1. SurveyMonkey Enter prise-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/edit-attribute.png)

6. SurveyMonkey Enter prise Application verwacht nog maar weinig kenmerken om te worden door gegeven in een SAML-respons die hieronder wordt weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereiste.

    | Naam | Bronkenmerk|
    | ---------------| --------------- |
    | E-mail | user.mail |
    | FirstName | user.givenname |
    | LastName | user.surname |

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Op de **surveymonkey Enter prise instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker in de Azure Portal met de naam B. Simon.

1. Selecteer in het linkerdeel venster van de Azure Portal **Azure Active Directory**, selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer in het veld **gebruikers naam** het username@companydomain.extensionin. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectie vakje **wacht woord weer geven** in en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Klik op **maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan SurveyMonkey Enter prise.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **surveymonkey Enter prise**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-surveymonkey-enterprise-sso"></a>SurveyMonkey Enter prise SSO configureren

Als u eenmalige aanmelding wilt configureren voor **surveymonkey Enter prise** Side, moet u het gedownloade **XML-bestand met federatieve meta gegevens** en de juiste gekopieerde url's verzenden van Azure Portal naar [surveymonkey Enter prise ondersteunings team](mailto:support@selerix.com). Het team stelt de instellingen zo in dat de verbinding tussen SAML en eenmalige aanmelding aan beide zijden goed is ingesteld.

### <a name="create-surveymonkey-enterprise-test-user"></a>SurveyMonkey Enter prise-test gebruiker maken

Het is niet nodig om een test gebruiker te maken in SurveyMonkey Enter prise. Gebruikers accounts worden ingericht als de gebruiker kiest voor het maken van een nieuw account, op basis van de SAML-bevestiging. Uw SurveyMonkey Enter prise-Manager voor klant succes biedt stappen om dit proces te volt ooien nadat uw Azure-meta gegevens zijn toegevoegd aan de SurveyMonkey-ondernemings configuratie en klaar zijn om te worden gevalideerd.

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SurveyMonkey Enter prise in het toegangs venster klikt, moet u automatisch worden aangemeld bij de SurveyMonkey-onderneming waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer SurveyMonkey Enter prise met Azure AD](https://aad.portal.azure.com/)