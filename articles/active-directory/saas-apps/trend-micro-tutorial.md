---
title: 'Zelf studie: Azure AD SSO-integratie met Trend Micro Web Security (TMWS)'
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
ms.openlocfilehash: 9d09af492439beb931b4ab1cd08ccb9e1d82fc3f
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583144"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-security-tmws"></a>Zelf studie: Azure Active Directory-integratie met eenmalige aanmelding (SSO) met Trend Micro Web Security (TMWS)

In deze zelf studie leert u hoe u Trend Micro Web Security (TMWS) integreert met Azure Active Directory (Azure AD). Wanneer u TMWS integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot TMWS.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij TMWS met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Eenmalige aanmelding voor toepassingen in Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) voor meer informatie over de integratie van SaaS-apps met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, hebt u het volgende nodig:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Een TMWS-abonnement dat is ingeschakeld voor SSO.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelf studie configureert en test u Azure AD SSO in een test omgeving.

* TMWS ondersteunt door SP geïnitieerde SSO.
* Nadat u TMWS hebt geconfigureerd, kunt u sessie beheer afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in real-time worden beschermd. Sessie beheer wordt uitgebreid vanuit voorwaardelijke toegang. Voor informatie over het afdwingen van sessie beheer met behulp van Microsoft Cloud App Security, raadpleegt u [voor elke app het onboarding en implementeert u app-beheer voor voorwaardelijke toegang](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-tmws-from-the-gallery"></a>TMWS toevoegen vanuit de galerie

Als u de integratie van TMWS in azure AD wilt configureren, moet u TMWS uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het linkerdeel venster.
1. Selecteer **bedrijfs toepassingen** en selecteer vervolgens **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Voer in de sectie **toevoegen vanuit de galerie** **Trend Micro Web Security (TMWS)** in het zoekvak in.
1. Selecteer **Trend Micro Web Security (TMWS)** in de zoek resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.

## <a name="configure-and-test-azure-ad-sso-for-tmws"></a>Azure AD SSO voor TMWS configureren en testen

U gaat Azure AD SSO met TMWS configureren en testen met behulp van een test gebruiker met de naam B. Simon. Voor het werken met SSO moet u een koppeling tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in TMWS.

U voltooit deze basis stappen voor het configureren en testen van Azure AD SSO met TMWS:

1. [Configureer Azure AD SSO](#configure-azure-ad-sso) om de functie voor uw gebruikers in te scha kelen.
    1. [Een Azure AD-gebruiker maken](#create-an-azure-ad-test-user) om eenmalige aanmelding voor Azure ad te testen.
    1. [Verleen de Azure AD-test gebruiker](#grant-the-azure-ad-test-user-access-to-tmws) toegang tot TMWS.
    1. [Gebruikers-en groeps synchronisatie-instellingen configureren in azure AD](#configure-user-and-group-synchronization-settings-in-azure-ad).
1. [CONFIGUREER TMWS SSO](#configure-tmws-sso) aan de kant van de toepassing.
1. [Test SSO](#test-sso) om de configuratie te controleren.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Voer de volgende stappen uit om Azure AD SSO in te scha kelen in de Azure Portal.

1. Selecteer in de [Azure Portal](https://portal.azure.com/)op de pagina **Trend Micro Web Security (TMWS)** Application Integration in de sectie **Manage** de optie **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Selecteer op de pagina **eenmalige aanmelding met SAML instellen** de knop pen voor de basis- **SAML-configuratie** om de instellingen te bewerken:

   ![De basis-SAML-configuratie-instellingen bewerken](common/edit-urls.png)

1. Voer in de sectie **basis configuratie van SAML** de waarden in de volgende vakken in:

    a. Voer in het vak **id (Entiteits-ID)** een URL in het volgende patroon in:

    `https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. Voer in het vak **antwoord-URL** de volgende URL in:

    `https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > De id-waarde in de vorige stap is niet de waarde die u moet invoeren. U moet de daad werkelijke id gebruiken. U kunt deze waarde ophalen in de **service provider instellingen voor de Azure-beheer Portal** sectie op de pagina **verificatie methode** voor azure AD vanuit **beheer > Directory Services**.

1. TMWS verwacht de SAML-beweringen in een specifieke indeling, dus u moet aangepaste kenmerk toewijzingen toevoegen aan de configuratie van uw SAML-token kenmerken. Deze scherm afbeelding toont de standaard kenmerken:

    ![Standaard kenmerken](common/default-attributes.png)

1. Naast de kenmerken in de vorige scherm afbeelding, verwacht TMWS twee meer kenmerken die moeten worden door gegeven in het SAML-antwoord. Deze kenmerken worden weer gegeven in de volgende tabel. De kenmerken zijn vooraf ingevuld, maar u kunt ze wijzigen zodat ze voldoen aan uw vereisten.
    
    | Naam | Bronkenmerk|
    | --------------- | --------- |
    | sAMAccountName | User. onpremisessamaccountname |
    | uPN | user.userprincipalname |

1. Ga op de pagina **eenmalige aanmelding met SAML instellen** naar **certificaat (base64)** in het gedeelte **SAML-handtekening certificaat** . Selecteer de **Download** koppeling naast deze certificaat naam om het certificaat te downloaden en op uw computer op te slaan:

    ![De koppeling om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in het gedeelte **Trend Micro Web Security (TMWS) instellen** de juiste URL of url's, op basis van uw vereisten:

    ![De configuratie-Url's kopiëren](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Een Azure AD-testgebruiker maken

In deze sectie maakt u een test gebruiker met de naam B. Simon in het Azure Portal.

1. Selecteer **Azure Active Directory**in het linkerdeel venster van de Azure Portal. Selecteer **gebruikers**en selecteer vervolgens **alle gebruikers**.
1. Selecteer **nieuwe gebruiker** boven aan het scherm.
1. Voer de volgende stappen uit in de eigenschappen van de **gebruiker** :
   1. Voer `B.Simon`in het vak **naam** in.  
   1. Voer in het vak **gebruikers naam** de ***naam*@* companydomain in *.* extensie***. Bijvoorbeeld `B.Simon@contoso.com`.
   1. Selecteer **wacht woord weer geven**en noteer de waarde die wordt weer gegeven in het vak **wacht woord** .
   1. Selecteer **Maken**.

### <a name="grant-the-azure-ad-test-user-access-to-tmws"></a>De Azure AD-test gebruiker toegang geven tot TMWS

In deze sectie schakelt u B. Simon in om eenmalige aanmelding van Azure te gebruiken door toegang te verlenen aan TMWS.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer in de lijst toepassingen de optie **Trend Micro Web Security (TMWS)**.
1. Selecteer op de pagina overzicht van de app in de sectie **beheren** de optie **gebruikers en groepen**:

   ![Gebruikers en groepen selecteren](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen** en selecteer vervolgens **Gebruikers en groepen** in het dialoogvenster **Toewijzing toevoegen**.

    ![Gebruiker toevoegen selecteren](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** in de lijst **gebruikers** en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een rolnaam in de SAML-bewering verwacht, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Selecteer **Toewijzen** in het dialoogvenster **Toewijzing toevoegen**.

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Gebruikers-en groeps synchronisatie-instellingen configureren in azure AD

1. Selecteer de knop **Azure Active Directory** in het linkerdeelvenster.

1. Selecteer onder **beheren**de optie **app-registraties**en selecteer vervolgens uw nieuwe zakelijke toepassing onder **alle toepassingen**.

1. Selecteer onder **beheren**de optie **certificaten & geheimen**.

1. Selecteer **Nieuw client geheim**in het gebied **client geheimen** .

1. Voeg in het **scherm een client geheim toevoegen**eventueel een beschrijving toe en selecteer een verloop periode voor het client geheim en selecteer vervolgens **toevoegen**. Het nieuwe client geheim wordt weer gegeven in het gebied **client geheimen** .

1. Noteer de waarde van het client geheim. Later voert u deze in op TMWS.

1. Selecteer onder **beheren**de optie **API-machtigingen**. 

1. Selecteer in het venster **API-machtigingen** de optie **een machtiging toevoegen**.

1. Op het tabblad **micro soft api's** van het venster **API-machtigingen voor aanvragen** selecteert u **Microsoft Graph** en vervolgens **toepassings machtigingen**.

1. Zoek en voeg deze machtigingen toe: 

    * Group.Read.All
    * User. Read. all

1. Selecteer **machtigingen toevoegen**. Er wordt een bericht weer gegeven om te bevestigen dat uw instellingen zijn opgeslagen. De nieuwe machtigingen worden weer gegeven in het venster **API-machtigingen** .

1. Selecteer in het gebied **toestemming geven** de optie **beheerder toestemming geven voor *uw beheerders account* (standaardmap)** en selecteer vervolgens **Ja**. Er wordt een bericht weer gegeven om te bevestigen dat de beheerder toestemming voor de aangevraagde machtigingen heeft verleend.

1. Selecteer **Overzicht**. 

1. Noteer de ID van de **toepassing (client)** en de **map (Tenant)** die u in het rechterdeel venster ziet. Later voert u die informatie in op TMWS. U kunt ook **aangepaste domein namen** selecteren onder **Azure Active Directory >** de domein naam die u ziet in het rechterdeel venster te beheren en vast te leggen.

## <a name="configure-tmws-sso"></a>TMWS SSO configureren

Voer deze stappen uit om TMWS SSO te configureren aan de kant van de toepassing.

1. Meld u aan bij de TMWS-beheer console en ga naar **beheer** > **gebruikers & Authentication** > **Directory Services**.

1. Selecteer **hier** in het bovenste gedeelte van het scherm.

1. Selecteer op de pagina **verificatie methode** de optie **Azure AD**.

1. Selecteer aan of **uit** om te **configureren of Azure** AD-gebruikers in uw organisatie websites mogen bezoeken via TMWS als hun gegevens niet worden gesynchroniseerd met TMWS.

    > [!NOTE]
    > Gebruikers die niet zijn gesynchroniseerd vanuit Azure AD kunnen alleen worden geverifieerd via bekende TMWS-gateways of de toegewezen poort voor uw organisatie.

1. Voer de volgende stappen uit in de sectie instellingen van de **identiteits provider** :

    a. Voer in het vak **service-URL** de waarde voor de **aanmeldings-URL** in die u hebt gekopieerd uit de Azure Portal.

    b. Voer in het vak Naam van het **aanmeldings kenmerk** de naam van de **gebruikers claim** in met het bron kenmerk **user. onpremisessamaccountname** uit de Azure Portal.

    c. Gebruik in het vak **openbaar SSL-certificaat** het gedownloade **certificaat (base64)** van de Azure Portal.

1. Voer de volgende stappen uit in de sectie **synchronisatie-instellingen** :

    a. Voer in het vak **Tenant** de **Directory-id (Tenant)** of de waarde van de **aangepaste domein naam** van de Azure Portal in.

    b. Voer in het vak **toepassings-id** de waarde voor de **toepassings-id** van de Azure Portal in.

    c. Voer in het vak **client geheim** het **client geheim** van de Azure Portal in.

    d. Selecteer **synchronisatie schema** om te synchroniseren met Azure AD hand matig of volgens een planning. Als u **hand matig**selecteert, wanneer er wijzigingen zijn in Active Directory gebruikers gegevens, moet u teruggaan naar de pagina **adreslijst Services** en hand matige synchronisatie uitvoeren zodat de gegevens in TMWS actueel blijven.

    e. Selecteer **verbinding testen** om te controleren of de Azure AD-service met succes kan worden verbonden.
    
    f. Selecteer **Opslaan**.
 
 > [!NOTE]
 > Zie [Azure AD-instellingen configureren op TMWS](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx)voor meer informatie over het configureren van TMWS met Azure AD.

## <a name="test-sso"></a>SSO testen 

Nadat u de Azure AD-service hebt geconfigureerd en Azure AD hebt opgegeven als de verificatie methode voor gebruikers, kunt u zich aanmelden bij de TMWS-proxy server om uw installatie te controleren. Nadat de aanmelding bij Azure AD uw account heeft gecontroleerd, kunt u het internet bezoeken.

> [!NOTE]
> TMWS biedt geen ondersteuning voor het testen van eenmalige aanmelding vanuit de Azure AD-Portal. onder **overzicht** > **eenmalige aanmelding** > **kunt u eenmalige aanmelding instellen met de SAML** > -**test** van uw nieuwe bedrijfs toepassing.

1. Wis de browser van alle cookies en start de browser opnieuw. 

1. Ga in uw browser naar de TMWS-proxy server. Zie [verkeer door sturen via PAC-bestanden](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B)voor meer informatie.

1. Ga naar een Internet website. TMWS leidt u naar de TMWS-Portal.

1. Geef een Active Directory account (indeling: *domein*\\*sAMAccountName* of *sAMAccountName*@-*domein*), e-mail adres of UPN op en selecteer **Aanmelden**. TMWS stuurt u naar het aanmeldings venster van Azure AD.

1. Voer in het aanmeldings venster van Azure AD uw Azure AD-account referenties in. U moet zich nu aanmelden bij TMWS.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Trend Micro Web Security met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Trend Micro Web Security beveiligen met geavanceerde zicht baarheid en controles](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

