---
title: Verificatiebibliotheken van microsoft-identiteitsplatform
description: Compatibele clientbibliotheken en mediamiddlewarebibliotheken voor server, samen met gerelateerde bibliotheek-, bron- en voorbeeldkoppelingen, voor het eindpunt van het Microsoft-identiteitsplatform.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/25/2019
ms.author: negoe
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.openlocfilehash: 22e2ee0e3596bc996482a090af7509db4c415e61
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80883488"
---
# <a name="microsoft-identity-platform-authentication-libraries"></a>Verificatiebibliotheken van microsoft-identiteitsplatform

Het [eindpunt van het Microsoft-identiteitsplatform](active-directory-v2-compare.md) ondersteunt de industriestandaard OAuth 2.0- en OpenID Connect 1.0-protocollen. De Microsoft Authentication Library (MSAL) is ontworpen om te werken met het eindpunt van het Microsoft-identiteitsplatform. U ook open-sourcebibliotheken gebruiken die OAuth 2.0 en OpenID Connect 1.0 ondersteunen.

We raden u aan bibliotheken te gebruiken die zijn geschreven door experts uit het protocoldomein die een SDL-methodologie (Security Development Lifecycle) volgen. Dergelijke methodologieën omvatten [degene die Microsoft volgt][Microsoft-SDL]. Als u code voor de protocollen gebruikt, moet u een methodologie volgen zoals Microsoft SDL. Besteed veel aandacht aan de beveiligingsoverwegingen in de standaardspecificaties voor elk protocol.

> [!NOTE]
> Bent u op zoek naar de Azure Active Directory Authentication Library (ADAL)? Bekijk de [ADAL bibliotheek gids](../azuread-dev/active-directory-authentication-libraries.md).

## <a name="types-of-libraries"></a>Typen bibliotheken

Het eindpunt van het Microsoft-identiteitsplatform werkt met twee typen bibliotheken:

* **Clientbibliotheken**: Native clients en servers gebruiken clientbibliotheken om toegangstokens te verkrijgen voor het aanroepen van een bron zoals Microsoft Graph.
* **Server middleware-bibliotheken**: Web-apps gebruiken mediamiddleware-bibliotheken voor gebruikersaanmelding. Web API's gebruiken server middleware-bibliotheken om tokens te valideren die worden verzonden door native clients of door andere servers.

## <a name="library-support"></a>Ondersteuning voor bibliotheek

Bibliotheken zijn er in twee ondersteuningscategorieën:

* **Microsoft-ondersteund:** Microsoft biedt oplossingen voor deze bibliotheken en heeft SDL due diligence gedaan op deze bibliotheken.
* **Compatibel**: Microsoft heeft deze bibliotheken getest in basisscenario's en heeft bevestigd dat ze werken met het eindpunt van het Microsoft-identiteitsplatform. Microsoft biedt geen oplossingen voor deze bibliotheken en heeft geen beoordeling van deze bibliotheken gedaan. Problemen en functieaanvragen moeten worden gericht op het open-sourceproject van de bibliotheek.

Zie de volgende secties voor een lijst met bibliotheken die werken met het eindpunt van het Microsoft-identiteitsplatform.

## <a name="microsoft-supported-client-libraries"></a>Door Microsoft ondersteunde clientbibliotheken

Gebruik clientverificatiebibliotheken om een token te verkrijgen voor het aanroepen van een beveiligde web-API.

