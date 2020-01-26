---
title: 'Zelf studie: Azure Active Directory de integratie van eenmalige aanmelding (SSO) met Firmex VDR | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Firmex VDR.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 670ff192-c23e-49e4-8fd1-516e02d8856c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdfb857d3a68081fda84aef33e6b5a4b4d1bce28
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76761236"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-firmex-vdr"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Firmex VDR

In deze zelf studie leert u hoe u Firmex VDR integreert met Azure Active Directory (Azure AD). Wanneer u Firmex VDR integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Firmex VDR.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij Firmex VDR met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Firmex VDR-abonnement met eenmalige aanmelding (SSO) ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Firmex VDR ondersteunt **SP en IDP** GEÏNITIEERDe SSO

* Zodra u de Firmex hebt geconfigureerd, kunt u sessie besturings elementen afdwingen, waardoor de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie besturings elementen worden uitgebreid vanuit voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-firmex-vdr-from-the-gallery"></a>Firmex VDR toevoegen vanuit de galerie

Als u de integratie van Firmex VDR wilt configureren in azure AD, moet u Firmex VDR van de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u bij de [Azure-portal](https://portal.azure.com) aan met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **Firmex vdr** in het zoekvak.
1. Selecteer **FIRMEX vdr** in het resultaten paneel en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-firmex-vdr"></a>Eenmalige aanmelding voor Azure AD configureren en testen voor Firmex VDR

Azure AD SSO configureren en testen met Firmex VDR met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Firmex VDR.

Als u Azure AD SSO wilt configureren en testen met Firmex VDR, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[FIRMEX vdr SSO configureren](#configure-firmex-vdr-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * **[Maak FIRMEX vdr test User](#create-firmex-vdr-test-user)** -als u een tegen hanger van B. Simon in Firmex vdr wilt hebben dat is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **Firmex vdr** Application Integration de sectie **Manage** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. In de sectie **SAML-basisconfiguratie** hoeft de gebruiker geen enkele stap uit te voeren omdat de app al vooraf is geïntegreerd met Azure.

1. Klik op **Extra URL's instellen** en voer de volgende stap uit als u de toepassing in de door **SP** geïnitieerde modus wilt configureren:

    In het tekstvak **Aanmeldings-URL** typt u de URL: `https://login.firmex.com`

1. Klik op **Opslaan**.

1. De Firmex VDR-toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Daarnaast verwacht Firmex VDR Application nog enkele kenmerken die kunnen worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    | Name | Bronkenmerk|
    | ------------ | --------- |
    | e-mail | user.mail |

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , de **federatieve meta gegevens-XML** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

1. Kopieer de gewenste URL ('s) op basis van uw vereiste op de **FIRMEX vdr-** sectie.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan Firmex VDR.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **FIRMEX vdr**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoogvenster **Toewijzing toevoegen** op de knop **Toewijzen**.

## <a name="configure-firmex-vdr-sso"></a>Firmex VDR SSO configureren

### <a name="before-you-get-started"></a>Voordat u aan de slag gaat

#### <a name="what-youll-need"></a>Wat u nodig hebt

-   Een actief Firmex-abonnement
-   Azure AD als uw SSO-service
-   De IT-beheerder voor het configureren van SSO
-   Zodra SSO is ingeschakeld, moeten alle gebruikers van uw bedrijf zich aanmelden bij Firmex met SSO en geen aanmelding/wacht woord gebruiken.

#### <a name="how-long-will-this-take"></a>Hoe lang duurt het?

Het implementeren van SSO duurt enkele minuten. Er is bijna geen downtime tussen Firmex-ondersteuning voor eenmalige aanmelding voor uw site en de gebruikers van uw bedrijf verifiëren met SSO. Volg de onderstaande stappen.

### <a name="step-1---identify-your-companys-domains"></a>Stap 1: de domeinen van uw bedrijf identificeren

Bepaal de domeinen waarmee de gebruikers van uw bedrijf zich aanmelden.

Bijvoorbeeld:

- @firmex.com
- @firmex.ca

### <a name="step-2---contact-firmex-support-with-your-domains"></a>Stap 2: contact opnemen met Firmex-ondersteuning voor uw domeinen

[Firmex-ondersteunings team](mailto:support@firmex.com) voor e-mail of bel 1888 688 4042 x. 11 om te praten met Firmex-ondersteuning. Geef uw domein gegevens door. Met de Firmex-ondersteuning worden de domeinen toegevoegd aan uw VDR als **geclaimde domeinen**. Uw beheerder moet nu SSO configureren.

Waarschuwing: totdat de site beheerder de geclaimde domeinen heeft geconfigureerd, kunnen gebruikers van uw bedrijf zich niet aanmelden bij de VDR. Gebruikers zonder het bedrijf (gast gebruikers) kunnen zich nog steeds aanmelden met hun e-mail adres en wacht woord. De configuratie kan enkele minuten duren.

### <a name="step-3---configure-the-claimed-domains"></a>Stap 3: de geclaimde domeinen configureren

1. Meld u aan bij Firmex als een site beheerder.
1. Klik in de linkerbovenhoek op uw bedrijfs logo.
1. Selecteer het tabblad **SSO** . Selecteer vervolgens **SSO-configuratie**. Klik op het domein dat u wilt configureren.

    ![Geclaimde domeinen](./media/firmex-vdr-tutorial/edit-sso.png)  

1. Laat uw IT-beheerder de volgende velden invullen. De velden moeten worden opgehaald uit uw ID-provider:  

    ![Configuratie van eenmalige aanmelding](./media/firmex-vdr-tutorial/SSO-config.png)

    a. Plak in het tekstvak **Entiteits-ID** de waarde van de **Azure ad-id** , die u hebt gekopieerd uit de Azure Portal.

    b. Plak in het tekstvak **ID-provider-URL** de waarde voor de AANMELDINGS- **URL** , die u hebt gekopieerd uit de Azure Portal.

    c. **Certificaat voor open bare sleutel** : voor verificatie doeleinden kan een SAML-bericht digitaal worden ondertekend door de uitgever. Voor het controleren van de hand tekening van het bericht gebruikt de ontvanger van het bericht een open bare sleutel die bekend is bij de certificaat verlener. Op dezelfde manier moet een open bare versleutelings sleutel die deel uitmaakt van de uiteindelijke ontvanger, ook bekend zijn bij de certificaat verlener om een bericht te versleutelen. In beide situaties: ondertekening en versleuteling: vertrouwde open bare sleutels moeten vooraf worden gedeeld.  Dit is de **X509Certificate** van de **XML voor federatieve meta gegevens**

    d. Klik op **Opslaan** om de SSO-configuratie te volt ooien. Wijzigingen worden direct van kracht.

1. Op dit moment is SSO ingeschakeld voor uw site.

### <a name="create-firmex-vdr-test-user"></a>Firmex VDR-test gebruiker maken

In deze sectie maakt u een gebruiker met de naam B. Simon in Firmex. Werk samen met [Firmex-ondersteunings team](mailto:support@firmex.com) om de gebruikers toe te voegen in het Firmex-platform. Er moeten gebruikers worden gemaakt en geactiveerd voordat u eenmalige aanmelding kunt gebruiken.

## <a name="test-sso"></a>SSO testen

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel Firmex VDR in het toegangs venster klikt, moet u automatisch worden aangemeld bij de Firmex VDR waarvoor u SSO hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [ List of Tutorials on How to Integrate SaaS Apps with Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list) (Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Firmex VDR met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Firmex beveiligen met geavanceerde zicht baarheid en controles](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
