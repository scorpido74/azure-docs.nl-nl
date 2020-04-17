---
title: Configuratie van clienttoepassingen (MSAL) | Azure
titleSuffix: Microsoft identity platform
description: Meer informatie over configuratieopties voor openbare client- en vertrouwelijke clienttoepassingen met behulp van de Microsoft Authentication Library (MSAL).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/27/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: b4595a63613afa3c6fef2fa2a85647d8b70b1388
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81534462"
---
# <a name="application-configuration-options"></a>Toepassingsconfiguratieopties

In uw code initialiseert u een nieuwe openbare of vertrouwelijke clienttoepassing (of user-agent voor MSAL.js) om tokens te verifiëren en te verwerven. U een aantal configuratieopties instellen wanneer u de client-app initialiseert in Microsoft Authentication Library (MSAL). Deze opties vallen in twee groepen:

- Registratieopties, waaronder:
    - [Autoriteit](#authority) (bestaande uit de [instantie](#cloud-instance) van de identiteitsprovider en de [aanmeldingsdoelgroep](#application-audience) voor de app, en mogelijk de tenant-id).
    - [Client-ID](#client-id).
    - [Redirect URI](#redirect-uri).
    - [Client geheim](#client-secret) (voor vertrouwelijke client toepassingen).
- [Logopties](#logging), waaronder logboekniveau, controle van persoonlijke gegevens en de naam van de component met behulp van de bibliotheek.

## <a name="authority"></a>Instantie

De autoriteit is een URL die een map aangeeft waar MSAL tokens van kan aanvragen. Gemeenschappelijke autoriteiten zijn:

- https\:\</login.microsoftonline.com/\>tenant &lt;/, waarbij tenant&gt; de tenant-id is van de Azure Active Directory -tenant (Azure AD) of een domein dat is gekoppeld aan deze Azure AD-tenant. Alleen gebruikt om gebruikers van een specifieke organisatie aan te melden.
- https\:-login.microsoftonline.com/common/. Wordt gebruikt om gebruikers aan te melden met werk- en schoolaccounts of persoonlijke Microsoft-accounts.
- https\:-login.microsoftonline.com/organizations/. Wordt gebruikt om gebruikers aan te melden met werk- en schoolaccounts.
- https\:-login.microsoftonline.com/consumers/. Wordt gebruikt om gebruikers aan te melden met alleen persoonlijke Microsoft-accounts (voorheen bekend als Windows Live ID-accounts).

De instelling van de autoriteit moet consistent zijn met wat er is aangegeven in het aanvraagregistratieportaal.

De url van de autoriteit bestaat uit de instantie en het publiek.

De autoriteit kan zijn:
- Een Azure AD-cloudautoriteit.
- Een Azure AD B2C-autoriteit. Zie [B2C-specificaties](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/AAD-B2C-specifics).
- Een AD FS-autoriteit (Active Directory Federation Services). Zie [AD FS-ondersteuning](https://aka.ms/msal-net-adfs-support).

Azure AD-cloudautoriteiten hebben twee delen:
- De *instantie* van de identiteitsprovider
- De *aanmeldingsdoelgroep* voor de app

De instantie en doelgroep kunnen worden samengevoegd en worden verstrekt als de URL van de autoriteit. In versies van MSAL.NET eerder dan MSAL 3. *x*, je moest de autoriteit zelf samenstellen, op basis van de cloud die je wilde targeten en de aanmeldingsdoelgroep.  In dit diagram ziet u hoe de URL van de autoriteit is samengesteld:

![Hoe de url van de autoriteit is samengesteld](media/msal-client-application-configuration/authority.png)

## <a name="cloud-instance"></a>Cloud-instantie

De *instantie* wordt gebruikt om aan te geven of uw app gebruikers ondertekent vanuit de openbare Azure-cloud of vanuit nationale clouds. Met BEHULP van MSAL in uw code u de Azure-cloudinstantie instellen met behulp van `Instance` een opsomming of door de URL door te geven aan de [nationale cloudinstantie](authentication-national-cloud.md#azure-ad-authentication-endpoints) als lid (als u het weet).

MSAL.NET een expliciete uitzondering `Instance` als `AzureCloudInstance` beide en zijn opgegeven.

Als u geen instantie opgeeft, is uw app gericht op de `https://login.onmicrosoftonline.com`openbare cloudinstantie Azure (de instantie van URL).

## <a name="application-audience"></a>Doelgroep voor toepassingen

De aanmeldingsdoelgroep is afhankelijk van de zakelijke behoeften van uw app:
- Als u een lob-ontwikkelaar (Line) bent, produceert u waarschijnlijk een toepassing met één tenant die alleen in uw organisatie wordt gebruikt. In dat geval moet u de organisatie opgeven, hetzij op basis van de tenant-id (de id van uw Azure AD-exemplaar) of door een domeinnaam die is gekoppeld aan het Ad-exemplaar azure.
- Als u een ISV bent, u gebruikers aanmelden met hun werk- en schoolaccounts in een organisatie of in sommige organisaties (multitenant-app). Maar u wilt misschien ook dat gebruikers zich aanmelden met hun persoonlijke Microsoft-accounts.

### <a name="how-to-specify-the-audience-in-your-codeconfiguration"></a>De doelgroep in uw code/configuratie opgeven

Met MSAL in uw code geeft u de doelgroep op met een van de volgende waarden:
- De opsomming van de Azure AD-autoriteit
- De tenant-ID, die kan zijn:
  - Een GUID (de id van uw Azure AD-exemplaar), voor toepassingen met één tenant
  - Een domeinnaam die is gekoppeld aan uw Azure AD-exemplaar (ook voor toepassingen met één tenant)
- Een van deze tijdelijke aanduidingen als tenant-id in plaats van de doelgroep van azure AD-autoriteit:
    - `organizations`voor een multitenant-toepassing
    - `consumers`om gebruikers alleen aan te melden met hun persoonlijke accounts
    - `common`om gebruikers aan te melden met hun werk- en schoolaccounts of hun persoonlijke Microsoft-accounts

MSAL geeft een zinvolle uitzondering als u zowel de Azure AD-autoriteitsdoelgroep als de tenant-id opgeeft.

Als u geen doelgroep opgeeft, is uw app gericht op Azure AD en persoonlijke Microsoft-accounts als doelgroep. (Dat wil zeggen, het `common` zal zich gedragen alsof ze zijn opgegeven.)

### <a name="effective-audience"></a>Effectief publiek

De effectieve doelgroep voor je toepassing is het minimum (als er een snijpunt is) van het publiek dat je in je app instelt en het publiek dat is opgegeven in de app-registratie. In de ervaring [met app-registraties](https://aka.ms/appregistrations) u de doelgroep (de ondersteunde accounttypen) voor de app opgeven. Zie [Snelstart: Een toepassing registreren bij het Microsoft-identiteitsplatform](quickstart-register-app.md)voor meer informatie.

Momenteel is de enige manier om een app aan te melden voor gebruikers met alleen persoonlijke Microsoft-accounts door beide instellingen te configureren:
- Stel de doelgroep `Work and school accounts and personal accounts`voor app-registratie in op .
- Stel de doelgroep in uw `AadAuthorityAudience.PersonalMicrosoftAccount` code/configuratie in op (of `TenantID` ="consumenten").

## <a name="client-id"></a>Client-id

De client-id is de unieke toepassings-id die door Azure AD aan uw app is toegewezen toen de app werd geregistreerd.

## <a name="redirect-uri"></a>Omleidings-URI

De omleiding URI is de URI de identiteit provider stuurt de security tokens terug naar.

### <a name="redirect-uri-for-public-client-apps"></a>URI omleiden voor openbare client-apps

Als u een ontwikkelaar van een openbare client-app bent die MSAL gebruikt:
- U wilt gebruiken `.WithDefaultRedirectUri()` in desktop- of UWP-toepassingen (MSAL.NET 4,1+). Met deze methode wordt de eigenschap redirect uri van de openbare clienttoepassing ingesteld op de standaard aanbevolen omleidingsuri voor openbare clienttoepassingen.

  Platform  | Omleidings-URI
  ---------  | --------------
  Bureaublad-app (.NET FW) | `https://login.microsoftonline.com/common/oauth2/nativeclient`
  UWP | waarde `WebAuthenticationBroker.GetCurrentApplicationCallbackUri()`van . Dit stelt SSO met de browser in staat door de waarde in te stellen op het resultaat van WebAuthenticationBroker.GetCurrentApplicationCallbackUri() die u moet registreren
  .NET Core | `https://localhost`. Hierdoor kan de gebruiker de systeembrowser gebruiken voor interactieve verificatie, omdat .NET Core op dit moment geen gebruikersinterface heeft voor de ingesloten webweergave.

- U hoeft geen omleiding URI toe te voegen als u een Xamarin Android- en iOS-toepassing bouwt die `msal{ClientId}://auth` geen broker ondersteunt (de omleiding URI wordt automatisch ingesteld op Xamarin Android en iOS

- U moet de omleiding URI configureren in [App-registraties:](https://aka.ms/appregistrations)

   ![URI omleiden in app-registraties](media/msal-client-application-configuration/redirect-uri.png)

U de omleiding URI `RedirectUri` overschrijven met behulp van de eigenschap (bijvoorbeeld als u makelaars gebruikt). Hier volgen enkele voorbeelden van omleidings-URI's voor dat scenario:

- `RedirectUriOnAndroid`= "msauth-5a434691-ccb2-4fd1-b97b-b64bcfbc03fc://com.microsoft.identity.client.sample";
- `RedirectUriOnIos`= $"msauth. {Bundle.ID}://auth";

Zie [IOS-toepassingen migreren van Microsoft Authenticator van ADAL.NET naar MSAL.NET](msal-net-migration-ios-broker.md) en [Gebruikmaken van de broker op iOS](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS)voor meer iOS-details.
Zie [Brokered auth in Android voor](brokered-auth.md)meer Android-details.

### <a name="redirect-uri-for-confidential-client-apps"></a>URI omleiden voor vertrouwelijke client-apps

Voor web-apps is de omleiding URI (of reply URI) de URI die Azure AD gebruikt om het token terug te sturen naar de toepassing. Deze URI kan de URL van de web-app /web-API zijn als de vertrouwelijke app een van deze is. De omleiding URI moet worden geregistreerd in app registratie. Deze registratie is vooral belangrijk wanneer u een app implementeert die u in eerste instantie lokaal hebt getest. Vervolgens moet u de antwoord-URL van de geïmplementeerde app toevoegen aan de portal voor toepassingsregistratie.

Voor daemon-apps hoeft u geen omleidingsURI op te geven.

## <a name="client-secret"></a>Clientgeheim

Met deze optie wordt het clientgeheim voor de vertrouwelijke client-app opgegeven. Dit geheim (app-wachtwoord) wordt geleverd door de portal voor toepassingsregistratie of wordt verstrekt aan Azure AD tijdens app-registratie met PowerShell AzureAD, PowerShell AzureRM of Azure CLI.

## <a name="logging"></a>Logboekregistratie

De andere configuratieopties maken logboekregistratie en probleemoplossing mogelijk. Zie het [artikel Logboekregistratie](msal-logging.md) voor meer informatie over het gebruik ervan.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [het instantiën van clienttoepassingen met behulp van MSAL.NET.](msal-net-initializing-client-applications.md)
Meer informatie over [het instantiën van clienttoepassingen met MSAL.js](msal-js-initializing-client-applications.md).
