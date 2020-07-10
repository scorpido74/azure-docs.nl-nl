---
title: Code voorbeelden voor het micro soft Identity platform
description: Biedt een index van beschik bare code voorbeelden van het micro soft Identity platform (v 2.0-eind punt), geordend op scenario.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: sample
ms.workload: identity
ms.date: 06/01/2020
ms.author: marsma
ms.reviewer: jmprieur
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 42b7cf88942fb9b54b1292ece18cf14eb0d21958
ms.sourcegitcommit: 1e6c13dc1917f85983772812a3c62c265150d1e7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/09/2020
ms.locfileid: "86165959"
---
# <a name="microsoft-identity-platform-code-samples-v20-endpoint"></a>Micro soft Identity platform code samples (v 2.0-eind punt)

U kunt micro soft Identity platform gebruiken voor het volgende:

- Voeg verificatie en autorisatie toe aan uw webtoepassingen en Web-Api's.
- Een toegangs token vereisen om toegang te krijgen tot een beveiligde web-API.

In dit artikel wordt kort beschreven en vindt u koppelingen naar voor beelden voor het micro soft Identity platform-eind punt. In deze voor beelden ziet u hoe deze worden uitgevoerd, en kunt u ook code fragmenten opgeven die u in uw toepassingen gebruikt. Op de voorbeeld pagina code vindt u gedetailleerde Leesmij-onderwerpen die u helpen bij vereisten, installatie en installatie. Opmerkingen in de code helpen u de kritieke secties te begrijpen.

> [!NOTE]
> Als u geïnteresseerd bent in v 1.0-voor beelden, raadpleegt u [Azure ad-code voorbeelden (v 1.0-eind punt)](../azuread-dev/sample-v1-code.md).

Zie [app-typen voor het micro soft Identity platform-eind punt](v2-app-types.md)voor meer informatie over het basis scenario voor elk type sample.

