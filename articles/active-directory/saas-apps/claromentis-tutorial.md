---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Claromentis | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Claromentis.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7980e0c5-e4d8-4678-afa2-7ec219432114
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/04/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c6eaf23950f83592a33709574be464f4499f0ab
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74823219"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-claromentis"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Claromentis

In deze zelf studie leert u hoe u Claromentis integreert met Azure Active Directory (Azure AD). Wanneer u Claromentis integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Claromentis.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Claromentis met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Claromentis-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Claromentis ondersteunt SSO die door **SP en IDP** is geïnitieerd
* Claromentis ondersteunt **just-in-time** -gebruikers inrichting

## <a name="adding-claromentis-from-the-gallery"></a>Claromentis toevoegen uit de galerie

Als u de integratie van Claromentis in azure AD wilt configureren, moet u Claromentis uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Claromentis** in het zoekvak.
1. Selecteer **Claromentis** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-claromentis"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Claromentis

Azure AD SSO met Claromentis configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Claromentis.

Als u Azure AD SSO wilt configureren en testen met Claromentis, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[CLAROMENTIS SSO configureren](#configure-claromentis-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een Claromentis-test gebruiker](#create-claromentis-test-user)** -om een equivalent van B. Simon in Claromentis te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **Claromentis** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. Voer in het tekstvak **id** de id-waarde in volgens uw organisatie vereiste.

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<customer_site_url>/custom/loginhandler/simplesaml/www/module.php/saml/sp/saml2-acs.php/claromentis`

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: 

    | | |
    |-|-|
    | `https://<customer_site_url>/login`|
    | `https://<customer_site_url>/login?no_auto=0`|

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de daad werkelijke id, de antwoord-URL en de aanmeldings-URL die verderop in de Turorial wordt uitgelegd.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Op de sectie **Claromentis instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Claromentis.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Claromentis**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-claromentis-sso"></a>Claromentis SSO configureren

1. Meld u in een ander browser venster als beheerder aan bij de Claromentis-website.

1. Klik op het **pictogram toepassingen** en selecteer **beheerder**.

    ![Claromentis-configuratie](./media/claromentis-tutorial/config1.png)

1. Selecteer tabblad **aangepaste aanmeldings-handler** .

    ![Claromentis-configuratie](./media/claromentis-tutorial/config2.png)

1. Selecteer **SAML-configuratie**.

    ![Claromentis-configuratie](./media/claromentis-tutorial/config3.png)

1. Ga op het tabblad **SAML-configuratie** omlaag naar het gedeelte **configuratie** en voer de volgende stappen uit:

    ![Claromentis-configuratie](./media/claromentis-tutorial/config4.png)

    a. Voer in het tekstvak **technische contact naam** de naam van de technische contact persoon in.

    b. Voer het e-mail adres van de technische contact persoon in het tekstvak voor **technische contact** personen in.

    c. Geef het wacht woord op in het tekstvak **auth Administrator-wacht woord** .

1. Schuif omlaag naar **auth-bronnen** en voer de volgende stappen uit:

    ![Claromentis-configuratie](./media/claromentis-tutorial/config5.png)

    a. Voer in het tekstvak **IDP** de id-waarde van **Azure AD** in die u hebt gekopieerd van de Azure Portal.

    b. Voer in het tekstvak **Entiteits-ID** de waarde voor de ENTITEITS-id in.

    c. Upload het **XML-bestand met federatieve meta gegevens** , dat u hebt gedownload van de Azure Portal.

    d. Klik op **Opslaan**.

1. U ziet nu dat alle Url's zijn ingevuld in de sectie **ID-provider** in de sectie **SAML-configuratie** .

    ![Claromentis-configuratie](./media/claromentis-tutorial/config6.png)

    a. Kopieer **-id-waarde (Entiteits-ID)** , plak deze waarde in het tekstvak **id** in het gedeelte **basis configuratie van SAML** in azure Portal.

    b. Kopieer de waarde van de **antwoord-URL** , plak deze waarde in het tekstvak **antwoord-URL** in het gedeelte basis- **SAML-configuratie** in azure Portal.

    c. Kopieer **teken op URL** -waarde, plak deze waarde in het tekstvak voor de **aanmeldings-URL** in het gedeelte **basis-SAML-configuratie** in azure Portal.

### <a name="create-claromentis-test-user"></a>Claromentis-test gebruiker maken

In deze sectie wordt een gebruiker met de naam B. Simon gemaakt in Claromentis. Claromentis biedt ondersteuning voor Just-in-time-gebruikers inrichting, die standaard is ingeschakeld. Er is geen actie-item voor u in deze sectie. Als een gebruiker nog niet bestaat in Claromentis, wordt er een nieuwe gemaakt na verificatie.

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Claromentis in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Claromentis waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Claromentis met Azure AD](https://aad.portal.azure.com/)