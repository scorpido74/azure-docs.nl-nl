---
title: 'Zelf studie: integratie met SAP Cloud platform identiteits verificatie Azure Active Directory | Microsoft Docs'
description: Informatie over het configureren van eenmalige aanmelding tussen Azure Active Directory en SAP Cloud Platform-identiteitsverificatie.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e92cba923bc197d82083973b3fac476ab93117ad
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82231370"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-platform-identity-authentication"></a>Zelf studie: Azure Active Directory SSO-integratie (single sign-on) met SAP Cloud platform identiteits verificatie

In deze zelf studie leert u hoe u de SAP Cloud platform identiteits verificatie integreert met Azure Active Directory (Azure AD). Wanneer u SAP Cloud platform identiteits verificatie integreert met Azure AD, kunt u het volgende doen:

* Controle in azure AD die toegang heeft tot de identiteits verificatie van SAP-Cloud platform.
* Zorg ervoor dat uw gebruikers automatisch worden aangemeld bij SAP Cloud platform identiteits verificatie met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure Portal.

Zie [Wat is toegang tot toepassingen en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over SaaS-app-integratie met Azure AD.

## <a name="prerequisites"></a>Vereisten

U hebt de volgende items nodig om aan de slag te gaan:

* Een Azure AD-abonnement Als u geen abonnement hebt, kunt u een [gratis account](https://azure.microsoft.com/free/)aanvragen.
* Abonnement voor SAP-Cloud platform identiteits verificatie voor eenmalige aanmelding (SSO) is ingeschakeld.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SAP Cloud Platform-identiteitsverificatie biedt ondersteuning voor door **SP** en **IDP** gestarte eenmalige aanmelding
* Zodra u de SAP Cloud platform identiteits verificatie hebt geconfigureerd, kunt u sessie besturings elementen afdwingen, waardoor exfiltration en infiltratie van de gevoelige gegevens van uw organisatie in realtime worden beschermd. Sessie besturings elementen worden uitgebreid vanuit voorwaardelijke toegang. [Meer informatie over het afdwingen van sessie beheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

Voordat u de technische details induikt, is van het belang dat u de concepten begrijpt die u gaat bekijken. Met de SAP Cloud Platform-identiteitsverificatie en Active Directory Federation Services kunt u eenmalige aanmelding uitvoeren op toepassingen of services die worden beveiligd door Azure AD (als een IdP) met SAP-toepassingen en -services die worden beveiligd door SAP-Cloud Platform-identiteitsverificatie.

Momenteel fungeert SAP Cloud Platform-identiteitsverificatie als een proxy-id-provider voor SAP-toepassingen. Azure Active Directory fungeert op zijn beurt als de voornaamste id-provider in deze installatie. 

Het volgende diagram illustreert deze relatie:

![Een Azure AD-testgebruiker maken](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Met deze instelling wordt uw tenant voor SAP Cloud Platform-identiteitsverificatie geconfigureerd als een vertrouwde toepassing in Azure Active Directory.

Alle SAP-toepassingen en -services die u op deze manier wilt beveiligen worden vervolgens geconfigureerd in de beheerconsole voor SAP Cloud Platform-identiteitsverificatie.

Daarom dient de autorisatie voor het verlenen van toegang tot de SAP-toepassingen en -services plaats te vinden in SAP Cloud Platform-identiteitsverificatie (in plaats van Azure Active Directory).

Door SAP Cloud Platform-identiteitsverificatie te configureren als een toepassing via de Azure Active Directory Marketplace, hoeft u geen afzonderlijke claims of SAML-asserties te configureren.

> [!NOTE]
> Momenteel is alleen eenmalige aanmelding bij het web door beide partijen getest. De stromen die nodig voor app-naar-API- of API-naar-API-communicatie zouden moeten werken, maar zijn nog niet getest. Ze worden tijdens latere activiteiten getest.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>SAP Cloud Platform-identiteitsverificatie uit de galerie toevoegen

Voor het configureren van de integratie van SAP Cloud Platform-identiteitsverificatie met Azure AD moet u SAP Cloud Platform-identiteitsverificatie uit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure Portal](https://portal.azure.com) met behulp van een werk-of school account of een persoonlijke Microsoft-account.
1. Selecteer de **Azure Active Directory** -service in het navigatie deel venster aan de linkerkant.
1. Ga naar **bedrijfs toepassingen** en selecteer **alle toepassingen**.
1. Selecteer **nieuwe toepassing**om een nieuwe toepassing toe te voegen.
1. Typ in de sectie **toevoegen vanuit de galerie** **SAP Cloud platform identiteits verificatie** in het zoekvak.
1. Selecteer **SAP Cloud platform identiteits verificatie** in het paneel resultaten en voeg vervolgens de app toe. Wacht een paar seconden wanneer de app aan uw Tenant is toegevoegd.


## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-platform-identity-authentication"></a>Eenmalige aanmelding van Azure AD configureren en testen voor SAP Cloud platform identiteits verificatie

Azure AD SSO configureren en testen met SAP Cloud platform identiteits verificatie met behulp van een test gebruiker met de naam **B. Simon**. Voor het werken met SSO moet u een koppelings relatie tot stand brengen tussen een Azure AD-gebruiker en de bijbehorende gebruiker in SAP Cloud platform identiteits verificatie.

Als u Azure AD SSO wilt configureren en testen met SAP Cloud platform identiteits verificatie, voltooit u de volgende bouw stenen:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** -om uw gebruikers in staat te stellen deze functie te gebruiken.
    * **[Een Azure AD-test gebruiker maken](#create-an-azure-ad-test-user)** : u kunt eenmalige aanmelding voor Azure AD testen met B. Simon.
    * **[Wijs de Azure AD-test gebruiker](#assign-the-azure-ad-test-user)** toe, zodat B. Simon de eenmalige aanmelding van Azure AD kan gebruiken.
1. **[CONFIGUREER SSO voor SAP-Cloud platform identiteits verificatie](#configure-sap-cloud-platform-identity-authentication-sso)** -voor het configureren van de instellingen voor eenmalige aanmelding aan de kant van de toepassing.
    * Een **[SAP-Cloud platform voor identiteits verificatie gebruiker maken](#create-sap-cloud-platform-identity-authentication-test-user)** : u hebt een equivalent van B. Simon in SAP Cloud platform identiteits verificatie die is gekoppeld aan de Azure AD-representatie van de gebruiker.
1. **[SSO testen](#test-sso)** : om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te scha kelen in de Azure Portal.

1. Ga in het [Azure Portal](https://portal.azure.com/) **naar de integratie** pagina **SAP-Cloud platform identiteits verificatie** en selecteer **eenmalige aanmelding**.
1. Selecteer op de pagina **Eén aanmeldings methode selecteren** de optie **SAML**.
1. Klik op de pagina **eenmalige aanmelding met SAML instellen** op het pictogram bewerken/pen voor **eenvoudige SAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer de volgende stappen uit in de sectie **basis configuratie van SAML** als u in de **IDP**-modus wilt configureren:

    ![Domein- en URL-informatie voor eenmalige aanmelding bij SAP Cloud Platform Identity-identiteitsverificatie](common/idp-intiated.png)

    a. Typ in het tekstvak **id** een URL met het volgende patroon:`<IAS-tenant-id>.accounts.ondemand.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem voor deze waarden contact op met het [clientondersteuningsteam van SAP Cloud Platform-identiteitsverificatie](https://cloudplatform.sap.com/capabilities/security/trustcenter.html). Als u de id-waarde niet begrijpt, leest u de documentatie van SAP Cloud Platform-identiteitsverificatie over [Tenant SAML 2.0-configuratie](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

5. Klik op **aanvullende Url's instellen** en voer de volgende stap uit als u de toepassing in de door **SP**geïnitieerde modus wilt configureren:

    ![Domein- en URL-informatie voor eenmalige aanmelding bij SAP Cloud Platform Identity-identiteitsverificatie](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Deze waarde is niet echt. Werk deze waarde bij met de werkelijke aanmeldings-URL. Gebruik uw specifieke aanmeldings-URL voor zakelijke toepassingen. Neem contact op met het [clientondersteuningsteam van SAP Cloud Platform-identiteitsverificatie](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) als u twijfelt.

1. SAP Cloud platform Identity-verificatie toepassing verwacht de SAML-beweringen in een specifieke indeling. hiervoor moet u aangepaste kenmerk toewijzingen toevoegen aan uw configuratie van uw SAML-token kenmerken. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. In aanvulling op het bovenstaande worden met SAP-Cloud platform identiteits verificatie slechts enkele kenmerken door gegeven aan het SAML-antwoord die hieronder worden weer gegeven. Deze kenmerken worden ook vooraf ingevuld, maar u kunt ze controleren volgens uw vereisten.

    | Naam | Bronkenmerk|
    | ---------------| --------------- |
    | voornaam | user.givenname |

8. Op de pagina **Eenmalige aanmelding met SAML instellen** in de sectie **SAML-handtekeningcertificaat** klikt u op **Downloaden** om de **metagegevens-XML** te downloaden uit de gegeven opties overeenkomstig met wat u nodig hebt, en slaat u dit op uw computer op.

    ![De link om het certificaat te downloaden](common/metadataxml.png)

9. In het gedeelte **SAP Cloud Platform-identiteitsverificatie instellen** kopieert u de juiste URL('s) op basis van uw behoeften.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

    a. Aanmeldings-URL

    b. Azure AD-id

    c. Afmeldings-URL

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

In deze sectie schakelt u B. Simon in voor het gebruik van eenmalige aanmelding van Azure door toegang te verlenen tot de verificatie van de SAP-Cloud platform identiteit.

1. Selecteer in het Azure Portal **bedrijfs toepassingen**en selecteer vervolgens **alle toepassingen**.
1. Selecteer **SAP Cloud Platform-identiteitsverificatie** in de lijst met toepassingen.
1. Ga op de pagina overzicht van de app naar de sectie **beheren** en selecteer **gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **gebruiker toevoegen**en selecteer vervolgens **gebruikers en groepen** in het dialoog venster **toewijzing toevoegen** .

    ![De koppeling gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer in het dialoog venster **gebruikers en groepen** **B. Simon** van de lijst gebruikers en klik vervolgens op de knop **selecteren** onder aan het scherm.
1. Als u een wille keurige rol verwacht in de SAML-bewering, selecteert u in het dialoog venster **rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **selecteren** onder aan het scherm.
1. Klik in het dialoog venster **toewijzing toevoegen** op de knop **toewijzen** .

## <a name="configure-sap-cloud-platform-identity-authentication-sso"></a>SSO voor SAP-Cloud platform identiteits verificatie configureren

1. Om een eenmalige aanmelding te configureren voor uw toepassing, gaat u naar de beheerconsole van SAP Cloud Platform-identiteitsverificatie. De URL heeft het volgende patroon: `https://<tenant-id>.accounts.ondemand.com/admin`. Lees de documentatie over SAP Cloud Platform-identiteitsverificatie in [Integration with Microsoft Azure AD](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/626b17331b4d4014b8790d3aea70b240.html) (Integratie met Microsoft Azure AD).

2. Selecteer in de Azure-portal de knop **Opslaan**.

3. Ga alleen door met het volgende als u eenmalige aanmelding voor nog een SAP-toepassing wilt toevoegen en inschakelen. Herhaal de stappen uit het gedeelte **SAP Cloud Platform-identiteitsverificatie uit de galerie toevoegen**.

4. Selecteer in de Azure-portal op de pagina voor integratie van de toepassing **SAP Cloud Platform-identiteitsverificatie** de optie **Gekoppelde aanmelding**.

    ![Gekoppelde aanmelding configureren](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. Sla de configuratie op.

> [!NOTE]
> De nieuwe toepassing maakt gebruik van de individuele aanmeldingsconfiguratie van de vorige SAP-toepassing. Zorg ervoor dat u de dezelfde zakelijke id-providers gebruikt in de beheerconsole voor SAP Cloud Platform-identiteitsverificatie.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>Testgebruiker maken voor SAP Cloud Platform-identiteitsverificatie

U hoeft geen gebruiker te maken in SAP Cloud Platform-identiteitsverificatie. Gebruikers die zich in het gebruikersarchief van Azure AD bevinden, kunnen de functionaliteit voor eenmalige aanmelding gebruiken.

SAP Cloud Platform-identiteitsverificatie ondersteunt de optie Identiteitsfederatie. Met deze optie kan de toepassing controleren of gebruikers die worden geverifieerd door de zakelijke id-provider bestaan in het gebruikersarchief van de SAP Cloud Platform-identiteitsverificatie.

De optie Identiteitsfederatie is standaard uitgeschakeld. Als Identiteitsfederatie is ingeschakeld, hebben alleen de gebruikers die zijn geïmporteerd in de SAP Cloud Platform-identiteitsverificatie, toegang tot de toepassing.

Zie voor meer informatie over het in- of uitschakelen van Identiteitsfederatie met SAP Cloud Platform-identiteitsverificatie 'Enable Identity Federation with SAP Cloud Platform Identity Authentication' (Identiteitsfederatie inschakelen met SAP Cloud Platform-identiteitsverificatie) in [Configure Identity Federation with the User Store of SAP Cloud Platform Identity Authentication](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/en-US/c029bbbaefbf4350af15115396ba14e2.html) (Identiteitsfederatie configureren met het gebruikersarchief van SAP Cloud Platform-identiteitsverificatie).

## <a name="test-sso"></a>SSO testen 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SAP Cloud Platform-identiteitsverificatie klikt in het toegangsvenster, wordt u automatisch aangemeld bij de instantie van SAP Cloud Platform-identiteitsverificatie waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelf studies voor het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer SAP Cloud platform identiteits verificatie met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessie beheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Identiteits verificatie van SAP-Cloud platform beveiligen met geavanceerde zicht baarheid en controles](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
