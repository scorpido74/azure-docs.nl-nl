---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met de supersfeer | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en de supersfeer.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: afc04efa-2eba-4e47-8ce4-b71eb293cd09
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 12/31/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: acb3704541e4c573da4d5331ee10a9c7256162e1
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75640156"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-supermood"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met de supersfeer

In deze zelf studie leert u hoe u de supersfeer integreert met Azure Active Directory (Azure AD). Wanneer u de supersfeer van Azure AD integreert, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de supersfeer.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij supersfeer met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Superpunte eenmalige aanmelding (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Supersfeer ondersteunt SSO die door **SP en IDP** is gestart
* Supersfeer biedt ondersteuning **voor Just-in-time** -gebruikers inrichting

## <a name="adding-supermood-from-the-gallery"></a>Supersfeer toevoegen vanuit de galerie

Als u de integratie van de supersfeer in azure AD wilt configureren, moet u de supersfeer van de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. **Typ in** de sectie **toevoegen vanuit de galerie** de tekst superpunt in het zoekvak.
1. Selecteer **supersfeer** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-supermood"></a>Eenmalige aanmelding voor Azure AD voor de supersfeer configureren en testen

Configureer en test Azure AD SSO met de supersfeer van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in de hoofd sfeer.

Als u Azure AD SSO wilt configureren en testen met een supersfeer, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. De **[supersfeer SSO configureren](#configure-supermood-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Test gebruiker van supersfeer maken](#create-supermood-test-user)** : als u een equivalent van B. Simon wilt hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina voor de integratie van de **supersfeer** toepassing en selecteer de sectie voor het **beheren** van **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus van **IDP** wilt configureren, voert u de volgende stappen uit in de sectie **basis configuratie van SAML** :

    a. Schakel **extra Url's instellen**in.
    
    b. Typ een URL in het tekstvak **Relay-status** :`https://supermood.co/auth/sso/saml20`

1. Klik op **Extra URL's instellen** en voer de volgende stappen uit als u de toepassing in de met **SP** geïnitieerde modus wilt configureren:

    Typ in het tekstvak **URL voor aanmelding** de URL:`https://supermood.co/app/#!/loginv2`

1. Klik op **Opslaan**.

1. Bij een supersfeer toepassing worden de SAML-beweringen in een specifieke indeling verwacht. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. In aanvulling op de bovenstaande wordt verwacht dat er nog maar enkele kenmerken worden door gegeven aan de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    | Naam | Bronkenmerk|
    | ---------------| ------|
    | voornaam | user.givenname |
    | achternaam | user.surname |

1. Klik op de pagina **eenmalige aanmelding met SAML instellen** in het gedeelte **SAML-handtekening certificaat** op de knop kopiëren om de URL van de **app Federation-meta gegevens** te kopiëren en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/copy-metadataurl.png)

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de supersfeer.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **supersfeer**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-supermood-sso"></a>Supersfeer SSO configureren

1. Ga naar het Supermood.co-beheerders paneel als beveiligings beheerder.

1. Klik op **Mijn account** (links linksonder) en **eenmalige aanmelding (SSO)**.

    ![Het certificaat single](./media/supermood-tutorial/tutorial_supermood_single.png)

1. Klik in **uw saml 2,0-configuraties**op **een SAML 2,0-configuratie toevoegen voor een e-mail domein**.

    ![Het certificaat toevoegen](./media/supermood-tutorial/tutorial_supermood_add.png)

1. In **een SAML 2,0-configuratie toevoegen voor een e-mail domein**. voert u de volgende stappen uit:

    ![De certificaat-SAML](./media/supermood-tutorial/tutorial_supermood_saml.png)

    a. Typ uw domein in het tekstvak **e-mail domein voor deze id-provider** .

    b. Plak in het tekstvak **URL voor meta gegevens gebruiken** de **URL voor de federatieve meta gegevens** van de App die u van Azure Portal hebt gekopieerd.

    c. Klik op **Add**.

### <a name="create-supermood-test-user"></a>Test gebruiker van supersfeer maken

In deze sectie wordt een gebruiker met de naam Julia Simon gemaakt in supersfeer. Supersfeer biedt ondersteuning voor Just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker niet al aanwezig is in de supersfeer, wordt er na verificatie een nieuwe gemaakt. Als u hand matig een gebruiker moet maken, neemt u contact op met het [ondersteunings team van de supersfeer](mailto:hello@supermood.fr).

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel supersfeer in het toegangs venster klikt, moet u automatisch worden aangemeld bij de supersfeer waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer het met Azure AD](https://aad.portal.azure.com/)