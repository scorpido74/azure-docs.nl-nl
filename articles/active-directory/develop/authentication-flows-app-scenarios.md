---
title: Verificatie scenario's voor micro soft Identity platform | Azure
description: Meer informatie over verificatie stromen en toepassings scenario's voor micro soft Identity platform. Meer informatie over de verschillende typen toepassingen die identiteiten kunnen verifiëren, tokens verkrijgen en beveiligde Api's aanroepen.
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/27/2019
ms.author: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5330111e5ae56471d26ebc39dca1a036246945e1
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/27/2019
ms.locfileid: "71348658"
---
# <a name="authentication-flows-and-application-scenarios"></a>Verificatie stromen en toepassings scenario's

Het micro soft Identity platform (v 2.0)-eind punt ondersteunt verificatie voor verschillende moderne app-architecturen, die allemaal zijn gebaseerd op gestandaardiseerde protocollen [OAuth 2,0 of OpenID Connect Connect](active-directory-v2-protocols.md).  Met behulp van de [verificatie bibliotheken](reference-v2-libraries.md)verifiëren toepassingen identiteiten en verkrijgen tokens voor toegang tot beveiligde api's. In dit artikel worden de verschillende verificatie stromen en de toepassings scenario's beschreven waarin ze worden gebruikt.  Dit artikel bevat ook een lijst met [toepassings scenario's en ondersteunde verificatie stromen](#scenarios-and-supported-authentication-flows) en [toepassings scenario's en ondersteunde platforms en talen](#scenarios-and-supported-platforms-and-languages).

## <a name="application-categories"></a>Toepassings Categorieën

Tokens kunnen worden verkregen uit een aantal toepassings typen: Webtoepassingen, mobiele toepassingen, Web-Api's en toepassingen die worden uitgevoerd op apparaten die geen browser (of iOT) hebben. Toepassingen kunnen worden ingedeeld op basis van het volgende:

- [Beveiligde bronnen versus client toepassingen](#protected-resources-vs-client-applications). Sommige scenario's zijn het beveiligen van resources (Web Apps of Web-Api's) en andere voor het verkrijgen van een beveiligings token om een beveiligde web-API aan te roepen.
- [Met gebruikers of zonder gebruikers](#with-users-or-without-users). Bij sommige scenario's is een aangemelde gebruiker betrokken, terwijl andere niet de gebruiker (daemon-scenario's) vereist.
- [Toepassingen met één pagina, open bare client toepassingen en vertrouwelijke client toepassingen](#single-page-applications-public-client-applications-and-confidential-client-applications). Dit zijn drie grote categorieën toepassings typen. De bibliotheken en objecten die worden gebruikt om ze te manipuleren, wijken af.
- [Aanmelden publiek](v2-supported-account-types.md#certain-authentication-flows-dont-support-all-the-account-types). Sommige verificatie stromen zijn niet beschikbaar voor bepaalde doel groepen voor het aanmelden. Sommige stromen zijn alleen beschikbaar voor werk-of school accounts en sommige zijn beschikbaar voor werk-of school accounts en persoonlijke micro soft-accounts. De toegestane doel groep is afhankelijk van de verificatie stromen.
- [Ondersteunde OAuth 2,0-stromen](#scenarios-and-supported-authentication-flows).  Verificatie stromen worden gebruikt voor het implementeren van de toepassings scenario's die tokens aanvragen.  Er is geen een-op-een-toewijzing tussen toepassings scenario's en verificatie stromen.
- [Ondersteunde platforms](#scenarios-and-supported-platforms-and-languages). Niet alle toepassings scenario's zijn beschikbaar voor elk platform.

### <a name="protected-resources-vs-client-applications"></a>Beveiligde bronnen versus client toepassingen

Verificatie scenario's hebben twee activiteiten:

- **Beveiligings tokens ophalen** voor een beveiligde web-API. Micro soft raadt u aan om [verificatie bibliotheken](reference-v2-libraries.md#microsoft-supported-client-libraries) te gebruiken voor het verkrijgen van tokens, met name de micro soft-verificatie bibliotheken-familie (MSAL)
- **Een web-API beveiligen** (of een web-app). Een van de uitdagingen van het beveiligen van een bron (Web-app of Web-API) is het valideren van het beveiligings token. Micro soft biedt op sommige platforms [middleware-bibliotheken](reference-v2-libraries.md#microsoft-supported-server-middleware-libraries).

### <a name="with-users-or-without-users"></a>Met gebruikers of zonder gebruikers

De meeste verificatie scenario's verkrijgen tokens namens een (aangemelde) **gebruiker**.

![scenario's met gebruikers](media/scenarios/scenarios-with-users.svg)

Er zijn echter ook scenario's (daemon-apps), waarbij toepassingen tokens kunnen verkrijgen namens zichzelf (zonder gebruiker).

![daemon-apps](media/scenarios/daemon-app.svg)

### <a name="single-page-applications-public-client-applications-and-confidential-client-applications"></a>Toepassingen met één pagina, open bare client toepassingen en vertrouwelijke client toepassingen

De beveiligings tokens kunnen worden verkregen uit een aantal toepassings typen. Toepassingen worden vaak onderverdeeld in drie categorieën:

- **Toepassingen met één pagina** Beveiligd-wachtwoord verificatie is een vorm van webtoepassingen waarbij tokens worden verkregen van de app die wordt uitgevoerd in de browser (geschreven in Java script of type script). Veel moderne apps hebben een front-end van de app met één pagina die voornamelijk is geschreven in Java script. Vaak wordt de app geschreven met behulp van een framework zoals hoek, reageren of Vue. MSAL. js is de enige micro soft-verificatie bibliotheek met ondersteuning voor toepassingen met één pagina.

- Bij **open bare client toepassingen** worden gebruikers altijd aangemeld. Deze apps zijn:
  - Desktop toepassingen die web-Api's aanroepen namens de aangemelde gebruiker.
  - Mobiele toepassingen.
  - Een derde categorie toepassingen die worden uitgevoerd op apparaten die geen browser hebben (zonder browser-apps, die worden uitgevoerd op iOT voor het exemplaar).

  Ze worden vertegenwoordigd door de MSAL-klasse met de naam [PublicClientApplication](msal-client-applications.md).

- **Vertrouwelijke client toepassingen**
  - Webtoepassingen die een web-API aanroepen
  - Web-Api's die een web-API aanroepen
  - Daemon-toepassingen (zelfs wanneer deze zijn geïmplementeerd als een-console service zoals een daemon in Linux of een Windows-service)
 
  Deze typen apps gebruiken de [ConfidentialClientApplication](msal-client-applications.md)

## <a name="application-scenarios"></a>Toepassingsscenario's

Het micro soft Identity platform-eind punt ondersteunt verificatie voor diverse app-architecturen: apps met één pagina, Web-apps, Web-Api's, mobiele en systeem eigen apps, daemons en apps aan de server zijde.  Toepassingen gebruiken de verschillende verificatie stromen voor het aanmelden van gebruikers en het ophalen van tokens om beveiligde Api's aan te roepen.

### <a name="single-page-application"></a>Toepassing met één pagina

Veel moderne webtoepassingen zijn gebouwd als toepassingen met één pagina op de client die zijn geschreven met behulp van Java script of een beveiligd-wachtwoord verificatie-Framework zoals een hoek, vue. js en reageren. js. Deze toepassingen worden uitgevoerd in een webbrowser en hebben verschillende verificatie kenmerken dan traditionele webtoepassingen aan de server zijde. Met het micro soft Identity-platform kunnen toepassingen met één pagina worden aangemeld en tokens worden opgehaald om toegang te krijgen tot back-end-services of Web-Api's.

![Toepassing met één pagina](media/scenarios/spa-app.svg)

Lees voor meer informatie [toepassingen met één pagina](scenario-spa-overview.md).

### <a name="web-application-signing-in-a-user"></a>Aanmelding van webtoepassingen: een gebruiker

![Web-app-ondertekening in gebruikers](media/scenarios/scenario-webapp-signs-in-users.svg)

Als u **een web-app wilt beveiligen** (aanmelden bij de gebruiker), gebruikt u:

- In de .NET World, ASP.NET of ASP.NET Core met de ASP.NET Open ID Connect middleware. Onder de schermen moet het beveiligen van een resource het beveiligings token valideren, dat wordt uitgevoerd door de [Identity model-extensies voor .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) -bibliotheek, niet MSAL-bibliotheken

- Als u in node. js ontwikkelt, gebruikt u Pass Port. js.

Lees voor meer informatie [Web-app die gebruikers aanmeldt](scenario-web-app-sign-user-overview.md).

### <a name="web-application-signing-in-a-user-and-calling-a-web-api-on-behalf-of-the-user"></a>Webtoepassing voor het aanmelden van een gebruiker en het aanroepen van een web-API namens de gebruiker

![Web-app roept Web-Api's aan](media/scenarios/web-app.svg)

Gebruik MSAL `ConfidentialClientApplication` om **de Web-API** namens de gebruiker aan te roepen vanuit de web-app. U gebruikt de autorisatie code stroom, waarbij u het verkregen token opslaat in de token cache. De controller schaft vervolgens, indien nodig, tokens af van de cache. MSAL vernieuwt het token als dat nodig is.

Lees web- [apps](scenario-web-app-call-api-overview.md)voor meer informatie.

### <a name="desktop-application-calling-a-web-api-on-behalf-of-the-signed-in-user"></a>Bureaublad toepassing die een web-API aanroept namens de aangemelde gebruiker

Als u een web-API wilt aanroepen vanuit een bureaublad toepassing die zich aanmeldt bij gebruikers, gebruikt u de methoden voor het PublicClientApplication's van interactieve tokens van MSAL. Met deze interactieve methoden kunt u de aanmelding van de gebruikers interface van het aanmeldings proces beheren. MSAL maakt gebruik van een webbrowser om deze interactie in te scha kelen.

![Bureaublad](media/scenarios/desktop-app.svg)

Voor Windows-gehoste toepassingen die worden uitgevoerd op computers die zijn gekoppeld aan een Windows-domein of AAD, is er een andere mogelijkheid. Deze toepassingen kunnen een token op de achtergrond verkrijgen met [geïntegreerde Windows-verificatie](https://aka.ms/msal-net-iwa).

Toepassingen die worden uitgevoerd op een apparaat zonder browser, kunnen nog steeds een API aanroepen namens een gebruiker. Als u zich wilt verifiëren, moet de gebruiker zich aanmelden op een ander apparaat met een webbrowser. Als u dit scenario wilt inschakelen, moet u de [code stroom](https://aka.ms/msal-net-device-code-flow) van het apparaat gebruiken

![Stroom voor apparaatcode](media/scenarios/device-code-flow-app.svg)

Ten slotte, hoewel dit niet wordt aangeraden, kunt u de [gebruikers naam en het wacht woord](https://aka.ms/msal-net-up) in open bare client toepassingen gebruiken. Deze stroom is nog steeds nodig in sommige scenario's (zoals DevOps), maar houd er wel rekening mee dat het gebruik ervan beperkingen oplegt aan uw toepassing. Apps die deze stroom gebruiken, kunnen bijvoorbeeld niet worden aangemeld bij een gebruiker die multi-factor Authentication moet uitvoeren (voorwaardelijke toegang). Het is niet mogelijk om uw toepassing te laten profiteren van een eenmalige aanmelding. Verificatie met gebruikers naam/wacht woord verloopt tegen de beginselen van moderne authenticatie en wordt alleen voor verouderde redenen gegeven.

Als u de token cache permanent wilt maken, moet u in bureaublad toepassingen [de token cache-serialisatie aanpassen](https://aka.ms/msal-net-token-cache-serialization). U kunt zelfs achterwaartse en forward compatibele token caches met eerdere generaties van verificatie bibliotheken (ADAL.NET 3. x en 4. x) inschakelen door de implementatie van [dubbele token-cache](https://aka.ms/msal-net-dual-cache-serialization)-serialisatie.

Lees voor meer informatie [bureau blad-app die web-api's aanroept](scenario-desktop-overview.md).

### <a name="mobile-application-calling-a-web-api-on-behalf-of-the-user-whos-signed-in-interactively"></a>Mobiele toepassing die een web-API aanroept namens de gebruiker die zich interactief aanmeldt

Net als bij desktop toepassingen maakt een mobiele toepassing gebruik van de MSAL PublicClientApplication's Interactive token Acquisition-methoden voor het verkrijgen van een token om een web-API aan te roepen.

![mobiele](media/scenarios/mobile-app.svg)

MSAL iOS en MSAL Android gebruiken standaard de webbrowser van het systeem. U kunt het echter ook gebruiken om de Inge sloten Webweergave uit te geven. Er zijn specifieke kenmerken afhankelijk van het mobiele platform: (UWP, iOS, Android).

In sommige scenario's, met betrekking tot voorwaardelijke toegang tot de apparaat-ID of een apparaat dat wordt geregistreerd, moet een [Broker](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/leveraging-brokers-on-Android-and-iOS) op een apparaat worden geïnstalleerd. Voor beelden van makelaars zijn micro soft-bedrijfs portal (op Android) Microsoft Authenticator (Android en iOS). MSAL is nu geschikt voor interactie met makelaars.

> [!NOTE]
> Uw mobiele app (met MSAL. iOS, MSAL. Op de Android-of MSAL.NET/Xamarin) kan beveiligings beleid voor apps worden toegepast (bijvoorbeeld voor komen dat de gebruiker beveiligde tekst kopieert). Dit wordt [beheerd door intune](https://docs.microsoft.com/intune/app-sdk) en herkend door intune als een beheerde app. De [intune SDK](https://docs.microsoft.com/intune/app-sdk-get-started) is gescheiden van MSAL-bibliotheken en wordt op basis van een eigen service aan Aad door gepraatd.

Lees voor meer informatie [de mobiele app die web-api's aanroept](scenario-mobile-overview.md).

### <a name="protected-web-api"></a>Beveiligde web-API

U kunt het micro soft Identity platform-eind punt gebruiken voor het beveiligen van webservices, zoals de REST Web API van uw app. Een beveiligde web-API wordt aangeroepen met een toegangs token om de gegevens te beveiligen en inkomende aanvragen te verifiëren. De aanroeper van een web-API voegt een toegangs token toe in de autorisatie-header van een HTTP-aanvraag. Als u uw ASP.NET of ASP.NET Core Web-API wilt beveiligen, moet u het toegangs token valideren. Hiervoor gebruikt u de ASP.NET JWT-middleware. Onder de motorkap wordt de validatie uitgevoerd door de [Identity model-extensies voor .net](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet/wiki) -bibliotheek, niet MSAL.net

Lees de [beveiligde web-API](scenario-protected-web-api-overview.md)voor meer informatie.

### <a name="web-api-calling-another-downstream-web-api-on-behalf-of-the-user-for-whom-it-was-called"></a>Web-API die een andere stroomafwaartse Web-API aanroept namens de gebruiker voor wie deze is aangeroepen

Als u bovendien wilt dat uw ASP.NET of ASP.NET Core beveiligde web-API om namens de gebruiker een andere web-API aan te roepen, moet de app een token voor de stroomafwaartse Web-API verkrijgen met behulp van de methode van de ConfidentialClientApplication die een token verkrijgt namens [een gebruiker](https://aka.ms/msal-net-on-behalf-of). Dit wordt ook wel service-to-service-aanroepen genoemd.
De Web-Api's die andere web-API aanroepen, moeten ook een aangepaste cache-serialisatie hebben

  ![Web-API](media/scenarios/web-api.svg)

Lees voor meer informatie [Web API die web-api's aanroept](scenario-web-api-call-api-overview.md).

### <a name="desktopservice-or-web-daemon-application-calling-web-api-without-a-user-in-its-own-name"></a>Desktop/service-of Web daemon-toepassing die Web-API aanroept zonder een gebruiker (met een eigen naam)

Apps die langlopende processen hebben of die werken zonder tussen komst van de gebruiker, hebben ook een manier nodig om toegang te krijgen tot beveiligde web-Api's. Deze apps kunnen tokens verifiëren en ophalen met behulp van de identiteit van de app, in plaats van de gedelegeerde identiteit van een gebruiker. Ze bewijzen hun identiteit met behulp van een client geheim of certificaat.
U kunt dergelijke apps (daemon-app) schrijven voor het verkrijgen van een token voor de app op de hoogste wijze met behulp van de MSAL ConfidentialClientApplication's- [client referenties](https://aka.ms/msal-net-client-credentials) . Dit veronderstelt dat de app eerder een geheim (toepassings wachtwoord of certificaat of client bevestiging) heeft geregistreerd bij Azure AD, dat vervolgens deelt met deze aanroep.

![Daemon-apps](media/scenarios/daemon-app.svg)

Lees voor meer informatie [daemon-toepassing die web-api's aanroept](scenario-daemon-overview.md).

## <a name="scenarios-and-supported-authentication-flows"></a>Scenario's en ondersteunde verificatie stromen

Scenario's met betrekking tot het verkrijgen van tokens zijn ook toegewezen aan OAuth 2,0-verificatie stromen die worden beschreven in de [micro soft Identity platform-protocollen](active-directory-v2-protocols.md) .

<table>
 <thead>
  <tr><th>Scenario</th> <th>Gedetailleerd scenario door lopen</th> <th>OAuth 2,0-stroom/toekenning</th> <th>Doelgroep</th></tr>
 </thead>
 <tbody>
  <tr>
   <td><a href="scenario-spa-overview.md"><img alt="Single Page App" src="media/scenarios/spa-app.svg"></a></td>
   <td><a href="scenario-spa-overview.md">App van één pagina</a></td>
   <td><a href="v2-oauth2-implicit-grant-flow.md">Wenst</a></td>
   <td>Werk-of school accounts en persoonlijke accounts, B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-sign-user-overview.md"><img alt="Web App that signs in users" src="media/scenarios/scenario-webapp-signs-in-users.svg"></a></td>
   <td><a href="scenario-web-app-sign-user-overview.md">Een web-app die gebruikers aanmeldt</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Autorisatie code</a></td>
   <td>Werk-of school accounts en persoonlijke accounts, B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-web-app-call-api-overview.md"><img alt="Web App that signs in users" src="media/scenarios/web-app.svg"></a></td>
   <td><a href="scenario-web-app-call-api-overview.md">Web-app die web-Api's aanroept</a></td>
   <td><a href="v2-oauth2-auth-code-flow.md">Autorisatie code</a></td>
   <td>Werk-of school accounts en persoonlijke accounts, B2C</td>
 </tr>

  <tr>
   <td rowspan="3"><a href="scenario-desktop-overview.md"><img alt=Desktop app that calls web APIs" src="media/scenarios/desktop-app.svg"></a></td>
   <td rowspan="4"><a href="scenario-desktop-overview.md">Desktop-app die web-API's aanroept</a></td>
   <td>Interactief (<a href="v2-oauth2-auth-code-flow.md">verificatie code</a> met PKCE)</td>
   <td>Werk-of school accounts en persoonlijke accounts, B2C</td>
 </tr>

  <tr>
   <td>Geïntegreerde Windows-verificatie</td>
   <td>Werk-of school accounts</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Wacht woord van resource-eigenaar</a></td>
   <td>Werk-of school accounts, B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-desktop-acquire-token.md#command-line-tool-without-web-browser"><img alt="Browserless application" src="media/scenarios/device-code-flow-app.svg"></a></td>
   <td><a href="v2-oauth2-device-code.md">Apparaatcode</a></td>
   <td>Werk-of school accounts *</td>
 </tr>

 <tr>
   <td rowspan="2"><a href="scenario-mobile-overview.md"><img alt="Mobile app that calls web APIs" src="media/scenarios/mobile-app.svg"></a></td>
   <td rowspan="2"><a href="scenario-mobile-overview.md">Mobiele app die web-Api's aanroept</a></td>
   <td>Interactief (<a href="v2-oauth2-auth-code-flow.md">verificatie code</a> met PKCE)</td>
   <td>Werk-of school accounts en persoonlijke accounts, B2C</td>
 </tr>

  <tr>
   <td><a href="v2-oauth-ropc.md">Wacht woord van resource-eigenaar</a></td>
   <td>Werk-of school accounts, B2C</td>
 </tr>

  <tr>
   <td><a href="scenario-daemon-overview.md"><img alt="Daemon app that calls Web APIs" src="media/scenarios/daemon-app.svg"></a></td>
   <td><a href=scenario-daemon-overview.md">Daemon-app die web-Api's aanroept</a></td>
   <td><a href="v2-oauth2-client-creds-grant-flow.md">Clientreferenties</a></td>
   <td>Alleen app-machtigingen (geen gebruiker) voor AAD-organisaties</td>
 </tr>

  <tr>
   <td><a href=scenario-web-api-call-api-overview.md"><img alt="Web API that calls web APIs" src="media/scenarios/web-api.svg"></a></td>
   <td><a href=scenario-web-api-call-api-overview.md">Web-API die web-Api's aanroept</a></td>
   <td><a href="v2-oauth2-on-behalf-of-flow.md">Namens</a></td>
   <td>Werk-of school accounts en persoonlijke accounts</td>
 </tr>

 </tbody>
</table>

## <a name="scenarios-and-supported-platforms-and-languages"></a>Scenario's en ondersteunde platforms en talen

Niet elk toepassings type is beschikbaar op elk platform. U kunt ook verschillende talen gebruiken om uw toepassingen te bouwen. Micro soft-verificatie bibliotheken ondersteunen een aantal **platformen** (Java script, .NET Framework, .net core, Windows 10/UWP, Xamarin. IOS, Xamarin. Android, systeem eigen iOS, Mac OS, systeem eigen Android, Java, python). In de onderstaande tabel wordt in Windows elke keer dat er .NET core wordt vermeld, .NET Framework ook mogelijk (dit wordt wegge laten om te voor komen dat de tabel onoverzichtelijk wordt)

|Scenario  | Windows | Linux | Mac | iOS | Android
|--|--|--|--|--|--|--|
| [App van één pagina](scenario-spa-overview.md) <br/>[![App met één pagina](media/scenarios/spa-app.svg)](scenario-spa-overview.md) | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png) MSAL.js | ![MSAL.js](media/sample-v2-code/small_logo_js.png)<br/>MSAL.js
| [Web-app die gebruikers aanmeldt](scenario-web-app-sign-user-overview.md) <br/>[![Een web-app die gebruikers aanmeldt](media/scenarios/scenario-webapp-signs-in-users.svg)](scenario-web-app-sign-user-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core
| [Web-app die web-Api's aanroept](scenario-web-app-call-api-overview.md) <br/> <br/>[![Web-app die web-Api's aanroept](media/scenarios/web-app.svg)](scenario-web-app-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL java](media/sample-v2-code/small_logo_java.png) <br/>msal4j<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>Kolf + MSAL python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>Kolf + MSAL python| ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/> ![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>Kolf + MSAL python
| [Desktop-app die web-API's aanroept](scenario-desktop-overview.md) <br/> <br/>![ [ Bureaublad-appdieweb-api's![aanroept](media/scenarios/desktop-app.svg)](scenario-desktop-overview.md) apparaat code stroom](media/scenarios/device-code-flow-app.svg) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/> ![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL java](media/sample-v2-code/small_logo_java.png)msal4j<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python <br/> MSAL. objc |
| [Mobiele app die web-Api's aanroept](scenario-mobile-overview.md) <br/> [![Mobiele app die web-Api's aanroept](media/scenarios/mobile-app.svg)](scenario-mobile-overview.md) | ![UWP](media/sample-v2-code/small_logo_windows.png) MSAL.NET ![Xamarin](media/sample-v2-code/small_logo_xamarin.png) MSAL.NET | | | ![iOS/objectief C of SWIFT](media/sample-v2-code/small_logo_iOS.png) MSAL. objc | ![Android](media/sample-v2-code/small_logo_Android.png) MSAL. Android
| [Daemon-app](scenario-daemon-overview.md) <br/> [![Daemon-app](media/scenarios/daemon-app.svg)](scenario-daemon-overview.md) | ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png) MSAL.NET ![MSAL java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)MSAL.NET ![MSAL java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python
| [Web-API die web-Api's aanroept](scenario-web-api-call-api-overview.md) <br/><br/> [![Web-API die web-Api's aanroept](media/scenarios/web-api.svg)](scenario-web-api-call-api-overview.md) | ![ASP.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python| ![.NET Core](media/sample-v2-code/small_logo_NETcore.png)<br/>ASP.NET Core + MSAL.NET ![MSAL java](media/sample-v2-code/small_logo_java.png)<br/>msal4j<br/>![MSAL python](media/sample-v2-code/small_logo_python.png)<br/>MSAL python

Zie ook [door micro soft ondersteunde bibliotheken per OS/taal](reference-v2-libraries.md#microsoft-supported-libraries-by-os--language)

## <a name="next-steps"></a>Volgende stappen
Meer informatie over de [basis principes van verificatie](authentication-scenarios.md) en [toegangs tokens](access-tokens.md).
