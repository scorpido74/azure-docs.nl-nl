---
title: 'Zelfstudie: Azure Active Directory-integratie met SAP Cloud Platform Identity Authentication | Microsoft Documenten'
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
ms.openlocfilehash: 95be73bd125c124409585a478fa9707e7b6a2ac2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76289064"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-platform-identity-authentication"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met SAP Cloud Platform Identity Authentication

In deze zelfstudie leert u hoe u SAP Cloud Platform Identity Authentication integreren met Azure Active Directory (Azure AD). Wanneer u SAP Cloud Platform Identity Authentication integreert met Azure AD, u het als:

* Beheer in Azure AD wie toegang heeft tot SAP Cloud Platform Identity Authentication.
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij SAP Cloud Platform Identity Authentication met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* SAP Cloud Platform Identity Authentication single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie gaat u in een testomgeving eenmalige aanmelding van Azure AD configureren en testen.

* SAP Cloud Platform-identiteitsverificatie biedt ondersteuning voor door **SP** en **IDP** gestarte eenmalige aanmelding
* Zodra u de SAP Cloud Platform Identity Authentication hebt geconfigureerd, u sessiecontroles afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebesturingselementen zijn van voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

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

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **SAP Cloud Platform Identity Authentication** in het zoekvak in de sectie Toevoegen vanuit de **galerie.**
1. Selecteer **SAP Cloud Platform Identity Authentication** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.


## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-platform-identity-authentication"></a>Azure AD-singlesign-on configureren en testen voor identiteitsverificatie van SAP Cloud Platform

Azure AD SSO configureren en testen met SAP Cloud Platform Identity Authentication met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppelingsrelatie tot stand brengen tussen een Azure AD-gebruiker en de gerelateerde gebruiker in SAP Cloud Platform Identity Authentication.

Als u Azure AD SSO wilt configureren en testen met SAP Cloud Platform Identity Authentication, voert u de volgende bouwstenen in:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    * **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    * **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
1. **[Configureer SAP Cloud Platform Identity Authentication SSO](#configure-sap-cloud-platform-identity-authentication-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
    * **[Maak de gebruiker van SAP Cloud Platform Identity Authentication-test-](#create-sap-cloud-platform-identity-authentication-test-user)** om een tegenhanger van B.Simon te hebben in SAP Cloud Platform Identity Authentication die is gekoppeld aan de Azure AD-weergave van de gebruiker.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina Met de integratie van de **SAP Cloud Platform Identity Authentication-toepassingsintegratie** de sectie **Beheren** en selecteer **eenmalige aanmelding**.
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

4. Voer in de sectie **BasisSAML-configuratie** de volgende stappen uit als u wilt configureren in **de idp-modus:**

    ![Domein- en URL-informatie voor eenmalige aanmelding bij SAP Cloud Platform Identity-identiteitsverificatie](common/idp-intiated.png)

    a. Typ **in** het tekstvak Id een URL met het volgende patroon:`<IAS-tenant-id>.accounts.ondemand.com`

    b. In het tekstvak **Antwoord-URL** typt u een URL met de volgende notatie: `https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Dit zijn geen echte waarden. Werk deze waarden bij met de werkelijke id en antwoord-URL. Neem voor deze waarden contact op met het [clientondersteuningsteam van SAP Cloud Platform-identiteitsverificatie](https://cloudplatform.sap.com/capabilities/security/trustcenter.html). Als u de id-waarde niet begrijpt, leest u de documentatie van SAP Cloud Platform-identiteitsverificatie over [Tenant SAML 2.0-configuratie](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

5. Klik **op Extra URL's instellen** en voer de volgende stap uit als u de toepassing wilt configureren in de sp-gestarte modus: **SP**

    ![Domein- en URL-informatie voor eenmalige aanmelding bij SAP Cloud Platform Identity-identiteitsverificatie](common/metadata-upload-additional-signon.png)

    In het tekstvak **Aanmeldings-URL** typt u een URL met het volgende patroon: `{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Deze waarde is niet echt. Werk deze waarde bij met de werkelijke aanmeldings-URL. Gebruik uw specifieke aanmeldings-URL voor zakelijke toepassingen. Neem contact op met het [clientondersteuningsteam van SAP Cloud Platform-identiteitsverificatie](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) als u twijfelt.

1. SAP Cloud Platform Identity Authentication-toepassing verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Naast bovenstaande, SAP Cloud Platform Identity Authentication applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw vereisten.

    | Name | Bronkenmerk|
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

In deze sectie maakt u een testgebruiker in de Azure-portal genaamd B.Simon.

1. Selecteer in het linkerdeelvenster in de Azure-portal **Azure Active Directory,** selecteer **Gebruikers**en selecteer **Alle gebruikers**.
1. Selecteer **Nieuwe gebruiker** boven aan het scherm.
1. Voer **in** de eigenschappen gebruiker de volgende stappen uit:
   1. Voer in het veld **Naam**`B.Simon` in.  
   1. Voer **in** het veld username@companydomain.extensionGebruikersnaam de . Bijvoorbeeld `B.Simon@contoso.com`.
   1. Schakel het selectievakje **Wachtwoord weergeven** in en noteer de waarde die wordt weergegeven in het vak **Wachtwoord.**
   1. Klik **op Maken**.

### <a name="assign-the-azure-ad-test-user"></a>De Azure AD-testgebruiker toewijzen

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot SAP Cloud Platform Identity Authentication.

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer **SAP Cloud Platform-identiteitsverificatie** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

## <a name="configure-sap-cloud-platform-identity-authentication-sso"></a>Sap Cloud Platform Identity Authentication SSO configureren

1. Om een eenmalige aanmelding te configureren voor uw toepassing, gaat u naar de beheerconsole van SAP Cloud Platform-identiteitsverificatie. De URL heeft het volgende patroon: `https://<tenant-id>.accounts.ondemand.com/admin`. Lees de documentatie over SAP Cloud Platform-identiteitsverificatie in [Integration with Microsoft Azure AD](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html) (Integratie met Microsoft Azure AD).

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

Zie voor meer informatie over het in- of uitschakelen van Identiteitsfederatie met SAP Cloud Platform-identiteitsverificatie 'Enable Identity Federation with SAP Cloud Platform Identity Authentication' (Identiteitsfederatie inschakelen met SAP Cloud Platform-identiteitsverificatie) in [Configure Identity Federation with the User Store of SAP Cloud Platform Identity Authentication](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html) (Identiteitsfederatie configureren met het gebruikersarchief van SAP Cloud Platform-identiteitsverificatie).

## <a name="test-sso"></a>Test SSO 

In deze sectie gaat u uw configuratie van Azure AD-eenmalige aanmelding testen via het toegangsvenster.

Wanneer u op de tegel SAP Cloud Platform-identiteitsverificatie klikt in het toegangsvenster, wordt u automatisch aangemeld bij de instantie van SAP Cloud Platform-identiteitsverificatie waarvoor u eenmalige aanmelding hebt ingesteld. Zie [Introduction to the Access Panel](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) (Inleiding tot het toegangsvenster) voor meer informatie over het toegangsvenster.

## <a name="additional-resources"></a>Aanvullende bronnen

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer de identiteitsverificatie van SAP Cloud Platform met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Hoe sap cloudplatformidentiteitsverificatie te beschermen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
