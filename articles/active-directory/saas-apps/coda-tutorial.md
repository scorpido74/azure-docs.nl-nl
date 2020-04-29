---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met CODA | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en CODA.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: acaf2012-ef2e-4ce0-8467-ceece3bae50e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/23/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 74da278dbbc0ac32407c345524e224ca5f7616da
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77194598"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-coda"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met CODA

In deze zelf studie leert u hoe u CODA kunt integreren met Azure Active Directory (Azure AD). Wanneer u CODA integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot CODA.
* Uw gebruikers in staat stellen om automatisch te worden aangemeld bij CODA met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* CODA (eenmalige aanmelding) ingeschakeld abonnement (Enter prise) met GDrive-integratie uitgeschakeld. Neem contact op met het [CODA-ondersteunings team](mailto:support@coda.io) om Gdrive-integratie voor uw organisatie uit te scha kelen als deze momenteel is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* CODA ondersteunt SSO met **IDP** -started

* CODA ondersteunt **just-in-time** -gebruikers inrichting

* Zodra u CODA hebt geconfigureerd, kunt u sessie besturings elementen afdwingen waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie besturings elementen worden uitgebreid vanuit voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-coda-from-the-gallery"></a>CODA toevoegen vanuit de galerie

Als u de integratie van CODA wilt configureren in azure AD, moet u CODA vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **CODA** in het zoekvak.
1. Selecteer in het paneel **CODA** in resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-coda"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor CODA

Azure AD SSO met CODA configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in CODA.

Als u Azure AD SSO wilt configureren en testen met CODA, voltooit u de volgende bouw stenen:

1. **[Begin met het configureren van CODA SSO](#begin-configuration-of-coda-sso)** -om de configuratie van SSO in CODA te starten.
1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
   * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
   * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[CODA-SSO configureren](#configure-coda-sso)** : voor het volt ooien van de configuratie van instellingen voor eenmalige aanmelding in CODA.
   * **[Maak een CODA-test gebruiker](#create-coda-test-user)** -om een equivalent van B. Simon te hebben in CODA dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="begin-configuration-of-coda-sso"></a>Configuratie van CODA SSO starten

Volg deze stappen in CODA om te beginnen.

1. Open in CODA het deel venster **organisatie-instellingen** .

   ![Organisatie-instellingen openen](media/coda-tutorial/org-settings.png)

1. Zorg ervoor dat GDrive-integratie is uitgeschakeld voor uw organisatie. Als deze momenteel is ingeschakeld, neemt u contact op met het [CODA-ondersteunings team](mailto:support@coda.io) om te helpen bij het migreren van Gdrive.

   ![GDrive is uitgeschakeld](media/coda-tutorial/gdrive-off.png)

1. Selecteer onder **verifiëren met SSO (SAML)** de optie **SAML configureren** .

   ![SAML-instellingen](media/coda-tutorial/saml-settings-link.png)

1. Noteer de waarden voor de **Entiteits-ID** en de **SAML-respons-URL**, die u in de volgende stappen nodig hebt.

   ![Entiteit-ID en SAML-antwoord-URL voor gebruik in azure](media/coda-tutorial/azure-settings.png)

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/)naar de pagina **CODA** -toepassings integratie en selecteer de sectie voor het **beheren** van **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer op de pagina **eenmalige aanmelding met SAML instellen** de waarden in voor de volgende velden:

   a. Voer in het tekstvak **id** de tekst ' entiteit-id ' in. Dit moet het patroon volgen:`https://coda.io/samlId/<CUSTOMID>`

   b. Voer in het tekstvak **antwoord-URL** het selectie vakje ' SAML-antwoord-URL ' in. Dit moet het patroon volgen:`https://coda.io/login/sso/saml/<CUSTOMID>/consume`

   > [!NOTE]
   > Uw waarden verschillen van bovenstaande. u kunt uw waarden vinden in de ' SAML configureren '-console. Werk deze waarden bij met de werkelijke id en antwoord-URL.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

   ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **CODA instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang aan CODA toe te kennen.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **CODA**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

   ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-coda-sso"></a>CODA SSO configureren

Als u de installatie wilt volt ooien, voert u waarden in Azure Active Directory in het SAML-paneel CODA **configureren** in.

1. Open in CODA het deel venster **organisatie-instellingen** .
1. Selecteer onder **verifiëren met SSO (SAML)** de optie **SAML configureren** .
1. Stel de **SAML-provider** in op **Azure Active Directory**.
1. Plak de **aanmeldings-URL** in de Azure-console in de AANMELDINGS-URL van de **identiteits provider**.
1. Plak in de uitgever van de **identiteits provider**de **Azure ad-id** uit de Azure-console.
1. Selecteer in het **open bare certificaat van de identiteits provider**de optie **certificaat uploaden** en selecteer het certificaat bestand dat u eerder hebt gedownload.
1. Selecteer **Opslaan**.

Hiermee wordt het werk voltooid dat nodig is voor de installatie van de SAML SSO-verbinding.

### <a name="create-coda-test-user"></a>CODA-test gebruiker maken

In deze sectie wordt een gebruiker met de naam Julia Simon in CODA gemaakt. CODA ondersteunt just-in-time-gebruikers inrichting, dat standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in CODA, wordt er na de verificatie een nieuwe gemaakt.

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de CODA-tegel in het toegangs venster klikt, moet u automatisch worden aangemeld bij de CODA waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer CODA met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [CODA beveiligen met geavanceerde zicht baarheid en controles](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
