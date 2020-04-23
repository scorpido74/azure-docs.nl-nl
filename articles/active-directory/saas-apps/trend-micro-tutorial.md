---
title: 'Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Trend Micro Web Security(TMWS) | Microsoft Documenten'
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
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083356"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-trend-micro-web-securitytmws"></a>Zelfstudie: Azure Active Directory single sign-on (SSO) integratie met Trend Micro Web Security (TMWS)

In deze zelfstudie leert u hoe u Trend Micro Web Security (TMWS) integreert met Azure Active Directory (Azure AD). Wanneer u Trend Micro Web Security (TMWS) integreert met Azure AD, u het als volgt op:

* Beheer in Azure AD die toegang heeft tot Trend Micro Web Security (TMWS).
* Stel uw gebruikers in staat om automatisch te worden aangemeld bij Trend Micro Web Security (TMWS) met hun Azure AD-accounts.
* Beheer uw accounts op één centrale locatie: de Azure-portal.

Zie [Wat is toepassingstoegang en eenmalige aanmelding met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)voor meer informatie over de integratie van de SaaS-app met Azure AD.

## <a name="prerequisites"></a>Vereisten

Om aan de slag te gaan, heb je de volgende items nodig:

* Een Azure AD-abonnement Wanneer je nog geen abonnement hebt kun je gratis een [account aanmaken.](https://azure.microsoft.com/free/)
* Trend Micro Web Security (TMWS) single sign-on (SSO) ingeschakeld abonnement.

## <a name="scenario-description"></a>Scenariobeschrijving

In deze zelfstudie configureert en test u Azure AD SSO in een testomgeving.

* Trend Micro Web Security (TMWS) ondersteunt **SP** geïnitieerde SSO
* Zodra u Trend Micro Web Security (TMWS) hebt geconfigureerd, u sessiecontrole afdwingen, die exfiltratie en infiltratie van de gevoelige gegevens van uw organisatie in realtime beschermen. Sessiebeheer strekt zich uit van Voorwaardelijke toegang. [Meer informatie over het afdwingen van sessiebeheer met Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="adding-trend-micro-web-securitytmws-from-the-gallery"></a>Het toevoegen van Trend Micro Web Security (TMWS) vanuit de galerij

Als u de integratie van Trend Micro Web Security (TMWS) in Azure AD wilt configureren, moet u TMWS (Trend Micro Web Security) vanuit de galerie toevoegen aan uw lijst met beheerde SaaS-apps.

1. Meld u aan bij de [Azure-portal](https://portal.azure.com) met een werk- of schoolaccount of een persoonlijk Microsoft-account.
1. Selecteer in het linkernavigatiedeelvenster de **Azure Active Directory-service.**
1. Navigeer naar **Ondernemingstoepassingen** en selecteer **Alle toepassingen**.
1. Als u nieuwe toepassingen wilt toevoegen, selecteert u **Nieuwe toepassing**.
1. Typ **Trend Micro Web Security (TMWS)** in de sectie Toevoegen in de **galerijsectie** in het zoekvak.
1. Selecteer **Trend Micro Web Security (TMWS)** in het deelvenster Resultaten en voeg de app toe. Wacht een paar seconden terwijl de app wordt toegevoegd aan uw tenant.

## <a name="configure-and-test-azure-ad-single-sign-on-for-trend-micro-web-securitytmws"></a>Azure AD-singlesign-on configureren en testen voor Trend Micro Web Security(TMWS)

Azure AD SSO configureren en testen met Trend Micro Web Security (TMWS) met behulp van een testgebruiker genaamd **B.Simon**. Als SSO kan werken, moet u een koppeling smaken tussen een Azure AD-gebruiker en de gerelateerde gebruiker in Trend Micro Web Security (TMWS).

Als u Azure AD SSO wilt configureren en testen met Trend Micro Web Security (TMWS), voert u de volgende bouwstenen uit:

1. **[Configureer Azure AD SSO](#configure-azure-ad-sso)** - zodat uw gebruikers deze functie kunnen gebruiken.
    1. **[Een Azure AD-testgebruiker maken](#create-an-azure-ad-test-user)** - om Azure AD-enkele aanmelding te testen met B.Simon.
    1. **[De Azure AD-testgebruiker toewijzen](#assign-the-azure-ad-test-user)** - om B.Simon in staat te stellen azure AD-aanmelding te gebruiken.
    1. **[Instellingen voor gebruikers- en groepssynchronisatie configureren in Azure AD](#configure-user-and-group-synchronization-settings-in-azure-ad)** - Instellingen voor gebruikers- en groepssynchronisatie configureren in Azure AD
1. **[Configureer Trend Micro Web Security (TMWS) SSO](#configure-trend-micro-web-security-sso)** - om de instellingen voor één aanmelding aan de toepassingszijde te configureren.
1. **[Test SSO](#test-sso)** - om te controleren of de configuratie werkt.

## <a name="configure-azure-ad-sso"></a>Azure AD SSO-configureren

Volg deze stappen om Azure AD SSO in te schakelen in de Azure-portal.

1. Zoek in de [Azure-portal](https://portal.azure.com/)op de pagina **TMWS-toepassingsintegratie (Trend Micro Web Security)** de sectie **Beheren** en selecteer **eenmalige aanmelding.**
1. Selecteer **SAML**op de pagina **Eén aanmeldingsmethode** selecteren .
1. Klik op de pagina **Eén aanmelding instellen met SAML** op het pictogram bewerken/pen voor **BasisSAML-configuratie** om de instellingen te bewerken.

   ![Standaard SAML-configuratie bewerken](common/edit-urls.png)

1. Voer in de sectie **BasisSAML-configuratie** de waarden in voor de volgende velden:

    a. In het tekstvak **Id (Entiteits-id)** typt u een URL met het volgende patroon: `https://auth.iws-hybrid.trendmicro.com/([0-9a-f]{16})`

    b. Typ een URL in het tekstvak **URL beantwoorden:**`https://auth.iws-hybrid.trendmicro.com/simplesaml/module.php/saml/sp/saml2-acs.php/ics-sp`

    > [!NOTE]
    > De id-waarde is niet echt. Werk deze waarde bij met de werkelijke id. U deze waarden ophalen onder de instellingen van de **serviceprovider voor het azure-beheerportalgebied** op het scherm **Verificatiemethode** voor Azure AD van **Beheer > Directory Services**.

1. De TMWS-toepassing (Trend Micro Web Security) verwacht de SAML-beweringen in een specifieke indeling, waarvoor u aangepaste kenmerktoewijzingen moet toevoegen aan uw SAML-tokenkenmerkenconfiguratie. In de volgende schermafbeelding wordt de lijst met standaardkenmerken weergegeven.

    ![installatiekopie](common/default-attributes.png)

1. Naast bovenstaande, Trend Micro Web Security (TMWS) applicatie verwacht weinig meer attributen worden teruggegeven in SAML reactie die hieronder worden weergegeven. Deze kenmerken zijn ook vooraf ingevuld, maar u ze bekijken volgens uw vereisten.
    
    | Naam | Bronkenmerk|
    | --------------- | --------- |
    | sAMAccountName | user.onpremisessamaccountnaam |
    | Upn | user.userprincipalname |

1. Zoek op de pagina **Eén aanmelding instellen met SAML** in de sectie **SAML-ondertekeningscertificaat** **certificaat** en selecteer **Downloaden** om het certificaat te downloaden en op te slaan op uw computer.

    ![De link om het certificaat te downloaden](common/certificatebase64.png)

1. Kopieer in de sectie **TMWS (Set up Trend Micro Web Security)** de juiste URL(s) op basis van uw vereiste.

    ![Configuratie-URL's kopiëren](common/copy-configuration-urls.png)

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

In deze sectie u B.Simon inschakelen om Azure single sign-on te gebruiken door toegang te verlenen tot Trend Micro Web Security (TMWS).

1. Selecteer in de Azure-portal **Enterprise-toepassingen**en selecteer **Alle toepassingen**.
1. Selecteer Trend Micro **Web Security(TMWS)** in de lijst met toepassingen.
1. Zoek op de overzichtspagina van de app de sectie **Beheren** en selecteer **Gebruikers en groepen**.

   ![De koppeling Gebruikers en groepen](common/users-groups-blade.png)

1. Selecteer **Gebruiker toevoegen**en selecteer vervolgens Gebruikers en **groepen** in het dialoogvenster **Toewijzing toevoegen.**

    ![De koppeling Gebruiker toevoegen](common/add-assign-user.png)

1. Selecteer **B.Simon** in het dialoogvenster **Gebruikers en groepen** in de lijst Gebruikers en klik vervolgens op de knop **Selecteren** onder aan het scherm.
1. Als u een rolwaarde verwacht in de SAML-bewering, selecteert u in het dialoogvenster **Rol selecteren** de juiste rol voor de gebruiker in de lijst en klikt u op de knop **Selecteren** onder aan het scherm.
1. Klik **in** het dialoogvenster Toewijzing toevoegen op de knop **Toewijzen.**

### <a name="configure-user-and-group-synchronization-settings-in-azure-ad"></a>Synchronisatie-instellingen voor gebruikers en groepen configureren in Azure AD

1. Klik links op **Azure Active Directory.**

1. Klik **onder Beheren**op **App-registraties** en klik vervolgens op uw nieuwe bedrijfstoepassing onder het gebied **Alle toepassingen.**

1. Klik **onder Beheren**op Certificaten & **geheimen**.

1. Klik onder het gebied Clientgeheimen dat wordt weergegeven op **Nieuw clientgeheim**.

1. Voeg in het scherm Een clientgeheim toevoegen dat wordt weergegeven, optioneel een beschrijving toe en selecteer een vervaldatum voor dit clientgeheim en klik vervolgens op **Toevoegen**. Het nieuw toegevoegde clientgeheim wordt weergegeven onder het gebied Clientgeheimen.

1. Leg de waarde vast. Later typt u de informatie in TMWS.

1. Klik **onder Beheren**op **API-machtigingen**. 

1. Klik in het scherm API-machtigingen dat wordt weergegeven op **Een machtiging toevoegen**.

1. Klik op het tabblad Microsoft API's van het scherm Machtigingen van api's aanvragen dat wordt weergegeven op **Microsoft Graph** en vervolgens **toepassingsmachtigingen**.

1. Zoek en voeg de volgende machtigingen toe: 

    * Group.Read.All
    * User.Read.All

1. Klik op **Machtigingen toevoegen**. Er verschijnt een bericht om te bevestigen dat uw instellingen zijn opgeslagen. De nieuw toegevoegde machtigingen worden weergegeven op het scherm API-machtigingen.

1. Klik onder het gebied Toestemming verlenen op **Beheerderstoestemming verlenen voor < uw beheerdersaccount > (Standaardmap)** en **vervolgens Ja**. Er verschijnt een bericht om te bevestigen dat de beheerderstoestemming voor de gevraagde machtigingen is verleend.

1. Klik op **Overzicht**. 

1. Noteer in het rechterdeelvenster dat wordt weergegeven de id van toepassing (client) en de id van directory (tenant). Later typt u de informatie in TMWS. U ook klikken op **Aangepaste domeinnamen** onder Azure Active Directory **> De** domeinnaam beheren en opnemen in het rechterdeelvenster.

## <a name="configure-trend-micro-web-security-sso"></a>Trend Micro Web Security SSO configureren

1. Meld u aan bij de TMWS-beheerconsole en ga naar **Gebruikers beheren** > **& AUTHENTICATION** > **Directory Services**.

1. Klik hier op het bovenste gedeelte van het scherm.

1. Klik in het scherm Verificatiemethode dat wordt weergegeven op **Azure AD**.

1. Klik **op Aan** of **Uit** om te beslissen of de AD-gebruikers van uw organisatie websites via TMWS mogen bezoeken als hun gegevens niet zijn gesynchroniseerd met TMWS.

    > [!NOTE]
    > Gebruikers die niet zijn gesynchroniseerd vanuit Azure AD, kunnen alleen worden geverifieerd via bekende TMWS-gateways of de speciale poort voor uw organisatie.

1. Voer in de sectie **Instellingen voor identiteitsprovider** de volgende stappen uit:

    a. Plak in het veld **Service-URL** de **URL-waarde aanmelding,** die u hebt gekopieerd vanuit azure-portal

    b. Plak in het **kenmerk aanmeldingsnaam** aan logboeken de naam van de claimgebruiker met het kenmerk **user.onpremisessamaccountname** van de Azure-portal.

    c. Gebruik in het veld **Openbaar SSL-certificaat** het gedownloade certificaat **(Base64)** van de Azure-portal.

1. Voer in de sectie **Synchronisatie-instellingen** de volgende stappen uit:

    a. Gebruik in het veld **Tenant** **directory (tenant)** of **aangepaste domeinnaamwaarde** van de Azure-portal.

    b. In het veld **Toepassings-id** wordt **de id-waarde van toepassing (client)** van de Azure-portal verwijderd.

    c. Gebruik **clientgeheim** van de Azure-portal in het veld **Clientgeheim.**

    d. Selecteer in het veld **Synchronisatieplanning** selecteren om handmatig of volgens een planning te synchroniseren met Azure AD. Als u handmatig kiest, wanneer er wijzigingen zijn in de gebruikersgegevens van Active Directory, vergeet dan niet terug te gaan naar het scherm Directory Services en handmatige synchronisatie uit te voeren, zodat de informatie in TMWS actueel blijft.

    e. Klik **op Verbinding testen** om te controleren of de Azure AD-service kan worden verbonden. 
    
    f. Klik op **Opslaan**.
 
 > [!NOTE]
 > Raadpleeg [dit](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/directory-services/azure-active-directo/configuring-azure-ad.aspx) document voor meer informatie over het configureren van Trend Micro Web Security met Azure AD.

## <a name="test-sso"></a>Test SSO 

Nadat u de Azure AD-service hebt geconfigureerd en Azure AD hebt opgegeven als gebruikersverificatiemethode, u zich aanmelden bij de TMWS-proxyserver om uw installatie te verifiëren. Nadat de Ad-aanmelding in Azure uw account heeft geverifieerd, u het internet bezoeken.

> [!NOTE]
> TMWS ondersteunt geen ondersteuning voor het testen van één aanmelding vanuit de Azure AD-portal, onder Overzicht > Single sign-on > Stel single sign-on in met SAML > Test van uw nieuwe bedrijfstoepassing.

1. Schakel de browser van alle cookies uit en start de browser opnieuw op. 

1. Wijs uw browser naar de TMWS proxy server. Zie Verkeer [doorsturen met PAC-bestanden](https://docs.trendmicro.com/en-us/enterprise/trend-micro-web-security-online-help/administration_001/pac-files/traffic-forwarding-u.aspx#GUID-A4A83827-7A29-4596-B866-01ACCEDCC36B)voor meer informatie.

1. Bezoek een internetwebsite. TMWS leidt u naar het TMWS captive portal.

1. Geef een Active Directory-account op (indeling: sAMAccountName@domaindomein\sAMAccountName of ), of e-mailadres of UPN en klik op **Aanmelden**. TMWS stuurt u naar de Azure AD-aanmelding.

1. Typ uw AD-accountreferenties in de Azure AD-aanmelding. U moet zich met succes aanmelden bij TMWS.

## <a name="additional-resources"></a>Extra resources

- [Lijst met zelfstudies over het integreren van SaaS-apps met Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [What is application access and single sign-on with Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) (Wat is toegang tot toepassingen en eenmalige aanmelding bij Azure Active Directory?)

- [Wat is voorwaardelijke toegang in Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Probeer Trend Micro Web Security (TMWS) met Azure AD](https://aad.portal.azure.com/)

- [Wat is sessiebeheer in Microsoft Cloud App Security?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Trend Micro Web Security(TMWS) beschermen met geavanceerde zichtbaarheid en besturingselementen](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

