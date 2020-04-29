---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met de Netskope-beheer console | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Netskope-beheer console.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e286adec-8d19-4d41-9afa-a2e39d7a5983
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/31/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c38900d4ded3d2ee08245674bda90d96226c1eb
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80396559"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netskope-administrator-console"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met de Netskope-beheer console

In deze zelf studie leert u hoe u de Netskope-beheer console integreert met Azure Active Directory (Azure AD). Wanneer u Netskope-beheer console integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de Netskope-beheer console.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij de Netskope-beheerders console met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Netskope-beheer console, eenmalige aanmelding (SSO), ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* De Netskope-beheer console ondersteunt SSO die door **SP en IDP** is gestart

## <a name="adding-netskope-administrator-console-from-the-gallery"></a>De Netskope-beheer console toevoegen vanuit de galerie

Als u de integratie van de Netskope-beheer console wilt configureren in azure AD, moet u de Netskope-beheer console vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ **Netskope Administrator console** in het zoekvak van de sectie **toevoegen vanuit de galerie** .
1. Selecteer **Netskope Administrator console** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netskope-administrator-console"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor de Netskope-beheer console

Azure AD SSO configureren en testen met Netskope-beheer console met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in de Netskope-beheer console.

Als u Azure AD SSO wilt configureren en testen met Netskope-beheer console, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[Netskope Administrator-console-SSO configureren](#configure-netskope-administrator-console-sso)** : Hiermee configureert u de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak Netskope Administrator-console test gebruiker](#create-netskope-administrator-console-test-user)** -om een equivalent van B. Simon te hebben in de Netskope-beheer console die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in het [Azure Portal](https://portal.azure.com/)op de pagina **Netskope Administrator console** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Als u de toepassing in de gestarte modus **IDP** wilt configureren, voert u in de sectie **basis configuratie van SAML** de waarden voor de volgende velden in:

    a. Typ in het tekstvak **id** een URL met het volgende patroon:`<OrgKey>`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<tenant_host_name>/saml/acs`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Deze waarden worden verderop in de zelf studie uitgelegd.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `https://<tenantname>.goskope.com`

    > [!NOTE]
    > De waarden voor de aanmeldings-URL zijn niet echt. URL-waarde voor aanmelding bijwerken met de werkelijke aanmeldings-URL. Neem contact op met het [ondersteunings team van de Netskope-beheerders console](mailto:support@netskope.com) om de AANMELDINGS-URL op te halen. U kunt ook verwijzen naar het patroon dat wordt weergegeven in de sectie **Standaard SAML-configuratie** in de Azure-portal.

1. De Netskope-beheer console toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. In aanvulling op de bovenstaande, verwacht de Netskope-beheer console toepassing nog maar weinig kenmerken meer door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    | Naam |  Bronkenmerk|
    | ---------| --------- |
    | beheerder-rol | user.assignedroles |

    > [!NOTE]
    > Klik [hier](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) voor meer informatie over het maken van rollen in azure AD.

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer op de sectie **Netskope-beheer console instellen** de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om de eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan de Netskope-beheer console.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Netskope Administrator-console**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-netskope-administrator-console-sso"></a>SSO van de Netskope-beheer console configureren

1. Open een nieuw tabblad in uw browser en meld u aan bij de bedrijfs site van uw Netskope-beheerders console als beheerder.

1. Klik op het tabblad **instellingen** in het navigatie deel venster aan de linkerkant.

    ![Configuratie van Netskope-beheer console](./media/netskope-cloud-security-tutorial/config-settings.png)

1. Klik op het tabblad **beheer** .

    ![Configuratie van Netskope-beheer console](./media/netskope-cloud-security-tutorial/config-administration.png)

1. Klik op het tabblad **SSO** .

    ![Configuratie van Netskope-beheer console](./media/netskope-cloud-security-tutorial/config-sso.png)

1. Voer de volgende stappen uit in de sectie **netwerk instellingen** :
    
    ![Configuratie van Netskope-beheer console](./media/netskope-cloud-security-tutorial/config-pasteurls.png)

    a. Kopieer de URL-waarde voor de **bewering van de Consumer-Service** en plak deze in het tekstvak antwoord- **URL** in het gedeelte basis- **SAML-configuratie** in de Azure Portal.

    b. Kopieer de waarde van de **entiteits-id van de service provider** en plak deze in het tekstvak **id** in het gedeelte **basis configuratie van SAML** in de Azure Portal.

1. Klik op **Instellingen bewerken** in de sectie **SSO/SLO-instellingen** .

    ![Configuratie van Netskope-beheer console](./media/netskope-cloud-security-tutorial/config-editsettings.png)

1. Voer in het pop-upvenster **instellingen** de volgende stappen uit.

    ![Configuratie van Netskope-beheer console](./media/netskope-cloud-security-tutorial/configuration.png)

    a. Selecteer **SSO inschakelen**.

    b. Plak in het tekstvak **IDP URL** de waarde voor de **aanmeldings-URL** , die u hebt gekopieerd uit de Azure Portal.

    c. Plak in het tekstvak **IDP entiteit-id** de waarde van de **Azure ad-id** , die u hebt gekopieerd uit de Azure Portal.

    d. Open het gedownloade base64-versleutelde certificaat in Klad blok, kopieer de inhoud ervan naar het klem bord en plak het in het tekstvak **IDP certificaat** .

    e. Selecteer **SSO inschakelen**.

    f. Plak in het tekstvak **IDP SLO-URL** de waarde voor de **afmeldings-URL** , die u hebt gekopieerd uit de Azure Portal.

    g. Klik op **verzenden**.

### <a name="create-netskope-administrator-console-test-user"></a>Test gebruiker voor Netskope-beheer console maken

1. Open een nieuw tabblad in uw browser en meld u aan bij de bedrijfs site van uw Netskope-beheerders console als beheerder.

1. Klik op het tabblad **instellingen** in het navigatie deel venster aan de linkerkant.

    ![Netskope-beheer console maken gebruiker](./media/netskope-cloud-security-tutorial/config-settings.png)

1. Klik op het tabblad **actief platform** .

    ![Netskope-beheer console maken gebruiker](./media/netskope-cloud-security-tutorial/user1.png)

1. Klik op tabblad **gebruikers** .

    ![Netskope-beheer console maken gebruiker](./media/netskope-cloud-security-tutorial/add-user.png)

1. Klik op **gebruikers toevoegen**.

    ![Netskope-beheer console maken gebruiker](./media/netskope-cloud-security-tutorial/user-add.png)

1. Voer het e-mail adres in van de gebruiker die u wilt toevoegen en klik op **toevoegen**.

    ![Netskope-beheer console maken gebruiker](./media/netskope-cloud-security-tutorial/add-user-popup.png)

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Netskope Administrator-console in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Netskope-beheer console waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Netskope-beheer console uit met Azure AD](https://aad.portal.azure.com/)