U kunt ook bijdragen aan de voor beelden op GitHub. Zie Microsoft Azure Active Directory-voor [beelden en-documentatie](https://github.com/Azure-Samples?page=3&query=active-directory)voor meer informatie.

## <a name="single-page-applications"></a>Toepassingen met één pagina

In deze voor beelden ziet u hoe u een toepassing met één pagina schrijft die is beveiligd met micro soft Identity platform. Deze steek proeven gebruiken een van de MSAL.js.

| Platform | Beschrijving | Koppeling |
| -------- | --------------------- | -------- |
| ![Deze afbeelding toont het Java script ](media/sample-v2-code/logo_js.png) [-logo java script (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA-aanroepen Microsoft Graph |[Java script-graphapi-v2](https://github.com/Azure-Samples/active-directory-javascript-graphapi-v2) |
| ![Deze afbeelding toont het Java script ](media/sample-v2-code/logo_js.png) [-logo java script (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | SPA-aanroepen Microsoft Graph verificatie code stroom met PKCE |[Java script-v2](https://github.com/Azure-Samples/ms-identity-javascript-v2) |
| ![Deze afbeelding toont het Java script ](media/sample-v2-code/logo_js.png) [-logo java script (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core) | Beveiligd-wachtwoord verificatie aanroepen B2C |[B2C-java script-msal-singlepageapp](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) |
| ![In deze afbeelding wordt het hoek logo ](media/sample-v2-code/logo_angular.png) [Java script (MSAL hoek)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) weer gegeven| SPA-aanroepen Microsoft Graph  | [Active-Directory-java script-singlepageapp-hoek](https://github.com/Azure-Samples/active-directory-javascript-singlepageapp-angular) |
| ![In deze afbeelding wordt het hoek logo ](media/sample-v2-code/logo_angular.png) [Java script (MSAL hoek)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) weer gegeven| Beveiligd-wachtwoord verificatie aanroepen aangepaste web-API | [MS-Identity-java script-hoek-Spa-aspnetcore-webapi](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnetcore-webapi) |
| ![In deze afbeelding wordt het hoek logo ](media/sample-v2-code/logo_angular.png) [Java script (MSAL hoek)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) weer gegeven | Beveiligd-wachtwoord verificatie aanroepen B2C |[Active-Directory-B2C-java script-hoek-Spa](https://github.com/Azure-Samples/active-directory-b2c-javascript-angular-spa) |
| ![Deze afbeelding toont het reactie logo ](media/sample-v2-code/logo_react.png) [Java script (msal.js)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-core)| Beveiligd-wachtwoord verificatie roept een aangepaste web-API op die op zijn beurt aanroept Microsoft Graph  | [MS-Identity-java script-reageren-Spa-dotnetcore-webapi-OBO](https://github.com/Azure-Samples/ms-identity-javascript-react-spa-dotnetcore-webapi-obo) |
| ![In deze afbeelding wordt het hoek logo ](media/sample-v2-code/logo_angular.png) [Java script (MSAL hoek)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) weer gegeven | Multi tenant beveiligd-wachtwoord verificatie aanroepen multi tenant aangepaste web-API |[MS-Identity-java script-hoek-Spa-ASPNET-webapi-multi tenant](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-aspnet-webapi-multitenant) |
| ![In deze afbeelding wordt het hoek logo ](media/sample-v2-code/logo_angular.png) [Java script (MSAL hoek)](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angular) weer gegeven | Beveiligd-wachtwoord verificatie roept een aangepaste web-API aan met app-rollen en beveiligings groepen |[MS-Identity-java script-hoek-Spa-dotnetcore-webapi-roles-groups](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups) |

## <a name="web-applications"></a>Webtoepassingen

De volgende voor beelden illustreren webtoepassingen die aanmelden bij gebruikers. Sommige voor beelden demonstreren ook de toepassing die Microsoft Graph aanroept of uw eigen web-API met de identiteit van de gebruiker.

| Platform | Alleen tekenen in gebruikers | Ondertekent gebruikers en aanroepen Microsoft Graph |
| -------- | ------------------- | --------------------------------- |
| ![Deze afbeelding toont het ASP.NET Core logo](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | [Zelf studie voor gebruikers van de webASP.NET Core WebApp-aanmelding](https://aka.ms/aspnetcore-webapp-sign-in) | Hetzelfde voor beeld in de [ASP.net core web-app aanroepen Microsoft Graph](https://aka.ms/aspnetcore-webapp-call-msgraph) fase |
| ![In deze afbeelding wordt het ASP.NET-logo weer gegeven](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET | [ASP.NET Quick Start](https://github.com/AzureAdQuickstarts/AppModelv2-WebApp-OpenIDConnect-DotNet) </p> [DotNet-webapp-openidconnect-v2](https://github.com/azure-samples/active-directory-dotnet-webapp-openidconnect-v2)  |  [DotNet-beheerder-beperkte scopes-v2](https://github.com/azure-samples/active-directory-dotnet-admin-restricted-scopes-v2) </p> |[MSGraph-training-aspnetmvcapp](https://github.com/microsoftgraph/msgraph-training-aspnetmvcapp)
| ![In deze afbeelding wordt het Java-logo weer gegeven](media/sample-v2-code/logo_java.png)  |                   | [MS-Identity-Java-webapp](https://github.com/Azure-Samples/ms-identity-java-webapp) |
| ![Deze afbeelding toont het python-logo](media/sample-v2-code/logo_python.png)  |                   | [MS-Identity-python-webapp](https://github.com/Azure-Samples/ms-identity-python-webapp) |
| ![Deze afbeelding toont het ruby-logo](media/sample-v2-code/logo_ruby.png) |                   | [MSGraph-training-rubyrailsapp](https://github.com/microsoftgraph/msgraph-training-rubyrailsapp) |

## <a name="desktop-and-mobile-public-client-apps"></a>Desktop-en mobiele open bare client-apps

De volgende voor beelden tonen open bare client toepassingen (desktop-of mobiele toepassingen) die toegang hebben tot de Microsoft Graph-API of uw eigen web-API in de naam van een gebruiker. Naast het *bureau blad (console) met WAM-voor* beeld gebruiken al deze client toepassingen micro soft Authentication Library (MSAL).

| Client toepassing | Platform | Stroom/verlenen | Aanroepen Microsoft Graph | Hiermee wordt een ASP.NET Core Web-API aangeroepen |
| ------------------ | -------- |  ----------| ---------- | ------------------------- |
| Bureau blad (WPF)      | ![In deze afbeelding wordt het .NET/C#-logo weer gegeven](media/sample-v2-code/logo_NET.png) | [Autorisatie code](msal-authentication-flows.md#authorization-code)| [DotNet-Desktop-MSGraph-v2](https://github.com/azure-samples/active-directory-dotnet-desktop-msgraph-v2) | [DotNet-systeem eigen-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi) |
| Bureau blad (console)   | ![In deze afbeelding wordt het .NET/C#-logo (Desktop) weer gegeven](media/sample-v2-code/logo_NET.png) | [Geïntegreerde Windows-verificatie](msal-authentication-flows.md#integrated-windows-authentication) | [DotNet-IWA-v2](https://github.com/azure-samples/active-directory-dotnet-iwa-v2) |  |
| Bureau blad (console)   | ![In deze afbeelding wordt het Java-logo weer gegeven](media/sample-v2-code/logo_java.png) | [Geïntegreerde Windows-verificatie](msal-authentication-flows.md#integrated-windows-authentication) |[MS-Identity-Java-Desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Bureau blad (console)   | ![In deze afbeelding wordt het .NET/C#-logo (Desktop) weer gegeven](media/sample-v2-code/logo_NETcore.png) | [Gebruikers naam/wacht woord](msal-authentication-flows.md#usernamepassword) |[dotnetcore-up-v2](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2) |  |
| Bureau blad (console) met WAM  | ![In deze afbeelding wordt het .NET/C#-logo (Desktop) weer gegeven](media/sample-v2-code/logo_NETcore.png) | Interactief met [web account manager](/windows/uwp/security/web-account-manager) (WAM) |[DotNet-systeem eigen-UWP-WAM](https://github.com/azure-samples/active-directory-dotnet-native-uwp-wam) |  |
| Bureau blad (console)   | ![In deze afbeelding wordt het Java-logo weer gegeven](media/sample-v2-code/logo_java.png) | [Gebruikers naam/wacht woord](msal-authentication-flows.md#usernamepassword) |[MS-Identity-Java-Desktop](https://github.com/Azure-Samples/ms-identity-java-desktop/) |  |
| Bureau blad (console)   | ![Deze afbeelding toont het python-logo](media/sample-v2-code/logo_python.png) | [Gebruikers naam/wacht woord](msal-authentication-flows.md#usernamepassword) |[MS-Identity-python-desktop](https://github.com/Azure-Samples/ms-identity-python-desktop) |  |
| Mobiel (Android, iOS, UWP)   | ![In deze afbeelding wordt het .NET/C#-logo (Xamarin) weer gegeven](media/sample-v2-code/logo_xamarin.png) | [Autorisatie code](msal-authentication-flows.md#authorization-code) |[xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) |  |
| Mobiel (iOS)       | ![Deze afbeelding toont iOS/objectief-C of SWIFT](media/sample-v2-code/logo_iOS.png) | [Autorisatie code](msal-authentication-flows.md#authorization-code) |[IOS-Swift-objc-native-v2](https://github.com/azure-samples/active-directory-ios-swift-native-v2) </p> [IOS-systeem eigen-nxoauth2-v2](https://github.com/azure-samples/active-directory-ios-native-nxoauth2-v2) |  |
| Bureau blad (macOS)       | macOS | [Autorisatie code](msal-authentication-flows.md#authorization-code) |[macOS-Swift-objc-native-v2](https://github.com/Azure-Samples/ms-identity-macOS-swift-objc) |  |
| Mobiel (Android-Java)   | ![Deze afbeelding toont het Android-logo](media/sample-v2-code/logo_Android.png) | [Autorisatie code](msal-authentication-flows.md#authorization-code) |  [Android-java](https://github.com/Azure-Samples/ms-identity-android-java) |  |
| Mobiel (Android-Kotlin)   | ![Deze afbeelding toont het Android-logo](media/sample-v2-code/logo_Android.png) | [Autorisatie code](msal-authentication-flows.md#authorization-code) |  [Android-Kotlin](https://github.com/Azure-Samples/ms-identity-android-kotlin) |  |

## <a name="daemon-applications"></a>Daemon-toepassingen

In de volgende voor beelden ziet u een toepassing die toegang heeft tot de Microsoft Graph-API met een eigen identiteit (zonder gebruiker).

| Client toepassing | Platform | Stroom/verlenen | Aanroepen Microsoft Graph |
| ------------------ | -------- | ---------- | -------------------- |
| Console | ![In deze afbeelding wordt het .NET core-logo weer gegeven](media/sample-v2-code/logo_NETcore.png)</p> ASP.NET  | [Client referenties](msal-authentication-flows.md#client-credentials) | [dotnetcore-daemon-v2](https://github.com/azure-samples/active-directory-dotnetcore-daemon-v2) |
| Web-app | ![In deze afbeelding wordt het ASP.NET-logo weer gegeven](media/sample-v2-code/logo_NETframework.png)</p> ASP.NET  | [Client referenties](msal-authentication-flows.md#client-credentials) | [DotNet-daemon-v2](https://github.com/azure-samples/active-directory-dotnet-daemon-v2) |
| Console | ![In deze afbeelding wordt het Java-logo weer gegeven](media/sample-v2-code/logo_java.png) | [Client referenties](msal-authentication-flows.md#client-credentials) | [MS-Identity-Java-daemon](https://github.com/Azure-Samples/ms-identity-java-daemon) |
| Console | ![Deze afbeelding toont het python-logo](media/sample-v2-code/logo_python.png) | [Client referenties](msal-authentication-flows.md#client-credentials) | [MS-Identity-python-daemon](https://github.com/Azure-Samples/ms-identity-python-daemon) |

## <a name="headless-applications"></a>Headless toepassingen

In het volgende voor beeld ziet u een open bare client toepassing die wordt uitgevoerd op een apparaat zonder webbrowser. De app kan een opdracht regel programma zijn, een app die wordt uitgevoerd op Linux of Mac of een IoT-toepassing. Het voor beeld bevat een app die toegang heeft tot de Microsoft Graph-API, in de naam van een gebruiker die zich interactief aanmeldt op een ander apparaat (zoals een mobiele telefoon). Deze client toepassing maakt gebruik van micro soft Authentication Library (MSAL).

| Client toepassing | Platform | Stroom/verlenen | Aanroepen Microsoft Graph |
| ------------------ | -------- |  ----------| ---------- |
| Bureau blad (console)   | ![In deze afbeelding wordt het .NET/C#-logo (Desktop) weer gegeven](media/sample-v2-code/logo_NETcore.png) | [Toestel code stroom](msal-authentication-flows.md#device-code) |[dotnetcore-devicecodeflow-v2](https://github.com/azure-samples/active-directory-dotnetcore-devicecodeflow-v2) |
| Bureau blad (console)   | ![In deze afbeelding wordt het Java-logo weer gegeven](media/sample-v2-code/logo_java.png) | [Toestel code stroom](msal-authentication-flows.md#device-code) |[MS-Identity-Java-devicecodeflow](https://github.com/Azure-Samples/ms-identity-java-devicecodeflow) |
| Bureau blad (console)   | ![Deze afbeelding toont het python-logo](media/sample-v2-code/logo_python.png) | [Toestel code stroom](msal-authentication-flows.md#device-code) |[MS-Identity-python-devicecodeflow](https://github.com/Azure-Samples/ms-identity-python-devicecodeflow) |

## <a name="web-apis"></a>Web-API's

De volgende voor beelden laten zien hoe u een web-API kunt beveiligen met het micro soft Identity platform-eind punt en hoe u een downstream API aanroept vanuit de Web-API.

| Platform | Voorbeeld |
| -------- | ------------------- |
| ![Deze afbeelding toont het ASP.NET Core logo](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | ASP.NET Core Web-API (Service) van [DotNet-native-aspnetcore-v2](https://aka.ms/msidentity-aspnetcore-webapi-calls-msgraph)  |
| ![In deze afbeelding wordt het ASP.NET-logo weer gegeven](media/sample-v2-code/logo_NET.png)</p>ASP.NET MVC | Web-API (Service) van [MS-Identity-ASPNET-webapi-onbehalfof](https://github.com/Azure-Samples/ms-identity-aspnet-webapi-onbehalfof) |
| ![In deze afbeelding wordt het Java-logo weer gegeven](media/sample-v2-code/logo_java.png) | Web-API (Service) van [MS-Identity-Java-webapi](https://github.com/Azure-Samples/ms-identity-java-webapi) |
| ![Deze afbeelding toont het Node.js logo](media/sample-v2-code/logo_nodejs.png) | Web-API (Service) van [Active Directory-java script-nodejs-webapi-v2](https://github.com/Azure-Samples/active-directory-javascript-nodejs-webapi-v2) |
| ![Deze afbeelding toont het Node.js logo](media/sample-v2-code/logo_nodejs.png) | B2C Web API (Service) van [Active-Directory-B2C-java script-nodejs-webapi](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi) |

## <a name="azure-functions-as-web-apis"></a>Azure Functions als web-Api's

De volgende voor beelden laten zien hoe u een Azure-functie kunt beveiligen met behulp van http trigger en een web-API kunt weer geven met het micro soft Identity platform-eind punt en hoe u een downstream API aanroept vanuit de Web-API.

| Platform | Voorbeeld |
| -------- | ------------------- |
| ![Deze afbeelding toont het ASP.NET Core logo](media/sample-v2-code/logo_NETcore.png)</p>ASP.NET Core | ASP.NET Core Web-API (Service) Azure function van [DotNet-native-aspnetcore-v2](https://github.com/Azure-Samples/ms-identity-dotnet-webapi-azurefunctions)  |
| ![Deze afbeelding toont het Node.js logo](media/sample-v2-code/logo_nodejs.png)</p>Node.js | Web-API (Service) van [NodeJS en Pass Port-Azure-AD](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-azurefunctions) |
| ![Deze afbeelding toont het python-logo](media/sample-v2-code/logo_python.png)</p>Python | Web-API (Service) van [python](https://github.com/Azure-Samples/ms-identity-python-webapi-azurefunctions) |
| ![Deze afbeelding toont het Node.js logo](media/sample-v2-code/logo_nodejs.png)</p>Node.js | Web-API (Service) van [NodeJS en Pass Port-Azure-AD namens](https://github.com/Azure-Samples/ms-identity-nodejs-webapi-onbehalfof-azurefunctions) |

## <a name="other-microsoft-graph-samples"></a>Andere Microsoft Graph-voor beelden

Zie [Microsoft Graph Community-voor beelden & zelf studies](https://github.com/microsoftgraph/msgraph-community-samples)voor meer informatie over de voor [beelden](https://github.com/microsoftgraph/msgraph-community-samples/tree/master/samples#aspnet) en zelf studies die verschillende gebruiks patronen voor de Microsoft Graph-API tonen, inclusief verificatie met Azure AD.

## <a name="see-also"></a>Zie ook

- [Hand leiding voor ontwikkel aars voor Azure Active Directory (v 1.0)](../azuread-dev/v1-overview.md)
- [Conceptuele en naslag informatie over Microsoft Graph-API](https://docs.microsoft.com/graph/use-the-api?context=graph%2Fapi%2Fbeta&view=graph-rest-beta)
