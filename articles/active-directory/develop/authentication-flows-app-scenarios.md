---
title: Verificatiestromen van Microsoft-identiteitsplatform &-app-scenario's | Azure
description: Meer informatie over toepassingsscenario's voor het Microsoft-identiteitsplatform, waaronder het verifiëren van identiteiten, het verwerven van tokens en het aanroepen van beveiligde API's.
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
ms.openlocfilehash: e73da5e7b440a216841fffd65ca2e1b95de7a609
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480926"
---
# <a name="authentication-flows-and-application-scenarios"></a>Verificatiestromen en toepassingsscenario's

Het Microsoft Identity Platform (v2.0) endpoint ondersteunt verificatie voor verschillende soorten moderne toepassingsarchitecturen. Alle architecturen zijn gebaseerd op de industriestandaard protocollen [OAuth 2.0 en OpenID Connect.](active-directory-v2-protocols.md)  Met behulp van de [verificatiebibliotheken van het Microsoft-identiteitsplatform](reference-v2-libraries.md)verifiëren toepassingen identiteiten en krijgen ze tokens om toegang te krijgen tot beveiligde API's.

In dit artikel worden verificatiestromen en de toepassingsscenario's beschreven waarin ze worden gebruikt:

- [Toepassingsscenario's en ondersteunde verificatiestromen](#scenarios-and-supported-authentication-flows).
- [Toepassingsscenario's en ondersteunde platforms en talen.](#scenarios-and-supported-platforms-and-languages)

## <a name="application-categories"></a>Toepassingscategorieën

Tokens kunnen worden verkregen uit verschillende soorten toepassingen, waaronder:

- Web-apps
- Mobiele apps
- Desktop-apps
- Web-API's

Tokens kunnen ook worden verkregen uit apps die worden uitgevoerd op apparaten die geen browser hebben of die op IoT worden uitgevoerd.

Toepassingen kunnen worden gecategoriseerd als in de volgende lijst:

- [Beveiligde bronnen versus clienttoepassingen:](#protected-resources-vs-client-applications)Sommige scenario's gaan over het beschermen van bronnen zoals web-apps of web-API's. Andere scenario's gaan over het verkrijgen van een beveiligingstoken om een beveiligde web-API aan te roepen.
- [Met gebruikers of zonder gebruikers:](#with-users-or-without-users)Bij sommige scenario's gaat het om een aangemelde gebruiker, maar andere, zoals daemon-scenario's, hebben geen betrekking op een gebruiker.
- [Toepassingen met één pagina, openbare client en vertrouwelijke client:](#single-page-public-client-and-confidential-client-applications)deze typen zijn drie grote categorieën toepassingen. Elk wordt gebruikt met verschillende bibliotheken en objecten.
- [Aanmeldingsdoelgroep](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types): De beschikbare verificatiestromen verschillen afhankelijk van de aanmeldingsdoelgroep. Sommige stromen zijn alleen beschikbaar voor werk- of schoolaccounts. En sommige zijn beschikbaar voor werk- of schoolaccounts en voor persoonlijke Microsoft-accounts. De toegestane doelgroep is afhankelijk van de verificatiestromen.
- [Ondersteunde OAuth 2.0-stromen](#scenarios-and-supported-authentication-flows): Verificatiestromen worden gebruikt om de toepassingsscenario's te implementeren die tokens aanvragen. Er is geen één-op-één toewijzing tussen toepassingsscenario's en verificatiestromen.
- [Ondersteunde platforms](#scenarios-and-supported-platforms-and-languages): Niet alle toepassingsscenario's zijn beschikbaar voor elk platform.

### <a name="protected-resources-vs-client-applications"></a>Beveiligde resources versus clienttoepassingen

Verificatiescenario's omvatten twee activiteiten:

- **Beveiligingstokens aanschaffen voor een beveiligde web-API:** We raden u aan [door Microsoft ondersteunde clientbibliotheken](reference-v2-libraries.md#microsoft-supported-client-libraries) te gebruiken om tokens te verkrijgen, met name de MSAL-familie (Microsoft Authentication Library).
- **Een web-API of een web-app beveiligen:** Een uitdaging voor het beschermen van een web-API of web-app-bron is het valideren van het beveiligingstoken. Op sommige platforms biedt Microsoft [middleware-bibliotheken](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries).

### <a name="with-users-or-without-users"></a>Met gebruikers of zonder gebruikers

De meeste verificatiescenario's verwerven tokens namens ingelogde gebruikers.

![Scenario's met gebruikers](media/scenarios/scenarios-with-users.svg)

Er zijn echter ook daemon-app-scenario's, waarin toepassingen tokens namens zichzelf verwerven zonder gebruiker.

![Scenario's met daemon-apps](media/scenarios/daemon-app.svg)

### <a name="single-page-public-client-and-confidential-client-applications"></a>Toepassingen voor één pagina, openbare client en vertrouwelijke client

De beveiligingstokens kunnen worden verkregen uit meerdere soorten toepassingen. Deze toepassingen zijn meestal onderverdeeld in drie categorieën:

- **Toepassingen met één pagina:** ook wel SB's genoemd, deze apps zijn web-apps waarin tokens worden verkregen uit een JavaScript- of TypeScript-app die in de browser wordt uitgevoerd. Veel moderne apps hebben een front-end toepassing van één pagina die voornamelijk in JavaScript is geschreven. De applicatie maakt vaak gebruik van een framework zoals Angular, React of Vue. MSAL.js is de enige Microsoft-verificatiebibliotheek die toepassingen met één pagina ondersteunt.

- **Openbare clienttoepassingen**: Deze toepassingen loggen altijd in op gebruikers:
  - Bureaublad-apps die webAPI's aanroepen namens de aangemelde gebruiker
  - Mobiele apps
  - Apps die worden uitgevoerd op apparaten die geen browser hebben, zoals apps die op iOT worden uitgevoerd

  Deze apps worden vertegenwoordigd door de klasse MSAL [PublicClientApplication.](/dotnet/api/microsoft.identity.client.publicclientapplication) Zie [Openbare client- en vertrouwelijke clienttoepassingen](msal-client-applications.md)voor meer informatie.

- **Vertrouwelijke clienttoepassingen**:
  - Web-apps die een web-API aanroepen
  - Web-API's die een web-API aanroepen
  - Daemon apps, zelfs wanneer geïmplementeerd als een console service zoals een Linux daemon of een Windows-service

  Dit soort apps maakt gebruik van de klasse [ConfidentialClientApplication.](/dotnet/api/microsoft.identity.client.confidentialclientapplication) Zie [Openbare client- en vertrouwelijke clienttoepassingen](msal-client-applications.md)voor meer informatie.

## <a name="application-scenarios"></a>Toepassingsscenario's

Het eindpunt van het Microsoft-identiteitsplatform ondersteunt verificatie voor verschillende app-architecturen:

- Toepassingen met één pagina
- Web-apps
- Web-API's
- Mobiele apps
- Systeemeigen apps
- Daemon-apps
- Apps aan de serverzijde

Toepassingen gebruiken de verschillende verificatiestromen om gebruikers aan te melden en tokens te krijgen om beveiligde API's aan te roepen.

### <a name="a-single-page-application"></a>Een toepassing van één pagina

Veel moderne webapps zijn gebouwd als client-side single-page applicaties. Deze toepassingen gebruiken JavaScript of een toepassingsframework van één pagina zoals Angular, Vue.js en React.js. Deze toepassingen worden uitgevoerd in een webbrowser.

Hun verificatiekenmerken verschillen van traditionele webapps aan de serverzijde. Door gebruik te maken van het Microsoft-identiteitsplatform kunnen toepassingen met één pagina gebruikers aanmelden en tokens krijgen om toegang te krijgen tot back-endservices of web-API's.

![Een toepassing van één pagina](media/scenarios/spa-app.svg)

Zie [Toepassingen met één pagina](scenario-spa-overview.md)voor meer informatie .

### <a name="a-web-app-that-is-signing-in-a-user"></a>Een web-app die zich aanmeldt bij een gebruiker

![Een web-app die een gebruiker aantekent](media/scenarios/scenario-webapp-signs-in-users.svg)

Ga als lid van het internet naar een webapp die zich aanmeldt bij een gebruiker:

- Als u zich ontwikkelt in .NET, gebruikt u ASP.NET of ASP.NET Core met de ASP.NET Open ID Connect middleware. Als u een resource beschermt, moet het beveiligingstoken worden geldig, wat wordt gedaan door de [Extensies van IdentityModel voor .NET-bibliotheek](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) en niet door MSAL-bibliotheken.

- Als je je ontwikkelt in Node.js, gebruik je Passport.js.

Zie [Web-app die gebruikers aanmeldt](scenario-web-app-sign-user-overview.md)voor meer informatie.

### <a name="a-web-app-that-signs-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>Een web-app die een gebruiker aantekent en namens de gebruiker een web-API aanroept

![Een webapp die web-API's aanroept](media/scenarios/web-app.svg)

Als u namens een gebruiker een web-API wilt aanroepen vanuit een web-app, gebruikt u de klasse MSAL **ConfidentialClientApplication.** U gebruikt de machtigingscodestroom en slaat de verkregen tokens op in de tokencache. Indien nodig vernieuwt MSAL tokens en verwerft de controller stilletjes tokens uit de cache.

Zie [Web-app bellen webAPI's voor](scenario-web-app-call-api-overview.md)meer informatie .

### <a name="a-desktop-app-calling-a-web-api-on-behalf-of-a-signed-in-user"></a>Een bureaublad-app die namens een aangemelde gebruiker een web-API aanroept

Als u een desktop-app wilt aanroepen die een web-API aanroept die gebruikers aantekent, gebruikt u de interactieve methoden voor het verkrijgen van token van de klasse MSAL **PublicClientApplication.** Met deze interactieve methoden u de inlog-ervaring beheren. MSAL gebruikt een webbrowser voor deze interactie.

![Een bureaublad-app die een web-API aanroept](media/scenarios/desktop-app.svg)

Er is nog een mogelijkheid voor door Windows gehoste toepassingen op computers die zijn verbonden met een Windows-domein of azure active directory (Azure AD). Deze toepassingen kunnen in stilte een token aanschaffen met [geïntegreerde Windows-verificatie.](https://aka.ms/msal-net-iwa)

Toepassingen die worden uitgevoerd op een apparaat zonder browser, kunnen nog steeds een API aanroepen namens een gebruiker. Om te verifiëren, moet de gebruiker zich aanmelden op een ander apparaat met een webbrowser. Dit scenario vereist dat u de [apparaatcodestroom](https://aka.ms/msal-net-device-code-flow)gebruikt.

![Apparaatcodestroom](media/scenarios/device-code-flow-app.svg)

Hoewel we u niet aanraden het te gebruiken, is de [gebruikersnaam/wachtwoordstroom](https://aka.ms/msal-net-up) beschikbaar in openbare clienttoepassingen. Deze stroom is nog steeds nodig in sommige scenario's zoals DevOps.

Maar het gebruik van deze stroom beperkt uw toepassingen. Toepassingen kunnen zich bijvoorbeeld niet aanmelden bij een gebruiker die multi-factor authenticatie of voorwaardelijke toegang moet gebruiken. Uw toepassingen profiteren ook niet van eenmalige aanmelding.

Verificatie met de gebruikersnaam/wachtwoordstroom druist in tegen de principes van moderne verificatie en wordt alleen om oudere redenen geleverd.

Als u in bureaublad-apps wilt dat de tokencache blijft bestaan, past u de [serialisatie van](https://aka.ms/msal-net-token-cache-serialization)de tokencache aan. Door serialisatie met [dubbele tokencache](https://aka.ms/msal-net-dual-cache-serialization)te implementeren, u achterwaarts compatibele en voorwaarts compatibele tokencaches gebruiken. Deze tokens ondersteunen eerdere generaties verificatiebibliotheken. Specifieke bibliotheken omvatten Azure AD-verificatiebibliotheek voor .NET (ADAL.NET) versie 3 en versie 4.

Zie [Bureaublad-app die web-API's aanroept](scenario-desktop-overview.md)voor meer informatie .

### <a name="a-mobile-app-calling-a-web-api-on-behalf-of-an-interactive-user"></a>Een mobiele app die namens een interactieve gebruiker een web-API aanroept

Net als bij een desktop-app roept een mobiele app de interactieve token-acquisitiemethoden van de klasse MSAL **PublicClientApplication** aan om een token te verkrijgen voor het aanroepen van een web-API.

![Een mobiele app die een web-API aanroept](media/scenarios/mobile-app.svg)

MSAL iOS en MSAL Android gebruiken standaard de systeemwebbrowser. U ze echter naar de ingesloten webweergave sturen. Er zijn specifieke kenmerken die afhankelijk zijn van het mobiele platform: Universal Windows Platform (UWP), iOS of Android.

Voor sommige scenario's, zoals die met voorwaardelijke toegang met betrekking tot een apparaat-id of een apparaatinschrijving, moet een broker op het apparaat worden geïnstalleerd. Voorbeelden van makelaars zijn Microsoft Company Portal op Android en Microsoft Authenticator op Android en iOS. MSAL kan nu communiceren met makelaars. Zie [Brokers op Android en iOS gebruiken](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS)voor meer informatie.

Zie [Mobiele app die web-API's aanroept](scenario-mobile-overview.md)voor meer informatie .

> [!NOTE]
> Uw mobiele app die msal.iOS, MSAL gebruikt. Android of MSAL.NET op Xamarin kan app-beveiligingsbeleid toepassen. Het beleid kan bijvoorbeeld voorkomen dat een gebruiker beveiligde tekst kopieert. De mobiele app wordt beheerd door Intune en door Intune herkend als een beheerde app. Zie [Microsoft Intune App SDK-overzicht](https://docs.microsoft.com/intune/app-sdk)voor meer informatie.
>
> De [Intune App SDK](https://docs.microsoft.com/intune/app-sdk-get-started) staat los van MSAL-bibliotheken en werkt alleen met Azure AD.

### <a name="a-protected-web-api"></a>Een beveiligde web-API

U het eindpunt van het Microsoft-identiteitsplatform gebruiken om webservices zoals de RESTful-web-API van uw app te beveiligen. Een beveiligde web-API wordt aangeroepen met behulp van een toegangstoken. Het token beveiligt de gegevens van de API en verifieert binnenkomende aanvragen. De beller van een web-API voegt een toegangstoken toe in de autorisatiekop van een HTTP-aanvraag.

Als u uw ASP.NET wilt beschermen of ASP.NET Core Web API, moet u het toegangstoken valideren. Voor deze validatie gebruikt u de ASP.NET JWT middleware. De validatie wordt uitgevoerd door de [IdentityModel-extensies voor .NET-bibliotheek](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) en niet door MSAL.NET.

Zie [Beveiligde web-API](scenario-protected-web-api-overview.md)voor meer informatie .

### <a name="a-web-api-calling-another-web-api-on-behalf-of-a-user"></a>Een web-API die namens een gebruiker een andere web-API aanroept

Voor uw ASP.NET of ASP.NET Kernbeveiligde Web API om namens een gebruiker een andere web-API aan te roepen, moet uw app een token voor de downstream web-API aanschaffen. Om een token te verkrijgen, roept uw app de [AcquireTokenOnBehalfOf-methode van](https://aka.ms/msal-net-on-behalf-of) de **klasse ConfidentialClientApplication** aan. Dergelijke gesprekken worden ook *service-to-services-oproepen* genoemd. De web-API's die andere web-API's aanroepen, moeten aangepaste cacheserialisatie bieden.

  ![Een web-API die een andere web-API aanroept](media/scenarios/web-api.svg)

Zie [Web API die webAPI's aanroept](scenario-web-api-call-api-overview.md)voor meer informatie .

### <a name="a-daemon-app-calling-a-web-api-in-the-daemons-name"></a>Een daemon-app die een web-API aanroept in de naam van de daemon

Apps die langlopende processen hebben of die werken zonder interactie van de gebruiker, hebben ook een manier nodig om toegang te krijgen tot beveiligde web-API's. Zo'n app kan tokens verifiëren en ontvangen met behulp van de identiteit van de app. De app bewijst zijn identiteit met behulp van een geheim of certificaat van de klant.

U dergelijke daemon-apps schrijven die een token voor de aanroepende app verkrijgen met behulp van de methoden voor het verkrijgen [van clientreferenties](https://aka.ms/msal-net-client-credentials) van de klasse MSAL **ConfidentialClientApplication** class. Deze methoden vereisen dat de aanroepende app een geheim heeft geregistreerd bij Azure AD. De app deelt vervolgens het geheim met de zogenaamde daemon. Voorbeelden van dergelijke geheimen zijn toepassingswachtwoorden, certificaatbewering of clientbewering.

![Een daemon-app die door andere apps en API's wordt aangeroepen](media/scenarios/daemon-app.svg)

Zie [Daemon-toepassing die webAPI's aanroept](scenario-daemon-overview.md)voor meer informatie.

## <a name="scenarios-and-supported-authentication-flows"></a>Scenario's en ondersteunde verificatiestromen

Scenario's waarbij tokens worden verweten, worden ook toegewezen aan OAuth 2.0-verificatiestromen. Zie [OAuth 2.0- en OpenID Connect-protocollen op het Microsoft-identiteitsplatform](active-directory-v2-protocols.md)voor meer informatie.

<table>
 <thead>
  <tr><th>Scenario</th> <th>Gedetailleerde scenario-walk-through</th> <th>OAuth 2.0 flow en subsidie</th> <th>Doelgroep</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single-Page App" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">App van één pagina</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">Impliciete</a></td>
   <td>Werk- of schoolaccounts, persoonlijke accounts en Microsoft Azure Active Directory B2C (Azure AD B2C)</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web App that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">Een web-app die gebruikers aanmeldt</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Autorisatiecode</a></td>
   <td>Werk- of schoolaccounts, persoonlijke accounts en Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web App that signs in users" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">Een web-app die web-API's aanroept</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Autorisatiecode</a></td>
   <td>Werk- of schoolaccounts, persoonlijke accounts en Azure AD B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">Een bureaublad-app die web-API's aanroept</a></td>
   <td>Interactief door <a href="v2-oauth2-auth-code-flow.md">autorisatiecode</a> met PKCE te gebruiken</td>
   <td>Werk- of schoolaccounts, persoonlijke accounts en Azure AD B2C</td>
 </tr>

  <tr>
   <td>Geïntegreerde Windows Auth</td>
   <td>Werk- of schoolaccounts</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Wachtwoord voor broneigenaar</a></td>
   <td>Werk- of schoolaccounts en Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">Apparaatcode</a></td>
   <td>Werk- of schoolaccounts</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">Een mobiele app die web-API's aanroept</a></td>
   <td>Interactief door <a href="v2-oauth2-auth-code-flow.md">autorisatiecode</a> met PKCE te gebruiken</td>
   <td>Werk- of schoolaccounts, persoonlijke accounts en Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Wachtwoord voor broneigenaar</a></td>
   <td>Werk- of schoolaccounts en Azure AD B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href="scenario-daemon-overview.md">Een daemon-app die web-API's aanroept</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">Clientreferenties</a></td>
   <td>Alleen-app-machtigingen zonder gebruiker en alleen gebruikt in Azure AD-organisaties</td>
 </tr>

  <tr>
   <td><a href="scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href="scenario-web-api-call-api-overview.md">Een web-API die web-API's aanroept</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">Namens</a></td>
   <td>Werk- of schoolaccounts en persoonlijke accounts</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>Scenario's en ondersteunde platforms en talen

Microsoft-verificatiebibliotheken ondersteunen meerdere platforms:

- JavaScript
- .NET Framework
- .NET Core
- Windows 10/UWP
- Xamarin.iOS
- Xamarin.Android
- Native iOS
- macOS
- Native Android
- Java
- Python

U ook verschillende talen gebruiken om uw toepassingen te bouwen.

> [!NOTE]
> Sommige toepassingstypen zijn niet op elk platform beschikbaar.

In de windows-kolom van de volgende tabel is elke keer dat .NET Core wordt genoemd, .NET Framework ook mogelijk. Dit laatste wordt weggelaten om te voorkomen dat de tabel rommelig wordt.

|Scenario  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [App van één pagina](scenario-spa-overview.md) <br/>[![App met één pagina](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Web App die zich aanmeldt bij gebruikers](scenario-web-app-sign-user-overview.md) <br/>[![Web App die gebruikers aanmeldt](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core
| [Web App die web-API's aanroept](scenario-web-app-call-api-overview.md) <br/> <br/>[![Web App die web-API's aanroept](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png) <br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Kolf + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Kolf + MSAL Python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>Kolf + MSAL Python
| [Desktop-app die web-API's aanroept](scenario-desktop-overview.md) <br/> <br/>Bureaublad-app die ![web-API's apparaatcodestroom [ ![aanroept](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md)](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/> ![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python <br/> ![iOS / Doelstelling C of snel](media/sample-v2-code/small_logo_iOS.png) MSAL.objc MSAL.objc |
| [Mobiele app die web-API's aanroept](scenario-mobile-overview.md) <br/> [![Mobiele app die web-API's aanroept](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS / Doelstelling C of snel](media/sample-v2-code/small_logo_iOS.png) MSAL.objc MSAL.objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL. Android
| [Daemon-apps](scenario-daemon-overview.md) <br/> [![Daemon-apps](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python
| [Web-API die web-API's aanroept](scenario-web-api-call-api-overview.md) <br/><br/> [![Web-API die web-API's aanroept](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL Java](media/sample-v2-code/small_logo_java.png)<br/>MSAL Java<br/>![MSAL Python](media/sample-v2-code/small_logo_python.png)<br/>MSAL Python

Zie [door Microsoft ondersteunde bibliotheken op os /taal](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [de basisprincipes van Verificatie](authentication-scenarios.md) en [toegangstokens voor microsoft-identiteitsplatforms](access-tokens.md).
* Meer informatie over [Het beveiligen van toegang tot IoT-apps](/azure/architecture/example-scenario/iot-aad/iot-aad).
