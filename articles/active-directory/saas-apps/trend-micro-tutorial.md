---
title: 'Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Trend Micro Web Security (TMWS) | Microsoft Docs'
description: Meer informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en Trend Micro Web Security (TMWS).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 827285d3-8e65-43cd-8453-baeda32ef174
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/21/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f4dc7223d64fd299da70375329260f7b4f8b322
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82083356"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-securitytmws"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Trend Micro Web Security (TMWS)

In deze zelf studie leert u hoe u Trend Micro Web Security (TMWS) integreert met Azure Active Directory (Azure AD). Wanneer u Trend Micro Web Security (TMWS) integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot Trend Micro Web Security (TMWS).
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Trend Micro Web Security (TMWS) met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* TMWS-abonnement (Micro-to-SSO) voor het eenmalige aanmelden op internet.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* Trend Micro Web Security (TMWS) ondersteunt door **SP** GEÏNITIEERDe SSO
* Zodra u Trend Micro Web Security (TMWS) hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie beheer is uitgebreid met voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud app Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-trend-micro-web-securitytmws-from-the-gallery"></a>Trend Micro Web Security (TMWS) toevoegen vanuit de galerie

Als u de integratie van Trend Micro Web Security (TMWS) wilt configureren in azure AD, moet u Trend Micro Web Security (TMWS) toevoegen vanuit de galerie aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** de tekst **Trend Micro Web Security (TMWS)** in het zoekvak.
1. Selecteer **Trend Micro Web Security (TMWS)** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-single-sign-on-for-trend-micro-web-securitytmws"></a>Eenmalige aanmelding van Azure AD voor Trend Micro Web Security (TMWS) configureren en testen

Azure AD SSO configureren en testen met Trend Micro Web Security (TMWS) met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in Trend Micro Web Security (TMWS).

