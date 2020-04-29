---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Helpshift | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Helpshift.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 114de95d-e9a7-4f87-b14d-54b91a63ce49
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 12/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f7e932a3b71e802c5b814f6dfb59922148c2519
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75533872"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-helpshift"></a>Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Helpshift

In deze zelf studie leert u hoe u Helpshift integreert met Azure Active Directory (Azure AD). Wanneer u Helpshift integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Helpshift.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Helpshift met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Helpshift-abonnement dat is ingeschakeld voor eenmalige aanmelding (SSO).

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Helpshift ondersteunt SSO die door **SP en IDP** is geïnitieerd

## <a name="adding-helpshift-from-the-gallery"></a>Helpshift toevoegen uit de galerie

Als u de integratie van Helpshift in azure AD wilt configureren, moet u Helpshift uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Helpshift** in het zoekvak.
1. Selecteer **Helpshift** uit het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-helpshift"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Helpshift

Azure AD SSO met Helpshift configureren en testen met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Helpshift.

Als u Azure AD SSO wilt configureren en testen met Helpshift, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[HELPSHIFT SSO configureren](#configure-helpshift-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak een Helpshift-test gebruiker](#create-helpshift-test-user)** -om een equivalent van B. Simon in Helpshift te hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina Toepassings integratie van **Helpshift** de sectie **beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. Typ in het tekstvak **id** een URL met het volgende patroon:`https://<YourDOMAIN>.helpshift.com/`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<YourDOMAIN>.helpshift.com/login/saml/acs/`

1. Klik op **Extra URL's instellen** en voer de volgende stappen uit als u de toepassing in de met **SP** geïnitieerde modus wilt configureren:

    d. Typ in het tekstvak **URL voor aanmelding** een URL met het volgende patroon:`https://<YourDOMAIN>.helpshift.com/login/saml/idp-login/`

    e. In het tekstvak **Relaystatus** typt u een URL met de volgende notatie: `https://<YourDOMAIN>.helpshift.com/`

    > [!NOTE]
    > Dit zijn geen echte waarden. Deze waarden bijwerken met de daad werkelijke id, antwoord-URL, aanmeldings-URL en relay-status. Neem contact op met het [ondersteunings team van Helpshift-clients](mailto:support@helpshift.com) om deze waarden te verkrijgen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **Helpshift instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Helpshift.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Helpshift**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-helpshift-sso"></a>Helpshift SSO configureren

1. Meld u in een andere webbrowser aan bij uw Helpshift-toepassing als beheerder.

1. Open het Helpshift- **dash board** en klik op het **pictogram instellingen**.

    ![De Helpshift-configuratie](./media/helpshift-tutorial/configuration01.png)

1. Klik op het tabblad **integraties** en voer de volgende stappen uit:

    ![De Helpshift-configuratie](./media/helpshift-tutorial/configuration02.png)

    a. Schakel **eenmalige aanmelding (SAML – SSO)** in.

    b. Selecteer de **ID-provider (IDP)** als **Azure Active Directory**.

    c. Plak in het tekstvak URL van het **SAML 2,0-eind punt** de waarde van de **aanmeldings-URL** , die u hebt gekopieerd uit de Azure Portal.

    d. Open het gedownloade **certificaat bestand (base64)** in Klad blok, kopieer de inhoud van het bestand (zonder het ' — – begin certificaat,-' en '-–-eind certificaat ' – ' lijnen) en plak deze in het tekstvak **X. 509-certificaat** .

    e. Plak in het tekstvak **URL van uitgever** de id-waarde van **Azure AD** , die u hebt gekopieerd uit de Azure Portal.

    f. Klik op **wijzigingen Toep assen**.

### <a name="create-helpshift-test-user"></a>Helpshift-test gebruiker maken

In deze sectie maakt u een gebruiker met de naam B. Simon in Helpshift. Werk samen met het [Helpshift-client ondersteunings team](mailto:support@helpshift.com) om de gebruikers toe te voegen in het Helpshift-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Helpshift in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Helpshift waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Helpshift met Azure AD](https://aad.portal.azure.com/)