| Platform | Bibliotheek | Download | Broncode | Voorbeeld | Naslaginformatie | Conceptueel document | Roadmap |
| --- | --- | --- | --- | --- | --- | --- | --- |
| ![Javascript](media/sample-v2-code/logo_js.png) | MSAL.js  | [NPM](https://www.npmjs.com/package/msal) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/README.md) |  [App met één pagina](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-dotnet-webapi-v2) | [Naslaginformatie](https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-core/) | [Conceptuele documenten](msal-overview.md)| [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
![Angular](media/sample-v2-code/logo_angular.png) | MSAL Hoekig | [NPM](https://www.npmjs.com/package/@azure/msal-angular) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular/README.md) | [Hoekige SPA](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) | [Naslaginformatie](https://azuread.github.io/microsoft-authentication-library-for-js/ref/msal-angular/) | [Conceptuele documenten](msal-overview.md) | [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-js/wiki#roadmap)
| ![.NET Framework](media/sample-v2-code/logo_NET.png) ![UWP](media/sample-v2-code/logo_windows.png) ![Xamarin](media/sample-v2-code/logo_xamarin.png) | MSAL.NET  |[NuGet (NuGet)](https://www.nuget.org/packages/Microsoft.Identity.Client) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) | [Desktop-app](guidedsetups/active-directory-mobileanddesktopapp-windowsdesktop-intro.md) | [MSAL.NET](https://docs.microsoft.com/dotnet/api/microsoft.identity.client?view=azure-dotnet-preview) |[Conceptuele documenten](msal-overview.md) | [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki#roadmap)
| ![Python](media/sample-v2-code/logo_python.png) | MSAL Python | [PyPI](https://pypi.org/project/msal) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-python) | [Voorbeelden](https://github.com/AzureAD/microsoft-authentication-library-for-python/tree/dev/sample) | [ReadTheDocs](https://msal-python.rtfd.io/) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki) | [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki/Roadmap)
| ![Java](media/sample-v2-code/logo_java.png) | MSAL Java | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/msal4j) | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-java) | [Voorbeelden](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples) | [Naslaginformatie](https://javadoc.io/doc/com.microsoft.azure/msal4j/latest/index.html) | [Wiki](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki) | [Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-java/wiki)
| iOS & macOS | MSAL iOS en macOS | [GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-objc) | [iOS-app](https://github.com/Azure-Samples/ms-identity-mobile-apple-swift-objc), [macOS-app](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) | [Naslaginformatie](https://azuread.github.io/microsoft-authentication-library-for-objc/index.html)  | [Conceptuele documenten](msal-overview.md) | |
|![Android / Java](media/sample-v2-code/logo_Android.png) | MSAL Android | [Centrale opslagplaats](https://repo1.maven.org/maven2/com/microsoft/identity/client/msal/) |[GitHub](https://github.com/AzureAD/microsoft-authentication-library-for-android) | [Android-app](quickstart-v2-android.md) | [JavaDocs (JavaDocs)](https://javadoc.io/doc/com.microsoft.identity.client/msal) | [Conceptuele documenten](msal-overview.md) |[Roadmap](https://github.com/AzureAD/microsoft-authentication-library-for-android/wiki/Roadmap)

## <a name="microsoft-supported-server-middleware-libraries"></a>Door Microsoft ondersteunde middleware-bibliotheken voor servers

Gebruik middleware-bibliotheken om webtoepassingen en web-API's te beschermen. Web-apps of web-API's die zijn geschreven met ASP.NET of ASP.NET Core gebruiken de middleware-bibliotheken.

| Platform | Bibliotheek | Download | Broncode | Voorbeeld | Naslaginformatie
| --- | --- | --- | --- | --- | --- |
| ![.NET](media/sample-v2-code/logo_NET.png) ![.NET Core](media/sample-v2-code/logo_NETcore.png) | ASP.NET Beveiliging |[NuGet (NuGet)](https://www.nuget.org/packages/Microsoft.AspNet.Mvc/) |[GitHub](https://github.com/aspnet/AspNetCore) |[MVC-app](quickstart-v2-aspnet-webapp.md) |[ASP.NET API-verwijzing](https://docs.microsoft.com/dotnet/api/?view=aspnetcore-2.0) |
| ![.NET](media/sample-v2-code/logo_NET.png)| IdentityModel-extensies voor .NET| |[GitHub](https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet) | [MVC-app](quickstart-v2-aspnet-webapp.md) |[Naslaginformatie](https://docs.microsoft.com/dotnet/api/overview/azure/activedirectory/client?view=azure-dotnet) |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | Azure AD-paspoort |[NPM](https://www.npmjs.com/package/passport-azure-ad) |[GitHub](https://github.com/AzureAD/passport-azure-ad) | [Web-app](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) | |

## <a name="microsoft-supported-libraries-by-os--language"></a>Door Microsoft ondersteunde bibliotheken op os / taal

Op het gebied van ondersteunde besturingssystemen versus talen is de toewijzing de volgende:

|             | Windows    | Linux      | macOS      | iOS | Android    |
|-------------|------------|------------|------------|------------|------------|
| ![Javascript](media/sample-v2-code/logo_js.png)  |  MSAL.js | MSAL.js | MSAL.js | MSAL.js |  MSAL.js |
| <img alt="C#" src="../../cognitive-services/speech-service/media/index/logo_csharp.svg" width="64px" height="64px" /> | ASP.NET, ASP.NET Core, MSAL.Net (.NET FW, Core, UWP)| ASP.NET Core, MSAL.Net (.NET Core) | ASP.NET Core, MSAL.Net (macOS)       | MSAL.Net (Xamarin.iOS) | MSAL.Net (Xamarin.Android)|
| Swift <br> Objective-C |            |            | [MSAL voor iOS en macOS](msal-overview.md) | [MSAL voor iOS en macOS](msal-overview.md) |            |
| ![Java](media/sample-v2-code/logo_java.png) Java | msal4j msal4j | msal4j msal4j | msal4j msal4j | | MSAL Android |
| ![Python](media/sample-v2-code/logo_python.png) Python | MSAL Python | MSAL Python | MSAL Python |
| ![Knooppunt.Js](media/sample-v2-code/logo_nodejs.png) Node.JS | Passport.node | Passport.node | Passport.node |

Zie ook [Scenario's door ondersteunde platforms en talen](authentication-flows-app-scenarios.md#scenarios-and-supported-platforms-and-languages)

## <a name="compatible-client-libraries"></a>Compatibele clientbibliotheken

| Platform | Bibliotheeknaam | Geteste versie | Broncode | Voorbeeld |
|:---:|:---:|:---:|:---:|:---:|
|![Javascript](media/sample-v2-code/logo_js.png)|[Hello.js](https://adodson.com/hello.js/) | Versie 1.13.5 |[Hello.js](https://github.com/MrSwitch/hello.js) |[Spa](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![Java](media/sample-v2-code/logo_java.png) | [Schrijf Java](https://github.com/scribejava/scribejava) | [Versie 3.2.0](https://github.com/scribejava/scribejava/releases/tag/scribejava-3.2.0) | [ScribeJava](https://github.com/scribejava/scribejava/) | |
| ![Java](media/sample-v2-code/logo_java.png) | [Gluu OpenID Connect-bibliotheek](https://github.com/GluuFederation/oxAuth) | [Versie 3.0.2](https://github.com/GluuFederation/oxAuth/releases/tag/3.0.2) | [Gluu OpenID Connect-bibliotheek](https://github.com/GluuFederation/oxAuth) | |
| ![Python](media/sample-v2-code/logo_python.png) | [Aanvragen-OAuthlib](https://github.com/requests/requests-oauthlib) | [Versie 1.2.0](https://github.com/requests/requests-oauthlib/releases/tag/v1.2.0) | [Aanvragen-OAuthlib](https://github.com/requests/requests-oauthlib) | |
| ![Node.js](media/sample-v2-code/logo_nodejs.png) | [openid-client](https://github.com/panva/node-openid-client) | [Versie 2.4.5](https://github.com/panva/node-openid-client/releases/tag/v2.4.5) | [openid-client](https://github.com/panva/node-openid-client) | |
| ![PHP](media/sample-v2-code/logo_php.png) | [De PHP League oauth2-client](https://github.com/thephpleague/oauth2-client) | [Versie 1.4.2](https://github.com/thephpleague/oauth2-client/releases/tag/1.4.2) | [oauth2-client](https://github.com/thephpleague/oauth2-client/) | |
| ![Ruby](media/sample-v2-code/logo_ruby.png) |[OmniAuth (OmniAuth)](https://github.com/omniauth/omniauth/wiki) |omniauth: 1.3.1<br />omniauth-oauth2: 1.4.0 |[OmniAuth (OmniAuth)](https://github.com/omniauth/omniauth)<br />[OmniAuth OAuth2](https://github.com/intridea/omniauth-oauth2) |  |
| iOS, macOS, & Android  | [Reageren Native App Auth](https://github.com/FormidableLabs/react-native-app-auth) | [Versie 4.2.0](https://github.com/FormidableLabs/react-native-app-auth/releases/tag/v4.2.0) | [Reageren Native App Auth](https://github.com/FormidableLabs/react-native-app-auth) | |

Voor elke bibliotheek die voldoet aan standaarden, u het eindpunt van het Microsoft-identiteitsplatform gebruiken. Het is belangrijk om te weten waar te gaan voor ondersteuning:

* Neem contact op met de bibliotheekeigenaar voor problemen en nieuwe functieaanvragen in bibliotheekcode.
* Neem contact op met Microsoft voor problemen en nieuwe functieaanvragen in de implementatie van het serviceprotocol.
* [Bestand een functie aanvraag](https://feedback.azure.com/forums/169401-azure-active-directory) voor extra functies die u wilt zien in het protocol.
* [Maak een ondersteuningsverzoek](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) als u een probleem vindt waarbij het eindpunt van het Microsoft-identiteitsplatform niet voldoet aan OAuth 2.0 of OpenID Connect 1.0.

## <a name="related-content"></a>Gerelateerde inhoud

Zie het overzicht van het [Microsoft-identiteitsplatform][AAD-App-Model-V2-Overview]voor meer informatie over het eindpunt van het Microsoft-identiteitsplatform.

<!--Image references-->

<!--Reference style links -->
[AAD-App-Model-V2-Overview]: v2-overview.md
[ClientLib-NET-Lib]: https://www.nuget.org/packages/Microsoft.Identity.Client
[ClientLib-NET-Repo]: https://github.com/AzureAD/microsoft-authentication-library-for-dotnet
[ClientLib-NET-Sample]: active-directory-v2-devquickstarts-wpf.md
[ClientLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ClientLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad
[ClientLib-Node-Sample]:/
[ClientLib-Iosmac-Lib]:/
[ClientLib-Iosmac-Repo]:/
[ClientLib-Iosmac-Sample]:/
[ClientLib-Android-Lib]:/
[ClientLib-Android-Repo]:/
[ClientLib-Android-Sample]:/
[ClientLib-Js-Lib]:/
[ClientLib-Js-Repo]:/
[ClientLib-Js-Sample]:/

[Microsoft-SDL]: https://www.microsoft.com/sdl/default.aspx
[ServerLib-Net4-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OpenIdConnect/
[ServerLib-Net4-Owin-Oidc-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oidc-Sample]: active-directory-v2-devquickstarts-dotnet-web.md
[ServerLib-Net4-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.Owin.Security.OAuth/
[ServerLib-Net4-Owin-Oauth-Repo]: https://katanaproject.codeplex.com/
[ServerLib-Net4-Owin-Oauth-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-dotnet-api/
[ServerLib-Net-Jwt-Lib]: https://www.nuget.org/packages/System.IdentityModel.Tokens.Jwt
[ServerLib-Net-Jwt-Repo]: https://github.com/AzureAD/azure-activedirectory-identitymodel-extensions-for-dotnet
[ServerLib-Net-Jwt-Sample]:/
[ServerLib-NetCore-Owin-Oidc-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OpenIdConnect/
[ServerLib-NetCore-Owin-Oidc-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oidc-Sample]: https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect-aspnetcore-v2
[ServerLib-NetCore-Owin-Oauth-Lib]: https://www.nuget.org/packages/Microsoft.AspNetCore.Authentication.OAuth/
[ServerLib-NetCore-Owin-Oauth-Repo]: https://github.com/aspnet/Security
[ServerLib-NetCore-Owin-Oauth-Sample]:/
[ServerLib-Node-Lib]: https://www.npmjs.com/package/passport-azure-ad
[ServerLib-Node-Repo]: https://github.com/AzureAD/passport-azure-ad/
[ServerLib-Node-Sample]: https://azure.microsoft.com/documentation/articles/active-directory-v2-devquickstarts-node-web/