Als u Azure AD SSO wilt configureren en testen met Trend Micro Web Security (TMWS), voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    1. **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    1. **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
    1. **[Gebruikers-en groeps synchronisatie-instellingen configureren in azure AD](#configure-user-and-group-synchronization-settings-in-azure-ad)** -synchronisatie-instellingen voor gebruikers en groepen configureren in azure AD
1. **[TMWS-SSO (Trend Micro Web Security) configureren](#configure-trend-micro-web-security-sso)** : voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Zoek in de [Azure Portal](https://portal.azure.com/)op de pagina **Trend Micro Web Security (TMWS)** Application Integration de sectie **Manage (beheren** ) en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in voor de volgende velden:

    a. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. Typ een URL in het tekstvak **antwoord-URL** :`https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > De id-waarde is niet echt. Werk deze waarde bij met de werkelijke id. U kunt deze waarden ophalen onder de **instellingen voor de service provider voor het gebied Azure-beheer Portal** in het scherm **verificatie methode** voor azure AD vanuit **beheer > Directory Services**.

1. Voor de toepassing Trend Micro Web Security (TMWS) wordt de SAML-beweringen in een specifieke indeling verwacht. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. In aanvulling op de bovenstaande TMWS-toepassing (Trend Micro Web Security) verwachtte nog enkele kenmerken terug te worden door gegeven in de SAML-respons die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.
    
    | Naam | Bronkenmerk|
    | --------------- | --------- |
    | sAMAccountName | User. onpremisessamaccountname |
    | uPN | user.userprincipalname |

1. Zoek op de pagina **eenmalige aanmelding met SAML instellen** , in de sectie **SAML-handtekening certificaat** , naar **certificaat (base64)** en selecteer **downloaden** om het certificaat te downloaden en op uw computer op te slaan.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Op de sectie **Trend Micro Web Security (TMWS) instellen** kopieert u de gewenste URL ('s) op basis van uw vereiste.

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

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen tot Trend Micro Web Security (TMWS).

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Trend Micro Web Security (TMWS)**.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Gebruikers-en groeps synchronisatie-instellingen configureren in azure AD

1. Klik op **Azure Active Directory**in het navigatie venster aan de linkerkant.

1. Klik onder **beheren**op **app-registraties** en klik vervolgens op uw nieuwe zakelijke toepassing in het gebied **alle toepassingen** .

1. Klik onder **beheren**op **certificaten & geheimen**.

1. Klik onder het gebied client geheimen dat wordt weer gegeven op **Nieuw client geheim**.

1. Voeg in het scherm een client geheim toevoegen dat wordt weer gegeven, eventueel een beschrijving toe en selecteer een verloop periode voor dit client geheim en klik vervolgens op **toevoegen**. Het zojuist toegevoegde client geheim wordt weer gegeven onder het gebied client geheimen.

1. Noteer de waarde. Later typt u de gegevens in TMWS.

1. Klik onder **beheren**op **API-machtigingen**. 

1. Klik op het scherm API-machtigingen dat wordt weer gegeven op **een machtiging toevoegen**.

1. Klik op het tabblad micro soft Api's van het scherm API-machtigingen voor aanvragen dat wordt weer gegeven op **Microsoft Graph** en vervolgens op **toepassings machtigingen**.

1. Zoek en voeg de volgende machtigingen toe: 

    * Group.Read.All
    * User. Read. all

1. Klik op **Machtigingen toevoegen**. Er wordt een bericht weer gegeven om te bevestigen dat de instellingen zijn opgeslagen. De nieuw toegevoegde machtigingen worden weer gegeven op het scherm API-machtigingen.

1. Klik in het gebied granting toestemming op **toestemming van de beheerder geven < uw Administrator-account > (standaardmap)** en klik vervolgens op **Ja**. Er wordt een bericht weer gegeven om te bevestigen dat de beheerder heeft toestemming verleend voor de aangevraagde machtigingen.

1. Klik op **Overzicht**. 

1. In het rechterdeel venster dat wordt weer gegeven, noteert u de ID van de toepassing (client) en de directory (Tenant). Later typt u de gegevens in TMWS. U kunt ook klikken op **aangepaste domein namen** onder Azure **Active Directory >** de domein naam in het rechterdeel venster te beheren en vast te leggen.

## <a name="configure-trend-micro-web-security-sso"></a>Micro-upeenmalige beveiliging voor trend microconfigureren

1. Meld u aan bij de TMWS-beheer console en ga naar **beheer** > **gebruikers & Authentication** > **Directory Services**.

1. Klik hier in het bovenste gedeelte van het scherm.

1. Klik in het scherm verificatie methode dat wordt weer gegeven op **Azure AD**.

1. Klik op of **uit** om te **bepalen of de** AD-gebruikers van uw organisatie websites mogen bezoeken via TMWS als hun gegevens niet zijn gesynchroniseerd met TMWS.

    > [!NOTE]
    > Gebruikers die niet zijn gesynchroniseerd vanuit Azure AD kunnen alleen worden geverifieerd via bekende TMWS-gateways of de toegewezen poort voor uw organisatie.

1. Voer de volgende stappen uit in de sectie instellingen van de **identiteits provider** :

    a. Plak in het veld **service-URL** de waarde voor de **aanmeldings-URL** , die u hebt gekopieerd uit Azure Portal

    b. Plak in het veld **naam kenmerk** van de gebruiker de naam van de gebruikers claim met het bron kenmerk **User. onpremisessamaccountname** uit de Azure Portal.

    c. Gebruik in het veld **openbaar SSL-certificaat** het gedownloade **certificaat (base64)** van de Azure Portal.

1. Voer de volgende stappen uit in de sectie **synchronisatie-instellingen** :

    a. Gebruik in het veld **Tenant** de **Directory-id (Tenant)** of de waarde van de **aangepaste domein naam** van de Azure Portal.

    b. In het veld **toepassings-id** wordt de waarde **Application (client) id** van de Azure Portal.

    c. Gebruik in het veld **client geheim** het **client geheim** van de Azure Portal.

    d. In het veld **synchronisatie schema** selecteert u om te synchroniseren met Azure AD hand matig of volgens een planning. Als u hand matig kiest wanneer er wijzigingen zijn in Active Directory gebruikers gegevens, moet u terugkeren naar het scherm adreslijst Services en hand matige synchronisatie uitvoeren zodat de gegevens in TMWS actueel blijven.

    e. Klik op **verbinding testen** om te controleren of de Azure AD-service met succes kan worden verbonden. 
    
    f. Klik op **Opslaan**.
 
 > [!NOTE]
 > Raadpleeg dit document voor meer informatie over het configureren van [de](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx) beveiliging van Trend Micro Web met Azure AD.

## <a name="test-sso"></a>SSO testen 

Zodra u de Azure AD-service hebt geconfigureerd en Azure AD hebt opgegeven als de verificatie methode voor gebruikers, kunt u zich aanmelden bij de TMWS-proxy server om uw installatie te controleren. Nadat de Azure AD-aanmelding uw account heeft geverifieerd, kunt u het internet bezoeken.

> [!NOTE]
> TMWS biedt geen ondersteuning voor het testen van eenmalige aanmelding vanuit de Azure AD-Portal, onder overzicht > eenmalige aanmelding > een eenmalige aanmelding instellen met SAML > test van uw nieuwe bedrijfs toepassing.

1. Wis de browser van alle cookies en start de browser opnieuw. 

1. Ga in uw browser naar de TMWS-proxy server. Zie [verkeer door sturen via PAC-bestanden](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B)voor meer informatie.

1. Ga naar een Internet website. TMWS leidt u naar de TMWS-Portal.

1. Geef een Active Directory account (indeling: domain\sAMAccountName of sAMAccountName@domain) of e-mail adres of UPN op en klik vervolgens op **Aanmelden**. TMWS stuurt u naar de Azure AD-aanmelding.

1. Typ de referenties van uw AD-account bij de aanmelding bij Azure AD. U moet zich aanmelden bij TMWS.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Trend Micro Web Security (TMWS) met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Trend Micro Web Security (TMWS) beveiligen met geavanceerde zicht baarheid en controles](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

