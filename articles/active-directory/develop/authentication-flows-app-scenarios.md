---
title: Micro soft Identity platform-verificatie stromen & app-scenario's | Azure
description: Meer informatie over toepassings scenario's voor het micro soft-identiteits platform, inclusief verificatie-identiteiten, het verkrijgen van tokens en het aanroepen van beveiligde Api's.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/03/2020
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: bf47a352542be0ad4467ed420cc38f62da0d47dc
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81534844"
---
# <a name="authentication-flows-and-application-scenarios"></a>Verificatie stromen en toepassings scenario's

Het micro soft Identity platform (v 2.0)-eind punt ondersteunt verificatie voor verschillende soorten architectuur van moderne toepassingen. Alle architecturen zijn gebaseerd op de industrie standaard protocollen [OAuth 2,0 en OpenID Connect Connect](active-directory-v2-protocols.md).  Met behulp van de [micro soft-verificatie bibliotheken voor identiteits platforms](reference-v2-libraries.md)verifiëren toepassingen identiteiten en verkrijgen tokens voor toegang tot beveiligde api's.

In dit artikel worden de verificatie stromen en de toepassings scenario's beschreven die worden gebruikt in:

- [Toepassings scenario's en ondersteunde verificatie stromen](#scenarios-and-supported-authentication-flows).
- [Toepassings scenario's en ondersteunde platforms en talen](#scenarios-and-supported-platforms-and-languages).

## <a name="application-categories"></a>Toepassings Categorieën

Tokens kunnen worden verkregen uit verschillende soorten toepassingen, waaronder:

- Web-apps
- Mobiele apps
- Desktop-apps
- Web-API's

Tokens kunnen ook worden verkregen van apps die worden uitgevoerd op apparaten die geen browser hebben of die op IoT worden uitgevoerd.

Toepassingen kunnen worden gecategoriseerd als in de volgende lijst:

- [Beveiligde bronnen versus client toepassingen](#protected-resources-vs-client-applications): sommige scenario's zijn het beveiligen van resources zoals web-apps of Web-api's. Andere scenario's zijn het verkrijgen van een beveiligings token om een beveiligde web-API aan te roepen.
- [Met gebruikers of zonder gebruikers: voor](#with-users-or-without-users)sommige scenario's is een aangemelde gebruiker vereist, maar andere, zoals daemon-scenario's, hebben geen betrekking op een gebruiker.
- [Toepassingen met één pagina, open bare client en vertrouwelijke client](#single-page-public-client-and-confidential-client-applications): deze typen zijn drie grote categorieën toepassingen. Elk wordt gebruikt met verschillende bibliotheken en objecten.
- [Doel groep voor aanmelding](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types): de beschik bare verificatie stromen variëren afhankelijk van de doel groep voor het aanmelden. Sommige stromen zijn alleen beschikbaar voor werk-of school accounts. En sommige zijn beschikbaar voor werk-of school accounts en voor persoonlijke micro soft-accounts. De toegestane doel groep is afhankelijk van de verificatie stromen.
- [Ondersteunde OAuth 2,0-stromen](#scenarios-and-supported-authentication-flows): verificatie stromen worden gebruikt voor het implementeren van de toepassings scenario's die tokens aanvragen. Er is geen een-op-een-toewijzing tussen toepassings scenario's en verificatie stromen.
- [Ondersteunde platforms](#scenarios-and-supported-platforms-and-languages): niet alle toepassings scenario's zijn beschikbaar voor elk platform.

### <a name="protected-resources-vs-client-applications"></a>Beveiligde bronnen versus client toepassingen

Verificatie scenario's hebben twee activiteiten:

- **Beveiligings tokens ophalen voor een beveiligde web-API**: we raden u aan door [micro soft ondersteunde client bibliotheken](reference-v2-libraries.md#microsoft-supported-client-libraries) te gebruiken voor het verkrijgen van tokens, met name de micro soft Authentication Library (MSAL)-familie.
- **Een web-API of web-app beveiligen**: een uitdaging van het beveiligen van een web-API of web-app-resource valideert het beveiligings token. Op sommige platforms biedt micro soft [middleware-bibliotheken](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries).

### <a name="with-users-or-without-users"></a>Met gebruikers of zonder gebruikers

De meeste verificatie scenario's verkrijgen tokens namens aangemelde gebruikers.

![Scenario's met gebruikers](media/scenarios/scenarios-with-users.svg)

Er zijn echter ook scenario's voor het maken van daemon-apps, waarbij toepassingen tokens namens zichzelf verkrijgen zonder gebruiker.

![Scenario's met daemon-apps](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>Toepassingen met één pagina, open bare client en vertrouwelijke client

De beveiligings tokens kunnen worden verkregen uit meerdere typen toepassingen. Deze toepassingen worden vaak onderverdeeld in drie categorieën:

- **Toepassingen met één pagina**: ook wel Spas genoemd, zijn deze apps web apps waarin tokens worden verkregen van een Java script-of type script-app die in de browser wordt uitgevoerd. Veel moderne apps hebben een front-end toepassing met één pagina die voornamelijk is geschreven in Java script. De toepassing maakt vaak gebruik van een kader zoals hoek, reageren of Vue. MSAL. js is de enige micro soft-verificatie bibliotheek die ondersteuning biedt voor toepassingen met één pagina.

- **Open bare client toepassingen**: deze toepassingen moeten zich altijd aanmelden bij gebruikers:
  - Bureau blad-apps voor het aanroepen van web-Api's namens de aangemelde gebruiker
  - Mobiele apps
  - Apps die worden uitgevoerd op apparaten die geen browser hebben, zoals toepassingen die op iOT worden uitgevoerd

  Deze apps worden vertegenwoordigd door de MSAL [PublicClientApplication](/dotnet/api/microsoft.identity.client.publicclientapplication) -klasse. Zie [open bare client-en vertrouwelijke client toepassingen](msal-client-applications.md)voor meer informatie.

- **Vertrouwelijke client toepassingen**:
  - Web-apps die een web-API aanroepen
  - Web-Api's die een web-API aanroepen
  - Daemon-apps, zelfs wanneer deze zijn geïmplementeerd als een console service, zoals een Linux-daemon of een Windows-service

  Deze typen apps gebruiken de [ConfidentialClientApplication](/dotnet/api/microsoft.identity.client.confidentialclientapplication) -klasse. Zie [open bare client-en vertrouwelijke client toepassingen](msal-client-applications.md)voor meer informatie.

## <a name="application-scenarios"></a>Toepassingsscenario's

Het micro soft Identity platform-eind punt ondersteunt verificatie voor verschillende app-architecturen:

- Toepassingen met één pagina
- Web-apps
- Web-API's
- Mobiele apps
- Systeemeigen apps
- Daemon-apps
- Apps aan de server zijde

Toepassingen gebruiken de verschillende verificatie stromen voor het aanmelden van gebruikers en het ophalen van tokens om beveiligde Api's aan te roepen.

### <a name="a-single-page-application"></a>Een toepassing met één pagina

Veel moderne web-apps zijn gebouwd als toepassingen met één pagina aan de client zijde. Deze toepassingen gebruiken Java script of een toepassings raamwerk met één pagina zoals hoek, vue. js en reageren. js. Deze toepassingen worden uitgevoerd in een webbrowser.

De verificatie-eigenschappen verschillen van traditionele web-apps aan de server zijde. Door gebruik te maken van het micro soft-identiteits platform kunnen toepassingen met één pagina gebruikers aanmelden en tokens verkrijgen voor toegang tot back-end-services of Web-Api's.

![Een toepassing met één pagina](media/scenarios/spa-app.svg)

Zie [toepassingen met één pagina](scenario-spa-overview.md)voor meer informatie.

### <a name="a-web-app-that-is-signing-in-a-user"></a>Een web-app die wordt aangemeld bij een gebruiker

![Een web-app die een gebruiker aanmeldt](media/scenarios/scenario-webapp-signs-in-users.svg)

Een web-app beveiligen die zich aanmeldt bij een gebruiker:

- Als u in .NET ontwikkelt, gebruikt u ASP.NET of ASP.NET Core met de ASP.NET Open ID Connect middleware. Het beveiligen van een resource omvat het valideren van het beveiligings token, dat wordt uitgevoerd door de [Identity model-extensies voor .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) -bibliotheek en niet MSAL-bibliotheken.

- Als u in node. js ontwikkelt, gebruikt u Pass Port. js.

Zie voor meer informatie [Web-app die gebruikers aantekent](scenario-web-app-sign-user-overview.md).

### <a name="a-web-app-that-signs-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>Een web-app die zich aanmeldt bij een gebruiker en een web-API aanroept namens de gebruiker

![Een web-app die web-Api's aanroept](media/scenarios/web-app.svg)

Als u een web-API wilt aanroepen vanuit een web-app namens een gebruiker, gebruikt u de klasse MSAL **ConfidentialClientApplication** . U gebruikt de autorisatie code stroom en slaat de aangeschafte tokens op in de token cache. Als dat nodig is, vernieuwt MSAL tokens en de controller wordt op de achtergrond tokens opgehaald uit de cache.

Zie [een web-app die web-api's aanroept](scenario-web-app-call-api-overview.md)voor meer informatie.

### <a name="a-desktop-app-calling-a-web-api-on-behalf-of-a-signed-in-user"></a>Een bureau blad-app die namens een aangemelde gebruiker een web-API aanroept

Voor een bureau blad-app om een web-API aan te roepen waarmee gebruikers zich aanmelden, gebruikt u de interactieve methoden voor het verkrijgen van tokens van de klasse MSAL **PublicClientApplication** . Met deze interactieve methoden kunt u de UI-ervaring voor aanmelden beheren. MSAL maakt gebruik van een webbrowser voor deze interactie.

![Een bureau blad-app die een web-API aanroept](media/scenarios/desktop-app.svg)

Er is een andere mogelijkheid voor door Windows gehoste toepassingen op computers die zijn toegevoegd aan een Windows-domein of door Azure Active Directory (Azure AD). Deze toepassingen kunnen een token op de achtergrond verkrijgen door gebruik te maken van [geïntegreerde Windows-verificatie](https://aka.ms/msal-net-iwa).

Toepassingen die worden uitgevoerd op een apparaat zonder browser, kunnen nog steeds een API aanroepen namens een gebruiker. De gebruiker moet zich aanmelden op een ander apparaat met een webbrowser om te verifiëren. Voor dit scenario moet u de [code stroom](https://aka.ms/msal-net-device-code-flow)van het apparaat gebruiken.

![Toestel code stroom](media/scenarios/device-code-flow-app.svg)

Hoewel het niet wordt aangeraden om het te gebruiken, is de werk [stroom gebruikers naam en wacht woord](https://aka.ms/msal-net-up) beschikbaar in open bare client toepassingen. Deze stroom is nog steeds nodig in sommige scenario's zoals DevOps.

Maar met deze stroom beperkt u uw toepassingen. Zo kunnen toepassingen zich niet aanmelden bij een gebruiker die multi-factor Authentication of voorwaardelijke toegang moet gebruiken. Uw toepassingen profiteren ook niet van eenmalige aanmelding.

Verificatie met de gebruikers naam/wacht woord stroom verloopt tegen de principes van moderne authenticatie en wordt alleen voor verouderde redenen verschaft.

In bureau blad-apps kunt u de token cache- [serialisatie](https://aka.ms/msal-net-token-cache-serialization)aanpassen als u wilt dat de token cache persistent blijft. Door het implementeren van [Dual token cache-serialisatie](https://aka.ms/msal-net-dual-cache-serialization)kunt u achterwaarts compatibele en forward-compatibele token caches gebruiken. Deze tokens ondersteunen vorige generaties van verificatie bibliotheken. Voor beelden van specifieke bibliotheken zijn Azure AD-verificatie bibliotheek voor .NET (ADAL.NET) versie 3 en versie 4.

Zie voor meer informatie [bureau blad-app die web-api's aanroept](scenario-desktop-overview.md).

### <a name="a-mobile-app-calling-a-web-api-on-behalf-of-an-interactive-user"></a>Een mobiele app die een web-API aanroept namens een interactieve gebruiker

Net als bij een bureau blad-app roept een mobiele app de interactieve methoden voor het verkrijgen van tokens van de MSAL **PublicClientApplication** -klasse op om een token op te halen voor het aanroepen van een web-API.

![Een mobiele app die een web-API aanroept](media/scenarios/mobile-app.svg)

MSAL iOS en MSAL Android gebruiken standaard de systeem webbrowser. U kunt ze echter ook gebruiken om in plaats daarvan de Inge sloten webweergave in te richten. Er zijn specifieke kenmerken die afhankelijk zijn van het mobiele platform: Universeel Windows-platform (UWP), iOS of Android.

In sommige scenario's, zoals die voor voorwaardelijke toegang tot een apparaat-ID of een apparaatregistratie, moet een Broker op het apparaat worden geïnstalleerd. Voor beelden van makelaars zijn micro soft Bedrijfsportal op Android en Microsoft Authenticator op Android en iOS. MSAL kan nu communiceren met brokers. Zie voor meer informatie gebruikmaken [van makelaars op Android en IOS](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS).

Zie voor meer informatie [mobiele app die web-api's aanroept](scenario-mobile-overview.md).

> [!NOTE]
> Uw mobiele app die gebruikmaakt van MSAL. iOS, MSAL. Voor Android-of MSAL.NET op Xamarin kunnen app-beveiligings beleid worden toegepast. Het beleid kan bijvoorbeeld voor komen dat een gebruiker beveiligde tekst kopieert. De mobiele app wordt beheerd door intune en wordt door intune herkend als een beheerde app. Zie [Microsoft intune app SDK Overview](https://docs.microsoft.com/intune/app-sdk)(Engelstalig) voor meer informatie.
>
> De [intune app SDK](https://docs.microsoft.com/intune/app-sdk-get-started) is gescheiden van MSAL-bibliotheken en communiceert zelf met Azure AD.

### <a name="a-protected-web-api"></a>Een beveiligde web-API

U kunt het micro soft Identity platform-eind punt gebruiken om webservices te beveiligen, zoals de REST Web-API van uw app. Een beveiligde web-API wordt aangeroepen met behulp van een toegangs token. Het token beveiligt de gegevens van de API en voor het verifiëren van binnenkomende aanvragen. De aanroeper van een web-API voegt een toegangs token toe in de autorisatie-header van een HTTP-aanvraag.

Als u uw ASP.NET of ASP.NET Core Web-API wilt beveiligen, moet u het toegangs token valideren. Voor deze validatie gebruikt u de ASP.NET JWT-middleware. De validatie wordt uitgevoerd door de [Identity model-uitbrei dingen voor de .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) -bibliotheek en niet door MSAL.net.

Zie [Protected Web API](scenario-protected-web-api-overview.md)(Engelstalig) voor meer informatie.

### <a name="a-web-api-calling-another-web-api-on-behalf-of-a-user"></a>Een web-API die namens een gebruiker een andere web-API aanroept

Voor uw ASP.NET of ASP.NET Core beveiligde web-API om een andere web-API namens een gebruiker aan te roepen, moet uw app een token verkrijgen voor de downstream Web-API. Voor het verkrijgen van een token roept uw app de methode [AcquireTokenOnBehalfOf](https://aka.ms/msal-net-on-behalf-of) van de klasse **ConfidentialClientApplication** aan. Dergelijke aanroepen worden ook wel *service-naar-service* -aanroepen genoemd. De Web-Api's die andere web-Api's aanroepen, moeten aangepaste cache-serialisatie bieden.

  ![Een web-API waarmee een andere web-API wordt aangeroepen](media/scenarios/web-api.svg)

Zie [Web API voor het aanroepen van web-api's](scenario-web-api-call-api-overview.md)voor meer informatie.

### <a name="a-daemon-app-calling-a-web-api-in-the-daemons-name"></a>Een daemon-app die een web-API aanroept in de naam van de daemon

Apps die langlopende processen hebben of die werken zonder tussen komst van de gebruiker, hebben ook een manier nodig om toegang te krijgen tot beveiligde web-Api's. Een dergelijke app kan tokens verifiëren en ophalen met behulp van de identiteit van de app. De app bewijst zijn identiteit door gebruik te maken van een client geheim of certificaat.

U kunt dergelijke daemon-apps schrijven die een token voor de aanroepende app verkrijgen door gebruik te maken van de MSAL-methoden voor het ophalen van de [client referenties](https://aka.ms/msal-net-client-credentials) van de **ConfidentialClientApplication** -klasse. Deze methoden vereisen dat de aanroepende app een geheim heeft geregistreerd bij Azure AD. De App deelt het geheim vervolgens met de aangeroepen daemon. Voor beelden van dergelijke geheimen zijn toepassings wachtwoorden, certificaat bevestiging of client verklaringen.

![Een daemon-app die wordt aangeroepen door andere apps en Api's](media/scenarios/daemon-app.svg)

Zie voor meer informatie [daemon-toepassing die web-api's aanroept](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Scenario's en ondersteunde verificatie stromen

Scenario's met betrekking tot het verkrijgen van tokens zijn ook toegewezen aan OAuth 2,0-verificatie stromen. Zie [OAuth 2,0 en OpenID Connect Connect protocollen op het micro soft Identity-platform](active-directory-v2-protocols.md)voor meer informatie.

<table>
 <thead>
  <tr><th>Scenario</th> <th>Gedetailleerd scenario door lopen</th> <th>OAuth 2,0-stroom en Grant</th> <th>Doelgroep</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">App met één pagina</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">Wenst</a></td>
   <td>Werk-of school accounts, persoonlijke accounts en Microsoft Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web app that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">Een web-app die gebruikers aanmeldt</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Autorisatie code</a></td>
   <td>Werk-of school accounts, persoonlijke accounts en Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web app that signs in users" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">Een web-app die web-Api's aanroept</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Autorisatie code</a></td>
   <td>Werk-of school accounts, persoonlijke accounts en Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">Een bureau blad-app die web-Api's aanroept</a></td>
   <td>Interactief door gebruik te maken van <a href="v2-oauth2-auth-code-flow.md">autorisatie code</a> met PKCE</td>
   <td>Werk-of school accounts, persoonlijke accounts en Azure AD B2C</td>
 </tr>

  <tr>
   <td>Geïntegreerde Windows-verificatie</td>
   <td>Werk- of schoolaccounts</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Wacht woord van resource-eigenaar</a></td>
   <td>Werk-of school accounts en Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">Apparaatcode</a></td>
   <td>Werk- of schoolaccounts</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">Een mobiele app die web-Api's aanroept</a></td>
   <td>Interactief door gebruik te maken van <a href="v2-oauth2-auth-code-flow.md">autorisatie code</a> met PKCE</td>
   <td>Werk-of school accounts, persoonlijke accounts en Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Wacht woord van resource-eigenaar</a></td>
   <td>Werk-of school accounts en Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href="scenario-daemon-overview.md">Een daemon-app die web-Api's aanroept</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">Clientreferenties</a></td>
   <td>Alleen app-machtigingen zonder gebruiker en worden alleen gebruikt in azure AD-organisaties</td>
 </tr>

  <tr>
   <td><a href="scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href="scenario-web-api-call-api-overview.md">Een web-API die web-Api's aanroept</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">Namens</a></td>
   <td>Werk-of school accounts en persoonlijke accounts</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>Scenario's en ondersteunde platforms en talen

Micro soft-verificatie bibliotheken ondersteunen meerdere platforms:

- Javascript
- .NET Framework
- .NET Core
- Windows 10-UWP
- Xamarin.iOS
- Xamarin.Android
- Systeem eigen iOS
- macOS
- Systeem eigen Android
- Java
- Python

U kunt ook verschillende talen gebruiken om uw toepassingen te bouwen.

> [!NOTE]
> Sommige toepassings typen zijn niet beschikbaar op elk platform.

In de kolom Windows van de volgende tabel wordt elke keer dat er .NET core wordt vermeld, .NET Framework ook mogelijk. Deze laatste wordt wegge laten om te voor komen dat de tabel onoverzichtelijk wordt.

|Scenario  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [App met één pagina](scenario-spa-overview.md) <br/>[![App met één pagina](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Web-app waarmee gebruikers worden aangemeld](scenario-web-app-sign-user-overview.md) <br/>[![Een web-app die gebruikers aanmeldt](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core
| [Web-app die web-API's aanroept](scenario-web-app-call-api-overview.md) <br/> <br/>[![Web-app die web-API's aanroept](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>Kolf + MSAL python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>Kolf + MSAL python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>Kolf + MSAL python
| [Desktop-app die web-API's aanroept](scenario-desktop-overview.md) <br/> <br/>Bureau blad- ![ [app die web-api's aanroept apparaat ![](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) code stroom](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python <br/> ![iOS/objectief C of SWIFT](media/sample-v2-code/small_logo_iOS.png) MSAL. objc |
| [Mobiele app die web-API's aanroept](scenario-mobile-overview.md) <br/> [![Mobiele app die web-API's aanroept](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS/objectief C of SWIFT](media/sample-v2-code/small_logo_iOS.png) MSAL. objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL. Android
| [Daemon-apps](scenario-daemon-overview.md) <br/> [![Daemon-apps](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python
| [Web-API die web-API's aanroept](scenario-web-api-call-api-overview.md) <br/><br/> [![Web-API die web-API's aanroept](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python

Zie voor meer informatie [door micro soft ondersteunde bibliotheken per OS/taal](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language).

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over de [basis principes van verificatie](authentication-scenarios.md) en [toegangs tokens voor micro soft Identity platform](access-tokens.md).
* Meer informatie over [het beveiligen van de toegang tot IOT-apps](/azure/architecture/example-scenario/iot-aad/iot-aad).
